# CHANGELOG

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

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
