# ARCHITECTURE.md

## 1. Purpose

`business-web-template` is a reusable foundation for creating modern business websites quickly without rebuilding infrastructure and application architecture from scratch.

The template is intended for small and medium business websites such as:

- construction companies
- IT support companies
- mechanics
- installers
- local service companies
- professional service providers
- small agencies
- landing pages with lead generation

The template should provide a shared technical foundation while allowing each client project to have different:

- branding
- colors
- typography
- content
- services
- images
- page sections
- contact form fields
- SEO metadata
- integrations

The goal is to reuse approximately 70–80% of the technical foundation across client websites.

---

# 2. High-level architecture

The initial architecture is:

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
   |                      |
   |                      v
   |                  Verification
   |
   v
Business website
```

The frontend and backend are separate application components.

The frontend must not contain server-side secrets.

The backend is responsible for all operations requiring trust, validation, external API credentials, or business logic.

---

# 3. Technology stack

## Frontend

Use:

- React
- Vite
- TypeScript
- Tailwind CSS

Preferred supporting libraries:

- React Hook Form
- Zod
- Lucide
- Motion when animations are actually needed

Avoid large UI frameworks unless there is a clear business reason.

Examples of frameworks that should not be used by default:

- Material UI
- Bootstrap
- Ant Design

The visual identity of each client website should be implemented using the shared design system and project configuration.

---

## Backend

Use:

- Node.js
- Fastify
- TypeScript

Backend responsibilities include:

- API endpoints
- server-side input validation
- anti-bot verification
- rate limiting
- contact form processing
- email delivery
- lead normalization
- health checks
- future LLM integration
- future CRM integration
- future database persistence

The backend should remain lightweight and modular.

---

# 4. Repository structure

Preferred structure:

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
│   ├── src/
│   │   ├── components/
│   │   ├── sections/
│   │   ├── layouts/
│   │   ├── pages/
│   │   ├── config/
│   │   ├── content/
│   │   ├── hooks/
│   │   ├── lib/
│   │   └── styles/
│   │
│   ├── public/
│   ├── package.json
│   ├── tsconfig.json
│   └── Dockerfile
│
├── api/
│   ├── src/
│   │   ├── routes/
│   │   ├── services/
│   │   ├── schemas/
│   │   ├── config/
│   │   ├── plugins/
│   │   ├── lib/
│   │   └── types/
│   │
│   ├── package.json
│   ├── tsconfig.json
│   └── Dockerfile
│
└── .github/
    └── workflows/
```

Client-specific projects created from this template should additionally contain:

```text
PROJECT.md
```

`PROJECT.md` should describe the specific client requirements.

---

# 5. Frontend architecture

The frontend should be built from reusable sections.

Typical section library:

```text
sections/
├── Hero
├── About
├── Services
├── Benefits
├── Statistics
├── Portfolio
├── Process
├── Testimonials
├── FAQ
├── Contact
├── Location
└── Footer
```

Sections should be reusable but not overly abstract.

The goal is not to create a generic website builder.

The goal is to create a reusable business website foundation.

Each client project may:

- enable or disable sections
- reorder sections
- provide custom content
- adjust visual styling
- replace section variants

---

# 6. Site configuration

Public client configuration should be separated from application logic.

Recommended structure:

```text
frontend/src/config/
├── site.ts
├── theme.ts
├── navigation.ts
└── sections.ts
```

Example responsibilities:

## `site.ts`

Contains public business information such as:

- company name
- public email
- phone number
- business address
- service area
- social media links
- default SEO metadata

Example concept:

```ts
export const siteConfig = {
  companyName: "Example Company",
  phone: "+48 000 000 000",
  email: "contact@example.com",
  city: "Wrocław",
}
```

No private secrets may be stored here.

---

## `theme.ts`

Contains visual configuration such as:

- primary color
- accent color
- background color
- typography choices
- border radius
- layout spacing

The theme should be customizable without rewriting components.

---

## `navigation.ts`

Contains:

- navigation labels
- page anchors
- CTA labels

---

