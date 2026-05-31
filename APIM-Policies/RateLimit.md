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
  - Click save to update and save the policy

## Rate Limit
```
<inbound>
    <base />
    <rate-limit calls="3" renewal-period="60" />
</inbound>
```

If you call the endpoint more than 3 time within a minute and run the traces with your APIM service it wil show you the rate limit (sample)
```
rate-limit (0.834 ms)
{
    "message": "Rate limit was exceeded.",
    "retryAfter": "00:00:37.5093691",
    "rateLimit": {
        "counterKey": 3884232709115864600,
        "period": "00:01:00",
        "callLimit": 3,
        "count": 3
    }
}
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

To troubleshoot the Rate limit leverage the APIM traces and you can use a variable to store the remaining Calls.
After each policy execution, the remaining calls allowed in the time period are stored in the variable which will help to further investigate the rate limit issue.
Refer the below code which can be used to traced the remaining Calls, run the APIM traces to check the rate limit and remaining Calls

**Code**
```
<rate-limit calls="3" renewal-period="60" remaining-calls-variable-name="remainingCallsPerSubscription" />
        <trace source="MyPolicyDebug" severity="information">
            <message>@($"The value of myVariable is: {context.Variables.GetValueOrDefault("remainingCallsPerSubscription")}")</message>
        </trace>
```
**Traces results**
``` 
rate-limit (0.051 ms)
{
    "message": "RateLimit counter 3884232709115864570 is incremented"
}
trace (0.058 ms)
{
    "message": "Expression was successfully evaluated.",
    "expression": "$\"The value of myVariable is: {context.Variables.GetValueOrDefault(\"remainingCallsPerSubscription\")}\"",
    "value": "The value of myVariable is: 2"
}
```


⭐ Best Practices
- This policy can never be accurate hence when you are configuring the policy make sure not too add very low calls or renewal-period
- Use rate-limit-by-key instead of global rate-limit for fairness
- Apply limits at product level for cleaner management
- Use different limits for read vs write operations
- Always test with tools like Postman or curl
- Refer offical document to further read on this policy
  -  https://learn.microsoft.com/en-us/azure/api-management/rate-limit-by-key-policy#usage-notes
