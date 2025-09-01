
## PostgreSQL Tables

The PostgreSQL implementation mirrors the MongoDB structure with additional sync metadata for the dual-write system.

### 1. postgres_users Table

```sql
CREATE TABLE postgres_users (
    id SERIAL PRIMARY KEY,
    mongo_id VARCHAR(24) UNIQUE,
    google_id VARCHAR(255) UNIQUE NOT NULL,
    email_id VARCHAR(255) UNIQUE NOT NULL,
    name VARCHAR(255) NOT NULL,
    picture VARCHAR(500),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE,
    last_sync_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    sync_status VARCHAR(20) DEFAULT 'SYNCED' CHECK (sync_status IN ('SYNCED', 'PENDING', 'FAILED')),
    sync_error TEXT
);
```

#### Fields

| Field        | Type      | Description                                    |
| ------------ | --------- | ---------------------------------------------- |
| id           | Serial    | Primary key (auto-increment).                  |
| mongo_id     | String    | MongoDB ObjectId reference (unique).           |
| google_id    | String    | Google OAuth ID (unique).                      |
| email_id     | String    | User's email address (unique).                 |
| name         | String    | User's display name.                           |
| picture      | String    | URL to user's profile picture (optional).      |
| created_at   | Timestamp | User creation timestamp.                       |
| updated_at   | Timestamp | Last update timestamp (optional).              |
| last_sync_at | Timestamp | Last sync with MongoDB timestamp.              |
| sync_status  | String    | Sync status: SYNCED, PENDING, FAILED.          |
| sync_error   | Text      | Error message if sync failed (optional).       |

### 2. postgres_tasks Table

```sql
CREATE TABLE postgres_tasks (
    id SERIAL PRIMARY KEY,
    mongo_id VARCHAR(24) UNIQUE,
    display_id VARCHAR(100),
    title VARCHAR(500) NOT NULL,
    description TEXT,
    priority INTEGER DEFAULT 3 CHECK (priority IN (1, 2, 3)),
    status VARCHAR(20) DEFAULT 'TODO',
    is_acknowledged BOOLEAN DEFAULT FALSE,
    is_deleted BOOLEAN DEFAULT FALSE,
    started_at TIMESTAMP WITH TIME ZONE,
    due_at TIMESTAMP WITH TIME ZONE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE,
    created_by VARCHAR(24) NOT NULL,
    updated_by VARCHAR(24),
    last_sync_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    sync_status VARCHAR(20) DEFAULT 'SYNCED' CHECK (sync_status IN ('SYNCED', 'PENDING', 'FAILED')),
    sync_error TEXT
);
```

#### Fields

| Field            | Type      | Description                                    |
| ---------------- | --------- | ---------------------------------------------- |
| id               | Serial    | Primary key (auto-increment).                  |
| mongo_id         | String    | MongoDB ObjectId reference (unique).           |
| display_id       | String    | Human-readable task identifier (optional).     |
| title            | String    | Task title.                                    |
| description      | Text      | Task description (optional).                   |
| priority         | Integer   | Task priority: 1=HIGH, 2=MEDIUM, 3=LOW.       |
| status           | String    | Task status: TODO, IN_PROGRESS, DEFERRED, BLOCKED, DONE. |
| is_acknowledged  | Boolean   | Whether the task has been acknowledged.        |
| is_deleted       | Boolean   | Soft delete flag.                              |
| started_at       | Timestamp | When the task was started (optional).          |
| due_at           | Timestamp | Task due date (optional).                      |
| created_at       | Timestamp | Task creation timestamp.                       |
| updated_at       | Timestamp | Last update timestamp (optional).              |
| created_by       | String    | User ID who created the task.                  |
| updated_by       | String    | User ID who last updated the task (optional).  |
| last_sync_at     | Timestamp | Last sync with MongoDB timestamp.              |
| sync_status      | String    | Sync status: SYNCED, PENDING, FAILED.          |
| sync_error       | Text      | Error message if sync failed (optional).       |

### 3. postgres_task_labels Table (Junction Table)