## `sections.ts`

Controls section availability and selected variants.

Example:

```ts
export const sections = {
  hero: true,
  about: true,
  services: true,
  portfolio: true,
  testimonials: true,
  faq: true,
  contact: true,
}
```

---

# 7. Content architecture

Business content should be separated from UI components.

Recommended structure:

```text
frontend/src/content/
├── services.ts
├── projects.ts
├── testimonials.ts
├── faq.ts
├── benefits.ts
└── process.ts
```

Components should render content from these files instead of containing large amounts of hardcoded client text.

This makes client customization easier and reduces unnecessary code changes.

---

# 8. Design system

The shared template should contain a lightweight design system.

Reusable primitives should include:

- container
- section wrapper
- heading
- paragraph styles
- buttons
- cards
- form controls
- badges
- icon wrappers
- responsive layout utilities

The design system should define consistent:

- spacing
- typography
- breakpoints
- form states
- button states
- focus states
- animation behavior

The base template should remain visually neutral enough to support different industries.

---

# 9. Client project design

A real client project should define its design direction in `PROJECT.md`.

Example for a construction company:

```text
Visual direction:
Premium industrial minimalism

Base colors:
- graphite
- black
- off-white

Accent:
- muted orange or another selected brand color

Characteristics:
- large project photography
- strong editorial typography
- generous whitespace
- subtle animation
- strong quote-request CTA
```

These choices belong to the client project, not to the generic template.

---

# 10. Contact form architecture

The contact system should be designed as a reusable lead-generation subsystem.

Frontend flow:

```text
Contact Form
   |
   v
POST /api/contact
```

Backend flow:

```text
POST /api/contact
   |
   v
Parse request
   |
   v
Validate payload
   |
   v
Check honeypot
   |
   v
Rate limiting
   |
   v
Verify Cloudflare Turnstile
   |
   v
Normalize lead
   |
   v
Lead Service
   |
   +-----------> Email Service
   |
   +-----------> Database later
   |
   +-----------> LLM analysis later
   |
   +-----------> CRM later
```

The frontend must not communicate directly with Resend.

---

# 11. Lead model

All incoming contact sources should eventually normalize into one internal lead model.

Conceptual structure:

```ts
type Lead = {
  id?: string
  name: string
  email: string
  phone?: string
  company?: string
  service?: string
  location?: string
  budget?: string
  timeline?: string
  message: string
  source: "contact-form" | "ai-chat"
  createdAt: string
}
```

The exact schema may evolve.

The important architectural rule is that both:

- traditional contact forms
- future AI chat

should generate the same internal lead format.

---

# 12. Contact form fields

The base template should support a core field set:

```text
name
email
phone
message
```

Optional configurable fields may include:

```text
company
service
location
budget
timeline
preferred contact method
attachment
```

Client projects may enable only the fields they need.

The backend must validate all fields independently of frontend validation.

---

# 13. Validation

Use schema-based validation.

Preferred:

- Zod

Validation must occur on the backend.

Frontend validation is only for better user experience.

The server is the final trust boundary.

Invalid requests should be rejected before any external integration is executed.

---

# 14. Anti-bot architecture

All public contact forms must use layered bot protection.

Required controls:

```text
Cloudflare Turnstile
+
server-side verification
+
honeypot
+
rate limiting
+
backend validation
```

## Turnstile

The frontend receives only the public site key.

Example:

```text
VITE_TURNSTILE_SITE_KEY
```

The backend stores:

```text
TURNSTILE_SECRET_KEY
```

The secret key must never be sent to the browser.

The backend must verify the Turnstile token before the lead is processed.

---

# 15. Honeypot

The contact form should contain an invisible field that normal users do not fill.

If the field contains a value, the request should be treated as suspicious and rejected or silently ignored.

The honeypot is only an additional layer.

It must not replace Turnstile or rate limiting.

---

# 16. Rate limiting

Public mutation endpoints should be rate limited.

Example protected endpoint:

```text
POST /api/contact
```

Rate limits should be configurable.

The goal is to reduce:

