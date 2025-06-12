# 🔗 Shortx - URL Shortener API

A fast and simple URL shortener backend built with [Hono.js](https://hono.dev/) and [Supabase](https://supabase.com/). This API generates short URLs, tracks them per user, and supports redirection.

---

## 🚀 Features

- Shorten long URLs to unique short URLs.
- Redirect using the short URL.
- Track usage by user email.
- Built-in endpoint list and health check.

---

## 📦 Tech Stack

- **Hono.js** – Lightweight and fast web framework.
- **Supabase** – Realtime Postgres database.
- **Nanoid** – Generates secure, short IDs.

---

## 📁 Project Structure

```
server/
├── api/
│   ├── index.js  # Main Hono server
│   └── db.js     # Supabase client setup
├── .env          # Environment variables
```

---

## ⚙️ Setup Instructions

### 1. Clone the Repository

```bash
git clone https://github.com/your-username/shortx-url-shortener.git
cd shortx-url-shortener
```

### 2. Install Dependencies

```bash
npm install
```

### 3. Configure Environment Variables

Create a `.env` file in the root directory:

```env
SUPABASE_URL=https://your-project-id.supabase.co
SUPABASE_ANON_KEY=your-anon-key
```

Get these from your Supabase dashboard under **Project Settings → API**.

### 4. Supabase Database Schema

Create the following tables manually in Supabase:

**`users` table**

| Column    | Type    |
|-----------|---------|
| id        | uuid    |
| email     | text    |
| urlCount  | integer |

Add a sample user:

```sql
INSERT INTO users (id, email, urlCount)
VALUES (gen_random_uuid(), 'tester@gmail.com', 0);
```

**`urls` table**

| Column       | Type  |
|--------------|-------|
| id           | uuid  |
| originalUrl  | text  |
| shortenedUrl | text  |
| shortName    | text  |
| userEmail    | text  |

**Optional: RPC Function**

```sql
CREATE OR REPLACE FUNCTION increment_url_count(user_email text)
RETURNS void AS $$
BEGIN
  UPDATE users
  SET urlCount = COALESCE(urlCount, 0) + 1
  WHERE email = user_email;
END;
$$ LANGUAGE plpgsql;
```

### 5. Start the Server

```bash
npm run dev
```

You should see:

```
🚀 Server listening on http://localhost:3000
```

---

## 🧪 Test API

Before testing, ensure this user exists in the `users` table:

```json
{
  "email": "tester@gmail.com"
}
```

**PowerShell or Terminal Test**:

```powershell
Invoke-RestMethod -Uri "http://localhost:3000/shorten" `
  -Method Post `
  -Body '{"originalUrl": "https://github.com/", "userEmail": "tester@gmail.com"}' `
  -ContentType "application/json"
```

**Example Response**:

```json
{
  "shortUrl": "http://localhost:3000/Ab12Cd",
  "shortName": "Ab12Cd"
}
```

---

## 🌐 Available Endpoints

| Method | Path            | Description                     |
|--------|-----------------|---------------------------------|
| GET    | `/`             | Status message                 |
| GET    | `/health`       | Health check                   |
| POST   | `/shorten`      | Create a new short URL         |
| GET    | `/:shortName`   | Redirect to original URL       |
| GET    | `/endpoints`    | List all available endpoints   |

---

## 📄 License

MIT © [Your Name]
