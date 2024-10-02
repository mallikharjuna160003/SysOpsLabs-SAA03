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


```
![image](https://github.com/user-attachments/assets/a6568f1e-7b16-48c5-a946-478bde15c3e0)

![image](https://github.com/user-attachments/assets/44114883-5d82-4278-81b3-5a9aaaf19b15)

## Client side encryption
```py
import boto3
import os
from cryptography.hazmat.primitives.ciphers import Cipher, algorithms, modes
from cryptography.hazmat.backends import default_backend
from cryptography.hazmat.primitives import padding

# Initialize AWS S3 client
s3 = boto3.client('s3')

# Your bucket name
BUCKET_NAME = 'client-side-mugg'

# Path to the file you want to upload
FILE_PATH = 'your-file.txt'

# Generate a 256-bit (32 bytes) key for AES
KEY = os.urandom(32)

def encrypt_data(data, key):
    """Encrypt data using AES256 with CBC mode"""
    iv = os.urandom(16)  # Initialization vector
    cipher = Cipher(algorithms.AES(key), modes.CBC(iv), backend=default_backend())
    encryptor = cipher.encryptor()
    
    # Padding to ensure the data is a multiple of block size
    padder = padding.PKCS7(algorithms.AES.block_size).padder()
    padded_data = padder.update(data) + padder.finalize()
    
    ciphertext = encryptor.update(padded_data) + encryptor.finalize()
    
    return iv + ciphertext  # Return the IV concatenated with the ciphertext

def decrypt_data(encrypted_data, key):
    """Decrypt AES256 encrypted data"""
    iv = encrypted_data[:16]  # Extract the IV
    ciphertext = encrypted_data[16:]  # The rest is ciphertext
    
    cipher = Cipher(algorithms.AES(key), modes.CBC(iv), backend=default_backend())
    decryptor = cipher.decryptor()
    
    decrypted_padded_data = decryptor.update(ciphertext) + decryptor.finalize()
    
    # Remove padding
    unpadder = padding.PKCS7(algorithms.AES.block_size).unpadder()
    data = unpadder.update(decrypted_padded_data) + unpadder.finalize()
    
    return data

# Read the file you want to upload
with open(FILE_PATH, 'rb') as f:
    file_data = f.read()

# Encrypt the file data before uploading to S3
encrypted_data = encrypt_data(file_data, KEY)

# Upload the encrypted data to S3
s3.put_object(Bucket=BUCKET_NAME, Key='encrypted-file.txt', Body=encrypted_data)
print(f'Encrypted file uploaded to {BUCKET_NAME}/encrypted-file.txt')

# Download the encrypted file from S3
response = s3.get_object(Bucket=BUCKET_NAME, Key='encrypted-file.txt')
downloaded_encrypted_data = response['Body'].read()

# Decrypt the downloaded data
decrypted_data = decrypt_data(downloaded_encrypted_data, KEY)

# Save the decrypted data to a new file
with open('decrypted-file.txt', 'wb') as f:
    f.write(decrypted_data)

print("File decrypted and saved as 'decrypted-file.txt'")

```
![image](https://github.com/user-attachments/assets/0ebb4c8b-c75a-4bfd-bdf7-5090911e7737)

![image](https://github.com/user-attachments/assets/f38bb5ee-7700-499f-80f8-bb3b80be0a2e)

![image](https://github.com/user-attachments/assets/18b5da3e-fdf4-4a71-a26e-c3eb0fe3388d)

![image](https://github.com/user-attachments/assets/bf8a8bbf-360c-4212-aa0e-9e667840a2e7)

![image](https://github.com/user-attachments/assets/86446e97-3ed6-458b-8831-559db2319cd0)

![image](https://github.com/user-attachments/assets/c9da95bd-76ce-4142-b923-84d89ac27f3f)

![image](https://github.com/user-attachments/assets/57a2364b-a9fd-43c4-a018-902318e24207)

![image](https://github.com/user-attachments/assets/b16162f4-e33e-47a7-896b-ae39d593fb38)

![image](https://github.com/user-attachments/assets/a7ab9cdf-9f1d-4f8f-9370-b87041487beb)

![image](https://github.com/user-attachments/assets/df3fe1fc-a39e-4f5a-b77b-87f6cbf19e82)

![image](https://github.com/user-attachments/assets/b4912442-8863-41e8-be3a-9b4e62cad38d)

![image](https://github.com/user-attachments/assets/93d0378b-cbf7-4433-bed9-4a80dc1ba435)

![image](https://github.com/user-attachments/assets/72d0055a-a954-4446-a139-b37d0f918f1e)

![image](https://github.com/user-attachments/assets/53a66874-5295-4cd3-80e2-59a22d338ea1)

![image](https://github.com/user-attachments/assets/5961f4a1-9977-44dc-87a6-f0429d11d20f)

![image](https://github.com/user-attachments/assets/695b2a47-ede1-4ab0-af46-9430ccbd1b41)

![image](https://github.com/user-attachments/assets/09f4f3a6-b3d6-4dc6-ac41-d1237768d59a)

![image](https://github.com/user-attachments/assets/0d00913e-6d7e-451c-851d-6c485e9f72c5)

![image](https://github.com/user-attachments/assets/5c9ca6a5-c513-4fe1-ac43-168093210d15)

![image](https://github.com/user-attachments/assets/fdeb4951-e200-403c-9acf-e8466ca9506b)

# Event Driven Actions
![image](https://github.com/user-attachments/assets/5f968621-3733-41e8-9970-d78301075a97)

# Storage Class Analysis
![image](https://github.com/user-attachments/assets/459d04a3-4c17-4ed1-af3d-aa17cba1fd81)

# Storage Class Lens
![image](https://github.com/user-attachments/assets/5871c2d1-1bd0-4246-a70a-ad05626e7e43)

# S3 Event Notification
![image](https://github.com/user-attachments/assets/654e35d5-0a92-45f7-82a9-b8220ad829b1)

# S3 Storage Class Analysis
![image](https://github.com/user-attachments/assets/fb500379-6c32-4ff5-8fe3-2c167f9bf922)

# Storage Lens
![image](https://github.com/user-attachments/assets/5e8deefa-891d-467a-b5f4-45c17554840a)

# S3 static website hosting

![image](https://github.com/user-attachments/assets/056d0715-8eff-482d-8e8b-a8164b32f63a)

![image](https://github.com/user-attachments/assets/17e5dce2-eef8-4bfc-a9a6-fd82e135a3e4)

![image](https://github.com/user-attachments/assets/2c51181d-258f-4f28-8c5c-43eb613b4341)

![image](https://github.com/user-attachments/assets/c7e9b7c8-c5db-4096-8d53-993d21fb9e98)

![image](https://github.com/user-attachments/assets/4d461e52-452d-4f94-b668-868c413f560b)

![image](https://github.com/user-attachments/assets/fab74b3c-166a-4671-b112-1d5c90087065)

![image](https://github.com/user-attachments/assets/edbfae78-8342-44a1-b166-8075f42c6924)



















