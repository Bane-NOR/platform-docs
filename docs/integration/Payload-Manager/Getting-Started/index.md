# Getting Started

---
tags:

- integration
- payload-manager
- API
- blobs
- storage

---

The goal of this guide is to give a quick introduction to how one can get access to and start using the Payload Manager in BaneNOR.

[TOC]

## Onboarding

The first step in getting access to the payload-manager service is an onboarding meeting with the integration platform.
Here the goal is to map out the thought-out use case/cases for your team and why the Payload Mangager would be a relevant solution.
To start this onboarding process, you should have talked with your BaneNOR collaborators who should take care of the onboarding.

If you are already onboarded and have been allowed to use the Payload Manager Service, the first step can be ignored.

## Domain for Storage Account Container

Our storage accounts used for the payload manager are divided into containers based on separate domains. Each domain has its own Kafka topic.

### Existing Domains

If your domain is already set up within the payload manager, we simply need to grant you the necessary permissions to start using it.

## Using the Payload Manager

Once you have access to the appropriate domain within the storage account, you can use the API to upload and download blobs.
For more information, refer to [API and Access Control](../Getting-Started/API-and-Access-Control.md).

### Accessing the Payload Manager

Our storage account utilizes Microsoft's standard API, with support from microsoft libraries,
making it easy to setup your custom applications for uploading and downloading blobs to the payload-manager storage accounts.
For external users, Skyporten has to be used in conjunction.

Listening to the payload-manager topic for your domain can be done with

1. **Event Issuer:** Use the [Event Issuer](../../Event-Issuer/User-Guides/How-to-start-using-the-event-issuer.md) API to listen to the payload-manager topic.
1. **Direct** Or listen directly to the payload-manager topic. For this we need to grant read-access to your confluent user

## Folder Access Control

We have decided to enable Folder Access Control for the payload-manager storage accounts,
which means that the owner of every container can control read / write / execute perissions for folders inside the container,
read more about how this works and how to use it [here](Folder-Access-Control.md)

If you have any questions or need further assistance, feel free to reach out!

## API Management DNS

We have different API Management instances depending on the environment you will be using.

| Environment    | DNS |
| -------- | ------- |
| Dev  | <https://dev.api.apps.banenor.no>     |
| Test/Staging | <https://test.api.apps.banenor.no>    |
| Prod    | <https://api.banenor.no>   |

  <!-- <div class="next-step-card-container">
    <div class="next-step-card">
        <h3>Next Step: Get familiar with Payload Manager</h3>
        <p><a href="/integration/Integration-platform/Payload-Manager/Getting-Started/API-and-Access-Control/">&#8594; Journey: Payload Manager security.</a></p>
        <p><a href="/integration/Integration-platform/Payload-Manager/User-Guides/How-to-Upload-and-Download-files/">&#8594; How to upload files.</a></p>
    </div>
</div> -->
