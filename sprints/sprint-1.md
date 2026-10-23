# Community Cloud: Sprint 1 Stories (Oct 26 to Oct 30)

Six stories for this week. Each has sub-tasks, reading, questions to think about, and a stretch goal. Pick a story on Monday morning, and pair where the story says so. Everything is due Friday, Oct 30, at the demo.

This sprint is week 1 of the [demo plan](../plan/demo-plan.md). Track it in Linear: [Community Cloud project](https://linear.app/dwosi/project/community-cloud-993f857dfbfd), label **Sprint 1**.

## Contents

- [Why the reading and the questions matter](#why-the-reading-and-the-questions-matter)
- [Friday demo, Oct 30](#friday-demo-oct-30)
- [Story 1: Contracts and local setup](#story-1-contracts-and-local-setup)
- [Story 2: Accounts, orgs and the context selector](#story-2-accounts-orgs-and-the-context-selector)
- [Story 3: A provider machine joins with a key](#story-3-a-provider-machine-joins-with-a-key)
- [Story 4: Deploy a Docker image and reach it at a public URL](#story-4-deploy-a-docker-image-and-reach-it-at-a-public-url)
- [Story 5: Credits, usage and billing from events](#story-5-credits-usage-and-billing-from-events)
- [Story 6: One dashboard](#story-6-one-dashboard)
- [Linear setup](#linear-setup)

## Why the reading and the questions matter

Building the basic version of each piece is the easy part, and it's not where you grow. The questions under each story are the ones that separate a demo from a real system: what happens when a machine lies, when two requests race, when a contract changes under someone else. Spend 30 minutes on them before you code, write your answers in your story's doc page, and bring them to Wednesday's review. If a question changes your design, that's the point.

## Friday demo, Oct 30

1. A provider runs the CLI on a Linux machine with a key. The machine shows up in the dashboard under the right context, with its CU.
2. A consumer picks an org in the top-right selector and deploys a Docker image.
3. The image runs on the provider's machine, and its public URL works for an end user.
4. Credits drop every minute while it runs, and the provider's earnings go up.
5. We stop the agent. Within seconds the machine shows offline and the deployment shows unhealthy.

## Story 1: Contracts and local setup

**Goal:** everyone can build against everyone else from Tuesday, without waiting.

**Done when:** every endpoint and event used in the Friday demo is in the contracts repo, mock servers run from it, and `docker compose up` starts the api with its four databases.

**Good fit:** everyone on Monday, then one person finishes the setup.

### Sub-tasks

- [ ] OpenAPI files for accounts, capacity, deployments and billing, with only the endpoints the demo needs
- [ ] Event catalog: names, payloads and the owner of each event
- [ ] Generate shared types for api, web, cli and desktop from the contracts
- [ ] Mock servers from the OpenAPI files, for example with Prism
- [ ] docker-compose for api, four Postgres databases, the event bus and the proxy
- [ ] CI in every repo that fails when code drifts from the contracts
- [ ] Contract review on Monday afternoon, frozen by Tuesday evening

### Read

- Google's API Improvement Proposals (aip.dev): resource names, standard methods, errors
- Stripe's blog post on API versioning
- The AsyncAPI spec, for writing events down the way OpenAPI writes endpoints
- The transactional outbox pattern on microservices.io

### Think about

- B renames a field after C has shipped against it. What breaks, and how would you have caught it in CI?
- Do events need versions too? What happens to an old consumer when a new field shows up?
- Which service is the single source of truth for a machine's state, and who is only allowed to cache it?
- Sortable IDs (UUIDv7, ULID) or random UUIDs? What does each do to your database indexes and your logs?

**Stretch:** contract tests that run in every repo's CI against both the mocks and the real api.

## Story 2: Accounts, orgs and the context selector

**Goal:** anyone can sign up, create an org or stay personal, and every request knows which context it's acting in.

**Done when:** a user can sign up, create an org (becoming its only admin), invite a member, switch context in the dashboard, and create a provider key that the capacity module can verify.

**Good fit:** A.

### Sub-tasks

- [ ] Sign up, log in, sessions
- [ ] Orgs with exactly one org admin, enforced in the database
- [ ] Invites and members
- [ ] `GET /me` returns the personal context plus every org the user belongs to
- [ ] Gateway middleware that adds user, context and role to every request
- [ ] Provider keys: create, list, revoke, and `POST /keys/verify` for the capacity module
- [ ] ContextStrategy in core: PersonalContext and OrgContext
- [ ] Super admin seeded and blocked from joining any org

### Read

- AWS whitepaper: SaaS Tenant Isolation Strategies
- Postgres docs on row-level security
- OWASP Authentication Cheat Sheet
- Google's Zanzibar paper, for how permissions work at very large scale

### Think about

- A user belongs to three orgs and switches mid-session. Does the context live in the token, a header or the URL? What goes wrong with each?
- How do you stop the only org admin from leaving, or deleting their account, and orphaning the org?
- A provider key leaks on GitHub. What stops someone registering 1,000 fake machines with it? Think about scopes, expiry and rate limits.
- Where exactly would a bug let org A see org B's data, and what test would catch it?

**Stretch:** relationship-based permissions in the Zanzibar style, so new roles never need a code change.

## Story 3: A provider machine joins with a key

**Goal:** a Linux machine joins the right context with one command and stays visibly alive.

**Done when:** `cc join --key <key>` registers the machine, reports its CPU, RAM and disk, sends heartbeats, and the machine shows online in the dashboard. Stopping the agent shows it offline within seconds.

**Good fit:** B.

### Sub-tasks

- [ ] Agent core in Go: register with a key, heartbeat loop with backoff, reconnect
- [ ] Machine profile: CPU, RAM, disk, OS, architecture
- [ ] CLI commands: `join`, `status`, `share --cu`, `leave`
- [ ] Capacity module: machine registry, heartbeats, online and offline, `machine.online` and `machine.offline` events
- [ ] Turn raw specs into CU (1 vCPU and 1 GB RAM per CU) and store shared CU separately from total CU
- [ ] ProviderStrategy in core: CliProvider now, DesktopProvider stubbed
- [ ] Capacity page in the dashboard

### Read

- Kubernetes docs on node heartbeats and leases
- Tailscale docs on auth keys, and their post How NAT traversal works
- The BOINC paper, for how volunteer computing handles machines that come and go

### Think about

- How do you tell a dead machine from one on bad Wi-Fi? What do false alarms cost you, and what do slow detections cost you?
- The same laptop runs `join` twice. One machine or two? How do you know?
- How would you fake 64 CU on a 4-core laptop? Now, how would you catch yourself?
- The provider lowers shared CU while containers are running on it. What happens to those containers?

**Stretch:** a 30-second benchmark on join that measures real CPU and RAM, and refuses to count CU it can't verify.

## Story 4: Deploy a Docker image and reach it at a public URL

**Goal:** a consumer deploys an image and an end user can open it at a public URL.

**Done when:** `POST /deployments` with an image places it on a provider machine, the agent runs it, and `https://<name>.<our-domain>` serves it through the proxy and the agent's tunnel.

**Good fit:** C, pairing with B on the agent side on Wednesday.

### Sub-tasks

- [ ] Deployments module: create, get, stop, with replicas stored but only 1 used this week
- [ ] PlacementStrategy in core: PrivatePool and OpenPool, first machine with enough free verified CU
- [ ] `POST /capacity/reserve` and release called before and after placement
- [ ] Agent runs and stops containers on request (with B)
- [ ] Agent opens one outbound tunnel to the proxy
- [ ] Proxy routes `<name>.<domain>` to the right tunnel and container, with Cloudflare wildcard DNS in front
- [ ] Health check on the container, and `replica.started` and `replica.stopped` events
- [ ] Deployments page in the dashboard with the public URL

### Read

- Cloudflare Tunnel docs and ngrok docs, for how reverse tunnels work
- Envoy's architecture overview
- Kubernetes docs on Deployments and readiness probes
- Fly.io's blog posts on their proxy

### Think about

- What does healthy mean for an app we know nothing about? What if it takes 40 seconds to start?
- Two providers sit behind the same home router. How does a request find the right container?
- The provider's machine dies mid-request. What does the end user see, and what would it take for them to see nothing?
- Where should the image be pulled from, and what happens when a 2 GB image is pulled over home internet?

**Stretch:** two replicas on two providers, with the proxy failing over when one dies.

## Story 5: Credits, usage and billing from events

**Goal:** money moves correctly. Consumers pay per CU per minute, and providers earn it.

**Done when:** a running deployment charges the consumer's context every minute, the provider's earnings rise by the same amount minus our share, and the ledger balances.

**Good fit:** D.

### Sub-tasks

- [ ] Double-entry ledger: every movement is two entries that sum to zero
- [ ] Price per CU per minute, and our share
- [ ] Usage from `replica.started` and `replica.stopped` events, with no separate reporting needed
- [ ] Per-minute charges, idempotent so a retried event never charges twice
- [ ] `GET /balance`, `POST /credits/hold` (called by C before deploying), `GET /earnings`
- [ ] PricingStrategy in core: OrgPricing and PersonalPricing
- [ ] Usage and Billing pages in the dashboard
- [ ] Logging stack: structured logs from every module with a request ID, viewable in one place

### Read

- Modern Treasury's Accounting for Developers series
- Stripe's blog post on idempotency, and Brandur Leach's Implementing Stripe-like Idempotency Keys in Postgres
- Amazon Builders' Library: Making retries safe with idempotent APIs
- Google SRE book: Monitoring Distributed Systems

### Think about

- A replica runs for 61 seconds. Do you charge one minute or two? Who decides, and where is that rule written?
- The provider's machine dies mid-minute. Who pays for that minute?
- Two deploys start at the same moment, with enough balance for only one. What stops both from going through?
- How would you prove to an angry user that their bill is right?

**Stretch:** rebuild every balance from the event history and compare it with the stored balances on every deploy.

## Story 6: One dashboard

**Goal:** one dashboard where every page follows the context selected at the top right.

**Done when:** the layout, the selector and the empty page shells exist, and each story's page is plugged in by Friday.

**Good fit:** A owns the shell. Each story owner builds their own page.

### Sub-tasks

- [ ] Layout, navigation and the top-right context selector
- [ ] Pages: Overview, Capacity, Sharing, Deployments, Usage, Billing, Members, Keys, Super admin
- [ ] Every data call carries the selected context
- [ ] Live status for machines and deployments
- [ ] Hide pages by role: Members for org admins, Super admin for the super admin

### Read

- How Vercel, GitHub and Supabase build their team or org switcher
- Next.js docs on caching and data fetching

### Think about

- You switch from org A to org B. What stops a cached page from showing A's data for a second?
- Polling, server-sent events or WebSockets for live machine status? What does each cost on the server?
- What should an org member see on the Billing page, and what should only the org admin see?

**Stretch:** live updates over server-sent events, with no page refresh anywhere in the demo.

## Linear setup

- **Project:** [Community Cloud](https://linear.app/dwosi/project/community-cloud-993f857dfbfd) in the DWOSI workspace, team COM, Oct 26 to Oct 30.
- **Issues:** each story is a parent issue with its sub-tasks as sub-issues, left unassigned so people pick on Monday. Every issue has the **Sprint 1** label.

| Story | Linear |
|---|---|
| 1. Contracts and local setup | [COM-48](https://linear.app/dwosi/issue/COM-48) |
| 2. Accounts, orgs and the context selector | [COM-56](https://linear.app/dwosi/issue/COM-56) |
| 3. A provider machine joins with a key | [COM-65](https://linear.app/dwosi/issue/COM-65) |
| 4. Deploy a Docker image and reach it at a public URL | [COM-73](https://linear.app/dwosi/issue/COM-73) |
| 5. Credits, usage and billing from events | [COM-82](https://linear.app/dwosi/issue/COM-82) |
| 6. One dashboard | [COM-91](https://linear.app/dwosi/issue/COM-91) |

- **Description:** each story's goal, done-when, reading and questions go in the issue description.
- **Labels:** contracts, accounts, capacity, deployments, billing, web.
