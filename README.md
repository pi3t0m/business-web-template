# business-web-template

Reusable foundation for building and deploying modern business websites.

The goal of this repository is to avoid rebuilding the same application structure, security controls, contact flow, Docker setup, and deployment pipeline for every new client website.

This template is intended for websites such as:

- construction companies
- IT support companies
- mechanics
- installers
- local service businesses
- small agencies
- professional service providers
- lead-generation landing pages

---

## Main goals

The template should make it possible to:

- create a new client website quickly
- reuse common frontend sections
- reuse contact and lead handling
- keep client-specific content separate from infrastructure
- deploy websites in containers
- protect public forms against spam and bots
- support future AI integrations
- keep deployment portable between VPS and cloud platforms

---

## Technology stack

### Frontend

- React
- Vite
- TypeScript
- Tailwind CSS

### Backend

- Node.js
- Fastify
- TypeScript
- Zod

### Infrastructure

- Docker
- Docker Compose
- Caddy
- GitHub Actions
- GitHub Container Registry

### External services

- Resend
- Cloudflare Turnstile

Future integrations may include:

- LLM providers
- CRM systems
- databases
- Azure
- Terraform
- centralized monitoring

---

## Architecture

High-level flow:

```text
Visitor
   |
   v
Cloudflare
   |
   v
Caddy
   |
   +----------------------+
   |                      |
   v                      v
Frontend                API
React/Vite              Fastify
   |                      |
   |                      +-------------------+
   |                      |                   |
   |                      v                   v
   |                  Turnstile            Resend
   |
   v
Business website
```

For detailed architecture, see:

```text
ARCHITECTURE.md
```

---

## Repository structure

Expected project structure:

```text
business-web-template/
│
├── AGENTS.md
├── ARCHITECTURE.md
├── README.md
├── .gitignore
├── .env.example
├── docker-compose.yml
├── Caddyfile
│
├── frontend/
│   └── src/
│       ├── components/
│       ├── sections/
│       ├── layouts/
│       ├── pages/
│       ├── config/
│       ├── content/
│       ├── hooks/
│       ├── lib/
│       └── styles/
│
├── api/
│   └── src/
│       ├── routes/
│       ├── services/
│       ├── schemas/
│       ├── config/
│       ├── plugins/
│       ├── lib/
│       └── types/
│
└── .github/
    └── workflows/
```

---

## Template responsibilities

The template contains reusable technical foundations:

- frontend structure
- reusable page sections
- design system
- contact form architecture
- Fastify API
- lead normalization
- Resend integration
- Cloudflare Turnstile integration
- rate limiting
- honeypot protection
- Docker configuration
- Caddy configuration
- CI/CD foundation

---

## Client project responsibilities

A real client project created from this template should contain:

```text
PROJECT.md
```

`PROJECT.md` describes the specific client.

Typical information includes:

- company name
- industry
- services
- branding
- visual direction
- colors
- contact information
- service area
- enabled sections
- form fields
- domain
- SEO requirements
- integrations

Client-specific data should not be added to the original template repository.

---

## Typical workflow

The intended workflow is:

```text
business-web-template
        |
        v
create new client repository
        |
        v
add PROJECT.md
        |
        v
customize content and branding
        |
        v
Codex implementation
        |
        v
tests
        |
        v
Docker image
        |
        v
deployment
```

---

## Creating a new client website

Recommended approach:

1. Create a new repository from this template.
2. Add client-specific `PROJECT.md`.
3. Configure public company information.
4. Configure theme and branding.
5. Select required sections.
6. Add client content and images.
7. Configure contact form fields.
8. Configure required environment variables.
9. Run validation and tests.
10. Build Docker images.
11. Deploy using the configured CI/CD workflow.

Example repository names:

```text
abc-construction-site
example-it-support-site
example-mechanic-site
```

---

## Client content structure

Client-specific public configuration should be stored separately from application logic.

Example:

```text
frontend/src/config/
├── site.ts
├── theme.ts
├── navigation.ts
└── sections.ts
```

Content:

```text
frontend/src/content/
├── services.ts
├── projects.ts
├── testimonials.ts
├── faq.ts
├── benefits.ts
└── process.ts
```

This makes it possible to customize a client website without rewriting core application logic.

---

