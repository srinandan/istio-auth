# istio-auth
istio-auth is an open source project that implements the istio-auth Apigee Edge proxy for generating, refreshing and revoking access tokens.

The edgemicro-auth proxy provides four functions:
* Provides a list of all products in the org (/products)
* Provides a signed JWT if the API Key is valid (/verifyApiKey)
* Generates an access token, which is a signed JWT. Supports client_credentials grant type (/token)
* Refresh an access token (/refresh)
* Revoke a refresh token (/revoke)

## Purpose
The istio-auth proxy acts as an authorization server for Apigee APIM with istio (https://github.com/apigee/istio-mixer-adapter) 

## Support
This is an open-source project of the Apigee Corporation. It is not covered by Apigee support contracts.
However, we will support you as best we can. For help, please open an issue in this GitHub project.
You are also always welcome to submit a pull request.

### Certificate management and Setup


### Customizations

#### How do I set custom expiry?
In the flow named 'Obtain Access Token' you'll find an Assign Message Policy called 'Create OAuth Request'. Change the value here
```
<AssignVariable>
    <Name>token_expiry</Name>
    <Value>300000</Value>
</AssignVariable>
```

#### How can I get refresh tokens?
The OAuth v2 policy supports password grant. If a request is sent as below:
```
POST /token
{
  "client_id":"foo",
  "client_secret":"foo",
  "grant_type":"password",
  "username":"blah",
  "password": "blah"
}
```
If valid, the response will contain a refresh token.

#### How do I refresh an access_token?
Send a request as below:
```
POST /refresh
{
	"grant_type": "refresh_token",
	"refresh_token": "foo",
	"client_id":"blah",
        "client_secret":"blah"
}
```
If valid, the response will contain a new access_token.

#### What grant types are supported?
client_credentials, password and refresh_token
Users can extend the Apigee OAuth v2 policy to add support for the remaining grant types.

#### Support for JSON Web Keys
Microgateway stores private keys and public keys in an encrypted kvm. The proxy exposes an endpoint '/jwkPublicKeys' to return public keys as JWK.
* Support for "kid" - Key Identifiers. If the KVM includes a field called 'private_key_kid' (value can be any string), the JWT header will include the "kid"
```
{
  "alg": "RS256",
  "typ": "JWT",
  "kid": "1"
}

* The "kid" can be leveraged during validation of the JWT (not yet implemented in microgateway)
```
