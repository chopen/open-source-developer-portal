---
layout: twoColumn
section: guides
type: guide
guide:
    name: receive-money
    step: '4'
title:  Creating an ACH transfer
description: Create a transfer by specifying your Customer's funding source ID as the source and your funding source ID as the destination.
---

# Step 4: Initiating a Transfer

## Identify Source and Destination For Transfer

Since you are utilizing a `receive` funds flow, you will need to ensure that you know who the funds are going to.

- Source - Your Customer’s Bank Funding Source
- Destination - Your Dwolla Master Account Bank Funding Source

## Step 4A: Initiate a Transfer
To initiate a transfer, we will need to specify the source and destination funding source URLs in the _links parameter.

#### Request Parameters

| Parameter | Required | Type   | Description |
|-----------|----------|--------|----------------|
| _links    | yes      | object | A _links JSON object describing the desired source and destination of a transfer. [Reference the Source and Destination object](https://docs.dwolla.com/#source-and-destination-types) to learn more about possible values for source and destination. |
| amount    | yes      | object | An amount JSON object. [Reference the amount JSON object](https://docs.dwolla.com/#amount-json-object) to learn more. |

<ol class = "alerts">
    <li class="alert icon-alert-alert">
      Within a transfer request, Dwolla supports additional optional parameters. These can range from a <code>facilitator-fee</code> to collect a portion of the transfer amount that is sent to your Dwolla account, or <code>correlationId</code> to help correlate transfers from end-to-end. For more information on all available transfer request parameters, check out our <a href="https://docs.dwolla.com/#initiate-a-transfer">API reference documentation. </a>
    </li>
</ol>

#### Request and response (view schema in ‘raw’)

```raw
POST https://api-sandbox.dwolla.com/transfers
Accept: application/vnd.dwolla.v1.hal+json
Content-Type: application/vnd.dwolla.v1.hal+json
Authorization: Bearer pBA9fVDBEyYZCEsLf/wKehyh1RTpzjUj5KzIRfDi0wKTii7DqY
Idempotency-Key: 19051a62-3403-11e6-ac61-9e71128cae77

{
   "_links": {
       "source": {
           "href": "https://api-sandbox.dwolla.com/funding-sources/707177c3-bf15-4e7e-b37c-55c3898d9bf4"
       },
       "destination": {
           "href": "https://api-sandbox.dwolla.com/funding-sources/AB443D36-3757-44C1-A1B4-29727FB3111C"
       }
   },
   "amount": {
       "currency": "USD",
       "value": "10.00"
    },
}

...

HTTP/1.1 201 Created
Location: https://api-sandbox.dwolla.com/transfers/74c9129b-d14a-e511-80da-0aa34a9b2388
```

```php
<?php
$transfer_request = array (
  '_links' =>
  array (
    'source' =>
    array (
      'href' => 'https://api-sandbox.dwolla.com/funding-sources/b5e68264-7d4d-42a9-88d4-5616c77c6baa',
    ),
    'destination' =>
    array (
      'href' => 'https://api-sandbox.dwolla.com/funding-sources/3152c22b-3d72-442d-a83b-e575df3a043e',
    ),
  ),
  'amount' =>
  array (
    'currency' => 'USD',
    'value' => '225.00',
  )
);

$transferApi = new DwollaSwagger\TransfersApi($apiClient);
$transfer = $transferApi->create($transfer_request);

print($transfer); # => https://api-sandbox.dwolla.com/transfers/d76265cd-0951-e511-80da-0aa34a9b2388
?>
```

```ruby
transfer_request = {
  :_links => {
    :source => {
      :href => "https://api-sandbox.dwolla.com/funding-sources/b5e68264-7d4d-42a9-88d4-5616c77c6baa"
    },
    :destination => {
      :href => "https://api-sandbox.dwolla.com/funding-sources/3152c22b-3d72-442d-a83b-e575df3a043e"
    }
  },
  :amount => {
    :currency => "USD",
    :value => "225.00"
  }
}

# Using DwollaV2 - https://github.com/Dwolla/dwolla-v2-ruby (Recommended)
transfer = app_token.post "transfers", transfer_request
transfer.response_headers[:location] # => "https://api-sandbox.dwolla.com/transfers/d76265cd-0951-e511-80da-0aa34a9b2388"
```

```python
transfer_request = {
  '_links': {
    'source': {
      'href': 'https://api-sandbox.dwolla.com/funding-sources/b5e68264-7d4d-42a9-88d4-5616c77c6baa'
    },
    'destination': {
      'href': 'https://api-sandbox.dwolla.com/funding-sources/3152c22b-3d72-442d-a83b-e575df3a043e'
    }
  },
  'amount': {
    'currency': 'USD',
    'value': '225.00'
  }
}

# Using dwollav2 - https://github.com/Dwolla/dwolla-v2-python (Recommended)
transfer = app_token.post('transfers', transfer_request)
transfer.headers['location'] # => 'https://api-sandbox.dwolla.com/transfers/d76265cd-0951-e511-80da-0aa34a9b2388'
```

```javascript
var transferRequest = {
  _links: {
    source: {
      href: 'https://api-sandbox.dwolla.com/funding-sources/b5e68264-7d4d-42a9-88d4-5616c77c6baa'
    },
    destination: {
      href: 'https://api-sandbox.dwolla.com/funding-sources/3152c22b-3d72-442d-a83b-e575df3a043e'
    }
  },
  amount: {
    currency: 'USD',
    value: '225.00'
  }
};

appToken
  .post('transfers', transferRequest)
  .then(function(res) {
    res.headers.get('location'); // => 'https://api-sandbox.dwolla.com/transfers/d76265cd-0951-e511-80da-0aa34a9b2388'
  });
```

When the transfer is created, you will receive a `201` HTTP response with an empty response body. You can refer to the Location header to retrieve a link to the created Transfer resource. All bank transactions that are sourced from a bank or that are going to a bank will have an initial status of `pending`. We recommend storing the full Transfer URL for future use, as it will be needed for correlating transfer update webhooks that are triggered for the user in the Dwolla system.

## Step 4B: Handle Webhooks

A single API call to create a payment transfer can trigger several transfer-related webhook events. The number of webhooks and type of webhook events can vary depending on the Customer type(s) involved in the transfer, as well as the source and destination for the funds transfer.
For more information on which webhooks will be fired for a given section of a transfer, refer to our [Developer Resource Article](https://developers.dwolla.com/resources/webhook-events.html).

## Step 4C: Simulate Payment Processing

To simulate paymentACH processing in the Dwolla Sandbox environment, navigate to the Sandbox Dashboard. From here, you will want to click the “Process Bank Transfers” button on the top of the screen. Your Sandbox transfer will be moved out of a `pending` status and moved to a `processed` status.

<img src="/images/process-bank-transfers.png">

**Production payment processing timing**

While ACHfunds transfer processing can be simulated at any time in the sandbox, behavior will vary in production depending on what transfer clearing options you specify. Refer to our developer resource article to [learn more on transfer timing](https://developers.dwolla.com/resources/bank-transfer-workflow/processing-times.html) in production.

## Step 4D: Verify Status of Transfer

Since ACH transactions in production can take a few days to complete, webhooks are an efficient way to notify you of when a transfer is completed and `processed` to a destination funding source. However, if you want to verify the status of a transfer at any given point in time, you can make a call to the API to retrieve the transfer by its unique id.

```noselect
{
    "_links": {
        "source": {
            "href": "https://api-sandbox.dwolla.com/accounts/30a6cb55-1754-4948-b431-ebe48288ef25",
            "type": "application/vnd.dwolla.v1.hal+json",
            "resource-type": "account"
        },
        "funding-transfer": {
            "href": "https://api-sandbox.dwolla.com/transfers/6fdd095c-afd7-e811-8111-bec1f96924ed",
            "type": "application/vnd.dwolla.v1.hal+json",
            "resource-type": "transfer"
        },
        "destination-funding-source": {
            "href": "https://api-sandbox.dwolla.com/funding-sources/AB443D36-3757-44C1-A1B4-29727FB3111C",
            "type": "application/vnd.dwolla.v1.hal+json",
            "resource-type": "funding-source"
        },
        "self": {
            "href": "https://api-sandbox.dwolla.com/transfers/74c9129b-d14a-e511-80da-0aa34a9b2388",
            "type": "application/vnd.dwolla.v1.hal+json",
            "resource-type": "transfer"
        },
        "source-funding-source": {
            "href": "https://api-sandbox.dwolla.com/funding-sources/707177c3-bf15-4e7e-b37c-55c3898d9bf4",
            "type": "application/vnd.dwolla.v1.hal+json",
            "resource-type": "funding-source"
        },
        "destination": {
            "href": "https://api-sandbox.dwolla.com/customers/4e988dba-0a1e-4591-ad04-eab3613e2f83",
            "type": "application/vnd.dwolla.v1.hal+json",
            "resource-type": "customer"
        }
    },
    "id": "74c9129b-d14a-e511-80da-0aa34a9b2388",
    "status": "processed",
    "amount": {
        "value": "10.00",
        "currency": "USD"
    }
}
```

<nav class="pager-nav">
    <a href="retrieve-funding-sources.html">Back: Retrieve Funding Sources</a>
    <a href="" style="display:none;"></a>
</nav>