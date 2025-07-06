# About
A collections of Dockerfile samples for building various projects.

## Explanation

This repository contains Dockerfile samples for building different types of projects using Docker. Each sample is designed to demonstrate best practices and efficient Dockerfile usage for specific technologies or frameworks.

You will find that all `Dockerfile` samples use a remote source code repository for demonstration purposes. In practice, you should modify the `RUN --mount` command to point to your local source code directory.

Here an example of the remote source code repository:

```dockerfile
FROM scratch AS source
ADD https://github.com/socheatsok78/docker-samples.git#examples/node/pnpm/vite /
```

And later in the `Dockerfile`, you may see a command like this:

```dockerfile
RUN --mount=type=bind,from=source,source=/,target=/tmp/source,readwrite=true \
    # ...
```

This command mounts the source code from the `source` stage into the build context, allowing you to access the sample source files during the build process. 
For your own projects, modify the `RUN --mount=type=bind` instruction to point to your local source code directory. 

First, remove the `from=source` argument and set the `source=/` to your local path. For example, if your source code is in `/path/to/your/source`, change it to `source=/path/to/your/source`. You can also use relative paths if your Docker build context is set up correctly. Or alternatively you can also omit the `source=/` argument, which will use the current build context as the source.
