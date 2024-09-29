## AWS SAA-03
### S3 bucket:
![image](https://github.com/user-attachments/assets/faa36ffd-e1aa-4747-9b50-43eb798e4d09)
- S3 bucket resource are deployed in particular region. But appear in a global namespace.
## Creating a bucket.
- Provide the Bucket name, AWS region.Object ownership ACL disabled access to particular ACL.
- Block public access
- Versioning - enable /disable
- Default Encryption - SSE-S3
- Bucket Key.
  ![image](https://github.com/user-attachments/assets/cb9549dc-b78c-4099-b568-43eebe83b536)
- Object lock Write once and read many
  ![image](https://github.com/user-attachments/assets/af402f86-34b1-4b91-bbd4-7b7f14f2ef03)
- Create the bucket.
    - Do search about KMS,CKMS services
## uploading the files to S3 bucket
- For generally upload the image in s3 bucket create by drag and drop.
  ![image](https://github.com/user-attachments/assets/50c53e61-361b-469b-b8f7-dd2a83832408)
- For permissions and destination
  ![image](https://github.com/user-attachments/assets/c9a9c199-b23a-4fee-8fb6-dae534014e3d)
- Storage Class
  ![image](https://github.com/user-attachments/assets/14884247-67f0-4d62-8d39-94c70c9e881f)
- For tagging to object and checksum for more encryption etc..
 ![image](https://github.com/user-attachments/assets/54bde492-3b7b-4c3c-9b4b-876487105695)

## Using AWS cloud shell
- We can access the s3 resource via S3 api SDK or via AWS cli.
---
![image](https://github.com/user-attachments/assets/c37e3626-0390-473e-be9d-ff8ee5bc793d)

---
AWS cli autopromt will prompt the cli commands

![image](https://github.com/user-attachments/assets/eec22c1a-c095-4e43-a02a-9ccc18813119)

![image](https://github.com/user-attachments/assets/414ec0ec-0d0e-4950-b159-9eb726a7b69e)

<a hre="https://docs.aws.amazon.com/cli/latest/reference/s3/"> AWS S3 CLI commands</a>

<a href="https://docs.aws.amazon.com/cli/latest/userguide/cli-usage-parameters-prompting.html"> CLI autoprompt</a>

