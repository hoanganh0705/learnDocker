docker use layers to optimize image storage and transfer. Each instruction in a Dockerfile creates a new layer in the image. When you build or pull an image, Docker only needs to download the layers that are not already present on your system, which saves time and bandwidth.

When you rebuild an image, Docker reuses the layers that haven't changed, which speeds up the build process. This layer caching mechanism is particularly useful during development when you frequently make changes to your Dockerfile.

use docker attach to connect to a running container's console. This allows you to interact with the container as if you were directly connected to it. For example, if you have a container running a shell, you can use `docker attach <container_id>` to access that shell.

use docker logs to view the output of a running or stopped container. This is especially useful for debugging purposes, as it allows you to see what is happening inside the container without needing to attach to it. This command also shows previous logs if the container has been restarted.

you can use -a tag with docker start to attach to the container's console immediately after starting it. For example, `docker start -a <container_id>` will start the container and attach your terminal to it, allowing you to see its output in real-time.

to type input into a running container, you can use docker exec with the -it flags. This allows you to run an interactive command inside the container. For example, `docker exec -it <container_id> /bin/sh` will open a shell session inside the container, allowing you to type commands directly into it.

use docker stop $(docker ps -q) to stop all running containers at once. The command `docker ps -q` lists the IDs of all currently running containers, and passing that list to `docker stop` will stop each of them. This is a quick way to halt multiple containers without needing to stop them individually.

use docker stop $(docker ps -q --filter ancestor=myapp) to stop all running containers that were created from the image named "myapp". The `--filter ancestor=myapp` option filters the list of running containers to only include those that were started from the specified image. This is useful for managing containers associated with a specific application or service.

docker stop $(docker ps -aq) to stop all containers, both running and stopped. The command `docker ps -aq` lists the IDs of all containers on the system, regardless of their state. Passing that list to `docker stop` will attempt to stop each container. Note that stopping a container that is already stopped will have no effect, but it is safe to run this command without causing errors.

use docker image prune -a to remove all unused images from your system. This command deletes images that are not currently associated with any containers, freeing up disk space. The `-a` flag ensures that all unused images are removed, not just dangling ones. Be cautious when using this command, as it will permanently delete images that you may want to keep for future use.

add --rm flag when running a container with docker run to automatically remove the container once it stops. This is useful for temporary containers that you don't need to keep after they have finished executing, helping to keep your system clean and free of unused containers. For example, `docker run --rm myimage` will run the container and delete it as soon as it exits.

use docker image inspect <image_id> to view detailed information about a specific Docker image. This command provides metadata about the image, including its layers, creation date, size, and configuration details. It is useful for understanding the structure and properties of an image, especially when troubleshooting or optimizing your Docker setup.

use docker cp to copy files or directories between your host machine and a Docker container. This command allows you to transfer data in and out of containers without needing to set up shared volumes. For example, `docker cp <container_id>:/path/to/file /host/path` copies a file from the container to the host, while `docker cp /host/path <container_id>:/path/to/destination` copies a file from the host to the container.

use docker cp when you need to extract logs or configuration files from a container for analysis or backup. This is particularly useful when you want to preserve important data before removing a container or when you need to share files between the host and the container for development purposes.

docker tags include 2 parts: the repository name and the tag name, separated by a colon. For example, in the tag `myapp:latest`, "myapp" is the repository name, and "latest" is the tag name. Tags are used to identify different versions of an image, allowing you to manage and deploy specific builds easily.

there are 2 ways to share docker images: by pushing them to a container registry (like Docker Hub) or by saving them as tar files. Pushing to a registry allows others to pull the image easily, while saving as a tar file enables you to transfer the image manually via file sharing methods.

We can use docker save to export a Docker image to a tar file. This is useful for backing up images or transferring them to another system without using a container registry. The command `docker save -o <filename>.tar <image_name>` creates a tar file containing the specified image.

We can create anonymous volumes in a Dockerfile using the VOLUME instruction. This instruction specifies a mount point within the container where data can be stored. When a container is created from the image, Docker automatically creates an anonymous volume at that location to persist data.

We can not create named volumes in a Dockerfile because named volumes are managed by the Docker daemon and are created at runtime, not during the image build process. The Dockerfile is used to define the image's filesystem and configuration, but it does not have the capability to create or manage volumes, which are external to the image itself. Named volumes are typically created and specified when running a container using the `docker run` command with the `-v` or `--mount` options.

To create or load a named volume while running a container, you can use the `-v` or `--mount` flag with the `docker run` command. For example, to create a named volume called "mydata" and mount it to the `/data` directory in the container, you can use the following command:

