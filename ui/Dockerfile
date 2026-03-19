# Dockerfile for UI (Next.js)

# Builder stage
FROM node:20-alpine AS builder

ARG API_GATEWAY_URL
ENV API_GATEWAY_URL=${API_GATEWAY_URL}

WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Production stage
FROM node:20-alpine
WORKDIR /app

COPY --from=builder /app/public ./public
COPY --from=builder /app/.next ./.next
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/package.json ./package.json

EXPOSE 3000
CMD ["npm", "start"]
