# Contributing

Thank you for helping improve the Networking Fundamentals Handbook.

## Good contributions

- correct a technical error and cite an authoritative standard or vendor document;
- improve a confusing explanation without reducing accuracy;
- add a reproducible lab or sanitized packet capture;
- add a focused diagram that clarifies packet flow or component relationships;
- fix broken navigation, accessibility text, spelling, or formatting.

## Documentation requirements

- Write clear technical English and define terms before using them.
- Follow [the chapter template](docs/_templates/CHAPTER-TEMPLATE.md).
- Use documentation address ranges such as `192.0.2.0/24`, `198.51.100.0/24`, and `203.0.113.0/24` in public examples.
- Explain what commands do, when to use them, and how to interpret important output.
- Mark environment-specific behavior instead of presenting it as universal.
- Cite the relevant RFC, standard, or official documentation for protocol and version-sensitive claims.
- Update `REFERENCES.md` when introducing a new authoritative source.
- Do not commit credentials, tokens, personal data, private topology details, or unsanitized captures.
- Prefer Mermaid for maintained flows; add alt text for images.

## Workflow

1. Create a focused branch.
2. Make one coherent change.
3. Verify commands and links.
4. Use a Conventional Commit, for example `docs: explain ARP packet flow`.
5. Open a pull request describing the learner benefit and validation performed.

## Review checklist

- [ ] The change is technically accurate.
- [ ] Links and relative navigation work.
- [ ] Commands are safe and tested in the stated environment.
- [ ] Diagrams render on GitHub.
- [ ] Examples use safe documentation data.
- [ ] New captures and images include source/license information.
- [ ] The roadmap and indexes are updated when scope changes.
