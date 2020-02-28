---
layout: twoColumn
section: resources
type: integrations
title:  Dwolla + Plaid
description: Quickly verify bank account ownership with Plaid through an integration with Dwolla.
---
# Plaid Integration

## Plaid Auth for bank funding source authentication in seconds

Dwolla and Plaid are collaborating to offer [Dwolla customers](https://www.dwolla.com/platform) a solution that enables their users to quickly and securely verify bank account ownership.

Using [Plaid Link](https://plaid.com/docs/#creating-items-with-plaid-link), your users verify their accounts in seconds by simply inputting their banking credentials in Plaid’s front-end module. Plaid’s mobile-friendly module handles input validation, error handling, and multi-factor authentication, providing a seamless experience that converts more users for your business.

Your Customers will authenticate information with their financial institution through Plaid, and select the bank or credit union account they wish to use for initiating bank transfers. Behind the scenes, you’ll receive a unique token from Plaid that will be used to [create a funding source for a Customer](https://docs.dwolla.com/#create-a-funding-source-for-a-customer) via the Dwolla API. The Dwolla and Plaid partnership offers an elegantly designed and secure way for our joint Customers to verify account ownership with access tokens, removing sensitive financial information from the transaction stream.

In this article we’ll cover the steps involved with obtaining a `plaidToken`, which will be sent to the Dwolla API in exchange for a funding source URL, used to identify a bank for a Dwolla API Customer. To test the integration in the Sandbox, you’ll use your [Plaid Sandbox Credentials](https://dashboard.plaid.com/signup) along with your [Dwolla Sandbox Credentials](https://developers.dwolla.com/guides/sandbox-setup/).

### Quick overview

* Register for a Plaid account and integrate with Plaid Link
* Create a funding source for an Dwolla API Customer using a `plaidToken`
* Obtain and store a unique funding source URL that belongs to the Dwolla API Customer

<img src="/images/dwolla-plaid-flow-v2@2x.png" width="75%" height="75%">

## Using Plaid + Dwolla

### Step 1 - Set up an account and integrate with Plaid Link
To get started, verify that your Plaid account is enabled for the Dwolla integration. Your account will be automatically enabled for Plaid's Sandbox and Development environments once you’ve created an account. Once your Plaid account is setup, add Plaid Link to your site.

A high-level overview of Plaid Link can be referenced below, however reference the [Plaid documentation](https://plaid.com/docs/dwolla/) for more information on integrating with Plaid Link.

#### Plaid Link
Plaid Link is handled completely on the client-side using JavaScript. Set up Link using only a few lines of Javascript, and then specify callbacks to handle the `public_token` after the user has authenticated and created an Item.

Plaid  Link handles the entire onboarding flow securely and quickly, but does not actually retrieve data for the selected financial institution. Instead, the Link module returns a `public_token` and an `account_id` (an id representing a user selected account at their FI) via a callback.

This  public token, along with the account id must be exchanged for a Plaid `access_token` by your back-end server making a call to the Plaid API. Finally, you’ll send the `access_token` and `account_id` to the Plaid API in exchange for a Dwolla `processor_token` which will be sent to the Dwolla API in the next step.

### Step 2 - Create a funding source for a Customer using a plaidToken

This step assumes you’ve [created a Customer](https://docs.dwolla.com/#create-a-customer) for the user that you’ve authenticated or will authenticate with Plaid. Using the processor_token obtained from the previous step, you’ll pass this in as the value of the `plaidToken` request parameter; along with a funding source `name` in the request to [create a funding source for a Customer](https://docs.dwolla.com/#new-funding-source-for-a-customer).

##### Request and response (view schema in 'raw')
```raw
POST https://api-sandbox.dwolla.com/customers/AB443D36-3757-44C1-A1B4-29727FB3111C/funding-sources
Content-Type: application/vnd.dwolla.v1.hal+json
Accept: application/vnd.dwolla.v1.hal+json
Authorization: Bearer pBA9fVDBEyYZCEsLf/wKehyh1RTpzjUj5KzIRfDi0wKTii7DqY

{
  "plaidToken": "processor-sandbox-161c86dd-d470-47e9-a741-d381c2b2cb6f",
  "name": "Jane Doe’s Checking"
}

...

HTTP/1.1 201 Created
Location: https://api-sandbox.dwolla.com/funding-sources/375c6781-2a17-476c-84f7-db7d2f6ffb31
```
```ruby
customer_url = 'https://api-sandbox.dwolla.com/customers/AB443D36-3757-44C1-A1B4-29727FB3111C'
request_body = {
  plaidToken: 'processor-sandbox-161c86dd-d470-47e9-a741-d381c2b2cb6f',
  name: 'Jane Doe’s Checking'
}

# Using DwollaV2 - https://github.com/Dwolla/dwolla-v2-ruby (Recommended)
funding_source = app_token.post "#{customer_url}/funding-sources", request_body
funding_source.response_headers[:location] # => "https://api-sandbox.dwolla.com/funding-sources/375c6781-2a17-476c-84f7-db7d2f6ffb31"
```
```php
/**
 * No example for this language yet.
 **/
```
```python
customer_url = 'https://api-sandbox.dwolla.com/customers/AB443D36-3757-44C1-A1B4-29727FB3111C'
request_body = {
  'plaidToken': 'processor-sandbox-161c86dd-d470-47e9-a741-d381c2b2cb6f',
  'name': 'Jane Doe’s Checking'
}

# Using dwollav2 - https://github.com/Dwolla/dwolla-v2-python (Recommended)
customer = app_token.post('%s/funding-sources' % customer_url, request_body)
customer.headers['location'] # => 'https://api-sandbox.dwolla.com/funding-sources/375c6781-2a17-476c-84f7-db7d2f6ffb31'
```
```javascript
var customerUrl = 'https://api-sandbox.dwolla.com/customers/AB443D36-3757-44C1-A1B4-29727FB3111C';
var requestBody = {
  'plaidToken': 'processor-sandbox-161c86dd-d470-47e9-a741-d381c2b2cb6f',
  'name': 'Jane Doe’s Checking'
};

appToken
  .post(`${customerUrl}/funding-sources`, requestBody)
  .then(res => res.headers.get('location')); // => 'https://api-sandbox.dwolla.com/funding-sources/375c6781-2a17-476c-84f7-db7d2f6ffb31'

```

### Step 3 - Obtain a funding source URL that belongs to the Customer
Once you’ve received a successful response from the API, you’ll use the unique funding source URL to identify the Customer’s bank when initiating ACH transfers.

* * *

#### Next steps

* [Sign up for Plaid API keys](https://dashboard.plaid.com/signup)
