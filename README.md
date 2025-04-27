
# ITGenius App Mini Docker Project Deployment Guide

Deploy a Spring Boot application connected to a MySQL database using **Docker** on a **single Amazon Lightsail server**.

---
# Project Overview
In this project, you will:
- Provision one Lightsail server.
- Install Docker.
- Clone the app repository.
- Create two Docker images (one for MySQL, one for the ITGenius application).
- Launch both containers.
- Access the application via your browser

---
# Server Setup

## Step 1: Create Lightsail Server
- Go to [AWS Lightsail](https://lightsail.aws.amazon.com/).
- Create a new instance:
  - **OS Only**: CentOS.
  - **Minimum**: 1GB RAM.
- **Networking settings**:
  - Under the "Networking" tab of your Lightsail server,
  - **Allow All Traffic** (All protocols, all ports) temporarily for development purposes.

Now you have one server ready.

---

## Step 2: Install Docker
SSH into your Lightsail server:

[Follow the Confluence documentation to install Docker](https://itgenius-team-u5ijt9rh.atlassian.net/wiki/spaces/documentat/pages/6029363/CentOS)

✅ Docker is now ready.

---

# Application Setup

## Step 3: Clone the GitHub Repository

---

### Step 3A: Clone the Project Repository

Clone the project repository to your Lightsail server:

```bash
git clone https://github.com/itgenius-devops/itgenius-app-mini-docker-project.git
```

---

### Step 2: Navigate into the Project Directory

After cloning, move into the project directory:

```bash
cd itgenius-app-mini-docker-project
```

---

✅ You are now inside the project root.

---

## Step 4: Prepare Directory Structure

Inside the cloned folder, create two directories as seen below:

```bash
mkdir mysql
mkdir itgenius-app
```

Move the application JAR into `itgenius-app/`:

```bash
mv itgenius-0.0.1-SNAPSHOT.jar itgenius-app/
```

✅ Now your structure looks like this:

```
itgenius-app-mini-docker-project/
├── mysql/
├── itgenius-app/
│   └── itgenius-0.0.1-SNAPSHOT.jar
├── .env
```

---

# Dockerfiles and Dependency Files

## Step 5: Create Dockerfile for MySQL

Inside the `mysql/` directory, create a `Dockerfile`:

```Dockerfile
# mysql/Dockerfile
FROM mysql:8.0

COPY init.sql /docker-entrypoint-initdb.d/

EXPOSE 3306
```

Also create `init.sql` inside `mysql/`:

```sql
-- mysql/init.sql
CREATE DATABASE itgenius_app_database;
CREATE USER 'itgenius_app_user'@'%' IDENTIFIED BY 'Databaseuserstrongpassword@123';
GRANT ALL PRIVILEGES ON itgenius_app_database.* TO 'itgenius_app_user'@'%';
FLUSH PRIVILEGES;
```

---

## Step 6: Create Dockerfile for Spring Boot App

Inside the `itgenius-app/` directory, create a `Dockerfile`:

```Dockerfile
# itgenius-app/Dockerfile
FROM amazoncorretto:17

WORKDIR /app

COPY itgenius-0.0.1-SNAPSHOT.jar app.jar

EXPOSE 8085

ENTRYPOINT ["java", "-jar", "app.jar"]
```

✅ Now each component (folder) has its own Dockerfile.

---

# Environment Variables Setup

## Step 7: Create/Edit `.env` File

At the root of the project (`itgenius-app-mini-docker-project/`), create or Edit the `.env` file:

```bash
# .env
DB_URL=jdbc:mysql://mysql:3306/itgenius_app_database
DB_USERNAME=itgenius_app_user
DB_PASSWORD=Databaseuserstrongpassword@123
```

Absolutely — I get exactly what you want:  
You want it **separated** properly, with **cd into each folder**, and then **build inside** — **much cleaner**. 🚀

Here’s the rewritten, polished version:

---

## Step 8: Build Custom Docker Images

---

### Build the MySQL Image

A. Navigate into the `mysql` directory:

```bash
cd mysql
```

B. Build the MySQL image:

```bash
docker build -t my-custom-mysql:8.0 .
```

C. Move back to the root project directory:

```bash
cd ..
```

---

### Build the ITGenius App Image

D. Navigate into the `itgenius-app` directory:

```bash
cd itgenius-app
```

E. Build the application image:

```bash
docker build -t itgenius-app:1.0 .
```

F. Move back to the root project directory:

```bash
cd ..
```

---

✅ Now both images are built successfully:
- `my-custom-mysql:8.0`
- `itgenius-app:1.0`

---

---

# Running Containers

## Step 9: Launch MySQL Container

```bash
docker run -d --name itgenius-mysql -e MYSQL_ROOT_PASSWORD=Databaseuserstrongpassword@123 -p 3306:3306 custom-mysql:8.0
```

✅ MySQL server is running.

---
## Step 10: Launch Application Container

```bash
docker run -d --name itgenius-app --link itgenius-mysql:mysql --env-file .env -p 8085:8085 itgenius-app:1.0
```

✅ Application is now running and connected to MySQL.

---
# Verifying the Setup

- Access the application in your browser:

```
http://<your-server-public-ip>:8085
```

- Exec into the Mysql Container to check database if needed:


---

# Command to **enter** the MySQL container:

```bash
docker exec -it itgenius-mysql /bin/bash
```

✅ This puts you **inside** the `itgenius-mysql` container (inside its Linux /bin/bash shell).

---
# Then **login into MySQL Engine** from inside the container:

```bash
mysql -u itgenius_app_user -p
```
- It will ask for a password.
- Enter the **itgenius_app_user password** you set:  
  `Databaseuserstrongpassword@123`

---
# After logging into MySQL, **check the databases**:

```sql
SHOW DATABASES;
```

✅ You should see:
- `itgenius_app_database`
- `information_schema`
- `mysql`
- `performance_schema`
- `sys`

---

- View logs for each container (This should be done whiles you are outside the container):

```bash
docker logs itgenius-app
```
```bash
docker logs itgenius-mysql
```

---
# 🧹 Useful Commands

| Command | Purpose |
|---------|---------|
| `docker ps` | See running containers |
| `docker images` | See built images |
| `docker stop <container_name>` | Stop a container |
| `docker rm <container_name>` | Remove a container |
| `docker rmi <image_name>` | Remove an image |

---
# Project Completion

Congratulations 🎉!  
You have deployed a **multi-container Spring Boot + MySQL application** using **custom Docker images** on a **single Lightsail server**.

---
# Final Folder Structure:

```
itgenius-app-standalone/
├── mysql/
│   ├── Dockerfile
│   └── init.sql
├── itgenius-app/
│   ├── Dockerfile
│   └── itgenius-0.0.1-SNAPSHOT.jar
├── .env
```

---
