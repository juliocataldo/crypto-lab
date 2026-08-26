FROM alpine:3.18

# Install essential tools for cryptography lab
RUN apk update && apk add --no-cache \
    # Web server
    nginx \
    \
    # Cryptography tools
    openssl \
    openssl-libs \
    gnupg \
    \
    # Hash cracking
    john \
    hashcat \
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

# Install Python cryptography library
RUN pip install --no-cache-dir pycryptodome

# Setup nginx
RUN mkdir -p /run/nginx /var/www/html

COPY nginx.conf /etc/nginx/nginx.conf
COPY index.html /var/www/html/index.html
COPY challenges/ /var/www/html/challenges/ 2>/dev/null || true

# Create lab scripts directory
RUN mkdir -p /opt/lab

COPY gera_chave_privada.py /opt/lab/gera_chave_privada.py 2>/dev/null || true
COPY desafios.sh /opt/lab/desafios.sh 2>/dev/null || true
RUN chmod +x /opt/lab/*.sh 2>/dev/null || true

# Expose port
EXPOSE 80

# Start nginx
CMD ["nginx", "-g", "daemon off;"]
