## Docker compose only for dev or test env

### Why don't use docker compose in production env
- it's only work on a single server (engine) host
- doesn't provide correctly uptim or update or helthchecks
- Swarm is easy and solve most problems
- Swarm is better for small amount of servers
- Kuber is better for large amount of servers (Always check first if it's better to use cloud Kuber)


```sh
version: '3.8'
services:
  blogo:
    container_name: blogo
    build: .
    ports:
      - "80:80"
    networks:
      - blogo
    depends_on:
      - postgres
    environment:
      JWT_SECRET: ${JWT_SECRET}
      REFRESH_TOKEN_SECRET: ${REFRESH_TOKEN_SECRET}
      PG_PASS: ${PG_PASS}
      PG_USER: ${PG_USER}
      DB_NAME: ${DB_NAME}
      HOST: "postgres"
  postgres:
    container_name: postgres
    image: postgres:10.20-alpine3.15
    restart: unless-stopped
    ports:
      - 5432:5432
    volumes:
      - ./storage/pgdata:/var/lib/postgresql/data
    networks:
      - blogo
    environment:
      POSTGRES_PASSWORD: ${PG_PASS}
      POSTGRES_USER: ${PG_USER}
      POSTGRES_DB: ${DB_NAME}

networks:
  blogo:
    driver: bridge

# Source file: https://github.com/arshamalh/blogo/blob/master/
```



