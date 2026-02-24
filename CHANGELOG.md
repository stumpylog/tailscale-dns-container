# CHANGELOG

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.12.0] - 2026-03-31

### Security

- SBOM and provenance attestations now generated and attached to released images
- Job permissions tightened: `build-docker-image` reduced from `contents: write` to `contents: read`, `scan-docker-image` now explicitly grants `contents: read`

## [0.11.0] - 2026-03-31

### Changed

- Dependabot GitHub Actions updates are now grouped into a single PR
- Base image updated to Alpine 3.23.3

### Added

- Image now builds for `linux/arm64` platform as well
- Dockerfile linting via Hadolint
- Validation of the CHANGELOG format
- Updates s6-overlay to v3.2.2.0

### Changed

- Replaced `ncipollo/release-action` with native `gh release create`

### Security

- GitHub Actions workflow audited with [zizmor](https://docs.zizmor.sh/)
- All GitHub Actions checkout steps now set `persist-credentials: false`
- Template expressions in `run:` steps moved to environment variables to prevent injection
- All GitHub Actions pinned to commit SHAs for supply chain security
- Dependabot now groups GitHub Actions updates and applies a 7-day cooldown

## [0.10.0] - 2025-12-09

### Changed

- Base image updated to Alpine 3.23.0
- YAML is now formatted via `yamlfmt`

### Added

- The image is now scanned with Trivy

## [0.9.0] - 2025-10-09

### Changed

- Base image updated to Alpine 3.22.2

## [0.8.0] - 2025-07-29

### Changed

- Base image updated to Alpine 3.22.1 [#16](https://github.com/stumpylog/tailscale-dns-container/pull/16)

## [0.7.0] - 2025-03-25

### Changed

- Base image updated to Alpine 3.21.3 [#14](https://github.com/stumpylog/tailscale-dns-container/pull/14)

### Added

- Added sponsor button [#13](https://github.com/stumpylog/tailscale-dns-container/pull/13)

## [0.6.0] - 2024-12-09

### Changed

- Base image updated to Alpine 3.21 [#11](https://github.com/stumpylog/tailscale-dns-container/pull/11)
- Small documentation updates [#12](https://github.com/stumpylog/tailscale-dns-container/pull/12)

## [0.5.0] - 2024-10-11

### Changed

- s6-overlay updated to 3.2.0.2

## [0.4.1] - 2024-07-31

### Fixed

- Resolved Docker build warning regarding cases of the `FROM x AS y` format

## [0.4.0] - 2024-06-10

### Changed

- Base image updated to Alpine 3.20
- s6-overlay updated to v3.2.0.0

### Added

- Github release with changelog for releases

### Fixed

- dependabot was targeting the wrong branch for updates

## [0.3.0]

### Changed

- s6-overlay 3.1.6.2
- Alpine 3.19

## [0.2.2] - 2023-11-01

### Changed

- Changelog format is now "Keep a Changelog" style
- Updated action versions

## [0.2.0] - 2023-05-25

- Alpine 3.18
- s6-overlay 3.1.5.0

## [0.1.0]

- Initial image release
- Alpine 3.17
- s6-overlay 3.1.4.2