- spam
- abuse
- brute-force requests
- unnecessary email API usage
- future LLM API costs

Rate limiting should not make legitimate form submissions difficult.

---

# 17. Email architecture

Use:

- Resend

The backend should expose an internal email service.

Example conceptual module:

```text
services/email.service.ts
```

The contact route should not contain direct Resend implementation details.

Preferred flow:

```text
contact route
   |
   v
lead service
   |
   v
email service
   |
   v
Resend
```

This makes it possible to replace Resend later without rewriting the contact route.

---

# 18. Email content

Email notifications should be structured and readable.

Example:

```text
NEW WEBSITE LEAD

Name:
Jan Kowalski

Email:
jan@example.com

Phone:
...

Service:
House construction

Location:
Wrocław

Budget:
...

Message:
...

Source:
contact-form
```

Future AI analysis may add:

```text
AI Summary
Priority
Category
Recommended next action
```

The original user message must always remain available.

---

# 19. Future LLM integration

LLM functionality is not part of the first implementation.

The architecture should, however, support adding it without redesigning the contact system.

Possible future capabilities:

- lead summarization
- lead categorization
- priority classification
- extracting structured project details
- suggested response
- internal routing
- AI website chat

The LLM must not directly control production infrastructure or email sending.

Preferred pattern:

```text
Lead Service
   |
   v
LLM Service
   |
   v
Structured analysis
```

The application decides what to do with the analysis.

---

# 20. Future AI chatbot

The future chatbot should use the backend API.

Do not place LLM API keys in the frontend.

Conceptual architecture:

```text
Visitor
   |
   v
AI Chat Widget
   |
   v
Fastify API
   |
   v
Chat Service
   |
   +------> LLM Provider
   |
   +------> Company Knowledge
   |
   v
Lead Service
```

The bot should be able to gather lead information conversationally.

Example:

```text
AI:
What service are you interested in?

Customer:
I want to renovate a 70 m² apartment.

AI:
Where is the apartment located?

Customer:
Wrocław.
```

At the end, the chat can create the same `Lead` object used by the traditional form.

---

# 21. AI safety principles

Future AI components must:

- not expose private prompts
- not expose API keys
- not invent binding offers
- not make contractual commitments
- not invent prices
- not bypass backend validation
- not directly access production infrastructure
- not execute arbitrary user-provided commands

Business-critical operations must remain controlled by application logic.

---

# 22. API structure

Initial public routes:

```text
GET /api/health
POST /api/contact
```

Potential future routes:

```text
POST /api/chat
GET /api/services
```

Do not add unused endpoints in advance.

Follow YAGNI.

---

# 23. Health endpoint

The backend must expose:

```text
GET /api/health
```

Expected response:

```json
{
  "status": "ok"
}
```

Health checks must not expose:

- environment variables
- secrets
- database credentials
- internal network information
- stack traces

---

# 24. API error model

Public API responses should be predictable.

Example:

```json
{
  "error": "Unable to send message"
}
```

Do not expose raw exceptions or internal stack traces in production.

Detailed technical information should go to server logs instead.

---

# 25. Logging

Use Fastify structured logging.

Logs should help diagnose:

- application startup
- API failures
- validation failures
- Turnstile verification problems
- Resend problems
- unexpected runtime errors

Never log:

- passwords
- API keys
- authentication tokens
- full Turnstile tokens
- private credentials

---

# 26. Environment variables

Real secrets must be stored outside Git.

Expected variables may include:

```text
NODE_ENV
API_PORT

RESEND_API_KEY
CONTACT_EMAIL
EMAIL_FROM

TURNSTILE_SECRET_KEY

VITE_TURNSTILE_SITE_KEY
```

`.env.example` should contain only placeholder values.

Example:

```env
RESEND_API_KEY=
CONTACT_EMAIL=
EMAIL_FROM=

TURNSTILE_SECRET_KEY=
VITE_TURNSTILE_SITE_KEY=
```

---

# 27. Docker architecture

Production applications must run in containers.

Initial services:

