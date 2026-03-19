# Multi-stage Docker build for API Gateway

# Build stage
FROM golang:1.24-alpine AS builder

RUN apk add --no-cache git ca-certificates tzdata fossil

WORKDIR /app

COPY go.mod go.sum ./
COPY vendor/ ./vendor/

COPY . .

RUN chmod 644 /app/.local.private.pem /app/.local.public.pem

# Build the application (main.go is the entrypoint)
ENV GOFLAGS=-mod=vendor
RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build \
    -ldflags='-w -s -extldflags "-static"' \
    -o api-gateway \
    ./cmd/api/main.go

# Create the directories to be used by the application
# This directories will be copied into the final image with correct permissions.
RUN mkdir /app/versioned_repos /app/tmp

# Final stage
FROM alpine:latest

COPY --from=builder /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/
COPY --from=builder /usr/share/zoneinfo /usr/share/zoneinfo
COPY --from=builder /usr/bin/fossil /usr/bin/fossil
COPY --from=builder /app/api-gateway /api-gateway
COPY --from=builder /app/.local.private.pem /.local.private.pem
COPY --from=builder /app/.local.public.pem /.local.public.pem
COPY --from=builder --chown=65534:65534 /app/versioned_repos /app_data/versioned_repos
COPY --from=builder --chown=65534:65534 /app/tmp /app_data/tmp

ENV PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
ENV HOME="/app_data"

EXPOSE 8080

USER 65534:65534

ENTRYPOINT ["/api-gateway"]
