# Nivara Playground

Unified portal for Nivara's Enterprise AI proof of concepts — a protected showroom with an integrated AI Playground for testing LLMs across providers.

[![Live](https://img.shields.io/badge/Live-playground.nivara.io-8B7355?style=flat-square)](https://playground.nivara.io)
[![Next.js](https://img.shields.io/badge/Next.js-16-black?style=flat-square&logo=next.js)](https://nextjs.org)
[![TypeScript](https://img.shields.io/badge/TypeScript-5-blue?style=flat-square&logo=typescript)](https://www.typescriptlang.org/)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind-4-06B6D4?style=flat-square&logo=tailwindcss)](https://tailwindcss.com/)
[![Vercel AI SDK](https://img.shields.io/badge/Vercel_AI_SDK-6-black?style=flat-square)](https://ai-sdk.dev)
[![Deploy](https://img.shields.io/badge/Deploy-Vercel-black?style=flat-square&logo=vercel)](https://vercel.com)

---

## Overview

Nivara Playground serves three purposes:

1. **POC Showroom** — A protected dashboard where team members browse, search, and explore all Enterprise AI prototypes with detailed cards, tech stacks, and live demo links
2. **Project Chat** — An AI assistant on each project page that explains the POC in natural language — ideal for non-technical stakeholders during prospect meetings
3. **AI Playground** — A multi-provider LLM testing environment (admin-only) with streaming chat, model comparison, parameter tuning, tool calling, and code export

---

## Current Projects

| Project | Sector | Live Demo | Status |
|---------|--------|-----------|--------|
| **LobbyAI** | Legal Tech | [lobbyai.netlify.app](https://lobbyai.netlify.app) | Completed |
| **Nina®** | AI Planning | [check.nivara.io](https://check.nivara.io) | Completed |
| **OTB Intelligence Platform** | Luxury Retail | [otb.nivara.io](https://otb.nivara.io) | Completed |

---

## Features

### POC Dashboard

- **Searchable grid** — Filter POCs by sector, tech stack, or free-text search
- **Detailed project pages** — Overview, features, key highlights, architecture, tech badges, and external links (repo + demo)
- **Dynamic data** — POC cards fetch live data from GitHub API (releases, topics, last updated) via ISR with 5-minute revalidation; curated content from `content/projects-config.json`

### Project Chat (All Users)

- **AI-powered project assistant** — Push-panel chat drawer on every project detail page, powered by GPT-4.1 Mini
- **Context-aware** — System prompt includes project description, architecture, features, tech stack, and key highlights for accurate, grounded answers
- **Suggested questions** — Pre-built conversation starters tailored to each project (e.g. "What does LobbyAI do in simple terms?")
- **Designed for prospects** — Non-technical stakeholders can understand what a POC does, its value proposition, and competitive advantages through natural language

### AI Playground (Admin Only)

- **Multi-provider chat** — Stream responses from OpenAI (GPT-4.1, GPT-4o, o3-mini), Anthropic (Claude Sonnet 4.5, Haiku 4.5), Google (Gemini 2.0 Flash/Pro), and Groq (Llama 3.3 70B, Mixtral 8x7B)
- **Compare mode** — Send the same prompt to two models side-by-side and compare responses, latency, and token usage
- **Parameter controls** — Temperature, max tokens, top-p sliders with real-time adjustment
- **System prompt editor** — Customize model behavior per conversation
- **Tool calling** — Built-in tools (web search, calculator, datetime) to test function calling capabilities
- **Token usage tracking** — Input/output token counts displayed per message
- **Code export** — Export your configuration as TypeScript, Python, or cURL snippets
- **Conversation persistence** — Save and resume conversations via Supabase

### Authentication & Access Control

- **Self-service signup** — Team members register with their `@nivara.io` email; no manual account creation needed
- **Domain whitelist** — Only `@nivara.io` emails accepted; all others rejected
- **Automatic role assignment** — `nicola@nivara.io` → Admin, all other `@nivara.io` → Viewer
- **Supabase Auth** — Passwords hashed with bcrypt, no credentials stored in code
- **3-layer RBAC** — Edge middleware blocks routes, UI hides restricted links, API endpoints reject unauthorized calls

| Role | Dashboard | Project Chat | AI Playground | API Chat | Signup |
|------|-----------|--------------|---------------|----------|--------|
| Admin (`nicola@nivara.io`) | Full access | Full access | Full access | Full access | Auto |
| Viewer (`*@nivara.io`) | Full access | Full access | Blocked | Blocked | Self-service |
| External | Blocked | Blocked | Blocked | Blocked | Rejected |

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Framework | Next.js 16 (App Router, Turbopack) |
| Language | TypeScript 5 |
| Styling | Tailwind CSS v4 with custom Nivara theme |
| AI | Vercel AI SDK 6 + provider registry (OpenAI, Anthropic, Google, Groq) |
| Auth | NextAuth v5 + Supabase Auth (bcrypt, self-service signup, domain whitelist) |
| Database | Supabase (PostgreSQL — user auth + conversation persistence) |
| Data | GitHub API + ISR (dynamic POC cards, 5-min revalidation) |
| Validation | Zod 4 |
| Icons | Lucide React |
| Fonts | Playfair Display + Inter (via next/font) |
| DNS | Cloudflare (CNAME → Vercel) |
| Deploy | Vercel (auto-deploy from GitHub) |

---

## Getting Started

### Prerequisites

- Node.js 18+
- npm 9+
- A Supabase project (for auth and conversations)
- API keys for at least one LLM provider (for the AI Playground)

### Install & Run

```bash
git clone git@github.com:nivara-ai/nivara-playground.git
cd nivara-playground
npm install
cp .env.example .env.local  # then fill in your keys
npm run dev
```

The app starts at `http://localhost:3000`. In production: [playground.nivara.io](https://playground.nivara.io).

### Environment Variables

| Variable | Description | Required |
|----------|-------------|----------|
| `AUTH_SECRET` | NextAuth session encryption key | Yes |
| `AUTH_TRUST_HOST` | Trust the host header (`true` for Vercel) | Yes |
| `NEXT_PUBLIC_SUPABASE_URL` | Supabase project URL | Yes |
| `NEXT_PUBLIC_SUPABASE_ANON_KEY` | Supabase anonymous key | Yes |
| `SUPABASE_SERVICE_ROLE_KEY` | Supabase service role key (for signup) | Yes |
| `GITHUB_TOKEN` | GitHub PAT with `repo` scope (for dynamic POC cards) | Yes |
| `OPENAI_API_KEY` | OpenAI API key (GPT-4.1, GPT-4o, o3-mini) | For Playground |
| `ANTHROPIC_API_KEY` | Anthropic API key (Claude Sonnet 4.5, Haiku 4.5) | For Playground |
| `GOOGLE_GENERATIVE_AI_API_KEY` | Google AI key (Gemini 2.0 Flash/Pro) | For Playground |
| `GROQ_API_KEY` | Groq API key (Llama 3.3, Mixtral) | For Playground |

Generate an auth secret:
```bash
openssl rand -base64 32
```

### Supabase Setup

1. Create a Supabase project at [supabase.com](https://supabase.com)
2. Run the schema in the SQL Editor:
   ```bash
   cat supabase-schema.sql
   # Copy and paste into Supabase SQL Editor
   ```
3. Copy the project URL, anon key, and service role key to `.env.local`

---

## Adding a New POC

Edit `content/projects-config.json` and add an entry:

```json
{
  "slug": "my-new-poc",
  "name": "My New POC",
  "tagline": "Short description of the POC",
  "description": "Longer description with context and value proposition.",
  "sector": "Industry Name",
  "techStack": ["React", "Supabase", "etc"],
  "features": ["Feature 1", "Feature 2"],
  "repoUrl": "https://github.com/nivara-ai/repo-name",
  "demoUrl": "https://demo.nivara.io",
  "screenshot": null,
  "status": "completed",
  "highlights": ["Key metric or achievement"],
  "architecture": "Frontend → Backend → Database",
  "github": { "owner": "nivara-ai", "repo": "repo-name" }
}
```

The `github` field enables dynamic data: latest release, tech stack (from repo topics), and last updated date are fetched automatically via GitHub API every 5 minutes (ISR). Push to `main` — Vercel auto-deploys and the new POC appears in the dashboard.

---

## Project Structure

```
nivara-playground/
├── app/
│   ├── layout.tsx                         # Root layout (fonts, metadata)
│   ├── page.tsx                           # Smart redirect by role
│   ├── login/page.tsx                     # Login page
│   ├── signup/page.tsx                    # Self-service signup (@nivara.io only)
│   ├── dashboard/
│   │   ├── layout.tsx                     # Authenticated layout (sidebar + header)
│   │   ├── page.tsx                       # POC grid with filters
│   │   └── [slug]/
│   │       ├── page.tsx                   # POC detail page (SSG + ISR)
│   │       └── project-detail-client.tsx  # Client wrapper (chat drawer state)
│   ├── playground/
│   │   ├── layout.tsx                     # Playground layout (conversation sidebar)
│   │   ├── page.tsx                       # Chat playground
│   │   └── compare/page.tsx              # Compare mode (side-by-side)
│   ├── not-found.tsx                      # Custom 404 page
│   └── api/
│       ├── auth/
│       │   ├── [...nextauth]/             # NextAuth API routes
│       │   └── signup/route.ts            # Registration API (domain whitelist, rate limited)
│       ├── chat/route.ts                  # Streaming chat API (Vercel AI SDK)
│       └── conversations/                 # Conversation CRUD API (Zod validated)
├── components/
│   ├── poc-card.tsx, sidebar.tsx, ...     # Dashboard components
│   ├── project-chat-drawer.tsx            # AI chat drawer for project pages
│   └── playground/                        # Playground components
│       ├── chat-area.tsx                  # Main chat interface
│       ├── compare-view.tsx              # Side-by-side model comparison
│       ├── config-panel.tsx              # Model + parameter configuration
│       ├── code-export-modal.tsx         # Export as TS/Python/cURL
│       ├── conversation-sidebar.tsx      # Conversation history
│       ├── message-bubble.tsx            # Chat message rendering
│       ├── model-selector.tsx            # Provider/model dropdown
│       ├── parameter-controls.tsx        # Temperature, tokens, top-p
│       ├── system-prompt-editor.tsx      # System prompt textarea
│       └── token-usage.tsx               # Token usage display
├── content/projects-config.json            # ← POC data + GitHub mapping (edit this!)
├── lib/
│   ├── auth.ts                            # NextAuth + Supabase Auth (RBAC, domain whitelist)
│   ├── github.ts                          # GitHub API client (releases, topics, languages)
│   ├── next-auth.d.ts                     # NextAuth type augmentation
│   ├── projects.ts                        # Async project data (config + GitHub merge)
│   ├── types.ts                           # TypeScript interfaces
│   ├── utils.ts                           # Utility helpers (relative time, etc.)
│   ├── ai/
│   │   ├── models.ts                     # Model definitions (10 models, 4 providers)
│   │   ├── project-prompt.ts             # Project chat system prompt builder
│   │   └── registry.ts                   # Vercel AI provider registry
│   └── supabase/
│       ├── client.ts                     # Supabase client (lazy init)
│       └── conversations.ts              # Conversation persistence helpers
├── middleware.ts                           # Route protection + RBAC enforcement
└── supabase-schema.sql                    # Database schema for conversations
```

---

## Supported Models

| Provider | Models | Context Window |
|----------|--------|----------------|
| **OpenAI** | GPT-4.1, GPT-4.1 Mini, GPT-4o, o3-mini | 128K–1M |
| **Anthropic** | Claude Sonnet 4.5, Claude Haiku 4.5 | 200K |
| **Google** | Gemini 2.0 Flash, Gemini 2.0 Pro | 1M |
| **Groq** | Llama 3.3 70B, Mixtral 8x7B | 32K–128K |

---

## Security Hardening

The codebase has been through a full security audit (31 issues identified, all addressed):

- **No eval/Function** — Calculator tool uses a recursive descent math parser instead of `new Function()` (CRITICAL RCE fix)
- **IDOR protection** — All conversation/message API endpoints verify resource ownership
- **Row Level Security** — Supabase RLS policies enforce `user_id = auth.uid()` on all tables
- **Rate limiting** — Signup endpoint rate-limited to 5 requests per 15 minutes per IP
- **Input validation** — Zod schemas validate all API request bodies (conversations, messages, chat)
- **AUTH_SECRET validation** — Runtime check ensures the secret is set before authorizing

---

## Architecture Decisions

- **Supabase Auth** for user management — bcrypt password hashing, domain whitelist enforcement, no credentials in source code
- **Hybrid ISR** for dynamic POC cards — curated config merged with live GitHub API data (releases, topics, languages), revalidated every 5 minutes
- **Static Generation (SSG)** for project detail pages via `generateStaticParams` — zero runtime cost, instant loads
- **Edge Middleware** for auth + RBAC — route protection and role enforcement at CDN level before page rendering
- **Vercel AI SDK provider registry** — unified `registry.languageModel("provider:model")` pattern decouples UI from provider-specific APIs
- **Streaming responses** via `streamText` + `toUIMessageStreamResponse` — real-time token streaming with metadata
- **Config + API data** — Curated POC data in JSON config, enriched with live GitHub data (releases, tech stack from topics, last push date)
- **Tailwind v4 `@theme inline`** — CSS-native design tokens, no config file needed

---

## License

Proprietary — Nivara AI © 2025–2026