```text
caddy
frontend
api
```

Conceptual architecture:

```text
Docker Compose
│
├── caddy
│
├── frontend
│
└── api
```

Application services should communicate over an internal Docker network.

Only required public ports should be exposed.

---

# 28. Frontend container

The frontend should be built using a multi-stage Dockerfile.

Conceptually:

```text
Node build stage
   |
   v
Vite production build
   |
   v
minimal runtime
```

The runtime image should not contain unnecessary development dependencies.

---

# 29. Backend container

The backend should use a multi-stage Dockerfile.

Conceptually:

```text
Node build stage
   |
   v
TypeScript build
   |
   v
production runtime
```

The production container should:

- contain only required files
- use production dependencies
- run as non-root where practical
- expose only the API port internally

---

# 30. Reverse proxy

Use:

- Caddy

Caddy is responsible for:

- incoming HTTP traffic
- HTTPS
- automatic certificate management
- reverse proxy routing

Conceptual routing:

```text
/
→ frontend

/api/*
→ api
```

The application containers should not normally be exposed directly to the public Internet.

---

# 31. HTTPS

Production websites must use HTTPS.

Caddy should manage certificates automatically.

No private TLS keys should be committed to Git.

---

# 32. Cloudflare

Cloudflare may be used for:

- DNS
- proxying
- DDoS protection
- Turnstile
- additional traffic protection

The application should not depend on Cloudflare proxying to function correctly.

Turnstile may be used independently.

---

# 33. Deployment architecture

Initial target:

```text
GitHub
   |
   v
GitHub Actions
   |
   v
Container Registry
   |
   v
VPS
   |
   v
Docker Compose
```

Preferred registry:

- GitHub Container Registry

Flow:

```text
git push
   |
   v
CI validation
   |
   v
Docker build
   |
   v
Push image to GHCR
   |
   v
VPS pulls image
   |
   v
docker compose up -d
```

The VPS should not be responsible for compiling the full application.

---

# 34. Production hosting strategy

The first hosting model should optimize for:

- low cost
- simplicity
- repeatability
- client isolation
- easy maintenance

A shared VPS may host multiple small business websites.

Each client should have:

- separate repository
- separate container stack
- separate environment configuration
- separate domain
- separate deployment pipeline where practical

Do not create one monolithic application containing all clients.

---

# 35. Client isolation

Client websites should be operationally isolated.

Preferred:

```text
client-a-site
client-b-site
client-c-site
```

rather than:

```text
one-application-for-all-clients
```

A failure or deployment for one customer should not require rebuilding every client website.

---

# 36. Future cloud migration

Applications should be designed to remain portable.

Today:

```text
Docker
→ VPS
```

Future:

```text
Docker
→ Azure Container Apps
```

or another compatible container platform.

Application code should not depend heavily on VPS-specific implementation details.

---

# 37. Future Azure architecture

For larger clients, the architecture may evolve toward:

```text
GitHub
   |
   v
GitHub Actions
   |
   v
Azure Container Registry
   |
   v
Azure Container Apps
   |
   +------> Key Vault
   |
   +------> Azure Monitor
   |
   +------> Managed Database
```

This is not required for the initial version.

---

# 38. CI pipeline

Pull requests should run:

```text
install
lint
typecheck
test
build
```

Where applicable also validate:

```text
docker compose config
docker build
```

Pull requests must not deploy to production.

---

# 39. CD pipeline

A successful production merge may perform:

```text
validation
   |
   v
Docker build
   |
   v
push to registry
   |
   v
production deployment
```

Production credentials must be stored using GitHub Secrets or another approved secret management system.

---

# 40. Security boundaries

The architecture should follow these trust boundaries:

```text
Browser
UNTRUSTED
   |
   v
Backend API
TRUST BOUNDARY
   |
   v
External services
```

Never trust:

- browser validation
- client-provided IDs
- client-provided prices
- Turnstile results without backend verification
- arbitrary request payloads

Every request must be validated server-side.

---

# 41. Secret management

Never commit:

