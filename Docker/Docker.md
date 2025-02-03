# Docker: Containerizing Your Applications

Docker is a platform for building, running, and managing containerized applications. It's a way to package your application and all its dependencies (libraries, system tools, code, runtime, etc.) into a single unit called a container, and then run that container consistently across different environments.

### Key Concepts

- **Containers:** Lightweight, standalone, executable software packages that include everything needed to run an application: code, runtime, system tools, system libraries, and settings. They are isolated from each other and the host operating system, ensuring consistent operation regardless of deployment location. This addresses the "it works on my machine" problem.

- **Images:** Read-only templates that define what goes into a container. Think of them as blueprints. Images are built in layers, which enables efficient storage and distribution. You can create your own images or use existing ones from a registry (like Docker Hub).

- **Docker Engine:** The core of Docker. It's a client-server application running on the host OS, responsible for creating and managing containers. The Docker daemon (server) handles the work, while the Docker client allows you to interact with the daemon using commands.

- **Docker Hub (and other registries):** A storage and distribution system for Docker images. Docker Hub is the default public registry for finding and sharing images. You can also set up private registries.

### Why Use Docker?

- **Consistency:** Containers guarantee consistent application behavior across different environments (development, testing, production).
- **Portability:** Easily move containers between machines or cloud providers without compatibility issues.
- **Efficiency:** Containers share the host OS kernel, making them far more lightweight than virtual machines. This results in faster startup times and better resource utilization.
- **Isolation:** Containers provide isolation, preventing applications in different containers from interfering with each other.
- **Scalability:** Docker simplifies application scaling by running multiple containers.
- **Simplified Deployment:** Docker packages everything your application needs into a container, streamlining deployment.

### Using Alpine Images for Smaller Footprints

To minimize disk space and create more efficient images, consider using Alpine Linux-based images. Alpine is a very lightweight Linux distribution. Using Alpine versions of your base images (whether for Node.js, Go, or other languages) results in significantly smaller image sizes.

**Example:**
Instead of:

```dockerfile
FROM node
```

Use:

```Dockerfile
FROM mhart/alpine-node
```

This simple change can dramatically reduce your image size, leading to faster downloads, deployments, and more efficient resource usage. This principle applies to other languages and tools as well. Look for Alpine versions of the images you use.

### How Docker Works

1. **Create a Dockerfile:** A text file containing instructions for building a Docker image. It specifies the base image, dependencies, application code, and other settings.

2. **Build an Image:** The Docker Engine uses the Dockerfile to build a Docker image.

3. **Run a Container:** Use the Docker Engine to run a container based on the image. This creates a running instance of your application.

4. **Docker Manages the Container:** The Docker Engine manages the container's lifecycle (starting, stopping, removing).

### In Short

Docker lets you package your application and its dependencies into a container, ensuring consistent operation anywhere. It simplifies development, deployment, and scaling, making them easier and more efficient.

## Containerization vs. Images - Key Differences

| Feature      | Docker Image                                                                                                                                            | Docker Container                                                                                                                                                                        |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Nature       | Read-only template/blueprint                                                                                                                            | Runnable instance of an image                                                                                                                                                           |
| Contents     | _Instructions for creating a container_, includes everything needed to run an application: code, runtime, system tools, system libraries, and settings. | Running application and its dependencies                                                                                                                                                |
| State        | _Immutable_, If you need to make changes, you create a new image based on the existing one.                                                             | _Stateful_, can be started, stopped, paused, resumed, and removed. Changes made inside a container are typically not persisted unless specifically configured to do so (using volumes). |
| Analogy      | Cake recipe                                                                                                                                             | Cake baked from the recipe                                                                                                                                                              |
| Relationship | Used to create containers                                                                                                                               | Created from an image                                                                                                                                                                   |

### In Simple Terms

You build a Docker _image_ (the recipe), and then you run that image to create a Docker _container_ (the cake). You can have multiple containers running from the same image, just like you can bake multiple cakes from the same recipe.

# Layers

Docker images are built using a layered approach, where each layer represents a set of changes to the filesystem. Think of it like a layered cake!

**_Docker uses layers to cache between builds._**

## Why Layers are Great

