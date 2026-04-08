# sv2-ui AGENTS.md

# sv2-ui

This [repository](https://github.com/stratum-mining/sv2-ui) aims to allow non-technical human users to deploy Stratum V2 applications on their machines.

Our goal is to provide an easy and intuitive UI/UX that allows them to benefit from Stratum V2 on their Bitcoin Mining without getting stuck on technical issues.

We leverage Docker deployments of github.com/stratum-mining/sv2-apps under the hood, which the TypeScript UI talks to via HTTP APIs.

There's different UI flows for different use-cases.

## agentic engineering

This repository is taking contributions from multiple human collaborators. Most of those contributions are done via agentic engineering.

Therefore we should always be careful with our assumptions and make sure humans and agents are always on the same page. Otherwise, this repository will quickly turn into regression festival.

Humans try to coordinate as much as possible via github issues and PRs. Whenever an agent detects that meaningful coordination between humans is necessary, it flags it on the interaction and is extra careful before proceeding.

## JDC + tProxy

Whenever jd_client_sv2 (aka JDC) is deployed, it's always deployed together with a translator_sv2 (aka tProxy) as a downstream connection. However, the human user never knows that these are two separate applications/containers.

tProxy's role is to translate Sv1 downstream connections to Sv2. Eventually JDC + tProxy might be fused together into one single app, but that might take a while so that's why we're trying to abstract that away on the UI as a temporary measure.

Whenever that future comes, we must remember to remove this section from this file so that agents don't get confused.

## tProxy aggregated vs non-aggregated modes

tProxy can work in aggregated or non-aggregated modes: aggregated mode funnels all Sv1 clients into one single upstream extended channel, while non-aggregated has a 1-to-1 relationship between Sv1 clients and upstream Sv2 extended channels.

At the moment, the Braiins Pool UI flow is limited to tProxy aggregated mode, because Braiins does not allow for multiple extended channels over one connection.

This distinction matters for HTTP monitoring too. In non-aggregated mode, the monitoring API is easier to reason about because one Sv1 miner usually maps to one upstream extended channel, so `/api/v1/sv1/clients` and `/api/v1/server/channels` feel closely aligned.

In aggregated mode, that intuition breaks down: many Sv1 miners can be represented by one single upstream extended channel. As a result, channel-level counters exposed by `/api/v1/server/channels` may not behave the same way as they do in non-aggregated mode, and they should not be assumed to preserve the same semantics.

In aggregated mode, the dashboard should not show per-worker Best Diff because `/api/v1/sv1/clients` does not expose it, and the upstream Best Diff is aggregated rather than worker-specific.
