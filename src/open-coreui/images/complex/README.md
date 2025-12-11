# Open CoreUI Backend Image

Custom Docker image for Open CoreUI backend with multi-architecture support and version parameterization.

## Features

- **Multi-architecture support**: Automatically builds for `amd64` (x86_64) and `arm64` (aarch64)
- **Version parameterization**: Specify any release version via build argument
- **Minimal footprint**: Alpine-based multi-stage build
- **Security**: Runs as non-root user
- **Port**: Exposes port 8168

## Quick Start

### Build the image

Build with default version (0.9.6):

```bash
cd src/open-coreui/images/complex
docker build -t open-coreui .
```

Build with specific version:

```bash
docker build --build-arg VERSION=0.9.6 -t open-coreui:0.9.6 .
```

### Run the container

```bash
docker run -d \
  --name open-coreui \
  -p 8168:8168 \
  open-coreui:0.9.6
```

## Build Arguments

| Argument | Description | Default |
|----------|-------------|---------|
| `VERSION` | Open CoreUI release version | `0.9.6` |
| `TARGETARCH` | Target architecture (auto-detected by Docker) | - |

## Supported Architectures

The image automatically detects and builds for the following architectures:

- `linux/amd64` (x86_64)
- `linux/arm64` (aarch64)

## Multi-Architecture Build

To build for multiple architectures using Docker Buildx:

```bash
# Create a new builder instance (one-time setup)
docker buildx create --name multiarch --use

# Build for multiple platforms
docker buildx build \
  --platform linux/amd64,linux/arm64 \
  --build-arg VERSION=0.9.6 \
  -t open-coreui:0.9.6 \
  --push .
```

## Usage Examples

### Basic usage

```bash
docker run -p 8168:8168 open-coreui:0.9.6
```

### With custom configuration

```bash
docker run -d \
  --name open-coreui \
  -p 8168:8168 \
  -v $(pwd)/config:/config \
  open-coreui:0.9.6
```

### Docker Compose integration

```yaml
services:
  open-coreui:
    image: open-coreui:0.9.6
    container_name: open-coreui
    restart: unless-stopped
    ports:
      - "8168:8168"
    networks:
      - app-network

networks:
  app-network:
    name: app-network
```

## Binary Source

The image downloads pre-compiled binaries from the official Open CoreUI releases:

- Repository: <https://github.com/xxnuo/open-coreui>
- Release pattern: `https://github.com/xxnuo/open-coreui/releases/download/v{VERSION}/open-coreui-{VERSION}-{ARCH}-unknown-linux-gnu`

## Development

### Testing locally

```bash
# Build
docker build -t open-coreui:test .

# Run
docker run --rm -p 8168:8168 open-coreui:test

# Test the endpoint
curl http://localhost:8168
```

### Building different versions

```bash
# Build version 0.9.5
docker build --build-arg VERSION=0.9.5 -t open-coreui:0.9.5 .

# Build version 0.9.6
docker build --build-arg VERSION=0.9.6 -t open-coreui:0.9.6 .
```

## Troubleshooting

### Architecture mismatch

If you encounter architecture-related errors, ensure you're using Docker BuildKit:

```bash
export DOCKER_BUILDKIT=1
docker build -t open-coreui .
```

### Version not found

If a specific version fails to download, verify it exists in the releases:

```bash
# Check available releases
curl -s https://api.github.com/repos/xxnuo/open-coreui/releases | grep tag_name
```

### Permission issues

The container runs as user `opencoreui` (UID 1000). Ensure mounted volumes have appropriate permissions:

```bash
chown -R 1000:1000 /path/to/volume
```

## License

This Dockerfile is part of the open-coreui-stack project. See the main repository for license information.
