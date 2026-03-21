---
name: fastapi-python
description: "Use when: building a FastAPI backend, setting up pydantic-settings config, configuring SQLAlchemy 2 async with PostgreSQL, writing Pydantic schemas, setting up APScheduler for cron jobs, or writing Alembic migrations."
---

# FastAPI + Python Backend

> Used by: @app-builder, @data-engineer

## Project Structure

```
backend/
├── app/
│   ├── main.py              -- FastAPI app factory + lifespan
│   ├── config.py            -- pydantic-settings Config
│   ├── database.py          -- SQLAlchemy async engine + session
│   ├── models.py            -- SQLAlchemy ORM models
│   ├── routers/             -- One file per domain
│   │   ├── posts.py
│   │   ├── admin.py
│   │   └── health.py
│   ├── services/            -- Business logic (no HTTP)
│   ├── schemas/             -- Pydantic request/response models
│   └── lib/                 -- Helpers (xai client, etc.)
├── alembic/
│   ├── env.py
│   └── versions/
├── alembic.ini
└── requirements.txt
```

## App Factory (main.py)

```python
from contextlib import asynccontextmanager
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
from app.routers import posts, admin, health
from app.database import init_db

@asynccontextmanager
async def lifespan(app: FastAPI):
    await init_db()
    yield

app = FastAPI(title="My App", version="1.0.0", lifespan=lifespan)

app.add_middleware(
    CORSMiddleware,
    allow_origins=["https://your-frontend.railway.app", "http://localhost:3003"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

app.include_router(health.router)
app.include_router(posts.router, prefix="/api")
app.include_router(admin.router, prefix="/api/admin")
```

## Config (pydantic-settings)

```python
# app/config.py
from pydantic_settings import BaseSettings, SettingsConfigDict

class Settings(BaseSettings):
    model_config = SettingsConfigDict(env_file=".env", extra="ignore")
    database_url: str
    xai_api_key: str
    admin_password: str
    port: int = 8000

settings = Settings()
```

## Database (SQLAlchemy async)

```python
# app/database.py
from sqlalchemy.ext.asyncio import create_async_engine, async_sessionmaker, AsyncSession
from sqlalchemy.orm import DeclarativeBase
from app.config import settings

DATABASE_URL = settings.database_url.replace("postgresql://", "postgresql+asyncpg://", 1)
engine = create_async_engine(DATABASE_URL, echo=False, pool_pre_ping=True)
AsyncSessionLocal = async_sessionmaker(engine, expire_on_commit=False)

class Base(DeclarativeBase):
    pass

async def init_db():
    async with engine.begin() as conn:
        await conn.run_sync(Base.metadata.create_all)

async def get_db():
    async with AsyncSessionLocal() as session:
        yield session
```

## Router Pattern

```python
# app/routers/posts.py
from fastapi import APIRouter, Depends, HTTPException
from sqlalchemy.ext.asyncio import AsyncSession
from sqlalchemy import select
from app.database import get_db
from app.models import Post
from app.schemas.post import PostResponse

router = APIRouter()

@router.get("/posts", response_model=list[PostResponse])
async def get_posts(db: AsyncSession = Depends(get_db)):
    result = await db.execute(select(Post).order_by(Post.created_at.desc()).limit(50))
    return result.scalars().all()
```

## APScheduler (daily jobs — matches ai_pulse)

```python
# app/scheduler.py
from apscheduler.schedulers.asyncio import AsyncIOScheduler
from app.config import settings

def setup_scheduler() -> AsyncIOScheduler:
    scheduler = AsyncIOScheduler(timezone=settings.timezone)
    scheduler.add_job(
        daily_refresh_job,
        trigger='cron',
        hour=settings.refresh_hour,
        minute=0,
    )
    return scheduler
```

## Alembic Setup

```bash
alembic init alembic
# Edit alembic/env.py to use async engine + import Base from app.database
alembic revision --autogenerate -m "add posts table"
alembic upgrade head
```

## Admin Endpoint Pattern

```python
# app/routers/admin.py
from fastapi import APIRouter, Header, HTTPException
from app.config import settings

router = APIRouter()

@router.post("/refresh")
async def manual_refresh(x_admin_password: str = Header(...)):
    if x_admin_password != settings.admin_password:
        raise HTTPException(status_code=401, detail="Unauthorized")
    # trigger refresh...
    return {"ok": True, "message": "refresh triggered"}
```