- **Space Savings:** Layers are shared between images. If two images use the same base layers, they only store those layers once. This is like using the same cake base for multiple different cakes. Efficient use of disk space!
- **Faster Builds:** Docker _caches_ layers. If a layer hasn't changed, Docker _reuses_ it, speeding up build times significantly. Think of it as baking the cake base once and then just adding new frosting each time - much faster than baking a whole new cake!
- **Easy Rollback:** If something goes wrong, you can easily remove a layer (like scraping off bad frosting without having to bake a new cake). This makes it easy to revert changes.

## How Layers are Created

Each instruction in a Dockerfile (the recipe for building your image) creates a new layer:

- `FROM`: This instruction creates the very first layer, the base image (like the cake base).
- `RUN`, `COPY`, `ADD`: These instructions add new layers on top of the base, like adding ingredients or decorations to the cake. Each of these commands adds a layer representing the changes they made.

## Inspecting Layers

You can use the `docker history` command to see all the layers in an image. This is like looking at the recipe for your cake, showing you all the steps and ingredients. It shows the image ID, creation time, and the command that created each layer.

## Key Takeaway for Efficient Docker Images

Understanding how Docker layers work is crucial for creating smaller and faster Docker images. Key points:

- **Minimize Layers:** Fewer layers generally mean smaller images.
- **Leverage Caching:** Structure your Dockerfile to take advantage of Docker's layer caching for faster builds. Put frequently changing instructions towards the end of the Dockerfile.

## Instructions

- **`FROM <image>[:<tag>]`:** Base image. _First instruction._ Example: `FROM ubuntu:latest`
- **`RUN <command>`:** Execute command in a _new_ layer. Used for software installation, file manipulation. Example: `RUN apt-get update && apt-get install -y nginx`
- **`COPY <src>... <dest>`:** Copy files/directories from host to image. Example: `COPY myapp /var/www/html`
- **`ADD <src>... <dest>`:** Copy files/directories (handles URLs, archives). Use `COPY` unless needed. Example: `ADD https://example.com/app.tar.gz /app/`
- **`CMD ["executable", "param1", "param2"]`:** Default command on container start. _One only._ Overridable at runtime. Example: `CMD ["nginx", "-g", "daemon off;"]`
- **`ENTRYPOINT ["executable", "param1", "param2"]`:** Main container command. Harder to override. Defines container's purpose. Example: `ENTRYPOINT ["/app/my_app"]`
- **`WORKDIR <path>`:** Set working directory. Affects `RUN`, `CMD`, `ENTRYPOINT`, `COPY`, `ADD`. Example: `WORKDIR /app`
- **`USER <user>[:<group>]`:** Set container user/group. For security. Example: `USER www-data`
- **`EXPOSE <port> [<port>/<protocol> ...]`:** Declare listening ports. Doesn't publish (use `-p` at runtime). Example: `EXPOSE 80/tcp`
- **`ENV <key>=<value>`:** Set environment variables. Example: `ENV APP_VERSION 1.0`
- **`ARG <name>[=<default value>]`:** Build-time variables. Use `--build-arg`. Example: `ARG BUILD_VERSION`
- **`LABEL <key>=<value> <key>=<value> ...`:** Add metadata. Example: `LABEL maintainer="info@example.com" version="1.2"`
- **`VOLUME ["/data"]`:** Create a mount point for volumes. Example: `VOLUME ["/var/lib/mysql"]`
- **`STOPSIGNAL <signal>`:** Set stop signal. Example: `STOPSIGNAL SIGTERM`
- **`MAINTAINER <email>` (Deprecated):** Use `LABEL maintainer=<email>`.
- **`ONBUILD <instruction>`:** Instructions executed when image is used as a base. Example: `ONBUILD COPY . /app`
- **`SHELL ["executable", "param1", "param2"]`:** Override default shell for `RUN`.
- **`HEALTHCHECK`:** Configure container health check.

## Example Dockerfile

```dockerfile
# Stage 1: Build the application
FROM node:16-alpine AS builder

ARG BUILD_VERSION=latest  # Build argument
ENV NODE_ENV production   # Environment variable
LABEL maintainer="[email address removed]" version=$BUILD_VERSION # Labels
WORKDIR /app

COPY package*.json ./
RUN npm install --omit=dev

COPY . .
RUN npm run build

# Stage 2: Create the final image
FROM nginx:alpine

COPY --from=builder /app/dist /usr/share/nginx/html

EXPOSE 80/tcp

HEALTHCHECK --interval=5s --timeout=3s --retries=3 CMD curl -fs http://localhost:80 || exit 1

STOPSIGNAL SIGTERM

ENTRYPOINT ["/usr/sbin/nginx", "-g", "daemon off;"]

CMD ["-g", "daemon off;"] # Default command if entrypoint is overridden without command

VOLUME /data # Example of a volume

# Example of ONBUILD instruction (if you intend to use this image as a base)
# ONBUILD COPY config /etc/myapp/config  # Copy config when image is used as base
```

