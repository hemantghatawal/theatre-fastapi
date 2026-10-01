# Theatre Reviews API

A FastAPI-based REST API for managing theatre play reviews, built with SQLModel and SQLite.

## Features

- Create, read, update, and delete theatre reviews
- Filter reviews by play name with pagination support
- Calculate average rating and total reviews for any play
- Input validation for ratings (1-5 range)
- Auto-generated API documentation (Swagger UI / ReDoc)

## Tech Stack

- **Framework:** [FastAPI](https://fastapi.tiangolo.com/)
- **ORM:** [SQLModel](https://sqlmodel.tiangolo.com/) (SQLAlchemy + Pydantic)
- **Database:** SQLite (`theatre.db`)
- **Server:** Uvicorn

## Project Structure

```
theatre-fastapi/
├── main.py              # FastAPI app entry point, lifespan management
├── database.py          # Database engine, session, and table creation
├── models.py            # SQLModel schemas (Review, ReviewCreate, ReviewRead, ReviewUpdate)
├── routes/
│   └── reviews.py       # Review CRUD endpoints
├── requirements.txt     # Python dependencies
└── theatre.db           # SQLite database (auto-created on startup)
```

## Installation

1. **Clone / Navigate to project:**

   ```bash
   cd theatre-fastapi
   ```

2. **Create and activate virtual environment:**

   ```bash
   python3 -m venv venv
   source venv/bin/activate
   ```

3. **Install dependencies:**
   ```bash
   pip install -r requirements.txt
   ```

## Running the Server

```bash
uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

- `--reload` — auto-reload on code changes (development only)
- The database tables are created automatically on startup via the FastAPI lifespan event.

## API Documentation

Once the server is running, visit:

- **Swagger UI (interactive):** http://localhost:8000/docs
- **ReDoc (alternative):** http://localhost:8000/redoc

## Endpoints

| Method   | Endpoint                      | Description                                   |
| -------- | ----------------------------- | --------------------------------------------- |
| `POST`   | `/review/`                    | Create a new review                           |
| `GET`    | `/review/`                    | List reviews (filter by play_name + paginate) |
| `GET`    | `/review/{review_id}`         | Get a single review by ID                     |
| `PATCH`  | `/review/{review_id}`         | Update a review's rating / comment            |
| `DELETE` | `/review/{review_id}`         | Delete a review by ID                         |
| `GET`    | `/review/average/{play_name}` | Get avg rating + total reviews for a play     |

## Example Requests

### Create a Review

```bash
curl -X POST http://localhost:8000/review/ \
  -H "Content-Type: application/json" \
  -d '{
    "play_name": "Hamlet",
    "reviewer_name": "John Doe",
    "rating": 5,
    "comment": "Excellent performance!"
  }'
```

**Response:**

```json
{
  "id": 1,
  "play_name": "Hamlet",
  "reviewer_name": "John Doe",
  "rating": 5,
  "comment": "Excellent performance!",
  "created_at": "2026-10-01T12:00:00+00:00"
}
```

### Get Average Rating for a Play

```bash
curl http://localhost:8000/review/average/Hamlet
```

**Response:**

```json
{
  "play_name": "Hamlet",
  "average_rating": 4.5,
  "total_reviews": 2
}
```

### List Reviews (Paginated)

```bash
curl "http://localhost:8000/review/?play_name=Hamlet&skip=0&limit=10"
```

### Update a Review

```bash
curl -X PATCH http://localhost:8000/review/1 \
  -H "Content-Type: application/json" \
  -d '{"rating": 4, "comment": "Updated comment"}'
```

### Delete a Review

```bash
curl -X DELETE http://localhost:8000/review/1
```

## Model Schema

### Review (Database Table)

| Field           | Type     | Constraints             |
| --------------- | -------- | ----------------------- |
| `id`            | int      | Primary key (auto)      |
| `play_name`     | str      | Indexed                 |
| `reviewer_name` | str      | Required                |
| `rating`        | int      | Between 1 and 5         |
| `comment`       | str      | Required                |
| `created_at`    | datetime | Auto-set (UTC timezone) |
