# Stage 1: Notification System Design

## 1. Overview

This system enables users to receive notifications when they are logged in. It supports real-time delivery, storage, and management of notifications.

---

## 2. Core Actions

* Send notification
* Fetch user notifications
* Mark notification as read
* Delete notification

---

## 3. REST API Design

### 3.1 Send Notification

**POST /api/notifications**

#### Request:

```json
{
  "userId": "string",
  "title": "string",
  "message": "string",
  "type": "info | warning | alert"
}
```

#### Response:

```json
{
  "success": true,
  "notificationId": "string"
}
```

---

### 3.2 Get Notifications

**GET /api/notifications/:userId**

#### Response:

```json
[
  {
    "id": "string",
    "title": "string",
    "message": "string",
    "type": "string",
    "read": false,
    "createdAt": "timestamp"
  }
]
```

---

### 3.3 Mark as Read

**PATCH /api/notifications/:id/read**

#### Response:

```json
{
  "success": true
}
```

---

### 3.4 Delete Notification

**DELETE /api/notifications/:id**

#### Response:

```json
{
  "success": true
}
```

---

## 4. Headers

All requests must include:

```json
{
  "Authorization": "Bearer <access_token>",
  "Content-Type": "application/json"
}
```

---

## 5. Data Model

```json
{
  "id": "string",
  "userId": "string",
  "title": "string",
  "message": "string",
  "type": "string",
  "read": "boolean",
  "createdAt": "date"
}
```

---

## 6. Real-Time Notification Mechanism

* Use WebSockets (e.g., Socket.IO)
* Server pushes notifications instantly to connected clients
* Fallback: polling every few seconds

---

## 7. Architecture

* Backend: Node.js (Express)
* Frontend: React
* Database: MongoDB
* Real-time layer: WebSocket

---

## 8. Flow

1. User logs in
2. Client connects via WebSocket
3. Server sends notifications in real-time
4. Notifications are stored in database
5. User can fetch or update notification status
