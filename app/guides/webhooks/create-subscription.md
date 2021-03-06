---
layout: twoColumn
section: guides
type: guide
guide:
    name: webhooks
    step: '2'
title: Create Webhook Subscription
description: Each application can have multiple subscriptions associated to it. While one webhook subscription is sufficient, you can create as many as you want for redundancy.
---

# Step 2: Create a webhook subscription

Each application can have multiple subscriptions associated to it. While one subscription is sufficient, you can create up to **ten** active webhook subscriptions in Sandbox, and up to **five** in Production at a time.

To make the following request, we need to use the `access_token` we just previously obtained.

#### Security considerations

Your webhook endpoint should only be accessible over TLS (HTTPS) and your server should have a valid certificate. Your subscription should include a random, secret key, only known by your application. This secret key should be securely stored and used later when validating the authenticity of the webhook request from Dwolla.

```raw
POST https://api-sandbox.dwolla.com/webhook-subscriptions
Accept: application/vnd.dwolla.v1.hal+json
Content-Type: application/vnd.dwolla.v1.hal+json
Authorization: Bearer 0Sn0W6kzNicvoWhDbQcVSKLRUpGjIdlPSEYyrHqrDDoRnQwE7Q
{
    "url": "http://myapplication.com/webhooks",
    "secret": "sshhhhhh"
}
```
```ruby
request_body = {
  :url => "http://myawesomeapplication.com/destination",
  :secret => "your webhook secret"
}

# Using DwollaV2 - https://github.com/Dwolla/dwolla-v2-ruby (Recommended)
subscription = app_token.post "webhook-subscriptions", request_body
subscription.response_headers[:location] # => "https://api-sandbox.dwolla.com/webhook-subscriptions/5af4c10a-f6de-4ac8-840d-42cb65454216"
```
```javascript
var requestBody = {
  url: 'http://myawesomeapplication.com/destination',
  secret: 'your webhook secret'
};

appToken
  .post('webhook-subscriptions', requestBody)
  .then(res => res.headers.get('location')); // => 'https://api-sandbox.dwolla.com/webhook-subscriptions/5af4c10a-f6de-4ac8-840d-42cb65454216'
```
```python
request_body = {
  'url': 'http://myapplication.com/webhooks',
  'secret': 'sshhhhhh'
}

# Using dwollav2 - https://github.com/Dwolla/dwolla-v2-python (Recommended)
retries = app_token.post('webhook-subscriptions', request_body)
retries.body['total'] # => 1
```
```php
<?php
$webhookApi = new DwollaSwagger\WebhooksubscriptionsApi($apiClient);

$subscription = $webhookApi->create(array (
  'url' => 'http://myapplication.com/webhooks',
  'secret' => 'sshhhhhh',
));
$subscription; # => "https://api-sandbox.dwolla.com/webhook-subscriptions/5af4c10a-f6de-4ac8-840d-42cb65454216"
?>
```

### Retrieve your newly created subscription by its resource location:

```raw
GET https://api-sandbox.dwolla.com/webhook-subscriptions/5af4c10a-f6de-4ac8-840d-42cb65454216
Accept: application/vnd.dwolla.v1.hal+json
Authorization: Bearer pBA9fVDBEyYZCEsLf/wKehyh1RTpzjUj5KzIRfDi0wKTii7DqY

...
{
    "_links": {
        "self": {
            "href": "https://api-sandbox.dwolla.com/webhook-subscriptions/ecbc139b-4ae5-41a9-95bc-e7378a41b582",
            "type": "application/vnd.dwolla.v1.hal+json",
            "resource-type": "webhook-subscription"
        },
        "webhooks": {
            "href": "https://api-sandbox.dwolla.com/webhook-subscriptions/ecbc139b-4ae5-41a9-95bc-e7378a41b582/webhooks",
            "type": "application/vnd.dwolla.v1.hal+json",
            "resource-type": "webhook"
        }
    },
    "id": "ecbc139b-4ae5-41a9-95bc-e7378a41b582",
    "url": "https://myapplication.com/webhooks",
    "paused": true,
    "created": "2018-12-18T14:32:40.129Z"
}
```
```ruby
webhook_subscription_url = 'https://api-sandbox.dwolla.com/webhook-subscriptions/5af4c10a-f6de-4ac8-840d-42cb65454216'

# Using DwollaV2 - https://github.com/Dwolla/dwolla-v2-ruby (Recommended)
webhook_subscription = app_token.get webhook_subscription_url
webhook_subscription.created # => 2015-10-28T16:20:47+00:00
```
```javascript
var webhookSubscriptionUrl = 'https://api-sandbox.dwolla.com/webhook-subscriptions/5af4c10a-f6de-4ac8-840d-42cb65454216';

appToken
  .get(webhookSubscriptionUrl)
  .then(res => res.body.created); // => '2016-04-20T15:49:50.340Z'
```
```python
webhook_subscription_url = 'https://api-sandbox.dwolla.com/webhook-subscriptions/5af4c10a-f6de-4ac8-840d-42cb65454216'

# Using dwollav2 - https://github.com/Dwolla/dwolla-v2-python (Recommended)
webhook_subscription = app_token.get(webhook_subscription_url)
webhook_subscription.body['created'] # => '2015-10-28T16:20:47+00:00'
```
```php
<?php
$webhookApi = new DwollaSwagger\WebhooksubscriptionsApi($apiClient);
$retrieved = $webhookApi->id('https://api-sandbox.dwolla.com/webhook-subscriptions/5af4c10a-f6de-4ac8-840d-42cb65454216');

$retrieved->created; # => 2015-10-28T16:20:47+00:00
?>
```

<nav class="pager-nav">
    <a href="obtain-authorization.html">Back: Obtain authorization</a>
    <a href="validating-webhooks.html">Next: Validating webhooks</a>
</nav>