```sql
CREATE TABLE postgres_task_labels (
    id SERIAL PRIMARY KEY,
    task_id INTEGER REFERENCES postgres_tasks(id) ON DELETE CASCADE,
    label_mongo_id VARCHAR(24) NOT NULL,
    UNIQUE(task_id, label_mongo_id)
);
```

#### Fields

| Field           | Type      | Description                                    |
| --------------- | --------- | ---------------------------------------------- |
| id              | Serial    | Primary key (auto-increment).                  |
| task_id         | Integer   | Foreign key reference to postgres_tasks.id.    |
| label_mongo_id  | String    | MongoDB ObjectId reference for the label.      |

### 4. postgres_deferred_details Table

```sql
CREATE TABLE postgres_deferred_details (
    id SERIAL PRIMARY KEY,
    task_id INTEGER REFERENCES postgres_tasks(id) ON DELETE CASCADE,
    deferred_at TIMESTAMP WITH TIME ZONE,
    deferred_till TIMESTAMP WITH TIME ZONE,
    deferred_by VARCHAR(24)
);
```

#### Fields

| Field         | Type      | Description                                    |
| ------------- | --------- | ---------------------------------------------- |
| id            | Serial    | Primary key (auto-increment).                  |
| task_id       | Integer   | Foreign key reference to postgres_tasks.id.    |
| deferred_at   | Timestamp | When the task was deferred (optional).         |
| deferred_till | Timestamp | Until when the task is deferred (optional).    |
| deferred_by   | String    | User ID who deferred the task (optional).      |

### 5. postgres_teams Table

```sql
CREATE TABLE postgres_teams (
    id SERIAL PRIMARY KEY,
    mongo_id VARCHAR(24) UNIQUE,
    name VARCHAR(100) NOT NULL,
    description TEXT,
    invite_code VARCHAR(100) UNIQUE NOT NULL,
    poc_id VARCHAR(24),
    created_by VARCHAR(24) NOT NULL,
    updated_by VARCHAR(24) NOT NULL,
    is_deleted BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    last_sync_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    sync_status VARCHAR(20) DEFAULT 'SYNCED' CHECK (sync_status IN ('SYNCED', 'PENDING', 'FAILED')),
    sync_error TEXT
);
```

#### Fields

| Field        | Type      | Description                                    |
| ------------ | --------- | ---------------------------------------------- |
| id           | Serial    | Primary key (auto-increment).                  |
| mongo_id     | String    | MongoDB ObjectId reference (unique).           |
| name         | String    | Team name (1-100 characters).                  |
| description  | Text      | Team description (optional).                   |
| invite_code  | String    | Unique team invite code.                       |
| poc_id       | String    | Point of contact user ID (optional).           |
| created_by   | String    | User ID who created the team.                  |
| updated_by   | String    | User ID who last updated the team.             |
| is_deleted   | Boolean   | Soft delete flag.                              |
| created_at   | Timestamp | Team creation timestamp.                       |
| updated_at   | Timestamp | Last update timestamp.                         |
| last_sync_at | Timestamp | Last sync with MongoDB timestamp.              |
| sync_status  | String    | Sync status: SYNCED, PENDING, FAILED.          |
| sync_error   | Text      | Error message if sync failed (optional).       |

### 6. postgres_user_team_details Table

```sql
CREATE TABLE postgres_user_team_details (
    id SERIAL PRIMARY KEY,
    mongo_id VARCHAR(24) UNIQUE,
    user_id VARCHAR(24) NOT NULL,
    team_id VARCHAR(24) NOT NULL,
    created_by VARCHAR(24) NOT NULL,
    updated_by VARCHAR(24) NOT NULL,
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    last_sync_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    sync_status VARCHAR(20) DEFAULT 'SYNCED' CHECK (sync_status IN ('SYNCED', 'PENDING', 'FAILED')),
    sync_error TEXT,
    UNIQUE(user_id, team_id)
);
```

#### Fields

