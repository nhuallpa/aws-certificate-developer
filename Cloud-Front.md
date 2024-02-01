## Cloud Front
- Content Delivery Network (CDN)
- Improves read performance, content is cache at edge
- Improves users experience
- DDoS protection

### Origins
- S3 Buckets
    - For distributing files anc caching them at the edge
    - Enhanced recurity with CloudFront Origin Access Control (OAC)
    - CloudFront can be used as an ingress (to upload files to S3)
- Custom Origin (HTTP)
    - Application Load Balancer
    - EC2 instance
    - S3 website, and so on
- CloudFront
    - Global Edge network
    - Files are cached for a TTL
    - Great for static content that must be available everywhere
- S3 Cross Region Replication
    - Must be setup for each region
    - Files are updated in near real time
    - Read only
    - Great for dynamic content that needs to be available at low-latency in few region.

### Caching and Caching Policies
- Cache key
    - A unique identifier for every object in the cache
    - By default, consists of hostname + reource portion of the URL
    - But you can add other elements (headers, cookies, query string) using **CloudFront Cache Policies**
- Cache policies
    - HTTP headers: None, Whitelist
    - Cookies: None, Whitelist, Include All-Except, ALL
    - Query Strings: None, Whitelist, Include All-Except, ALL
    - Control the TTL (0 seconds to 1 years), can be set by the origin using **Cache-Control** header, **Expires** header...
- Origin Request Policy
    - Specify values that you want to include in origin requests **whitout including them in the cache key**.

### Cache Invalidation
- Force an entire or partial cache refresh by performing a **CloudFront Invalidation**

### Cache Behaviours
- Configure different settings for a given URL path pattern.
- Route to different kind of origins/origin groups based on the content type or path pattern.

### ALB or EC2 as an origin
1. Look at Public IP of Edge Location: https://d7uri8nf7uskq.cloudfront.net/tools/list-cloudfront-ips
2. Allow those IPs in the target group of EC2 instance or the ALB. **These must be public.**

### Geo Restriction
- You can restrict who can access your distribution
    - Allowlist: Allow your users to access your content only if they're in one of the countries on a list of approved countries
    - Blocklist: Prevent your users from accessing your content if they're in one of the countries on a list of banned countries.

### Hang on: CloudFron URL signed
- Create a private key and a public key based on RSA 2048 bit 
- The public key is used by CloudFront to verify URLs
- The private key is used by your applicatioins (e.g. EC2) to sign URLs
- Load the public key in Public Key Manu.
- Then create a Key Group and choose the public key loaded.

### Advanced Concepts
- Diferent class for pricing
- Multiple origin
- Origin groups
- Field Level Encryption

### Real Time Logs
- Users -> ClouldFront -> Kinesis Data Streams -> Lambda (Real-time Processing)
- Users -> ClouldFront -> Kinesis Data Streams -> Kinesis Data Firehose (Near real-time)
