---
layout: twoColumn
title:  "Integrations | Integrations Partner | Dwolla API Documentation "
description: " Build, discover and connect with third party integration applications published to the Dwolla Integration Directory"
---
# Build an Integration
#### Extend the Dwolla Platform with a custom integration

A partner integration enables a Dwolla Client to authenticate with the Dwolla Platform and authorize a third party application to interact with data stored in Dwolla via the application's user interface. Publish your integration to Dwolla’s Integration Directory and make our clients your clients too.

Dwolla uses [OpenID Connect](https://openid.net/connect/), which is a layer built on top of the [OAuth 2.0 protocol](https://tools.ietf.org/html/rfc6749), to facilitate this authorization. Third party applications can perform actions, such as creating Customer records, viewing transaction history and more. To do so, your application will first need to request authorization. The user (Dwolla Client) is first presented with a permission dialog for the third party application, at which point the user can either approve or deny requested permissions. Once the user approves, an `authorization_code` is sent to your application, which will then be exchanged for an `access_token` and `refresh_token` pair.

The OpenID Connect flow is designed for web applications that leverage a browser-based interaction for user authentication.

### Terminology

Before learning more about the authorization process, take a moment to review some of the key terms used in this guide:

* **Integrator:** *You, the service provider/third party application creating an integration with Dwolla*
* **API Client:** *Dwolla’s Clients, applications utilizing the Dwolla platform to power payments within their application*
* **Integration Application:** *A third party application*
* **Access Token:**  *Token-based authentication to allow an application to access an API–used by Dwolla to authorize or deny requests*
* **Refresh Token:** *A token used within 60 days to generate a new access_token and refresh_token pair issued by the authorization server*
* **Sandbox:** *Dwolla’s testing environment*
* **Dashboard:** *Interface for Dwolla Clients to view application information and activity*

### Initial Setup - Configure application in Sandbox

While anyone can sign up with a sandbox account and start building with Dwolla’s API as an API Client, in order to be onboarded as a third party integrator, your application will need to be reviewed and approved by Dwolla.

After creating your Application in sandbox, you will need to contact `partnerships@dwolla.com` to finish setting up your integration. When you contact Dwolla, please provide the following pieces of information to begin the approval process:

* Subject line of: `Enable Sandbox Integrator`
* The email address you signed up with in sandbox
* The name of your Integration Application in sandbox
* The redirect URI that the authorization flow will redirect to after a Dwolla API Client grants permission to your integration. The redirect URI will be the endpoint that will handle receiving the code from Dwolla during the OpenID/OAuth flow
* A description that will be shown with your integration in the Dwolla Dashboard within the marketplace (Optional, but recommended)
* A sign-up URL on your application that Dwolla Clients will be redirected to when enabling your integration. It is recommended you have users either login or (sign up for) your integration on this page.  After you’ve authenticated the user, you will start the OpenID/OAuth flow with Dwolla (Optional, but recommended)
* An informational URL that is a page on your application where Dwolla API Clients can learn more about your integration–there is a “Learn More” link on your integration in sandbox that will link to this URL (Optional, but recommended)

After receiving confirmation from our team via email, we will finish enabling your integration application in sandbox and you can begin developing and testing your integration application.

## Authorization Workflow
There are three main steps that must happen between the Dwolla API Client and your integration.

1. [Request authorization](/integrations/authorization.html#request-authorization-redirect-user-to-dwolla)
2. [Finish authorization](/integrations/authorization.html#finish-authorization-exchange-code-for-access-token)
3. [Refresh authorization](/integrations/authorization.html#refresh-authorization)

## Request Authorization - Redirect user to Dwolla

In order to obtain authorization to the user’s data (API Client), the user is redirected to Dwolla and presented with a permissions dialog, at which point the user can either approve or reject the requested permissions. If the user approves, the user will be sent back to your app with a temporary `authorization_code` that will be exchanged for an `access_token` and `refresh_token` pair.

To start the authorization flow, construct the initiation URL that the user will visit in order to grant permission to your application. The request authorization URL describes who the Integration Application is (`client_id`) and where the user should be redirected to after they grant or deny permissions to your application (`redirect_uri`).

### Access Control
When your integration application is approved to be published to the Integration Directory, Dwolla will assign permissions to your application based on your use case, with the intent of only allowing the integration application to handle data that is applicable to the integration. These permissions ultimately define and apply access control to individual resources, information or actions available in the Dwolla API.

Permissions can be broken down into three basic categories:

* Read
* Write
* Read, Write + Transact

Read, Write and Read, Write + Transact permissions will all be available by default in the sandbox environment.

#### Request parameters

**Note: Remember to url-encode all query string parameters**

| Parameter | Description |
|-----------|----------|
| client_id | *Required* <br> Your application key that can be retrieved from the Dwolla Sandbox Dashboard. |
| redirect_uri | *Required* <br> URL where the user will be redirected to, after accepting or rejecting permissions. The value of this parameter must match the value specified in your email to Dwolla to get access. Dwolla compares: protocol, subdomain, domain, tld, and file path. Additional query string parameters are ignored. |
| response_type | *Required* <br> Value of `code`. |
| scope | *Required* <br> Permissions your application is requesting. Scopes are specified via a space-delimited list ("%20"). In Sandbox, you can just set this list to ‘send funding managecustomers transactions accountinfofull openid’ which will give your integration full access to the client’s account. **Note:** Explicit request of scopes will change. Integration applications will be subject to review of allowed permissions by the Dwolla team. |
| state | *Optional* <br> Indicates any state which may be useful to your application upon redirect. Dwolla passes this value through the OAuth flow, so your application receives the same value it sent in the initial authorization request. Possible uses include redirecting the user to the correct resource in your site, nonces and cross-site-request-forgery (XRSF) mitigations. |


###### Example Request URL
```noselect
https://accounts-sandbox.dwolla.com/auth?client_id=fKSC3bQbLexyTDSXVvTv7Df0z5ow52YM9TotbKeKrycnYnKwnX&response_type=code&redirect_uri=https%3A%2F%2Facd30eaf.ngrok.io%2Fcallback&scope=openid%20send%20funding%20managecustomers%20transactions
```

After redirecting, the user will be prompted to login using their Dwolla account credentials. After logging in, the user will be prompted to grant authorization to the integration application request using the account with which you just authenticated.

#### User experience - Testing in Sandbox

We recommend logging out of all sandbox account sessions prior to initiating the authorization experience.

Once initiated, you can mimic the steps that an API Client will go through to grant or deny authorization.

The user (API Client) will be prompted to log into the Dwolla Sandbox with their account (for sandbox testing purposes). This can be the same account that owns the Integration Application or a new sandbox account tied to a separate email address.

![Image of accounts login](/images/accounts_login.png "Auth flow accounts login")

After successfully logging in, the API Client will be prompted to grant authorization to the integration request using the account with which you just authenticated.

![Image of permissions screen](/images/permissions_screen.png "Auth flow permissions screen")

Once authorization has been granted, Dwolla will redirect the API Client to your integration application’s redirect URI with a query parameter called “code.”

![Image of auth code](/images/callback_auth_code.png "Auth flow callback")

If the Client chooses not to authorize the integration application, Dwolla will redirect the API Client to your integration application’s redirect URL with two query parameters: `error` & `error_description`. These values should be used to display a meaningful error message to the user in your application.

## Finish Authorization - Exchange Code for Access Token
Once you’ve obtained authorization from the API Client and have received a temporary `authorization_code` you will exchange this code for an `access_token` and `refresh_token` pair.

This will require an API POST to the `https://accounts-sandbox.dwolla.com/token` endpoint with an x-www-form-urlencoded form request with the following attributes:

* “Authorization” header with the client key and client secret for your integration joined by a ‘:’, then base64 encoded
    * Authorization: `Basic <Base64(client_id:client_secret)>`
* Content-Type header with value: `application/x-www-form-urlencoded`
Body form parameters (key: value)
    * code: the code received from Dwolla via a query parameter
**Note: This code has an expiry time of 60 seconds. If not exchanged within 60 seconds you’ll need to prompt the user to restart the auth flow.**
    * grant\_type: authorization\_code
    * redirect\_uri: the redirectUri specified for your integration application

##### Example Auth flow
```raw
CLIENT_ID=CLIENTID
CLIENT_SECRET=CLIENTSECRET
AUTH=$(echo -ne "$CLIENT_ID:$CLIENT_SECRET" | base64)
echo $AUTH
curl \
  --header "Content-Type: application/x-www-form-urlencoded" \
  --header "Authorization: Basic $AUTH" \
  --request POST \
  --data 'code=<AUTHCODE>&grant_type=authorization_code&redirect_uri=http%3A%2F%2Frequestb.in%2F17pkt3k1' \
  https://api-sandbox.dwolla.com/token
```
```javascript
const auth = dwolla.auth({ redirect_uri: "https://mysite.com/dwolla/callback" })
auth.url # => "https://accounts.dwolla.com/auth?response_type=code&client_id=my-client-id&redirect_uri=https%3A%2F%2Fmysite.com%2Fdwolla%2Fcallback"

auth.callback(code: "my-code").then(token => {
  token.access_token // => "new-access-token"
  token.get("customers")
})
```
```ruby
auth = $dwolla.auth(redirect_uri: "https://mysite.com/dwolla/callback")
auth.url # => "https://accounts.dwolla.com/auth?response_type=code&client_id=my-client-id&redirect_uri=https%3A%2F%2Fmysite.com%2Fdwolla%2Fcallback"

token = auth.callback(code: "my-code")
token.access_token # => "access-token"
token.get("customers")
```
```python
auth = dwolla.auth({ 'redirect_uri': 'https://mysite.com/dwolla/callback' })
auth.url # => "https://accounts.dwolla.com/auth?response_type=code&client_id=my-client-id&redirect_uri=https%3A%2F%2Fmysite.com%2Fdwolla%2Fcallback"

token = auth.callback({ 'code': 'my-code' })
token.access_token # => 'access-token'
token.get('customers')
```
```php
/**
 * No example for this language yet.
**/
```


The API POST to the token endpoint will return a JSON response that includes the fields `access_token` and `refresh_token`. The `access_token` can be used to call the API on behalf of the API Client that granted the permissions.

##### Example response
```jsonnoselect
{
  "access_token": "zNLXAACWfCOb859MBGR12x4I67tWJB7y6eUQVdQT8zsLabBk8R",
  "token_type": "Bearer",
  "refresh_token": "YY5Upft2c9Ci0OOWPijyHrOsEdnaoeZGAjAtLVwlpgb2LD4Zf8",
  "expires_in": 3600,
  "id_token": "eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiI3ZTE1ZGZjMy0wNGU2LTRlYTYtOTYwNS1hMzYzMDE1NDcxOGEiLCJhdWQiOiJmS1NDM2JRYkxleHlURFNYVnZUdjdEZjB6NW93NTJZTTlUb3RiS2VLcnljblluS3duWCIsImFtciI6WyJwdyJdLCJpc3MiOiJkd29sbGEuY29tIiwiZXhwIjoxNTU5MDkyMzUwLCJpYXQiOjE1NTkwNjM1NTB9.VzJN0VFV_SKkwZM2gM1lNJUvhtjRmUXuZLgpUaVJmIdMoV3lbbK677e-_CPXviniLEtI_pebTTyJP6Uan_IG-A"
}
```

##### Token lifetimes
Access tokens are short lived: 1 hour.
Refresh tokens are long lived: 60 days.
If your `access_token` expires, the `refresh_token` should be used to get a new `access_token`. A refresh token can be used within 60 days to generate a new `access_token` and `refresh_token` pair. As long as your authorization is refreshed at least every 60 days, your application can maintain authorization indefinitely without requiring the user to re-authorize.

## Refresh Authorization
A refresh token can be used to refresh authorization on the account. A new access token and refresh token pair will be returned in the response body upon a successful API POST request, to the token exchange endpoint.
Refresh tokens are long-lived. This token must be stored securely to keep it from being used by potential attackers. If a refresh token is leaked, it may be used to obtain new access tokens (and access protected resources) until it is either blacklisted or it expires (which may take a long time).

#### HTTP request
Production: `POST https://api.dwolla.com/token`
Sandbox: `POST https://api-sandbox.dwolla.com/token`

Including the Content-Type: application/x-www-form-urlencoded header, the request is sent to the token endpoint with the following form-encoded parameters:

| Parameter | Required | Type | Description |
|-----------|----------|------|-------------|
| client_id | yes      | string | Application key. Navigate to https://dashboard.dwolla.com/applications (production) or https://dashboard-sandbox.dwolla.com/applications (Sandbox) for your application key. |
| client_secret | yes  | string | Application secret. Navigate to https://dashboard.dwolla.com/applications (production) or https://dashboard-sandbox.dwolla.com/applications (Sandbox) for your application key. |
| grant_type | yes | string | This must be set to refresh_token. |
| refresh_token | yes | string | A valid refresh token. |

An API POST to the token endpoint for retrieving a new access token and refresh token pair will return a JSON response that includes the fields `access_token` and `refresh_token`.

##### Example request
```raw
CLIENT_ID=CLIENTID
CLIENT_SECRET=CLIENTSECRET
AUTH=$(echo -ne "$CLIENT_ID:$CLIENT_SECRET" | base64)
echo $AUTH
curl \
  --header "Content-Type: application/x-www-form-urlencoded" \
  --header "Authorization: Basic $AUTH" \
  --request POST \
  --data 'client_id=<CLIENT_ID>&client_secret=<CLIENT_SECRET>&grant_type=refresh_token&refresh_token=<REFRESH_TOKEN>' \
  https://api-sandbox.dwolla.com/token
```
```javascript
dwolla.refreshToken({ refresh_token: oldToken.refresh_token }).then(newToken => {
  newToken.access_token // => "new-access-token"
  newToken.get("customers")
})
```
```ruby
new_token = $dwolla.refresh_token(refresh_token: old_token.refresh_token)
new_token.access_token # => "new-access-token"
new_token.get("customers")
```
```python
new_token = dwolla.refresh_token({ refresh_token: old_token.refresh_token })
new_token.access_token # => 'new-access-token'
new_token.get('customers')
```
```php
/*
 * No example for this language yet.
*/
```


##### Example response
```jsonnoselect
{
  "access_token": "uBa6RPl9Btb2M41ysgRmGnGAxpBZe9vqqHUjedQRFi6hTQ9b9C",
  "token_type": "Bearer",
  "refresh_token": "YY5Upft2c9Ci0OOWPijyHrOsEdnaoeZGAjAtLVwlpgb2LD4Zf8",
  "expires_in": 3600,
  "id_token": "eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiI3ZTE1ZGZjMy0wNGU2LTRlYTYtOTYwNS1hMzYzMDE1NDcxOGEiLCJhdWQiOiJmS1NDM2JRYkxleHlURFNYVnZUdjdEZjB6NW93NTJZTTlUb3RiS2VLcnljblluS3duWCIsImFtciI6WyJwdyJdLCJpc3MiOiJkd29sbGEuY29tIiwiZXhwIjoxNTU5MDkyMzUwLCJpYXQiOjE1NTkwNjM1NTB9.VzJN0VFV_SKkwZM2gM1lNJUvhtjRmUXuZLgpUaVJmIdMoV3lbbK677e-_CPXviniLEtI_pebTTyJP6Uan_IG-A"
}
```
