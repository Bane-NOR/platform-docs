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

The following guide will show you how you can start uploading or downloading files from storage.

## Dependencies

The blob store (storage container) is accessible from API Management which is just a proxy in front of a Storage Account.
To be able to upload and download files the service needs an Entra identity with correct access to the blob store.
This will be granted to external parties through [Skyporten](/integration/Payload-Manager/Getting-Started/Skyporten)

Since it is just a proxy for the Microsoft API the Nuget packages created by Microsoft can be used as is.
An example can be found here: [Microsoft blob storage Nuget Package](https://learn.microsoft.com/en-us/azure/storage/blobs/storage-quickstart-blobs-dotnet?tabs=visual-studio%2Cmanaged-identity%2Croles-azure-portal%2Csign-in-azure-cli%2Cidentity-visual-studio&pivots=blob-storage-quickstart-scratch)

### Nuget

To be able to use the Payload Manager, the following Nuget packages are needed:

Global Nuget Packages:

- Azure.Identity
- Azure.Storage.Blobs
- Microsoft.Extensions.Azure

### Confluent Access

[Event Issuer](../../Event-Issuer/) will also needs access to the `cloud.<environment>.internal.payload-manager.<container-name>.claim-check.blob.v1`
topic and the schemas registered to the topic. These topics are dependent on schemas and as such
consumers need to use the schema serializer to be able to deserialize messages read from this topic.

## Easy ASP.NET program.cs

The example below showcases a minimal C# program for uploading and downloading a file using the payload manager storage account API.
More in-depth examples can be seen in Microsoft documentation linked to at the top of this article.

```csharp

using Azure.Identity;
using Azure.Storage.Blobs;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Azure;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddAzureClients(azClientBuilder =>
{
    azClientBuilder.AddBlobServiceClient(new Uri("https://dev.api.apps.banenor.no/payload-manager/v1/"));
    azClientBuilder.UseCredential(new DefaultAzureCredential());
});

var app = builder.Build();

app.MapGet("/", ([FromServices] BlobServiceClient blobServiceClient) =>
{
    var blobContainer = blobServiceClient.GetBlobContainerClient("integration-dev");
    
    using var stream = File.OpenRead(@"appsettings.json");
    
    var blobClient = 
        blobContainer.GetBlobClient("v1/integration-dev/appsettings.json");  

    # Microsoft SDK is not setting the correct URL for the blob container so we 
    # need to add the "v1/<container-name>/<file-path-and-file>" 
    # as part of the blob name.

    blobClient.Upload(stream);

    using var stream2 = File.OpenWrite(@"appsettings2.json");
    var file = blobClient.DownloadTo(stream2);
    return blobContainer.GetBlobs();
});

app.Run();

```

## Upload with Skyporten

```csharp
using Azure.Identity;  
using Azure.Storage.Blobs;  
using dotnet_payload_manager_skyporten_poc;  
  
var maskinPortenOptions = new MaskinportenOptions()  
{  
    ClientId = "182410a3-0c27-4193-a89b-551be7e38389",   
    Audience = "https://test.sky.maskinporten.no",  
    KeyId = "712f59034deb24013f81",  
    Url = new Uri("https://test.sky.maskinporten.no/token"),  
};  
  
var httpClient = new HttpClient();  
  
var tokenGenerator = new TokenGenerator(maskinPortenOptions, httpClient);  

// Returns bearer token from MaskinPorten  
var header = await tokenGenerator.GetAuthenticationHeaderValueAsync();  
  
var tenantId = "6ee535f2-3064-4ac9-81d8-4ceb2ff790c6";  
var clientId = "be433565-d4aa-4d0b-b588-1ac539047082";  
var tokenPath = "/tmp/maskinporten-token.txt";  
await File.WriteAllTextAsync(tokenPath, header.Parameter);  
  
var workLoadIdentityOption = new WorkloadIdentityCredentialOptions()  
{  
    TenantId = tenantId,  
    ClientId = clientId,  
    TokenFilePath = tokenPath  
};  
  
var workLoadIdentity = new WorkloadIdentityCredential(workLoadIdentityOption);  


// Blob interactions  
var blobClientOptions = new BlobClientOptions();  
  
var blobServiceClient = new BlobServiceClient(  
    new Uri("https://dev.api.apps.banenor.no/payload-manager/v1"), workLoadIdentity, blobClientOptions);  

string containerName = "integration-dev";  
  
var containerClient = blobServiceClient.GetBlobContainerClient(containerName);  
  
// Create a local file in the ./data/ directory for uploading and downloading  
string localPath = "data";  
Directory.CreateDirectory(localPath);  
string fileName = "quickstart" + Guid.NewGuid().ToString() + ".txt";  
string localFilePath = Path.Combine(localPath, fileName);  
  
await File.WriteAllTextAsync(localFilePath, "Hello, World!");  
  
BlobClient blobClient = containerClient.GetBlobClient("v1/integration-dev/path/to/folder/" + fileName);  
  
await blobClient.UploadAsync(localFilePath, false);  
```
