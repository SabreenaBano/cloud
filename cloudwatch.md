## Custom Cloudwatch metrics (task-23):
# cloudwatch: is a monitoring and observability service provided by AWS. It helps you collect,      monitor, and analyze performance and operational data from AWS resources, applications, and       services. CloudWatch can track a wide variety of metrics, such as:
                   CPU usage
                   Disk reads/writes
                   Network activity
                   Application performance
                   Custom metrics (defined by the user)
## CloudWatch also allows you to set alarms and take automated actions (like scaling up or down      EC2 instances) based on specific metrics. Additionally, it can aggregate log data from services    like EC2, AWS Lambda, and others for easier monitoring and troubleshooting.
"Grab memory metrics from EC2
  - Launch EC2
  - Create IAM role with cloudwatch full access and attach it to EC2
  - Install Cloudwatch agent on the EC2
  - Create a config file to Collect memory usege
  - See the metrics in Cloudwatch console"
**commands used 
       # sudo apt-get update
       # sudo apt-get install amazon-cloudwatch-agent -y
 Create a Configuration File to Collect Memory Usage
       #Example configuration file (/opt/aws/amazon-cloudwatch-agent/bin/config.json):
         {
  "metrics": {
    "append_dimensions": {
      "InstanceId": "${aws:InstanceId}"
    },
    "metrics_collected": {
      "mem": {
        "measurement": [
          "mem_used_percent",
          "mem_available_percent"
        ],
        "metrics_collection_interval": 60
      }
    }
  }
}
After creating the configuration file, run the following command to start the agent using the configuration:
## sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
   -a fetch-config \
   -m ec2 \
   -c file:/opt/aws/amazon-cloudwatch-agent/bin/config.json \
   -s
# View Metrics in the CloudWatch Console
    Go to the CloudWatch service in the AWS Console.
    Navigate to Metrics.
    Under Custom Namespaces (or EC2), look for the new memory metrics being reported.
    The metrics should include mem_used_percent and mem_available_percent.
    Now our EC2 instance is set up to report memory usage metrics to CloudWatch!


   