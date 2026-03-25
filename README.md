# Secrets Project

An Express + Passport app that lets users register/login and save a personal “secret” in PostgreSQL (local auth + Google OAuth). Pages are rendered with EJS.

## Features

- Local registration and login (email + password, bcrypt-hashed)
- Google OAuth login
- Authenticated secret viewing (`/secrets`)
- Authenticated secret submission (`/submit`)

## Tech Stack

- Node.js / Express
- Passport.js (local strategy + Google OAuth)
- PostgreSQL (`pg`)
- EJS templates
- `express-session` for session management

## Prerequisites

- Node.js (use a current LTS version)
- PostgreSQL running locally (or update the connection settings)
- (Optional) Google OAuth credentials

## Environment Variables

Create a `.env` file (this repo includes a sample `.env` you can copy/update). Required variables:

- `SESSION_SECRET` (string): session signing secret
- `PG_USER`, `PG_HOST`, `PG_DATABASE`, `PG_PASSWORD`, `PG_PORT`: PostgreSQL connection settings
- `GOOGLE_CLIENT_ID`, `GOOGLE_CLIENT_SECRET`: Google OAuth credentials

Keep `.env` out of version control (this repo’s `.gitignore` already ignores it).

The Google OAuth callback URL used by the app is:

`http://localhost:3000/auth/google/secrets`

Make sure this exact URL is configured in your Google Cloud project.

## Database Setup

The app expects a `users` table with at least:

- `email` (used as the unique identifier)
- `password` (stored bcrypt hash for local users)
- `secret` (added by `queries.sql`)

This repo’s `queries.sql` currently contains:

```sql
ALTER TABLE users ADD COLUMN secret TEXT;
```

If your base `users` table does not already exist, create it first (adjust to your needs):

```sql
CREATE TABLE users (
  email TEXT PRIMARY KEY,
  password TEXT NOT NULL
);
```

Then run `queries.sql` to add the `secret` column.

## Install & Run

1. Install dependencies:

```bash
npm install
```

2. Update `.env` with your settings.
3. Start the server:

```bash
npm run dev
```

4. Open the app in your browser:

`http://localhost:3000`

## Routes (High Level)

- `GET /` Home page
- `GET /login` Login page
- `POST /login` Local Passport authentication
- `GET /register` Registration page
- `POST /register` Create a local user (bcrypt password hash)
- `GET /auth/google` Start Google OAuth
- `GET /auth/google/secrets` Google OAuth callback
- `GET /secrets` View the authenticated user’s secret
- `GET /submit` Submit a new secret
- `POST /submit` Save the secret to the authenticated user
- `GET /logout` Log out

## Project Structure

- `index.js`: Express app + Passport strategies + route handlers
- `views/`: EJS templates (`home.ejs`, `login.ejs`, `register.ejs`, `secrets.ejs`, `submit.ejs`)
- `public/`: Static assets (CSS)
- `queries.sql`: SQL migration for adding the `secret` column

## Notes / Production Caveats

- `express-session` defaults to an in-memory store, which is not suitable for production deployments with multiple instances.
- There is no CSRF protection in place.
- Google-created users are inserted with a placeholder value for `password`; local login for those users is not guaranteed to work.

