# ---------- Build Stage ----------
ARG NODE_VERSION=18
FROM node:${NODE_VERSION} AS builder

LABEL stage=builder

WORKDIR /app

COPY package.json package-lock.json ./

RUN npm install

COPY src ./src
COPY public ./public

RUN npm run build

# ---------- Runtime Stage ----------
FROM nginx:alpine

LABEL maintainer="Fahad Jaseem"
LABEL version="1.0"
LABEL description="Employee Management Frontend"

ENV NGINX_PORT=80

COPY --from=builder /app/build /usr/share/nginx/html

EXPOSE 80

HEALTHCHECK --interval=30s --timeout=5s --retries=3 \
CMD wget --spider http://localhost:${NGINX_PORT} || exit 1

CMD ["nginx", "-g", "daemon off;"]
