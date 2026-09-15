# 🎓 Student Management System

> A full-stack Student Management System built with **Spring Boot, Thymeleaf, MySQL, Jenkins, and AWS**, with an automated CI/CD pipeline for building, testing, and deploying the application to an AWS EC2 server.

![Java](https://img.shields.io/badge/Java-21-orange?logo=openjdk)
![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.x-brightgreen?logo=springboot)
![MySQL](https://img.shields.io/badge/MySQL-8-blue?logo=mysql)
![Maven](https://img.shields.io/badge/Maven-Build-C71A36?logo=apachemaven)
![Jenkins](https://img.shields.io/badge/Jenkins-CI%2FCD-D24939?logo=jenkins)
![AWS](https://img.shields.io/badge/AWS-Cloud-FF9900?logo=amazonaws)
![EC2](https://img.shields.io/badge/AWS-EC2-FF9900?logo=amazonec2)
![RDS](https://img.shields.io/badge/AWS-RDS-527FFF?logo=amazonrds)
![Linux](https://img.shields.io/badge/Linux-Ubuntu-E95420?logo=ubuntu)

---

## 📌 Project Overview

The Student Management System provides a web-based interface for managing student records.

The application is deployed on an **AWS EC2 Application Server**, while student data is stored in **Amazon RDS for MySQL**.

Jenkins is hosted on a separate EC2 instance and automatically:

1. Checks out the source code from GitHub
2. Builds and tests the application using Maven
3. Creates the Spring Boot JAR
4. Archives the build artifact
5. Transfers the JAR to the Application EC2 using SSH/SCP
6. Installs the new JAR
7. Restarts the systemd service
8. Verifies that the application service is running

---

# 🏗️ AWS Architecture

### Architecture Diagram

![Student Management AWS Architecture](docs/architecture/student-management-architecture.png)

### Application Flow

```text
                         Developer
                             |
                             | git push
                             v
                    +----------------+
                    |    GitHub      |
                    | Student Repo   |
                    +-------+--------+
                            |
                          HTTPS
                            |
                            v
                    +----------------+
                    |   Jenkins EC2  |
                    |                |
                    | Jenkins        |
                    | Java 21        |
                    | Maven          |
                    +-------+--------+
                            |
                         SSH / SCP
                            |
                            v
                    +----------------+
                    | Application EC2|
                    |                |
                    | Java 21        |
                    | Spring Boot    |
                    | Port 8080      |
                    | systemd        |
                    +-------+--------+
                            |
                         JDBC :3306
                            |
                            v
                    +----------------+
                    |   Amazon RDS   |
                    |     MySQL      |
                    |   studentdb    |
                    +----------------+
```

The Jenkins-to-Application Server deployment uses SSH/SCP, while GitHub source checkout and application deployment are separate connections.

---

# 🛠️ Technology Stack

### Application

- Java 21
- Spring Boot 3
- Spring MVC
- Spring Security
- Thymeleaf
- Bootstrap
- MySQL
- REST APIs
- Maven

### DevOps & Cloud

- AWS EC2
- Amazon RDS
- AWS VPC
- Jenkins
- Git
- GitHub
- SSH
- SCP
- Linux / Ubuntu
- systemd
- Nginx

---

# 🔄 CI/CD Pipeline

The project implements a Jenkins-based CI/CD pipeline.

### Pipeline Flow

```text
docs/videos/jenkins-cicd-demo.mp4
```

---

# 📸 Application Screenshots

## 🔐 Login

![Login](docs/screenshots/login.png)

## 📊 Dashboard

![Dashboard](docs/screenshots/dashboard.png)

## 👨‍🎓 Student List

![Student List](docs/screenshots/student-list.png)

## ➕ Add Student

![Add Student](docs/screenshots/add-student.png)

---

# 🎥 Project Demo

## 🎥 Application Demo

[![Student Management System Demo](https://img.youtube.com/vi/Frmffy9lOsI/maxresdefault.jpg)](https://youtu.be/Frmffy9lOsI)

Click the image above to view the complete application demo.

The application demo demonstrates:

- Login
- Dashboard
- Student management
- CRUD operations
- Search
- Application navigation

---



# ☁️ AWS Deployment

The Jenkins pipeline uses an SSH credential:

```text
student-management-app-ssh
```

The application server receives the generated JAR through SCP.

The deployment location is:

```text
AWS VPC
│
├── Public Subnet
│   ├── Jenkins EC2
│   └── Application EC2
│
└── Database Subnets
    └── Amazon RDS MySQL
```

### Jenkins EC2

Jenkins runs on an AWS EC2 instance and is responsible for:

- Pulling source code
- Running Maven builds
- Running tests
- Creating the JAR
- Deploying the application
- Verifying deployment

### Application EC2

The application server:

- Runs Ubuntu Linux
- Runs Java 21
- Hosts the Spring Boot JAR
- Uses systemd for process management
- Exposes the Spring Boot application on port `8080`
- Connects to Amazon RDS through MySQL port `3306`

### Amazon RDS

Amazon RDS MySQL provides the persistent database for the application.

```text
Application EC2
      |
      | TCP 3306
      v
Amazon RDS MySQL
      |
      v
   studentdb
```

The RDS security group is designed to allow MySQL traffic from the Application Server security group rather than opening port `3306` to the entire internet.

---

# 🗄️ Database Configuration

The Spring Boot application supports environment variables for database configuration:

```properties
spring.datasource.url=jdbc:mysql://${DB_HOST:localhost}:${DB_PORT:3306}/${DB_NAME:studentdb}?useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true

spring.datasource.username=${DB_USER:root}

spring.datasource.password=${DB_PASSWORD:root}
```

Production deployment uses environment variables rather than committing database credentials into the source code.

Example:

```text
DB_HOST=<RDS_ENDPOINT>
DB_PORT=3306
DB_NAME=studentdb
DB_USER=<RDS_USERNAME>
DB_PASSWORD=<RDS_PASSWORD>
```

> 🔐 **Never commit real database passwords, private keys, or other secrets to GitHub.**

---

# ⚙️ Application Service

The application runs as a Linux `systemd` service.

Service:

```text
student-management.service
```

Application directory:

```text
/opt/student-management
```

JAR:

```text
/opt/student-management/student-management.jar
```

Application port:

```text
8080
```

The service runs the Spring Boot JAR using Java 21 and automatically restarts if the process exits.

Useful commands:

```bash
sudo systemctl status student-management
```

```bash
sudo systemctl restart student-management
```

```bash
sudo journalctl -u student-management -n 50 --no-pager
```

---

# 🔌 REST API

The application provides REST endpoints for student management.

| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/students` | List students |
| GET | `/api/students/{id}` | Get student |
| POST | `/api/students` | Create student |
| PUT | `/api/students/{id}` | Update student |
| DELETE | `/api/students/{id}` | Delete student |

Example:

```text
GET /api/students
```

---

# 📁 Project Structure

```text
Student-management/
│
├── docs/
│   ├── architecture/
│   │   └── student-management-architecture.png
│   │
│   ├── screenshots/
│   │   ├── login.png
│   │   ├── dashboard.png
│   │   ├── student-list.png
│   │   ├── add-student.png
│   │   └── application-demo-thumbnail.png
│   │
│   └── videos/
│       ├── application-demo.mp4
│       └── jenkins-cicd-demo.mp4
│
├── src/
│   ├── main/
│   │   ├── java/
│   │   └── resources/
│   │
│   └── test/
│
├── Jenkinsfile
├── pom.xml
├── README.md
└── .gitignore
```

---

# 🔐 Security Considerations

The project follows several basic security practices:

- 🔑 SSH access should be restricted to trusted IP addresses
- 🛡️ AWS Security Groups control inbound traffic
- 🔒 Database access is restricted to the application server
- 🚫 Database should not be publicly accessible
- 🔐 Production credentials should be stored outside source code
- 🔄 SSH keys should be managed securely
- 🗝️ Database credentials should be rotated periodically

For production environments, AWS Secrets Manager or SSM Parameter Store can be used for centralized secret management.

---

# 🛠️ Useful Troubleshooting Commands

### Check application service

```bash
sudo systemctl status student-management --no-pager
```

### Check application logs

```bash
sudo journalctl -u student-management -n 100 --no-pager
```

### Check port 8080

```bash
sudo ss -lntp | grep 8080
```

### Test the application locally on EC2

```bash
curl -I http://localhost:8080
```

Expected application response may redirect to the login page.

### Test RDS connectivity

```bash
nc -zv <RDS-ENDPOINT> 3306
```

---

# 🎯 What I Learned From This Project

This project helped me gain practical experience in:

- AWS EC2 provisioning and configuration
- Amazon RDS MySQL
- VPC and Security Groups
- Linux server administration
- Java application deployment
- Maven build automation
- Jenkins CI/CD
- Git and GitHub
- SSH key-based authentication
- SCP-based deployment
- systemd service management
- Application troubleshooting
- Database connectivity troubleshooting
- CI/CD pipeline debugging

---

# 🚀 Future Improvements

Possible improvements for the project:

- [ ] HTTPS using SSL/TLS
- [ ] AWS Application Load Balancer
- [ ] Auto Scaling
- [ ] CloudWatch monitoring
- [ ] AWS Secrets Manager
- [ ] Blue/Green deployment
- [ ] Automated rollback
- [ ] Infrastructure as Code with Terraform
- [ ] Jenkins webhook-based automatic deployment
- [ ] Automated integration testing

---

# 📊 Project Highlights

```text
┌──────────────────────────────────────────┐
│        STUDENT MANAGEMENT SYSTEM         │
├──────────────────────────────────────────┤
│                                          │
│  ☁️ AWS Deployment                       │
│  🔄 Jenkins CI/CD                        │
│  ☕ Spring Boot                           │
│  🗄️ MySQL RDS                            │
│  🐧 Linux / Ubuntu                       │
│  🔐 SSH/SCP Deployment                   │
│  ⚙️ systemd                              │
│  📦 Maven                                │
│                                          │
└──────────────────────────────────────────┘
```

---

# 👨‍💻 Author

## Pritesh Biradar

AWS / DevOps enthusiast focused on building practical cloud infrastructure, CI/CD pipelines, and production-style deployments.

### GitHub

[![GitHub](https://img.shields.io/badge/GitHub-PriteshBiradar-black?logo=github)](https://github.com/PriteshBiradar)

### Portfolio

[![Portfolio](https://img.shields.io/badge/Portfolio-Visit-blue?logo=googlechrome)](https://priteshbiradar.github.io/Pritesh_Biradar.github.io/)

---

## ⭐ If you found this project useful

Feel free to ⭐ the repository and explore the implementation.

---

### 🚀 Learning • Building • Automating • Growing
**Built by Pritesh Biradar**