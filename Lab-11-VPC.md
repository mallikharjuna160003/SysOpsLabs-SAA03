# AWS - SAA 03
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