## Best Practices

- `.dockerignore`: Exclude files/directories.
- Multi-stage builds: Smaller final images.
- Layer caching: Optimize Dockerfile order.
- Small base images: Minimize size.
- Combine `RUN` commands: Fewer layers.

# Volumes: Persistent Data Storage

Docker containers are ephemeral, meaning their data is lost when they are stopped or removed. Docker volumes provide a way to persist data generated and used by containers, independent of the container's lifecycle. They are the preferred mechanism for persisting data in Docker.

## Key Concepts

- **Independent Lifecycle:** Volumes exist independently of containers. They can be created before a container, attached to a running container, and remain even after the container is removed.
- **Data Persistence:** Data stored in volumes is persisted across container creations, deletions, and restarts. This ensures that important data is not lost.
- **Shared Data:** Volumes can be shared between multiple containers, allowing them to access and modify the same data.
- **Storage Location:** Volumes are stored on the host filesystem (or by a volume driver), but their management is handled by Docker. This provides an abstraction from the underlying storage details.
- **Types of Volumes:** Docker offers different types of volumes, each with its own characteristics:
  - **Named Volumes:** Have a specific name and are managed by Docker. Best for persisting data that needs to survive container removal.
  - **Bind Mounts:** Mount a directory from the host filesystem directly into the container. Useful for development, sharing configuration files, or when performance is critical. Changes on the host are immediately reflected in the container, and vice-versa.
  - **tmpfs Mounts:** Store data in the host's memory. Data is lost when the container is stopped or the host is rebooted. Useful for temporary files or performance-sensitive applications where data loss is acceptable.
- **Volume Drivers:** Extend Docker's volume capabilities to support different storage backends, such as NFS, GlusterFS, or cloud-based storage.

## Use Cases

- **Databases:** Persisting database files so data is not lost if the database container is replaced.
- **Configuration Files:** Sharing configuration files between containers or updating them without rebuilding images.
- **User Data:** Storing user uploads, session data, or other application-specific data.
- **Development:** Mounting source code into a container for easy development and testing.

## Key Commands

This document lists and describes the Docker volume-related commands.

- **`docker volume create [OPTIONS] [VOLUME]`:** Creates a new volume.

  - `--driver <DRIVER>`: Specifies the volume driver to use (e.g., local, nfs, etc.). Defaults to `local`.
  - `--label <KEY=VALUE>`: Adds metadata to the volume.
  - `--opt <KEY=VALUE>`: Driver-specific options. For the `local` driver, you might use this to specify the mount path on the host.

- **`docker volume ls [OPTIONS]`:** Lists all volumes.

  - `-q`: Only display volume names.
  - `--filter <FILTER>`: Filter volumes based on conditions (e.g., `--filter driver=local`).

- **`docker volume inspect [VOLUME...]`:** Displays detailed information about one or more volumes.

- **`docker volume rm [VOLUME...]`:** Removes one or more volumes. A volume must not be in use by a container to be removed.

- **`docker volume prune`:** Removes all unused volumes. Unused volumes are those not currently attached to any container.

- **`docker volume cp <CONTAINER>:<PATH> <DEST>`:** Copies files/folders between a container and the volume. This is useful for initializing data in a volume or backing up data from a volume. Note that this command is deprecated and `docker cp` should be used instead. The syntax for `docker cp` to copy from a container to a volume is `docker cp <CONTAINER>:<PATH> <VOLUME_MOUNT_POINT_ON_HOST>` and to copy from a volume to a container is `docker cp <VOLUME_MOUNT_POINT_ON_HOST> <CONTAINER>:<PATH>`. This requires knowing where the volume is mounted on the host in case of named volumes.

- **`docker volume ls --verbose`:** Lists all volumes with additional information like driver, labels, and mountpoint.

## Important Considerations

