
# A1Base API Interaction with `a1base-node`

This document provides a comprehensive guide on how to interact with the A1Base API using the `a1base-node` library. It covers sending messages, emails, and other types of communication, explaining the available functions and their effective usage with code snippets and examples.

## Target Audience

*   Developers integrating with the A1Base agent
*   Contributors to the project

## A1Base API Integration

The `a1base-node` library simplifies interaction with the A1Base API. It provides functions for sending messages via various channels (WhatsApp, Telegram, SMS, iMessage), sending emails, and retrieving message details.

**Authentication:**

All API requests require authentication using the `x-api-key` and `x-api-secret` headers.  These should be obtained from your A1Base account.  The `accountId` is also required in the path for most endpoints.

**Base URL:**

While not explicitly defined in the OpenAPI spec, you'll need the base URL of the A1Base API to make requests.  For example: `https://api.a1base.com` (This is an example and may not be the actual URL).

### Sending Messages

The `a1base-node` library offers several functions for sending messages, catering to different communication channels and scenarios.

#### Sending an Individual Message

This function allows you to send a message to a single recipient via a specified service.

**API Endpoint:** `POST /v1/messages/individual/{accountId}/send`

**Parameters:**

*   `accountId` (path): Your A1Base account ID.
*   `x-api-key` (header): Your A1Base API key.
*   `x-api-secret` (header): Your A1Base API secret.
*   `content` (body): Message body text (string).
*   `attachment_uri` (body, optional): URI of an attachment (string).
*   `from` (body): Sender phone number (string).
*   `to` (body): Recipient phone number (string).
*   `service` (body): Communication service (e.g., "whatsapp", "telegram") (string).

**Example (Conceptual - using a hypothetical `a1base-node` function):**

```javascript
// Assuming a1base-node is installed: npm install a1base-node
const a1base = require('a1base-node');

async function sendMessage(accountId, apiKey, apiSecret, fromNumber, toNumber, messageBody, serviceType, attachmentUri = null) {
  try {
    const response = await a1base.sendMessageIndividual({
      accountId: accountId,
      apiKey: apiKey,
      apiSecret: apiSecret,
      from: fromNumber,
      to: toNumber,
      content: messageBody,
      service: serviceType,
      attachment_uri: attachmentUri
    });

    console.log("Message sent successfully:", response);
    return response;
  } catch (error) {
    console.error("Error sending message:", error);
    throw error; // Re-throw the error for handling elsewhere
  }
}

// Example usage:
const accountId = "your_account_id";
const apiKey = "your_api_key";
const apiSecret = "your_api_secret";
const fromNumber = "61421868490";
const toNumber = "61433174782";
const messageBody = "Hello from A1Base!";
const serviceType = "whatsapp"; // or "telegram", "sms", etc.

sendMessage(accountId, apiKey, apiSecret, fromNumber, toNumber, messageBody, serviceType)
  .then(result => {
    console.log("Result:", result);
  })
  .catch(err => {
    console.error("Final error handler:", err);
  });
```

**Expected Response:**

```json
{
  "to": "61433174782",
  "from": "61421868490",
  "body": "Hello from A1Base!",
  "status": "queued"
}
```

#### Sending a Group Message

This function sends a message to a group or thread.

**API Endpoint:** `POST /v1/messages/group/{accountId}/send`

**Parameters:**

*   `accountId` (path): Your A1Base account ID.
*   `x-api-key` (header): Your A1Base API key.
*   `x-api-secret` (header): Your A1Base API secret.
*   `content` (body): Message body text (string).
*   `attachment_uri` (body, optional): URI of an attachment (string).
*   `from` (body): Sender phone number (string).
*   `service` (body): Communication service (e.g., "whatsapp", "telegram") (string).

**Example (Conceptual - using a hypothetical `a1base-node` function):**

```javascript
const a1base = require('a1base-node');

async function sendGroupMessage(accountId, apiKey, apiSecret, fromNumber, messageBody, serviceType, threadId, attachmentUri = null) {
  try {
    const response = await a1base.sendGroupMessage({
      accountId: accountId,
      apiKey: apiKey,
      apiSecret: apiSecret,
      from: fromNumber,
      content: messageBody,
      service: serviceType,
      thread_id: threadId,
      attachment_uri: attachmentUri
    });

    console.log("Group message sent successfully:", response);
    return response;
  } catch (error) {
    console.error("Error sending group message:", error);
    throw error;
  }
}

// Example usage:
const accountId = "your_account_id";
const apiKey = "your_api_key";
const apiSecret = "your_api_secret";
const fromNumber = "61421868490";
const messageBody = "Hello everyone in the group!";
const serviceType = "whatsapp";
const threadId = "your_thread_id"; // Replace with the actual thread ID

sendGroupMessage(accountId, apiKey, apiSecret, fromNumber, messageBody, serviceType, threadId)
  .then(result => {
    console.log("Result:", result);
  })
  .catch(err => {
    console.error("Final error handler:", err);
  });
```

