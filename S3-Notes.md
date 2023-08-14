
## 11 - S3

### Classes : https://aws.amazon.com/es/s3/storage-classes/
  - General Purpose
    - 99,99% Availability
    - Used for frequently accessed data
    - Low latency and high throughput
    - Use case: Big data, mobile $ gaming apps, content distribution.
  - Standard-Infrequents Access 
    - 99,9% Availability
    - Use cases: Disaster Recovery, backups
  - One Zone-Infrequents Access
    - High durability in a single AZ
    - 99,5% Availability
    - Use cases: Storing secondary backup copies of on-premise data, or data you can recreate.
  - Glacier: Low-Cost object storage meant for archiving /backup  
    - Glacier Instant Retrieval
      - Milisecond retrieval, great for accessed once a quarter
      - Minimum Storage duration of 90 days
    - Glacier Flexible Retrieval
      - Expedited (1 to 5 minutes), Standard (3 to 5 hours), bulk (5 to 12 hours)
      - Minimum storage duration of 90 days
    - Glacier Deep Archive - for long term storage
      - Standard (12 hours), Bulk (48 hours)
      - Minimum storage duration of 180 days.
  - Intelligent Tiering
    - Small monthly monitoring and auto-tiering fee
    - Moves objects automatically between Access Tiers base on usage.


## Object Encryption

- Server-Side Encryption (SSE)
    - Server-Side Encryption with Amazon S3-Managed Keys (SSE-S3) - Default
        - Type: AES-256
        - Must set header "x-amz-server-side-encription":"AES256"
    - Server-Side Encryption with KMS
        - KMS Advantages: User control + audit key usage using CloudTrail
        - Must set header "x-amz-server-side-encription":"aws:kms"
        - Limitation on upload and download KMS API. Solution: Increase Quotas
    - Server-Side Encryption with Customer-Provided Keys
        - Amz S3 does NOT store the key
        - HTTPS must be used
        - Encryption key must provided in HTTP headers, for every HTTP request made
- Client-Side Encryption
    - Customer fully manages the keys and encryption cycle
- Encryption in transit (SSL/TLS)
- Force encryption in transit with aws:SecureTransport in false in Bucket Policy
- Force encryption with Bucket Policies and s3:x-amz-server-side-encryption:"aws:kms" or "s3:x-amz-server-side-encryption-customer-algorithm":"true"

``` json
{
       "Version": "2012-10-17",
       "Id": "PutObjPolicy",
       "Statement": [
           {
                "Sid": "DenyIncorrectEncryptionHeader",
                "Effect": "Deny",
                "Principal": "*",
                "Action": "s3:PutObject",
                "Resource": "arn:aws:s3:::<bucket_name>/*",
                "Condition": {
                    "StringNotEquals": {
                          "s3:x-amz-server-side-encryption": "aws:kms"
                             }
                   }
           },
           {
                "Sid": "DenyUnEncryptedObjectUploads",
                "Effect": "Deny",
                "Principal": "*",
                "Action": "s3:PutObject",
                "Resource": "arn:aws:s3:::<bucket_name>/*",
                "Condition": {
                    "Null": {
                          "s3:x-amz-server-side-encryption": true
                            }
                    }
           }
    ]
}
```
Link: 
- https://aws.amazon.com/es/blogs/security/how-to-prevent-uploads-of-unencrypted-objects-to-amazon-s3/
- https://docs.aws.amazon.com/AmazonS3/latest/userguide/UsingServerSideEncryption.html


## CORS
- If a client makes a cross origin request on our S3 bucket, we need to enable correct CORS headers


``` json
[
    {
        "AllowedHeaders": [
            "*"
        ],
        "AllowedMethods": [
            "PUT",
            "POST",
            "DELETE"
        ],
        "AllowedOrigins": [
            "http://www.example1.com"
        ],
        "ExposeHeaders": []
    },
    {
        "AllowedHeaders": [
            "*"
        ],
        "AllowedMethods": [
            "PUT",
            "POST",
            "DELETE"
        ],
        "AllowedOrigins": [
            "http://www.example2.com"
        ],
        "ExposeHeaders": []
    },
    {
        "AllowedHeaders": [],
        "AllowedMethods": [
            "GET"
        ],
        "AllowedOrigins": [
            "*"
        ],
        "ExposeHeaders": []
    }
]
```

## MFA delete
- It will be required to 
    - Permanently delete an object version
    - Suspend version on the bucket.
- Versioning must be enabled on the bucket
- Only the bucket owner (root account) can enable/disable MFA Delete


### Generate root access keys
```
aws configure --profile root-mfa-delete-demo
```

### Enable MFA delete 
```
aws s3api put-bucket-versioning --bucket mybucketname --versioning-configuration MFADelete=Enabled,Status=Enabled --mfa "arn:aws:iam::(accountnumber):mfa/root-account-mfa-device (pass)"
```

### Disable MFA delete 
```
aws s3api put-bucket-versioning --bucket mybucketname --versioning-configuration MFADelete=Disabled,Status=Enabled --mfa "arn:aws:iam::(accountnumber):mfa/root-account-mfa-device (pass)"
```

### Confirm that MFA delete is working
```
aws s3api get-bucket-versioning --bucket mybucketname  
```
### Delete de credentials in IAM console

Link:
- https://repost.aws/knowledge-center/s3-bucket-mfa-delete

## S3 Log
- For audit purpose, you may want to log all access to S3 bucket.
- The target logging bucket must be in the same AWS Region
- **WARNING**: Do not set your logging bucket to be the monitored bucket. It will create a logging loop, and your bucket will grow emponentialy,
- Log Format: https://docs.aws.amazon.com/AmazonS3/latest/userguide/LogFormat.html


## Pre-Signed URL
- Generate pre-signed URL using the S3 console, AWS CLI or SDK
- URL Expiration
- Examples: 
    - Allow login users to download a premiun file from your S3 Bucket
    - Allow and ever-changing list of users to download files by generating URLs dynamically.
    - Allow tempararily a user to upload a file to a precise location in your S3 bucket.

## Access point
- Simplify security management for S3 buckets
- Each Access point has
    - its own DNS name (Internet origin or VPC origin)
    - an access policy (similar to bucket policy) - manage security at scale
- VPC Origin
    - We can define the access point from within the VPC. So an EC2  Instance in a VPC access it without going through the internet.
    - You must create a VPC endpoint to access the Access Point (Gateway or Interface Endpoint)
    - The VPC Endpoint Policy must allow access to the target bucket and access point

## Object Lambda
- Use AWS Lambda Functions to change the object before it is retrieved by the caller application.
- Only one S3 bucket is needed, on top of which we create **S3 access Point and S3 Object Lambda Access Points**