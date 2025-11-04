# ---- Build Stage ----
FROM golang:1.21-alpine AS build
WORKDIR /app

# Copy go.mod and go.sum, then download dependencies
COPY go.mod go.sum ./
RUN go mod download

# Copy the rest of the source code
COPY . .

# Build the Go binary
RUN go build -o server main.go

# ---- Run Stage ----
FROM alpine:latest
WORKDIR /app

# Copy the binary from the builder stage
COPY --from=build /app/server .

# Expose backend service port (internal port)
EXPOSE 8080

# Run the app
CMD ["./server"]

