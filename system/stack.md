# Stack

Suggested stack. Decide on day 1 and keep it simple.

| Piece | Choice |
|---|---|
| Services | TypeScript with NestJS |
| Databases | One Postgres server with four separate databases |
| Events | Redis Streams or NATS |
| Dashboard | Next.js |
| Agent | Go, one binary for the CLI, wrapped with Tauri for the desktop app |
| Workloads on providers | Docker |
| Public URLs | Caddy or Traefik as the proxy, Cloudflare wildcard DNS and TLS in front |
| Logs and metrics | Grafana, Loki, Prometheus |
| Payments | Stripe in test mode |
| CI | GitHub Actions for our repos, a small builder service for users' repos |
