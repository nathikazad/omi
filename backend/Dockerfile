# Builder stage
FROM python:3.11 AS builder

ENV PATH="/opt/venv/bin:$PATH"
RUN python -m venv /opt/venv

# Copy the build context to a temporary directory and list its contents
COPY . /tmp/build-context
RUN echo "Debug: Listing files in build context" && ls -la /tmp/build-context && pwd

# Decode and create google-credentials.json from environment variable with debugging
RUN echo "Debug: First 100 chars of GOOGLE_CREDENTIALS_BASE64: $(echo $GOOGLE_CREDENTIALS_BASE64 | head -c 100)" && \
    echo "Debug: Decoded first 100 chars: $(echo $GOOGLE_CREDENTIALS_BASE64 | base64 -d | head -c 100)" && \
    echo $GOOGLE_CREDENTIALS_BASE64 | base64 -d > /tmp/google-credentials.json
# Debug: Check if the credentials file is non-empty
RUN echo "Debug: Size of google-credentials.json: $(wc -c < /tmp/google-credentials.json) bytes" && ls -la /tmp/google-credentials.json


COPY requirements.txt /tmp/requirements.txt
RUN pip install --no-cache-dir --upgrade -r /tmp/requirements.txt

# Final stage
FROM python:3.11-slim

WORKDIR /app
ENV PATH="/opt/venv/bin:$PATH"

RUN apt-get update && apt-get -y install ffmpeg curl unzip && rm -rf /var/lib/apt/lists/*

COPY --from=builder /opt/venv /opt/venv
COPY --from=builder /tmp/google-credentials.json /app/google-credentials.json

# Copy the build context to /app and list its contents
COPY . /app
RUN echo "Debug: Listing files in /app after COPY" && ls -la /app

EXPOSE 8080
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8080"]
