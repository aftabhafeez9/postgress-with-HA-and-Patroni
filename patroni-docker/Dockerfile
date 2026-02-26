FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive

# Install dependencies
RUN apt-get update && apt-get install -y \
    curl \
    gnupg \
    lsb-release \
    python3 \
    python3-pip \
    && rm -rf /var/lib/apt/lists/*

# Add official PostgreSQL apt repository
RUN curl -fsSL https://www.postgresql.org/media/keys/ACCC4CF8.asc | gpg --dearmor -o /etc/apt/trusted.gpg.d/postgresql.gpg \
    && echo "deb http://apt.postgresql.org/pub/repos/apt jammy-pgdg main" > /etc/apt/sources.list.d/pgdg.list

# Install PostgreSQL 16
RUN apt-get update && apt-get install -y \
    postgresql-16 \
    && rm -rf /var/lib/apt/lists/*

# Install Patroni with etcd support
RUN pip3 install patroni[etcd3] psycopg2-binary

# Create data directory
RUN mkdir -p /data/patroni && chown -R postgres:postgres /data/patroni && chmod 700 /data/patroni


# Copy Patroni config
COPY patroni.yml /etc/patroni.yml

USER postgres

CMD ["patroni", "/etc/patroni.yml"]