# AWS SAA-03
## Lab 01
- Creating AWS CLI setup install AWS cli based on machine OS.
  - Doc: <a href="https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html"> AWS CLI installtion </a>
- For VSCode editor install the plugins AWS toolkit.
- For configuring the AWS CLI create an IAM role and create a key use the access ID and access key and region to work on.Check the credentials are set and good to work on.
####  Creating IAM role.
- fill the username form  give permissions say Admin access Groupname if not present create one group.
- Under security credentials create access key. 
```sh
aws sts get-caller-identity
```