- **Mountpoint:** For named volumes, Docker manages the actual location on the host filesystem. `docker volume inspect` will show you the mountpoint. For bind mounts, you explicitly define this mountpoint.
- **Driver-Specific Options:** The `--opt` flag allows you to pass driver-specific options. These options will vary depending on the volume driver you are using. Consult the documentation for the specific driver for details.
- **Removing Volumes in Use:** You cannot remove a volume that is currently in use by a running container. You must stop and remove the container first.
- **Data Persistence:** Data in named volumes persists even after the container using them is removed. Bind mounts' persistence depends on the mounted directory on the host. `tmpfs` volumes are ephemeral.

These commands provide all the necessary tools for managing Docker volumes effectively. Remember to use the `docker help` command for more detailed information on each command and its options.

## Benefits of using Volumes

- **Data Persistence:** Ensures data survives container lifecycle changes.
- **Data Sharing:** Enables sharing data between containers.
- **Simplified Backups:** Volumes can be easily backed up and restored.
- **Decoupling:** Decouples data storage from the container, making it easier to manage and update containers.

## Other Commands

- **`docker run -v <volume_name>:<data_dump_location> <image_name>`:** This will start container with mounting it to the given volume.

- **`docker run -v <local_dir_path>:<container_dir_path> <image_name>`:** This will start container with mounting it to your main computer directory so if you change files in main computer it automatically chages inside the container directory and vice versa.

# Networking

Docker networking allows containers to communicate with each other and the outside world. It provides an abstraction over the underlying network infrastructure, simplifying container connectivity.

## Key Concepts

- **Container Isolation:** By default, containers are isolated from each other and the host network. Networking allows you to create connections between them.
- **Network Drivers:** Docker uses network drivers to implement different types of networks.
- **Network Types:** Docker offers several built-in network types:
  - **Bridge:** The default network. Containers connected to a bridge network can communicate with each other. They are isolated from the host's network unless ports are published.
  - **Host:** The container shares the host's network stack. It has direct access to the host's network interfaces and all its ports. Offers the best performance but less isolation.
  - **Overlay:** Connects containers running on different Docker hosts (used with Docker Swarm). Enables multi-host networking.
  - **Macvlan:** Assigns a MAC address to a container, making it appear as a physical device on the network.
  - **None:** The container has no network connectivity.
- **User-Defined Networks:** You can create custom bridge networks for better isolation and management of container communication.
- **Port Mapping:** The `-p` flag (or `--publish`) when running a container maps a port on the host to a port inside the container. This allows external access to the container's services.
- **DNS Resolution:** Docker provides DNS resolution for containers within the same network, using _container names as hostnames_. (So, we have to use container name inpalce of localhost)
- **Container Linking (Legacy):** A deprecated way to link containers. User-defined networks are now the preferred method for container communication.

## Key Commands

### Network Management

- **`docker network create [OPTIONS] NETWORK`:** Create a new network.

  - `--attachable`: Enable manual container attachment.
  - `--driver <driver>`: Network driver (e.g., bridge, overlay).
  - `--gateway <IP>`: IPv4 gateway.
  - `--ingress`: Enable ingress traffic.
  - `--internal`: Restrict external access.
  - `--ip-range <CIDR>`: Custom subnet.
  - `--ipv6`: Enable IPv6.
  - `--label <key>=<value>`: Metadata.
  - `--opt <option>=<value>`: Driver options.
  - `--subnet <CIDR>`: Subnet.

- **`docker network ls [OPTIONS]`:** List networks.

  - `--filter <filter>`: Filter output.
  - `--format <template>`: Output format.
  - `-q`, `--quiet`: Only IDs.

- **`docker network inspect [OPTIONS] NETWORK [NETWORK...]`:** Inspect network(s).

  - `--format <template>`: Output format.

- **`docker network rm NETWORK [NETWORK...]`:** Remove network(s).

- **`docker network prune [OPTIONS]`:** Remove unused networks.
  - `--filter <filter>`: Filter values.
  - `-f`, `--force`: No confirmation.

### Container Connectivity

- **`docker network connect [OPTIONS] NETWORK CONTAINER`:** Connect container to network.

  - `--alias <alias>`: Network alias.
  - `--driver-opt <option>=<value>`: Driver options.

