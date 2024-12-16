# Learn CMS - A practical hans-on guide to building stuff

[schema diagram](https://drawsql.app/teams/meme-stage-startup/diagrams/medium/embed)

```sql
-- Users table to store user information
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    username VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    slug VARCHAR(50) UNIQUE NOT NULL,
    created_at timestamptz DEFAULT '2024-12-15 10:11:12',
    updated_at timestamptz DEFAULT '2024-12-15 10:11:12'
);

-- relationships table for followers
CREATE TABLE followers (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id) ON DELETE CASCADE,
    follower_id INTEGER REFERENCES users(id) ON DELETE CASCADE,
    created_at timestamptz DEFAULT '2024-12-15 10:11:12'
);

-- Posts table to store blog posts
CREATE TABLE posts (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id) ON DELETE CASCADE,
    title VARCHAR(255) NOT NULL,
    content TEXT NOT NULL,
    post_status VARCHAR(20) NOT NULL,
    post_tags VARCHAR(255)[] NOT NULL,
    slug VARCHAR(255) UNIQUE NOT NULL,
    created_at timestamptz DEFAULT '2024-12-15 10:11:12',
    updated_at timestamptz DEFAULT '2024-12-15 10:11:12'
);

-- Comments table to store both top-level comments and replies
CREATE TABLE comments (
    id SERIAL PRIMARY KEY,
    post_id INTEGER REFERENCES posts(id) ON DELETE CASCADE,
    user_id INTEGER REFERENCES users(id) ON DELETE CASCADE,
    parent_comment_id INTEGER REFERENCES comments(id) ON DELETE CASCADE,
    content TEXT NOT NULL,
    created_at timestamptz DEFAULT '2024-12-15 10:11:12',
    updated_at timestamptz DEFAULT '2024-12-15 10:11:12',
    -- Ensure comments can only be nested one level deep
    CONSTRAINT valid_comment_hierarchy CHECK (
        (parent_comment_id IS NULL) OR
        (parent_comment_id IN (SELECT id FROM comments WHERE parent_comment_id IS NULL))
    )
);

-- Indexes for better query performance
CREATE INDEX idx_posts_user_id ON posts(user_id);
CREATE INDEX idx_comments_post_id ON comments(post_id);
CREATE INDEX idx_comments_user_id ON comments(user_id);
CREATE INDEX idx_comments_parent_id ON comments(parent_comment_id);
CREATE INDEX idx_relationships_user_id ON relationships(user_id);
CREATE INDEX idx_relationships_follower_id ON relationships(follower_id);
```
