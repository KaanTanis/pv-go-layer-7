# Pinoy Vendetta - Go Layer 7 💣

## Overview

**pv_http_pro** is a Go-based Layer 7 HTTP load and resilience testing tool intended for **authorized** testing of web infrastructure. It automatically detects supported HTTP versions (HTTP/1.1, HTTP/2 and HTTP/3) or allows them to be forced manually, distributing workers across protocols to simulate diverse client traffic.

## Features

- Automatic HTTP/1.1, HTTP/2 and HTTP/3 detection
- Optional protocol selection with `--http-protocol`
- Native HTTP/3 (QUIC) support
- HTTP/2 MadeYouReset-style stream reset workload
- uTLS browser impersonation with rotating JA3/TLS fingerprints
- Periodic client recycling to rotate fingerprints
- Randomized:
  - User-Agent
  - Accept / Accept-Language
  - Referer
  - Cookies
  - Header names (mixed case)
  - Header order
  - X-Forwarded-For / X-Real-IP
  - SNI
  - Request payloads (JSON, form, plain)
  - Cache-bypass query parameters
- Burst & Think traffic simulation
- Adaptive request delay reacting to selected HTTP responses and network errors
- Live dashboard with:
  - Requests/sec
  - Average latency
  - Active HTTP versions
  - Status codes grouped by protocol
  - Error counters
  - Adaptive delay
  - MadeYouReset statistics
  - Live event log

## Requirements

- Go 1.18+

## Installation

```bash
go mod init pv_http_pro
go mod tidy
```

## Build

```bash
go build pv_http_pro.go
```

## Usage

```bash
./pv_http_pro --url https://example.com [OPTIONS]
```

## Command-line Flags

| Flag | Default | Description |
|------|---------|-------------|
| `--url` | required | Target URL |
| `--port` | auto | Override target port |
| `--duration` | `5` | Duration (minutes) |
| `--concurrency` | `100` | Worker count |
| `--http-method` | `GET,POST` | Comma-separated HTTP methods |
| `--http-protocol` | auto | Force `1.1`, `2`, `3`, or combinations |
| `--random-path` | `false` | Preserve the original path while adding randomized cache-bypass query parameters |
| `--burst` | `true` | Enable burst mode |
| `--burst-size` | `15` | Maximum requests per burst |
| `--think-time` | `7000` | Maximum think time (ms) |
| `--jitter` | `50` | Additional random delay (ms) |
| `--adaptive-delay` | `false` | Dynamically adjust pacing |

## How It Works

1. Detects supported HTTP versions unless overridden.
2. Creates workers distributed across active protocols.
3. Uses uTLS to impersonate modern browsers.
4. Rotates JA3 fingerprints by rebuilding clients periodically.
5. Randomizes headers, cookies, SNI and payloads.
6. HTTP/2 workers also perform MadeYouReset stream-reset activity.
7. Adaptive delay reacts to rate limiting, blocking responses, timeouts and connection resets.
8. Dashboard updates in real time.

## Examples

Basic:

```bash
./pv_http_pro --url https://example.com
```

HTTP/2 only:

```bash
./pv_http_pro --url https://example.com --http-protocol 2
```

HTTP/2 + HTTP/3:

```bash
./pv_http_pro --url https://example.com --http-protocol 2,3
```

POST workload:

```bash
./pv_http_pro --url https://example.com/login --http-method POST
```

Adaptive delay:

```bash
./pv_http_pro --url https://example.com --adaptive-delay=true
```

Random cache-bypass parameters:

```bash
./pv_http_pro --url https://example.com --random-path=true
```

## Dashboard

The terminal dashboard displays:

- Time remaining
- Requests sent
- Responses received
- Requests/sec
- Average latency
- Current adaptive delay
- Active HTTP versions
- Per-protocol response status counters
- Error statistics
- MadeYouReset counters
- Recent log messages

## Notes

- Cache-bypass randomization modifies query parameters while preserving the original path.
- HTTP/3 uses QUIC when supported.
- Client fingerprints rotate throughout long-running executions.

## Disclaimer

This software is intended **only** for authorized load testing, resilience testing, and security assessments of systems you own or have explicit permission to test. Unauthorized use against third-party systems may be illegal and unethical.