| Field        | Type      | Description                                    |
| ------------ | --------- | ---------------------------------------------- |
| id           | Serial    | Primary key (auto-increment).                  |
| mongo_id     | String    | MongoDB ObjectId reference (unique).           |
| user_id      | String    | User ID.                                       |
| team_id      | String    | Team ID.                                       |
| created_by   | String    | User ID who created the membership.            |
| updated_by   | String    | User ID who last updated the membership.       |
| is_active    | Boolean   | Whether the membership is active.              |
| created_at   | Timestamp | Membership creation timestamp.                 |
| updated_at   | Timestamp | Last update timestamp.                         |
| last_sync_at | Timestamp | Last sync with MongoDB timestamp.              |
| sync_status  | String    | Sync status: SYNCED, PENDING, FAILED.          |
| sync_error   | Text      | Error message if sync failed (optional).       |

### 7. postgres_labels Table

```sql
CREATE TABLE postgres_labels (
    id SERIAL PRIMARY KEY,
    mongo_id VARCHAR(24) UNIQUE,
    name VARCHAR(100) UNIQUE NOT NULL,
    color VARCHAR(7) DEFAULT '#000000',
    description TEXT,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE,
    last_sync_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    sync_status VARCHAR(20) DEFAULT 'SYNCED' CHECK (sync_status IN ('SYNCED', 'PENDING', 'FAILED')),
    sync_error TEXT
);
```

#### Fields

| Field        | Type      | Description                                    |
| ------------ | --------- | ---------------------------------------------- |
| id           | Serial    | Primary key (auto-increment).                  |
| mongo_id     | String    | MongoDB ObjectId reference (unique).           |
| name         | String    | Label name.                                    |
| color        | String    | Label color (hex code).                        |
| description  | Text      | Label description (optional).                   |
| created_at   | Timestamp | Label creation timestamp.                      |
| updated_at   | Timestamp | Last update timestamp (optional).              |
| last_sync_at | Timestamp | Last sync with MongoDB timestamp.              |
| sync_status  | String    | Sync status: SYNCED, PENDING, FAILED.          |
| sync_error   | Text      | Error message if sync failed (optional).       |

### 8. postgres_task_assignments Table

```sql
CREATE TABLE postgres_task_assignments (
    id SERIAL PRIMARY KEY,
    mongo_id VARCHAR(24) UNIQUE,
    task_mongo_id VARCHAR(24) NOT NULL,
    assignee_id VARCHAR(24) NOT NULL,
    user_type VARCHAR(10) CHECK (user_type IN ('user', 'team')),
    team_id VARCHAR(24),
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE,
    created_by VARCHAR(24) NOT NULL,
    updated_by VARCHAR(24),
    last_sync_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    sync_status VARCHAR(20) DEFAULT 'SYNCED' CHECK (sync_status IN ('SYNCED', 'PENDING', 'FAILED')),
    sync_error TEXT
);
```

#### Fields

| Field          | Type      | Description                                    |
| -------------- | --------- | ---------------------------------------------- |
| id             | Serial    | Primary key (auto-increment).                  |
| mongo_id       | String    | MongoDB ObjectId reference (unique).           |
| task_mongo_id  | String    | Task ID being assigned.                        |
| assignee_id    | String    | User or Team ID receiving the assignment.      |
| user_type      | String    | Type of assignee: "user" or "team".            |
| team_id        | String    | Original team ID (for reassignments).          |
| is_active      | Boolean   | Whether the assignment is active.              |
| created_at     | Timestamp | Assignment creation timestamp.                 |
| updated_at     | Timestamp | Last update timestamp (optional).              |
| created_by     | String    | User ID who created the assignment.            |
| updated_by     | String    | User ID who last updated the assignment.       |
| last_sync_at   | Timestamp | Last sync with MongoDB timestamp.              |
| sync_status    | String    | Sync status: SYNCED, PENDING, FAILED.          |
| sync_error     | Text      | Error message if sync failed (optional).       |

### 9. postgres_roles Table

