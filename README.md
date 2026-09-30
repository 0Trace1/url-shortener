# URL Shortener

A small Flask-based URL shortener service backed by PostgreSQL for persistence and Redis for caching and rate limiting.

## Features

- Create short URLs from long URLs
- Redirect short URLs to their original targets
- Cache redirect lookups in Redis for faster responses
- Persist URLs in PostgreSQL
- Basic per-IP rate limiting
- Docker Compose setup for local development

## Tech Stack

- Python 3.12
- Flask
- PostgreSQL 16
- Redis 7
- Docker and Docker Compose

## Project Structure

```text
.
├── app/
│   ├── app.py
│   ├── Dockerfile
│   ├── init.sql
│   └── requirements.txt
├── docker-compose.yml
├── README.md
└── .gitignore
```

## Quick Start

From the project root, start the application:

```bash
docker compose up --build
```

This will build and start:

- the Flask web app on http://localhost:5000
- PostgreSQL on the default Compose network
- Redis on the default Compose network

## API Endpoints

### POST /shorten

Creates a short URL from a long URL.

Request body:

```json
{
  "url": "https://example.com/very/long/path"
}
```

Example response:

```json
{
  "short_url": "http://localhost:5000/abc123",
  "short_code": "abc123"
}
```

### GET /<short_code>

Redirects the short code to the original URL.

### GET /health

Returns service health status.

Example response:

```json
{
  "status": "ok"
}
```

## Environment Variables

The app accepts the following environment variables via Docker Compose:

- `REDIS_HOST` (default: `redis`)
- `REDIS_PORT` (default: `6379`)
- `POSTGRES_HOST` (default: `db`)
- `POSTGRES_DB` (default: `urlshortener`)
- `POSTGRES_USER` (default: `postgres`)
- `POSTGRES_PASSWORD` (default: `postgres`)

## Database Schema

The app creates the following table automatically through the SQL init script:

```sql
CREATE TABLE IF NOT EXISTS urls (
    id SERIAL PRIMARY KEY,
    long_url TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

## Notes

- The app uses Redis both as a cache and for request throttling.
- The request rate limit defaults to 10 requests per 60 seconds per client IP.
- The app listens on port 5000 inside the container and is exposed to the host as `5000:5000`.
