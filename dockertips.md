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
