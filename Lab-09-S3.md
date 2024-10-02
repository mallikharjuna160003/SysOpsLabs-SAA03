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

<a href="https://docs.aws.amazon.com/cli/latest/reference/s3api/put-object.html">Doc</a>
```sh
#!/bin/bash
# Create a bucket
aws s3 mb s3://encryption-fun-ab-123

# create a file
echo "Hello World" > hello.txt
aws s3 cp hello.txt s3://encryption-fun-ab-123

# Put object with encryption with kms
# arn:aws:kms:us-west-1:533834059331:key/3aa16d16-8b0e-4eae-8b4a-85b80be107be

aws s3api put-object --bucket encryption-fun-ab-123 \
  --key hello.txt \
  --server-side-encryption aws:kms \
  --ssekms-key-id alias/aws/s3 \
  --body hello.txt

## Put object with SSE-C

# Step 1: Generate a raw 32-byte key (256 bits)
export RAW_KEY=$(openssl rand -out /dev/stdout 32)

# Step 2: Print the raw key in hex format to ensure it's 32 bytes
echo "Raw Key (hex): $(echo -n $RAW_KEY | xxd -p | tr -d '\n')"
echo "Raw Key Length: $(echo -n $RAW_KEY | wc -c)"

# Step 3: Base64 encode the raw key
export BASE64_ENCODED_KEY=$(echo -n $RAW_KEY | base64)

# Step 4: Print the base64-encoded key
echo "Base64 Encoded Key: $BASE64_ENCODED_KEY"

# Step 5: Calculate the MD5 of the raw key
export MD5_VALUE=$(echo -n $RAW_KEY | md5sum | awk '{ print $1 }' | base64)

# Step 6: Print the MD5 value
echo "MD5 Value: $MD5_VALUE"

# Step 7: Verify the decoded key length
DECODED_KEY=$(echo -n $BASE64_ENCODED_KEY | base64 --decode)
echo "Decoded Key Length: $(echo -n "$DECODED_KEY" | wc -c)"  # Should output 32

# Step 8: Upload the object to S3 using SSE-C
aws s3api put-object --bucket encryption-fun-ab-123 \
  --key="hello1.txt" \
  --body="hello1.txt" \
  --sse-customer-algorithm="AES256" \
  --sse-customer-key=$BASE64_ENCODED_KEY

![image](https://github.com/user-attachments/assets/a6568f1e-7b16-48c5-a946-478bde15c3e0)


#

```






