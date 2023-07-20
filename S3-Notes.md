
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
