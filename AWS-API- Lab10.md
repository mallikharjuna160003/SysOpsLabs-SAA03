# AWS SAA - 03
## AWS APIs

![image](https://github.com/user-attachments/assets/dd8431d1-8fb1-46b8-9a32-500e8a250c04)

![image](https://github.com/user-attachments/assets/13508d80-8fe0-4858-b953-963633df0651)

![image](https://github.com/user-attachments/assets/e1515777-fa93-4091-9db1-9af07e7db6c3)

![image](https://github.com/user-attachments/assets/db1e9e11-1fe2-49e4-bb09-a49ddb725f46)

![image](https://github.com/user-attachments/assets/091f63d3-8c9d-4786-ac8f-127f16bfeca9)

![image](https://github.com/user-attachments/assets/0007ffbd-1536-4aee-a50f-782cd064f560)

![image](https://github.com/user-attachments/assets/cbd20423-8371-42bd-afdb-168d11f4e064)

![image](https://github.com/user-attachments/assets/5e26d38f-64d3-4206-8e67-bd2243e3ee83)

![image](https://github.com/user-attachments/assets/a59e30c3-0809-4e6b-b553-ef2dda153c9d)

![image](https://github.com/user-attachments/assets/86280305-b06b-480c-98e0-f7c3bf4cb148)

![image](https://github.com/user-attachments/assets/41134ead-df81-4dab-864a-07005f68cb82)

# sts
### authentication using sts

```yaml
AWSTemplateFormatVersion: 2010-09-09
Description: Create a role for us to assume and create resource we'll have access to
Parameters:
  BucketName:
    Type: String
    Default: "sts-fun-ab-1234"

Resources:
  S3Bucket:
    Type: 'AWS::S3::Bucket'
    DeletionPolicy: Retain
    Properties:
      BucketName: !Ref BucketName

  StsRole:
    Type: 'AWS::IAM::Role'
    Properties:
      AssumeRolePolicyDocument:
        Version: "2012-10-17"
        Statement:
          - Effect: Allow
            Principal: "arn:aws:iam::533834059331:user/sts-machine-user"
              #Service:
              #  - s3.amazonaws.com
            Action:
              - 'sts:AssumeRole'
      Path: /
      Policies:
        - PolicyName: s3access
          PolicyDocument:
            Version: "2012-10-17"
            Statement:
              - Effect: Allow
                Action: 
                  - 's3:ListBucket'
                  - 's3:GetObject'
                  - 's3:PutObject'
                Resource: 
                  - !Sub 'arn:aws:s3:::${BucketName}' # access to bucket
                  - !Sub 'arn:aws:s3:::${BucketName}/*' # access to all objects

  RootInstanceProfile:
    Type: 'AWS::IAM::InstanceProfile'
    Properties:
      Path: /
      Roles:
        - !Ref StsRole # Corrected reference from RootRole to StsRole

```

```sh
#!/bin/bash
# create a new user with no permissions and generate out a access keys.

aws iam create-user \
    --user-name sts-machine-user

aws iam create-access-key \
    --user-name sts-machine-user --output table


# deploy stack
aws cloudformation deploy   --template-file template.yaml   --stack-name my-new-stack --capabilities CAPABILITY_IAM
```
### attach policy
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:AssumeRole",
      "Resource": "arn:aws:iam::533834059331:role/my-new-stack-StsRole-0LzF0vMwS4JT"
    }
  ]
}
```

```sh
# copy the credentials and use for 'aws configure'
aws configure

# Test who you are:
aws sts get-caller-identity --profile sts

# Open the credentials
open ~/.aws/credentials

# check access to s3 bucket for the default profile here user sts-machine-user
aws s3 ls --profile default

# Create a role

# We need to create a role that will access a new resource

## use new user credentials and assume role
aws iam put-user-policy \
    --user-name sts-machine-user \
    --policy-name StsAssumePolicy \
    --policy-document file://policy.json

aws sts assume-role \
    --role-arn arn:aws:iam::533834059331:role/my-new-stack-StsRole-0LzF0vMwS4JT \
    --role-session-name s3-sts-fun
    --profile default

```