- **`docker network disconnect [OPTIONS] NETWORK CONTAINER`:** Disconnect container from network.
  - `--force`: Force disconnect.

## Use Cases

- **Microservices:** Containers can communicate with each other over a network.
- **External Access:** Publishing ports allows external clients to access services running in containers.
- **Multi-host Communication:** Overlay networks enable communication between containers across multiple hosts.
- **Service Discovery:** Docker's DNS resolution simplifies service discovery within a network.

## Best Practices

- **Use user-defined bridge networks:** For better isolation and management.
- **Avoid container linking:** Use user-defined networks instead.
- **Plan your network topology:** Consider the communication requirements of your application when designing your Docker networks.
- **Use port mapping carefully:** Only publish the ports that are necessary for external access.
- **Name you container:** When you connecting a docker network to conatainer give it a name so that another container can easily find your container.

# Environment Variables

Environment variables in Docker are key-value pairs that provide configuration information to applications running inside containers. They offer a way to customize application behavior without modifying the application's code itself.

## Key Concepts

- **Configuration:** Environment variables store settings, secrets (though Docker secrets are preferred for sensitive data), and other parameters.
- **Flexibility:** Set at build time (Dockerfile) or runtime (`docker run`). Runtime values override build-time values.
- **Dynamic Updates:** Changes to environment variables typically require a container restart to take effect.
- **Scope:** Environment variables are specific to a container.

## Setting Environment Variables

1. **Dockerfile (Build Time):** Use the `ENV` instruction.

   ```dockerfile
   ENV MY_VAR="my_value"
   ENV DATABASE_URL="user:password@host:port/database"
   ```

2. `docker run` **(Runtime):** Use the `-e` flag.
   ```bash
   docker run -e "MY_VAR=runtime_value" -e "SECRET_KEY=my_secret" <image_name>
   ```
3. **Docker Compose:** Define in `docker-compose.yml`.
   ```YAML
   version: "3.9"
   services:
    web:
    image: <image>
    environment:
      - MY_VAR=compose_value
      - DATABASE_URL=...
    env_file:  # Or use an env_file
      - .env
   ```
4. **.env files:** Store key-value pairs in a `.env` file.

   ```bash
   MY_VAR=env_file_value
   API_KEY=another_secret
   ```

   Then use it with `docker run` or Docker Compose.

   ```bash
   docker run --env-file .env <image>
   ```

## Accessing Environment Variables

Accessing environment variables depends on the language/framework used in the container:

- **Bash:** `echo $MY_VAR`
- **Python:** `import os; print(os.environ.get("MY_VAR"))`
- **Node.js:** `console.log(process.env.MY_VAR)`
- **Java:** `System.getenv("MY_VAR");`

## Best Practices

- **Docker Secrets:** Use Docker secrets for sensitive data in production. ENV variables are not ideal for secrets.
- **Descriptive Names:** Use clear, meaningful names.
- **Group Variables:** Use prefixes (e.g., `DATABASE_`, `API_`) for related variables.
- **Document:** Keep track of used variables and their purpose.
- **`.env` for Development:** Use `.env` files for local development.

## Commands and Environment Variables

- **`docker build`:** `ARG` instruction and `--build-arg` flag for build-time variables.
- **`docker run`:** `-e` flag or `--env-file` for setting/overriding variables.
- **`docker-compose up`:** `environment` section in `docker-compose.yml`.

## Example

**Dockerfile:**

```dockerfile
FROM ubuntu:latest

ENV APP_NAME="My Awesome App"
ENV PORT=8080

COPY app.py /app/
WORKDIR /app

CMD ["python", "app.py"]
```

**Bash:**

```bash
docker run -p 80:8080 -e "PORT=9000" <image_name>  # Overrides the PORT in the Dockerfile
```

In this example, the `APP_NAME` and `PORT` are set in the Dockerfile. The `docker run` command overrides the `PORT` to 9000. The application inside the container can then access these environment variables.

# Multistage Builds

Multistage builds in Docker are a powerful technique for optimizing Docker images by using multiple stages within a Dockerfile. This allows you to use intermediate container images for compilation or dependency management, and then copy only the necessary artifacts into a final, smaller image. This reduces the final image size, improves security, and speeds up deployments.

## Why use Multistage Builds?

