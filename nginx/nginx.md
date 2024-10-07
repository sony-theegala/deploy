Certainly! Below is a streamlined guide to **enable HTTPS on your EC2 instance using a self-signed SSL certificate**. This setup will secure the communication between clients and your server, even without a domain name. Please note that browsers will display a security warning since the certificate isn't issued by a recognized Certificate Authority (CA).

---

### **Prerequisites**

- **EC2 Instance:** Ensure you have an EC2 instance running with NGINX installed.
- **SSH Access:** Ability to connect to your EC2 instance via SSH.
- **Public IP Address:** Your EC2 instance should have a public IP address.

---

### **Step 1: Connect to Your EC2 Instance**

Use SSH to connect to your EC2 instance.

```bash
ssh -i /path/to/your/key.pem ec2-user@your_ec2_public_ip
```

_Replace `/path/to/your/key.pem` with the path to your SSH key and `your_ec2_public_ip` with your EC2 instance's public IP address._

---

### **Step 2: Install Necessary Packages**

Ensure that **NGINX** and **OpenSSL** are installed on your EC2 instance.

**For Ubuntu/Debian:**

```bash
sudo apt update
sudo apt install nginx openssl -y
```

---

### **Step 3: Generate a Self-Signed SSL Certificate**

1. **Create a Directory for SSL Certificates:**

   ```bash
   sudo mkdir -p /etc/nginx/ssl
   ```

2. **Generate the Certificate and Private Key:**

   Execute the following command to create a self-signed certificate valid for 365 days. Replace `your_ec2_public_ip` with your actual EC2 public IP address.

   ```bash
   sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
   -keyout /etc/nginx/ssl/selfsigned.key \
   -out /etc/nginx/ssl/selfsigned.crt
   ```

3. **Fill in the Certificate Details:**

   You'll be prompted to enter information for the certificate. For the **Common Name**, enter your EC2 instance's **public IP address**.

   ```
   Country Name (2 letter code) [AU]:US
   State or Province Name (full name) [Some-State]:YourState
   Locality Name (eg, city) []:YourCity
   Organization Name (eg, company) [Internet Widgits Pty Ltd]:YourOrganization
   Organizational Unit Name (eg, section) []:YourUnit
   Common Name (e.g. server FQDN or YOUR name) []:your_ec2_public_ip
   Email Address []:youremail@example.com
   ```

---

### **Step 4: Configure NGINX to Use the Self-Signed Certificate**

1. **Backup the Default NGINX Configuration:**

   ```bash
   sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/default.bak
   ```

2. **Edit the NGINX Configuration File:**

   ```bash
   sudo nano /etc/nginx/sites-available/default
   ```

3. **Replace the Existing Configuration with the Following:**

   ```nginx
   # Redirect HTTP to HTTPS
   server {
       listen 80;
       server_name your_ec2_public_ip;

       # Redirect all HTTP requests to HTTPS with a 301 Moved Permanently response.
       return 301 https://$host$request_uri;
   }

   # HTTPS Server Block
   server {
       listen 443 ssl;
       server_name your_ec2_public_ip;

       ssl_certificate /etc/nginx/ssl/selfsigned.crt;
       ssl_certificate_key /etc/nginx/ssl/selfsigned.key;

       # SSL Protocols and Ciphers
       ssl_protocols TLSv1.2 TLSv1.3;
       ssl_ciphers HIGH:!aNULL:!MD5;

       # Proxy Settings
       location / {
           proxy_pass http://localhost:4000;
           proxy_http_version 1.1;
           proxy_set_header Upgrade $http_upgrade;
           proxy_set_header Connection 'upgrade';
           proxy_set_header Host $host;
           proxy_cache_bypass $http_upgrade;
       }
   }
   ```

   > **Replace `your_ec2_public_ip`** with your actual EC2 public IP address.

4. **Save and Exit:**

   - In `nano`, press `CTRL + O` to save the changes, then `CTRL + X` to exit the editor.

---

### **Step 5: Adjust EC2 Security Groups**

Ensure that your EC2 instance's security group allows traffic on both **port 80 (HTTP)** and **port 443 (HTTPS)**.

1. **Navigate to the EC2 Dashboard:**

   - Go to the [AWS EC2 Console](https://console.aws.amazon.com/ec2/).

2. **Select Your Instance:**

   - Click on your instance to view its details.

3. **Modify Security Groups:**

   - Under the **"Security"** tab, click on the linked security group.

4. **Edit Inbound Rules:**

   - **Add Rule for HTTPS:**
     - **Type:** HTTPS
     - **Protocol:** TCP
     - **Port Range:** 443
     - **Source:** Anywhere (0.0.0.0/0) _(or restrict as needed)_
   - **Ensure HTTP Rule Exists:**
     - **Type:** HTTP
     - **Protocol:** TCP
     - **Port Range:** 80
     - **Source:** Anywhere (0.0.0.0/0) _(or restrict as needed)_

5. **Save Rules:**
   - Click **"Save rules"** to apply the changes.

---

### **Step 6: Test and Reload NGINX Configuration**

1. **Test the NGINX Configuration:**

   ```bash
   sudo nginx -t
   ```

   - **Expected Output:**

     ```
     nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
     nginx: configuration file /etc/nginx/nginx.conf test is successful
     ```

   - **If Errors Exist:**
     - Review the error messages, correct the configuration file, and test again.

2. **Reload NGINX to Apply Changes:**

   ```bash
   sudo systemctl reload nginx
   ```

   > **Alternatively, to restart NGINX:**
   >
   > ```bash
   > sudo systemctl restart nginx
   > ```

---
