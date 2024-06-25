

# Updating the host's IP with an HTTPS request
*This information is adapted from [this article](https://www.namecheap.com/support/knowledgebase/article.aspx/29/11/how-to-dynamically-update-the-hosts-ip-with-an-https-request/).*

Please substitute these with appropriate values for host, domain, password and IP:

https://dynamicdns.park-your-domain.com/update?host=**[host]**&domain=**[domain_name]**&password=**[ddns_password]**&ip=**[your_ip]**

Fields that are to be changed in the URL are: **[host]**, **[domain_name]**, **[ddns_password]** and **[your_ip]**.

Here is an example for IP address update for a bare domain (e.g., yourdomain.tld):

- Host = @
- Domain Name = yourdomain.tld
- Dynamic DNS Password = Domain List >> click Manage next to the domain >>Advanced DNS tab >> Dynamic DNS. If it is not enabled, enable it to check the password.
- IP Address = optional value. If you don't specify any IP, the IP from which you are accessing this URL will be set for the domain.

**NOTE:** The values for the host name and domain in the URL must be of the same case (lowercase/uppercase) as in your account.

If the domain is shown as YourDomain.tld in your Dashboard, enter it in the same way: https://dynamicdns.park-your-domain.com/update?host=@&domain=YourDomain.tld&password=e747d77054a844409c486973cb&ip=127.0.0.1

If it shown in lowercase, like yourdomain.tld, add just yourdomain.tld. The URL will have the following format in this case: https://dynamicdns.park-your-domain.com/update?host=@&domain=yourdomain.tld&password=e747d77054a844409c486973cb&ip=127.0.0.1

Please make sure you are using your Dynamic DNS password and not the Namecheap account's one.

To dynamically update the IP address for a subdomain (test.yourdomain.tld), use test for Host (host=test). To dynamically update it for www.yourdomain.tld, use www for Host: host=www.

To have the wildcard subdomain updated dynamically, just make sure that you insert an asterisk (*) as Host in the URL.

# Creating a recurring process to update the host's IP
*This information is adapted from [this article](https://www.duckdns.org/install.jsp?tab=linux-cron&domain=).*

## Prerequisites
**Crontab**

If your linux install is running a crontab, then you can use a cron job to keep the IP updated. We can see this with
```
ps -ef | grep cr[o]n
```
If that command returns nothing, then you will need to install cron for your distribution of Linux.

**Curl**
Test if you have curl by running:
```
curl
```
If this command returns a 'not found' error, then you will need ot install curl for your distribution.

## Writing the Bash Script
SSH into the server as root. Run the following commands:
```
cd ~
mkdir namecheap_ddns
cd namecheap_ddns
vi namecheap_ddns.sh
```
Copy the following text using the Vim editor (press 'i' to toggle insert mode first).
```
echo url="https://dynamicdns.park-your-domain.com/update?host=*&domain=syihan.com&password=e88d11c6f2bb414bbfd34041aa917c51" | curl -k -o ~/namecheap_ddns/namecheap_ddns.log -K -
```
Save the file by hitting ESQ and :wq! and ENTER.

Once executed, this script will make a https request and log the output in the file namecheap_ddns.log.

To make the file executeable, run:
```
chmod 700 namecheap_ddns.sh
```

## Setting up the Recurrance using Cron
We will set up the cron process to make the script get run every 5 minutes. To start, run the following:
```
crontab -e
```
Copy the following text and paste it at the bottom of the crontab:
```
*/5 * * * * ~/namecheap_ddns/namecheap_ddns.sh >/dev/null 2>&1
```
Save the file.

## Testing the script
Before running the script, create a test A tag on Namecheap with a fake IP address. You can set the host to "\*" and the address to "8.8.8.8" (Google's DNS server). By going to the address, the browser should resolve it to Google's DNS server.

Test the script by returning to the terminal and running:
```
./namecheap_ddns.sh
```

When refreshing Namecheap, this should replace the test A tag's IP address to the server's current IP. You can further test this by going to the address, which should now resolve to your server.

We can also see if the last attempt was successful (OK or bad KO) with
```
cat namecheap_ddns.log
```

If it is KO, check that your Token and Domain are correct in namecheap_ddns.sh.

# Additional Resources
- [DDNS on a Raspberry Pi using the Cloudflare API (Dynamic DNS)](https://www.youtube.com/watch?v=rI-XxnyWFnM&list=PL2vEDdJJGqRExuY-6UcryUHaCNTFaBDk5&index=6)
- [[No Dependencies] Namecheap - How to Setup a FREE DDNS Script (self contained)](https://www.youtube.com/watch?v=9Wd2a_69QIw&list=PL2vEDdJJGqRExuY-6UcryUHaCNTFaBDk5&index=5)
- [Free Dynamic DNS with DuckDNS!](https://www.youtube.com/watch?v=mu02Ute0VTI&list=PL2vEDdJJGqRExuY-6UcryUHaCNTFaBDk5&index=4)