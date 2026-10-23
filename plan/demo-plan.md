# Demo Plan

Team: A, B, C, D. Four weeks to one working demo where providers share machines, consumers deploy apps on them, orgs and personal users both work, and a super admin sees everything. Everything goes through one dashboard and one API.

How the system works (roles, terms, architecture, services) lives in [system/](../system/overview.md). This page is only the plan: what we show, who builds it, and by when.

## Demo script

This is what we show at the end. Every task exists to make one of these steps work.

1. Super admin logs in and sees the platform overview: all orgs, users, machines, capacity units and revenue.
2. User 1 signs up, creates the org "Acme", becomes its org admin, and invites User 2.
3. The org admin creates a provider key. On a Linux server, the CLI joins with that key. The server shows up in Acme's Capacity page, verified at 8 CU.
4. User 3, a personal user, installs the desktop app, signs in with a key, and shares 4 CU from 9:00 AM to 6:00 PM. After verification it joins the open pool.
5. User 2 switches to Acme in the top-right selector and deploys a Docker image with 2 replicas. It gets a public URL.
6. User 2 connects a GitHub repo, pushes a commit, and CI builds and redeploys it automatically.
7. We open the public URL, then watch the logs and usage update live.
8. We kill one provider machine. Traffic fails over, the replica is rescheduled, and an alert shows on the dashboard.
9. Billing page: the consumer is charged credits per minute, the provider has earned credits, and Acme sees usage per member.
10. A fake provider claims 64 CU. The benchmark measures 4, so the machine is flagged and quarantined.

## Who builds what

| Person | Area | Details |
|---|---|---|
| A | Accounts, orgs and the dashboard shell | [Accounts](../system/services.md#accounts-a) |
| B | Capacity and providers: agent, CLI, desktop app | [Capacity](../system/services.md#capacity-b) |
| C | Deployments, scheduling and public URLs | [Deployments](../system/services.md#deployments-c) |
| D | Billing, usage and observability | [Billing and observability](../system/services.md#billing-and-observability-d) |

C pairs with B in week 2: B's agent runs containers and holds the tunnel, C defines that contract and runs the proxy.

## Four-week plan

Friday of each week is integration day, where everything joins up and we run the demo so far.

| Person | Week 1 | Week 2 | Week 3 | Week 4 |
|---|---|---|---|---|
| A | Contracts. Auth, orgs, contexts, gateway, dashboard layout and selector | Invites, members, keys, key verify | Super admin panel, role checks on every endpoint | Seed script for the demo accounts, polish |
| B | Contracts. Agent core: register with a key, heartbeat, CU report | CLI, run containers on request from C, sharing settings | Desktop app wrapper, benchmark verification, capacity reserve and release | Spot checks, trust score, quarantine, capacity planning view |
| C | Contracts. Deployments API, deploy a Docker image to one machine against fake capacity | Public URL through the proxy and tunnel, replicas, health checks | Failover on `machine.offline`, GitHub webhook build and redeploy | Deployment logs, scaling, polish |
| D | Contracts. Ledger, CU pricing, balances, logging stack | Usage from events, per-minute charges, Stripe test top-up | Provider earnings, org usage per member, `credits.low` | Grafana dashboards, alerts, super admin revenue |

Week by week stories live in [sprints/](../sprints/). Week 1 is [Sprint 1](../sprints/sprint-1.md).

### Repo scope

| Repo | In scope |
|---|---|
| contracts, api, web, docs | From week 1 |
| core | Strategies now, scheduler later |
| packages | cli now, sdk and mcp later |
| desktop | From week 3 |
| apps | Later, community contributors |

## Working together

- **Day 1:** each person writes their endpoints and events in `contracts/` and reviews everyone else's. Run mock servers from the OpenAPI files, with Prism for example, so nobody waits on anybody.
- **After day 1:** a contract changes only through a pull request that the consuming person approves.
- **Daily standup in Slack:**

  ```
  Done yesterday:
  Doing today:
  Blocked by:
  ```

- **Every service has:** a `/health` endpoint, structured JSON logs with a request ID, a Dockerfile, CI, and a README that explains how to run it alone.
- **Done means:** it works against the real services on Friday, it has tests, and its docs are updated.

## Demo is ready when

- [ ] All 10 steps of the demo script run end to end without manual fixes
- [ ] The context selector changes every page correctly
- [ ] A server joins with the CLI and a desktop joins with the app, both with a key
- [ ] A deployment from a Docker image and one from a Git repo both get a public URL
- [ ] Killing a machine fails over with no visible downtime on a 2-replica deployment
- [ ] Credits move correctly between consumer and provider, and the ledger balances
- [ ] The fake 64 CU machine is caught and quarantined
- [ ] Logs and metrics for every service are visible in one place
