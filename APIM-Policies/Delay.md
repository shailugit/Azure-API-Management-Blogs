Adding a delay in APIM response.
The below sample code will add 5 second delay in your APIM call 


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

##Trace to validate causing delay
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
