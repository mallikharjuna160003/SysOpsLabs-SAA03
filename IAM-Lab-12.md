# AWS SAA-03
## IAM
for an ec2 instance to access s3 bucket we create role not a policy.
![image](https://github.com/user-attachments/assets/94f17df0-a0cd-495c-9565-0b7880120314)

![image](https://github.com/user-attachments/assets/d8c0c648-ded4-4aef-856a-3597433ef9ff)

Create a policy 
![image](https://github.com/user-attachments/assets/45d87e2a-5993-435d-8167-f9f377350014)

Attaching to role Iam user
![image](https://github.com/user-attachments/assets/dc047ef8-c4b5-49b0-a791-4b8ec5bedf57)

Roles are for services, we dont need here.

```yaml
# template.yaml
Resources:
  MyUser:
    Type: AWS::IAM::User
    Properties:
      # Uncomment and set a user name if necessary
      # UserName: 'my-cool-user'
      ManagedPolicyArns: 
        - arn:aws:iam::aws:policy/job-function/ViewOnlyAccess
      Path: "/"
      Policies:
        - PolicyName: MyCoolPolicy
          PolicyDocument:
            Version: 2012-10-17
            Statement:
              - Sid: IamListAccess
                Effect: Allow
                Action:
                  - 'iam:ListRoles'
                  - 'iam:ListUsers'
                Resource: '*'

  MyInlinePolicy:
    Type: 'AWS::IAM::UserPolicy'
    Properties:
      PolicyName: myinlinePolicy
      PolicyDocument:
        Version: "2012-10-17"
        Statement:
          - Effect: Allow
            Action: '*'
            Resource: '*'
      UserName: !Ref MyUser

  MySecondInlinePolicy:
    Type: 'AWS::IAM::UserPolicy'
    Properties:
      PolicyName: MySecondInlinePolicy
      PolicyDocument:
        Version: "2012-10-17"
        Statement:
          - Effect: Allow
            Action: '*'
            Resource: '*'
      UserName: !Ref MyUser

```

![image](https://github.com/user-attachments/assets/0eb44261-1bf6-4a32-83fb-7fbc03d01890)

![image](https://github.com/user-attachments/assets/0569ee6b-557a-46e2-acc3-ecb9e1c3cc56)

on root user create the policy like below.
![image](https://github.com/user-attachments/assets/2d336437-b8e4-43eb-9664-75350402966d)



# Policies 
Creating customer managed policies. We can write the policy.json file using yaml and then convert to json

```yaml
Version: "2012-10-17"
Statement:
  - Sid: "AccessToS3"
    Effect: "Allow"
    Action: "s3:*"
    Resource: "*"
```
```sh
yq -o json policy.yaml > policy.json
```

```sh

# create policy

aws iam create-policy \
    --policy-name my-policy \
    --policy-document file://policy

# Attach policy to user
aws iam attach-user-policy \
    --policy-arn arn:aws:iam::aws:policy/AdministratorAccess \
    --user-name Alice
```

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:Get*",
                "s3:List*"
            ],
            "Resource": "*"
        }
    ]
}
```
Output:
```md
{
    "Policy": {
        "PolicyName": "my-policy",
        "PolicyId": "ANPAXYSX5JZBVXVXQQX4P",
        "Arn": "arn:aws:iam::<acc-id>:policy/my-policy",
        "Path": "/",
        "DefaultVersionId": "v1",
        "AttachmentCount": 0,
        "PermissionsBoundaryUsageCount": 0,
        "IsAttachable": true,
        "CreateDate": "2024-10-04T18:02:31+00:00",
        "UpdateDate": "2024-10-04T18:02:31+00:00"
    }
}
```
Updating the policy

```sh
aws organizations update-policy \
    --policy-id p-examplepolicyid111 \
    --name Renamed-Policy \
    --description "This description replaces the original."
```
Multiple buckets policy
![image](https://github.com/user-attachments/assets/dc489185-d6bb-489f-8858-85f0fbe5ea24)

Delete policy with version
![image](https://github.com/user-attachments/assets/ea9ef71d-63bf-4939-8bf6-de6e8e842a97)

![image](https://github.com/user-attachments/assets/5a3570f6-588c-4c22-b216-e9ff48585e85)

![image](https://github.com/user-attachments/assets/b75173ff-ea34-464d-aba1-696e8b11bf92)

![image](https://github.com/user-attachments/assets/b8726ec9-0e36-40dc-95b0-4944577e5f16)

![image](https://github.com/user-attachments/assets/967556e5-c87d-402b-9487-cbe302a86228)

![image](https://github.com/user-attachments/assets/65c7aad9-ce61-4848-b62c-1eda72c0d23a)

![image](https://github.com/user-attachments/assets/167e634a-aa8d-4bd0-adcc-c3b9f624c4b3)


![image](https://github.com/user-attachments/assets/e777d56c-3a04-46f3-bac0-b7215408d7d7)

![image](https://github.com/user-attachments/assets/61a29b87-a493-40c8-84be-0849df555d3b)
### Creating Access key for the current user
![image](https://github.com/user-attachments/assets/28d2bedb-959c-443f-8695-049664aa67e9)

![image](https://github.com/user-attachments/assets/867d99c4-2cc5-4c8b-91a9-90d12bd96e01)


![image](https://github.com/user-attachments/assets/82c7250b-6d33-4063-8ae8-dd844f72689c)

![image](https://github.com/user-attachments/assets/a7d7e93e-8b26-4a27-a88e-680302e5a248)

![image](https://github.com/user-attachments/assets/18ee6f16-483a-472d-b73f-51291ef9d717)

### Read doc for Cloudinit 

![image](https://github.com/user-attachments/assets/a06348f2-c177-4ed8-ba1c-ffaf9c664c82)

![image](https://github.com/user-attachments/assets/cfda8550-9b57-4f52-83c0-322df2fe0caa)

![image](https://github.com/user-attachments/assets/34b0dde1-6252-4d99-8c9f-69af794c51fa)

![image](https://github.com/user-attachments/assets/1cef0a20-3045-4a15-b5a4-fcd40aaaccbc)

![image](https://github.com/user-attachments/assets/806e476f-4156-4e76-9488-1f058231dfb7)

![image](https://github.com/user-attachments/assets/f0372a43-8a88-40ed-bc23-ec02df76f4a5)

![image](https://github.com/user-attachments/assets/121eac88-9f5c-4644-9bfa-87918918854d)

Retrieve instance metadata
![image](https://github.com/user-attachments/assets/f589d887-b30e-44fb-ac2b-e820c95a4053)

![image](https://github.com/user-attachments/assets/f5844d8b-1725-4253-bb95-e16d75230fe1)

![image](https://github.com/user-attachments/assets/e505b53b-b8ee-41aa-97e5-6eb2320f33c0)

![image](https://github.com/user-attachments/assets/4a90b889-777f-4d44-b3ed-9095b1335d73)

![image](https://github.com/user-attachments/assets/4f8aad7d-6f81-4983-b69b-a6874543053d)

![image](https://github.com/user-attachments/assets/3f451db4-bc2b-4ada-ac16-3b155feba385)

![image](https://github.com/user-attachments/assets/2addd64c-56b6-44f2-9909-a90bdf2610f0)

![image](https://github.com/user-attachments/assets/d3c71501-914e-4f54-a0a7-2b33dd86cdbb)

![image](https://github.com/user-attachments/assets/56c91736-646a-489a-bd6f-7468c0e5afdd)

![image](https://github.com/user-attachments/assets/f41fef19-910e-4b82-93ec-b893b8674669)

![image](https://github.com/user-attachments/assets/76cc6f97-8d10-47cc-846e-a654cff9cd33)

![image](https://github.com/user-attachments/assets/502163f8-6094-455f-ac20-6e0c42488b69)

![image](https://github.com/user-attachments/assets/02151b1b-ad2a-4791-a726-6f24b0b98975)

![image](https://github.com/user-attachments/assets/324878ac-46ad-45d5-973e-041815a02117)

![image](https://github.com/user-attachments/assets/d47e5db8-e09d-476e-9225-fdd4fd69e043)

![image](https://github.com/user-attachments/assets/4bd64fc6-4d88-48f1-b1b5-e1ba56ddb51f)

![image](https://github.com/user-attachments/assets/7a726c92-b6ba-4ef5-bcb9-fd950beecfda)

![image](https://github.com/user-attachments/assets/24562d69-4a4b-41d4-83b7-3a47346c1624)

![image](https://github.com/user-attachments/assets/a30a16a9-9e41-4612-a919-41d21d929f2d)

![image](https://github.com/user-attachments/assets/c16f1d81-076b-40e5-a5af-903fe5e47aed)

![image](https://github.com/user-attachments/assets/ae148ae3-18b3-45a7-8c9c-fd56d5bdaf39)

EC2 instance connect through ssh keys
![image](https://github.com/user-attachments/assets/b60b15f5-b121-4e3f-bc55-adbd092f90a7)

![image](https://github.com/user-attachments/assets/d263e8b2-b45c-491c-a10b-e847dd8c1d81)

![image](https://github.com/user-attachments/assets/d37e0bef-769f-4ba7-8529-cafdf90acf88)

![image](https://github.com/user-attachments/assets/a9298b11-a0f1-4d23-94fb-e95c6a518849)

![image](https://github.com/user-attachments/assets/9b387c14-60db-4d72-9c2e-25236058e76d)

![image](https://github.com/user-attachments/assets/0c089a8a-9fbf-4aec-ac7e-7c6f1cbce664)

![image](https://github.com/user-attachments/assets/ee909ca0-7e98-47ce-8d9c-3122c51f3138)

![image](https://github.com/user-attachments/assets/cb9518d1-de54-4847-8fdc-6d065acc3e07)

![image](https://github.com/user-attachments/assets/3665943e-2e07-4981-baf5-e9fadea5c35d)

![image](https://github.com/user-attachments/assets/36c88351-ff7b-49e3-8ad6-f752a4ae7f03)

![image](https://github.com/user-attachments/assets/7bb57f94-e2c3-4d94-91ff-71eb31e3059c)

![image](https://github.com/user-attachments/assets/34523045-6259-41a9-b293-2a72453635f1)


![image](https://github.com/user-attachments/assets/cf824047-8f82-4868-b233-e67db114a7fc)

![image](https://github.com/user-attachments/assets/7bfbb8e9-8c3f-4385-a3eb-6b402ac559f8)

![image](https://github.com/user-attachments/assets/370d6fee-b0f8-43e2-8a5c-4b57d77010b9)

![image](https://github.com/user-attachments/assets/6adc8896-d2e4-4269-ac7c-382b7bce17a3)

![image](https://github.com/user-attachments/assets/57b00923-9d43-4d0a-b3d7-620e2e21ae6a)

![image](https://github.com/user-attachments/assets/bdc1ef79-9d7d-4a7e-b2a8-ba9df5b094a3)

Creating image 
![image](https://github.com/user-attachments/assets/7ac1e41f-a264-476d-9e38-e45ed52f8074)

![image](https://github.com/user-attachments/assets/1bc7802b-f1f8-4fa0-9dfd-3e3dd894069e)






































