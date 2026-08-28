FROM alpine:3.18

# Install essential tools for cryptography lab
RUN apk update && apk add --no-cache \
    # Web server
    nginx \
    \
    # Cryptography tools (openssl already pulls in libssl3/libcrypto3)
    openssl \
    gnupg \
    \
    # Hash cracking (hashcat has no Alpine package; john covers challenges 1-2)
    john \
    \
    # Languages & scripting
    python3 \
    py3-pip \
    bash \
    curl \
    wget \
    \
    # Utils
    xxd \
    binutils \
    coreutils \
    nano \
    vim \
    less \
    && rm -rf /var/cache/apk/*

# Install Python cryptography + factorization libraries
RUN pip install --no-cache-dir pycryptodome sympy

# Setup nginx
RUN mkdir -p /run/nginx /var/www/html

COPY nginx.conf /etc/nginx/nginx.conf
COPY index.html /var/www/html/index.html
COPY challenges/ /var/www/html/challenges/

# Create lab scripts directory
RUN mkdir -p /opt/lab

# Expose port
EXPOSE 80

# Start nginx
CMD ["nginx", "-g", "daemon off;"]
