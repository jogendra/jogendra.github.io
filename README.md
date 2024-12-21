## jogendra.dev

The site is available at - https://jogendra.dev/. It has been hosted using GitHub pages.

## Run locally
It can be run locally using Docker and Docker Compose.

### Prerequisites

- Docker installed on your machine.
- Docker Compose installed (comes bundled with Docker Desktop).

### 1. Build and Run the Container
To start the Jekyll server locally:

```bash
docker-compose up --build
```

This will:
- Build the Docker image.
- Start the Jekyll server.
- Expose the website at `http://localhost:4000`.

Modify any source file in your local directory. Changes will automatically reflect in the browser after a short rebuild.

To stop the server:
```bash
docker-compose down
```
