---
title: Subscribe
sidebar_label: Subscribe
sidebar_position: 1
---

# Subscribe API

Author: `Shaurya Sharma`

**Endpoint**: `/subscribe`  
**Method**: **POST**

Creates or updates a subscription for a user in the database.

- If a subscriber with the given `email` does not exist, the service automatically creates a new subscriber document.
- If the topic is already subscribed to but new `option_ids` are provided, they are merged (unless some conflict arises).

## Request Body
```json
{
  "email": "user@example.org",
  "name": "John Doe",
  "organization": "Any Organization",
  "topic_id": "64a888c072f0ab23456789cd",
  "option_ids": ["64a888c000000000000001ab", "64a888c000000000000001ac"]
}
```
- `topic_id`: The ID of the topic to which the user wants to subscribe.
- `option_ids`: An array of report IDs, required only if the topic has `options_available: true`.

## Sample Response
```json
{
  "message": "Successfully subscribed"
}
```

## Error Cases
- **400** if `topic_id` is invalid or doesn’t exist.
- **400** if the topic requires `option_ids` but none (or empty array) is provided.
- **400** if already subscribed to some of the provided option IDs.