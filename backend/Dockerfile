# Builder stage
FROM python:3.11 AS builder

ENV PATH="/opt/venv/bin:$PATH"
RUN python -m venv /opt/venv

# Debug: List files in the build context
RUN echo "Debug: Listing files in build context" && ls -la / && pwd

COPY requirements.txt /tmp/requirements.txt
RUN pip install --no-cache-dir --upgrade -r /tmp/requirements.txt

# Final stage
FROM python:3.11-slim

WORKDIR /app
ENV PATH="/opt/venv/bin:$PATH"

RUN apt-get update && apt-get -y install ffmpeg curl unzip && rm -rf /var/lib/apt/lists/*

COPY --from=builder /opt/venv /opt/venv

# Debug: List files in the build context before COPY . .
RUN echo "Debug: Listing files in build context" && ls -la / && pwd

COPY . .

# Debug: List files in /app after COPY . .
RUN echo "Debug: Listing files in /app after COPY" && ls -la /app

EXPOSE 8080
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8080"]
