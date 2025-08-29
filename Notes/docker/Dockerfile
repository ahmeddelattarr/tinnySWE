# base img
FROM python:3.11-slim

# 2) Faster, cleaner Python
ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

WORKDIR /app

# 4) Install deps first (layer caching)
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 8000

CMD ["uvicorn", "helloworld:app", "--host", "0.0.0.0", "--port", "8000"]