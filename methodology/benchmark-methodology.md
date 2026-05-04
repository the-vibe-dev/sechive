# Benchmark Methodology

Vantix benchmark evidence should be reproducible, label the test mode clearly, and link back to proof material.

## Supported Result Types

- XBOW-style campaigns
- OWASP Juice Shop runs
- CTF or room proof inventories
- Bug bounty proof inventories

Each imported result records status, vulnerability class, black-box/source-aware labels, evidence references, proof-pack links, and model/cost summary when available.

## Import Formats

JSON imports use a top-level campaign object with optional `cases` and `model_cost`. Markdown imports support bullet rows like:

```md
# Juice Shop
- Admin login: success - auth weakness (proof-packs/admin-login)
```

## Claims Boundary

Imported benchmark data is treated as evidence inventory. Public claims should cite the exact result set, target version, methodology, and proof-pack hash.
