![image](https://github.com/user-attachments/assets/c25a91c7-3f2a-4d30-abaf-da99e3ada5d2)# AWS - SAA 03
### VPC 
![image](https://github.com/user-attachments/assets/ed34c413-bbad-432d-a9a4-144e2d496ff2)

![image](https://github.com/user-attachments/assets/0842c0c4-2bb9-4daa-91ff-96de72a61359)


![image](https://github.com/user-attachments/assets/358e5b1f-0a94-4298-a29f-e489a54dab7a)

create_vpc.sh
```sh
#!/bin/bash

# Create our VPC
VPC_ID=$(aws ec2 create-vpc \
    --cidr-block "172.1.0.0/16" \
    --tag-specifications 'ResourceType=vpc,Tags=[{Key=Name,Value=MyVpc}]' \
    --region ca-central-1 \
    --query Vpc.VpcId \
    --output text)
echo "Created VPC: $VPC_ID"

# Create an Internet Gateway (IGW)
IGW_ID=$(aws ec2 create-internet-gateway \
  --query InternetGateway.InternetGatewayId \
  --output text)
echo "Created Internet Gateway: $IGW_ID"

# Attach the IGW to the VPC
aws ec2 attach-internet-gateway --internet-gateway-id $IGW_ID --vpc-id $VPC_ID
echo "Attached IGW to VPC"

# Create a new subnet
SUBNET_ID=$(aws ec2 create-subnet \
  --vpc-id $VPC_ID \
  --cidr-block 172.1.0.0/20 \
  --query Subnet.SubnetId \
  --output text )
echo "Created Subnet: $SUBNET_ID"

# Auto-assign IPv4 addresses to instances launched in the subnet
aws ec2 modify-subnet-attribute \
    --subnet-id $SUBNET_ID \
    --map-public-ip-on-launch
echo "Enabled auto-assign public IPv4 for subnet: $SUBNET_ID"

# Enable DNS support and DNS hostnames
aws ec2 modify-vpc-attribute --vpc-id $VPC_ID --enable-dns-support "{\"Value\":true}"
aws ec2 modify-vpc-attribute --vpc-id $VPC_ID --enable-dns-hostnames "{\"Value\":true}"
echo "Enabled DNS support and DNS hostnames for VPC: $VPC_ID"

# Get the main route table ID for the VPC
RT_ID=$(aws ec2 describe-route-tables \
    --filters "Name=vpc-id,Values=$VPC_ID" "Name=association.main,Values=true" \
    --query 'RouteTables[0].RouteTableId' \
    --output text)
echo "Main Route Table ID: $RT_ID"

# Create a route in the route table that routes traffic to the Internet Gateway
aws ec2 create-route \
    --route-table-id $RT_ID \
    --destination-cidr-block 0.0.0.0/0 \
    --gateway-id $IGW_ID
echo "Added route to IGW in Route Table: $RT_ID"

# Associate the subnet with the Route Table
aws ec2 associate-route-table --subnet-id $SUBNET_ID --route-table-id $RT_ID
echo "Associated Subnet $SUBNET_ID with Route Table $RT_ID"

```
```md
sh create.sh
Created VPC: vpc-0c181edbc1fb8783e
Created Internet Gateway: igw-00a0e38970f3a0e37
Attached IGW to VPC
Created Subnet: subnet-023af3220cdb2898a
Enabled auto-assign public IPv4 for subnet: subnet-023af3220cdb2898a
Enabled DNS support and DNS hostnames for VPC: vpc-0c181edbc1fb8783e
Main Route Table ID: rtb-01da6ad2cdb7429cc
{
    "Return": true
}
Added route to IGW in Route Table: rtb-01da6ad2cdb7429cc
{
    "AssociationId": "rtbassoc-03edaca4b6696d884",
    "AssociationState": {
        "State": "associated"
    }
}
Associated Subnet subnet-023af3220cdb2898a with Route Table rtb-01da6ad2cdb7429cc

```

delete_vpc.sh
```sh
#!/bin/bash
# VPC ->IGW->SUBNET->RT

# Variables
VPC_ID="vpc-0c181edbc1fb8783e"
IGW_ID="igw-00a0e38970f3a0e37"
SUBNET_ID="subnet-023af3220cdb2898a"
RTB_ID="rtb-01da6ad2cdb7429cc"
ASSOCIATION_ID="rtbassoc-03edaca4b6696d884"

# Delete the route from the route table
echo "Deleting route to IGW from Route Table: $RTB_ID"
aws ec2 delete-route \
    --route-table-id $RTB_ID \
    --destination-cidr-block 0.0.0.0/0 || echo "Route already deleted."

# Disassociate the subnet from the route table
echo "Disassociating Subnet $SUBNET_ID from Route Table $RTB_ID"
aws ec2 disassociate-route-table --association-id $ASSOCIATION_ID || echo "Association already disassociated."

# Delete the subnet
echo "Deleting Subnet: $SUBNET_ID"
aws ec2 delete-subnet --subnet-id $SUBNET_ID || echo "Subnet already deleted or not found."

# Detach and delete the internet gateway
echo "Detaching Internet Gateway: $IGW_ID from VPC: $VPC_ID"
aws ec2 detach-internet-gateway --internet-gateway-id $IGW_ID --vpc-id $VPC_ID || echo "Internet Gateway already detached."

echo "Deleting Internet Gateway: $IGW_ID"
aws ec2 delete-internet-gateway --internet-gateway-id $IGW_ID || echo "Internet Gateway already deleted."

# Delete the VPC
echo "Deleting VPC: $VPC_ID"
aws ec2 delete-vpc --vpc-id $VPC_ID || echo "VPC could not be deleted. Check for remaining dependencies."

echo "All resources cleanup complete."

```
![image](https://github.com/user-attachments/assets/b61287c0-54e0-4761-816b-85fa08b0414e)

![image](https://github.com/user-attachments/assets/eefd256b-d202-4692-a1dd-addd7a37174e)

![image](https://github.com/user-attachments/assets/383131cc-30dc-40fc-b83f-97f45b53229d)

![image](https://github.com/user-attachments/assets/c1cbc11e-8ecd-49ef-ac86-95e506da3f11)

![image](https://github.com/user-attachments/assets/0e60a902-78e7-4044-88a5-fea903ab2a48)

# NACL
![image](https://github.com/user-attachments/assets/25e26364-d791-4769-8d37-02c7e6261106)

![image](https://github.com/user-attachments/assets/214a1172-9d0f-40ea-84b5-3c20a41cc5d0)

![image](https://github.com/user-attachments/assets/c8963c4b-2b54-456f-88bd-0482979e9860)

![image](https://github.com/user-attachments/assets/f6830104-f549-499b-9226-c6f8b7fd58e6)

Create NACL
```sh
aws ec2 create-network-acl --vpc-id vpc-0b346576f3ad22b96
```


Launch VPC and subnet manaullay use it create ec2 and iam assume role.
Grab the ami image from the console as it changes based on selected region.

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: Launch an EC2 instance and install Apache with a custom index.html

Parameters:
  InstanceType:
    Type: String
    Default: t2.micro
  VPCId:
    Type: String
    Default: vpc-0b346576f3ad22b96
  ImageId:
    Type: String
    Default: ami-00da5f1c744fc2cc0
  SubnetId:
    Type: String
    Default: subnet-0ea63509565fa2dea

Resources:
  SSMRole:
    Type: 'AWS::IAM::Role'
    Properties:
      AssumeRolePolicyDocument:
        Version: "2012-10-17"
        Statement:
          - Effect: Allow
            Principal:
              Service: 
                - ec2.amazonaws.com
            Action: 'sts:AssumeRole'
      Policies:
        - PolicyName: SSMAccessPolicy
          PolicyDocument:
            Version: "2012-10-17"
            Statement:
              - Effect: Allow
                Action:
                  - "ssm:*"
                  - "ec2messages:*"
                  - "cloudwatch:*"
                  - "logs:*"
                Resource: "*"

  EC2InstanceProfile:
    Type: AWS::IAM::InstanceProfile
    Properties:
      Roles:
        - !Ref SSMRole 

  SG:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Allow HTTP and SSH traffic
      VpcId: !Ref VPCId
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 0.0.0.0/0
        - IpProtocol: tcp
          FromPort: 22
          ToPort: 22
          CidrIp: 0.0.0.0/0
      SecurityGroupEgress:
        - IpProtocol: -1
          FromPort: -1
          ToPort: -1
          CidrIp: 0.0.0.0/0

  MyEC2Instance: 
    Type: AWS::EC2::Instance
    Properties:
      IamInstanceProfile: !Ref EC2InstanceProfile
      InstanceType: !Ref InstanceType
      ImageId: !Ref ImageId
      SubnetId: !Ref SubnetId
      SecurityGroupIds:
        - !Ref SG
      UserData: 
        Fn::Base64: !Sub |
          #!/bin/bash
          yum update -y
          yum install -y httpd
          systemctl start httpd
          systemctl enable httpd
          echo '<!DOCTYPE html>
          <html>
          <body>
          <h1>My First Heading</h1>
          <p>My first paragraph.</p>
          </body>
          </html>' > /var/www/html/index.html

Outputs:
  InstanceId:
    Description: The Instance ID
    Value: !Ref MyEC2Instance
  InstancePublicIp:
    Description: Public IP of the created EC2 instance
    Value: !GetAtt MyEC2Instance.PublicIp
```





