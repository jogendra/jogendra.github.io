---
layout: post
title: "Dockerize Your Jekyll Site for Local Development"
description: Get your Jekyll site running in a Docker container quickly and easily. This practical guide provides step-by-step instructions for building and running a containerized Jekyll development environment with live reloading.
comments: true
keywords: jekyll, docker, docker compose, Containerization, Gemfile, Bundler, GitHub Pages, How To
scover: docker-for-jekyll.jpg
---

Jekyll is a fantastic static site generator, especially popular for its integration with GitHub Pages. This website is is hosted on GitHub Pages and built using Jekyll. Initially, I tried installing all the dependencies on my Mac to work on the site locally. However, managing dependencies and making everything work seamlessly turned out to be quite a hustle. Different Ruby versions, gem dependencies, and operating system quirks can lead to inconsistencies and headaches. This is where Docker comes in to save the day. By using Docker and Docker Compose, you can ensure a consistent environment, avoid dependency conflicts, and simplify the process of running your Jekyll website locally. In this guide, I’ll walk you through the steps to set up and run your Jekyll website locally using Docker.

---

### Prerequisites

Before you begin, ensure you have the following installed on your system:

- **Docker:** Download and install [Docker](https://www.docker.com/).
- **Docker Compose:** Comes bundled with Docker Desktop.

---

### Step 1: Create the Dockerfile

Let's start by crafting a Dockerfile that defines our Jekyll development environment. Create a file named `Dockerfile` in the root directory of your Jekyll project. The `Dockerfile` defines the environment required to build and run your Jekyll site. Here’s a complete `Dockerfile`:

```dockerfile
# Use a slim Ruby image as the base
FROM ruby:3.1-slim

# Set environment variables for UTF-8 encoding and non-interactive package installation
ENV LANG=C.UTF-8 \
    LC_ALL=C.UTF-8 \
    DEBIAN_FRONTEND=noninteractive

# Install essential build tools and dependencies
RUN apt-get update && apt-get install -y \
    build-essential \
    git \
    curl \
    && rm -rf /var/lib/apt/lists/*

# Set the working directory inside the container
WORKDIR /usr/src/app

# Copy the Gemfile and Gemfile.lock
COPY Gemfile* ./

# Install Bundler and project dependencies
RUN gem install bundler && bundle install

# Copy the rest of the source code
COPY . .

# Expose port 4000
EXPOSE 4000

# Start the Jekyll development server with live reloading
CMD ["bundle", "exec", "jekyll", "serve", "--host", "0.0.0.0", "--watch"]
```

#### Key Points
- **Base Image:** The `ruby:3.1-slim` image provides a minimal Ruby environment.
- **Dependencies:** Installs essential build tools, Git, and Curl.
- **Working Directory:** The project files are copied into `/usr/src/app`.
- **Jekyll Command:** Runs the Jekyll server with `--watch` to detect file changes.

Now, let's go through the steps to build and run your Jekyll site using only Docker commands:
#### 1. Build the Docker Image:
```bash
docker build -t jekyll-site .
```

#### 2. Run the Docker Container:
```bash
docker run -d -p 4000:4000 -v "$PWD":/usr/src/app jekyll-site
```

After running the container, your Jekyll site should now be accessible at `http://localhost:4000` in your browser.

Couple of key points from commands above:
- `-t jekyll-dev`: This tags (names) the image `jekyll-site`. You can choose any name you like.
- `-v "$PWD":/usr/src/app`: This is crucial for live reloading. It mounts your current working directory (the Jekyll project) to the `/usr/src/app` directory inside the container. This means any changes you make to your files locally will be immediately reflected inside the container.

---

### Step 2: Create the Docker Compose File

The `docker-compose.yml` simplifies running your Jekyll container by defining the necessary services and configurations. Here’s the complete file:

```yaml
services:
  jekyll:
    image: jekyll-site:latest
    build:
      context: .
    volumes:
      - .:/usr/src/app
      - /usr/src/app/_site
    ports:
      - "4000:4000"
    command: bundle exec jekyll serve --host 0.0.0.0 --watch
```

#### Key Points
- **Image:** Builds the Docker image from the `Dockerfile`.
- **Volumes:** Maps the local project directory and `_site` (generated files) to the container.
- **Ports:** Maps container’s port `4000` to the host’s port `4000`.
- **Command:** Starts the Jekyll server.

---

### Step 3: Build and Run the Jekyll Site

Run the following commands to build and start your Jekyll site:

#### Build the Docker Image
```bash
docker-compose up --build
```

#### Access the Website
Once the server starts, open your browser and navigate to:
```text
http://localhost:4000
```
You should see your Jekyll website running locally.

---

### Step 4: Reflect Live Changes

With the `--watch` option and volume mapping, changes made to your local files (e.g., `index.html`, `_config.yml`, `_posts`) will automatically reflect on the website after a short rebuild by Jekyll.

#### Example
1. Modify `index.html` in your project directory.
2. Refresh the browser to see the changes.

---

### Step 5: Stop the Server

To stop the server, press `Ctrl+C` in the terminal or use the following command:
```bash
docker-compose down
```

---

### Troubleshooting

### Blank Page or Missing Assets
- Check the `baseurl` in `_config.yml`. For local development, it should be empty:
  ```yaml
  baseurl: ""
  ```

#### Permission Issues
If you encounter permission errors, ensure proper file permissions:
```bash
chmod -R 755 .
```

#### Rebuild the Image
If changes are not reflecting, force a rebuild:
```bash
docker-compose up --build
```

---

Now you’re all set to run and manage your Jekyll website locally with Docker! Feel free to checkout [github.com/jogendra/jogendra.github.io](https://github.com/jogendra/jogendra.github.io). Let me know if you have any questions or need further assistance.