```docker run -v mydata:/data myimage

```

To load an existing named volume, you can use the same command, and Docker will automatically use the existing volume if it already exists:

````docker run -v mydata:/data myimage
```This way, you can create or load named volumes when starting a container.

To create a bind mount while running a container, you can use the `-v` or `--mount` flag with the `docker run` command. A bind mount allows you to mount a directory from your host machine into the container. For example, to mount the host directory `/path/on/host` to the container directory `/path/in/container`, you can use the following command:
```docker run -v /path/on/host:/path/in/container myimage
````

Alternatively, using the `--mount` flag:

````docker run --mount type=bind,source=/path/on/host,target=/path/in/container myimage
```This way, you can create a bind mount to share files between the host and the conta  iner.
````

-v tag has 2 ways to use: use to create a new volume by copying the data from the image to the volume, or use to mount an existing volume or host directory into the container.

When you use `-v /container/path`, Docker creates a new anonymous volume and copies the data from the image at that path into the volume.

When you use `-v volume_name:/container/path` or `-v /host/path:/container/path`, Docker mounts the specified existing volume or host directory into the container at the specified path, without copying any data from the image.

ro means read-only. When you mount a volume or bind mount with the `:ro` option, the container can only read data from that mount point and cannot make any changes to it. This is useful for protecting data that should not be modified by the container, ensuring that the original data remains intact. For example, using `-v /host/path:/container/path:ro` mounts the host directory as read-only inside the container.

ro restricts the container's ability to modify the data in the mounted volume or bind mount, providing an additional layer of data protection.

we can set environment variables in 2 ways: in the Dockerfile using the ENV instruction, or at runtime using the -e or --env flag with the docker run command. For example, in a Dockerfile, you can set an environment variable like this:

```ENV MY_VAR=value

```

At runtime, you can set an environment variable like this:

```
docker run -e MY_VAR=value myimage
```

or in a third way, we can use --env-file to load environment variables from a file when running a container. This method allows you to specify a file containing key-value pairs of environment variables, which Docker will read and set in the container. For example, if you have a file named `env.list` with the following content:

```MY_VAR1=value1
MY_VAR2=value2
```

You can run a container and load these environment variables using the following command:

```docker run --env-file ./.env myimage

```

We can use ARG to define build-time variables in a Dockerfile. These variables can be passed during the build process using the --build-arg flag with the docker build command. ARG variables are only available during the image build and are not accessible in the running container. For example, in a Dockerfile, you can define an ARG variable like this:

`````ARG PORT=80

```Then, when building the image, you can override the default value like this:
```docker build --build-arg PORT=8080 -t myimage .
````This allows you to customize the build process based on different parameters.
`````

We can use host.docker.internal to connect from a Docker container to services running on the host machine. This special DNS name resolves to the internal IP address of the host, allowing containers to access host services without needing to know the host's actual IP address. This is particularly useful for development and testing scenarios where you want a containerized application to communicate with a database or API running on the host. For example, if you have a database running on the host at port 27017, you can connect to it from a container using the connection string `mongodb://host.docker.internal:27017`.

And to connect to the public API from the container, you can simply use the standard public URL or IP address of the API, as the container has access to the internet by default. For example, if you want to connect to a public API at `https://api.example.com`, you can use that URL directly in your application code running inside the container.

if you want to connect current container to another container, you can use docker inspect to find the IP address of the target container and use that IP address in your current container to establish a connection. You can get the IP address by running the following command:

```docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' <target_container_name_or_id>

```

For example, if you have a MongoDB container named "mongodb-container", you can find its IP address and use it to connect from your application container.
mongoose.connect(
'mongodb://<mongodb-container-ip>:27017/swfavorites',
{ useNewUrlParser: true },
(err) => {
if (err) {
console.error('Failed to connect to MongoDB', err);
} else {
console.log('Connected to MongoDB');
app.listen(3000, () => {
console.log('Server is running on port 3000');
});
}
}
);

unlike volumes, networks can not be automatically created from a docker run command. Networks must be created explicitly using the docker network create command before they can be used by containers. Once a network is created, you can connect containers to it using the --network flag when running or creating containers.

Docker net work is used for inter-container communication. It allows containers to communicate with each other within the same network, enabling them to share data and services securely. Networks provide isolation between different sets of containers, ensuring that only containers connected to the same network can interact with each other. This is particularly useful for microservices architectures, where different services need to communicate while remaining isolated from other services.

