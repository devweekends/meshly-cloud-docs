# Services

Four services sit behind the one API gateway. Each owns its database, its endpoints, its events and its dashboard pages.

## Accounts (A)

Accounts, orgs and the dashboard shell.

- **Owns:** sign up and login, users, orgs, the one-admin-per-org rule, members and invites, roles, provider keys, the context selector, the super admin panel, auth at the gateway.
- **Database:** accounts.
- **Endpoints:** `POST /auth/signup`, `POST /auth/login`, `GET /me` (the user's contexts), `POST /orgs`, `POST /orgs/:id/invites`, `GET /orgs/:id/members`, `POST /keys`, `POST /keys/verify` (used by B), `GET /contexts/:id` (used by everyone).
- **Events out:** `user.created`, `org.created`, `member.added`, `key.created`, `key.revoked`.
- **Dashboard:** the layout, the top-right selector, Login, Members, Keys, Super admin.
- **Strategy:** ContextStrategy.

## Capacity (B)

Capacity and providers.

- **Owns:** the Go agent core, the CLI, the desktop app wrapper, machine registration with a key, heartbeats, CU per machine, sharing settings (how much, which hours), verification (a benchmark on join, spot checks, trust score, quarantine), and capacity planning (available, reserved and used per pool).
- **Database:** capacity.
- **Endpoints:** `POST /machines/register` (with a key), `POST /machines/:id/heartbeat`, `PUT /machines/:id/sharing`, `GET /machines?context=`, `GET /pools/:context/capacity`, `POST /capacity/reserve` and `POST /capacity/release` (used by C).
- **Events out:** `machine.online`, `machine.offline`, `machine.verified`, `machine.quarantined`, `capacity.changed`.
- **Dashboard:** Capacity and Sharing pages.
- **Strategy:** ProviderStrategy.

## Deployments (C)

Deployments, scheduling and public URLs.

- **Owns:** the deployment API (from a Docker image or a Git repo), a builder for Git repos (CI/CD through a GitHub webhook), the scheduler, replicas, health checks, failover and rescheduling, the proxy and agent tunnel for public URLs, and deployment logs.
- **Database:** deployments.
- **Endpoints:** `POST /deployments`, `GET /deployments/:id`, `POST /deployments/:id/scale`, `POST /deployments/:id/redeploy`, `POST /webhooks/github`, `GET /deployments/:id/logs`.
- **Events out:** `deployment.created`, `replica.started`, `replica.stopped`, `deployment.failed`.
- **Events in:** `machine.offline` (triggers a reschedule).
- **Dashboard:** Deployments page: create, public URL, replicas, connect a repo, logs tab.
- **Strategy:** PlacementStrategy.
- **Agent contract:** B's agent runs containers and holds the tunnel. C defines that contract and runs the proxy.

## Billing and observability (D)

Billing, usage and observability.

- **Owns:** pricing per CU per minute, a double-entry credits ledger, charges from usage, provider earnings, top-ups through Stripe test mode, org usage per member, logs and metrics for every service, alerts, and the health views on the dashboard.
- **Database:** billing, plus Loki and Prometheus.
- **Endpoints:** `GET /balance?context=`, `POST /credits/hold` (used by C before deploying), `POST /topup`, `GET /usage?context=&by=member`, `GET /earnings?context=`.
- **Events in:** `replica.started`, `replica.stopped`, `machine.online`, `machine.offline`. D works out usage from these itself, so nobody has to report usage separately.
- **Events out:** `charge.created`, `credits.low`, `earning.created`.
- **Dashboard:** Usage and Billing pages, monitoring widgets on Overview, revenue on Super admin.
- **Strategy:** PricingStrategy.

## How the services talk

| From | To | Call or event | Why |
|---|---|---|---|
| CLI, desktop app | Capacity | `POST /machines/register` with a key | A machine joins |
| Capacity | Accounts | `POST /keys/verify` | Check the key and find its context |
| Deployments | Capacity | `POST /capacity/reserve` | Hold CU before placing replicas |
| Deployments | Billing | `POST /credits/hold` | Make sure the consumer can pay |
| Capacity | everyone | `machine.offline` | Deployments reschedules, Billing stops charging |
| Deployments | Billing | `replica.started`, `replica.stopped` | Usage and charges |
| Billing | Deployments | `credits.low` | Warn the user, then scale to zero after a grace period |
| Any service | Accounts | `GET /contexts/:id` | Who is calling, which org, which role |

Next: [Reliability and fraud](reliability.md)
