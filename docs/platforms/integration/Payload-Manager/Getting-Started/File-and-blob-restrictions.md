---
tags:
  - integration
  - payload-manager
  - API
  - blobs
  - storage
  - restrictions
  - policies
---

# File and blob restrictions

To ensure the security and quality of our file storage, we have implemented
policies on the storage account API to restrict certain files.

## File Retention

Files stored inside the payload manager storage accounts will be kept for a
standard of 8 days from creation. This is per our Kafka message retention policy
of 7 days + 1.

## File type restriction

To limit potentially dangerous files, we keep a whitelist of allowed file types
listed in the table below. If you need to upload a file type not in this list,
please contact the integration team.

| Category             | Allowed file types                    |
| -------------------- | ------------------------------------- |
| **Text files**       | .txt .csv .log .json .xml .md         |
| **Images**           | .jpg .jpeg .png .gif .bmp .tiff .webp |
| **Audio & Video**    | .mp3 .wav .mp4 .avi .mov .flac        |
| **Office Documents** | .pdf .doc .docx .xls .xlsx .ppt .pptx |
| **Archives**         | .zip .tar .gz .rar                    |
| **Code Files**       | .html .css .js .py .cs                |
| **Other**            | .vp0 .vspe .kmm .kmm2 .bmp .hdr       |

## File Size Restriction

We currently limit each request towards storage to 100 mb per request. This is a
limit set to ensure only reasonably large files are uploaded to the storage
account. When uploading larger files, they have to be uploaded in multiple
[blocks](https://learn.microsoft.com/en-us/rest/api/storageservices/put-block).

## File name restrictions

To prevent path traversal, other dangers, and programmatic errors, we have put
restrictions on names allowed to be uploaded to the storage account. This
restriction follows the regex `[a-zA-Z0-9.-]`.

### Examples

| Filename           | Allowed/Disallowed |
| ------------------ | ------------------ |
| kjørebok.xlsx      | Disallowed         |
| kjorebok.xlsx      | Allowed            |
| ola´s-reisebok.txt | Disallowed         |
| olas-reisebok.txt  | Allowed            |
