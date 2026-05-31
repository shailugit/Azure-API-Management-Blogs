### Configuring CORS in Azure API Management

Configuring CORS in Azure API Management is about adding the built‑in cors policy in the right scope. You can set it up at all APIs or a single API/single operation and setting the allowed origins, methods, and headers correctly. This article will explain step by step on how to setup APIM CORS policy from basics to testing and common pitfalls.

This policy let browser-based apps to call your APIs hosted behind APIM without being blocked by the browser’s same-origin policy. APIM handle preflight OPTIONS requests and add the right headers before the request hits your backend. 

Here are the basic **CORS configuration** which you can setup via Azure portal.

Open your APIM instance
  - Go to Azure portal and search for “API Management services” and select    your APIM instance from the list. 

Add CORS policy at the desired scope
  - To configure at API level (most common):

In the left menu, select APIs and choose the API you want to configure.
- In the Design or Policies view, locate Inbound processing.
  - Click + Add policy.
  - Choose “Allow cross-origin resource sharing (CORS)” from the list.     
  - You’ll see a form-based editor where you can set:
  - Allowed origins – e.g. https://myapp.com, https://app.contoso.com
  - Allowed methods – e.g. GET, POST, PUT, DELETE
  - Allowed headers – e.g. Content-Type, Authorization, custom headers
  - Expose headers – headers the browser can read from responses
  - Allow credentials – whether to allow cookies/auth headers

```
<inbound>
        <cors allow-credentials="true">
            <allowed-origins>
                <origin>https://apim-one-xxxxxxxxxxxx.azure-api.net</origin>
            </allowed-origins>
            <allowed-methods preflight-result-max-age="60">
                <method>POST</method>
                <method>PATCH</method>
            </allowed-methods>
            <allowed-headers>
                <header>*</header>
            </allowed-headers>
            <expose-headers>
                <header>*</header>
            </expose-headers>
        </cors>
    </inbound>
```

![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/p4y169gzjxcodt2bnl6n.png)


Click Save to apply.
Initally lets allow all the methods to call and to test the scenerio we will be using APIM developer portal test console to work so you can try APIs from the portal, here portal’s domain must be in your CORS policy’s allowed origins. 

In your APIM instance, under Developer portal → Portal overview, there’s an Enable CORS option that can auto-configure a CORS policy for all APIs.
Alternatively, manually add the portal domain to your global CORS policy.

Since in the above sample code "GET" method is not allowed in CORS policy , hence testing it via APIM developer portal is showing CORS validation error. 

![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/919ivxgvl8g9k6tw9o3d.png)

You can allowed origins, headers  or methods as your scenario grows.

Note: If you put CORS only at Product scope and your API uses subscription key in a header, the preflight OPTIONS request may fail because it doesn’t include the subscription key header. Prefer API/global scope or pass the key via query string in that scenario.

**Common mistakes and how to avoid them**
No CORS policy at all:
Browser shows “No Access-Control-Allow-Origin header is present” → Add a cors policy at the appropriate scope. 

Using * with credentials:  
If allow-credentials="true", you cannot use * for allowed-origins. List explicit origins instead.

Multiple conflicting CORS policies:  
Having one at global and another at API level with different settings can cause unexpected behavior. Consolidate into one clear policy. 

Forgetting custom headers:  
If your frontend sends headers like x-api-version or x-request-id, they must be listed in <allowed-headers> or the preflight will fail.

OPTIONS not defined in backend:
Without a CORS policy, APIM may return 405 Method Not Allowed for OPTIONS because no operation is defined. The cors policy handles OPTIONS at the gateway so your backend doesn’t need to. 
