---
tags:
- integration
- payload-manager
- API
- blobs
- storage
- guide
- .NET
- C#
---

# Uploading and Downloading files

The following guide will show you how you can start uploading or downloading files with the Storage Accounts exposed by Bane NOR

## Information

- The blob store (storage container) is accessible from API Management which is just a proxy in front of a Storage Account.

- To be able to upload and download files, your service needs an Entra identity with correct access to the blob store. Check out our guide on how to receive this here: [Skyporten](../Getting-Started/Skyporten.md)

- Since our API uses Microsofts own blob storage API, all methods from [Azure Blob Storage client library](https://learn.microsoft.com/en-us/azure/storage/blobs/storage-quickstart-blobs-dotnet?tabs=visual-studio%2Cmanaged-identity%2Croles-azure-portal%2Csign-in-azure-cli%2Cidentity-visual-studio&pivots=blob-storage-quickstart-scratch) will work.

### Nuget

To be able to use the code example, the following Nuget packages are needed:

Global Nuget Packages:

- Azure.Identity
- Azure.Storage.Blobs

## Easy ASP.NET program.cs

The example below showcases a C# program for uploading a file using the payload manager storage account API.
More in-depth examples can be seen in Microsoft documentation linked to at the top of this article.

## Upload with Skyporten

```csharp
using Azure.Identity;  
using Azure.Storage.Blobs;  
  
// This method uses private/public key pair, can also be done with certificates
var maskinPortenOptions = new MaskinportenOptions()  
{  
    ClientId = "id-of-your-self-service-integration",   
    Audience = "https://test.sky.maskinporten.no", // Prod: "https://sky.maskinporten.no"  
    KeyId = "id-for-your-key",  
    Url = new Uri("https://test.sky.maskinporten.no/token"),  // Prod: "https://sky.maskinporten.no/token"   
};  
  
// Our implementation for fetching token from maskinporten
var httpClient = new HttpClient();  
var tokenGenerator = new TokenGenerator(maskinPortenOptions, httpClient);  
var header = await tokenGenerator.GetAuthenticationHeaderValueAsync();  

// ManagedIdentityClientId is provided by integration team
var AzureTenantId = "6ee535f2-3064-4ac9-81d8-4ceb2ff790c6";  
var ManagedIdentityClientId = "client-id";  
var tokenPath = "/tmp/maskinporten-token.txt";  
await File.WriteAllTextAsync(tokenPath, header.Parameter); //header.parameter = token received from maskinporten

// By setting these environment variables, DefaultAzureCredential uses them automatically
// for the workloadIdentityCredential flow which handles token exchange automatically.
Environment.SetEnvironmentVariable("AZURE_CLIENT_ID", clientId);
Environment.SetEnvironmentVariable("AZURE_TENANT_ID", tenantId);
Environment.SetEnvironmentVariable("AZURE_FEDERATED_TOKEN_FILE", tokenPath);

var defaultAzureCredential = new DefaultAzureCredential();

// Use correct storage account
var blobClientOptions = new BlobClientOptions()
{
    Audience = "https://bnplmextnwesttest.blob.core.windows.net"
};

// Use correct API environment
var blobServiceClient = new BlobServiceClient(  
    new Uri("https://test.api.apps.banenor.no/payload-manager/v1"), workLoadIdentity, blobClientOptions);  

string containerName = "container-name";  
  
var containerClient = blobServiceClient.GetBlobContainerClient(containerName);  
  
// Create a local file in the ./data/ directory for uploading and downloading  
string localPath = "data";  
Directory.CreateDirectory(localPath);  
string fileName = "quickstart" + Guid.NewGuid().ToString() + ".txt";  
string localFilePath = Path.Combine(localPath, fileName);  
await File.WriteAllTextAsync(localFilePath, "Hello, World!");  

// Microsoft SDK is not able to correctly add /path/to/folder/ so you must re-specify version and container-name
BlobClient blobClient = containerClient.GetBlobClient("v1/container-name/path/to/folder/" + fileName);

  
await blobClient.UploadAsync(localFilePath, false);  
```
