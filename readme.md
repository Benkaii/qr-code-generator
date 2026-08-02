# Dockerized QR Code Generator

This project is a Python QR Code Generator packaged and executed using Docker. The application accepts a URL through a command-line argument, validates the URL, generates a QR code, and saves the resulting PNG file in the `qr_codes` directory.

This project was completed for Module 7 of IS 601 and demonstrates Docker image creation, container security, Docker Compose, environment-variable configuration, volume mounts, DockerHub publishing, and GitHub Actions automation.

## Features

- Generates a QR code from a supplied URL
- Validates URLs before generating the QR code
- Creates timestamped PNG filenames
- Supports configurable foreground and background colors
- Uses environment variables for application configuration
- Stores generated images in the `qr_codes` directory
- Runs inside a Docker container as a non-root user
- Supports Docker Compose
- Includes a GitHub Actions workflow for Docker image validation
- Published as a public DockerHub image

## Project Structure

```text
qr-code-generator/
├── .github/
│   └── workflows/
│       └── docker-build.yml
├── qr_codes/
├── .dockerignore
├── .gitignore
├── Dockerfile
├── docker-compose.yml
├── main.py
├── readme.md
└── requirements.txt
```

## Application Dependencies

The application uses the following Python packages:

- `qrcode`
- `Pillow`
- `python-dotenv`
- `validators`
- `pypng`
- `typing_extensions`

The required versions are listed in `requirements.txt`.

## Configuration

The application supports the following environment variables:

| Variable | Default | Purpose |
|---|---|---|
| `QR_CODE_DIR` | `qr_codes` | Directory used to save generated QR codes |
| `FILL_COLOR` | `red` | Foreground color of the QR code |
| `BACK_COLOR` | `white` | Background color of the QR code |

Example:

```text
QR_CODE_DIR=qr_codes
FILL_COLOR=blue
BACK_COLOR=white
```

## Build the Docker Image

From the project root, run:

```bash
docker build -t qr-code-generator-app .
```

Verify the image:

```bash
docker images
```

## Run the Docker Container

Run the application using the default URL:

```bash
docker run --name qr-generator qr-code-generator-app
```

Run the application with a custom URL:

```bash
docker run --name qr-generator \
  qr-code-generator-app \
  --url https://www.njit.edu
```

The application generates one QR code and then exits. An exit code of `0` indicates that the application completed successfully.

View the logs:

```bash
docker logs qr-generator
```

Remove the container after testing:

```bash
docker rm qr-generator
```

## Persist Generated QR Codes

A volume mount allows generated QR codes to remain available on the host computer after the container exits.

### PowerShell

```powershell
New-Item -ItemType Directory -Force .\qr_codes

docker run --name qr-generator `
  -v "${PWD}\qr_codes:/app/qr_codes" `
  qr-code-generator-app `
  --url "https://www.njit.edu"
```

### Linux or macOS

```bash
mkdir -p qr_codes

docker run --name qr-generator \
  -v "$(pwd)/qr_codes:/app/qr_codes" \
  qr-code-generator-app \
  --url "https://www.njit.edu"
```

## Docker Compose

Run the application using Docker Compose:

```bash
docker compose up
```

Docker Compose builds the image, supplies the environment variables, mounts the local `qr_codes` directory, and runs the application with the configured URL.

The expected log output is similar to:

```text
INFO - QR code successfully saved to /app/qr_codes/QRCode_<timestamp>.png
```

Because this is a command-line application rather than a continuously running service, the container exits after the QR code is successfully generated.

## DockerHub Image

The public Docker image is available at:

https://hub.docker.com/r/benkaii/qr-code-generator-app

Pull the image:

```bash
docker pull benkaii/qr-code-generator-app:latest
```

Run the DockerHub image:

```bash
docker run --rm \
  benkaii/qr-code-generator-app:latest \
  --url https://www.njit.edu
```

## GitHub Actions

The workflow located at:

```text
.github/workflows/docker-build.yml
```

runs automatically when changes are pushed to the `main` branch or submitted through a pull request. It checks out the repository and verifies that the Docker image builds successfully.

## Security Considerations

The Dockerfile follows several container-security practices:

- Uses the smaller `python:3.12-slim-bullseye` base image
- Installs only the dependencies listed in `requirements.txt`
- Uses `pip install --no-cache-dir`
- Creates a dedicated non-root user
- Assigns ownership of writable directories to the non-root user
- Runs the application without root privileges
- Excludes unnecessary files using `.dockerignore`

## Links

- GitHub repository: https://github.com/Benkaii/qr-code-generator
- DockerHub image: https://hub.docker.com/r/benkaii/qr-code-generator-app

## Author

Ismael Albilal