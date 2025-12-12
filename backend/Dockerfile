# Multi-stage build to reduce final image size
FROM python:3.9-slim as builder

WORKDIR /app

# Install only essential build dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
    gcc \
    g++ \
    && rm -rf /var/lib/apt/lists/*

# Copy requirements
COPY backend/requirements.txt .

# Install Python dependencies without cache
RUN pip install --no-cache-dir --user -r requirements.txt

# Final stage - minimal runtime
FROM python:3.9-slim

WORKDIR /app

# Copy installed packages from builder
COPY --from=builder /root/.local /root/.local

# Add local bin to PATH
ENV PATH=/root/.local/bin:$PATH \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# Copy application code
COPY backend/ .

# Clean up Python cache and unnecessary files
RUN find /root/.local -type d -name __pycache__ -exec rm -rf {} + 2>/dev/null || true && \
    find /root/.local -type f -name "*.pyc" -delete && \
    find /root/.local -type f -name "*.pyo" -delete && \
    find /root/.local -type f -name "*.dist-info" -exec rm -rf {} + 2>/dev/null || true && \
    apt-get autoremove -y && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Expose port
EXPOSE 8000

# Health check removed - Koyeb handles health checks automatically

# Run the application
CMD uvicorn main:app --host 0.0.0.0 --port ${PORT:-8000}
