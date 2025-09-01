# Todo Backend Data Models

This document provides comprehensive data model documentation for both MongoDB and PostgreSQL implementations in the todo backend system.

## Overview

The system implements a dual-write architecture with:
- **MongoDB**: Primary database for current operations
- **PostgreSQL**: Secondary database for future migration and backup

## MongoDB Collections

### 1. Users Collection

```json
{
  "_id": "ObjectId",
  "google_id": "string",
  "email_id": "string (email)",
  "name": "string",
  "picture": "string (URL) | null",
  "created_at": "datetime",
  "updated_at": "datetime | null"
}
```

#### Fields

| Field      | Type      | Description                                    |
| ---------- | --------- | ---------------------------------------------- |
| _id        | ObjectId  | Unique identifier for the document.            |
| google_id  | String    | Google OAuth ID for authentication.            |
| email_id   | String    | User's email address (unique).                 |
| name       | String    | User's display name.                           |
| picture    | String    | URL to user's profile picture (optional).      |
| created_at | DateTime  | Timestamp when the user was created.           |
| updated_at | DateTime  | Timestamp when the user was last updated.      |

#### Example data

```json
{
  "_id": "507f1f77bcf86cd799439011",
  "google_id": "123456789012345678901",
  "email_id": "john.doe@example.com",
  "name": "John Doe",
  "picture": "https://lh3.googleusercontent.com/a/ACg8ocK...",
  "created_at": "2024-01-15T10:30:00Z",
  "updated_at": "2024-01-20T14:45:00Z"
}
```

### 2. Tasks Collection

```json
{
  "_id": "ObjectId",
  "displayId": "string | null",
  "title": "string",
  "description": "string | null",
  "priority": "<1 | 2 | 3>",
  "status": "<TODO | IN_PROGRESS | DEFERRED | BLOCKED | DONE>",
  "isAcknowledged": "boolean",
  "labels": "Array<ObjectId>",
  "isDeleted": "boolean",
  "deferredDetails": {
    "deferredAt": "datetime | null",
    "deferredTill": "datetime | null",
    "deferredBy": "string | null"
  },
  "startedAt": "datetime | null",
  "dueAt": "datetime | null",
  "createdAt": "datetime",
  "updatedAt": "datetime | null",
  "createdBy": "string",
  "updatedBy": "string | null"
}
```

#### Fields

| Field            | Type      | Description                                    |
| ---------------- | --------- | ---------------------------------------------- |
| _id              | ObjectId  | Unique identifier for the document.            |
| displayId        | String    | Human-readable task identifier (optional).     |
| title            | String    | Task title.                                    |
| description      | String    | Task description (optional).                   |
| priority         | Integer   | Task priority: 1=HIGH, 2=MEDIUM, 3=LOW.       |
| status           | String    | Task status: TODO, IN_PROGRESS, DEFERRED, BLOCKED, DONE. |
| isAcknowledged   | Boolean   | Whether the task has been acknowledged.        |
| labels           | Array     | Array of label ObjectIds.                      |
| isDeleted        | Boolean   | Soft delete flag.                              |
| deferredDetails  | Object    | Deferral information (optional).               |
| startedAt        | DateTime  | When the task was started (optional).          |
| dueAt            | DateTime  | Task due date (optional).                      |
| createdAt        | DateTime  | Task creation timestamp.                       |
| updatedAt        | DateTime  | Last update timestamp (optional).              |
| createdBy        | String    | User ID who created the task.                  |
| updatedBy        | String    | User ID who last updated the task (optional).  |

#### Example data

```json
{
  "_id": "507f1f77bcf86cd799439012",
  "displayId": "TASK-001",
  "title": "Implement user authentication",
  "description": "Add OAuth2 authentication with Google",
  "priority": 1,
  "status": "IN_PROGRESS",
  "isAcknowledged": true,
  "labels": ["507f1f77bcf86cd799439020"],
  "isDeleted": false,
  "deferredDetails": null,
  "startedAt": "2024-01-15T09:00:00Z",
  "dueAt": "2024-01-25T17:00:00Z",
  "createdAt": "2024-01-15T08:30:00Z",
  "updatedAt": "2024-01-16T10:15:00Z",
  "createdBy": "507f1f77bcf86cd799439011",
  "updatedBy": "507f1f77bcf86cd799439011"
}
```

### 3. Teams Collection

