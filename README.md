# chat_application_Design

high-level relational database design for Telegram

1. User Table
   This table stores user details (like phone number, username, profile, etc.).
 
Column | Type | Constraints | Description
user_id | BIGINT | PRIMARY KEY | Unique identifier for each user
username | VARCHAR(255) | UNIQUE, NOT NULL | Unique username (optional, can be NULL)
phone_number | VARCHAR(20) | UNIQUE, NOT NULL | User’s phone number (must be unique)
password_hash | VARCHAR(255) | NOT NULL | Hashed password
bio | TEXT | NULLABLE | Optional user bio
profile_photo | TEXT | NULLABLE | URL to user’s profile photo
created_at | TIMESTAMP | NOT NULL | Account creation time
last_seen | TIMESTAMP | NULLABLE | Last seen timestamp


2. Chats Table
This table defines chat metadata like group chats, private chats, and channels.

Column | Type | Constraints | Description
chat_id | BIGINT | PRIMARY KEY | Unique identifier for each chat
chat_type | ENUM('private', 'group', 'channel') | NOT NULL | Type of chat (private, group, channel)
title | VARCHAR(255) | NULLABLE | Chat title (for groups/channels)
created_by | BIGINT | FOREIGN KEY (user_id) | FK to Users.user_id (creator of the chat)
created_at | TIMESTAMP | NOT NULL | Creation timestamp
