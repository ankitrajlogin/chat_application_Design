# chat_application_Design

High-level relational database design for a Telegram-like chat application.

## 1. User Table
This table stores user details (like phone number, username, profile, etc.).

| Column        | Type        | Constraints        | Description                                          |
|---------------|-------------|--------------------|------------------------------------------------------|
| user_id       | BIGINT      | PRIMARY KEY        | Unique identifier for each user                     |
| username      | VARCHAR(255)| UNIQUE, NOT NULL   | Unique username (optional, can be NULL)             |
| phone_number  | VARCHAR(20) | UNIQUE, NOT NULL   | User’s phone number (must be unique)                 |
| password_hash | VARCHAR(255)| NOT NULL           | Hashed password                                     |
| bio           | TEXT        | NULLABLE           | Optional user bio                                   |
| profile_photo | TEXT        | NULLABLE           | URL to user’s profile photo                         |
| created_at    | TIMESTAMP   | NOT NULL           | Account creation time                               |
| last_seen     | TIMESTAMP   | NULLABLE           | Last seen timestamp                                  |

## 2. Chats Table
This table defines chat metadata like group chats, private chats, and channels.

| Column       | Type        | Constraints        | Description                                          |
|--------------|-------------|--------------------|------------------------------------------------------|
| chat_id      | BIGINT      | PRIMARY KEY        | Unique identifier for each chat                     |
| chat_type    | ENUM('private', 'group', 'channel') | NOT NULL | Type of chat (private, group, channel)              |
| title        | VARCHAR(255)| NULLABLE           | Chat title (for groups/channels)                     |
| created_by   | BIGINT      | FOREIGN KEY (user_id) | FK to Users.user_id (creator of the chat)           |
| created_at   | TIMESTAMP   | NOT NULL           | Creation timestamp                                   |

## 3. ChatMembers Table
Defines the membership for each chat (users in a private/group chat).

| Column      | Type        | Constraints        | Description                                          |
|-------------|-------------|--------------------|------------------------------------------------------|
| id          | BIGINT      | PRIMARY KEY        | Unique ID for each record                            |
| chat_id     | BIGINT      | FOREIGN KEY (chat_id) | FK to Chats.chat_id                                 |
| user_id     | BIGINT      | FOREIGN KEY (user_id) | FK to Users.user_id                                  |
| role        | ENUM('admin', 'member', 'owner') | NOT NULL | Role in the chat                                    |
| joined_at   | TIMESTAMP   | NOT NULL           | Timestamp of when the user joined the chat          |
| is_muted    | BOOLEAN     | NOT NULL           | Whether the user has muted notifications in the chat |

## 4. Messages Table
Stores all messages in a chat (whether it's text, image, or media).

| Column      | Type        | Constraints        | Description                                          |
|-------------|-------------|--------------------|------------------------------------------------------|
| message_id  | BIGINT      | PRIMARY KEY        | Unique identifier for each message                   |
| chat_id     | BIGINT      | FOREIGN KEY (chat_id) | FK to Chats.chat_id                                 |
| sender_id   | BIGINT      | FOREIGN KEY (user_id) | FK to Users.user_id (sender of the message)         |
| content     | TEXT        | NULLABLE           | The text content of the message (optional)           |
| media_url   | TEXT        | NULLABLE           | URL for the attached media (image, video, etc.)      |
| message_type| ENUM('text', 'image', 'video', 'file', 'voice') | NOT NULL | Type of message (text, media, etc.)                 |
| reply_to    | BIGINT      | FOREIGN KEY (message_id) | FK to Messages.message_id (for replies)             |
| sent_at     | TIMESTAMP   | NOT NULL           | Timestamp when the message was sent                  |
| edited_at   | TIMESTAMP   | NULLABLE           | Timestamp when the message was edited                |
| is_deleted  | BOOLEAN     | NOT NULL           | Whether the message is deleted or not                |

## 5. Media Table
Stores media files (images, videos, etc.) sent by users in messages.

| Column      | Type        | Constraints        | Description                                          |
|-------------|-------------|--------------------|------------------------------------------------------|
| media_id    | BIGINT      | PRIMARY KEY        | Unique ID for the media                              |
| uploaded_by | BIGINT      | FOREIGN KEY (user_id) | FK to Users.user_id (who uploaded the media)        |
| media_url   | TEXT        | NOT NULL           | URL where the media file is stored                   |
| media_type  | ENUM('image', 'video', 'file', 'audio') | NOT NULL | Type of media (image, video, etc.)                  |
| uploaded_at | TIMESTAMP   | NOT NULL           | Timestamp when the media was uploaded                |

## 6. Reactions Table
Stores user reactions to messages (like 👍, ❤️, etc.).

| Column      | Type        | Constraints        | Description                                          |
|-------------|-------------|--------------------|------------------------------------------------------|
| reaction_id | BIGINT      | PRIMARY KEY        | Unique identifier for each reaction                  |
| message_id  | BIGINT      | FOREIGN KEY (message_id) | FK to Messages.message_id (the message being reacted to) |
| user_id     | BIGINT      | FOREIGN KEY (user_id) | FK to Users.user_id (who reacted)                    |
| emoji       | VARCHAR(5)  | NOT NULL           | Emoji used for the reaction (like 👍, ❤️)            |
| reacted_at  | TIMESTAMP   | NOT NULL           | Timestamp when the reaction was added                |

## 7. PinnedMessages Table
Stores pinned messages for a group or channel.

| Column      | Type        | Constraints        | Description                                          |
|-------------|-------------|--------------------|------------------------------------------------------|
| id          | BIGINT      | PRIMARY KEY        | Unique ID for the pinned message record              |
| chat_id     | BIGINT      | FOREIGN KEY (chat_id) | FK to Chats.chat_id (which chat the message is pinned in) |
| message_id  | BIGINT      | FOREIGN KEY (message_id) | FK to Messages.message_id (the pinned message)      |
| pinned_by   | BIGINT      | FOREIGN KEY (user_id) | FK to Users.user_id (who pinned the message)        |
| pinned_at   | TIMESTAMP   | NOT NULL           | Timestamp when the message was pinned                |

## Table Relationships Recap:

### Users Table
- **Primary Key:** user_id
- **Foreign Keys:**
  - user_id → Messages.sender_id
  - user_id → ChatMembers.user_id
  - user_id → Reactions.user_id
  - user_id → Media.uploaded_by

### Chats Table
- **Primary Key:** chat_id
- **Foreign Keys:**
  - created_by → Users.user_id (creator of the chat)

### ChatMembers Table
- **Primary Key:** id
- **Foreign Keys:**
  - chat_id → Chats.chat_id
  - user_id → Users.user_id

### Messages Table
- **Primary Key:** message_id
- **Foreign Keys:**
  - chat_id → Chats.chat_id
  - sender_id → Users.user_id
  - reply_to → Messages.message_id (optional, for replies)

### Media Table
- **Primary Key:** media_id
- **Foreign Keys:**
  - uploaded_by → Users.user_id

### Reactions Table
- **Primary Key:** reaction_id
- **Foreign Keys:**
  - message_id → Messages.message_id
  - user_id → Users.user_id

### PinnedMessages Table
- **Primary Key:** id
- **Foreign Keys:**
  - chat_id → Chats.chat_id
  - message_id → Messages.message_id
  - pinned_by → Users.user_id

## Summary of Connections:

- Users can send messages, react to messages, upload media, and be part of chats.
- Chats are owned by a user (creator), and users can be members of group chats or channels.
- Messages belong to chats and can have media or reactions.
- Pinned messages are linked to specific messages within a chat.