- **Smaller Image Size:** Intermediate stages contain build tools, dependencies, and temporary files that are not needed in the final image. Multistage builds discard these, resulting in a significantly smaller final image.
- **Improved Security:** By not including build tools and dependencies in the final image, you reduce the attack surface.
- **Faster Builds:** Docker can cache intermediate stages, speeding up subsequent builds if only later stages have changed.
- **Cleaner Dockerfiles:** Multistage builds make Dockerfiles more organized and readable by separating build steps from the final image creation.

## How Multistage Builds Work?

A Dockerfile can have multiple `FROM` instructions. Each `FROM` instruction starts a new stage. You can copy artifacts from previous stages into the current stage using the `COPY --from` instruction.

## Example

```dockerfile
# Stage 1: Build Stage
FROM node:20 AS base

# Install dependencies, copy source code, and build the application
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install
COPY . .

# Stage 2: development Stage
FROM base AS development
CMD ["npm","run","dev"]

# Stage 3: production Stage
FROM base AS production
RUN npm prune --production
CMD ["npm","run","start"]

```

This Dockerfile uses a multistage build for a Node.js app:

1. **`base` stage:** Installs dependencies and copies source code, common to both dev and prod.

2. **`development` stage:** Inherits from `base`, sets `CMD` to `npm run dev` for development mode.

3. **`production` stage:** Inherits from `base`, uses `npm prune --production` to remove dev dependencies, sets `CMD` to `npm run start` for production mode.

Build with `docker build --target <stage_name> -t <image_name> `
or Run with `docker run --target <stage_name> -t <image_name> .` (e.g., `--target production`). This creates smaller, more secure production images.

## Key Considerations

- **Naming Stages:** Use `AS <stage_name>` to give names to your stages. This makes the Dockerfile more readable and allows you to reference specific stages in the `COPY --from` instruction.
- **Caching:** Docker caches intermediate stages. If a stage hasn't changed, Docker will reuse the cached image, speeding up builds.
- **Build Arguments:** You can use build arguments (`ARG`) to pass values to your Dockerfile. This can be useful for customizing builds.
- **Target Stage:** You can specify a target stage using the `--target` flag when building the image: `docker build --target <stage_name> .` This is helpful for development where you might want to stop at a particular build stage for debugging.

By using multistage builds, you can create significantly smaller and more secure Docker images, leading to faster deployments and improved application performance. These examples provide a solid foundation for implementing multistage builds in your own Dockerfiles. Remember to tailor the specific commands and dependencies to your application's needs.

# Docker Compose

Docker Compose is a powerful tool within the Docker ecosystem that simplifies the management of multi-container applications. It allows you to define and run complex applications consisting of multiple containers as a single unit.

## Key Concepts

- **YAML file:** Docker Compose uses a YAML file (typically named `docker-compose.yml`) to define the services (containers) that make up your application, their configurations, and their relationships.
- **Services:** Each service in the YAML file represents a container. You define the image to use, ports to expose, volumes to mount, environment variables, and other container-specific settings.
- **Project:** A project is a collection of services defined in a single `docker-compose.yml` file. Compose manages the entire project as a unit.

## Benefits of Using Docker Compose

- **Simplified management:** Manage multiple containers as a single unit, making it easier to start, stop, and scale your application.
- **Configuration as code:** Define your application's architecture and dependencies in a declarative YAML file, ensuring consistency and reproducibility.
- **Isolation and dependencies:** Easily define dependencies between containers, ensuring that services start in the correct order and can communicate with each other.
- **Development and testing:** Quickly spin up complex application environments for development and testing purposes.

- **Automatic Connectivity:** Docker Compose simplifies networking for your multi-container applications. By default, it **automatically creates a network** for your services. This means you typically _don't_ need to explicitly define a network in your `docker-compose.yml` file. All services defined within your Compose file are connected to this default network. This allows them to communicate with each other seamlessly using their service names as hostnames.

## Basic Commands

- **`docker compose up`:** Builds (if necessary) and starts all the services defined in the `docker-compose.yml` file. You can use the `-d` flag to run the containers in detached mode (background).
- **`docker compose -f <docker_compose_file_name> up`:** starts all the services defined in the `<docker_compose_file_name>.yml` file.
- **`docker compose down`:** Stops and removes all the containers, networks, and volumes associated with the project.
- **`docker compose ps`:** Lists the running containers for the project.
- **`docker compose build`:** Builds or rebuilds the images for the services defined in the `docker-compose.yml` file.
- **`docker compose stop`:** Stops the running containers for the project.
- **`docker compose start`:** Starts the stopped containers for the project.
- **`docker compose restart`:** Restarts the containers for the project.
- **`docker compose logs`:** Views the logs from the containers.
- **`docker compose exec`:** Executes a command inside a running container.

