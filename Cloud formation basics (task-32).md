##  AWS CloudFormation provides a robust framework for automating infrastructure management and        consistency across environments. Its features make it easier to deploy and manage AWS              resources, streamline operations, and reduce the risk of errors.
#Creating a VPC (Virtual Private Cloud) using CloudFormation (CFT) involves defining the necessary resources in a YAML or JSON template. Below is a step-by-step guide for each level you outlined, including the creation of a VPC, subnets, NAT Gateway, route tables, EC2 instances, security groups, and parameterization.
# Level 0: Create a VPC and Subnets
1.Create a VPC:
Define a VPC resource in the CloudFormation template. Specify the CIDR block for the VPC (e.g., 10.0.0.0/16).
2. Create Two Subnets:
    Define two subnet resources, specifying their CIDR blocks and associating them with the VPC. Example:
    Subnet 1: 10.0.1.0/24
    Subnet 2: 10.0.2.0/24
** AWSTemplateFormatVersion: '2010-09-09'
Description: Create a VPC with two subnets.

Resources:
  MyVPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsSupport: true
      EnableDnsHostnames: true

  PublicSubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref MyVPC
      CidrBlock: 10.0.1.0/24
      MapPublicIpOnLaunch: true

  PrivateSubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref MyVPC
      CidrBlock: 10.0.2.0/24 **
# Level 1: Add NAT Gateway and Route Table
1 Create NAT Gateway:
   Define a NAT Gateway resource, providing an Elastic IP for it.
2. Create a Route Table:
   Define a route table and associate it with the private subnet.
   Add a route to the NAT Gateway to allow internet access for instances in the private subnet.
   ** Resources:
  MyNatGateway:
    Type: AWS::EC2::NatGateway
    Properties:
      AllocationId: !GetAtt MyEIP.AllocationId
      SubnetId: !Ref PublicSubnet

  MyEIP:
    Type: AWS::EC2::EIP

  MyRouteTable:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref MyVPC

  MyRoute:
    Type: AWS::EC2::Route
    Properties:
      RouteTableId: !Ref MyRouteTable
      DestinationCidrBlock: 0.0.0.0/0
      NatGatewayId: !Ref MyNatGateway

  PrivateSubnetRouteTableAssociation:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      SubnetId: !Ref PrivateSubnet
      RouteTableId: !Ref MyRouteTable ** 
# Level 2: Launch EC2 Instance with Bootstrap Script
1 Launch EC2 Instance:
    Define an EC2 instance resource (e.g., Amazon Linux).
    Add a bootstrap script to install Apache.
 2. Create a Security Group:
     Define a security group allowing HTTP and SSH access.
  ** Resources:
  MySecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Allow HTTP and SSH
      VpcId: !Ref MyVPC
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 0.0.0.0/0
        - IpProtocol: tcp
          FromPort: 22
          ToPort: 22
          CidrIp: 0.0.0.0/0

  MyEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: t2.micro
      ImageId: ami-0c55b159cbfafe1f0 # Amazon Linux 2 AMI ID (ensure it's correct for your region)
      SecurityGroupIds:
        - !Ref MySecurityGroup
      SubnetId: !Ref PrivateSubnet
      UserData:
        Fn::Base64: !Sub |
          #!/bin/bash
          yum update -y
          yum install httpd -y
          systemctl start httpd
          systemctl enable httpd ** 
# Level 3: Parameterize the Template
1 Add Parameters for VPC, Subnet, and EC2 Instance Names:
  Modify the template to accept parameters for the VPC name, subnet names, and EC2 instance name.
      ** Parameters:
  VpcName:
    Type: String
    Description: Name of the VPC

  PublicSubnetName:
    Type: String
    Description: Name of the public subnet

  PrivateSubnetName:
    Type: String
    Description: Name of the private subnet

  EC2InstanceName:
    Type: String
    Description: Name of the EC2 instance

Resources:
  MyVPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsSupport: true
      EnableDnsHostnames: true
      Tags:
        - Key: Name
          Value: !Ref VpcName

  PublicSubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref MyVPC
      CidrBlock: 10.0.1.0/24
      MapPublicIpOnLaunch: true
      Tags:
        - Key: Name
          Value: !Ref PublicSubnetName

  PrivateSubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref MyVPC
      CidrBlock: 10.0.2.0/24
      Tags:
        - Key: Name
          Value: !Ref PrivateSubnetName

  MyEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: t2.micro
      ImageId: ami-0c55b159cbfafe1f0 # Update this with the latest Amazon Linux 2 AMI ID for your region
      SecurityGroupIds:
        - !Ref MySecurityGroup
      SubnetId: !Ref PrivateSubnet
      UserData:
        Fn::Base64: !Sub |
          #!/bin/bash
          yum update -y
          yum install httpd -y
          systemctl start httpd
          systemctl enable httpd
      Tags:
        - Key: Name
          Value: !Ref EC2InstanceName**
 #  Deployment Steps
 1 Save the CloudFormation template to a file (e.g., vpc-template.yaml).
 2 Deploy the CloudFormation stack using the AWS Management Console or AWS CLI:
  AWS Console: Go to CloudFormation, click on Create stack, and upload your template file.
  AWS CLI:
   aws cloudformation create-stack --stack-name MyVPCStack --template-body file://vpc-template.yaml --parameters ParameterKey=VpcName,ParameterValue=MyVPC ParameterKey=PublicSubnetName,ParameterValue=MyPublicSubnet ParameterKey=PrivateSubnetName,ParameterValue=MyPrivateSubnet ParameterKey=EC2InstanceName,ParameterValue=MyEC2Instance


    



 

            
 