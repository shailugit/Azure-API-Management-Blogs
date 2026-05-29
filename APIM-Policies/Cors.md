You can configure APIM cors policy to allow cross-domain calls from browser-based clients.
It works with all APIM tiers. It is evaluated on the OPTIONS request during preflight.

Check here for common misconfiguration
https://learn.microsoft.com/en-us/azure/api-management/cors-policy#common-configuration-issues

'''
<inbound>
        <cors allow-credentials="true">
            <allowed-origins>
                <origin>https://www.example.com</origin>
                <origin>https://app.example.com</origin>
            </allowed-origins>
            <allowed-methods preflight-result-max-age="300">
                <method>GET</method>
                <method>POST</method>
                <method>PUT</method>
                <method>DELETE</method>
                <method>OPTIONS</method>
            </allowed-methods>
            <allowed-headers>
                <header>Authorization</header>
                <header>Content-Type</header>
                <header>X-Custom-Header</header>
            </allowed-headers>
            <expose-headers>
                <header>Content-Range</header>
            </expose-headers>
        </cors>
'''
        
        <base />
    </inbound>
