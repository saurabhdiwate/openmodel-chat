<p align="center">
  <img src="frontend/public/img/logo.svg" alt="OpenModel Chat" width="96" height="96" />
</p>

<h1 align="center">OpenModel Chat</h1>

<p align="center">
  <strong>A production-grade, privacy-first AI chat platform with streaming responses, cross-conversation memory, web search, image generation, and a multi-user admin console.</strong>
</p>

<p align="center">
  <a href="https://github.com/saurabhdiwate/openmodel-chat/actions/workflows/ci.yml"><img src="https://github.com/saurabhdiwate/openmodel-chat/actions/workflows/ci.yml/badge.svg" alt="CI" /></a>
  <a href="https://opensource.org/licenses/MIT"><img src="https://img.shields.io/badge/License-MIT-green" alt="MIT License" /></a>
  <a href="https://github.com/saurabhdiwate/openmodel-chat/releases"><img src="https://img.shields.io/badge/version-0.2.1-blue" alt="Version" /></a>
</p>

---

A complete, full-stack AI chat application. Works with any LLM provider — OpenAI, Anthropic, Google, Groq, DeepSeek, xAI, Ollama and more — or runs **completely offline** with local models. Your data stays on your machine: no vendor lock-in, no tracking, full control.

## Screenshots

<p align="center">
  <img src="screenshots/chat.png" alt="Chat Interface" width="90%" />
</p>

<p align="center">
  <em>The chat workspace — sidebar navigation, folder organization, pinned chats, and a streaming AI conversation.</em>
</p>

<p align="center">
  <img src="screenshots/login.png" alt="Login" width="49%" />
  <img src="screenshots/admin-panel.png" alt="Admin Panel" width="49%" />
</p>

<p align="center">
  <img src="screenshots/settings.png" alt="Settings" width="90%" />
</p>

<p align="center">
  <em>Branded authentication, a complete admin console, and a rich settings experience with themes, fonts, voice, and memory controls.</em>
</p>

## Key Features

| Category | Highlights |
|---|---|
| **Chat** | Real-time token streaming (Vercel AI SDK), markdown with Shiki syntax highlighting, LaTeX via KaTeX, inline editing, source citations |
| **100+ AI providers** | 17 built-in (OpenAI, Anthropic, Google, Vertex, Bedrock, Azure, Groq, Mistral, xAI, DeepSeek, Cohere, Fireworks, Cerebras, OpenRouter, Replicate, Ollama, LM Studio) + 180 auto-discovered via models.dev |
| **Cross-chat memory** | The AI learns your preferences, projects, and style across conversations — with per-user and per-chat control |
| **Web search** | Autonomous web search with SSRF-protected fetching, real-time status, and clickable source citations |
| **Images** | Vision/multimodal uploads + AI image generation (DALL-E 3, FLUX, OpenRouter) with inline preview and download |
| **Attachments** | Files, office docs, and images with previews, downloads, and security validation |
| **Import** | One-click conversation import from ChatGPT exports |
| **Themable UI** | 15+ curated themes, dark/light mode, custom fonts and sizes — fully theme-aware CSS-variable system |
| **Voice** | Speech-to-text input and text-to-speech output with voice selection |
| **Power user** | Keyboard shortcuts, virtualized chat lists, drag-and-drop files, folder organization, search |
| **Multi-user** | Authentication with roles (admin / member / readonly), user management, password resets |
| **Customization** | Change the app name, logo, and branding from the admin panel |
| **Provider Hub** | Discover and add providers with one click, pull Ollama models right from the UI |

## Architecture

```
┌────────────────────────────────────────────────────────────┐
│                    Preact SPA (Vite)                        │
│   TanStack Router  ·  TanStack Query  ·  Zustand  ·  AI SDK │
└───────────────────────────┬────────────────────────────────┘
                            │  REST /api (streamed responses)
┌───────────────────────────▼────────────────────────────────┐
│                        Hono API (Bun)                       │
│   Auth · RBAC · Rate limits · Security headers · Validation │
├───────────────────────────┬────────────────────────────────┤
│  Provider Factory (100+)  │  Web Search · Image Gen · Memory │
│  models.dev auto-discovery │  SSRF-protected fetch · AI tools │
├───────────────────────────┴────────────────────────────────┤
│                    SQLite (bun:sqlite)                      │
│   Users · Chats · Files · Folders · Settings · Memories     │
│   AES-encrypted provider API keys                           │
└─────────────────────────────────────────────────────────────┘
```

