### AWS SAA-03
## Working with AWS S3 using shellscripting

# Create bucket
```sh
#!/bin/bash

if [ $# -eq 0 ]; then
  echo "No input values"
  exit 1
fi

BUCKET_NAME=$1

# Specify the region for the AWS CLI command
#aws s3api create-bucket --bucket "$BUCKET_NAME" --region ca-central-1 --create-bucket-configuration LocationConstraint=ca-central-1

aws s3api list-buckets

```

# Delete bucket
```sh
#!/bin/bash
echo "Delete objects"

if [ -z "$1" ]; then
  echo "There need to be a bucket name eg.
  exit 1
fi

if [ -z "$2" ];then
  echo "There needs to be a file name
fi

BUCKET_NAME=$1

aws s3api list-objects-v2 --bucket $BUCKET_NAME --query Contents[].Key | jq -n '{Objects: [inputs | .[] | {Key: . }]}' > /tmp/delete_objects.jsob

aws s3api delete-objects --bucket $BUCKET_NAME --delete file://tmp/delete_objects.json

```

# sync the file to s3 bucket
```
#!/bin/bash
OUTPUT_DIR="/tmp/s3-bash-scripts"
mkdir -p $OUTPUT_DIR
NUM_FILES=$((RANDOM % 6 + 5))

for ((i=1; i<=$NUM_FILES; i++)); do
  FILENAME="$OUTPUT_DIR/file_$i.txt"
  dd if=/dev/urandom of="$FILENAME" bs=1024 count=$((RANDOM % 1024 + 1)) 2>/dev/null
done

tree $OUTPUT_DIR

BUCKET_NAME=$1
FILENAME=$2

aws s3 sync $OUTPUT_DIR s3://$BUCKET_NAME/files

```

# List bucket
```sh

echo "=== list buckets"

aws s3 ls
aws s3api list-buckets | jq -r '.Buckets | sort_by(.CreationDate) | reverse | .[0:5] | .[] | .Name'

echo "end---"
```

# Put bucket

```sh
#!/bin/bash

if [ -z "$1" ]; then
  echo "There need to be a bucket name eg.
  exit 1
fi

if [ -z "$2" ];then
  echo "There needs to be a file name
fi

BUCKET_NAME=$1
FILENAME=$2
OBJECT_KEY=$(basename "$FILENAME")

aws s3api put-object --bucket $BUCKET_NAME --body $FILENAME --key $OBJECT_KEY

```





























