---
tags:
- integration
- payload-manager
- API
- blobs
- storage
---

# Getting Started

The goal of this guide is to give a quick introduction to how one can get access to and start using the Payload Manager in Bane NOR.

[TOC]

!!! info
    The Payload Manager solution is only available to norwegian companies as per writing as the Authentication and Authorization
    mechanism relies on **Skyporten**.

## Onboarding

The first step in getting access to the payload-manager service is contacting the integration platform team. To contact us, please send us an e-mail
at: <integrasjonsteamet@banenor.no>. We aim to respond within a day of receiving an e-mail.

In the e-mail, please detail:

- Why you would like to use the solution

- Who or what systems in Bane NOR you want to interact with

- How we should contact you for further details and questions

## Payload Manager Storage Account Structure

The payload manager utilizes a storage account divided up into multiple containers.
Each container represents a domain within Bane NOR meaning that the container you should use
is based on the data that you will be producing. To decide on a container, please consult with the team you are
communicating with within Bane NOR and the integration team if a new container is required.

The storage accounts also employ hierarchical namespaces meaning that they employ ACL (Access control lists).
This means that each folder in a container has an extra layer of security requiring both RBAC and ACL to be allowed to read/write.

## Using the Payload Manager

Once it has been decided that you will start using the payload manager, and it has been decided which container you should use,
you can start looking into how to produce/consume files. For external parties such as yourself, Bane NOR has implemented the use
of [Skyporten](Skyporten.md) for Authentication and Authorization purposes. The required resources in the Bane NOR's tenant are
provided by the integration team on request, and will be handled as part of the onboarding procedure. For more information on
how Bane NOR utilizes Skyporten to allow external parties access to the storage accounts, please refer to: [Skyporten](Skyporten.md).

## Using the API

As mentioned, the Payload Manager exposes two storage accounts per environment through an API found in Bane NOR's APIM instance.
The API uses Microsoft's own blob storage API meaning that all API methods that work directly against a regular storage account,
will also work with the API we present, given that you have the correct RBAC and ACL access.

To check out the API, please go to our page here:
- [Payload Manager API](../apis/v1/)

The API can also be found in Bane NOR's development portals given you have the correct access, found on these two URL's:

- Staging: <https://test.api-portal.apps.banenor.no/>

- Production: <https://api-portal.banenor.no/>

### Reaching the storage account

As mentioned, the API provided by Bane NOR exposes multiple storage accounts through the same api (two accounts per environment). For the API
to be able to select the correct backend storage account, it uses an APIM policy which both validates the incoming JWT token and checks specifically the audience header
in the JWT token to determine which backend to use.

This Audience header **MUST** be scoped to the external storage account you are trying to reach. The accounts we expose are:

| Environment         | Name                  |
| --------------------| :-------------------- |
| Development         | bnplmextnwestdev      |
| Staging             | bnplmextnwesttest     |
| Production          | bnplmextnwestprod     |

This means that the audience **MUST** contain:

https://&lt;storage-account-name&gt;.blob.core.windows.net

For concrete examples on how to achieve this, please to go our user guide: [Uploading files with skyporten](../User-Guides/Uploading-files-with-skyporten.md)

## Other relevant info

### Consuming Payload Manager Events

The Payload Manager presents a claim-check solution which produces messages to kafka. In the case that you have to consume information, the event-issuer can be utilized.

**Event Issuer:** Use the [Event Issuer](../../Event-Issuer/) API to listen to the payload-manager topic.
