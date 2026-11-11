# Java Full-Stack Interview Preparation

A practical, searchable knowledge base for Java full-stack developer interviews.

## Documentation website

**Website:** https://syamkumar-c.github.io/java-fullstack-interview-prep/

The site is built with [MkDocs Material](https://squidfunk.github.io/mkdocs-material/) and deployed to GitHub Pages through GitHub Actions.

## Repository structure

- `docs/` — documentation content
- `docs/topics/` — 12 interview-preparation topic categories
- `docs/roadmap.md` — study roadmap and priorities
- `mkdocs.yml` — site theme, navigation, and Markdown configuration
- `requirements.txt` — documentation build dependencies
- `.github/workflows/deploy.yml` — build and deploy workflow

## Learning format

Topic lessons will cover the definition, purpose, internal working, code examples, trade-offs, common mistakes, interview questions, and practical project scenarios.

## Phases

- **Phase 1:** Documentation platform, topic navigation, search, and GitHub Pages deployment.
- **Phase 2:** Detailed topic explanations, code examples, diagrams, and expandable interview Q&A.
- **Phase 3:** Revision tracking and progress dashboard.
- **Phase 4:** Full topic coverage and coding/system-design practice.

## Local preview

```bash
pip install -r requirements.txt
mkdocs serve
```

Open http://127.0.0.1:8000 to preview the site locally.
