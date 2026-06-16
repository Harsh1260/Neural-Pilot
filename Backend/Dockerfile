# Use an official Python runtime as a parent image
FROM python:3.10-slim

# Set environment variables
ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PORT=7860 \
    HOME=/home/user \
    KAGGLE_CONFIG_DIR=/home/user/.kaggle

# Install system dependencies
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        build-essential \
        gcc \
    && rm -rf /var/lib/apt/lists/*

# Hugging Face Spaces require running as a non-root user.
# Create a user 'user' with UID 1000.
RUN useradd -m -u 1000 user

# Switch to the new user
USER user

# Set the working directory
WORKDIR $HOME/app

# Ensure local python bin is in PATH
ENV PATH="$HOME/.local/bin:$PATH"

# Copy requirements and install them as the non-root user
COPY --chown=user:user requirements.txt $HOME/app/
RUN pip install --upgrade pip && \
    pip install --no-cache-dir -r requirements.txt

# Copy the rest of the application files with proper ownership
COPY --chown=user:user . $HOME/app/

# Create necessary directories for media/uploads if they don't exist
# and ensure the user owns them
RUN mkdir -p $HOME/app/media $HOME/app/uploads $HOME/app/exports $HOME/app/cache

# Hugging Face Spaces expose port 7860
EXPOSE 7860

# Start the Django application on port 7860
# (Using runserver for simplicity, but for production consider adding gunicorn to requirements.txt and using it)
CMD ["python", "manage.py", "runserver", "0.0.0.0:7860"]
