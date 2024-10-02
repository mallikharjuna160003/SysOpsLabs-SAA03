# AWS SAA-03
#### Encryption at Rest
![image](https://github.com/user-attachments/assets/cfa89425-0fa5-4a5e-8f51-1c5be4469069)

#### Encryption In Transit
![image](https://github.com/user-attachments/assets/3f32f765-c270-4af3-853f-bcd63ccee136)

#### Server side Encryption
![image](https://github.com/user-attachments/assets/b97ce7af-7e58-461a-ae80-d1f79ccbb11d)

![image](https://github.com/user-attachments/assets/723d7891-dffc-48f4-b1b1-1b56228b0b2b)

### SSE - KMS
![image](https://github.com/user-attachments/assets/6d0f1c5c-9f1d-4874-9906-b4afaf4d2e55)

### SSE - C
![image](https://github.com/user-attachments/assets/8c1da59b-29c0-4a9b-87c0-7610cedfdc44)

### SSE-Dual
![image](https://github.com/user-attachments/assets/e70c3e21-e9d0-4222-9731-ce6ed41d47cb)

```sh
#!/bin/bash
# Create a bucket
aws s3 mb s3://encryption-fun-ab-123

# create a file
echo "Hello World" > hello.txt
aws s3 cp hello.txt s3://encryption-fun-ab-123

#

```






