# Repos

Eight repos under [devweekends](https://github.com/devweekends).

| Repo | What lives there | Owner |
|---|---|---|
| [contracts](https://github.com/devweekends/meshly-cloud-contracts) | OpenAPI per service, event definitions, and the shared types generated from them for api, web, cli and desktop | Everyone, each person their own part |
| [api](https://github.com/devweekends/meshly-cloud-api) | The one API: the gateway, the four service modules (accounts, capacity, deployments, billing) each with its own database, the proxy for public URLs, docker-compose and the monitoring setup | A, B, C and D, each their own module. C owns the proxy, D the monitoring setup |
| [core](https://github.com/devweekends/meshly-cloud-core) | Package the api uses. Strategy now: context, provider, placement and pricing. Scheduler later | Each person owns their strategy: A context, B provider, C placement, D pricing |
| [packages](https://github.com/devweekends/meshly-cloud-packages) | cli now: the agent that registers with a key, sends heartbeats, runs containers and opens the tunnel. sdk later. mcp later | B |
| [desktop](https://github.com/devweekends/meshly-cloud-desktop) | Desktop app for providers. It wraps the same agent as the cli, so agent logic is written once | B |
| [web](https://github.com/devweekends/meshly-cloud-web) | The one dashboard, with the org selector top right | A owns the layout and selector, each person owns their own pages |
| [docs](https://github.com/devweekends/meshly-cloud-docs) | Docs site: overview, how to use the contracts, a page per module, runbooks, the demo script | Everyone writes, A keeps it tidy |
| [apps](https://github.com/devweekends/meshly-cloud-apps) | Later, contributors only: one-click templates for databases, storage and apps such as WordPress | Community contributors |

## How the repos depend on each other

- contracts depends on nothing. Every other repo builds against it.
- api depends on contracts and core.
- web, cli and desktop depend only on contracts, and talk to api over HTTP.
- desktop reuses the agent code from `packages/cli`.

Next: [Stack](stack.md)
