# GitHub IP/URL Policy Guidance Across Security Platforms — Implementation Plan

## Problem
Build an authoritative list of GitHub IP ranges and GitHub URLs/domains, then translate that inventory into platform-specific configuration guidelines for:
- Check Point firewall policy
- FortiGate firewall policy
- Forcepoint Web Gateway policy
- Ericom RBI policy
- Island policy controls (where GitHub access controls are applicable)

## Current State
- No single target repository was specified for this request.
- Workspace contains many projects; this task is external research + policy-ready guidance, not code changes.
- Session plan file exists and is now expanded to multi-platform scope.

## Proposed Approach
1. Confirm scope as full GitHub cloud footprint and include only source-cited, officially documented endpoints.
2. Gather authoritative source data from GitHub docs/APIs (including official IP metadata endpoints).
3. Build a normalized endpoint inventory split into:
   - IP/CIDR ranges (IPv4 + IPv6 where available)
   - URL/FQDN endpoints required by service function
4. Produce platform-specific policy mappings:
   - Check Point: network objects, URL objects, application/site categories, rule sequencing
   - FortiGate: address objects/groups, FQDN objects, policy packages, SSL inspection considerations
   - Forcepoint Web Gateway: URL/category policy, bypass/allow rules, TLS handling
   - Ericom RBI: domain allow policies, isolation vs direct access decisions
   - Island: browser policy controls for GitHub domains/actions where configurable
5. Produce final deliverable with:
   - Source-cited tables
   - Per-platform configuration guidance and caveats
   - Update/maintenance workflow for changing GitHub endpoints

## Todo Breakdown
1. Confirm output granularity (high-level guidance vs copy/paste-ready config snippets) per platform.
2. Collect official GitHub IP/CIDR data for full cloud footprint.
3. Collect official GitHub URLs/FQDNs and normalize by service use-case.
4. Build baseline endpoint matrix (endpoint -> service -> protocol/port -> rationale/source).
5. Map baseline matrix into Check Point policy guidance.
6. Map baseline matrix into FortiGate policy guidance.
7. Map baseline matrix into Forcepoint Web Gateway policy guidance.
8. Map baseline matrix into Ericom RBI and Island policy guidance.
9. Produce final consolidated, source-cited guideline document.

## Notes and Considerations
- "All GitHub IPs" is dynamic; final guidance must include refresh cadence and automation/update strategy.
- Some platform features are license/version dependent; guidance should flag vendor/version assumptions explicitly.
- Some GitHub services are better controlled via FQDN/URL/application controls than static IPs.
- Scope is full GitHub cloud footprint (including Actions, Packages, GHCR, Copilot, Codespaces, and webhook-related endpoints where officially documented).
- Output level confirmed: include copy/paste-ready configuration snippets where feasible, plus explanatory guidance and caveats.
