# Stage 1: Fetch the source code
# NOTE: The `source` stage is only used in this example to fetch the source code from a Git repository.
#       For your own projects, you should modify the `RUN --mount` command to point to your local source code directory.
FROM scratch AS source
ADD https://github.com/socheatsok78/docker-samples.git#examples/vite-vanilla-project /

# Stage 2: Build the Vite application
# This stage uses Node.js with pnpm to build the Vite application.
# It installs pnpm using corepack, sets up a cache directory for pnpm store,
# and then builds the application, outputting the build files to a temporary directory.
# 
# NOTE: For most web applications, you can use `--platform=$BUILDPLATFORM` to build the application using the host architecture.
#       This is useful for multi-platform builds, if there are no architecture-specific dependencies.
#       If your application has architecture-specific dependencies, you need to remove the `--platform=$BUILDPLATFORM` option
#       and build the application for the target architecture.
FROM --platform=$BUILDPLATFORM node:latest AS build

RUN set -xe \
    && mkdir -p /tmp/pnpm-store \
    && npm install --global corepack@latest \
    && corepack enable \
    && corepack prepare pnpm@latest-10 --activate \
    && pnpm config set store-dir /tmp/pnpm-store \
    && mkdir -p /tmp/dist /tmp/source

WORKDIR /tmp/source

# TODO: Modify the `RUN --mount=type=bind` instruction to point to your local source code directory.
#       Remove the `from=source` argument and set the `source=/` to your local path.
#
#       For example, if your source code is in `/path/to/your/source`, change it to `source=/path/to/your/source`.
#       You can also use relative paths if your Docker build context is set up correctly.
#
#       Or you can also omit the `source=/` argument, which will use the current build context as the source.
RUN --mount=type=bind,from=source,source=/,target=/tmp/source,readwrite=true \
    --mount=type=cache,target=/tmp/pnpm-store \
    <<EOF
    set -xe

    # NOTE: This example repo does not contain a lock file.
    #       If your project has a lock file, please specify the `--frozen-lockfile --prefer-offline` options to `pnpm install` command.
    #       This will ensure that the exact versions of dependencies are installed.
    #       e.g: pnpm install --frozen-lockfile --prefer-offline
    pnpm install

    # Build the application.
    pnpm run build --outDir /tmp/dist
EOF

# Stage 3: Serve the built application with Nginx
# This stage copies the built files from the previous stage and serves them using Nginx.
FROM nginx:latest
COPY --link --from=build /tmp/dist /usr/share/nginx/html
