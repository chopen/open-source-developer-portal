---
layout: twoColumn
section: resources
type: integrations
title:  Dwolla + Sift
description: As part of Dwolla’s API, you can choose to implement Sift’s machine learning model for real-time ACH fraud monitoring.
---
# Sift Integration

As part of Dwolla’s [API](https://www.dwolla.com/platform), you can choose to implement Sift’s machine learning model for [real-time ACH fraud monitoring](https://www.dwolla.com/ach/automated-ach-fraud-monitoring).

The need to leverage machine learning in protecting against fraud becomes increasingly valuable as payment volume scales. With this in mind, many Dwolla customers look for the right solution to help manage risk for their ACH payments.

Often, we see customers choosing to use [Sift](https://sift.com/) in order to better monitor and protect against fraud. We understand the value Sift’s real-time risk scoring model can provide our customers, so we’ve integrated the service right into the Dwolla API.

### Integration overview

* [Register](https://sift.com/contact-us) for a Sift Account
* Visit `Integrations` under your account settings, or visit [https://dashboard.dwolla.com/account/integrations](https://dashboard.dwolla.com/account/integrations)
* Toggle to turn Sift on, and enter your Sift Key

**It is important to note** that your integration with Sift will involve the placement of [Sift JavaScript](https://sift.com/developers/docs/curl/javascript-api) within your application. Dwolla is unable to perform this code placement on your behalf, as the Sift JavaScript implementation occurs within your application.

As outlined in the bullet points above, first you’ll insert your Sift JavaScript within your application. Subsequently, you will copy and paste your Sift API key within the Integrations section under `Account` in the Dwolla API Dashboard. **Then you are all set!**

The events below are sent to Sift on your behalf by Dwolla’s API, and there is no additional development needed on your side. Events will begin populating into Sift once your integration is complete.  

| Dwolla Event | Sift Event |
|--------------|------------|
| [Customer Created](https://docs.dwolla.com/#create-a-customer) | [$create_account](https://sift.com/developers/docs/curl/events-api/reserved-events/content-status) |
| [Customer Updated](https://docs.dwolla.com/#update-a-customer) | [$update_account](https://sift.com/developers/docs/curl/events-api/reserved-events/update-account) |
| [Customer Suspended](https://docs.dwolla.com/#update-a-customer) | [Label User](https://sift.com/developers/docs/curl/labels-api/label-user) with $abuse_type = 'account\_abuse' |
| [Customer Funding Source Added](https://docs.dwolla.com/#create-a-funding-source-for-a-customer) | [$update_account](https://sift.com/developers/docs/curl/events-api/reserved-events/update-account) |
| [Customer Funding Source Removed](https://docs.dwolla.com/#remove-a-funding-source) | [$update_account](https://sift.com/developers/docs/curl/events-api/reserved-events/update-account) |
| [Transfers](https://docs.dwolla.com/#transfers) | [$transaction](https://sift.com/developers/docs/curl/events-api/reserved-events/transaction) with $transaction_type = '$transfer' |
| [Transfers In](https://docs.dwolla.com/#initiate-a-transfer) | [$transaction](https://sift.com/developers/docs/curl/events-api/reserved-events/transaction) with $transaction_type = '$deposit' |
| [Transfers Out](https://docs.dwolla.com/#initiate-a-transfer) | [$transaction](https://sift.com/developers/docs/curl/events-api/reserved-events/transaction) with $transaction_type = '$withdrawal' |

If you would like to go live with your Dwolla API integration, please [reach out to our sales team](https://www.dwolla.com/get-started/). If you already have an Dwolla API Integration and would like to get started with Sift, [log in and connect your Sift account](https://dashboard.dwolla.com/account/integrations) now.