A **shared workspace package** (`@openmodel/shared`) defines the contracts — constants, schemas, and formatters — used by both frontend and server, so the API surface stays in sync.

See [docs/architecture.md](docs/architecture.md) for the full architecture documentation with Mermaid diagrams.

## Tech Stack

| Layer | Technology |
|---|---|
| **Frontend** | Preact 10, TanStack Router, TanStack Query, Tailwind CSS 4, Zustand, Vite 7 |
| **Backend** | Hono 4, Bun runtime, Vercel AI SDK (multi-provider streaming) |
| **Database** | SQLite via `bun:sqlite`, encrypted API-key storage |
| **Validation** | Zod schemas at every system boundary |
| **Auth** | Session cookies, Argon2 password hashing, role-based access control |
| **Quality** | 460 tests (Bun test + Vitest), oxlint, Prettier, typed contracts in a shared package |
| **Deploy** | Docker, Caddy (HTTPS), Fly.io, Render, Railway |

## Quick Start

**Prerequisite:** [Bun](https://bun.sh/)

```bash
git clone https://github.com/saurabhdiwate/openmodel-chat.git
cd openmodel-chat
bun install
bun run dev
```

- **Frontend:** http://localhost:3000
- **API:** http://localhost:3001

On first run the server generates an encryption key and initializes the database automatically. Register the first account — it becomes the **admin**.

## Testing

OpenModel Chat uses automated backend and frontend test suites to validate authentication, API behavior, model integrations, file security, UI behavior, permissions, and core application logic.

**Test counts (verified):**
- Server tests: 420 (Bun test)
- Frontend tests: 40 (Vitest)
- **Total: 460 tests**

```bash
bun run test            # server tests (420)
bun run test:frontend   # frontend tests (40)
bun run test:all        # both suites
```

CI runs all tests on every push and pull request to `main`. Tests must pass before a build is attempted.

**Coverage areas:**
- Authentication flows (registration, login, sessions, password changes, rate limiting)
- Chat CRUD, messages, completions, pin/archive, folder organization
- File upload security (type validation, size limits, dangerous file rejection, SSRF protection)
- Admin operations (user management, provider CRUD, audit logging)
- Encryption (AES-256-GCM key roundtrip, masking)
- Middleware (CORS, security headers, body limits, rate limiting)
- Data isolation (cross-user access prevention)
- Frontend API client, error handling, file drag-and-drop, input area behavior

## Security

See [SECURITY.md](SECURITY.md) for the full security policy and vulnerability reporting process.

**Implemented controls:**
- Argon2 password hashing and session-based authentication
- AES-256-GCM encryption for stored provider API keys
- SSRF protection with DNS-level validation and private-IP blocking
- File upload validation (dangerous type rejection, size limits, filename sanitization)
- Rate limiting, CORS policy, and security response headers
- Zod validation at every API boundary
- Role-based access control (admin / member / readonly)
- 460 automated tests covering security-critical paths

## Deployment

```bash
docker compose up -d
```

Production-ready with automatic HTTPS via Caddy:

```bash
docker compose -f docker-compose.yml -f docker-compose.caddy.yml up -d
```

Also ships with ready-to-deploy configs for **Fly.io**, **Render**, and **Railway**. See [docs/architecture.md](docs/architecture.md#deployment) for platform-specific details.

## Demo

A short product walkthrough can demonstrate:

1. Login and multi-user authentication
2. Starting a conversation with streaming responses
3. Switching AI providers and models
4. File attachments and image generation
5. Web search with source citations
6. Cross-chat memory
7. Admin panel — user management, provider configuration, branding

No live demo is currently deployed. To try it locally, follow the Quick Start above.

## Development Workflow

OpenModel Chat uses AI-assisted development tools to accelerate implementation, refactoring, debugging, and documentation.

All changes are validated through:

- Automated tests (460 tests across backend and frontend)
- Linting (oxlint)
- Formatting (Prettier)
- Production build verification
- CI (GitHub Actions)

AI-generated changes are treated like any other code contribution and must pass the same validation process before merging. See [CLAUDE.md](CLAUDE.md) for the agent coding guidelines used in this repository.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for setup instructions, code style guidelines, and the pull request process.

## Roadmap

- Chat export (JSON / Markdown) and Claude import
- Inline message editing and full-text message search
- Per-chat system prompts and temperature control
- Personal prompt templates

## License

MIT — see [LICENSE](LICENSE) for details.
