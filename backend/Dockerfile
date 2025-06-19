FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive

# Install system dependencies
RUN apt-get update && apt-get install -y \
    software-properties-common \
    curl \
    git \
    build-essential \
    libssl-dev \
    libffi-dev \
    libmysqlclient-dev \
    default-libmysqlclient-dev \
    python3.8 \
    python3.8-dev \
    python3.8-venv \
    && rm -rf /var/lib/apt/lists/*

# Set working directory
WORKDIR /app

# Clone your project repository (optional if using Docker Compose to mount context)
RUN git https://github.com/Shubhradip-Saha/chatapp-modified-ubuntu-22.04.git .

# Set up Python virtual environment
RUN python3.8 -m venv venv

# Install Python dependencies
RUN ./venv/bin/pip install --upgrade pip && \
    ./venv/bin/pip install -r requirements.txt && \
    ./venv/bin/pip install python-dotenv mysqlclient

# Activate virtualenv for subsequent commands
ENV PATH="/app/venv/bin:$PATH"

# Expose Django server port
EXPOSE 8000

# Start the app
CMD ["bash", "-c", "python3 fundoo/manage.py migrate && python3 fundoo/manage.py runserver 0.0.0.0:8000"]

