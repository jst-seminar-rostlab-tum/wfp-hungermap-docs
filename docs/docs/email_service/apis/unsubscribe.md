---
title: Unsubscribe
sidebar_label: Unsubscribe
sidebar_position: 2
---

# Unsubscribe API

Author: `Shaurya Sharma`

**Endpoint**: `/unsubscribe`  
**Method**: **GET**

Removes a subscriber from the database (and thus all subscriptions).

- This call removes the subscriber record entirely from `subscribers` and also purges any associated entries in `subscriptions`.

## Query Parameter
- `subscriber_id`: The ObjectId of the subscriber to remove.

### Example Request
```
GET /unsubscribe?subscriber_id=64a888c072f0ab23456789cd
```

## Response
- Renders an **HTML** confirmation page stating that the user has successfully unsubscribed.

## Error Cases
- **400** if `subscriber_id` is missing or invalid.
- **404** if the subscriber is not found in the database.