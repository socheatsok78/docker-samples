# Stage 1: Fetch the source code
# NOTE: The `source` stage is only used in this example to fetch the source code from a Git repository.
#       For your own projects, you should modify the `RUN --mount` command to point to your local source code directory.
FROM scratch AS source
ADD https://github.com/socheatsok78/docker-samples.git#examples/vite-vanilla-project /

# Stage 2: Build the Vite application
# This stage uses Node.js with pnpm to build the Vite application.
# It installs pnpm using corepack, sets up a cache directory for pnpm store,
# and then builds the application, outputting the build files to a temporary directory.
FROM node:latest AS build

RUN set -xe \
    && mkdir -p /tmp/npm-cache \
    && npm config set cache /tmp/npm-cache \
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
    --mount=type=cache,target=/tmp/npm-cache \
    <<EOF
    set -xe

    # NOTE: This example repo does not contain a lock file, so we will use `install` command instead of `ci`.
    #       If your project has a lock file, use `npm ci` instead of `npm install`.
    #       This will ensure that the exact versions of dependencies are installed.
    #       e.g: npm ci
    npm install

    # Build the application.
    # NOTE: NPM will silently swallowing the `--outDir` option, so we need to use `--` to pass it to the build script.
    npm run build -- --outDir /tmp/dist
EOF

# Stage 3: Serve the built application with Nginx
# This stage copies the built files from the previous stage and serves them using Nginx.
FROM nginx:latest
COPY --link --from=build /tmp/dist /usr/share/nginx/html
