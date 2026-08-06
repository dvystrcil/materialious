# materialious

Kubernetes deployment manifests for [Materialious](https://github.com/Materialious/Materialious), a Material Design frontend for a self-hosted [Invidious](https://github.com/dvystrcil/invidious) instance. No build step here — this repo consumes the upstream `wardpearce/materialious-full` image directly, same shape as [`dvystrcil/invidious`](https://github.com/dvystrcil/invidious).

Licensed under [MIT](LICENSE).

## Why `-full`, not the plain `materialious` image

The plain `wardpearce/materialious` image is a static frontend that calls Invidious/Companion directly from the browser, which needs CORS headers Invidious itself doesn't support (per upstream's own docs) and our `gateway-services` HTTPRoute chart doesn't yet implement. `materialious-full` bundles a small Node backend that proxies all Invidious/Companion/YouTube traffic server-side — the browser only ever talks to Materialious's own origin, so no CORS setup is needed.

## Config

Runs stateless — no database. `PUBLIC_INTERNAL_AUTH` / `PUBLIC_REQUIRE_AUTH` are both disabled, since this instance isn't internet-exposed (LAN + Pi-hole local DNS only, matching the Invidious deployment it fronts) and per-user accounts aren't needed for this household. Points at:

- `PUBLIC_DEFAULT_INVIDIOUS_INSTANCE`: `https://invidious.sirddail.net`
- `PUBLIC_DEFAULT_COMPANION_INSTANCE`: `https://invidious-companion.sirddail.net`
- SponsorBlock / DeArrow / Return YouTube Dislike: public community instances (matches upstream's own default docker-compose example)

See `base/deployment.yaml` for the full rationale on each setting.

## Structure

- `base/` — namespace, Deployment, Service, VPA (recommendation-only, D-012)
- `overlays/` — ArgoCD entry point

Deployed via [`dvystrcil/argocd-projects`](https://github.com/dvystrcil/argocd-projects)'s `materialious` Application.
