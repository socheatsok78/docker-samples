FROM scratch AS source
ADD https://github.com/socheatsok78/docker-samples.git#examples/node/pnpm/vite /

FROM node:latest AS build

RUN set -xe \
    && mkdir -p /tmp/pnpm-store \
    && npm install --global corepack@latest \
    && corepack enable \
    && corepack prepare pnpm@latest-10 --activate \
    && pnpm config set store-dir /tmp/pnpm-store

WORKDIR /tmp/dist
WORKDIR /tmp/source
RUN --mount=type=bind,from=source,source=/,target=/tmp/source,readwrite=true \
    --mount=type=cache,target=/tmp/pnpm-store \
    <<EOF
    set -xe
    pnpm install --frozen-lockfile --prefer-offline
    pnpm run build --outDir /tmp/dist
EOF

FROM nginx:latest
COPY --link --from=build /tmp/dist /usr/share/nginx/html
