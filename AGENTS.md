# AGENTS.md

## Purpose

This repository is a reusable business website template.

The repository must remain generic and reusable across different client websites.

Do not add client-specific:

- company names
- domains
- addresses
- phone numbers
- branding
- logos
- images
- business content

Client-specific requirements belong in `PROJECT.md` inside repositories created from this template.

---

## Required reading

Before making changes:

1. Read `AGENTS.md`.
2. Read `ARCHITECTURE.md`.
3. Read `README.md` if setup or deployment behavior is relevant.
4. Inspect the files related to the current task.
5. In client repositories, also read `PROJECT.md` when it exists.

Do not make assumptions that contradict these files.

---

## Working principles

Prefer:

- simple solutions
- small focused modules
- strict TypeScript
- reusable components
- secure defaults
- minimal dependencies
- clear naming
- predictable project structure

Follow:

- KISS
- YAGNI
- least privilege

Do not introduce unnecessary complexity.

---

## Change scope

Only modify files required for the current task.

Do not perform unrelated refactoring.

Do not redesign architecture unless:

- the task explicitly requires it, or
- the existing architecture prevents a correct implementation.

If a significant architectural change is necessary, explain the reason before implementing it.

---

## Frontend rules

Frontend stack:

- React
- Vite
- TypeScript
- Tailwind CSS

Preferred libraries:

- React Hook Form
- Zod
- Lucide
- Motion only when animation is actually required

Avoid adding large UI frameworks unless explicitly requested.

Do not add:

- Material UI
- Bootstrap
- Ant Design

unless there is a clear project requirement.

The frontend must remain easy to customize for different clients.

---

## Backend rules

Backend stack:

- Node.js
- Fastify
- TypeScript

The backend is responsible for:

- API routes
- server-side validation
- rate limiting
- anti-bot verification
- email delivery
- lead processing
- external integrations
- future AI integrations

Do not place trusted business logic in the frontend.

---

## Security

Security is mandatory.

Never:

- commit secrets
- commit `.env`
- expose backend API keys to the frontend
- hardcode credentials
- log secrets
- expose unnecessary ports
- trust client-side validation
- return raw production stack traces
- store SSH keys in the repository

All public input must be validated on the backend.

Client-side validation is only for user experience.

---

## Environment variables

Real configuration secrets belong in environment variables.

Never commit `.env`.

Maintain:

```text
.env.example
```

with placeholder values only.

When adding or removing environment variables:

1. update application configuration
2. update `.env.example`
3. update documentation when needed

Any variable prefixed with:

```text
VITE_
```

must be treated as public.

Never expose through `VITE_`:

- Resend secret keys
- Turnstile secret keys
- database credentials
- private API keys
- SSH credentials

---

## Contact form security

Public contact forms must use layered protection.

Required:

- backend validation
- Cloudflare Turnstile
- server-side Turnstile verification
- honeypot field
- rate limiting

The frontend must not communicate directly with Resend.

Expected flow:

```text
Frontend
   |
   v
Fastify API
   |
   v
Validation
   |
   v
Honeypot
   |
   v
Rate limit
   |
   v
Turnstile verification
   |
   v
Lead Service
   |
   v
Email Service
```

---

## Lead architecture

Contact submissions should be normalized into a reusable internal lead model.

The contact form and future AI chat should eventually use the same lead-processing layer.

Do not tightly couple:

- routes
- Resend
- future LLM providers
- future CRM providers

Use services to keep integrations replaceable.

---

## AI rules

AI functionality is future-facing unless explicitly requested.

Do not implement AI features prematurely.

Future AI components must:

- use backend APIs
- never expose API keys to the frontend
- not bypass backend validation
- not directly control production infrastructure
- not send emails without application-controlled logic
- not invent binding prices or offers

Follow YAGNI.

---

## Docker rules

Production workloads must run in containers.

Do not require Node.js or application dependencies to be installed directly on the production server.

Prefer:

- multi-stage Docker builds
- minimal runtime images
- non-root containers where practical
- `.dockerignore`
- health checks

Do not expose application containers directly unless required.

---

## Docker Compose

The application should be deployable through Docker Compose.

Expected services:

```text
caddy
frontend
api
```

The intended deployment flow should remain close to:

```bash
docker compose pull
docker compose up -d
```

---

## Reverse proxy

Use:

- Caddy

Caddy is responsible for:

- HTTPS
- automatic TLS certificates
- reverse proxying
- frontend routing
- API routing

Expected routing concept:

```text
/
→ frontend

/api/*
→ api
```

---

## CI/CD

Use GitHub Actions.

