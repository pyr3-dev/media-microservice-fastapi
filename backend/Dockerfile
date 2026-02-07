FROM python:3.12-slim

# System deps
RUN apt-get update && apt-get install -y \
    curl \
    gcc \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Install deps first (better cache)
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Prisma
RUN pip install prisma
RUN prisma generate

# App code
COPY . .

EXPOSE 8000

# Dev-friendly default
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000", "--reload"]
