# personal-server

A self-hosted personal server on Oracle Cloud ARM64. Everything runs in Docker behind a single authentication layer. One login with TOTP gets you into everything. Built from scratch and maintained manually.

---

## What it is

A private, self-owned cloud alternative, built from scratch. No subscriptions, no third-party data handling, no vendor lock-in. Accessible from anywhere via browser or phone.

---

## Capabilities

- **Personal dashboard** - real-time server health (CPU, RAM, disk), running containers, security events, and file management from any device
- **File server** - upload and access files from anywhere, stored in isolated container storage
- **Automation** - self-hosted workflow engine for personal automations without sending data to third-party services
- **Security monitoring** - live IP ban tracking, API rate limiting, full audit trail of incoming requests
- **Secure remote access** - every service behind SSO with TOTP, nothing reachable unauthenticated

---

## Infrastructure

- Oracle Cloud ARM64 (aarch64), 4 OCPU / 24GB RAM / 146GB storage
- Ubuntu 22.04 LTS
- DuckDNS dynamic DNS with Let's Encrypt SSL auto-renewal
- Nginx Proxy Manager handling all ingress
- Internal Docker network isolation, all inter-service traffic stays off the host
- Oracle VCN firewall rules locking down everything except web traffic

---

## Architecture

All services run in Docker Compose on a shared internal network. Nginx Proxy Manager sits at the edge, terminates SSL, and forwards requests through Authelia for authentication before any service receives traffic. The dashboard talks to Docker via a read-only socket proxy. The Docker socket is never directly mounted.

---

## Dashboard

Built with FastAPI serving a single-page frontend.

- Real-time CPU, RAM, disk, and container health via host /proc passthrough
- Container monitoring via read-only Docker socket proxy
- File upload with size limits, filename sanitization, and isolated Docker volume storage
- Rate limiting on all API endpoints via slowapi
- Cache-Control no-store middleware on all responses
- Dark/light theme, mobile PWA

---

## Authentication

- Authelia SSO with TOTP on every subdomain
- Forward-auth pattern, no service is reachable without passing through the auth layer
- Secure logout with bfcache invalidation

---

## Security

- SSH key-only authentication, root login disabled, X11 forwarding off
- fail2ban with automated IP banning and live ban tracking in the dashboard
- Rate limiting on all dashboard API endpoints
- All containers running as non-root (1001:1001)
- Uploads in an isolated named Docker volume, off the host filesystem
- Docker socket exposed read-only via Tecnativa proxy, no direct socket mounting
- Automated maintenance script for system and container updates

---

## Services

| Service | Purpose |
|---|---|
| Nginx Proxy Manager | Reverse proxy and SSL termination |
| Authelia | SSO and TOTP authentication |
| n8n | Self-hosted automation engine |
| Tecnativa Docker Socket Proxy | Read-only Docker API access |

... and many more
---

## Stack

FastAPI, Docker, Authelia, Nginx Proxy Manager, n8n, fail2ban, slowapi, Ubuntu 22.04, Oracle Cloud ARM64
