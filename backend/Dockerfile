FROM python:3.13-alpine AS builder

WORKDIR /app

COPY --from=ghcr.io/astral-sh/uv:0.9.15 /uv /usr/local/bin/uv

COPY pyproject.toml ./

RUN uv pip install --system --no-cache -e .

FROM python:3.13-alpine

WORKDIR /app

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

COPY --from=builder /usr/local/lib/python3.13/site-packages /usr/local/lib/python3.13/site-packages
COPY --from=builder /usr/local/bin /usr/local/bin

RUN addgroup -g 1000 effective && \
    adduser -D -u 1000 -G effective effective

COPY backend/app.py /app/app.py

RUN chown -R effective:effective /app

USER effective

EXPOSE 8080

CMD ["gunicorn", \
    "--bind", "0.0.0.0:8080", \
    "--workers", "4", "--timeout", "120", \
    "app:app"]
