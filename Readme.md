# Tech-Stack

1. Take any OS in aws console [RHEL,Centos,Amezon Linux].

AWS-S3 End-to-End Using AWS CLI

Step 1: Verify AWS CLI Installation
Download AWS CLI:
```
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" \
-o "awscliv2.zip"
```
Install unzip:
```
sudo yum install unzip -y
```
Unzip:
```
unzip awscliv2.zip
```
Install:
```
sudo ./aws/install
```
Check whether AWS CLI is installed.
```
aws --version
```
Example:
```
aws-cli/2.17.x Python/3.x Linux
```
Purpose: Displays the installed AWS CLI version.

Step 2: Configure AWS Credentials
```
aws configure
```
Provide:
```
AWS Access Key ID
AWS Secret Access Key
Default Region (e.g., us-east-1)
Output Format (json)
```
Purpose: Stores AWS credentials locally so CLI can access AWS services.

Step 3: Verify AWS Credentials
```
aws sts get-caller-identity
```
Purpose: Confirms that your AWS credentials are valid and shows your AWS Account ID, User ARN, and User ID.

Create an S3 Bucket
```
aws s3 mb s3://blujaytech-devops-demo --region us-east-1
```
List All Buckets
```
aws s3 ls
```
Create an HTML File
```
vi index.html
```
# add this content in above index.html file.
```
each "This is my 1st s3-bucket creation"
```
Upload the HTML File
```
aws s3 cp index.html s3://blujaytech-devops-demo/
```
Create Bucket Policy
```
vi bucket-policy.json
```
Use the correct bucket name:
```

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicRead",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::blujaytech-devops-demo/*"
    }
  ]
}
```
Apply Bucket Policy
```
aws s3api put-bucket-policy \
  --bucket blujaytech-devops-demo \
  --policy file://bucket-policy.json
```
Purpose: Allows public read access to objects in the bucket.

Verify Bucket Policy
```
aws s3api get-bucket-policy \
  --bucket blujaytech-devops-demo
```
Purpose: Displays the currently applied bucket policy.

Modify the HTML File
```
vi index.html
```
# Edit above index.html file.
```
eacho "All The Best All Our Blujaytech Students"
```
Check the file:
```
cat index.html
```
Upload Updated File
```
aws s3 cp index.html s3://blujaytech-devops-demo/index.html
```
Purpose: Replaces the existing index.html with the updated version.

Verify Object Upload
```
aws s3api head-object \
  --bucket blujaytech-devops-demo \
  --key index.html
```
Purpose: Displays metadata such as:


Content Type

Access our s3-Bucket content.

```
https://us-east-1.console.aws.amazon.com/s3/buckets?region=us-east-1
```
-----s3 bucket delete-process----------

This helps confirm the file was updated successfully.

Check Whether Versioning is Enabled
```
aws s3api get-bucket-versioning \
  --bucket blujaytech-devops-demo
```
List All Object Versions
```
aws s3api list-object-versions \
  --bucket blujaytech-devops-demo
```
If versioning is enabled:
```
aws s3api list-object-versions \
  --bucket blujaytech-devops-demo \
| jq -r '.Versions[] | "\(.Key) \(.VersionId)"' \
| while read key version; do
    aws s3api delete-object \
      --bucket blujaytech-devops-demo \
      --key "$key" \
      --version-id "$version"
```
done

Purpose: Deletes every version of every object in the bucket.

Delete Delete Markers

```
aws s3api list-object-versions \
  --bucket blujaytech-devops-demo \
| jq -r '.DeleteMarkers[] | "\(.Key) \(.VersionId)"' \
| while read key version; do
    aws s3api delete-object \
      --bucket blujaytech-devops-demo \
      --key "$key" \
      --version-id "$version"
```

Purpose: Removes delete markers left by versioned deletions.

Delete the Bucket
```
aws s3 rb s3://blujaytech-devops-demo
```


