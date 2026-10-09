# LOAD BALANCER SOLUTION WITH NGINX AND SSL/TLS. 

Having already load-balanced with Apache, this project builds the same solution using Nginx, because a good DevOps engineer knows more than one way to solve a problem.

It also covers HTTPS. When data travels between a browser and a web server, it passes through multiple network devices, if it is unencrypted, that data can be intercepted along the way (a Man-In-The-Middle attack), putting sensitive info like credentials or bank details at risk.

SSL/TLS prevents this by encrypting the connection between browser and server, using digital certificates issued by a trusted Certificate Authority (CA) to verify the site is legitimate.

For this project, I registered a domain and used Let's Encrypt as the CA, with certbot to automate certificate issuance.

# Objectives : 

1. Configure Nginx as a Load Balancer.
2. Register a domain and secure it with SSL/TLS. 


# Solution Architecture 

1. ![img1](./images/img1.png)

# Part 1: 

1. Create an EC2 VM based on Ubuntu Server 24.04 LTS and name it Nginx LB. 

![img2](./images/img2.png)

2. Open TCP port 80 for HTTP connections, also open TCP port 443 – this port is used for secured HTTPS connections. 

![img3](./images/img3.png)

3. Connect to the instance. 

![img4](./images/img4.png)

4. Update the package manager 
```bash 
sudo apt update
```
![img5](./images/img5.png)

5. Install Nginx 
```bash 
sudo apt install nginx 
```
![img6](./images/img6.png)

6. Configure Nginx-LB using Web Servers' names defined in /etc/hosts

Configure the local DNS name 
```bash
sudo vi /etc/hosts
```
![img7](./images/img7.png)


7. Open the default nginx configuration file to ensure nginx serves as a load balancer. 
```bash
sudo vi /etc/nginx/nginx.conf
```
![img8](./images/img8.png)

8. Restart Nginx and make sure the service is up and running. 

```bash
sudo systemctl restart nginx
sudo systemctl status nginx
```
![img9](./images/img9.png)


# Part 2: Register a Domain and Secure It with SSL/TLS

>> Note: In order to get a valid SSL certificate – we need to register a new domain name, you can do it using any Domain name registrar – a company that manages reservation of domain names. The most popular ones are: Godaddy.com, Domain.com, Bluehost.com.  

>> Register a new domain name with any registrar of your choice in any domain zone (e.g. .com, .net, .org, .edu, .info, .xyz or any other)  

>> Assign an Elastic IP to your Nginx LB server and associate your domain name with this Elastic IP. 


1. Went to duckdns.org and Signed in using GitHub (Saintemmy09 account)

![img10](./images/img10.png)

2. Authorized DuckDNS to access public GitHub profile data and Completed the reCAPTCHA verification to access the dashboard. 
In the add domain field, i entered a subdomain name: saintemmy
Clicked add domain and Domain "saintemmy.duckdns.org"  was successfully created. 

![img11](./images/img11.png)

3. Allocate and Associate an Elastic IP to the Nginx-lb. 
Logged into AWS Console → EC2
Navigated to Network & Security → Elastic IPs →  Allocate Elastic IP address. 

![img12](./images/img12.png)

4. Named the new Elastic IP Nginx-lb-eip
Chose Instance as resource type and selected the Nginx-LB EC2 instance → Associate Resulting Elastic IP: 98.94.113.230, associated with instance i-07d40688dda054d3e. 

![img13](./images/img13.png)

5. Returned to the DuckDNS dashboard
In the saintemmy domain row, replaced the auto-detected current ip with the Elastic IP 98.94.113.230 → update ip. 

![img14](./images/img14.png)

6. Update Nginx config file to recognize domain. 

```bash 
sudo vi /etc/nginx/nginx.conf
```
![img15](./images/img15.png)

7. Confirm configuration is okay. 
```bash
sudo nginx -t
```
![img16](./images/img16.png)

8. Restart Nginx and confirm it's running
```bash
sudo systemctl restart nginx
sudo systemctl status nginx
```
![img17](./images/img17.png)

**Note:** After completing step 5, `saintemmy.duckdns.org` still returned `ERR_CONNECTION_TIMED_OUT` — even though the Elastic IP itself worked and Nginx's `server_name` was correctly configured. Checking the DuckDNS dashboard revealed the update from step 5 had not actually saved; the domain was still pointing to the old auto-detected IP. Updating it again resolved the issue.

9. Update the DuckDNS domain record with the Elastic IP  ```98.94.113.230```

![img18](./images/img18.png)

10. Verify the domain resolves and serves the site
   [saintemmy.duckdns.org](http://saintemmy.duckdns.org)
   
   ![img19](./images/img19.png)

11. Install certbot and request for  SSL/TLS certificate 
Make sure ```snapd``` is active and running
```bash
sudo systemctl status snapd
```
![img20](./images/img20.png)

12. Install certbot: 
```bash
sudo snap install --classic certbot
```
![img21](./images/img21.png)


13. Request the certificate 
```bash 
sudo ln -s /snap/bin/certbot /usr/bin/certbot
sudo certbot --nginx
```
![img22](./images/img22.png)
![img23](./images/img23.png)

14. Test secured access to your Web Solution by trying to reach `https://saintemmy.duckdns.org`

![img24](./images/img24.png)
![img25](./images/img25.png)

15. Set up periodical renewal of your SSL/TLS certificate. By default, Let's Encrypt certificates are valid for 90 days, so it's recommended to renew at least every 60 days.

Test the renewal command in dry-run mode:
```bash
sudo certbot renew --dry-run
```
![img26](./images/img26.png)

16. Best practice is to have a scheduled job to run the renew command periodically.Let us configure a cronjob to run the command twice a day.

To do so, we will edit the crontab file with the following command:
```crontab -e```

Add the following line
```0 */12 * * * /usr/bin/certbot renew --quiet```

![img27](./images/img27.png)
![img28](./images/img28.png)
   
# Conclusion

This project successfully implemented an Nginx Load Balancer with SSL/TLS encryption, extending the previous load-balancing solution built with Apache.

The solution demonstrates how Nginx can distribute traffic across multiple web servers while HTTPS, powered by Let's Encrypt and Certbot, secures communication between users and the application. Automatic certificate renewal was also configured to maintain continuous HTTPS protection.

Overall, this project strengthened my practical understanding of Nginx, load balancing, DNS, Elastic IPs, SSL/TLS, Let's Encrypt, and Certbot, while reinforcing the importance of security and availability in modern web infrastructure.