**Expected Response:**

```json
{
  "thread_id": "your_thread_id",
  "body": "Hello everyone in the group!",
  "status": "queued"
}
```

#### Sending a Message (Generic)

This endpoint seems to be a more generic way to send messages, allowing for a content object.

**API Endpoint:** `POST /v1/messages/send/{accountId}`

**Parameters:**

*   `accountId` (path): Your A1Base account ID.
*   `x-api-key` (header): Your A1Base API key.
*   `x-api-secret` (header): Your A1Base API secret.
*   `content` (body):  A JSON object containing:
    *   `message`: Message body text (string).
    *   `type`: Content type (e.g., "text", "image") (string).
*   `from` (body): Sender phone number (string).
*   `to` (body): Recipient phone number (string).
*   `service` (body): Communication service (e.g., "whatsapp", "sms", "telegram", "imessage") (string).

**Example (Conceptual - using a hypothetical `a1base-node` function):**

```javascript
const a1base = require('a1base-node');

async function sendMessageGeneric(accountId, apiKey, apiSecret, fromNumber, toNumber, messageBody, contentType, serviceType) {
  try {
    const response = await a1base.sendMessage({
      accountId: accountId,
      apiKey: apiKey,
      apiSecret: apiSecret,
      from: fromNumber,
      to: toNumber,
      content: {
        message: messageBody,
        type: contentType
      },
      service: serviceType
    });

    console.log("Message sent successfully:", response);
    return response;
  } catch (error) {
    console.error("Error sending message:", error);
    throw error;
  }
}

// Example usage:
const accountId = "your_account_id";
const apiKey = "your_api_key";
const apiSecret = "your_api_secret";
const fromNumber = "61421868490";
const toNumber = "61433174782";
const messageBody = "Hello from A1Base!";
const contentType = "text";
const serviceType = "whatsapp";

sendMessageGeneric(accountId, apiKey, apiSecret, fromNumber, toNumber, messageBody, contentType, serviceType)
  .then(result => {
    console.log("Result:", result);
  })
  .catch(err => {
    console.error("Final error handler:", err);
  });
```

**Expected Response:**

```json
{
  "to": "61433174782",
  "from": "61421868490",
  "body": "Hello from A1Base!",
  "status": "queued"
}
```

### Retrieving Message Details

#### Get Individual Message Details

**API Endpoint:** `GET /v1/messages/individual/{accountId}/get-details/{messageId}`

**Parameters:**

*   `accountId` (path): Your A1Base account ID.
*   `messageId` (path): The ID of the message to retrieve.
*   `x-api-key` (header): Your A1Base API key.
*   `x-api-secret` (header): Your A1Base API secret.

**Example (Conceptual - using a hypothetical `a1base-node` function):**

```javascript
const a1base = require('a1base-node');

async function getMessageDetails(accountId, apiKey, apiSecret, messageId) {
  try {
    const response = await a1base.getMessageDetails({
      accountId: accountId,
      apiKey: apiKey,
      apiSecret: apiSecret,
      messageId: messageId
    });

    console.log("Message details:", response);
    return response;
  } catch (error) {
    console.error("Error getting message details:", error);
    throw error;
  }
}

// Example usage:
const accountId = "your_account_id";
const apiKey = "your_api_key";
const apiSecret = "your_api_secret";
const messageId = "your_message_id"; // Replace with the actual message ID

getMessageDetails(accountId, apiKey, apiSecret, messageId)
  .then(result => {
    console.log("Result:", result);
  })
  .catch(err => {
    console.error("Final error handler:", err);
  });
```

#### Get Recent Messages in a Thread

**API Endpoint:** `GET /v1/messages/threads/{accountId}/get-recent/{threadId}`

**Parameters:**

*   `accountId` (path): Your A1Base account ID.
*   `threadId` (path): The ID of the thread.
*   `x-api-key` (header): Your A1Base API key.
*   `x-api-secret` (header): Your A1Base API secret.

#### Get Chat Group Details

**API Endpoint:** `GET /v1/messages/threads/{accountId}/get-details/{threadId}`

**Parameters:**

*   `accountId` (path): Your A1Base account ID.
*   `threadId` (path): The ID of the thread.
*   `x-api-key` (header): Your A1Base API key.
*   `x-api-secret` (header): Your A1Base API secret.

#### Get All Threads

**API Endpoint:** `GET /v1/messages/threads/{accountId}/get-all`

**Parameters:**

*   `accountId` (path): Your A1Base account ID.
*   `x-api-key` (header): Your A1Base API key.
*   `x-api-secret` (header): Your A1Base API secret.

#### Get All Threads for a Phone Number

**API Endpoint:** `GET /v1/messages/threads/{accountId}/get-all/{phone_number}`

**Parameters:**

*   `accountId` (path): Your A1Base account ID.
*   `phone_number` (path): The phone number to search for.
*   `x-api-key` (header): Your A1Base API key.
*   `x-api-secret` (header): Your A1Base API secret.