## Example `docker-compose.yml` file

```yaml
version: "3.8"
services:
  web:
    image: nginx:latest
    ports:
      - "80:80"
    volumes:
      - ./html:/usr/share/nginx/html
  app:
    build: ./app
    ports:
      - "3000:3000"
    depends_on:
      - db
  db:
    image: postgres:latest
    environment:
      POSTGRES_USER: myuser
      POSTGRES_PASSWORD: password
```

This example defines three services:

- **web:** An Nginx web server that serves static HTML files from the `./html` directory.
- **app:** A custom application built from the `./app` directory, which depends on the `db` service.
- **db:** A PostgreSQL database with specified user and password.

## How to Use

1. Create a directory for your project and place your application code and the `docker-compose.yml` file in it.
2. Navigate to the project directory in your terminal.
3. Run `docker compose up` to start the application.
4. Use other `docker compose` commands to manage your application.

## Additional Tips

- Use volumes to persist data and keep it separate from the containers.
- Use networks to allow containers to communicate with each other.
- Use environment variables to configure your application.

Docker Compose is an essential tool for anyone working with multi-container Docker applications. It simplifies the development, testing, and deployment process, allowing you to focus on building your application rather than managing complex container interactions.

# All Docker Command

## Image Management

- **`docker build [OPTIONS] PATH | URL | -`:** Build an image from a Dockerfile.

  - `-t <image_name>:<tag>`: Tag the image.
  - `--build-arg <ARG>=<VALUE>`: Set build-time variables.
  - `-f <Dockerfile>`: Specify the Dockerfile.
  - `--no-cache`: Do not use the cache.

- **`docker images [OPTIONS] [REPOSITORY[:TAG]]`:** List images.

  - `-a`, `--all`: Show all images.
  - `--filter <filter>`: Filter images.
  - `--format <string>`: Format the output.

- **`docker pull [OPTIONS] IMAGE[:TAG]`:** Pull an image from a registry.

- **`docker push [OPTIONS] IMAGE[:TAG]`:** Push an image to a registry.

- **`docker rmi [OPTIONS] IMAGE[:TAG] [IMAGE[:TAG]...]`:** Remove one or more images.

  - `-f`, `--force`: Force removal.

- **`docker tag IMAGE[:TAG] [REGISTRY]/IMAGE[:TAG]`:** Create a tag for an image.

- **`docker history [OPTIONS] IMAGE[:TAG]`:** Show the history of an image.

## Container Management

- **`docker run [OPTIONS] IMAGE [COMMAND] [ARG...]`:** Create and start a container.

  - `-d`, `--detach`: Run in background.
  - `-e`, `--env <KEY>=<VALUE>`: Set environment variables.
  - `-p`, `--publish <host_port>:<container_port>`: Publish ports.
  - `-v`, `--volume <host_path>:<container_path>`: Bind mount a volume.
  - `--name <name>`: Assign a name.
  - `--network <network>`: Connect to a network. By default it is bridge.
  - `--rm`: Remove on exit.

- `docker exec -it <container_name> bash` : this is use to execute a container.It get me inside the container i.e., I goes in mini machine inside my machine. (here it stands for _interactive_ thats means it connects my terminal to docker terminal.)

- **`docker ps [OPTIONS]`:** List containers.

  - `-a`, `--all`: Show all.
  - `-q`, `--quiet`: Only IDs.

- **`docker start [OPTIONS] CONTAINER [CONTAINER...]`:** Start stopped containers.

- **`docker stop [OPTIONS] CONTAINER [CONTAINER...]`:** Stop running containers.

- **`docker restart [OPTIONS] CONTAINER [CONTAINER...]`:** Restart containers.

- **`docker kill [OPTIONS] CONTAINER [CONTAINER...]`:** Kill running containers.

- **`docker rm [OPTIONS] CONTAINER [CONTAINER...]`:** Remove stopped containers.

  - `-f`, `--force`: Force removal.

- **`docker pause [CONTAINER [CONTAINER...]]`:** Pause containers.