## Environment variables

Real secrets must not be committed.

Create a local:

```text
.env
```

based on:

```text
.env.example
```

Expected environment variables may include:

```env
NODE_ENV=
API_PORT=

RESEND_API_KEY=
CONTACT_EMAIL=
EMAIL_FROM=

TURNSTILE_SECRET_KEY=
VITE_TURNSTILE_SITE_KEY=
```

Important:

Anything prefixed with:

```text
VITE_
```

is public and may be visible in the browser.

Never expose private API keys through frontend environment variables.

---

## Contact flow

Initial contact flow:

```text
Contact form
   |
   v
POST /api/contact
   |
   v
Backend validation
   |
   v
Honeypot
   |
   v
Rate limit
   |
   v
Cloudflare Turnstile verification
   |
   v
Lead Service
   |
   v
Email Service
   |
   v
Resend
```

The frontend must never call Resend directly.

---

## Health check

The backend should expose:

```text
GET /api/health
```

Expected response:

```json
{
  "status": "ok"
}
```

This endpoint is intended for:

- Docker health checks
- monitoring
- deployment validation

---

## Local development

Once the frontend and backend are implemented, local development should follow this general workflow.

### Frontend

```bash
cd frontend
npm install
npm run dev
```

### Backend

```bash
cd api
npm install
npm run dev
```

---

## Local Docker environment

The complete application should eventually be runnable with:

```bash
docker compose up --build
```

To stop the environment:

```bash
docker compose down
```

To validate Docker Compose configuration:

```bash
docker compose config
```

---

## Validation

Before considering changes complete, run the relevant checks.

Typical frontend and backend validation:

```bash
npm run lint
npm run typecheck
npm run test
npm run build
```

Infrastructure validation:

```bash
docker compose config
docker compose build
```

Exact commands may evolve as the project is implemented.

---

## Production deployment

The intended initial production model is:

```text
GitHub
   |
   v
GitHub Actions
   |
   v
GitHub Container Registry
   |
   v
VPS
   |
   v
Docker Compose
   |
   v
Caddy
   |
   v
Client domain
```

The production server should pull pre-built Docker images.

The VPS should not normally compile the application directly.

---

## Hosting strategy

The first production model is optimized for:

- low cost
- simplicity
- repeatability
- container isolation
- easy migration

Multiple small client websites may run on one VPS as separate Docker stacks.

Each client website should have:

- separate repository
- separate environment configuration
- separate domain
- separate containers
- separate deployment process where practical

Do not build one monolithic application for all clients.

---

## Cloud portability

The project should remain portable.

Initial deployment:

```text
Docker
→ VPS
```

Future deployment may use:

```text
Docker
→ Azure Container Apps
```

The application should avoid unnecessary dependence on a specific hosting provider.

---

## Future AI support

Future versions may include:

- AI lead summarization
- AI lead classification
- suggested replies
- AI chat widget
- conversational lead collection

Both traditional forms and future AI chat should use the same internal lead model.

AI API keys must remain backend-only.

---

## Security

Security is a default requirement.

Important rules:

- never commit `.env`
- never commit API keys
- never expose backend secrets to the frontend
- validate all public input on the server
- use Cloudflare Turnstile
- use rate limiting
- use a honeypot
- avoid unnecessary open ports
- do not expose raw production stack traces
- do not give Codex unrestricted production root access

For full working rules, see:

```text
AGENTS.md
```

---

## Documentation

The repository uses:

```text
AGENTS.md
```

for Codex and development working rules.

```text
ARCHITECTURE.md
```

for technical architecture.

```text
README.md
```

for setup and usage instructions.

Client repositories additionally use:

```text
PROJECT.md
```

for client-specific requirements.

---

## Current status

This repository is currently in the initial architecture and scaffolding phase.

Planned implementation order:

```text
1. repository documentation
2. frontend scaffolding
3. backend scaffolding
4. health endpoint
5. Docker
6. Caddy
7. contact form
8. Resend
9. Turnstile
10. rate limiting
11. tests
12. CI
13. GHCR
14. production deployment
```

---

## Project philosophy

The main principle is:

```text
build the reusable foundation once
customize only what is client-specific
```

The template should reduce repeated work while still allowing each client website to have its own visual identity and business content.
