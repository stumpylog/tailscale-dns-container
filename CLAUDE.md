# CLAUDE.md - Project Guide for AI Assistants

## Project Overview

**Tailscale DNS Container** is a lightweight Docker container that provides DNS resolution services for Tailscale networks. It runs dnsmasq within a Tailnet to:

- Rewrite domain names to their Tailnet IP addresses
- Forward non-matching DNS requests to upstream DNS servers
- Enable local network DNS resolution for Tailscale-connected devices

**Use Case**: Replicate home network DNS behavior (domain-to-local-IP mapping) for devices connected via Tailscale VPN.

## Architecture

- **Base**: Alpine Linux 3.23.2 (minimal footprint)
- **Init System**: s6-overlay v3.2.2.0 (process supervision)
- **DNS Server**: dnsmasq 2.91-r0
- **Platforms**: linux/amd64, linux/arm64
- **Registry**: ghcr.io/stumpylog/tailscale-dns-container

The container shares the Tailscale container's network interface via `network_mode: "service:tailscale"`, allowing it to listen on Tailnet IP addresses.

## Directory Structure

```
.
├── .github/
│   ├── FUNDING.yml              # GitHub sponsor config
│   ├── dependabot.yml           # Automated dependency updates
│   └── workflows/
│       └── ci.yml               # CI/CD pipeline
├── rootfs/                      # Container filesystem overlay
│   └── etc/s6-overlay/s6-rc.d/
│       ├── user/contents.d/     # Service list
│       ├── init-dnsmasq/        # Init service (oneshot)
│       │   ├── type
│       │   ├── run
│       │   └── up
│       └── svc-dnsmasq/         # Main service (longrun)
│           ├── type
│           ├── run
│           ├── notification-fd
│           └── dependencies.d/
├── Dockerfile                   # Multi-stage build
├── docker-compose.yml           # Example deployment
├── .dockerignore
├── .pre-commit-config.yaml      # Code quality hooks
├── .editorconfig                # Editor settings
├── .yamlfmt                     # YAML formatter config
├── CHANGELOG.md                 # Version history (Keep a Changelog format)
├── README.md                    # User documentation
└── LICENSE
```

## Key Files

| File                                             | Purpose                                                   |
| ------------------------------------------------ | --------------------------------------------------------- |
| `Dockerfile`                                     | Multi-stage build: downloads s6-overlay, installs dnsmasq |
| `docker-compose.yml`                             | Example deployment with Tailscale sidecar                 |
| `rootfs/etc/s6-overlay/s6-rc.d/init-dnsmasq/run` | Config validation script                                  |
| `rootfs/etc/s6-overlay/s6-rc.d/svc-dnsmasq/run`  | Main dnsmasq service script                               |
| `.github/workflows/ci.yml`                       | Build, scan, and release pipeline                         |

## Docker Build Process

**Stage 1 (s6-overlay-base)**:

1. Sets s6-overlay environment variables
2. Downloads and validates s6-overlay tarballs (with SHA256 checksums)
3. Extracts s6-overlay and copies rootfs configuration

**Stage 2 (main-app)**:

1. Installs dnsmasq with pinned version
2. Sets `/init` as entrypoint (s6-overlay init)

**Build command** (local):

```bash
docker buildx build --platform linux/amd64,linux/arm64 -t tailscale-dns-container .
```

## s6-overlay Service Configuration

**Init Service** (`init-dnsmasq`):

- Type: `oneshot` (runs once at startup)
- Validates dnsmasq configuration with `dnsmasq --test`

**Main Service** (`svc-dnsmasq`):

- Type: `longrun` (persistent)
- Health check: `nc -z localhost 53` (port 53 connectivity)
- Runs dnsmasq in foreground with stdout logging

## GitHub Actions CI/CD

**Workflow**: `.github/workflows/ci.yml`

**Triggers**:

- Push to `main` or `develop` branches (build only)
- Push semver tags `v*.*.*` (build + push + release)

**Jobs**:

1. **lint**: Hadolint (Dockerfile), changelog validation
2. **build-docker-image**: Multi-arch build, push on tags only
3. **scan-docker-image**: Trivy security scan, SARIF upload
4. **release**: Create GitHub release with changelog notes

**Tag Strategy**:

- `v1.2.3` produces tags: `1.2.3`, `1.2`, `1`

**Dependabot**:

- Docker dependencies: weekly updates to `develop`
- GitHub Actions: monthly updates to `develop`

## Development Workflow

1. **Branch**: Create feature branch from `develop`
2. **Pre-commit**: Hooks run automatically on commit
   - YAML/JSON/TOML validation
   - Shell script linting (ShellCheck)
   - Code formatting (Prettier)
   - Trailing whitespace removal
3. **Push**: CI builds image (not pushed)
4. **Merge to develop**: CI builds image
5. **Release**: Tag with `vX.Y.Z`, CI builds, pushes to ghcr.io, creates GitHub release

## Code Quality Tools

| Tool       | Purpose                     |
| ---------- | --------------------------- |
| Hadolint   | Dockerfile linting          |
| ShellCheck | Shell script analysis       |
| Prettier   | Code formatting             |
| yamlfmt    | YAML formatting             |
| Trivy      | Container security scanning |
| pre-commit | Git hook framework          |

## Conventions

- **Version pinning**: All packages, images, and actions use explicit versions
- **Semantic versioning**: Tags follow `vMAJOR.MINOR.PATCH`
- **Changelog**: Follows Keep a Changelog format
- **Indentation**: 2 spaces (see `.editorconfig`)
- **Line endings**: LF (Unix-style)
- **s6 scripts**: Use execlineb syntax (not shell)

## Environment Variables (s6-overlay)

| Variable                           | Value | Purpose                       |
| ---------------------------------- | ----- | ----------------------------- |
| `S6_BEHAVIOUR_IF_STAGE2_FAILS`     | `2`   | Continue on stage 2 failure   |
| `S6_CMD_WAIT_FOR_SERVICES_MAXTIME` | `0`   | Unlimited wait for services   |
| `S6_CMD_WAIT_FOR_SERVICES`         | `1`   | Wait for services to be ready |
| `S6_VERBOSITY`                     | `1`   | Standard logging              |

## User Configuration

Users mount dnsmasq configuration files to `/etc/dnsmasq.d/` (read-only):

```yaml
volumes:
  - ./config/:/etc/dnsmasq.d/:ro
```

## Common Tasks

**Run locally**:

```bash
docker compose up -d
```

**View logs**:

```bash
docker logs tailscale-dns
```

**Test dnsmasq config**:

```bash
docker exec tailscale-dns dnsmasq --test
```

**Lint Dockerfile locally**:

```bash
hadolint Dockerfile
```

## Security Considerations

- Trivy scans run on every build (results in GitHub Security tab)
- Base image is minimal Alpine Linux
- s6-overlay provides proper signal handling and zombie reaping
- Configuration mounted read-only
- No secrets stored in image
