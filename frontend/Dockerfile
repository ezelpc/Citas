# Build stage
FROM node:18-bullseye AS builder

WORKDIR /app

COPY package.json package-lock.json* ./

RUN npm ci

COPY . .

RUN npm run build:web

# Production stage
FROM node:18-bullseye

WORKDIR /app

RUN npm install -g expo-cli

COPY package.json package-lock.json* ./

RUN npm ci --only=production

COPY --from=builder /app/dist ./dist

EXPOSE 8081

CMD ["expo", "start", "--web"]
