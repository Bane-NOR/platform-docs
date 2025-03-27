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

!!! warning "Under construction"

Skyporten is a service from DigDir, created to allow Norwegian companies to share data between themselves using
MaskinPorten as an external iDP token provider and exchanging said token with your own cloud environment such as
GCP, Azure, or AWS. A detailed drawing from DigDir showcasing the flow can be seen from
[DigDirs documentation](https://docs.digdir.no/docs/Maskinporten/maskinporten_skyporten).

## What does the Integration Platform provide

### API

The Integration Platform provides an API in Maskinporten and provides access to this API through an organization number.

### Managed Identity with Federated Credential

The second resouce the Integration Platform provides is a managed identity for external parties.
This managed identity will be used as your identity in our cloud IDP solution, EntraID.
