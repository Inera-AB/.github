

# Exampels of Inera's FHIR Repositories


|GitHub Repository|Implentation Guide (CI Build)|Released version(s)|
|:---|:---|:---|
| [Inera FHIR Landingpage](https://github.com/Inera-AB/Inera-FHIR-landingpage)| [N/A| [fhir.inera.se](httls://fhir.inera.se) |
| [Inera FHIR CareTeam](https://github.com/Inera-AB/FastaKontakter)| [Inera FHIR CareTeam](https://Inera-AB.github.io/FastaKontakter)| - |
| [Inera FHIR Forms](https://github.com/Inera-AB/abc)| [Inera FHIR Forms](https://Inera-AB.github.io/abc/)| - |
| [Inera EHDS](https://github.com/Inera-AB/EHDS-TK)| [Inera EHDS](https://Inera-AB.github.io/EHDS-TK/)| - |

## How an IG gets published

Every IG repo is created from [IneraFHIRTemplate](https://github.com/Inera-AB/IneraFHIRTemplate) and calls the shared workflows in [Inera-AB/fhir-ig-workflows](https://github.com/Inera-AB/fhir-ig-workflows). There is no custom publish script per repo, and no release-please — releases are published with **IG Publisher's own `-go-publish` mode**, and versions are bumped by hand.

```
  ┌───────────────────┐
  │ IneraFHIRTemplate │
  └───────────────────┘
            │  Use this template
            ▼
  ┌──────────────────────────┐
  │ new IG repo (FSH source) │
  └──────────────────────────┘
                │  push: main / develop / feature/** / support/**
                ▼
  ┌────────────────────────────────┐
  │ build.yml                      │
  │ FSH --> SUSHI --> IG Publisher │
  └────────────────────────────────┘
                   │
                   ▼
  ┌──────────────────────────────────────────────┐
  │ own GitHub Pages: inera-ab.github.io/<repo>/ │
  │ + dispatch ig-deployed --> updates the       │
  │   fhir-portal catalog row (no version yet)   │
  └──────────────────────────────────────────────┘
                          │
                          ▼
    tag "1.2.0" (no "v")   --or--   manual run on a branch
                          │
                          ▼
  ┌───────────────────────────┐
  │ release.yml               │
  │ publisher.jar -go-publish │
  └───────────────────────────┘
                │
                ▼
  ┌──────────────────────────────────────────────────┐
  │ tag     --> inera-ab/fhir-portal                 │
  │             (fhir.inera.se/ig/<name>/<version>/) │
  │ manual  --> inera-ab/fhir-portal-test            │
  │             (test only -- never fhir.inera.se)   │
  └──────────────────────────────────────────────────┘
                            │
                            ▼
  ┌────────────────────────────────────────┐
  │ fhir.inera.se/ig/                      │
  │ catalog of every IG -- a row can exist │
  │ before the IG has its first release    │
  └────────────────────────────────────────┘
                       │
                       ▼
  ┌──────────────────────┐
  │ package-feed.xml /   │
  │ publication-feed.xml │
  └──────────────────────┘
              │  diff saved as a build artifact
              ▼
  ┌───────────────────────────────────┐
  │ HL7 FHIR/ig-registry              │
  │ (PR not sent yet -- IGs aren't on │
  │  packages.fhir.org until it is)   │
  └───────────────────────────────────┘
```

A few things worth knowing that aren't obvious from the diagram:

- **The catalog on `fhir.inera.se/ig/` updates on every push, not just releases.** `build.yml` fires the same `ig-deployed` event as `release.yml`, just with no version attached — the same way HL7's own IGs show a rolling CI build alongside their releases. That's why some rows there have no version tag yet.
- **Releases are real IG Publisher releases**, not a hand-rolled copy of the output folder: `-go-publish` owns `package-list.json`, `history.html`, the version banners on every page, and the package/publication feeds.
- **A manual run of `release.yml` on a branch is a test publication** to `inera-ab/fhir-portal-test` — it never touches `fhir.inera.se`.
- **The package/publication feeds already exist**, but Inera hasn't opened the PR to [`FHIR/ig-registry`](https://github.com/FHIR/ig-registry) yet, so published IGs don't show up on [packages.fhir.org](https://packages.fhir.org) yet either.


