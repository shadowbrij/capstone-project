# Github link of all the code repos are below
1. **[Service Discovery (Eureka Server)](https://github.com/shadowbrij/servicediscovery.git)** 
2. **[API Gateway](https://github.com/shadowbrij/apigateway.git)** 
3. **[User Service](https://github.com/shadowbrij/userService.git)**
4. **[Product Service](https://github.com/shadowbrij/productservice.git)** 
5. **[Payment Service](https://github.com/shadowbrij/paymentservice.git)** 

## Project setup instructions

## Overview

This is a distributed e-commerce platform built using Spring Boot microservices architecture. The system consists of 5 independent microservices that work together to provide a complete e-commerce solution with user management, product catalog, payment processing, and API gateway for routing.

### Microservices Architecture

1. **[Service Discovery (Eureka Server)](https://github.com/shadowbrij/servicediscovery.git)** - Central service registry for service location
2. **[API Gateway](https://github.com/shadowbrij/apigateway.git)** - Entry point for all client requests with routing capabilities
3. **[User Service](https://github.com/shadowbrij/userService.git)** - User authentication, authorization, and role management
4. **[Product Service](https://github.com/shadowbrij/productservice.git)** - Product catalog management and search functionality
5. **[Payment Service](https://github.com/shadowbrij/paymentservice.git)** - Payment processing with multiple gateway support (Stripe, Razorpay)

---

## Prerequisites

Before you begin, ensure you have the following installed on your machine:

- **Java Development Kit (JDK)** - Version 11 or higher
- **Maven** - Version 3.6.0 or higher
- **MySQL Server** - Version 5.7 or higher
- **Git** - For cloning repositories

Verify installations:
```bash
java -version
mysql --version
git --version
```

---

## Setup Instructions

### Step 1: Clone All Microservices

Clone each microservice repository into your local workspace:

```bash
# Navigate to your preferred workspace directory
cd /path/to/your/workspace

# Clone each microservice
git clone https://github.com/shadowbrij/userService.git
git clone https://github.com/shadowbrij/productservice.git
git clone https://github.com/shadowbrij/servicediscovery.git
git clone https://github.com/shadowbrij/paymentservice.git
git clone https://github.com/shadowbrij/apigateway.git
```

---

### Step 2: Run Service Discovery (Eureka Server)

The Service Discovery server must be started first as other services depend on it for registration.

```bash
cd servicediscovery/src
./mvnw spring-boot:run
```

**Expected Output:**
- Server should start on `http://localhost:8761`
- Eureka dashboard available at `http://localhost:8761/eureka`
- Wait for the server to be fully initialized before proceeding

---

### Step 3: Run API Gateway

The API Gateway serves as the entry point for all client requests and routes them to appropriate microservices.

```bash
cd apigateway/src
./mvnw spring-boot:run
```

**Expected Output:**
- API Gateway should start on its configured port (9093 in this case)
- Service should register with Eureka automatically
- Gateway will be available for routing requests to other services

---

### Step 4: Run User Service

The User Service handles authentication, user management, and role-based access control.

#### 4.1 Setup Environment Variables

Set the following environment variables in your terminal:

```bash
# Database Configuration
export DB_USER=<your-mysql-username>
export DB_PASSWORD=<your-mysql-password>
export USERSERVICE_DB_NAME=userservice

# Create the database if it doesn't exist
mysql -u $DB_USER -p$DB_PASSWORD -e "CREATE DATABASE IF NOT EXISTS $USERSERVICE_DB_NAME;"
```

#### 4.2 Insert Client Details

Before running the service, insert initial client details by running the test suite:

```bash
cd userservice/src
./mvnw spring-boot:test
```

This command will:
- Create database tables
- Insert default OAuth2 client configurations
- Setup initial security configurations

#### 4.3 Start User Service

```bash
cd userservice/src
./mvnw spring-boot:run
```

**Expected Output:**
- User Service should start on its configured port (typically 9090)
- Service registers with Eureka
- OAuth2 authorization server is initialized
- Database migrations are applied

---

### Step 5: Run Payment Service

The Payment Service handles all payment processing through multiple payment gateways.

#### 5.1 Setup Environment Variables

Set the following environment variables:

```bash
# Database Configuration
export DB_USER=<your-mysql-username>
export DB_PASSWORD=<your-mysql-password>
export PAYMENTSERVICE_DB_NAME=paymentservice

# Payment Gateway Configuration
export STRIPE_KEY_SECRET=<your-stripe-secret-key>

# Create the database if it doesn't exist
mysql -u $DB_USER -p$DB_PASSWORD -e "CREATE DATABASE IF NOT EXISTS $PAYMENTSERVICE_DB_NAME;"
```

#### 5.2 Start Payment Service

```bash
cd paymentservice/src
./mvnw spring-boot:run
```

**Expected Output:**
- Payment Service should start on port 9092
- Service registers with Eureka
- Payment gateways are initialized

---

### Step 6: Run Product Service

The Product Service manages the product catalog, categories, and search functionality.

#### 6.1 Setup Environment Variables

Create a `.env` file or set the following environment variables:

```bash
# Database Configuration
export DB_USER=<your-mysql-username>
export DB_PASSWORD=<your-mysql-password>
export PRODUCTSERVICE_DB_NAME=productservice

# Create the database if it doesn't exist
mysql -u $DB_USER -p$DB_PASSWORD -e "CREATE DATABASE IF NOT EXISTS $PRODUCTSERVICE_DB_NAME;"
```

#### 6.2 Run test

```bash
cd productservice/src
./mvnw test
```
This command will:
- Create database tables
- Insert few roles into role table
- Setup initial security configurations
 
#### 6.2 Start Product Service

```bash
cd productservice/src
./mvnw spring-boot:run
```

**Expected Output:**
- Product Service should start on its configured port (typically 9091)
- Service registers with Eureka
- Database tables are created/updated
- Search functionality is initialized

---

## Startup Order Summary

Follow this order to start all services:

1. ✅ **Service Discovery** (Port 8761) - Wait for full initialization
2. ✅ **API Gateway** (Port 8080) - Wait for Eureka registration
3. ✅ **User Service** (Port 9090) - Run tests first, then service
4. ✅ **Payment Service** (Port 9092) - With environment variables configured
5. ✅ **Product Service** (Port 9091) - With environment variables configured

---
Verification
...
---

## Environment Variables Reference

### User Service
```properties
DB_USER=your_mysql_username
DB_PASSWORD=your_mysql_password
USERSERVICE_DB_NAME=userservice
```

### Payment Service
```properties
DB_USER=your_mysql_username
DB_PASSWORD=your_mysql_password
PAYMENTSERVICE_DB_NAME=paymentservice
STRIPE_KEY_SECRET=your_stripe_secret_key
```

### Product Service
```properties
DB_USER=your_mysql_username
DB_PASSWORD=your_mysql_password
PRODUCTSERVICE_DB_NAME=productservice
```

---

## Troubleshooting

### Service Not Registering with Eureka
- Ensure Service Discovery is running first
- Check that `eureka.client.service-url.defaultZone` points to correct Eureka server
- Verify network connectivity between services

### Database Connection Issues
- Verify MySQL is running
- Check database credentials in environment variables
- Ensure databases are created before running services
- Check MySQL port (default: 3306)

### Port Already in Use
If a port is already in use, you can change the port in the `application.properties` file:
```properties
server.port=<new-port-number>
```

### Environment Variables Not Found
- Verify environment variables are set correctly
- On macOS/Linux: Use `echo $VARIABLE_NAME` to verify
- On Windows: Use `echo %VARIABLE_NAME%` to verify
- Restart your terminal after setting environment variables

### Maven Build Issues
- Clear Maven cache: `mvn clean`
- Update dependencies: `mvn dependency:resolve`
- Check Java version compatibility: `java -version`

---

## API Endpoints Summary

| Service | Base URL | Key Endpoints |
|---------|----------|---------------|
| API Gateway | http://localhost:9093 | Route to all services |
| User Service | http://localhost:9091 | /users, /auth, /roles |
| Product Service | http://localhost:9090 | /products, /categories, /search |
| Payment Service | http://localhost:9092 | /payments |
| Service Discovery | http://localhost:8761 | /eureka |

---

## Additional Resources

- [Spring Boot Documentation](https://spring.io/projects/spring-boot)
- [Spring Cloud Eureka](https://spring.io/projects/spring-cloud-netflix)
- [Spring Cloud Gateway](https://spring.io/projects/spring-cloud-gateway)
- [Spring Security](https://spring.io/projects/spring-security)
