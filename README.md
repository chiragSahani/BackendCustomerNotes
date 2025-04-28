# Custom Notes Service

A simple notes service built with Supabase that allows authenticated users to create and retrieve their personal notes.

## Schema Design

The database uses a single `notes` table with the following structure:

```sql
CREATE TABLE notes (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL,
  title TEXT NOT NULL,
  content TEXT NOT NULL,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT now(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT now()
);

### Why these design choices?

- **UUID as primary key**: Provides globally unique identifiers without revealing sequential information, better for distributed systems and security.
- **user_id as UUID**: Directly maps to Supabase Auth user IDs for seamless integration.
- **TEXT for content fields**: Allows for variable-length content without arbitrary limitations.
- **TIMESTAMP WITH TIME ZONE**: Ensures consistent time representation across different timezones.
- **DEFAULT gen_random_uuid()**: Automatically generates UUIDs without client-side logic.
- **DEFAULT now()**: Automatically sets creation and update timestamps.


## Setup Instructions

### 1. Create a Supabase Project

1. Go to [Supabase](https://supabase.com) and create a new project
2. Note your project URL and anon key for later use


### 2. Set Up the Database

1. Navigate to the SQL Editor in your Supabase dashboard
2. Copy and paste the contents of `schema.sql` into the SQL Editor
3. Run the SQL to create the table and set up Row Level Security


### 3. Deploy Edge Functions

1. Install the Supabase CLI if you haven't already:

```shellscript
npm install -g supabase
```


2. Login to Supabase CLI:

```shellscript
supabase login
```


3. Link your project:

```shellscript
supabase link --project-ref your-project-id
```


4. Deploy the functions:

```shellscript
supabase functions deploy post_notes
supabase functions deploy get_notes
```




### 4. Set Environment Variables

In the Supabase dashboard, navigate to Settings > API and set the following environment variables for your functions:

- `SUPABASE_URL`: Your Supabase project URL
- `SUPABASE_ANON_KEY`: Your Supabase project anon key


## Testing the API

### Create a Note (POST /notes)

```shellscript
curl -X POST 'https://your-project-id.supabase.co/functions/v1/post_notes' \
  -H 'Authorization: Bearer YOUR_JWT_TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{"title": "My First Note", "content": "This is the content of my first note."}'
```

Sample output:

```json
{
  "success": true,
  "note": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "user_id": "auth-user-id",
    "title": "My First Note",
    "content": "This is the content of my first note.",
    "created_at": "2023-04-28T09:45:27.456Z",
    "updated_at": "2023-04-28T09:45:27.456Z"
  }
}
```

### Get Notes (GET /notes)

```shellscript
curl 'https://your-project-id.supabase.co/functions/v1/get_notes' \
  -H 'Authorization: Bearer YOUR_JWT_TOKEN'
```

Sample output:

```json
{
  "notes": [
    {
      "id": "550e8400-e29b-41d4-a716-446655440000",
      "user_id": "auth-user-id",
      "title": "My First Note",
      "content": "This is the content of my first note.",
      "created_at": "2023-04-28T09:45:27.456Z",
      "updated_at": "2023-04-28T09:45:27.456Z"
    }
  ]
}
```

## Edge Function Design

### POST /notes (post_notes.js)

**Why?** POST is the appropriate HTTP method for creating resources. The URL path is simple and follows REST conventions. Parameters are read from the request body as it's the standard approach for sending data in POST requests.

### GET /notes (get_notes.js)

**Why?** GET is the appropriate HTTP method for retrieving resources. The simple URL path follows REST conventions. Query parameters are used for filtering and pagination, which is standard for GET requests.

```plaintext

This project provides a complete, production-ready notes service with Supabase. The code is designed to be simple, secure, and follows best practices for Supabase Edge Functions and database design.

<Actions>
  <Action name="Add update_note function" description="Create an Edge Function to update existing notes" />
  <Action name="Add delete_note function" description="Create an Edge Function to delete notes" />
  <Action name="Add tags functionality" description="Extend the schema to support tagging notes" />
  <Action name="Add search functionality" description="Implement full-text search for notes content" />
  <Action name="Add note sharing" description="Create functionality to share notes with other users" />
</Actions>


This  may require the following integrations: 
<AddIntegration names={["supabase"]} />


```