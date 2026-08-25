# FossaPay Docs Contributor Guide

This repository contains the public FossaPay documentation site. It is a docs-only workspace built with Mintlify.

## Supported Local Environment

- Node.js `v20.17.0` or newer
- Mintlify CLI `v4.0.1125` or newer
- A terminal with access to the repository root and `docs.json`

Node.js `v20.17.0+` is the minimum supported runtime for the Mintlify CLI, and Mintlify CLI releases before `4.0.1125` can mis-handle local preview behavior in docs repositories.

## Install

Install the Mintlify CLI globally:

```bash
npm i -g mint
```

If your local CLI is out of date, update it:

```bash
mint update
```

Verify the installed version before you start editing:

```bash
mint --version
```

## Preview

Run the local docs server from the repository root:

```bash
mint dev
```

By default, the preview runs on `http://localhost:3000`.

Useful flags:

```bash
mint dev --port 3333
mint dev --no-open
```

## Validation

Run these checks before opening a pull request:

```bash
mint validate
mint broken-links
git diff --check
```

- `mint validate` checks the docs build and validates the OpenAPI file referenced in `docs.json`
- `mint broken-links` catches broken internal links
- `git diff --check` catches whitespace and patch-format issues

## Repository Structure

- [`docs.json`](/Users/codewithmide/Documents/github/docs/docs.json) - site configuration, navigation, and OpenAPI wiring
- [`index.mdx`](/Users/codewithmide/Documents/github/docs/index.mdx) and [`introduction.mdx`](/Users/codewithmide/Documents/github/docs/introduction.mdx) - landing pages
- [`quickstart.mdx`](/Users/codewithmide/Documents/github/docs/quickstart.mdx) - end-to-end onboarding flow
- [`concepts/`](/Users/codewithmide/Documents/github/docs/concepts) - product concepts and implementation guidance
- [`guides/`](/Users/codewithmide/Documents/github/docs/guides) - longer workflows such as collections and payouts
- [`api-reference/`](/Users/codewithmide/Documents/github/docs/api-reference) - endpoint documentation and the OpenAPI contract
- [`essentials/`](/Users/codewithmide/Documents/github/docs/essentials) - documentation system references and reusable docs patterns
- [`white-label/`](/Users/codewithmide/Documents/github/docs/white-label) - white-label product documentation, currently hidden from navigation

## Review Workflow

1. Create a branch for the documentation change.
2. Edit the relevant MDX or OpenAPI files.
3. Preview the site with `mint dev`.
4. Validate the site with `mint validate` and `mint broken-links`.
5. Fix any broken links, schema errors, or playground issues.
6. Open a pull request and include the affected pages, endpoints, or screenshots when helpful.

## Deployment

The site deploys from the default branch through Mintlify.

Before merging:

- Confirm `mint validate` passes
- Confirm `mint broken-links` passes
- Confirm `docs.json` still points at the correct OpenAPI file
- Confirm any API reference changes match the production contract

After merge:

- Mintlify runs its checks
- A production deployment is generated from the default branch

## Ownership

- API contract changes are owned by the API/backend team
- Endpoint documentation should match the production contract exactly
- Docs structure, navigation, examples, and prose are owned by the documentation team
- Any change that alters request bodies, response schemas, error codes, or endpoint paths must be reviewed alongside the API contract

## Conventions

- Use production URLs only
- Do not add sandbox or test-environment instructions unless the platform supports them
- Keep endpoint examples aligned with the live API reference
- Prefer absolute file links in internal contributor docs
