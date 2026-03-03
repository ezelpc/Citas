# Build stage
FROM node:18-bullseye AS builder

WORKDIR /app

COPY package.json package-lock.json* ./

RUN npm ci

COPY . .

RUN npm run build

# Production stage
FROM node:18-bullseye

WORKDIR /app

COPY package.json package-lock.json* ./

RUN npm ci --only=production

COPY --from=builder /app/dist ./dist

EXPOSE 3000

CMD ["node", "dist/main"]
