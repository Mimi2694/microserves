# Distributed Microservices System

## Overview

This project demonstrates a **distributed microservice architecture** using modern backend technologies. The system is divided into multiple independent services that communicate through **REST APIs and asynchronous messaging**.

The architecture follows key distributed system principles including:

* Microservice architecture
* API Gateway pattern
* Service-to-service communication
* Authentication and authorization
* Event-driven messaging

The system consists of the following services:

* **API Gateway**
* **Auth Service**
* **Main Service**
* **Notification Service**
* **RabbitMQ Message Broker**

---

# Architecture

## System Architecture Diagram

```
                +---------------------+
                |       Client        |
                |  (Web / Mobile)     |
                +----------+----------+
                           |
                           |
                     API Gateway
                           |
        ---------------------------------------
        |                |                    |
        |                |                    |
   Auth Service      Main Service     Notification Service
        |                |
        |                |
        ----------- RabbitMQ ------------
              (Asynchronous Messaging)
```

### Architecture Description

1. Clients communicate only with the **API Gateway**.
2. The **API Gateway** routes requests to the appropriate service.
3. The **Auth Service** handles user authentication and JWT generation.
4. The **Main Service** handles core business logic.
5. The **Notification Service** processes events asynchronously using RabbitMQ.

---

# Microservices

## 1. Auth Service

### Purpose

Handles authentication and authorization.

### Features

* User login
* JWT token generation
* Token validation
* Role-based access control

### Example Endpoints

| Method | Endpoint    | Description        |
| ------ | ----------- | ------------------ |
| POST   | `/login`    | Authenticate user  |
| GET    | `/validate` | Validate JWT token |

Example Response

```json
{
 "access_token": "JWT_TOKEN",
 "token_type": "bearer"
}
```

---

## 2. Main Service

### Purpose

Handles the core application logic such as managing workouts or user data.

### Features

* Retrieve data
* Add new records
* Publish events to RabbitMQ

### Example Endpoints

| Method | Endpoint | Description       |
| ------ | -------- | ----------------- |
| GET    | `/items` | Get list of items |
| POST   | `/items` | Add new item      |

---

## 3. Notification Service

### Purpose

Handles notifications triggered by system events.

### Features

* Listens to RabbitMQ events
* Sends notifications
* Logs events

Example message from queue:

```json
{
 "user": "user1",
 "message": "Workout added successfully"
}
```

---

# API Gateway

The **API Gateway** is the entry point for all client requests.

### Responsibilities

* Request routing
* Authentication forwarding
* Service aggregation
* Logging and monitoring

### Request Flow

```
Client → API Gateway → Auth Service
Client → API Gateway → Main Service
Client → API Gateway → Notification Service
```

---

# Service Communication

This system implements **two communication methods**.

## 1. REST Communication (Synchronous)

Used when an immediate response is required.

Example:

```
Main Service → Auth Service → Validate Token
```

Example Request

```
GET /validate
Authorization: Bearer <token>
```

### Advantages

* Easy to implement
* Simple debugging

### Disadvantages

* Services become dependent on each other

---

## 2. Asynchronous Messaging (RabbitMQ)

Used for event-driven communication.

Example workflow:

1. Main Service publishes an event:

```
WorkoutAdded
```

2. Event is sent to RabbitMQ queue.

3. Notification Service consumes the event.

### Advantages

* Loose coupling
* Improved scalability
* Better fault tolerance

---

# Authentication and Authorization

Authentication is implemented using **JWT (JSON Web Tokens)**.

## Authentication Flow

1. Client sends login request

```
POST /auth/login
```

2. Auth Service validates credentials.

3. JWT token is returned.

Example response

```json
{
 "access_token": "token",
 "role": "user"
}
```

4. Client includes token in future requests.

```
Authorization: Bearer TOKEN
```

---

## Role-Based Authorization

| Role  | Permissions           |
| ----- | --------------------- |
| Admin | Full system access    |
| User  | Limited functionality |

Example restricted endpoint:

```
POST /admin/create-user
```

---

# Technologies Used

| Component         | Technology        |
| ----------------- | ----------------- |
| Backend Framework | FastAPI           |
| Messaging System  | RabbitMQ          |
| Containerization  | Docker            |
| Authentication    | JWT               |
| API Documentation | OpenAPI / Swagger |

---

# Docker Deployment

All services are containerized using Docker.

Services included:

* api-gateway
* auth-service
* main-service
* notification-service
* rabbitmq

## Running the System

### Step 1

Clone the repository

```
git clone https://github.com/your-repo/microservices-project.git
cd microservices-project
```

### Step 2

Build and run the containers

```
docker-compose up --build
```

### Step 3

Access services

| Service              | URL                    |
| -------------------- | ---------------------- |
| API Gateway          | http://localhost:8003  |
| Auth Service         | http://localhost:8000  |
| Main Service         | http://localhost:8001  |
| Notification Service | http://localhost:8002  |
| RabbitMQ Dashboard   | http://localhost:15672 |

Default RabbitMQ credentials

```
username: guest
password: guest
```

---

# API Documentation

FastAPI automatically provides **Swagger documentation**.

Access it via:

Auth Service

```
http://localhost:8000/docs
```

Main Service

```
http://localhost:8001/docs
```

API Gateway

```
http://localhost:8003/docs
```

Swagger allows developers to:

* Test endpoints
* View request schemas
* Explore API responses

---

# Communication Design Summary

| Communication                       | Method   |
| ----------------------------------- | -------- |
| Client → Gateway                    | REST     |
| Gateway → Services                  | REST     |
| Main Service → Auth Service         | REST     |
| Main Service → Notification Service | RabbitMQ |

This hybrid model combines **synchronous REST calls with asynchronous messaging** to balance simplicity and scalability.

---

# Key Distributed System Concepts

This project demonstrates several important concepts:

* Microservice architecture
* API Gateway pattern
* Service-to-service communication
* Event-driven architecture
* Authentication and authorization
* Containerized deployment
* Distributed system design

---

# Future Improvements

Potential improvements include:

* Kubernetes deployment
* Load balancing
* Distributed tracing
* Monitoring with Prometheus and Grafana
* Advanced security mechanisms

---

# Conclusion

This project demonstrates how to design and implement a **distributed microservice system** using modern backend technologies. The architecture improves scalability, modularity, and maintainability while maintaining secure communication between services.
