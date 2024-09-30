# AWS SAA- 03
Working with the terraform for IaC  on AWS

<a href="https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli"> Installing Terraform </a>
# Deploying S3 bucket using  terraform 
```tf
# main.tf

terraform {
  required_providers {
    aws = {
      source = "hashicorp/aws"
      version = "5.69.0"
    }
  }
}

provider "aws" {
  # Configuration options
  region = "ca-central-1"
}


resource "aws_s3_bucket" "s3_bucket" {

  bucket = "my-s3-bucket-123"

  tags = {
    Name = "My Bucket"
    Environment = "Dev"
  }
}
```
