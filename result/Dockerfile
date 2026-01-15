FROM node:16-buster

# Update apt sources to use archived repositories
RUN sed -i 's/http:\/\/deb.debian.org/http:\/\/archive.debian.org/g' /etc/apt/sources.list \
    && sed -i 's/http:\/\/security.debian.org/http:\/\/archive.debian.org/g' /etc/apt/sources.list \
    && apt-get update \
    && apt-get install -y --no-install-recommends \
    curl \
    && rm -rf /var/lib/apt/lists/*

# Add Tini for proper init of signals
ENV TINI_VERSION v0.19.0
ADD https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini /tini
RUN chmod +x /tini

# Set working directory
WORKDIR /app

# Install nodemon for local dev use (file watching)
RUN npm install -g nodemon

# Copy package.json and package-lock.json first for better Docker cache usage
COPY package*.json ./

# Install dependencies
RUN npm ci \
    && npm cache clean --force \
    && mv /app/node_modules /node_modules

# Copy all other application files
COPY . .

# Set environment variable for port
ENV PORT 80

# Expose the port
EXPOSE 80

# Use Tini as init system and run the Node.js app
CMD ["/tini", "--", "node", "server.js"]
