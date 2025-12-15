---
title: Migrating Grafana SQL DB to PostgresDB
description: This docker compose was used during my migration of the grafana sqlite db to postgres. 
date: 2024-06-04T13:09:00+10:00
categories:
    - Wiki
tags:
    - IT
    - Home Automation
image: /imgs/grafana-dashboard.png
---

## Overview
This setup migrates a Grafana SQLite database to PostgreSQL for improved performance and high availability. The process involves:
1. Spinning up a new Grafana container connected to PostgreSQL (which auto-creates the schema)
2. Using pgloader to migrate data from the old SQLite database to PostgreSQL

## Migration Process

### Step 1: Set up PostgreSQL
Deploy a PostgreSQL instance to host the Grafana database. The database will be automatically initialised when you first connect Grafana to it.

### Step 2: Initialize Grafana Schema
Point a temporary Grafana container at your new PostgreSQL instance. This creates the necessary schema with an empty database.

### Step 3: Migrate Data with pgloader
Use the pgloader container to migrate your old SQLite database into PostgreSQL. Example pgloader configuration:

```yaml
# pgloader service (optional for migration)
pgloader:
  image: dimitri/pgloader:latest
  container_name: pgloader
  entrypoint: pgloader
  command: /data/db.load
  volumes:
    - /path/to/pgloader/data:/data  # Mount your db.load config and SQLite DB here
```

Copy your SQLite database file into the pgloader volume mount, create a `db.load` configuration file, and run the migration.

## References
- [Migrate Grafana SQLite DB to Postgresql for High Availability](https://medium.com/@yashraj45dighe/migrate-grafana-sqlite-db-to-postgresql-for-high-availability-c7303c3750ff#:~:text=Migration%20Steps%3A&text=Backup%20the%20schemas%20of%20all,database%20with%20the%20same%20name.)
- [Grafana SQLite Migration to PostgreSQL](https://polyglot.jamie.ly/programming/2019/07/01/grafana-sqlite-to-postgres.html)

## Docker Compose Configuration

```yaml
version: '3'

services:
  postgres:
    image: postgres:11
    container_name: grafana-postgres
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: <your-secure-password>
      POSTGRES_DB: grafana
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - grafana-net
    restart: unless-stopped

  grafana:
    image: grafana/grafana:latest
    container_name: grafana
    user: "<your-uid>"  # Change to appropriate user ID for your system
    ports:
      - "3000:3000"
    volumes:
      - grafana_data:/var/lib/grafana
      - grafana_logs:/var/log/grafana
      - ./grafana.ini:/etc/grafana/grafana.ini:ro
    environment:
      GF_DATABASE_TYPE: postgres
      GF_DATABASE_HOST: postgres
      GF_DATABASE_USER: postgres
      GF_DATABASE_PASSWORD: <your-secure-password>
      GF_DATABASE_NAME: grafana
    depends_on:
      - postgres
    networks:
      - grafana-net
    restart: unless-stopped

volumes:
  postgres_data:
  grafana_data:
  grafana_logs:

networks:
  grafana-net:
    driver: bridge
  
```