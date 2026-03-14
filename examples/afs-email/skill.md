---
# AFS Skill Definition — afs-email
# TODO: Replace all placeholder values with real data before publishing.
name: afs-email
version: "0.1.0"
description: "Send, receive, and manage email messages on behalf of an agent."
sideEffects: true
permissions:
  - email:read
  - email:send
capabilities:
  - sendEmail
  - readEmail
  - listInbox
inputSchema:
  sendEmail:
    type: object
    required:
      - to
      - subject
      - body
    properties:
      to:
        type: string
        format: email
        description: "Recipient email address."
      cc:
        type: array
        items:
          type: string
          format: email
        description: "Optional list of CC email addresses."
      subject:
        type: string
        description: "Email subject line (max 998 characters)."
      body:
        type: string
        description: "Plain-text body of the email."
outputSchema:
  sendEmail:
    type: object
    properties:
      messageId:
        type: string
        description: "Unique identifier assigned to the sent message."
      status:
        type: string
        enum:
          - sent
          - queued
          - failed
        description: "Delivery status returned by the mail server."
auth:
  type: oauth2
  scopes:
    - email:read
    - email:send
  # TODO: Replace with real OAuth 2.0 authorization and token endpoint URLs.
  authorizationUrl: "https://example.com/oauth/authorize"
  tokenUrl: "https://example.com/oauth/token"
---

# AFS Email — Example Skill

<!-- TODO: Update this example once the canonical AFS skill schema is finalised in spec/overview.md. -->

This file is a minimal worked example of an AFS-compliant skill definition for a hypothetical email service. It demonstrates the recommended structure for the YAML frontmatter and the Markdown documentation body.

## Overview

The **afs-email** skill allows an AI agent to send and receive email messages. It declares its required permissions (`email:read`, `email:send`) and marks itself as having side effects, so consuming agents know that invoking `sendEmail` will cause real-world changes.

## Capabilities

| Capability   | Description |
|-------------|-------------|
| `sendEmail`  | Compose and send an email to one or more recipients. |
| `readEmail`  | Retrieve the full content of a specific email by message ID. |
| `listInbox`  | Return a paginated list of messages in the agent's inbox. |

## Usage Example

<!-- TODO: Replace with a real request/response pair once the runtime format is defined. -->

**Request — send an email:**

```json
{
  "capability": "sendEmail",
  "input": {
    "to": "alice@example.com",
    "subject": "Hello from an AFS agent",
    "body": "This message was sent by an AI agent using the afs-email skill."
  }
}
```

**Response:**

```json
{
  "messageId": "msg_01HZ123ABC",
  "status": "sent"
}
```

## Notes

<!-- TODO: Add error codes, rate-limit details, and edge-case behaviour here. -->

- The `cc` field is optional and accepts a list of email addresses. Omit it if no carbon-copy recipients are needed.
- The `body` field accepts plain text only in this version. Rich-text (HTML) support is planned for v0.2.
- If the mail server is unavailable, the response will return `"status": "queued"` and the message will be retried automatically.