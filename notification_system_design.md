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


# Stage 2: Database Design & Scalability

## 1. Database Choice

We use **MongoDB (NoSQL)** for this system.

### Reason:

* Flexible schema for notifications
* Fast reads/writes
* Good for real-time applications
* Easy horizontal scaling

---

## 2. Database Schema

### Notification Collection

```json
{
  "_id": "ObjectId",
  "userId": "string",
  "title": "string",
  "message": "string",
  "type": "string",
  "read": false,
  "createdAt": "date"
}
```

---

## 3. Queries (Based on APIs)

### 🔹 1. Insert Notification

```js
db.notifications.insertOne({
  userId: "123",
  title: "New Message",
  message: "You have a new message",
  type: "info",
  read: false,
  createdAt: new Date()
});
```

---

### 🔹 2. Get Notifications

```js
db.notifications.find({ userId: "123" }).sort({ createdAt: -1 });
```

---

### 🔹 3. Mark as Read

```js
db.notifications.updateOne(
  { _id: ObjectId("abc") },
  { $set: { read: true } }
);
```

---

### 🔹 4. Delete Notification

```js
db.notifications.deleteOne({ _id: ObjectId("abc") });
```

---

## 4. Problems with Large Data

* Too many notifications → slow queries
* High read/write load
* Database size increases
* Real-time delivery becomes slow

---

## 5. Solutions

### 🔹 Indexing

* Add index on `userId` and `createdAt`

```js
db.notifications.createIndex({ userId: 1, createdAt: -1 });
```

---

### 🔹 Pagination

* Load notifications in chunks (limit & skip)

```js
db.notifications.find({ userId: "123" }).limit(10).skip(0);
```

---

### 🔹 Data Archiving

* Move old notifications to archive collection

---

### 🔹 Caching

* Use Redis for frequently accessed data

---

### 🔹 Horizontal Scaling

* Use sharding in MongoDB

---

## 6. Architecture Update

* Backend → Node.js
* DB → MongoDB
* Cache → Redis
* Real-time → WebSocket

---

## 7. Summary

The system is designed to handle large-scale notification data efficiently using NoSQL, indexing, caching, and scalable architecture.
