---
tags:
- integration
- payload-manager
- API
- blobs
- storage
- skyporten
- access
---

# Skyporten

[TOC]

Skyporten is a service from DigDir, created to allow Norwegian companies to share data between themselves using
MaskinPorten as an external iDP token provider and exchanging said token with your own cloud environment such as
GCP, Azure, or AWS. A detailed drawing from DigDir showcasing the flow can be seen from
[DigDirs documentation](https://docs.digdir.no/docs/Maskinporten/maskinporten_skyporten).

The payload manager solution utilizes Skyporten for this purpose as an external third party token provider,
which can be exchanged for an Entra-Id token within Bane NOR's Azure Tenant, where the Entra-id token can
be used to authenticate against the required storage account.

## What does the Integration Platform provide

On request, the integration platform within Bane NOR will generate and provide access to a managed identity
with a federated credential within Bane NOR's Azure tenant for your organization. This manged identity is then used
by the integration platform to provide you with the correct RBAC and ACL permissions for your use-case.

Due to how Skyporten provides access to an entire company at the time, the managed identity you receive within the BaneNOR
tenant will also be available for your entire company, you do not get a team specific one.

The integration platform also hosts an API in both MaskinPorten environments which you will be given access to
so you can fetch an identity token which you will use in your token exchange.

### TLDR

- Managed identity with Federated credential in Bane NOR tenant
- API in maskinporten scoped to correct resource

## What must you do yourself

To be able to get the token from MaskinPorten, you have to create your own self-service integration through DigDir: <https://samarbeid.digdir.no/>
This self-service integration has to provide access to the scope which is the API we provide.
The API is named:

- **banenor:payloadmanager.storage** (In both test and prod)

We will provide your organization with access to the API as part of onboarding.

Once you have your own integration, you have to write your own logic for fetching a maskinPorten token. Some code examples from DigDir
might be of assistance: <https://docs.digdir.no/docs/Maskinporten/maskinporten_guide_apikonsument>

Lastly, you have to implement code to interact with our storage accounts. We have user examples in Dotnet to achieve this showcased in our guide: [Uploading files with skyporten](../User-Guides/Uploading-files-with-skyporten.md)

### TLDR

- Create your own self service integration
- Create your own logic for fetching maskinporten token
- Create your own logic for using the storage account (see user-guide for help)
