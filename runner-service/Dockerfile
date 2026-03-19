# build stage
FROM golang:1.24-alpine as builder

ENV GOFLAGS=-mod=vendor

WORKDIR /app
COPY go.mod go.sum ./
COPY vendor/ ./vendor/
COPY . .

ENV GOFLAGS=-mod=vendor
RUN go build -o /runner-service ./cmd/runner

# final stage
FROM alpine:latest
WORKDIR /
COPY --from=builder /runner-service /runner-service
EXPOSE 8083
ENTRYPOINT [ "/runner-service" ]
