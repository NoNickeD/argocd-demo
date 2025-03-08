# Stage 1: Build the Go application
FROM golang:1.23.5-alpine AS build

# Install necessary build dependencies
RUN apk add --no-cache git

# Set working directory
WORKDIR /src

# Copy Go modules files first for better caching
COPY go.mod go.sum ./

# Download dependencies before copying source code
RUN go mod tidy

# Copy the source code
COPY . . 

# Build the Go application
RUN CGO_ENABLED=0 GOOS=linux go build -o echo-server

# Stage 2: Create a minimal runtime image
FROM scratch

# Copy the compiled binary from the build stage
COPY --from=build /src/echo-server /usr/local/bin/echo-server

# Expose application port
EXPOSE 8080

# Run the application
CMD ["/usr/local/bin/echo-server"]
