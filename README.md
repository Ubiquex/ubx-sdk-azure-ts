# ubx-sdk-azure-ts

TypeScript bindings for the `hashicorp/azurerm` Terraform provider, generated
by [ubx](https://github.com/ubiquex/ubiquex) (`ubx sdk gen --lang ts`). One
package per derived Azure service boundary (`kubernetes/`, `network/`,
`storage/`, ...), one file per resource type, all nested under
`azurerm/` — matches the real Terraform provider source,
`hashicorp/azurerm`, exactly.

Regenerated automatically on a weekly schedule (and via manual dispatch) by
`.github/workflows/version-watch.yml` — it checks the real
[Terraform Registry provider-versions API](https://registry.terraform.io/v1/providers/hashicorp/azurerm/versions)
for a newer `hashicorp/azurerm` release, and opens a PR with the
regenerated diff for review (never auto-merges). The version this repo was
last generated from is tracked in `VERSION`, not `.ubx/config.hcl` — this
repo carries no ubx stack/config of its own, only generated bindings.

Depends on the shared runtime, `@ubx/sdk`, via a real, published
`jsr:@ubx/sdk@^0.1.0` import (`deno.json`) — no vendoring, born directly on
the real JSR publish (same as `ubx-sdk-kubernetes-ts`).

Every file under a service directory except `doc.ts` is generated —
do not hand-edit; re-run `ubx sdk gen` (or wait for the automated PR) after a
provider version bump. `deno.json`/`deno.lock` are hand/tooling-maintained,
not codegen output.

**TypeScript sibling to `ubx-sdk-azure-go` (UBI-115) — real findings, not
assumed to match Go's own:**

- **Real schema scale, confirmed independently**: **1,103 resource types**
  for `hashicorp/azurerm@5.0.0`, exactly matching Go's own count — 144
  derived service packages, `deno check --frozen` clean across the full
  1,246-file tree (largest file `azurerm/kubernetes/cluster.ts`, 1,139
  lines — no compiler-crash-class issue).
- **The ticket's own real ask — UBI-115's v5-protocol finding confirmed
  identical for TS's own generation path, checked directly not assumed**:
  `hashicorp/azurerm@5.0.0` negotiates tfplugin **protocol v5** for
  `--lang ts` generation too (re-verified directly against the real
  binary). Expected, since protocol negotiation happens once in `provider/`
  — shared, language-independent code that runs identically regardless of
  `--lang` — but checked live rather than assumed. `deno check --frozen`
  ran clean with no v5-specific gap in the TS template path.
- **A real correction to this ticket's own stated premise, worth recording
  rather than silently accepting**: the ticket text assumed "AWS/Google/
  Kubernetes may all have been v6," making this "TS's first v5-only
  provider." Checked directly against all four real provider binaries at
  their actual pinned rollout versions rather than trusted as background
  color: `hashicorp/aws@6.54.0` → **v5**, `hashicorp/google@7.42.0` →
  **v5**, `hashicorp/kubernetes@3.2.1` → **v6**, `hashicorp/azurerm@5.0.0`
  → **v5**. **Kubernetes was the outlier, not the norm** — AWS-TS
  (UBI-104) and Google-TS (UBI-109) were already exercising a v5 provider
  the entire time; this repo is the *third* v5-protocol TS repo in the
  rollout, not the first. The v6-specific `NestedType` shape (UBI-112) was
  never a TS-wide risk to begin with — it was always a Kubernetes-specific
  one, now confirmed rather than assumed.
- **Sibling-`_config` collision (UBI-96/108) and bare-version-suffix
  collision (UBI-112)**: both wire-name-level facts, language-independent
  — UBI-115's own Go-side check (0/1,103 candidates; 3 version-suffixed
  names, none bare) carries over unchanged, reconfirmed here by the same
  clean `deno check --frozen` result (a collision would have failed
  typechecking, not just `go build`).
- **Zero `ubiquex`-core changes needed this session.**
