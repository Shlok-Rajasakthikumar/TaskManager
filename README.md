# Task Manager Application

A comprehensive Task Management system built with Node.js, Express, MongoDB, and Mongoose. This application allows users to create, manage, and track tasks with various features such as priority levels, status transitions, soft delete functionality, and user-specific task assignments.

## Table of Contents

- [Features](#features)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Environment Variables](#environment-variables)
- [Running the Application](#running-the-application)
- [API Endpoints](#api-endpoints)
  - [Authentication](#authentication)
  - [Users](#users)
  - [Tasks](#tasks)
  - [Statuses](#statuses)
- [Models](#models)
  - [User Model](#user-model)
  - [Task Model](#task-model)
  - [Status Model](#status-model)
- [Error Handling](#error-handling)
- [Logging](#logging)
- [Contributing](#contributing)
- [License](#license)

---

## Features

- **User Authentication**: Secure user registration and login using JWT tokens.
- **Task Management**:
  - Create, update, delete, and restore tasks.
  - Assign tasks to users.
  - Set task priorities (low, medium, high).
  - Update task statuses with validation for allowed transitions.
  - Soft delete tasks with the ability to restore.
  - Search and filter tasks by status, priority, date range, and keywords.
  - View tasks assigned to or created by the user.
  - View overdue tasks and tasks due today.
- **Status Management**:
  - Create, update, delete, and restore statuses.
  - Define allowed next and previous statuses for transitions.
  - Validate status transitions to ensure tasks follow predefined workflows.
  - Soft delete statuses with the ability to restore.
- **Comments**:
  - Add comments to tasks by authorized users.
- **Logging**:
  - Track user actions such as task creation, updates, deletions, and status changes.
- **Rate Limiting**:
  - Implemented rate limiting to prevent abuse of the API.

---

## Prerequisites

- **Node.js**: Version 14 or above.
- **MongoDB**: A running MongoDB instance.
- **npm**: Node package manager.

---

## Installation

1. **Clone the repository**:

   ```bash
   git clone https://github.com/your-username/task-manager.git
   cd task-manager
   ```

2. **Install dependencies**:

   ```bash
   npm install
   ```

---

## Environment Variables

Create a `.env` file in the root directory and set the following environment variables:

```env
PORT=3000
MONGO_URI=mongodb://localhost:27017/task-manager
JWT_SECRET=your_jwt_secret_key
JWT_EXPIRE=1d
```

- **PORT**: The port on which the server will run.
- **MONGO_URI**: The MongoDB connection string.
- **JWT_SECRET**: Secret key for JWT token generation.
- **JWT_EXPIRE**: JWT token expiration time.

---


2. **Run the Seeder Script**:

   ```bash
   node seeder.js
   ```

   The script will:

   - Clear existing data.
   - Create 10 dummy users with hashed passwords.
   - Create 10 dummy statuses with relationships.
   - Create 10 dummy tasks assigned to users.

---

## Running the Application

Start the server:

```bash
npm start
```

The server will start on the port specified in your `.env` file (default is `3000`).

---

## API Endpoints

### Authentication

- **Register a User**

  ```http
  POST /api/v1/user/register
  ```

  **Request Body**:

  ```json
  {
    "name": "John Doe",
    "email": "john@example.com",
    "password": "password123"
  }
  ```

- **Login a User**

  ```http
  POST /api/v1/user/login
  ```

  **Request Body**:

  ```json
  {
    "email": "john@example.com",
    "password": "password123"
  }
  ```

### Users

- **Get Current User Profile**

  ```http
  GET /api/v1/user/profile
  ```

  **Headers**:

  ```
  Authorization: Bearer <JWT_TOKEN>
  ```

### Tasks

- **Get All Tasks**

  ```http
  GET /api/tasks
  ```

- **Create a Task**

  ```http
  POST /api/tasks
  ```

  **Request Body**:

  ```json
  {
    "title": "Task Title",
    "description": "Task Description",
    "status": "<STATUS_ID>",
    "assignedTo": "<USER_ID>",
    "priority": "medium"
  }
  ```

- **Update a Task**

  ```http
  PUT /api/tasks/:id
  ```

- **Delete a Task**

  ```http
  DELETE /api/tasks/:id
  ```

- **Soft Delete a Task**

  ```http
  DELETE /api/tasks/:id/soft-delete
  ```

- **Restore a Soft-Deleted Task**

  ```http
  POST /api/tasks/:id/restore
  ```

- **Assign a Task to a User**

  ```http
  POST /api/tasks/:id/assign
  ```

  **Request Body**:

  ```json
  {
    "assignedToId": "<USER_ID>"
  }
  ```

- **Unassign a Task**

  ```http
  PUT /api/tasks/:id/unassign
  ```

- **Update Task Status**

  ```http
  PUT /api/tasks/:taskId/status/:statusId
  ```

- **Add Comment to Task**

  ```http
  POST /api/tasks/:taskId/comments
  ```

  **Request Body**:

  ```json
  {
    "commentText": "This is a comment."
  }
  ```

- **Get Tasks by Priority**

  ```http
  GET /api/tasks/priority/:priority
  ```

- **Search Tasks**

  ```http
  GET /api/tasks/search?searchTerm=keyword
  ```

- **Get Overdue Tasks**

  ```http
  GET /api/tasks/overdue
  ```

- **Get Tasks Due Today**

  ```http
  GET /api/tasks/due-today
  ```

### Statuses

- **Get All Statuses**

  ```http
  GET /api/status
  ```

- **Create a Status**

  ```http
  POST /api/status
  ```

  **Request Body**:

  ```json
  {
    "statusName": "In Progress"
  }
  ```

- **Update a Status**

  ```http
  PUT /api/status/:id
  ```

- **Delete a Status**

  ```http
  DELETE /api/status/:id
  ```

- **Soft Delete a Status**

  ```http
  DELETE /api/status/:id/soft-delete
  ```

- **Restore a Soft-Deleted Status**

  ```http
  POST /api/status/:id/restore
  ```

- **Add Next Status**

  ```http
  POST /api/status/:id/next-status
  ```

  **Request Body**:

  ```json
  {
    "nextStatusId": "<STATUS_ID>"
  }
  ```

- **Add Previous Status**

  ```http
  POST /api/status/:id/prev-status
  ```

  **Request Body**:

  ```json
  {
    "prevStatusId": "<STATUS_ID>"
  }
  ```

- **Validate Status Transition**

  ```http
  GET /api/status/validate-transition/:currentStatusId/:nextStatusId
  ```

---

## Models

### User Model

- **Fields**:
  - `name`: String (required)
  - `email`: String (required, unique)
  - `password`: String (required)
  - `createdAt`: Date (default: now)

### Task Model

- **Fields**:
  - `title`: String (required)
  - `description`: String
  - `status`: ObjectId (ref: Status, required)
  - `createdDate`: Date (default: now)
  - `endDate`: Date
  - `modifiedDate`: Date
  - `user`: ObjectId (ref: User, required)
  - `assignedTo`: ObjectId (ref: User)
  - `comments`: Array of ObjectId (ref: Comment)
  - `priority`: String (enum: ['low', 'medium', 'high'], default: 'low')
  - `isDeleted`: Boolean (default: false)
  - `deletedDate`: Date

### Status Model

- **Fields**:
  - `statusName`: String (required)
  - `nextStatus`: Array of ObjectId (ref: Status)
  - `previousStatus`: Array of ObjectId (ref: Status)
  - `user`: ObjectId (ref: User, required)
  - `isDeleted`: Boolean (default: false)
  - `deletedDate`: Date

---

## Error Handling

The application uses a centralized error-handling mechanism to capture and respond to errors gracefully.

- **Validation Errors**: Respond with `400 Bad Request` and error details.
- **Authentication Errors**: Respond with `401 Unauthorized`.
- **Authorization Errors**: Respond with `403 Forbidden`.
- **Not Found Errors**: Respond with `404 Not Found`.
- **Server Errors**: Respond with `500 Internal Server Error`.

---

## Logging

User actions and important events are logged using the `Log` model, which stores information about actions performed, such as task creation, updates, deletions, and status changes.

---
