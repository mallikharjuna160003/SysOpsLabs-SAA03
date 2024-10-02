# AWS SAA- 03
## Hosting a static website on S3

## Create a bucket
```sh
#!/bin/bash

# Create the bucket
aws s3 mb s3://cors-fun-ab-123

# Change block public access
aws s3api put-public-access-block \
    --bucket cors-fun-ab-123 \
    --public-access-block-configuration "BlockPublicAcls=true,IgnorePublicAcls=true,BlockPublicPolicy=false,RestrictPublicBuckets=false"

# Create a bucket policy
aws s3api put-bucket-policy --bucket cors-fun-ab-123 --policy file://policy.json

# Turn on static website hosting
aws s3api put-bucket-website --bucket cors-fun-ab-123 --website-configuration file://website.json

# Create the index.html file
cat <<EOT > index.html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>My Website</title>
<link rel="stylesheet" href="https://fonts.googleapis.com/css?family=Sofia">
<style>
body {
  font-family: "Sofia", sans-serif;
}
</style>

  </head>
  <body>
    <main>
        <h1>Welcome to My Website</h1>
        <p> This is a static website hosted on S3</p>
    </main>
    <script src="index.js"></script>
  </body>
</html>
EOT

# Upload index.html to S3
aws s3 cp index.html s3://cors-fun-ab-123

# Get the region of the bucket
region=$(aws configure get region)
# https://cors-fun-ab-123.s3.us-west-1.amazonaws.com/index.htm

# Construct the S3 website URL
website_url="https://cors-fun-ab-123.s3.${region}.amazonaws.com/index.html"

# Print the website URL
echo "Website is hosted at: $website_url"

# Optionally, perform a curl request to test the URL
curl $website_url

```
website.json
```json
{
    "IndexDocument": {
        "Suffix": "index.html"
    },
    "ErrorDocument": {
        "Key": "error.html"
    }
}


```
policy.json
```json
{
"Version": "2012-10-17",
"Statement": [
      {
         "Sid": "PublicReadGetObject",
         "Effect": "Allow",
         "Principal": "*",
         "Action": "s3:GetObject",
         "Resource": ["arn:aws:s3:::cors-fun-ab-123/*"]
      }
      
   ]
}
```
