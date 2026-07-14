# GitHub Endpoints Firewall Guidelines

This repository contains verified, public-source artifacts for GitHub endpoint allowlisting and firewall policy guidance work.

## Scope

This repo covers:

- GitHub IP ranges and domains from `https://api.github.com/meta`
- GitHub guidance for restricted-network access and IP allow lists
- Public guidance links for Check Point, FortiGate, Forcepoint Web Gateway, Ericom RBI, and Island

This repo does not contain:

- Private or tenant-specific firewall policy
- Personal SSH keys
- Unpublished vendor documentation

## Authoritative sources

| Artifact | Origin | Notes |
|---|---|---|
| `github-meta.json` | `https://api.github.com/meta` | Live GitHub Meta API snapshot; source of IP ranges, domain lists, and SSH host keys |
| `github-ip-cidrs.tsv` | `https://api.github.com/meta` | Flattened CIDR list generated from the Meta API |
| `github-domains.tsv` | `https://api.github.com/meta` | Flattened domain/FQDN inventory generated from the Meta API |
| `plan.md` | `https://api.github.com/meta`, GitHub docs, vendor docs | Research/implementation plan that documents scope and workflow |

## Reference links

- GitHub Meta API: `https://api.github.com/meta`
- GitHub IP address guidance: `https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/about-githubs-ip-addresses`
- GitHub restricted-network guidance: `https://docs.github.com/en/get-started/using-github/allowing-access-to-githubs-services-from-a-restricted-network`
- Check Point Application Control and URL Filtering: `https://sc1.checkpoint.com/documents/R81/WebAdminGuides/EN/CP_R81_SecurityManagement_AdminGuide/Topics-SECMG/Creating-Application-Control-and-URL-Filtering-Rules.htm`
- FortiGate firewall policy: `https://docs.fortinet.com/document/fortigate/7.4.0/administration-guide/656084/firewall-policy`
- FortiGate FQDN addresses: `https://docs.fortinet.com/document/fortigate/7.6.4/administration-guide/707266/fqdn-addresses`
- Forcepoint filtering service: `https://help.forcepoint.com/websec/en-us/on-prem/85/web_admin_guide/guid-a945557b-21be-4b7c-85aa-8ef07aac9dc8.html`
- Forcepoint technical docs: `https://help.forcepoint.com/docs/Tech_Pubs/index.html`
- Ericom SWG/RBI docs: `https://docs.ericom.com/p/ericom-swg-and-rbi`
- Island enterprise browser: `https://www.island.io/enterprise-browser`
- Island support portal: `https://support.island.io/solutions/vdi-and-daas`

## Files

- `github-meta.json` — raw live output from `https://api.github.com/meta`
- `github-ip-cidrs.tsv` — flattened IP/CIDR list by category
- `github-domains.tsv` — flattened domain/FQDN patterns by category
- `plan.md` — implementation/research plan used for this work

## Guide: `gh api /meta`

Use GitHub CLI to fetch the same metadata endpoint used in this project. This is the fastest way to regenerate the repo artifacts from the source of truth.

```bash
gh api /meta > github-meta.json
```

### Quick examples

Get top-level keys:

```bash
gh api /meta --jq 'keys'
```

Get all IP ranges for a specific category (example: Actions):

```bash
gh api /meta --jq '.actions'
```

Get domain patterns used for policy allowlisting:

```bash
gh api /meta --jq '.domains'
```

Flatten category + CIDR rows (TSV):

```bash
gh api /meta --jq '
  to_entries[]
  | select(.value | type == "array")
  | .key as $k
  | .value[]
  | select(type=="string" and contains("/"))
  | "\($k)\t\(.)"
' > github-ip-cidrs.tsv
```

## Unified feed strategy by platform

| Platform | Native dynamic object/feed | Can point at self-hosted list | Best fit |
|---|---|---|---|
| Check Point | Yes — External Network Feed | Yes | One HTTPS feed can contain IPs, domains, or both |
| FortiGate | Yes — IP Address External Feed | Yes for IPs; FQDN objects for domains | Split into `ips.txt` feed plus FQDN address objects |
| Forcepoint Web Gateway | Partial — custom URL/category constructs | Yes, via URL/category import or sync | Use custom URL lists or categories, not one universal feed object |
| Ericom RBI | No public feed-object doc found | Likely via policy/API sync | Maintain allowlists in a central source and push into policy |
| Island | No public feed-object doc found | Likely via policy/API sync | Manage URL lists/policies centrally and sync from source of truth |

## Recommended architecture

Use one self-hosted source of truth and publish platform-specific outputs from it:

- `ips.txt` for feed-based IP consumers
- `domains.txt` for FQDN or URL-list consumers
- `github-services.json` if you want a single canonical API payload

Then map each product to the format it supports:

- Check Point: one external network feed for IPs + domains
- FortiGate: external IP feed for IPs, FQDN objects for domains
- Forcepoint: URL/category list import or API-driven sync
- Ericom / Island: policy/API sync from the same source data
