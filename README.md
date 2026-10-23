# meshly-cloud-docs

Documentation for Meshly Cloud, a community cloud built by [Dev Weekends](https://github.com/devweekends).

The docs are split by what they're for:

| Folder | What's in it | Changes when |
|---|---|---|
| [system/](system/overview.md) | How Meshly Cloud works: the agreed design from our discussions | We change the design |
| [plan/](plan/demo-plan.md) | The four-week demo plan: demo script, who builds what, timeline | The plan changes |
| [sprints/](sprints/README.md) | Each week's stories, tasks, reading and questions | Every week |

## System

Read in this order:

1. [Overview](system/overview.md): what it is, roles and accounts, core terms
2. [Architecture](system/architecture.md): the diagram, public URLs and tunnels, rules, the strategy pattern
3. [Services](system/services.md): accounts, capacity, deployments, billing, and how they talk
4. [Reliability and fraud](system/reliability.md): validation, resilience, fraud checks
5. [Dashboard](system/dashboard.md): pages, owners, who sees what
6. [Repos](system/repos.md): what lives where and how repos depend on each other
7. [Stack](system/stack.md): the tools we picked

## Plan

- [Demo plan](plan/demo-plan.md)

## Sprints

- [Sprint 1](sprints/sprint-1.md): Oct 26 to Oct 30, tracked in [Linear](https://linear.app/dwosi/project/community-cloud-993f857dfbfd)

## Repositories

| Repo | What it holds |
|---|---|
| [meshly-cloud-contracts](https://github.com/devweekends/meshly-cloud-contracts) | Shared contracts: API schemas, events and types used across services |
| [meshly-cloud-api](https://github.com/devweekends/meshly-cloud-api) | Control plane API |
| [meshly-cloud-packages](https://github.com/devweekends/meshly-cloud-packages) | CLI, SDK and MCP server |
| [meshly-cloud-core](https://github.com/devweekends/meshly-cloud-core) | Core package: scheduler and placement strategy |
| [meshly-cloud-web](https://github.com/devweekends/meshly-cloud-web) | Web app and dashboard |
| [meshly-cloud-docs](https://github.com/devweekends/meshly-cloud-docs) | Documentation (this repo) |
| [meshly-cloud-desktop](https://github.com/devweekends/meshly-cloud-desktop) | Desktop app for contributing compute |
| [meshly-cloud-apps](https://github.com/devweekends/meshly-cloud-apps) | Contributor applications: database, storage and app workloads such as WordPress |

## License

[MIT](LICENSE)
