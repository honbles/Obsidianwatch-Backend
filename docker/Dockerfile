# ── Build stage ─────────────────────────────────────────────────────────────
FROM golang:1.22-alpine AS builder

WORKDIR /build

# Download dependencies first (cached layer)
COPY go.mod go.sum ./
RUN go mod download

# Copy source and build
COPY . .
RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 \
    go build -ldflags="-s -w" -o server ./cmd/server

# ── Runtime stage ────────────────────────────────────────────────────────────
FROM alpine:3.19

RUN apk add --no-cache ca-certificates tzdata

WORKDIR /app

# Copy binary and default config
COPY --from=builder /build/server .
COPY configs/server.yaml .

# Create certs directory (mount your certs here at runtime)
RUN mkdir -p certs

EXPOSE 8443

ENTRYPOINT ["./server", "-config", "server.yaml"]
