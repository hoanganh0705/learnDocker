docker use layers to optimize image storage and transfer. Each instruction in a Dockerfile creates a new layer in the image. When you build or pull an image, Docker only needs to download the layers that are not already present on your system, which saves time and bandwidth.

When you rebuild an image, Docker reuses the layers that haven't changed, which speeds up the build process. This layer caching mechanism is particularly useful during development when you frequently make changes to your Dockerfile.
