#!/bin/bash

# Update package list and install dependencies
echo "Updating package list..."
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common

# Add Docker's GPG key
echo "Adding Docker GPG key..."
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

# Add Docker repository
echo "Adding Docker repository..."
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

# Install Docker
echo "Installing Docker..."
sudo apt-get update
sudo apt-get install -y docker-ce

# Install Docker Compose
echo "Installing Docker Compose..."
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

# Create directory for Docker Chromium
echo "Creating directory for Docker Chromium..."
mkdir -p ~/docker-chromium
cd ~/docker-chromium

# Create docker-compose.yml file
cat <<EOL > docker-compose.yml
version: '2'

services:
  chromium:
    image: linuxserver/chromium
    container_name: chromium
    environment:
      - PUID=1000 # Ganti dengan UID pengguna Anda
      - PGID=1000 # Ganti dengan GID pengguna Anda
      - DISPLAY=:99
    volumes:
      - ./config:/config
    ports:
      - 9222:9222
    cap_add:
      - SYS_ADMIN
    restart: unless-stopped
EOL

# Start Docker Compose
echo "Starting Docker Chromium..."
docker-compose up -d

# Display status
echo "Docker Chromium is now running."
echo "You can access it at http://<your-vps-ip>:9222"