### Sending Emails

The `a1base-node` library also provides functionality for sending emails.

#### Creating an Email (Potentially for Templates)

**API Endpoint:** `POST /v1/emails/{accountId}/create-email`

This endpoint's purpose is unclear from the OpenAPI spec. It might be for creating email templates or configuring email settings.  More information is needed to provide a useful example.

**Parameters:**

*   `accountId` (path): Your A1Base account ID.
*   `x-api-key` (header): Your A1Base API key.
*   `x-api-secret` (header): Your A1Base API secret.

#### Sending an Email

**API Endpoint:** `POST /v1/emails/{accountId}/send`

**Parameters:**

*   `accountId` (path): Your A1Base account ID.
*   `x-api-key` (header): Your A1Base API key.
*   `x-api-secret` (header): Your A1Base API secret.
*   `sender_address` (body): Sender email address (string).
*   `recipient_address` (body): Recipient email address (string).
*   `subject` (body): Email subject (string).
*   `body` (body): Email body (string).
*   `headers` (body, optional):  A JSON object containing email headers like `bcc`, `cc`, and `reply-to`.
*   `attachment_uri` (body, optional): URI of an attachment (string).

**Example (Conceptual - using a hypothetical `a1base-node` function):**

```javascript
const a1base = require('a1base-node');

async function sendEmail(accountId, apiKey, apiSecret, senderAddress, recipientAddress, subject, body, headers = {}, attachmentUri = null) {
  try {
    const response = await a1base.sendEmail({
      accountId: accountId,
      apiKey: apiKey,
      apiSecret: apiSecret,
      sender_address: senderAddress,
      recipient_address: recipientAddress,
      subject: subject,
      body: body,
      headers: headers,
      attachment_uri: attachmentUri
    });

    console.log("Email sent successfully:", response);
    return response;
  } catch (error) {
    console.error("Error sending email:", error);
    throw error;
  }
}

// Example usage:
const accountId = "your_account_id";
const apiKey = "your_api_key";
const apiSecret = "your_api_secret";
const senderAddress = "jane@a101.bot";
const recipientAddress = "john@a101.bot";
const subject = "Hello from Jane";
const body = "Have a nice day!";
const headers = {
  bcc: ["jim@a101.bot", "james@a101.bot"],
  cc: ["sarah@a101.bot", "janette@a101.bot"],
  "reply-to": "jane@a101.bot"
};
const attachmentUri = "https://a101.bot/attachment.pdf";

sendEmail(accountId, apiKey, apiSecret, senderAddress, recipientAddress, subject, body, headers, attachmentUri)
  .then(result => {
    console.log("Result:", result);
  })
  .catch(err => {
    console.error("Final error handler:", err);
  });
```

**Expected Response:**

```json
{
  "to": "john@a101.bot",
  "from": "jane@email",
  "subject": "Hello from Jane",
  "body": "have a nice day",
  "status": "queued"
}
```

### Webhooks

The A1Base API uses webhooks to notify your application about incoming messages and email, as well as message delivery statuses.

#### Incoming WhatsApp Messages

**API Endpoint:** `POST /v1/wa/whatsapp/incoming`

This endpoint receives incoming WhatsApp messages.

**Request Body:**

```json
{
  "external_thread_id": "3456098@s.whatsapp",
  "external_message_id": "2asd5678cfvgh123",
  "chat_type": "group/individual/broadcast",
  "content": "content",
  "sender_name": "Bobby",
  "sender_number": "61421868490",
  "participants": ["61421868490", "61433174782"],
  "a1_account_number": "61421868490",
  "timestamp": 1734486451000,
  "secret_key": "xxx"
}
```

#### Incoming Emails

**API Endpoint:** `POST /v1/emails/mailserver/incoming`

This endpoint receives incoming emails. The format of the request body is not specified in the OpenAPI spec, but it is likely to contain the email content and metadata.

## Code Examples

The code examples provided above are conceptual and demonstrate how to use the `a1base-node` library.  You will need to install the library and replace the placeholder values with your actual credentials and data.  Remember to handle errors appropriately in your application.

**Important Considerations:**

*   **Error Handling:**  Always implement robust error handling to catch potential issues during API calls.
*   **Rate Limiting:** Be aware of any rate limits imposed by the A1Base API and implement appropriate strategies to avoid exceeding them.
*   **Security:**  Protect your API keys and secrets.  Do not hardcode them directly into your application.  Use environment variables or a secure configuration management system.
*   **Asynchronous Operations:**  The API calls are asynchronous.  Use `async/await` or Promises to handle the asynchronous operations correctly.
*   **Library Updates:** Keep the `a1base-node` library up-to-date to benefit from the latest features and bug fixes.

This documentation provides a starting point for integrating with the A1Base API using the `a1base-node` library.  Refer to the library's documentation and the A1Base API documentation for more detailed information.
