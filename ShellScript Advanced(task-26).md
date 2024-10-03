## Install wordpress via shellscript
 # step 1: Create IAM User with Administrator Access
          i)  Go to AWS IAM
          ii) Create a new user
          iii)Attach existing policies 
          iv) download access keys
 # Step 2: Install and Configure AWS CLI
          i) Install AWS CLI
          ii) configure aws cli
              ** aws configure **
## Step 3: Launch EC2 Instance and RDS
 # 1 Launch EC2 Instance: Replace the placeholders in the following command with your details.
    * aws ec2 run-instances \
    --image-id ami-xxxxxxxx \ # Replace with a valid Ubuntu AMI ID
    --count 1 \
    --instance-type t2.micro \
    --key-name YourKeyPairName \
    --security-group-ids sg-xxxxxxxx \ # Replace with your security group ID
    --subnet-id subnet-xxxxxxxx \ # Replace with your public subnet ID
    --associate-public-ip-address *
3 Launch RDS (MySQL):
    ** aws rds create-db-instance \
    --db-instance-identifier YourDBInstanceIdentifier \
    --db-instance-class db.t2.micro \
    --engine mysql \
    --master-username YourMasterUsername \
    --master-user-password YourMasterPassword \
    --allocated-storage 20 \
    --vpc-security-group-ids sg-xxxxxxxx \ # Replace with your security group ID
    --db-subnet-group-name YourDBSubnetGroup **
## Step 4: Create a Shell Script on EC2 Instance
   # 1 SSH into your EC2 Instance:
       ** ssh -i YourKeyPair.pem ubuntu@<EC2_PUBLIC_IP>**
     2 Create the installation script:
       ** nano install_wordpress.sh **
    3  Add the following script content:
       ** #!/bin/bash

# Update package list and install required packages
sudo apt update
sudo apt install -y apache2 php libapache2-mod-php php-mysql

# Install MySQL Client
sudo apt install -y mysql-client

# Create MySQL database and user
DB_NAME="your_database_name"
DB_USER="your_username"
DB_PASSWORD="your_password"
DB_HOST="your_rds_endpoint"

mysql -h $DB_HOST -u master_username -p'master_password' -e "CREATE DATABASE $DB_NAME;"
mysql -h $DB_HOST -u master_username -p'master_password' -e "CREATE USER '$DB_USER'@'%' IDENTIFIED BY '$DB_PASSWORD';"
mysql -h $DB_HOST -u master_username -p'master_password' -e "GRANT ALL PRIVILEGES ON $DB_NAME.* TO '$DB_USER'@'%';"
mysql -h $DB_HOST -u master_username -p'master_password' -e "FLUSH PRIVILEGES;"

# Download WordPress
wget https://wordpress.org/latest.tar.gz
tar -xzvf latest.tar.gz
cp wordpress/wp-config-sample.php wordpress/wp-config.php

# Update wp-config.php
sed -i "s/database_name_here/$DB_NAME/" wordpress/wp-config.php
sed -i "s/username_here/$DB_USER/" wordpress/wp-config.php
sed -i "s/password_here/$DB_PASSWORD/" wordpress/wp-config.php
sed -i "s/localhost/$DB_HOST/" wordpress/wp-config.php

# Move WordPress files to Apache directory
sudo cp -r wordpress/* /var/www/html/

# Set permissions
sudo chown -R www-data:www-data /var/www/html/
sudo chmod -R 755 /var/www/html/

# Restart Apache
sudo systemctl restart apache2

echo "WordPress installed successfully!" **
4 Make the script executable:
  chmod +x install_wordpress.sh
## Step 5: Run the Shell Script
   ./install_wordpress.sh
## Step 6: Verify Installation
    Open your web browser and navigate to http://<EC2_PUBLIC_IP> to check if WordPress is working correctly.





