## Shellscript for database backup to S3
# Step 1: Create RDS MySQL Instance
   aws rds create-db-instance \
    --db-instance-identifier my-db-instance \
    --db-instance-class db.t3.micro \
    --engine mysql \
    --allocated-storage 20 \
    --master-username wp_user \
    --master-user-password My$tr0ngP#ssw0rd123! \
    --vpc-security-group-ids sg-04f86a6ab6b34d32f \
    --db-subnet-group-name db-subnet-group
## Step 2: Create EC2 Instance with IAM Role
  #  1.Create an IAM Role with S3 Write Access:
     Go to IAM in the AWS console, and create a new IAM role with AmazonS3FullAccess or a custom policy that allows write access to the sk-mysql-backup bucket.
  #  2.Launch EC2 Instance and Attach IAM Role:
     ** aws ec2 run-instances \
    --image-id ami-xxxxxxxx \ # Replace with your AMI ID
    --count 1 \
    --instance-type t2.micro \
    --key-name YourKeyPairName \
    --security-group-ids sg-xxxxxxxx \ # Your security group ID
    --subnet-id subnet-xxxxxxxx \ # Your subnet ID
    --iam-instance-profile Name=YourIamRole \
    --associate-public-ip-address **
## Step 3: Create the Backup Folder on EC2
      1.SSH into the EC2 instance:
         ** ssh -i YourKeyPair.pem ubuntu@<PUBLIC_IP>
      2.Create the folder /opt/databasedump/:
         ** sudo mkdir -p /opt/databasedump
            sudo chmod 777 /opt/databasedump*
## Step 4: Create the Shell Script for Database Backup
      1.  Create the shell script:
             sudo nano /opt/databasedump/backup_to_s3.sh
      2.Add the following script content (replace the placeholders with actual values):
             #!/bin/bash

# Variables
DB_HOST="your_rds_endpoint"        # e.g., yourdbinstance.abc123xyz.us-east-1.rds.amazonaws.com
DB_USER="your_master_username"     # RDS master username
DB_PASSWORD="your_master_password" # RDS master password
DB_NAME="your_database_name"       # The database name to backup
S3_BUCKET="sk-mysql-backup"
BACKUP_DIR="/opt/databasedump"
CURRENT_DATE=$(date +"%Y%m%d")
BACKUP_PATH="$BACKUP_DIR/$CURRENT_DATE"
BACKUP_FILE="$BACKUP_PATH/$DB_NAME.sql"

# Create a directory for today's backup
mkdir -p $BACKUP_PATH

# Take MySQL database backup
mysqldump -h $DB_HOST -u $DB_USER -p$DB_PASSWORD $DB_NAME > $BACKUP_FILE

# Check if the backup was successful
if [ $? -eq 0 ]; then
    echo "Database backup successful!"
else
    echo "Database backup failed!"
    exit 1
fi

# Compress the backup file
zip -r "$BACKUP_PATH/$DB_NAME.zip" $BACKUP_FILE

# Upload the compressed backup to S3
aws s3 cp "$BACKUP_PATH/$DB_NAME.zip" s3://$S3_BUCKET/$CURRENT_DATE/

# Check if the upload was successful
if [ $? -eq 0 ]; then
    echo "Backup uploaded to S3 successfully!"
else
    echo "Failed to upload backup to S3!"
    exit 1
fi

# Clean up old backup files
find $BACKUP_DIR -type d -mtime +7 -exec rm -rf {} \;  # Delete folders older than 7 days

echo "Backup process completed!"
## 3 Make the script executable:
      sudo chmod +x /opt/databasedump/backup_to_s3.sh
## Step 5: Schedule the Backup Script to Run Daily at 1:15 AM
   1.Edit the crontab:
       sudo crontab -e
   2.Add the following cron job:
        15 1 * * * /opt/databasedump/backup_to_s3.sh >> /opt/databasedump/backup.log 2>&1
## Step 6: Verify the Backup
After the cron job runs, you can check the following to ensure everything is working correctly:
  1 Check the log file:
     cat /opt/databasedump/backup.log
  2  Check if the backup exists on S3:
      Go to the AWS S3 console and check the sk-mysql-backup bucket to see if the backup file for the day has been uploaded.
      ## Summary ##
    i)RDS and EC2 setup: created an RDS MySQL instance and an EC2 instance with an IAM role that has permission to write to an S3 bucket.
    ii) Backup script: A shell script backs up the database, compresses it, and uploads it to S3.
    iii) Automation: Cron schedules the script to run daily at 1:15 AM.


