# **RestRedis**

`RestRedis` is a lightweight REST API application that acts as a bridge to interact with a Redis database. It allows clients to perform basic Redis operations (e.g., `GET`, `SET`, `DELETE`) via simple HTTP requests. This is particularly useful for scenarios where RESTful communication is needed to interact with Redis.

---

## **Features**

- Lightweight REST API built with Go.
- Supports basic Redis operations:
  - **SET**: Store raw values with optional expiration.
  - **GET**: Retrieve stored values.
  - **DELETE**: Remove keys from the database.
- Easily configurable via environment variables.
- Dockerized for deployment flexibility.
- Designed to work with Redis running locally, in Docker, or on a remote host.
- Handles raw body input for storing data.

---

## **Endpoints**

### **1. Set Key-Value Pair**
**URL**: `POST /:key`

**Description**: Store a key-value pair in Redis, with an optional expiration time.

- **Path Parameter**:
  - `key` (required): The Redis key.
- **Query Parameter**:
  - `expiration` (optional): Expiration time in seconds.
- **Body**: Raw data to store as the value.

**Example**:
```bash
curl -X POST "http://localhost:8081/mykey?expiration=60" \
     -d "This is my raw value"
```

**Response**:
```
HTTP 200 OK
Key 'mykey' set successfully
```

---

### **2. Get Key**
**URL**: `GET /:key`

**Description**: Retrieve the value of a given key from Redis.

- **Path Parameter**:
  - `key` (required): The Redis key to retrieve.

**Example**:
```bash
curl "http://localhost:8081/mykey"
```

**Response**:
```
HTTP 200 OK
This is my raw value
```

---

### **3. Delete Key**
**URL**: `DELETE /:key`

**Description**: Delete a key from Redis.

- **Path Parameter**:
  - `key` (required): The Redis key to delete.

**Example**:
```bash
curl -X DELETE "http://localhost:8081/mykey"
```

**Response**:
```
HTTP 200 OK
Key 'mykey' deleted successfully
```

---

## **Environment Variables**

The app uses environment variables for configuration. These variables can be set directly in the runtime environment or passed using a `.env` file.

| Variable        | Description                                | Default Value |
|------------------|--------------------------------------------|---------------|
| `REDIS_HOST`     | The hostname or IP address of the Redis server. | `localhost`   |
| `REDIS_PORT`     | The Redis server port.                     | `6379`        |
| `REDIS_PASSWORD` | The password for the Redis server (if any). | (empty)       |
| `APP_PORT`       | The port for the REST API server.          | `8081`        |

**Example `.env` File**:
```dotenv
REDIS_HOST=redis-server
REDIS_PORT=6379
REDIS_PASSWORD=
APP_PORT=8081
```

---

## **Running the App**

### **1. Using Go Directly**
1. **Clone the repository**:
   ```bash
   git clone https://github.com/sistemica/restredis.git
   cd restredis
   ```

2. **Install dependencies**:
   ```bash
   go mod tidy
   ```

3. **Run the app**:
   ```bash
   go run main.go
   ```

4. **Set environment variables** or provide a `.env` file in the root directory.

---

### **2. Using Docker**

#### **Building the Docker Image**
```bash
docker build -t restredis .
```

#### **Running the Container**
```bash
docker run -d \
  --name restredis \
  --env-file .env \
  -p 8081:8081 \
  restredis
```

#### **Connecting to Redis**
- If Redis is running in Docker:
  ```bash
  docker network create app-network

  docker run -d \
    --name redis-server \
    --network app-network \
    redis:latest

  docker run -d \
    --name restredis \
    --env-file .env \
    --network app-network \
    -p 8081:8081 \
    restredis
  ```

- If Redis is running on the host:
  - Use `REDIS_HOST=host.docker.internal` (for Mac/Windows) or the host IP for Linux.

---

## **Development**

### **Requirements**
- Go 1.20 or later
- Redis (local or remote)

### **Directory Structure**
```
.
├── main.go         # Application entry point
├── go.mod          # Go module definition
├── go.sum          # Dependencies checksum
├── Dockerfile      # Dockerfile for containerization
└── .env            # Environment variables (not committed to Git)
```

### **Testing the Endpoints**
- Use `curl`, Postman, or any REST client to interact with the API.

---

## **Troubleshooting**

### **Redis Connection Issues**
1. Verify Redis is running and reachable:
   ```bash
   redis-cli -h <REDIS_HOST> -p <REDIS_PORT> ping
   ```
   Expected output:
   ```
   PONG
   ```

2. Check the `REDIS_HOST` and `REDIS_PORT` environment variables.

### **Docker-Specific Issues**
- If using `host.docker.internal` and it fails:
  - Use the host's IP address (`172.17.0.1` for Linux) or Docker's host networking mode (`--network host`).

---

## **Extending the App**

### **Additional Features**
- **Authentication**: Add basic authentication or API tokens for secure access.
- **Additional Redis Commands**:
  - Support for more commands like `EXISTS`, `INCR`, `HGET`, etc.
- **Monitoring**:
  - Integrate with monitoring tools like Prometheus for metrics.
- **WebSocket Support**:
  - Add real-time updates for subscribed keys.

---

## **License**
This project is licensed under the [MIT License](LICENSE).

---

Feel free to reach out for suggestions or contributions! 🚀