```text
.env
*.pem
*.key
credentials/
secrets/
```

Production credentials should be managed outside the repository.

Possible future secret stores:

- GitHub Secrets
- Azure Key Vault
- another dedicated secrets manager

---

# 42. Frontend security

The frontend may contain only public values.

Any variable prefixed with:

```text
VITE_
```

must be assumed to be visible to end users.

Therefore never use frontend variables for:

- private API keys
- Resend secret keys
- Turnstile secret keys
- database credentials
- SSH credentials

---

# 43. SEO

The template should support:

- page title
- meta description
- canonical URL
- Open Graph metadata
- social preview image
- sitemap
- robots.txt
- semantic heading hierarchy

Client projects provide the actual SEO content.

---

# 44. Accessibility

Use semantic HTML.

Forms must include:

- labels
- keyboard support
- visible focus states
- understandable validation errors

Images should include meaningful alternative text when appropriate.

Accessibility is part of the base template and not an optional client feature.

---

# 45. Responsive design

The application must be responsive.

Minimum target categories:

```text
mobile
tablet
desktop
```

Mobile usability is a first-class requirement.

Do not design desktop-only layouts and attempt to repair them later.

---

# 46. Performance

The template should prefer:

- minimal JavaScript
- optimized assets
- compressed images
- lazy loading where appropriate
- minimal dependencies
- production caching

Avoid adding libraries solely for minor visual effects.

---

# 47. Animations

Animations should be restrained.

Preferred:

- subtle entrance transitions
- hover feedback
- small image movement
- controlled scroll effects

Avoid:

- excessive parallax
- constant motion
- distracting transitions
- animation that delays access to content

Animation must support the design rather than dominate it.

---

# 48. Client-specific project file

Each client project created from the template should contain:

```text
PROJECT.md
```

It should describe:

- company name
- industry
- business goals
- target customers
- services
- contact details
- service area
- required pages
- enabled sections
- visual direction
- colors
- typography preferences
- CTA
- form fields
- domain
- SEO requirements
- required integrations

`PROJECT.md` must not contain private credentials.

---

# 49. Template vs client responsibilities

## Template contains

```text
application architecture
shared UI components
shared sections
form infrastructure
security controls
Docker
CI/CD foundation
Resend integration structure
Turnstile integration structure
```

## Client project contains

```text
branding
content
images
services
projects
testimonials
SEO text
domain
business-specific form configuration
```

This separation is a core architectural principle.

---

# 50. Non-goals

The initial template is not intended to be:

- a full CMS
- WordPress replacement
- visual website builder
- CRM
- chatbot platform
- customer portal
- SaaS platform

Those capabilities may be added later only when required.

The initial goal is a reliable reusable foundation for business websites and lead generation.

---

# 51. Architectural priorities

In priority order:

1. Security
2. Simplicity
3. Reusability
4. Maintainability
5. Performance
6. Developer experience
7. Portability
8. Scalability

Avoid sacrificing simplicity for theoretical future scale.

---

# 52. Initial implementation scope

Version 1 should include:

```text
React/Vite frontend
Fastify API
TypeScript
Tailwind CSS

Reusable business sections

GET /api/health
POST /api/contact

Zod validation
honeypot
rate limiting
Cloudflare Turnstile
Resend

Docker
Docker Compose
Caddy

.env.example
```

CI/CD and production deployment may be implemented immediately after the local application architecture is verified.

---

# 53. Future roadmap

Possible later additions:

```text
AI lead summarization
AI chatbot
database lead history
CRM integration
client dashboard
file uploads
monitoring
centralized logs
backups
Terraform
Azure deployment
multi-VPS hosting
```

These should not be implemented until required.

---

# 54. Final architecture principle

The system should make this workflow possible:

```text
business-web-template
        |
        v
new client repository
        |
        v
PROJECT.md
        |
        v
custom content + branding
        |
        v
Codex customization
        |
        v
tests
        |
        v
Docker image
        |
        v
deployment
        |
        v
client website
```

The template should reduce repeated work without forcing every business website to look identical.
