## AWS SAA-03
### IaC
Infrastructure as Code using the Cloud Formation.
```yaml
# template.yaml

AWSTemplateFormatVersion: "2010-09-09"
Description: A Simple s3 bucket
Resources:
  S3Bucket:
    Type: 'AWS::S3::Bucket'

```


Deploying the infrastructure 

```sh
STACK_NAME="my-s3-stack"
aws cloudformation deploy --template-file template.yaml --stack-name $STACK_NAME
```
