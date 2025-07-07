# Subscriptions

(For concrete examples, check out our [bruno collection](https://github.com/Bane-NOR/platform-docs/tree/main/bruno/Event-Issuer-Examples))

Subscriptions are the main mechanism for getting real-time events from the Bane NOR event backbone. The subscription is a reference to an application that wants events to be sent to a webhook endpoint. The subscriber can configure the authentication towards the endpoint in addition to an API key if that is needed.

## Webhook endpoint

The endpoint can receive the event payload with additional metadata by using the [CloudEvents](../Getting-Started/cloudevents.md) HTTP binding.

Cloud events are sent by using the [HTTP Protocol Binding](https://github.com/cloudevents/spec/blob/v1.0.2/cloudevents/bindings/http-protocol-binding.md). This means that the CloudEvents are part of the HTTP headers.

!!! info
    Bane NOR is working on standardizing event messages around the cloud event specification which means that some event types might be missing from the cloud event headers.

### What does this look like

The CloudEvent is sent in [binary content mode](https://github.com/cloudevents/spec/blob/main/cloudevents/bindings/http-protocol-binding.md#31-binary-content-mode:~:text=3.1.-,Binary%20Content%20Mode,-The%20binary%20content). This means that the contents of the payload will be
cloudevent.data while the CloudEvent attributes are passed as HTTP headers. Here is an example
message:

```http
POST /event-endpoint HTTP/1.1
Host: example.com
Content-Type: application/json
Ce-Specversion: 1.0
Ce-Type: com.example.object.created
Ce-Source: /mycontext
Ce-Id: A234-1234-1234
Ce-Time: 2025-06-12T12:00:00Z
// Payload will be dependant on contents of the original message.
{
  "objectId": "abc-123",
  "status": "created"
}

```

## Create Subscription

The following command can be used to create a subscription that uses an API key and basic authentication.

```curl
curl -H "Ocp-Apim-Subscription-Key: ApiKey" https://<bane-nor-api-endpoint>/event-issuer/v1alpha/{tenantId}/subscriptions \ 
-d '{"applicationId": "my-application", "event": "event-name", "URL": "https://my-endpoint.com/events}, \ 
"apiKey": { "header": "Ocp-Apim-Subscription-Key", "key": "API-key" }, \ 
"authentication": { "username": "user1", "password": "my-secure-password" }'
```

If only an API key is needed don't set the `authentication` and and only the `apiKey` section.
