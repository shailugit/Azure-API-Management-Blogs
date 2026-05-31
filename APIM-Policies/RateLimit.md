### Configuring RateLimit in Azure API Management

The rate‑limit policy in APIM controls how many requests a client can make within a specific time window. It helps you to protect backend services from overload and prevent abuse or accidental traffic spikes
You can apply throttling per subscription, per key, per IP, or globally 

APIM does have two Types of Throttling in APIM, APIM enforces this at the gateway level before the request reaches your backend.
1. rate-limit-by-key (MOST COMMON)
2. rate-limit

The first one Limits requests per client key, such as:
- Subscription key
- JWT claim
- IP address
- Header value

The second one is for global rate limit for all callers combined.

Here are the basic **RateLimit configuration** which you can setup via Azure portal.

Open your APIM instance
  - Go to Azure portal and search for “API Management services” and select your APIM instance from the list. 
Add RATELIMIT policy at the desired scope
  - To configure at API level (most common):

## Rate Limit
```
<inbound>
    <base />
    <rate-limit calls="10" renewal-period="60" />
</inbound>
```

## Rate Limit Per Subscription Key
```
<inbound>
    <base />
    <rate-limit-by-key 
        calls="100" 
        renewal-period="60" 
        counter-key="@(context.Subscription.Id)" />
</inbound>
```


Click Save to apply.

⭐ Best Practices
- This policy can never be accurate hence when you are configuring the policy make sure not too add very low calls or renewal-period
- Use rate-limit-by-key instead of global rate-limit for fairness
- Apply limits at product level for cleaner management
- Use different limits for read vs write operations
- Always test with tools like Postman or curl
- Refer offical document to further read on this policy
  -  https://learn.microsoft.com/en-us/azure/api-management/rate-limit-by-key-policy#usage-notes