```json
{
  "_id": "ObjectId",
  "name": "string",
  "description": "string | null",
  "poc_id": "ObjectId | null",
  "invite_code": "string",
  "created_by": "ObjectId",
  "updated_by": "ObjectId",
  "created_at": "datetime",
  "updated_at": "datetime",
  "is_deleted": "boolean"
}
```

#### Fields

| Field        | Type      | Description                                    |
| ------------ | --------- | ---------------------------------------------- |
| _id          | ObjectId  | Unique identifier for the document.            |
| name         | String    | Team name (1-100 characters).                  |
| description  | String    | Team description (optional).                   |
| poc_id       | ObjectId  | Point of contact user ID (optional).           |
| invite_code  | String    | Unique team invite code.                       |
| created_by   | ObjectId  | User ID who created the team.                  |
| updated_by   | ObjectId  | User ID who last updated the team.             |
| created_at   | DateTime  | Team creation timestamp.                       |
| updated_at   | DateTime  | Last update timestamp.                         |
| is_deleted   | Boolean   | Soft delete flag.                              |

#### Example data

```json
{
  "_id": "507f1f77bcf86cd799439013",
  "name": "Frontend Development Team",
  "description": "Responsible for user interface development",
  "poc_id": "507f1f77bcf86cd799439011",
  "invite_code": "FRONTEND2024",
  "created_by": "507f1f77bcf86cd799439011",
  "updated_by": "507f1f77bcf86cd799439011",
  "created_at": "2024-01-10T10:00:00Z",
  "updated_at": "2024-01-15T14:30:00Z",
  "is_deleted": false
}
```

### 4. User Team Details Collection

```json
{
  "_id": "ObjectId",
  "user_id": "ObjectId",
  "team_id": "ObjectId",
  "is_active": "boolean",
  "created_at": "datetime",
  "updated_at": "datetime",
  "created_by": "ObjectId",
  "updated_by": "ObjectId"
}
```

#### Fields

| Field      | Type      | Description                                    |
| ---------- | --------- | ---------------------------------------------- |
| _id        | ObjectId  | Unique identifier for the document.            |
| user_id    | ObjectId  | User ID.                                       |
| team_id    | ObjectId  | Team ID.                                       |
| is_active  | Boolean   | Whether the membership is active.              |
| created_at | DateTime  | Membership creation timestamp.                 |
| updated_at | DateTime  | Last update timestamp.                         |
| created_by | ObjectId  | User ID who created the membership.            |
| updated_by | ObjectId  | User ID who last updated the membership.       |

#### Example data

```json
{
  "_id": "507f1f77bcf86cd799439014",
  "user_id": "507f1f77bcf86cd799439011",
  "team_id": "507f1f77bcf86cd799439013",
  "is_active": true,
  "created_at": "2024-01-10T10:30:00Z",
  "updated_at": "2024-01-15T14:30:00Z",
  "created_by": "507f1f77bcf86cd799439011",
  "updated_by": "507f1f77bcf86cd799439011"
}
```

### 5. Labels Collection

```json
{
  "_id": "ObjectId",
  "name": "string",
  "color": "string",
  "isDeleted": "boolean",
  "createdAt": "datetime",
  "updatedAt": "datetime | null",
  "createdBy": "string",
  "updatedBy": "string | null"
}
```

#### Fields

| Field      | Type      | Description                                    |
| ---------- | --------- | ---------------------------------------------- |
| _id        | ObjectId  | Unique identifier for the document.            |
| name       | String    | Label name.                                    |
| color      | String    | Label color (hex code).                        |
| isDeleted  | Boolean   | Soft delete flag.                              |
| createdAt  | DateTime  | Label creation timestamp.                      |
| updatedAt  | DateTime  | Last update timestamp (optional).              |
| createdBy  | String    | User ID who created the label.                 |
| updatedBy  | String    | User ID who last updated the label (optional). |

#### Example data

```json
{
  "_id": "507f1f77bcf86cd799439020",
  "name": "Bug",
  "color": "#FF0000",
  "isDeleted": false,
  "createdAt": "2024-01-10T09:00:00Z",
  "updatedAt": null,
  "createdBy": "507f1f77bcf86cd799439011",
  "updatedBy": null
}
```

### 6. Task Assignments Collection (task_details)

```json
{
  "_id": "ObjectId",
  "task_id": "ObjectId",
  "assignee_id": "ObjectId",
  "user_type": "<user | team>",
  "is_active": "boolean",
  "created_by": "ObjectId",
  "updated_by": "ObjectId | null",
  "created_at": "datetime",
  "updated_at": "datetime | null",
  "executor_id": "ObjectId | null",
  "team_id": "ObjectId | null"
}
```

