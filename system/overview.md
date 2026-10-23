# Overview

Meshly Cloud is a community cloud. Providers share machines, consumers deploy apps on them, orgs and personal users both work, and a super admin sees everything. Everything goes through one dashboard and one API.

## Roles and accounts

| Role | Who | Can |
|---|---|---|
| Super admin | Platform team only, never part of an org | See and manage all orgs, users, machines and payouts, and quarantine machines |
| Org admin | Exactly one per org | Manage members, keys, org capacity, sharing to the public, org billing |
| Org member | Belongs to an org | Deploy on the org's capacity, see their own usage |
| Personal user | Anyone with an account | Provide capacity, consume capacity, hold their own credits |

Provider and consumer are not account types. Any org or personal user can do both. One account is personal by default and can also be a member of several orgs. The top-right selector picks the context: Personal, or one of the user's orgs.

## Core terms

| Term | Meaning |
|---|---|
| Capacity unit (CU) | 1 vCPU and 1 GB RAM. A 4 CU plan means 4 vCPU and 4 GB RAM |
| Credits | Money inside the platform. Consumers spend credits per CU per minute, and providers earn them |
| Context | Personal or an org. Every API call carries it |
| Pool | The private pool is an org's machines, for its members only. The open pool is shared with everyone |
| Provider key | Created in the dashboard. The CLI or desktop app uses it to register a machine to a context |
| Deployment | A Docker image or a Git repo running as N replicas behind a public URL |
| End user | Someone using a consumer's app through its public URL. They have no account with us and never see the platform |

Next: [Architecture](architecture.md)
