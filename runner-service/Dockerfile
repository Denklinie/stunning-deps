# build stage
FROM golang:1.26-alpine as builder

ENV GOFLAGS=-mod=vendor

RUN apk add --no-cache tesseract-ocr-dev leptonica-dev gcc g++ make libc-dev

WORKDIR /app
COPY go.mod go.sum ./
COPY vendor/ ./vendor/
COPY . .

ENV GOFLAGS=-mod=vendor
RUN CGO_ENABLED=1 GOOS=linux go build -o /runner-service ./cmd/runner

# final stage
FROM alpine:latest
RUN apk add --no-cache tesseract-ocr tesseract-ocr-data-eng
WORKDIR /
COPY --from=builder /runner-service /runner-service
EXPOSE 8083
ENTRYPOINT [ "/runner-service" ]
