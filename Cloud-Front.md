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
