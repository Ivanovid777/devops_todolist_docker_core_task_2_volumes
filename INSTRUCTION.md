# Repository
https://hub.docker.com/repository/docker/ivanoid777/todoapp/general
https://hub.docker.com/repository/docker/ivanoid777/mysql-local/general
# -------- Dockerfile --------

# Builder
ARG PYTHON_VERSION=3.9

FROM python:${PYTHON_VERSION} AS builder
LABEL authors="Ivan"
WORKDIR /todoapp
COPY . .

# run stage
FROM python:${PYTHON_VERSION}-slim
ENV PYTHONUNBUFFERED=1
WORKDIR /todoapp
COPY --from=builder /todoapp .
RUN pip install --upgrade pip && \
    pip install -r requirements.txt && \
    python manage.py migrate

CMD ["python", "manage.py", "runserver", "0.0.0.0:8080"]

# -------- Dockerfile.sql --------
FROM mysql:latest

ENV MYSQL_ROOT_PASSWORD=toor
ENV MYSQL_DATABASE=app_db
ENV MYSQL_USER=app_user
ENV MYSQL_PASSWORD=1234

EXPOSE 3306
VOLUME /var/lib/mysq

# -------- How to build and run --------

# 1. Build image from Dockerfile.mysql
docker build -t <tag> -f Dockerfile.mysql .

# 2. Run mysql container
docker run --name <name> -v <path>>:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=<root_password> -d ivanoid777/mysql-local:2.0.0

# 3. Build image from Dockerfile
docker build -t <tag> .

# 4. Run app container 
docker run --name <name> -d -p <host_port>:<container_port> ivanoid777/todoapp:2.0.0

# 3. Open in browser
http://127.0.0.1:<host_port>