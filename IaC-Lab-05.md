## AWS SAA-03
### IaC
Infrastructure as Code using the Cloud Formation. We can also do  this using Terraform.
```yaml
# template.yaml

AWSTemplateFormatVersion: "2010-09-09"
Description: A Simple s3 bucket
Resources:
  S3Bucket:
    Type: 'AWS::S3::Bucket'

```
## Deploying the infrastructure 
```sh
#!/bin/bash
# deploy.sh

STACK_NAME="my-s3-stack"
aws cloudformation deploy \
--template-file template.yaml \
--region ca-central-1 \
--stack-name $STACK_NAME

```

## Delete stack

```sh
#!/bin/bash

STACK_NAME="my-s3-stack"

aws cloudformation delete-stack \
--stack-name $STACK_NAME \
--region ca-central-1

```









