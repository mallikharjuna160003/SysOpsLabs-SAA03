![image](https://github.com/user-attachments/assets/1d8e832b-3d11-490e-8437-532113f6df87)![image](https://github.com/user-attachments/assets/c25a91c7-3f2a-4d30-abaf-da99e3ada5d2)# AWS - SAA 03
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

aws ec2 describe-network-acls --query "NetworkAcls[*].{ID: NetworkAclId, VpcId: VpcId, Rules: Entries}" --output table

aws ec2 create-network-acl-entry \
    --network-acl-id <your-nacl-id> \
    --rule-number <rule-number> \
    --protocol <protocol> \
    --port-range From=<start-port>,To=<end-port> \
    --cidr-block <source-cidr> \
    --egress | --ingress \
    --rule-action <allow|deny>
# example

aws ec2 create-network-acl-entry \
    --network-acl-id nacl-abc12345 \
    --rule-number 100 \
    --protocol tcp \
    --port-range From=80,To=80 \
    --cidr-block 0.0.0.0/0 \
    --ingress \
    --rule-action allow

```


Launch VPC more manaullay use it create ec2 and iam assume role.
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


# Security Groups

![image](https://github.com/user-attachments/assets/f53053f0-c790-494d-8b16-13f91e069312)

![image](https://github.com/user-attachments/assets/972d47d3-2f02-4b93-9967-0dd022ea51d2)

![image](https://github.com/user-attachments/assets/fa765c82-35c6-4c20-9235-cdf469f74aef)

![image](https://github.com/user-attachments/assets/2f7743f3-4f0c-43c6-b595-ec0e32587f5c)

![image](https://github.com/user-attachments/assets/22749a64-3fd5-4b5a-841a-722674ac53b1)


for practice use the above template and manually change the sg rules inbound/outbound and with protocol and custom ip.

![image](https://github.com/user-attachments/assets/96ee1485-26eb-4397-8eda-b3da1b203f1a)

![image](https://github.com/user-attachments/assets/23c54ab8-3184-4c0b-b839-45f3253e33f9)

![image](https://github.com/user-attachments/assets/320dc3de-02ec-4942-9d9a-8ee43973a3c2)

![image](https://github.com/user-attachments/assets/0e9f5c79-f0a4-4954-9f90-5036280ce811)

Practice via the aws console adding associating to subnets .

# Gateways

![image](https://github.com/user-attachments/assets/59666a68-f932-49d3-8efe-e46209c07c0a)

# IGW
![image](https://github.com/user-attachments/assets/6aeed006-3208-425d-a6e0-06fe50b8a68d)

# EO-IGW
![image](https://github.com/user-attachments/assets/599f8908-9650-4c84-909a-bd708fe3636b)


Work on below
- creating vpc only
- create subnets private
- add nat gateway to access internet
- create ec2 instance
- try to access the internet from the private subnet ec2 from nat gateway.
- better try to work on aws console

# Elastic IPs

![image](https://github.com/user-attachments/assets/d8ef58e7-b2f7-4442-b65b-70febe10d47f)

![image](https://github.com/user-attachments/assets/095df2af-f83f-433e-8031-67743c622be3)


![image](https://github.com/user-attachments/assets/03c71d1f-010e-4997-bb13-7ea6a8c42843)

![image](https://github.com/user-attachments/assets/ce61871b-ef4a-4ee2-9d02-0f390781f73e)

![image](https://github.com/user-attachments/assets/57b89c41-d8f1-4956-9e13-e1fdd5de84e0)

![image](https://github.com/user-attachments/assets/a9aef02a-1399-432b-ad74-c8c2a86a54be)
# * Not shown up in exam *
![image](https://github.com/user-attachments/assets/ae551468-65eb-4053-ba8b-651283f30a87)

![image](https://github.com/user-attachments/assets/ce7bdf1e-22be-44c9-932b-251f359b6660)

![image](https://github.com/user-attachments/assets/0afecf40-24cd-407c-91bd-639567027900)

# VPC Endpoints

![image](https://github.com/user-attachments/assets/4f159795-38b3-44d4-9913-b27dcb3300af)

![image](https://github.com/user-attachments/assets/930b740b-6afe-42cf-9204-eef96b7765da)

![image](https://github.com/user-attachments/assets/ad9aeef3-e03f-439a-b29f-cf0c680949d5)

![image](https://github.com/user-attachments/assets/4f1066f4-8d00-4b20-8a34-dae4fc1bafe4)


![image](https://github.com/user-attachments/assets/79ade05c-cabf-4777-be7a-d9050b225bdf)

![image](https://github.com/user-attachments/assets/f7a81c1f-9349-481d-be1a-b0a76a84690c)

![image](https://github.com/user-attachments/assets/d0b1c85a-b647-4d53-91e2-d0bd8261c172)

![image](https://github.com/user-attachments/assets/0933ebc4-f187-43c7-9bb3-675ce3bdc135)

![image](https://github.com/user-attachments/assets/a99cc890-95b5-4716-9613-1106efaa04bf)

![image](https://github.com/user-attachments/assets/53069b85-9d60-4b39-8f0c-f2ca2c3bc26c)

![image](https://github.com/user-attachments/assets/feea61cb-1645-4f56-baac-8330281d1992)

![image](https://github.com/user-attachments/assets/069bd975-ef1c-462b-bffa-ad0208bdbf3b)

![image](https://github.com/user-attachments/assets/2685c8b4-1145-481d-8c14-58adf8c007ad)

![image](https://github.com/user-attachments/assets/bf227481-c767-4119-b6f5-e6ea6c8e49af)

![image](https://github.com/user-attachments/assets/c8fc4f17-1f08-4dbc-9474-7a3b969f3830)

![image](https://github.com/user-attachments/assets/1c630010-7480-4ad1-9fe0-661eeb41a05e)

![image](https://github.com/user-attachments/assets/0aa32181-3167-43e1-a1fc-88e60b3fe986)

![image](https://github.com/user-attachments/assets/1bf5dacf-6b88-43ed-bb84-2586fcb9060b)

![image](https://github.com/user-attachments/assets/263eef87-15b3-4858-885b-c1d82984edc2)

![image](https://github.com/user-attachments/assets/d54a0c3e-c20e-4822-8ba6-f6cacc6d20ed)

![image](https://github.com/user-attachments/assets/177fdb0e-63b0-4df6-93ac-196c95a8e66d)


![image](https://github.com/user-attachments/assets/2af8be76-9e60-4f21-a1b0-8dd0cfbefda9)

![image](https://github.com/user-attachments/assets/788ff8e1-2513-459c-bbb1-dfcb0fcdcbfb)

![image](https://github.com/user-attachments/assets/b89c021b-fe2d-4f9d-a99a-9a3227c5f0a0)

![image](https://github.com/user-attachments/assets/27628bb9-820c-4b7f-978c-f92633093b9e)

![image](https://github.com/user-attachments/assets/1362bf69-74a4-48ed-a5de-5e7c21213ff9)

![image](https://github.com/user-attachments/assets/4e215d91-69df-45e0-ab2b-b12f91039c76)

![image](https://github.com/user-attachments/assets/76c31639-8f27-4ad8-a4bc-288c2fdcd73c)


![image](https://github.com/user-attachments/assets/66c10e7d-4325-4bcd-abde-5cdb2431a75a)

![image](https://github.com/user-attachments/assets/fa422bcf-883c-4a37-806d-374fab423c5f)

![image](https://github.com/user-attachments/assets/23408acf-ea5b-49bb-a296-eb2edb9e4a17)


![image](https://github.com/user-attachments/assets/ccdebf96-8106-4cd2-8e05-28b4e673daa4)

# Steps for VPC peering
- Create two vpcs and use the nacl template to create the ec2 instances
- using cli connect the peering  and add them to both the route tables
- Try to access the other vpc ec2 instance httpd server via the curl in from not the same.

![image](https://github.com/user-attachments/assets/c8dc7471-8ba0-441d-aac5-79bd473a8b07)

![image](https://github.com/user-attachments/assets/767f04eb-b192-4cdf-b7d0-118a9676008b)

![image](https://github.com/user-attachments/assets/b7e0a20d-cd60-44e3-9685-bd7edde6e959)

![image](https://github.com/user-attachments/assets/73784f09-5940-4e53-a2ea-265bf2b821d0)