```sql
CREATE TABLE postgres_roles (
    id SERIAL PRIMARY KEY,
    mongo_id VARCHAR(24) UNIQUE,
    name VARCHAR(100) UNIQUE NOT NULL,
    description TEXT,
    permissions JSONB DEFAULT '{}',
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE,
    last_sync_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    sync_status VARCHAR(20) DEFAULT 'SYNCED' CHECK (sync_status IN ('SYNCED', 'PENDING', 'FAILED')),
    sync_error TEXT
);
```

#### Fields

| Field        | Type      | Description                                    |
| ------------ | --------- | ---------------------------------------------- |
| id           | Serial    | Primary key (auto-increment).                  |
| mongo_id     | String    | MongoDB ObjectId reference (unique).           |
| name         | String    | Role name: moderator, owner, admin, member.    |
| description  | Text      | Role description (optional).                   |
| permissions  | JSONB     | Role permissions as JSON object.               |
| created_at   | Timestamp | Role creation timestamp.                       |
| updated_at   | Timestamp | Last update timestamp (optional).              |
| last_sync_at | Timestamp | Last sync with MongoDB timestamp.              |
| sync_status  | String    | Sync status: SYNCED, PENDING, FAILED.          |
| sync_error   | Text      | Error message if sync failed (optional).       |

### 10. postgres_user_roles Table

```sql
CREATE TABLE postgres_user_roles (
    id SERIAL PRIMARY KEY,
    mongo_id VARCHAR(24) UNIQUE,
    user_id VARCHAR(24) NOT NULL,
    role_name VARCHAR(50) NOT NULL,
    scope VARCHAR(20) NOT NULL,
    team_id VARCHAR(24),
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    created_by VARCHAR(24) DEFAULT 'system',
    last_sync_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    sync_status VARCHAR(20) DEFAULT 'SYNCED' CHECK (sync_status IN ('SYNCED', 'PENDING', 'FAILED')),
    sync_error TEXT,
    UNIQUE(user_id, role_name, scope, team_id)
);
```

#### Fields

| Field        | Type      | Description                                    |
| ------------ | --------- | ---------------------------------------------- |
| id           | Serial    | Primary key (auto-increment).                  |
| mongo_id     | String    | MongoDB ObjectId reference (unique).           |
| user_id      | String    | User ID.                                       |
| role_name    | String    | Role name: moderator, owner, admin, member.    |
| scope        | String    | Role scope: GLOBAL or TEAM.                    |
| team_id      | String    | Team ID (required for TEAM scope, null for GLOBAL). |
| is_active    | Boolean   | Whether the role assignment is active.         |
| created_at   | Timestamp | Role assignment creation timestamp.            |
| created_by   | String    | User ID who assigned the role.                 |
| last_sync_at | Timestamp | Last sync with MongoDB timestamp.              |
| sync_status  | String    | Sync status: SYNCED, PENDING, FAILED.          |
| sync_error   | Text      | Error message if sync failed (optional).       |

### 11. postgres_watchlist Table

```sql
CREATE TABLE postgres_watchlist (
    id SERIAL PRIMARY KEY,
    mongo_id VARCHAR(24) UNIQUE,
    task_id VARCHAR(24) NOT NULL,
    user_id VARCHAR(24) NOT NULL,
    is_active BOOLEAN DEFAULT TRUE,
    created_by VARCHAR(24) NOT NULL,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_by VARCHAR(24),
    updated_at TIMESTAMP WITH TIME ZONE,
    last_sync_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    sync_status VARCHAR(20) DEFAULT 'SYNCED' CHECK (sync_status IN ('SYNCED', 'PENDING', 'FAILED')),
    sync_error TEXT,
    UNIQUE(user_id, task_id)
);
```

#### Fields

| Field        | Type      | Description                                    |
| ------------ | --------- | ---------------------------------------------- |
| id           | Serial    | Primary key (auto-increment).                  |
| mongo_id     | String    | MongoDB ObjectId reference (unique).           |
| task_id      | String    | Task ID being watched.                         |
| user_id      | String    | User ID watching the task.                     |
| is_active    | Boolean   | Whether the watch is active.                   |
| created_by   | String    | User ID who created the watch.                 |
| created_at   | Timestamp | Watch creation timestamp.                      |
| updated_by   | String    | User ID who last updated the watch (optional). |
| updated_at   | Timestamp | Last update timestamp (optional).              |
| last_sync_at | Timestamp | Last sync with MongoDB timestamp.              |
| sync_status  | String    | Sync status: SYNCED, PENDING, FAILED.          |
| sync_error   | Text      | Error message if sync failed (optional).       |

