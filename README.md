# **wordpress-deploy-on-ec2-with-rds**


This project demonstrates how to deploy a scalable WordPress website using an EC2 instance for the web server and Amazon RDS for the MySQL database.


## **Project Overview**
We will:
  * Launch an EC2 instance (Amazon Linux 2)
  * Set up a security group
  * Install Apache, PHP, and WordPress
  * Create and configure an RDS MySQL database
  * Connect WordPress to RDS



## **Prerequisites**
  * AWS Account
  * Key Pair (PEM file)
  * Domain name (optional, for HTTPS)
  * Basic knowledge of AWS Console and Linux commands


  ## **Step-by-Step Deployment**

  ### **1. Create an RDS MySQL Instance**
  1. Go to **RDS > Databases > Create database**
  2. Select:
   - Engine: MySQL
   - Template: Free Tier
  3. Configure:
   - DB instance identifier: `wordpress-db`
   - Master username: `admin`
   - Master password: `yourpassword`
  4. Choose a VPC, enable public access
  5. Security Group: allow inbound MySQL/Aurora (port 3306)
  6. Create database and note the **endpoint**

  ### **2️. Launch an EC2 Instance**
  1. Go to **EC2 > Instances > Launch Instance**
  2. Select **Amazon Linux 2 AMI**
  3. Choose instance type: `t2.micro` (Free Tier)
  4. Configure security group:
   - Allow SSH (22), HTTP (80), and optionally HTTPS (443)
  5. Launch with your Key Pair
  6. Note the **Public IPv4 address**

  ### **3️.  SSH into EC2 and Install LAMP Stack**
  ```bash
ssh -i your-key.pem ec2-user@<EC2-Public-IP>

# Update packages
sudo yum update -y

# Install Apache
sudo yum install httpd -y
sudo systemctl start httpd
sudo systemctl enable httpd

# Install PHP and dependencies
sudo amazon-linux-extras enable php8.0
sudo yum clean metadata
sudo yum install php php-mysqlnd php-fpm php-json -y

# Restart Apache
sudo systemctl restart httpd

```

### **4️. Download and Configure WordPress**
```
cd /var/www/html
sudo wget https://wordpress.org/latest.tar.gz
sudo tar -xzf latest.tar.gz
sudo cp -r wordpress/* .
sudo rm -rf wordpress latest.tar.gz
```

### **5️. Configure WordPress with RDS Database**
```
sudo cp wp-config-sample.php wp-config.php
sudo nano wp-config.php
```
And Modify these lines:
```
php
define( 'DB_NAME', 'your_db_name' );
define( 'DB_USER', 'admin' );
define( 'DB_PASSWORD', 'yourpassword' );
define( 'DB_HOST', 'your-rds-endpoint.amazonaws.com' );
```
Save and exit.

Set Permissions:
```
sudo chown -R apache:apache /var/www/html
sudo chmod -R 755 /var/www/html
```


### **6️. Allow HTTP Access (Security Group)**
  * Edit EC2 instance security group

  * Allow Inbound rule for:

    * HTTP (80)

    * HTTPS (443) – if using SSL

    * SSH (22)


### **7️. Complete WordPress Installation via Browser**
1. Visit: http://EC2-Public-ip
2. Complete the WordPress setup wizard:
   * Site title, admin user, password, etc.


### **Final Checklist**
  * WordPress accessible via EC2 IP or domain
  * Database hosted on RDS and connected
  * Apache and PHP running
  *  HTTPS enabled (optional)
