## AWSs SAA-03
## Create a new bucket
```sh
aws s3api create-bucket --bucket acl-example-ab-123 --region us-east-1
```
## Turn of Block Public Access for ACLs

```sh
aws s3api put-public-access-block \
    --bucket acl-example-ab-123  \
    --public-access-block-configuration "BlockPublicAcls=false,IgnorePublicAcls=false,BlockPublicPolicy=true,RestrictPublicBuckets=true"
```

## Get public access block
```sh
aws s3api get-public-access-block \
    --bucket acl-example-ab-123
```

## change ownership of bucket object
```sh
aws s3api put-bucket-ownership-controls \
    --bucket acl-example-ab-123 \
    --ownership-controls="Rules=[{ObjectOwnership=BucketOwnerEnforced}]"
```
## Get the ownership of the bucket object
```sh
aws s3api get-bucket-ownership-controls \
    --bucket acl-example-ab-123
```
# using other account to provide access
![image](https://github.com/user-attachments/assets/198c8fec-f39f-42fa-baab-2a7d0b73ace2)

## Change acls to allow allow access to other account
```sh
aws s3api put-bucket-acl --bucket acl-example-ab-123 --access-control-policy file://temp.json
```
Get the canonical id from the aws security credentials page
temp.json
```json
{
  "AccessControlPolicy": {
    "Owner": {
      "ID": "*** Owner-Canonical-User-ID ***",
      "DisplayName": "owner-display-name"
    },
    "AccessControlList": {
      "Grant": {
        "Grantee": {
          "xsi:type": "Canonical User",
          "ID": "*** Owner-Canonical-User-ID ***",
          "DisplayName": "display-name"
        },
        "Permission": "FULL_CONTROL"
      }
    }
  }
}

```

```sh
aws s3api list-buckets --query Owner.ID --output text
```
After this try to perform the CRUD operations on the other account Highlevel aws s3 commands

![image](https://github.com/user-attachments/assets/f22dfd38-c8c9-4b7c-8f9f-fbfdf36c3215)

![image](https://github.com/user-attachments/assets/3889f0f0-6fe2-42bd-97ef-388fb7e6cafc)

![image](https://github.com/user-attachments/assets/f522bdf4-c98c-4626-aff0-3ddbc90819c7)

![image](https://github.com/user-attachments/assets/ed5b08f7-01de-4fd8-8dd4-9cff0afa85d4)

In production never use the '*' in configuration it will allows every cross domain.

![image](https://github.com/user-attachments/assets/7bc116d0-d355-4ffa-bd54-9d3df807245e)