### 12. postgres_team_creation_invite_codes Table

```sql
CREATE TABLE postgres_team_creation_invite_codes (
    id SERIAL PRIMARY KEY,
    mongo_id VARCHAR(24) UNIQUE,
    code VARCHAR(100) UNIQUE NOT NULL,
    description TEXT,
    created_by VARCHAR(24) NOT NULL,
    used_by VARCHAR(24),
    is_used BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    used_at TIMESTAMP WITH TIME ZONE,
    last_sync_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    sync_status VARCHAR(20) DEFAULT 'SYNCED' CHECK (sync_status IN ('SYNCED', 'PENDING', 'FAILED')),
    sync_error TEXT
);
```

#### Fields

| Field        | Type      | Description                                    |
| ------------ | --------- | ---------------------------------------------- |
| id           | Serial    | Primary key (auto-increment).                  |
| mongo_id     | String    | MongoDB ObjectId reference (unique).           |
| code         | String    | Unique invite code.                            |
| description  | Text      | Code description (optional).                   |
| created_by   | String    | User ID who created the code.                  |
| used_by      | String    | User ID who used the code (optional).          |
| is_used      | Boolean   | Whether the code has been used.                |
| created_at   | Timestamp | Code creation timestamp.                       |
| used_at      | Timestamp | When the code was used (optional).             |
| last_sync_at | Timestamp | Last sync with MongoDB timestamp.              |
| sync_status  | String    | Sync status: SYNCED, PENDING, FAILED.          |
| sync_error   | Text      | Error message if sync failed (optional).       |

### 13. postgres_audit_logs Table

```sql
CREATE TABLE postgres_audit_logs (
    id SERIAL PRIMARY KEY,
    mongo_id VARCHAR(24) UNIQUE,
    task_id VARCHAR(24),
    team_id VARCHAR(24),
    previous_executor_id VARCHAR(24),
    new_executor_id VARCHAR(24),
    spoc_id VARCHAR(24),
    action VARCHAR(100) NOT NULL,
    timestamp TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    status_from VARCHAR(20),
    status_to VARCHAR(20),
    assignee_from VARCHAR(24),
    assignee_to VARCHAR(24),
    performed_by VARCHAR(24),
    last_sync_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    sync_status VARCHAR(20) DEFAULT 'SYNCED' CHECK (sync_status IN ('SYNCED', 'PENDING', 'FAILED')),
    sync_error TEXT
);
```

#### Fields

| Field                | Type      | Description                                    |
| -------------------- | --------- | ---------------------------------------------- |
| id                   | Serial    | Primary key (auto-increment).                  |
| mongo_id             | String    | MongoDB ObjectId reference (unique).           |
| task_id              | String    | Task ID (optional).                            |
| team_id              | String    | Team ID (optional).                            |
| previous_executor_id | String    | Previous executor ID (optional).               |
| new_executor_id      | String    | New executor ID (optional).                    |
| spoc_id              | String    | SPOC (Single Point of Contact) ID (optional).  |
| action               | String    | Action performed (e.g., "assigned_to_team", "status_changed"). |
| timestamp            | Timestamp | When the action occurred.                      |
| status_from          | String    | Previous status (for status changes).          |
| status_to            | String    | New status (for status changes).               |
| assignee_from        | String    | Previous assignee (for assignment changes).    |
| assignee_to          | String    | New assignee (for assignment changes).         |
| performed_by         | String    | User ID who performed the action (optional).   |
| last_sync_at         | Timestamp | Last sync with MongoDB timestamp.              |
| sync_status          | String    | Sync status: SYNCED, PENDING, FAILED.          |
| sync_error           | Text      | Error message if sync failed (optional).       |