Pull requests should validate:

```text
install
lint
typecheck
test
build
```

When relevant also validate:

```bash
docker compose config
docker build
```

Pull requests must not deploy to production.

Production deployment should happen only from the intended production branch.

---

## Container registry

Preferred registry:

- GitHub Container Registry

Production servers should pull pre-built images.

Avoid building the entire application directly on the VPS.

---

## Production access

Do not use unrestricted root SSH access for routine deployments unless explicitly required.

Prefer a dedicated deployment user.

Codex should not receive unrestricted production root access.

Preferred trust flow:

```text
Codex
   |
   v
Git
   |
   v
Pull Request
   |
   v
GitHub Actions
   |
   v
Deployment user
   |
   v
Docker
```

---

## Code quality

Prefer:

- small modules
- single responsibility
- explicit types
- readable code
- simple interfaces
- predictable folder structure

Avoid:

- giant files
- duplicated logic
- unnecessary abstraction
- hidden side effects
- clever code that hurts readability

---

## TypeScript

Use strict TypeScript where practical.

Avoid unnecessary:

```ts
any
```

Do not suppress TypeScript errors unless there is a documented reason.

Prefer schema validation at system boundaries.

---

## Dependencies

Before adding a dependency, verify that it is necessary.

Prefer:

- actively maintained packages
- small focused packages
- stable libraries

Do not add dependencies for trivial functionality.

Do not add architectural dependencies without a clear reason.

---

## Frontend design

The base template must remain visually generic.

Client projects may customize:

- typography
- colors
- layouts
- section variants
- imagery
- animation style

The shared frontend should prioritize:

- strong typography
- responsive layouts
- accessibility
- performance
- clear CTA hierarchy
- restrained animation
- mobile-first usability

Avoid excessive visual effects.

---

## Accessibility

Use semantic HTML.

Interactive elements must support keyboard use.

Forms must contain:

- labels
- accessible errors
- visible focus states

Images should contain meaningful alternative text where appropriate.

---

## Performance

Avoid unnecessary JavaScript.

Prefer:

- optimized images
- lazy loading where appropriate
- minimal dependencies
- production builds
- sensible caching

Do not add heavy animation libraries for minor effects.

---

## SEO

The template should support:

- page titles
- meta descriptions
- Open Graph metadata
- canonical URLs
- sitemap
- robots.txt
- semantic heading structure

Client-specific SEO text belongs in the client repository.

---

## Logging

Use structured logging on the backend.

Fastify logger is preferred.

Logs may contain technical diagnostic information but must never contain:

- API keys
- passwords
- authentication tokens
- full Turnstile tokens
- private credentials

---

## Error handling

Public API responses must be predictable.

Do not expose internal implementation details.

Example:

```json
{
  "error": "Unable to process request"
}
```

Detailed errors belong in server logs.

---

## Testing and verification

Before considering a task complete, run all relevant checks.

Typical frontend and backend checks:

```bash
npm run lint
npm run typecheck
npm run test
npm run build
```

Infrastructure checks when applicable:

```bash
docker compose config
docker compose build
```

If a check fails because of the current change:

1. investigate
2. fix the issue
3. run the check again

Do not claim a check passed unless it was actually executed.

---

## Git

Keep changes focused.

Before finishing:

```bash
git status
git diff
```

Review the diff for:

- unrelated changes
- secrets
- temporary files
- debug code
- generated files that should not be committed

Do not create commits unless requested or required by the workflow.

---

## Documentation

Use:

```text
AGENTS.md
```

for stable Codex working rules.

Use:

```text
ARCHITECTURE.md
```

for technical architecture.

Use:

```text
README.md
```

for human setup, usage, and deployment instructions.

Use:

```text
PROJECT.md
```

for client-specific requirements in repositories created from this template.

Do not duplicate large documentation sections across these files.

---

## Definition of Done

A task is complete only when:

- requested functionality is implemented
- relevant TypeScript checks pass
- lint passes
- tests pass where applicable
- production build passes where applicable
- Docker configuration remains valid when affected
- no secrets were introduced
- `.env.example` is current
- documentation is current
- the final diff contains no unrelated changes

At the end of the task, provide a concise summary containing:

1. what changed
2. which files changed
3. which checks were executed
4. whether all checks passed
5. any remaining manual steps
6. any remaining risks

---

## Primary rule

Keep this repository:

- simple
- reusable
- secure
- portable
- containerized
- easy to deploy
- easy to understand

Do not optimize for theoretical future complexity.

The goal is to create new client business websites quickly without rebuilding the same technical foundation each time.
