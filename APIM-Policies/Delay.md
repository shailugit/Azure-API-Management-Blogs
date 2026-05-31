### How to Add a Delay in APIM

Adding a delay in APIM response somtime required where you may be simulating slow backend for testing or staggering calls to downstream systems.
In one of the most common scenerio it helps you where you are seeing **Client connection failures** due to client timeout in APIM. Adding delay will help to visualize the **CCF** in case client is closing the connection. 
This scenario may also help in debugging the async flows. 


To setup this policy open your APIM instance
- Go to Azure Portal
- Search for API Management services
- Select your APIM instance

Choose where to apply the delay, you can apply the delay at:
- All APIs (global)
- A single API
- A single operation. Most commonly its API → Inbound processing

Open the Policy Editor
- Go to APIs
- Select your API
- Click Design
- Under Inbound processing, click </> (Policy editor).
- The below sample code will add 5 second delay in your APIM call 


```xml
 <inbound>
        <base />
        <retry condition="@(true)" count="1" interval="5" />
    </inbound>

OR 
<set-variable name="delayExpression" value="@{
                var curr = System.DateTime.UtcNow.AddMilliseconds(5000);
                while(System.DateTime.UtcNow < curr){
                    continue;
                }
                return System.DateTime.UtcNow;
            }" />
```

- Here is a trace output to validate the delay you added.
  
```
retry (0.481 ms)
{
    "message": "Expression was successfully evaluated.",
    "expression": "true",
    "value": true
}
retry (0.139 ms)
{
    "message": "Retry condition was met. Retrying in '00:00:05'"
}
retry (5,005.562 ms)
{
    "message": "Retrying...",
    "attempt": 1
}
retry (0.033 ms)
{
    "message": "Expression was successfully evaluated.",
    "expression": "true",
    "value": true
}
retry (0.012 ms)
{
    "message": "Leaving retry section. Number of retries exceeded configured value"
}

```

## Important Notes
 - Delay affects only this request, not global throughput.
 - Overusing delays can slow down your gateway.
 - Not recommended for production unless you have a specific reason.