- **`docker unpause [CONTAINER [CONTAINER...]]`:** Unpause containers.

- **`docker attach [OPTIONS] CONTAINER`:** Attach to a running container.

- **`docker exec [OPTIONS] CONTAINER [COMMAND] [ARG...]`:** Execute a command in a container.

  - `-it`: Interactive terminal.

- **`docker logs [OPTIONS] CONTAINER`:** Fetch container logs.

- **`docker inspect [OPTIONS] CONTAINER [CONTAINER...]`:** Inspect containers.

- **`docker stats [OPTIONS] [CONTAINER...]`:** Container resource usage stats.

- **`docker top CONTAINER [OPTIONS]`:** Container processes.

- **`docker cp [CONTAINER:]SRC_PATH DEST_PATH`:** Copy files between container and host.

## Network Management

- **`docker network ls [OPTIONS]`:** List networks.

- **`docker network create [OPTIONS] <network_type> <custom_name>`:** It create your custom network.

- **`docker network connect [OPTIONS] NETWORK CONTAINER`:** Connect container to network.

- **`docker network disconnect [OPTIONS] NETWORK CONTAINER`:** Disconnect container from network.

- **`docker network inspect [OPTIONS] NETWORK [NETWORK...]`:** Inspect networks. Shows all details about that network.

- **`docker network rm NETWORK [NETWORK...]`:** Remove networks.

- **`docker network prune [OPTIONS]`:** Remove unused networks.

## Volume Management

- **`docker volume ls [OPTIONS]`:** List volumes.

- **`docker volume create [OPTIONS] [VOLUME]`:** Create a volume.

- **`docker volume inspect [OPTIONS] [VOLUME...]`:** Inspect volumes.

- **`docker volume rm [VOLUME...]`:** Remove volumes.

- **`docker volume prune`:** Remove unused volumes.

- **`docker volume cp [CONTAINER:]SRC_PATH DEST_PATH`:** Copy files between container and volume (Deprecated, use `docker cp`).

## Docker Compose

- **`docker-compose up [OPTIONS]`:** Create and start containers.

- **`docker-compose down [OPTIONS]`:** Stop and remove containers.

- **`docker-compose build [OPTIONS]`:** Build services.

- **`docker-compose ps [OPTIONS]`:** List containers.

- **`docker-compose logs [OPTIONS] [SERVICE...]`:** View container output.

- **`docker-compose exec [OPTIONS] SERVICE COMMAND [ARGS...]`:** Execute command in container.

## Docker Swarm

- **`docker swarm init [OPTIONS]`:** Initialize Swarm manager.

- **`docker swarm join [OPTIONS]`:** Join Swarm.

- **`docker swarm update [OPTIONS]`:** Update Swarm.

- **`docker swarm leave [OPTIONS]`:** Leave Swarm.

- **`docker node ls [OPTIONS]`:** List Swarm nodes.

- **`docker service create [OPTIONS] IMAGE [COMMAND] [ARG...]`:** Create service.

- **`docker service update [OPTIONS] SERVICE`:** Update service.

- **`docker service ls [OPTIONS]`:** List services.

- **`docker service rm SERVICE [SERVICE...]`:** Remove services.

- **`docker service scale SERVICE=REPLICAS`:** Scale service.

- **`docker service inspect [OPTIONS] SERVICE [SERVICE...]`:** Inspect services.

- **`docker service logs [OPTIONS] SERVICE`:** Get service logs.

- **`docker stack deploy [OPTIONS] STACK_NAME`:** Deploy stack.

- **`docker stack ls [OPTIONS]`:** List stacks.

- **`docker stack rm STACK_NAME`:** Remove stack.

## Other Commands

- **`docker login [OPTIONS] [SERVER]`:** Log in to registry.

- **`docker logout [SERVER]`:** Log out from registry.

- **`docker pull <image_name>`** : it pulls image from docker hub.
- **`docker push <image_name>`** : it pushes image to docker hub.

- **`docker info`:** System information.

- **`docker version`:** Docker version.

- **`docker search [OPTIONS] TERM`:** Search images on Docker Hub.

- **`docker system df [OPTIONS]`:** Disk usage.

- **`docker system prune [OPTIONS]`:** Remove unused data.

- **`docker help [COMMAND]`:** Get help on a command.