#### Fields

| Field       | Type      | Description                                    |
| ----------- | --------- | ---------------------------------------------- |
| _id         | ObjectId  | Unique identifier for the document.            |
| task_id     | ObjectId  | Task ID being assigned.                        |
| assignee_id | ObjectId  | User or Team ID receiving the assignment.      |
| user_type   | String    | Type of assignee: "user" or "team".            |
| is_active   | Boolean   | Whether the assignment is active.              |
| created_by  | ObjectId  | User ID who created the assignment.            |
| updated_by  | ObjectId  | User ID who last updated the assignment.       |
| created_at  | DateTime  | Assignment creation timestamp.                 |
| updated_at  | DateTime  | Last update timestamp (optional).              |
| executor_id | ObjectId  | User ID executing the task (for team assignments). |
| team_id     | ObjectId  | Original team ID (for reassignments).          |

#### Example data

```json
{
  "_id": "507f1f77bcf86cd799439021",
  "task_id": "507f1f77bcf86cd799439012",
  "assignee_id": "507f1f77bcf86cd799439013",
  "user_type": "team",
  "is_active": true,
  "created_by": "507f1f77bcf86cd799439011",
  "updated_by": null,
  "created_at": "2024-01-15T08:30:00Z",
  "updated_at": null,
  "executor_id": "507f1f77bcf86cd799439011",
  "team_id": null
}
```

### 7. Roles Collection

```json
{
  "_id": "ObjectId",
  "name": "<moderator | owner | admin | member>",
  "description": "string | null",
  "scope": "<GLOBAL | TEAM>",
  "is_active": "boolean",
  "created_by": "string",
  "created_at": "datetime",
  "updated_by": "string | null",
  "updated_at": "datetime | null"
}
```

#### Fields

| Field       | Type      | Description                                    |
| ----------- | --------- | ---------------------------------------------- |
| _id         | ObjectId  | Unique identifier for the document.            |
| name        | String    | Role name: moderator, owner, admin, member.    |
| description | String    | Role description (optional).                   |
| scope       | String    | Role scope: GLOBAL or TEAM.                    |
| is_active   | Boolean   | Whether the role is active.                    |
| created_by  | String    | User ID who created the role.                  |
| created_at  | DateTime  | Role creation timestamp.                       |
| updated_by  | String    | User ID who last updated the role (optional).  |
| updated_at  | DateTime  | Last update timestamp (optional).              |

#### Example data

```json
{
  "_id": "507f1f77bcf86cd799439022",
  "name": "admin",
  "description": "Team administrator with full permissions",
  "scope": "TEAM",
  "is_active": true,
  "created_by": "507f1f77bcf86cd799439011",
  "created_at": "2024-01-10T09:00:00Z",
  "updated_by": null,
  "updated_at": null
}
```

### 8. User Roles Collection

```json
{
  "_id": "ObjectId",
  "user_id": "string",
  "role_name": "<moderator | owner | admin | member>",
  "scope": "<GLOBAL | TEAM>",
  "team_id": "string | null",
  "is_active": "boolean",
  "created_at": "datetime",
  "created_by": "string"
}
```

#### Fields

| Field      | Type      | Description                                    |
| ---------- | --------- | ---------------------------------------------- |
| _id        | ObjectId  | Unique identifier for the document.            |
| user_id    | String    | User ID.                                       |
| role_name  | String    | Role name: moderator, owner, admin, member.    |
| scope      | String    | Role scope: GLOBAL or TEAM.                    |
| team_id    | String    | Team ID (required for TEAM scope, null for GLOBAL). |
| is_active  | Boolean   | Whether the role assignment is active.         |
| created_at | DateTime  | Role assignment creation timestamp.            |
| created_by | String    | User ID who assigned the role.                 |

#### Example data

```json
{
  "_id": "507f1f77bcf86cd799439023",
  "user_id": "507f1f77bcf86cd799439011",
  "role_name": "admin",
  "scope": "TEAM",
  "team_id": "507f1f77bcf86cd799439013",
  "is_active": true,
  "created_at": "2024-01-10T10:30:00Z",
  "created_by": "system"
}
```

### 9. Watchlist Collection

```json
{
  "_id": "ObjectId",
  "taskId": "string",
  "userId": "string",
  "isActive": "boolean",
  "createdAt": "datetime",
  "createdBy": "string",
  "updatedAt": "datetime | null",
  "updatedBy": "string | null"
}
```

#### Fields

