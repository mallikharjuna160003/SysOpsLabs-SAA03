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
            Principal:
              AWS: "arn:aws:iam::<account-id>:user/sts-machine-user"
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
      "Resource": "arn:aws:iam::<account-id>:role/my-new-stack-StsRole-0LzF0vMwS4JT"
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
    --role-arn arn:aws:iam::<account-id>:role/my-new-stack-StsRole-0LzF0vMwS4JT \
    --role-session-name s3-sts-fun
    --profile default
```
assume role creation is for temporary access to aws resource.
```sh
# update the role policy to allow the sysops user to assumeroles.
aws iam update-assume-role-policy \
    --role-name my-new-stack-StsRole-ABiCr0weaoj3 \
    --policy-document '{
      "Version": "2012-10-17",
      "Statement": [
        {
          "Effect": "Allow",
          "Principal": {
            "AWS": [
              "arn:aws:iam::<account-id>:user/sts-machine-user",
              "arn:aws:iam::<account-id>:user/sysops"
            ]
          },
          "Action": "sts:AssumeRole"
        }
      ]
    }' --profile default

aws sts assume-role     --role-arn arn:aws:iam::<account-id>:role/my-new-stack-StsRole-ABiCr0weaoj3     --role-session-name UniqueSessionName     --profile sts
```
Now update the ~/.aws/credentials file with the below update the values make changes 
```sh
[assumed]
aws_access_key_id = <key-id>
aws_secret_access_key = <secret-key>
aws_session_token = <token>
```
Check the assumed role permissions and identity
```sh
aws sts get-caller-identity --profile assumed
aws s3 ls --profile assumed
```
```json
{
    "Credentials": {
        "AccessKeyId": "<id>",
        "SecretAccessKey": "<key>",
        "SessionToken": "<token>",
        "Expiration": "2024-10-02T19:20:51+00:00"
    },
    "AssumedRoleUser": {
        "AssumedRoleId": "<roleid>:UniqueSessionName",
        "Arn": "arn:aws:sts::<accountid>:assumed-role/my-new-stack-StsRole-ABiCr0weaoj3/UniqueSessionName"
    }
}
```

Now update the template.yaml file with Service we deleted before under Principal object.
```yaml
Service:
  - s3.amazonaws.com
```

Now update the template.yaml file, bcz with the previous changes assumed role is not able to list the buckets. now check after updating the cft stack.
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
            Principal:
              AWS: "arn:aws:iam::<account-id>:user/sts-machine-user"
                #Service:
                # - s3.amazonaws.com
            Action:
              - 'sts:AssumeRole'
      Path: /
      Policies:
        - PolicyName: s3access
          PolicyDocument:
            Version: "2012-10-17"
            Statement:
              - Effect: Allow
                Action: 's3:*'
                Resource: [ 
                   !Sub 'arn:aws:s3:::*',
                   !Sub 'arn:aws:s3:::${BucketName}',
                   !Sub 'arn:aws:s3:::${BucketName}/*'
                ]
  RootInstanceProfile:
    Type: 'AWS::IAM::InstanceProfile'
    Properties:
      Path: /
      Roles:
        - !Ref StsRole # Corrected reference from RootRole to StsRole

```

### Now teardown the cft stack on aws console
```sh
# delete policy
aws iam delete-user-policy     --user-name sts-machine-user --policy-name AssumeRolePolicy

# delete accesskey
aws iam delete-access-key     --access-key-id <key-id>     --user-name sts-machine-user

# delete user
aws iam delete-user \
    --user-name sts-machine-user
```

![image](https://github.com/user-attachments/assets/27b14cef-6511-4140-90d3-eb761beed500)

![image](https://github.com/user-attachments/assets/78da1221-983b-4d2c-b90d-70d3a7e8b4d6)

![image](https://github.com/user-attachments/assets/43da37fd-54b2-42f4-a896-ce70d770075c)

![image](https://github.com/user-attachments/assets/1e3e1377-ca8b-4b48-965b-bd082b3c71ee)

![image](https://github.com/user-attachments/assets/c34112e3-c47c-4b3d-ab88-a851799d73ce)

![image](https://github.com/user-attachments/assets/3d0f22bb-04df-4936-b3d5-366780f7df96)

![image](https://github.com/user-attachments/assets/da066667-4c27-40e2-84d1-90b8e29d3840)

![image](https://github.com/user-attachments/assets/6d353855-bfce-48ea-8082-59485672d5ec)

![image](https://github.com/user-attachments/assets/359974a7-e3ca-4cfc-91c1-d575a857eaf9)

![image](https://github.com/user-attachments/assets/f0ad7cc1-265d-4e9b-b55d-6ed08774f56c)

![image](https://github.com/user-attachments/assets/bf415731-f307-4b4b-a9b1-31028533e11b)

![image](https://github.com/user-attachments/assets/ea599a74-44ae-4917-8dc6-4b039b5b73b3)

![image](https://github.com/user-attachments/assets/53f4f547-6ad2-4f78-b135-09e1fcbbaa0b)

![image](https://github.com/user-attachments/assets/1fbe18ea-6611-477d-8b39-81cb237733d6)

![image](https://github.com/user-attachments/assets/54b26e7e-ce28-409c-a7b3-a6cbf0d43096)

![image](https://github.com/user-attachments/assets/6a23498b-5964-4e21-a165-655e6a811caa)

![image](https://github.com/user-attachments/assets/96fe4ce6-f13a-471d-af62-50ee6235f81e)


![image](https://github.com/user-attachments/assets/b5ffde85-9514-4e75-8d8e-839728b9a28b)

![image](https://github.com/user-attachments/assets/a25e976a-d510-46ad-88d0-3059eb31ca41)

![image](https://github.com/user-attachments/assets/1069fce5-69fb-4d93-a7c1-9ad3c3743e05)

![image](https://github.com/user-attachments/assets/8b11c237-3fdc-40e7-a7ed-6f0fde2b06a6)