to run a container and connect it to a specific network, you can use the --network flag with the docker run command. For example, if you have a network named "my-network" and you want to run a container from the "myapp" image and connect it to that network, you can use the following command:

````docker run --network my-network myapp

```This will start the container and connect it to the specified network, allowing it to communicate with other containers on the same network.
````

when 2 containers are on the same user-defined network, they can communicate with each other using their container names as hostnames. Docker's built-in DNS service resolves these names to the appropriate IP addresses within the network. For example, if you have a container named "webapp" and another named "database" on the same network, the "webapp" container can connect to the "database" container using the hostname "database". This makes it easy to set up inter-container communication without needing to know the specific IP addresses of the containers.
'mongodb://mongodb:27017/swfavorites',

By default, when you use docker-compose your services will be down when you stop the docker-compose process. To keep the services running in the background, you can use the -d (detached) flag when starting your docker-compose setup. For example:

````docker-compose up -d
```This command will start all the services defined in your docker-compose.yaml file and run them in the background, allowing you to continue using your terminal while the services are running
````

in docker compose file, you need to use a dash whenever you have a single value in list and no dash when you have key-value pairs in a map. For example, in the volumes section, each volume is listed with a dash:
volumes:

- mongo-data:/data/db

When you use docker compose, networks are created automatically for your services. By default, docker compose creates a single network for all the services defined in the docker-compose.yaml file. Each service can communicate with other services on the same network using their service names as hostnames. This automatic network creation simplifies inter-service communication and isolation.

you must declare volumes in the volumes section at the bottom of the docker-compose.yaml file to use named volumes. This section defines the named volumes that can be referenced by services in the compose file. For example:
volumes:
mongo-data:
driver: local
This declaration creates a named volume called "mongo-data" that can be used by services in the docker-compose.yaml file.

If you use the same named volume across multiple services in a docker-compose file, the data stored in that volume will be shared among those services. This means that any changes made to the data by one service will be reflected in the other services that use the same volume. This is useful for sharing persistent data, such as databases or configuration files, between different services in your application.

build options in a docker-compose file allows you to specify how to build a Docker image for a service. You can define the context (the directory containing the Dockerfile), the Dockerfile name, and any build arguments needed for the build process. This is useful when you want to create custom images for your services directly from the compose file.

we just need to use relative paths instead of absolute path when bind mounting host directories in a docker-compose file. Relative paths are based on the location of the docker-compose.yaml file, making it easier to manage and share the compose file across different environments without needing to change absolute paths.

you don't need to add bindmount paths in the volumes section at the bottom of the docker-compose.yaml file. Bind mounts are specified directly within the service definition under the volumes key.

you also don't need to add anonymous volumes in the volumes section at the bottom of the docker-compose.yaml file. Anonymous volumes are created automatically by Docker when you specify a volume without a name in the service definition. They do not require explicit declaration in the volumes section.

depends_on in a docker-compose file is used to specify the order in which services should be started. It ensures that a service will only start after the services it depends on have been started. However, it does not wait for the dependent services to be fully ready or healthy; it only ensures that they have been started. For example, if a web service depends on a database service, you can use depends_on to ensure that the database service starts before the web service.
services:
web:
depends_on: - db
db:
image: mongo

This configuration ensures that the "db" service starts before the "web" service.

depends_on is only available in docker compose, we can't use it in plain docker run commands or dockerfiles. It is a feature specific to docker compose that helps manage the startup order of services defined in a docker-compose.yaml file.

if your service use a db service, in your connection string, you should use the service name of the db service as the hostname. For example, if your db service is named "mongodb" in your docker-compose file, your connection string should look like this:
'mongodb://mongodb:27017/swfavorites',
This allows your application service to connect to the database service using Docker's internal DNS resolution. And in this case, the name of the container does not matter, only the service name defined in the docker-compose file is important for inter-service communication.

in docker compose up command we can add --build to force rebuild of the images before starting the containers. This is useful when you have made changes to the Dockerfile or the application code and want to ensure that the latest version is used. For example:

````docker-compose up --build
```This command will rebuild the images for all services defined in the docker-compose.yaml file before starting the containers.
````

you can only build images not trigger a container by using docker compose build command. This command reads the docker-compose.yaml file and builds the images for the services defined in it, but it does not start any containers. For example:

````docker-compose build
```This command will build the images for all services without starting the containers.
````

You can assign the container name in docker compose file using the container_name key under the service definition. For example:
services:
web:
container_name: my-web-container
image: my-web-image
This configuration will create a container named "my-web-container" when the web service is started.