| Field      | Type      | Description                                    |
| ---------- | --------- | ---------------------------------------------- |
| _id        | ObjectId  | Unique identifier for the document.            |
| taskId     | String    | Task ID being watched.                         |
| userId     | String    | User ID watching the task.                     |
| isActive   | Boolean   | Whether the watch is active.                   |
| createdAt  | DateTime  | Watch creation timestamp.                      |
| createdBy  | String    | User ID who created the watch.                 |
| updatedAt  | DateTime  | Last update timestamp (optional).              |
| updatedBy  | String    | User ID who last updated the watch (optional). |

#### Example data

```json
{
  "_id": "507f1f77bcf86cd799439024",
  "taskId": "507f1f77bcf86cd799439012",
  "userId": "507f1f77bcf86cd799439011",
  "isActive": true,
  "createdAt": "2024-01-15T09:00:00Z",
  "createdBy": "507f1f77bcf86cd799439011",
  "updatedAt": null,
  "updatedBy": null
}
```

### 10. Team Creation Invite Codes Collection

```json
{
  "_id": "ObjectId",
  "code": "string",
  "description": "string | null",
  "created_by": "ObjectId",
  "created_at": "datetime",
  "used_at": "datetime | null",
  "used_by": "ObjectId | null",
  "is_used": "boolean"
}
```

#### Fields

| Field       | Type      | Description                                    |
| ----------- | --------- | ---------------------------------------------- |
| _id         | ObjectId  | Unique identifier for the document.            |
| code        | String    | Unique invite code.                            |
| description | String    | Code description (optional).                   |
| created_by  | ObjectId  | User ID who created the code.                  |
| created_at  | DateTime  | Code creation timestamp.                       |
| used_at     | DateTime  | When the code was used (optional).             |
| used_by     | ObjectId  | User ID who used the code (optional).          |
| is_used     | Boolean   | Whether the code has been used.                |

#### Example data

```json
{
  "_id": "507f1f77bcf86cd799439025",
  "code": "TEAM2024ABC",
  "description": "Q1 2024 team creation code",
  "created_by": "507f1f77bcf86cd799439011",
  "created_at": "2024-01-01T00:00:00Z",
  "used_at": "2024-01-10T10:00:00Z",
  "used_by": "507f1f77bcf86cd799439011",
  "is_used": true
}
```

### 11. Audit Logs Collection

```json
{
  "_id": "ObjectId",
  "task_id": "ObjectId | null",
  "team_id": "ObjectId | null",
  "previous_executor_id": "ObjectId | null",
  "new_executor_id": "ObjectId | null",
  "spoc_id": "ObjectId | null",
  "action": "string",
  "timestamp": "datetime",
  "status_from": "string | null",
  "status_to": "string | null",
  "assignee_from": "ObjectId | null",
  "assignee_to": "ObjectId | null",
  "performed_by": "ObjectId | null"
}
```

#### Fields

| Field                | Type      | Description                                    |
| -------------------- | --------- | ---------------------------------------------- |
| _id                  | ObjectId  | Unique identifier for the document.            |
| task_id              | ObjectId  | Task ID (optional).                            |
| team_id              | ObjectId  | Team ID (optional).                            |
| previous_executor_id | ObjectId  | Previous executor ID (optional).               |
| new_executor_id      | ObjectId  | New executor ID (optional).                    |
| spoc_id              | ObjectId  | SPOC (Single Point of Contact) ID (optional).  |
| action               | String    | Action performed (e.g., "assigned_to_team", "status_changed"). |
| timestamp            | DateTime  | When the action occurred.                      |
| status_from          | String    | Previous status (for status changes).          |
| status_to            | String    | New status (for status changes).               |
| assignee_from        | ObjectId  | Previous assignee (for assignment changes).    |
| assignee_to          | ObjectId  | New assignee (for assignment changes).         |
| performed_by         | ObjectId  | User ID who performed the action (optional).   |

#### Example data

```json
{
  "_id": "507f1f77bcf86cd799439026",
  "task_id": "507f1f77bcf86cd799439012",
  "team_id": null,
  "previous_executor_id": null,
  "new_executor_id": "507f1f77bcf86cd799439011",
  "spoc_id": null,
  "action": "assigned_to_team",
  "timestamp": "2024-01-15T08:30:00Z",
  "status_from": null,
  "status_to": null,
  "assignee_from": null,
  "assignee_to": "507f1f77bcf86cd799439013",
  "performed_by": "507f1f77bcf86cd799439011"
}
```
