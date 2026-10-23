# Dashboard

One app with one layout. The top right holds the context selector (Personal or an org) and the user menu. Everything on the page follows the selected context.

| Page | Shows | Owner | Who sees it |
|---|---|---|---|
| Overview | CU in use, live deployments, balance, alerts | D | Everyone |
| Capacity | As a provider: my machines, verified CU, status. As a consumer: CU available to me | B | Everyone |
| Sharing | How much to share, which hours, private or open pool | B | Providers. In an org, the org admin only |
| Deployments | Create from an image or a repo, public URL, replicas, CI/CD, logs | C | Everyone |
| Usage | What is being used, by deployment, and by member in an org | D | Everyone. Per-member view for the org admin |
| Billing | Credits, charges, earnings, top-up | D | Personal users and org admins |
| Members | Invite, remove, roles | A | Org admin |
| Keys | Create and revoke provider keys | A | Personal users and org admins |
| Super admin | All orgs, users, machines, quarantine, revenue | A, with B and D widgets | Super admin |

Next: [Repos](repos.md)
