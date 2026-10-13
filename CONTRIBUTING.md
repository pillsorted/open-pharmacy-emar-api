# Contributing

Thank you for helping. Issues and pull requests are welcome from eMAR suppliers, pharmacies, care
providers, clinicians and developers.

## Ground rules

1. **Original work only.** Do not copy field names, message structures, code lists or documentation
   from any supplier's proprietary API, or from any material you received under a confidentiality
   obligation. Describe the problem in your own words and propose a design in this spec's vocabulary.
2. **Say where it comes from.** If your change adds a design choice, add a row to
   [docs/design-rationale.md](docs/design-rationale.md) naming its source (a public standard, general
   practice, or your own experience in plain terms).
3. **No patient data.** Examples use made-up people, IDs and codes.
4. **Sign off every commit** with the [Developer Certificate of Origin](https://developercertificate.org/):
   `git commit -s`. Your sign-off confirms the contribution is yours to give under the Apache 2.0
   licence and follows rule 1.

## How changes are decided

- Open an issue for anything bigger than a fix, so the design can be discussed first.
- The maintainers (PillSorted) review and merge. When three or more independent organisations have
  implemented the spec, governance will move to a group that includes them.
- Additive changes go into the current minor version. Breaking changes wait for the next major
  version and need a migration note.

## Checking your change

```sh
npx @redocly/cli lint openapi.yaml
```
