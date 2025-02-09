FROM node:18-alpine as builder

RUN apk add --no-cache bash

WORKDIR /app

COPY package.json package-lock.json ./

ENV CI=1

RUN npm ci --legacy-peer-deps

COPY ./nginx /nginx
COPY ./assets /assets
COPY ./locale /locale
COPY ./scripts /scripts
COPY ./src /src
COPY ./vite.config.js /vite.config.js
COPY ./tsconfig.json /tsconfig.json
COPY ./*.d.ts ./
COPY ./schema.graphql /schema.graphql
COPY ./introspection.json /introspection.json
COPY ./introspection*.json /introspection*.json
COPY ./.featureFlags /featureFlags

ARG API_URL
ARG APP_MOUNT_URI
ARG APPS_MARKETPLACE_API_URL
ARG APPS_TUNNEL_URL_KEYWORDS
ARG STATIC_URL
ARG SKIP_SOURCEMAPS
ARG LOCALE_CODE

ENV API_URL=${API_URL:-http://localhost:8000/graphql/}
ENV APP_MOUNT_URI=${APP_MOUNT_URI:-/dashboard/}
ENV APPS_MARKETPLACE_API_URL=${APPS_MARKETPLACE_API_URL:-https://apps.saleor.io/api/v2/saleor-apps}
ENV APPS_TUNNEL_URL_KEYWORDS=${APPS_TUNNEL_URL_KEYWORDS}
ENV STATIC_URL=${STATIC_URL:-/dashboard/}
ENV SKIP_SOURCEMAPS=${SKIP_SOURCEMAPS:-true}
ENV LOCALE_CODE=${LOCALE_CODE:-EN}

RUN npm run build

FROM nginx:stable-alpine as runner

WORKDIR /app

COPY --from=builder /app/build/ /app/

COPY ./nginx/default.conf /etc/nginx/conf.d/default.conf

COPY ./nginx/replace-env-vars.sh /docker-entrypoint.d/50-replace-env-vars.sh

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]