# 330 Conceptual Architecture & Tech Stack Questions

**Format:** Question → Answer (interview style)
**Level:** Mixed — 🟢 Junior · 🟡 Mid · 🔴 Senior/Architect
**Companion to:** *Vibe Coding Pre-Build Template* (Document 2 — TRD, Document 5 — Backend Schema)

---

## Table of Contents

| # | Section | Questions |
|---|---|---|
| 1 | Tech Stack Selection Fundamentals | 1–20 |
| 2 | Node.js vs FastAPI vs Others (Backend Runtime) | 21–48 |
| 3 | Frontend Framework Trade-offs | 49–70 |
| 4 | Rendering Strategy: SSR / SSG / ISR / CSR / Edge | 71–85 |
| 5 | Database Selection & Trade-offs | 86–115 |
| 6 | ORM & Data Access Layer | 116–127 |
| 7 | Architecture Patterns: Monolith / Microservices / Serverless | 128–152 |
| 8 | API Design: REST / GraphQL / gRPC / tRPC / WebSockets | 153–172 |
| 9 | Load Balancing | 173–205 |
| 10 | Caching & CDN | 206–220 |
| 11 | Scaling, Queues & Async Processing | 221–233 |
| 12 | Auth & Security Architecture | 234–243 |
| 13 | Deployment, Observability & Cost Architecture | 244–250 |
| 14 | Networking & Protocol Fundamentals | 251–265 |
| 15 | File Storage & Media Architecture | 266–277 |
| 16 | Payments, Billing & Third-Party Integrations | 278–289 |
| 17 | AI / LLM Application Architecture | 290–306 |
| 18 | Environments, Config, Feature Flags & CI/CD | 307–318 |
| 19 | Testing, Data Privacy & Compliance Architecture | 319–330 |

---

# SECTION 1 — Tech Stack Selection Fundamentals (1–20)

**1. 🟢 What does "choosing a tech stack" actually mean beyond picking languages?**
It means committing to a set of constraints: hiring pool, ecosystem maturity, deployment model, operational cost, and the failure modes you're willing to live with. The language is the smallest part of the decision — the surrounding ecosystem (libraries, hosting, monitoring, community answers to your bugs) is what you actually consume daily.

**2. 🟢 Why is "the best tech stack" a meaningless phrase?**
Because "best" is only definable relative to constraints: team skill, time to market, expected scale, budget, and compliance needs. A stack that is optimal for a 3-person startup shipping an MVP in 6 weeks is actively harmful for a 200-engineer regulated fintech, and vice versa.

**3. 🟢 What are the main dimensions you should score a stack against?**
Team familiarity, ecosystem/library availability, performance profile for your workload, operational complexity, hiring availability, cost at expected scale, vendor lock-in risk, and long-term maintenance/LTS support.

**4. 🟡 When should team familiarity override technical superiority?**
Almost always for an MVP or any deadline-bound project. A technically better tool that the team must learn adds an invisible tax on every ticket: slower debugging, worse architecture decisions, and no intuition for failure modes. Superiority only wins when the workload has a hard requirement the familiar stack physically cannot meet.

**5. 🟡 What is "boring technology" and why does it win so often?**
Boring technology (Postgres, Django/Rails/Express, nginx, Linux) has known failure modes, exhaustive documentation, and a decade of Stack Overflow answers. Every novel technology spends part of your innovation budget. Reserve novelty for the one or two places where it's genuinely your competitive advantage.

**6. 🟡 What is vendor lock-in and when is it an acceptable trade?**
Lock-in is when migrating off a provider requires rewriting application logic, not just redeploying. It's acceptable when the provider collapses months of work into days (Supabase auth+RLS, Stripe billing, Vercel edge) and your realistic exit cost is smaller than the time you saved. It's unacceptable when the locked-in component is your core differentiator.

**7. 🔴 How do you quantify the exit cost of a managed platform?**
Estimate (a) proprietary API surface used, (b) data egress volume and cost, (c) behaviours you depend on that have no open equivalent (e.g. Postgres RLS with `auth.uid()`, Vercel ISR semantics), and (d) downtime required for cutover. If any of those has no open-source equivalent, you're not locked into a vendor — you're locked into a rewrite.

**8. 🟡 What's the difference between accidental complexity and essential complexity in stack choice?**
Essential complexity comes from the problem (real-time collaboration is genuinely hard). Accidental complexity comes from your choices (running Kubernetes for a single-container app). Good architecture minimises accidental complexity so the team's energy goes to the essential kind.

**9. 🟡 Why is "we might need to scale to millions of users" a bad reason to pick a stack?**
Because you're paying present-day complexity for a future that statistically won't arrive, and if it does arrive you'll have money and engineers to fix it. Design so scaling is *possible* (stateless services, clean data boundaries), not so it's *pre-built*.

**10. 🔴 What does "design for scalability without building for scale" look like concretely?**
Keep services stateless so you can add replicas; put a queue boundary between request handling and slow work; keep the database schema normalised enough that you can shard later; avoid in-process caches that assume one instance. None of these costs meaningful time upfront, and all of them are painful to retrofit.

**11. 🟢 What is a polyglot stack and what does it cost?**
Using multiple languages/runtimes across services. It costs duplicated tooling (CI, linting, observability agents), fragmented shared libraries, harder on-call rotation, and context-switching for engineers. Justified when a workload has a genuine ecosystem requirement (e.g. Python for ML, Go for a high-throughput proxy).

**12. 🟡 How does compliance (HIPAA, GDPR, PCI, SOC 2) constrain stack choice?**
It restricts hosting regions, mandates data residency and encryption at rest/in transit, requires audit logs, and rules out providers without the relevant certification or BAA. It also pushes you toward fewer third parties, since each one becomes a subprocessor you must document and audit.

**13. 🟡 What is the "conway's law" implication for architecture?**
Your system's structure will mirror your org's communication structure. Three teams will naturally produce three services with awkward seams where the teams don't talk. Choose an architecture that matches the org you have, or reorganise the org deliberately to get the architecture you want.

**14. 🔴 When does the cost model of a stack matter more than its performance?**
When your workload is spiky or low-volume. A serverless stack that's 3× slower per request but costs $5/month beats a always-on cluster costing $300/month for a product with 200 daily users. Performance only becomes the dominant term once volume makes compute a material line item.

**15. 🟡 What questions reveal whether a stack choice was made for the right reasons?**
"What workload characteristic made you pick this?", "What would have to be true for this to be the wrong choice?", "What's your migration path if it is?" If the answers are "it's popular" or "I saw it on Twitter," the decision wasn't made — it was absorbed.

**16. 🟢 What is an ADR (Architecture Decision Record) and why does it matter?**
A short markdown document capturing the context, decision, alternatives considered, and consequences of one architectural choice. It matters because 18 months later nobody remembers *why* Mongo was chosen, so nobody feels safe changing it. ADRs make decisions reversible by making them legible.

**17. 🟡 What is the difference between a reversible and irreversible architecture decision?**
Reversible: which CSS framework, which CI provider, which hosting platform for a containerised app. Irreversible (expensive): database engine after millions of rows, public API contract, auth provider holding user credentials, event schema consumed by external partners. Spend deliberation budget on the irreversible ones.

**18. 🔴 How would you evaluate adopting a brand-new framework in production?**
Check release cadence and breaking-change history, size and diversity of maintainers (bus factor), issue-close latency, whether major companies run it in production, and whether the escape hatch is clean (can you drop to the underlying primitive?). If the framework is a thin layer over something stable, risk is low; if it's a whole new runtime, risk is high.

**19. 🟡 What is "stack sprawl" and how do you contain it?**
The gradual accumulation of overlapping tools — three HTTP clients, two ORMs, four state libraries. Contain it with a written "golden path" (approved default for each layer), a lightweight exception process, and periodic dependency audits. The goal is that any engineer can read any service without new learning.

**20. 🔴 If you inherit a stack you consider wrong, how do you decide whether to migrate?**
Quantify the ongoing pain (incident frequency, velocity loss, hiring difficulty) against migration cost and opportunity cost. Prefer strangler-fig incremental migration over rewrites. Only rewrite when the existing system cannot meet a hard business requirement — "I don't like it" is not a business requirement.

---

# SECTION 2 — Node.js vs FastAPI vs Others (21–48)

**21. 🟢 What is the fundamental concurrency difference between Node.js and FastAPI?**
Node.js is a single-threaded event loop with non-blocking I/O built into the runtime — asynchrony is the default. FastAPI runs on ASGI (Uvicorn) and is async-capable, but Python's GIL means a single worker process executes one bytecode thread at a time, so you scale with multiple worker processes rather than threads.

**22. 🟢 When should you choose Node.js over FastAPI?**
When the workload is I/O-bound with many concurrent connections (chat, notifications, API gateways, BFF layers), when you want one language across frontend and backend, when you need to share TypeScript types/validation between client and server, or when you're already on Next.js and want API routes in the same deployment.

**23. 🟢 When should you choose FastAPI over Node.js?**
When the work touches data science, ML inference, numerical computing, or PDF/document/image processing — Python's ecosystem (NumPy, pandas, PyTorch, transformers, scikit-learn) has no equivalent in Node. Also when you want automatic OpenAPI docs and Pydantic-based validation as a first-class part of the framework.

**24. 🟡 Where is Node.js genuinely the *worse* choice?**
CPU-bound work: image transcoding, cryptographic batch operations, large JSON transforms, ML inference. A blocking computation stalls the entire event loop and every concurrent request on that instance. Worker threads exist but are clumsy compared to Python's process pools or Go's goroutines.

**25. 🟡 Where is FastAPI genuinely the *worse* choice?**
Very high-concurrency long-lived connections (tens of thousands of WebSockets per instance) where Python's per-connection memory overhead and GIL make it costly. Also anywhere you need to share code with a TS frontend, or where cold-start-sensitive serverless deployment matters (Python cold starts are typically heavier).

**26. 🟡 Is FastAPI actually slower than Node.js?**
For pure I/O-bound JSON APIs the gap is small — both are dominated by network and database latency, and Uvicorn+uvloop is fast. The difference shows up under CPU load and in per-instance concurrency ceilings. Benchmarks comparing "hello world" throughput rarely predict real application performance.

**27. 🔴 Explain why "Node is non-blocking so it's faster" is a misleading claim.**
Non-blocking describes *how it waits*, not *how fast it computes*. Node excels at holding many idle connections cheaply. It does not compute faster than Python or Java — it just doesn't waste a thread while waiting on the DB. If your bottleneck is CPU or database, Node's event loop gives you nothing.

**28. 🟡 What is the event loop blocking problem and how do you detect it?**
Any synchronous work (`JSON.parse` on a 50 MB payload, `bcrypt` sync, a tight loop) freezes all request handling. Detect it with event-loop-lag metrics (`perf_hooks.monitorEventLoopDelay`), p99 latency spikes uncorrelated with DB time, and APM flame graphs. Fix by offloading to worker threads, a queue, or a different service.

**29. 🟡 Why does the GIL matter for FastAPI and how do you work around it?**
The GIL prevents true parallel execution of Python bytecode within one process, so CPU-bound endpoints don't benefit from threads. Work around it by running multiple Uvicorn workers (one per core) behind a load balancer, offloading CPU work to Celery/RQ workers, or dropping to C extensions (NumPy, Rust via PyO3) which release the GIL.

**30. 🟡 In FastAPI, what breaks if you use a synchronous DB driver inside an `async def` endpoint?**
The blocking call runs on the event loop and stalls all concurrent requests on that worker — you get the worst of both worlds. Either use an async driver (asyncpg, databases, SQLAlchemy 2.0 async) with `async def`, or use a plain `def` endpoint so FastAPI runs it in a threadpool.

**31. 🟡 Compare the type-safety story of Node/TypeScript vs FastAPI/Pydantic.**
TypeScript gives compile-time types across frontend and backend but erases at runtime, so you still need runtime validation (Zod). Pydantic validates at runtime by design and generates OpenAPI schemas automatically, but the types stop at the Python boundary — your TS frontend needs generated clients to benefit.

**32. 🔴 When would you pick Go over both Node and FastAPI?**
When you need high throughput with predictable low latency and low memory per connection: API gateways, proxies, real-time fan-out, CLI tools, sidecars. Goroutines give cheap true concurrency without the GIL or event-loop blocking. The cost is a smaller web ecosystem and more boilerplate for ordinary CRUD.

**33. 🔴 When is Java/Spring or .NET the right call over Node/FastAPI?**
Large enterprise domains with long-lived teams, heavy transactional integrity requirements, existing enterprise integrations (SAP, Oracle, message buses), and organisations that value strict typing, mature threading, and long-term LTS support over startup speed. Overkill for a 5-endpoint MVP.

**34. 🟡 What is Bun/Deno's pitch against Node, and when is it too early?**
Faster startup, built-in TypeScript, integrated bundler/test runner, and better default APIs. Too early when you depend on native modules with unclear compatibility, need enterprise support contracts, or your hosting/observability vendors don't have first-class support. The migration risk usually outweighs the speed gain for a business app.

**35. 🟡 Should you run FastAPI and Node in the same product?**
Yes, if the split follows a real capability boundary: Node/Next.js for the web BFF and real-time layer, FastAPI for ML inference and data processing. Communicate over HTTP or a queue. It's a mistake when the split is arbitrary — you then pay polyglot cost with no ecosystem benefit.

**36. 🟢 What does Express give you that FastAPI doesn't, and vice versa?**
Express is minimal and unopinionated — you assemble validation, docs, and serialisation yourself. FastAPI ships dependency injection, request/response validation, and auto-generated OpenAPI/Swagger docs out of the box. Express is more flexible; FastAPI is more productive for typed REST APIs.

**37. 🟡 How does the middleware model differ between Express and FastAPI?**
Express uses a linear `(req, res, next)` chain where order is everything and error handling is a special four-arg signature. FastAPI has ASGI middleware plus a dependency-injection system where dependencies are declared per-route and resolved (and cached) by the framework — more declarative, less order-sensitive.

**38. 🔴 Your API is 90% simple CRUD and 10% heavy PDF generation. How do you architect the split?**
Serve the CRUD from whichever runtime the team prefers, and move PDF generation out of the request path entirely: enqueue a job, return 202 with a job ID, process in a Python/worker pool, notify via webhook/websocket/polling. The runtime debate matters far less than getting the slow work off the request thread.

**39. 🟡 Which runtime handles WebSockets better and why?**
Node handles many idle WebSocket connections cheaply because each is just an event-loop handle. FastAPI supports WebSockets well but each connection consumes more memory and you're constrained by worker count. For 50k concurrent sockets, Node (or Go, or a dedicated service like Ably/Pusher/Centrifugo) is the safer bet.

**40. 🟡 How does cold-start behaviour differ across runtimes in serverless?**
Roughly: JS/TS and Go start fastest; Python is moderate but degrades badly with heavy imports (pandas, torch); JVM/.NET are slowest without ahead-of-time compilation or snapshotting. If you're going serverless with FastAPI, keep imports lazy and dependencies lean, or use provisioned concurrency.

**41. 🔴 What operational differences exist between running Node and Python in containers?**
Node images are typically smaller and single-process (one container = one event loop; scale by replicas). Python needs a process manager (Gunicorn with Uvicorn workers) and worker-count tuning per CPU allocation. Memory limits behave differently: Node needs `--max-old-space-size` aligned to the container limit or the OOM killer fires unexpectedly.

**42. 🟡 How do you decide worker counts for a FastAPI deployment?**
Start at `(2 × cores) + 1` for sync-heavy workloads and roughly one worker per core for async-heavy ones, then tune against real p95 latency and CPU utilisation. Too many workers thrash memory and DB connections; too few leave cores idle. Always check that total workers × DB pool size stays under the database's connection limit.

**43. 🔴 Why can adding more application instances make your database slower?**
Each instance holds its own connection pool, so N instances × M pooled connections can exceed Postgres's `max_connections`, causing connection storms, memory pressure, and rejected connections. The fix is a connection pooler (PgBouncer, Supabase pooler, RDS Proxy) sitting between apps and the database.

**44. 🟡 In the template's stack (Next.js + Supabase), when do you still need a separate Node or FastAPI service?**
When you need long-running jobs beyond serverless timeouts, background schedulers, heavy CPU work, third-party webhook processing with retries, or an ML model. Next.js API routes and Supabase Edge Functions cover request/response work; anything durable or long-lived wants its own service.

**45. 🟡 What's the argument against putting all backend logic in Next.js API routes?**
They're coupled to your frontend's deploy cycle and hosting platform, subject to platform execution limits, and awkward to reuse from a mobile app or partner integration. Fine for an MVP; extract to a standalone service once you have a second consumer or a long-running workload.

**46. 🔴 How would you migrate from Next.js API routes to a standalone backend without a big-bang rewrite?**
Introduce a stable API base URL indirection, stand up the new service, and move endpoints one at a time behind that indirection (strangler fig). Keep contracts identical, dual-run with comparison logging if the endpoint is critical, then delete the old route once traffic is fully shifted.

**47. 🟡 What signals tell you your chosen runtime is the actual bottleneck?**
CPU pegged at 100% with low DB time; event-loop lag or worker saturation while the database is idle; latency that improves linearly when you add instances but not when you optimise queries. If DB time dominates traces, changing runtime will change nothing.

**48. 🔴 A team wants to rewrite a working Express API in FastAPI "for performance." How do you respond?**
Ask for the trace data. If p95 is dominated by database and downstream calls (usually true), the rewrite changes nothing and costs months. Propose profiling first, then targeted fixes: indexes, N+1 elimination, caching, connection pooling. Reserve the rewrite conversation for a measured runtime-level ceiling.

---

# SECTION 3 — Frontend Framework Trade-offs (49–70)

**49. 🟢 What problem does a framework like Next.js solve that plain React doesn't?**
Routing, server-side rendering, data fetching conventions, code splitting, image optimisation, and build/deploy pipeline — all pre-wired. Plain React is a view library; you assemble everything else yourself, which is more flexible but slower to ship and easier to get wrong.

**50. 🟢 When is plain React (Vite SPA) the better choice over Next.js?**
Internal tools, dashboards behind auth, and admin panels where SEO is irrelevant and every route requires login. You skip SSR complexity, deploy as static files to any CDN, and avoid server infrastructure entirely.

**51. 🟡 When is Next.js the wrong choice?**
When your app is a pure client-side SPA behind auth (SSR buys nothing but complexity), when you must self-host cheaply and don't want a Node server, when you need a non-Vercel deployment target with unusual constraints, or when the team doesn't understand the server/client component boundary and will fight it daily.

**52. 🟡 Compare React, Vue, and Svelte on the dimensions that actually matter.**
React: largest ecosystem and hiring pool, most third-party components, more boilerplate. Vue: gentler learning curve, excellent official tooling (Router, Pinia), smaller hiring pool in some markets. Svelte: least runtime overhead and cleanest syntax, smallest ecosystem and hiring pool. For most businesses the ecosystem/hiring dimension dominates the DX dimension.

**53. 🟡 When would you deliberately choose *no* framework — server-rendered HTML + htmx/Alpine?**
Content-heavy sites, CRUD admin apps, and anything where interactivity is localised. You get simpler deploys, better default performance, no hydration cost, and no build pipeline. It breaks down when you need rich client state, offline behaviour, or complex optimistic UI.

**54. 🔴 What is the hidden cost of React Server Components?**
A new mental model with a hard serialisation boundary: server components can't hold state or use browser APIs, and passing non-serialisable props across the boundary fails at runtime rather than compile time. Teams frequently "fix" errors by marking everything `'use client'`, which silently reverts to a plain SPA plus extra complexity.

**55. 🟡 What is hydration and why is it a performance problem?**
Hydration is the client re-running React over server-rendered HTML to attach event handlers. The page *looks* ready before it's interactive, producing a poor Interaction-to-Next-Paint. Mitigate with less client JS, streaming/partial hydration, islands architecture, or frameworks like Astro/Qwik that avoid full hydration.

**56. 🟡 What is islands architecture and when does it beat a full SPA?**
Rendering the page as static HTML with small independently hydrated interactive "islands." It wins for content-dominant pages (marketing, blogs, docs, e-commerce listings) where only a search box and cart widget need JS. It's a poor fit for app-like UIs where most of the page is interactive.

**57. 🟢 Tailwind vs a component library like MUI — what's the actual trade-off?**
Tailwind gives full design control with no opinion on components; you build or copy them (shadcn/ui). MUI/Ant give you a complete accessible component set fast but impose a visual identity and are painful to deviate from. Tailwind+shadcn is the common modern compromise: you own the component code.

**58. 🟡 Why do teams regret CSS-in-JS at scale?**
Runtime style generation costs render performance, complicates SSR (style extraction, flashes of unstyled content), and conflicts with React Server Components. Zero-runtime alternatives (Tailwind, CSS Modules, vanilla-extract, Panda) give most of the benefit without the runtime cost.

**59. 🟡 When do you need a global state manager, and when is it premature?**
You need one when the same non-server state is read and written by distant components (auth session, theme, multi-step wizard, editor document). It's premature when your "global state" is really cached server data — that belongs in React Query/SWR/RTK Query, not Redux/Zustand.

**60. 🟡 What's the architectural difference between server state and client state?**
Server state is asynchronous, shared, and owned elsewhere — it needs caching, revalidation, staleness policy, and retry. Client state is synchronous and owned by the UI. Conflating them is the root cause of most over-complicated frontend state code.

**61. 🔴 When would you choose Redux Toolkit over Zustand or Jotai?**
Large teams needing enforced conventions, time-travel debugging, complex derived state with middleware, or an existing Redux codebase. For small-to-mid apps, Zustand/Jotai give 90% of the benefit with a fraction of the ceremony.

**62. 🟡 What are the architectural implications of choosing a mobile strategy: PWA vs React Native vs native?**
PWA: one codebase, instant updates, no app store, but limited device APIs and weak iOS push/background support. React Native: near-native UX with shared logic, but you still hit native modules and platform-specific bugs. Native: best performance and platform integration, highest cost — two codebases and two teams.

**63. 🟡 When does a monorepo make sense for frontend + backend?**
When you want shared types/validation schemas, atomic cross-stack changes, and a single CI pipeline — typical for a TS full-stack team. It stops making sense when teams deploy on independent cadences, the toolchain slows to a crawl, or services are in different languages with no shared code.

**64. 🔴 What is a BFF (Backend for Frontend) and what problem does it solve?**
A thin per-client backend that aggregates and reshapes downstream services into exactly what one client needs. It solves chatty clients, over-fetching, and leaking internal service shapes into the UI. Cost: another deployable and another place business logic can leak into.

**65. 🟡 How should the design system live in a multi-app architecture?**
As a versioned package (internal npm registry or monorepo workspace) with tokens, primitives, and composed components, plus a visual reference (Storybook). Consumers pin versions so a design change can't break three apps simultaneously. Copy-pasting components across apps guarantees drift.

**66. 🟡 What's the trade-off between micro-frontends and a monolithic frontend?**
Micro-frontends allow independent team deploys and mixed frameworks, at the cost of duplicated dependencies, bundle bloat, cross-app state/auth complexity, and inconsistent UX. Only justified above roughly 4–5 independent teams shipping to one surface; below that, a modular monolith frontend is strictly better.

**67. 🟢 Why does bundle size matter architecturally, not just cosmetically?**
JS is the most expensive byte on the web — it must be downloaded, parsed, compiled, and executed, disproportionately hurting low-end mobile devices. Bundle discipline (code splitting, dynamic imports, tree-shakeable libs, avoiding moment/lodash-full) is an architectural constraint, not a polish step.

**68. 🟡 How do you decide between client-side and server-side form validation?**
Both, always. Client-side is UX (fast feedback); server-side is security (the client is untrusted and trivially bypassed). Share one schema (Zod/Pydantic) across both to avoid divergence, and treat the server rule as the source of truth.

**69. 🔴 What frontend decisions are hardest to reverse?**
Framework choice (React↔Vue is a rewrite), routing/URL structure (breaks bookmarks and SEO), design system API, and the state-management paradigm once hundreds of components depend on it. Styling approach and component library are comparatively cheap to swap incrementally.

**70. 🟡 How does the choice of frontend framework constrain your hosting options?**
A static SPA runs anywhere (S3+CloudFront, Netlify, any CDN). SSR requires a Node runtime or edge platform. ISR/streaming requires platform-level support, which is why self-hosting Next.js gives a different feature set than Vercel. Choose the rendering strategy with the hosting reality in mind.

---

# SECTION 4 — Rendering Strategy: SSR / SSG / ISR / CSR / Edge (71–85)

**71. 🟢 Define CSR, SSR, SSG, and ISR in one line each.**
CSR: browser downloads JS and renders everything. SSR: server renders HTML per request. SSG: HTML built once at deploy time. ISR: static pages regenerated in the background on a revalidation interval or on demand.

**72. 🟢 Which rendering strategy fits a marketing landing page and why?**
SSG. The content changes rarely, SEO and first-paint matter enormously, and static files served from a CDN are the cheapest and fastest thing on the internet. Regenerate on content change via a webhook from your CMS.

**73. 🟡 Which fits a logged-in analytics dashboard, and why is SSR often wrong there?**
CSR (or SSR only for the shell). The data is user-specific, uncacheable, and behind auth, so SSR adds server cost and TTFB latency for zero SEO benefit. Render an app shell instantly and stream data client-side with a query cache.

**74. 🟡 When is ISR the right answer and when does it bite you?**
Right for large catalogues with semi-fresh content — e-commerce listings, blogs, docs. It bites when users must see writes immediately (they'll see stale prices/stock), when the page count explodes cache storage, or when your hosting doesn't support on-demand revalidation and you're stuck with time-based staleness.

**75. 🔴 Explain the trade-off between TTFB and Time-to-Interactive across strategies.**
SSG has the best TTFB (file from CDN) and good TTI. SSR has worse TTFB (server work per request) but good first contentful paint and delayed TTI due to hydration. CSR has excellent TTFB (tiny shell) but the worst FCP and TTI because everything waits on JS + API calls.

**76. 🟡 What does "edge rendering" actually change?**
It moves compute geographically close to the user, cutting network round-trip latency. It helps most when your work is light and latency-dominated (auth checks, personalisation, redirects, A/B assignment). It helps little — and can hurt — when the function must call a database in a single region, because you've just added a long round trip from the edge to the origin DB.

**77. 🔴 Why can edge functions make an app slower?**
Because data gravity beats compute proximity. An edge function in Sydney querying a Postgres primary in Virginia pays ~200ms per round trip, and multiple sequential queries multiply it. Unless you also distribute or replicate your data (edge KV, read replicas, Turso/D1), keep compute near the database.

**78. 🟡 What are the constraints of edge runtimes vs Node runtimes?**
Edge runtimes typically lack full Node APIs (fs, native modules, some crypto), have short CPU-time limits, restricted TCP access (often HTTP-only, so no raw Postgres driver without a proxy), and smaller bundle limits. Great for middleware and lightweight rendering; wrong for heavy work.

**79. 🟡 How does the choice of rendering strategy affect SEO?**
Crawlers index HTML reliably; JS-rendered content is indexed inconsistently and with delay. Public content that must rank should be SSG or SSR. Content behind auth is irrelevant to SEO, so use whatever is simplest. Don't pay SSR complexity for pages Google will never see.

**80. 🟡 How do you handle personalised content on a cached page?**
Cache the shared shell and fill personalised slots client-side, or use edge personalisation with cache keys that include a coarse segment (locale, plan tier) rather than user ID. Per-user cache keys destroy hit rate — that's not caching, it's storage.

**81. 🔴 What is streaming SSR and what problem does it solve?**
The server flushes HTML in chunks as data resolves, so users see the shell and fast content immediately while slow sections stream in behind Suspense boundaries. It solves the "one slow query blocks the whole page" problem of traditional SSR, at the cost of more complex error handling and caching semantics.

**82. 🟡 How does the rendering choice interact with your caching layers?**
SSG/ISR pages are CDN-cacheable by URL. SSR responses containing auth-specific data must be marked private and never cached at the CDN. Getting `Cache-Control` and `Vary` wrong here is a classic incident: one user's dashboard served to everyone.

**83. 🟡 Can you mix strategies in one application, and should you?**
Yes, and you should — modern frameworks make it per-route. Marketing pages SSG, product pages ISR, dashboard CSR, auth middleware at the edge. Uniform rendering across every route means at least some routes are paying for something they don't need.

**84. 🔴 What operational cost does SSR add that teams underestimate?**
A stateful-ish server tier to run, scale, monitor, and pay for; higher origin load (every request hits compute); cold starts on serverless; and a new failure mode where a downstream API outage turns into blank pages instead of a degraded client-side error state. Static output has none of these.

**85. 🟡 How do you decide the rendering strategy per route in a checklist form?**
Ask: (1) Is it public and SEO-relevant? (2) How fresh must the content be? (3) Is the content personalised? (4) How expensive is generating it? Public+SEO+stable → SSG. Public+SEO+semi-fresh → ISR. Public+SEO+realtime → SSR. Private+personalised → CSR.

---

# SECTION 5 — Database Selection & Trade-offs (86–115)

**86. 🟢 What is the single most useful default database choice and why?**
PostgreSQL. It handles relational workloads, JSONB for semi-structured data, full-text search, geospatial (PostGIS), arrays, and increasingly vectors (pgvector). Choosing it means one operational skill set covers workloads that would otherwise need three databases.

**87. 🟢 When is MongoDB genuinely the better choice than Postgres?**
When documents are truly self-contained and schema varies per record (CMS content blocks, event payloads, product catalogues with wildly heterogeneous attributes), and when you rarely need multi-entity joins or cross-document transactions. Its horizontal sharding story is also more built-in.

**88. 🟡 Why do teams regret choosing MongoDB?**
Because most business data is relational in practice. As soon as you need joins, referential integrity, or reporting across collections, you're reimplementing a relational engine in application code — with denormalised copies that drift, no foreign keys, and aggregation pipelines that are harder to reason about than SQL.

**89. 🟡 "Schemaless" — what does that actually mean operationally?**
It means the schema moved from the database to your application code, undocumented and unenforced. You still have a schema; you just can't see it, and every reader must defensively handle five historical shapes of the same document. Schema validation in Mongo helps, but at that point you've reintroduced schemas.

**90. 🟡 When should you use JSONB in Postgres instead of reaching for a document DB?**
When most of your data is relational but a few fields are genuinely variable — metadata, feature flags, third-party payloads, user preferences. You keep transactions, joins, and constraints, and get flexibility only where you need it. Note the template's `metadata jsonb` column does exactly this.

**91. 🔴 What are the downsides of overusing JSONB?**
No column-level constraints or defaults, weaker type safety, larger row sizes and TOAST overhead, indexes (GIN) that are bigger and slower to update, and queries that are harder to optimise. If you're querying and filtering a JSONB key constantly, it should be a real column.

**92. 🟢 What is ACID and which parts do people forget matter?**
Atomicity, Consistency, Isolation, Durability. People remember atomicity and forget isolation — which is why they hit lost updates and phantom reads in production under concurrency, and why "it worked in testing with one user" is a meaningless signal.

**93. 🟡 Explain CAP theorem in terms an engineer can apply.**
When a network partition happens, you must choose between consistency (reject requests you can't confirm) and availability (serve possibly stale data). It only applies during partitions — the rest of the time you're trading consistency for latency, which is the PACELC refinement and more relevant day to day.

**94. 🟡 What is eventual consistency and where is it acceptable?**
Replicas converge to the same state after a delay. Acceptable for feeds, view counts, search indexes, analytics, and recommendations. Unacceptable for account balances, inventory decrements, seat booking, and anything where a user makes an irreversible decision on the value they just read.

**95. 🔴 Describe the read-your-own-writes problem with read replicas.**
A user writes to the primary, then their next read hits a lagging replica and shows old data — "I saved it and it disappeared." Fixes: route reads to the primary for a short window after a write (sticky sessions or a per-user write timestamp), or use a consistency token / `wait_for_lsn` style mechanism.

**96. 🟡 When do you actually need read replicas?**
When read traffic saturates the primary's CPU or I/O and you've already fixed queries and indexes. They're also useful to isolate analytics and reporting from transactional load. They do not help write throughput at all — that's a sharding or vertical-scaling problem.

**97. 🟡 Vertical vs horizontal database scaling — which do you try first and why?**
Vertical, almost always. Bigger instances are a config change; sharding is an architectural commitment that breaks joins, transactions, and unique constraints across shards. Modern hardware takes single-node Postgres remarkably far — most apps never need to shard.

**98. 🔴 What breaks when you shard a relational database?**
Cross-shard joins and transactions, globally unique auto-increment IDs, foreign keys across shards, aggregate queries, and rebalancing when shards grow unevenly. You also need a routing layer and a re-shard strategy. Pick a shard key that keeps related data co-located — usually tenant/org ID.

**99. 🟡 What is a "hot partition"/"hot shard" and how do you avoid it?**
One shard receiving disproportionate traffic because the shard key is skewed (e.g. sharding by date puts all today's writes on one node, or one enterprise tenant is 40% of volume). Avoid with high-cardinality, evenly distributed keys; handle whale tenants with dedicated shards.

**100. 🟡 When is SQLite a legitimate production choice?**
Single-node applications, edge deployments, embedded/desktop apps, CI test databases, and read-heavy workloads with modest write volume (Litestream/LiteFS for replication, Turso for distributed). It fails when you need many concurrent writers or multiple app servers sharing one database.

**101. 🟡 What is Redis good at, and what is it bad at as a primary store?**
Excellent at caching, rate limiting, session storage, leaderboards, pub/sub, and queues — everything low-latency and ephemeral. Bad as a primary store: memory-bound, expensive per GB, persistence is a trade-off not a guarantee, and querying is limited to key patterns and data-structure operations.

**102. 🔴 Compare Redis and Memcached as a cache.**
Memcached is simpler, purely a string KV cache, multithreaded, slightly better for raw large-volume simple caching. Redis has rich data structures, persistence options, pub/sub, Lua scripting, and cluster mode. Redis is the default because the extra capabilities usually get used within a year.

**103. 🟡 When do you need a dedicated search engine (Elasticsearch/OpenSearch/Typesense/Meilisearch)?**
When you need relevance ranking, typo tolerance, faceting, synonyms, and sub-100ms search across millions of documents. Below that, Postgres full-text search (`tsvector` + GIN) is usually sufficient and saves you an entire distributed system to operate.

**104. 🔴 What's the architectural cost of adding Elasticsearch?**
A second source of truth that must be kept in sync (dual writes, CDC, or a reindex pipeline), eventual consistency between DB and index, cluster operations (shards, heap tuning, snapshots), and a new failure mode where search breaks while the app is fine. Only worth it when search is a core product feature.

**105. 🟡 When do you need a time-series database (TimescaleDB, InfluxDB, ClickHouse)?**
When you ingest high-volume append-only measurements and query aggregates over time windows — metrics, IoT telemetry, event analytics. Row-store OLTP databases degrade on these; column stores compress 10–100× better and scan orders of magnitude faster.

**106. 🟡 What's the difference between OLTP and OLAP, and why not use one database for both?**
OLTP: many small reads/writes of individual rows, latency-sensitive (your app). OLAP: few queries scanning millions of rows, throughput-sensitive (analytics). Running analytics on your OLTP primary causes lock contention, cache eviction, and latency spikes for real users. Separate them with a replica or a warehouse.

**107. 🟡 When would you introduce a data warehouse (BigQuery/Snowflake/Redshift/DuckDB)?**
When business users need cross-source reporting, when analytical queries hurt production, or when you must retain history the app doesn't need. Before that, a read replica plus a BI tool is far cheaper and simpler.

**108. 🔴 Vector database vs pgvector — how do you decide?**
Use pgvector when your vectors live alongside relational data, volume is under roughly a few million embeddings, and you value one database. Move to a dedicated vector DB (Pinecone, Qdrant, Weaviate, Milvus) when you need very large indexes, advanced filtering at scale, or sub-50ms ANN on hundreds of millions of vectors.

**109. 🟡 Managed database vs self-hosted — how do you decide?**
Managed unless you have a dedicated infra team, a hard compliance requirement, or such extreme scale that the markup exceeds a salary. The managed premium buys backups, failover, patching, and 3am pager coverage — usually far cheaper than the engineer-hours it replaces.

**110. 🟡 What database questions should the TRD answer before Phase 1 of the build?**
Expected read/write ratio, expected row counts at 12 months, latency targets, consistency requirements per entity, multi-tenancy model, backup/RPO/RTO targets, and whether analytics will share the same instance. Without these, "PostgreSQL via Supabase" is a guess, not a decision.

**111. 🟡 What are the three common multi-tenancy models and their trade-offs?**
Shared schema with `tenant_id` (cheapest, simplest, relies on RLS/app discipline for isolation), schema-per-tenant (better isolation, migration pain at hundreds of tenants), database-per-tenant (strongest isolation and per-tenant restore, highest cost and operational overhead). Start shared, escalate for enterprise customers.

**112. 🔴 Why is Row-Level Security both powerful and dangerous?**
Powerful because isolation is enforced by the database, so an application bug can't leak another tenant's rows. Dangerous because policies are easy to get subtly wrong, service-role keys bypass them entirely, and unindexed policy predicates silently wreck query performance at scale.

**113. 🟡 Why does the template index every `user_id` column, and what's the general rule?**
Because every RLS policy and nearly every query filters on it, and an unindexed foreign key filter means a sequential scan per request. General rule: index the columns you filter, join, and sort on — and remember each index costs write throughput and storage, so don't index everything.

**114. 🔴 When is denormalisation the right call?**
When a read path is hot, the join is expensive, and the denormalised value changes rarely — cached counters, materialised aggregates, embedded display names. Accept the cost: you now own consistency. Prefer materialised views or triggers over hand-maintained duplication scattered across application code.

**115. 🔴 How do you design a database so a future migration is possible?**
Keep business logic out of vendor-specific features where feasible, use standard SQL, own your migration files (Prisma/Drizzle/Alembic) rather than clicking in a dashboard, avoid proprietary types, and keep a tested export path. The goal isn't portability for its own sake — it's that the exit is a project, not a rewrite.

---

# SECTION 6 — ORM & Data Access Layer (116–127)

**116. 🟢 What does an ORM buy you and what does it cost?**
It buys type safety, migrations, relationship traversal, and protection from string-concatenated SQL. It costs an abstraction that hides query cost, generates suboptimal SQL for complex cases, and lets developers write catastrophic queries without noticing.

**117. 🟡 Prisma vs Drizzle vs raw SQL — when does each win?**
Prisma: best DX, strong migrations, great for standard CRUD; heavier runtime and less control over generated SQL. Drizzle: SQL-shaped and type-safe, thin runtime, better for people who think in SQL and need edge compatibility. Raw SQL (+ a query builder): maximum control for analytics-heavy or highly tuned workloads.

**118. 🟡 What is the N+1 query problem and why do ORMs cause it?**
Fetching a list (1 query) then lazily loading a relation per item (N queries). ORMs cause it because relation access looks like a property read. Detect it in query logs or APM traces; fix with eager loading (`include`/`join`) or DataLoader-style batching.

**119. 🟡 Why should the ORM choice not dictate the database choice?**
Because the database is the irreversible decision and the ORM is the reversible one. Picking Mongo because you liked Mongoose, or Postgres because Prisma supports it best, inverts the priority. Choose the data model first, then the best access layer for it.

**120. 🔴 When should you drop out of the ORM into raw SQL?**
Complex reporting aggregates, window functions, CTEs, bulk upserts, recursive queries, and anything where you need a specific query plan. A good architecture makes this easy — the ORM should be a convenience, not a prison.

**121. 🟡 What is a repository pattern and is it worth it in a small app?**
A layer that hides data access behind domain-shaped methods (`findActiveProjectsForUser`). Worth it when you have complex domain logic, multiple data sources, or want testability without a database. Overkill for a CRUD MVP, where it's often just a pass-through adding a file per entity.

**122. 🟡 What is connection pooling and why does serverless break it?**
A pool reuses a small set of DB connections across requests. Serverless functions scale to hundreds of short-lived instances, each opening its own connections, exhausting the database's limit. Fix with an external pooler (PgBouncer in transaction mode, RDS Proxy, Supabase pooler) or an HTTP-based driver.

**123. 🔴 What's the difference between session, transaction, and statement pooling in PgBouncer?**
Session mode: one client holds a server connection for its whole session (safest, least efficient). Transaction mode: connection is returned after each transaction (best for serverless; breaks prepared statements, `SET`, and advisory locks held across statements). Statement mode: per statement (most aggressive, no multi-statement transactions).

**124. 🟡 Where should database migrations live in the deploy pipeline?**
In version control, applied as a discrete step before or alongside the deploy, forward-only, and designed to be backward-compatible with the currently running code (expand/contract). Never as an ad-hoc dashboard change — undocumented drift between environments is a top source of production incidents.

**125. 🔴 Explain the expand/contract migration pattern.**
Phase 1 (expand): add the new column/table, write to both old and new. Phase 2: backfill and switch reads to new. Phase 3 (contract): stop writing the old, then drop it in a later deploy. This lets schema and code deploy independently with zero downtime and a safe rollback at every step.

**126. 🟡 Why are `updated_at` columns and soft deletes architectural decisions, not conveniences?**
`updated_at` enables incremental sync, cache invalidation, and CDC pipelines. Soft deletes preserve referential integrity and enable undo/audit, but every query must then filter deleted rows — forget once and you leak deleted data. Decide the policy globally, not per table.

**127. 🔴 How do you handle transactions across two data stores (e.g. Postgres + Stripe)?**
You can't have a distributed ACID transaction with a third-party API. Use the outbox pattern or idempotent, retryable steps with a state machine: record intent locally, call the external service with an idempotency key, reconcile via webhook, and make every step safe to replay.

---

# SECTION 7 — Architecture Patterns: Monolith / Microservices / Serverless (128–152)

**128. 🟢 Define monolith, modular monolith, and microservices.**
Monolith: one deployable, often with tangled internal boundaries. Modular monolith: one deployable with strictly enforced internal module boundaries and no cross-module database access. Microservices: independently deployable services, each owning its own data, communicating over the network.

**129. 🟢 Why is a modular monolith the correct default for most new products?**
You get clean boundaries without network calls, distributed transactions, service discovery, or a dozen pipelines. Refactoring across modules is a compiler-checked rename rather than a coordinated multi-service release. If a module later needs independent scaling, its boundary is already the extraction seam.

**130. 🟡 What actually justifies microservices?**
Independent team autonomy at organisational scale, genuinely divergent scaling profiles (a video transcoder vs a settings API), different runtime requirements, and fault isolation for a critical subsystem. Note that none of these is "the codebase feels big."

**131. 🟡 What do you pay for microservices that nobody puts in the proposal?**
Distributed tracing, service discovery, retries and idempotency, network partial failure, eventual consistency, schema/contract versioning, per-service CI/CD and IaC, duplicated auth, a much harder local dev story, and on-call complexity. The code gets simpler; the system gets much harder.

**132. 🔴 What is a distributed monolith and how do you recognise one?**
Services that must be deployed together, share a database, or break when any one is down. Signs: a release train coordinating five repos, synchronous call chains three deep in every request, shared DB tables across services. It's the worst of both worlds — microservice cost with monolith coupling.

**133. 🔴 How do you choose service boundaries correctly?**
Along business capabilities and data ownership (DDD bounded contexts), not technical layers. A good boundary owns its data exclusively, can be deployed alone, and most changes touch only one service. If a typical feature requires changing three services, the boundaries are wrong.

**134. 🟡 What is the strangler-fig pattern?**
Put a facade/router in front of the legacy system, implement new functionality in the new system, and incrementally redirect routes until the legacy system has no traffic and can be deleted. It de-risks migration by keeping the system shippable throughout, unlike a big-bang rewrite.

**135. 🟢 When is serverless the right architecture?**
Spiky or low traffic, event-driven work, glue between managed services, and teams without ops capacity. You pay per invocation, scale to zero, and don't patch servers. Ideal for webhooks, scheduled jobs, and image processing triggered by uploads.

**136. 🟡 When is serverless the wrong choice?**
Sustained high traffic (containers become far cheaper per request), long-running jobs beyond execution limits, workloads needing persistent connections (WebSockets, DB pools), latency-critical paths sensitive to cold starts, and anything needing large in-memory state or GPUs.

**137. 🔴 Explain the cost crossover between serverless and containers.**
Serverless bills per invocation-millisecond with no idle cost; containers bill for provisioned capacity regardless of traffic. Below roughly continuous-single-instance utilisation, serverless is cheaper. Above it — a service busy most of the day — reserved containers typically cost several times less for the same throughput.

**138. 🟡 What is a cold start and what are the mitigations?**
Latency incurred when a new execution environment must be initialised (runtime boot + your imports + connection setup). Mitigate with smaller bundles, lazy imports, lighter runtimes, provisioned/warm concurrency, or by not using serverless on latency-critical paths.

**139. 🟡 What is BaaS (Supabase/Firebase) and what's the architectural trade?**
The backend is provided as configured managed services (DB, auth, storage, realtime) that clients hit more or less directly. You trade custom server logic and portability for enormous speed. The trap is business logic drifting into client code and RLS policies, where it's hard to test and audit.

**140. 🔴 Firebase vs Supabase — the honest comparison.**
Firebase: mature realtime, excellent mobile SDKs, strong push/analytics/crash tooling, but a NoSQL document model that punishes relational queries and a pricing model that surprises on read volume. Supabase: real Postgres with SQL, RLS, and portability, but a younger realtime story and fewer mobile-native conveniences.

**141. 🟡 What is event-driven architecture and when does it fit?**
Services publish events and interested parties react asynchronously. It fits when you need loose coupling, fan-out to many consumers, and resilience to downstream outages — order placed → email, invoice, analytics, inventory. It's a poor fit when the caller needs an immediate answer.

**142. 🔴 Choreography vs orchestration in distributed workflows?**
Choreography: services react to events with no central coordinator — loosely coupled but the end-to-end flow exists nowhere and is hard to debug. Orchestration: a coordinator (Temporal, Step Functions, a saga orchestrator) drives the steps — explicit, observable, testable, at the cost of a central dependency. Use orchestration for business-critical multi-step flows.

**143. 🔴 What is the saga pattern and what does it give up?**
A long-running transaction split into local transactions with compensating actions for rollback. It gives up atomicity and isolation: intermediate states are visible, and compensation is semantic (refund) rather than a true undo. Necessary when a business process spans services.

**144. 🟡 What is CQRS and when is it justified?**
Separating the write model from one or more read models optimised for querying. Justified when read and write shapes/loads diverge sharply — a complex domain writing normalised data but serving denormalised dashboards. Unjustified in a CRUD app, where it doubles the model count for no benefit.

**145. 🔴 When is event sourcing worth it?**
When the audit trail *is* the product requirement (finance, healthcare, compliance), when you need temporal queries ("what did this look like on March 3rd?"), or when multiple read models derive from the same facts. It costs schema evolution pain, replay infrastructure, and a much steeper onboarding curve.

**146. 🟡 What is the sidecar pattern and where do you see it?**
A helper container deployed alongside the app container handling cross-cutting concerns — mTLS, retries, telemetry, config. Service meshes (Istio, Linkerd) work this way. It centralises policy but adds latency, memory overhead, and a substantial operational learning curve.

**147. 🔴 When is a service mesh over-engineering?**
Below roughly 15–20 services, or when you don't have a platform team. Everything a mesh gives you (retries, timeouts, mTLS, tracing) can be done with a shared client library and a load balancer at small scale, without a control plane to operate and debug.

**148. 🟡 What is the API gateway pattern and what belongs in it?**
A single entry point handling routing, authn, rate limiting, request/response transformation, and observability. What belongs: cross-cutting concerns. What doesn't: business logic — a gateway that knows about orders and discounts becomes a shared bottleneck every team must coordinate on.

**149. 🟡 Monorepo vs polyrepo for a service architecture?**
Monorepo: atomic cross-service changes, shared tooling and types, easier refactors; needs build tooling (Nx, Turborepo, Bazel) to stay fast. Polyrepo: clean ownership and independent versioning; makes cross-cutting changes slow and encourages duplication. Team topology decides more than technology.

**150. 🔴 How do you decide whether to extract a service from a monolith?**
Extract when the module has a stable, narrow interface; distinct scaling or reliability requirements; a dedicated owning team; and its own data with few cross-boundary transactions. If any of those is false, extraction converts an easy in-process call into a hard network call for nothing.

**151. 🟡 What does "design for failure" mean concretely in a distributed system?**
Every network call gets a timeout, bounded retries with exponential backoff and jitter, idempotency keys, a circuit breaker, and a defined degraded behaviour. Assume every dependency is down right now — what does the user see? If the answer is "a 500," the design isn't finished.

**152. 🔴 The template says "Monolithic / Microservices / Serverless / JAMstack." How would you improve that choice list?**
By separating orthogonal axes: deployment topology (monolith vs services), compute model (servers vs containers vs functions), and rendering/delivery (JAMstack is a frontend delivery pattern, not a backend topology). A serverless modular monolith serving a JAMstack frontend is one coherent answer, and the current list implies you must pick one.

---

# SECTION 8 — API Design: REST / GraphQL / gRPC / tRPC / WebSockets (153–172)

**153. 🟢 When is REST the right default?**
Public APIs, third-party integrations, simple CRUD resources, and anywhere HTTP caching, standard tooling, and universal client support matter. It's the lowest-common-denominator choice, and that's usually a feature.

**154. 🟡 When does REST become painful?**
When clients need varied data shapes and you're forced into over-fetching, under-fetching, or endpoint proliferation (`/users/:id/full`, `/users/:id/summary`, `/users/:id/with-orders`). Also when mobile and web clients need different payloads and you end up versioning per client.

**155. 🟡 When is GraphQL the right choice?**
Many heterogeneous clients with different data needs, deeply nested relational data, and a platform team that can operate it. It removes client-driven endpoint churn and gives a strongly typed schema as the contract.

**156. 🔴 What are GraphQL's real costs?**
HTTP caching is largely lost (POST to one endpoint), N+1 resolution needs DataLoader everywhere, malicious deep queries need depth/complexity limiting, per-field authorisation is subtle, rate limiting by request count is meaningless, and observability tooling needs GraphQL awareness. It's a platform commitment, not a library.

**157. 🟡 When is tRPC a great fit and when is it inappropriate?**
Great for a TypeScript monorepo with a single first-party client — end-to-end type safety with no codegen or schema. Inappropriate for public APIs, non-TS consumers, or mobile teams in Swift/Kotlin, because the contract only exists in the TypeScript type system.

**158. 🟡 When would you use gRPC?**
Internal service-to-service communication where you want strict contracts (protobuf), low serialisation overhead, bidirectional streaming, and code generation across languages. Poor for browser clients (needs grpc-web + a proxy) and harder to debug by hand than JSON over HTTP.

**159. 🔴 Compare protobuf/gRPC and JSON/REST on the axes that matter operationally.**
Payload size and parse cost: protobuf wins substantially. Human debuggability and tooling ubiquity: JSON wins. Schema evolution: protobuf has explicit rules (field numbers, reserved tags) versus REST's informal conventions. Browser reach: REST wins outright. Use gRPC inside the mesh, REST at the edge.

**160. 🟡 Polling vs long-polling vs SSE vs WebSockets — how do you choose?**
Polling: simplest, fine for low-frequency updates (minutes). Long-polling: legacy fallback. SSE: server→client only, over plain HTTP, auto-reconnect, great for notifications and streaming AI tokens. WebSockets: bidirectional, needed for chat, collaborative editing, and multiplayer.

**161. 🟡 Why are WebSockets architecturally expensive?**
They're stateful long-lived connections, which conflicts with stateless scaling: you need sticky routing or a shared pub/sub backplane (Redis) so any node can reach any client, connection-count limits per instance, careful reconnect/backfill logic, and load balancers configured for long idle timeouts.

**162. 🔴 How do you scale WebSockets across many instances?**
Terminate sockets on a dedicated tier, keep a presence/registry (Redis) of which node holds which connection, and fan out messages through pub/sub rather than direct node-to-node calls. Alternatively, offload to a managed realtime provider so your app tier stays stateless.

**163. 🟢 What makes an API idempotent and why does it matter?**
The same request applied multiple times produces the same result. It matters because networks retry: without idempotency keys, a timeout on a payment can charge a customer twice. GET/PUT/DELETE are naturally idempotent; POST needs an explicit client-supplied key.

**164. 🟡 How should you version an API and when?**
Version when you make a breaking change; prefer additive, non-breaking evolution otherwise. URL versioning (`/v1/`) is the most operationally obvious; header versioning is cleaner but harder to debug and cache. Whichever you pick, publish a deprecation policy — versions you never retire are versions you maintain forever.

**165. 🟡 What is the difference between pagination strategies, and when does offset break?**
Offset/limit is simple but degrades on deep pages (the DB still scans skipped rows) and produces duplicates/skips when data changes mid-pagination. Cursor/keyset pagination is stable and O(1) per page but can't jump to page 50. Use cursors for feeds and large datasets.

**166. 🔴 How do you design an API for a mobile client on a flaky network?**
Minimise round trips (batch/aggregate endpoints or a BFF), make writes idempotent, support conditional requests (ETag/If-None-Match) and delta sync via `updated_at`, keep payloads small, and design for offline queueing with server-side conflict resolution rules.

**167. 🟡 Where should rate limiting live and what algorithm should it use?**
At the edge (CDN/gateway) for crude protection, plus application-level limits keyed by user/API key for fairness. Token bucket allows bursts while capping sustained rate; sliding window log is accurate but memory-heavy; fixed window is cheap but allows 2× bursts at boundaries.

**168. 🟡 What's the right way to handle errors in an API contract?**
Correct HTTP status codes plus a stable machine-readable error body (`code`, `message`, `details`, `request_id`). Clients branch on `code`, not on prose. Never leak stack traces or SQL. `request_id` is what makes a user's support ticket debuggable.

**169. 🔴 What is an anti-corruption layer and when do you need one?**
An adapter that translates a third party's (or legacy system's) model into your domain model. You need it whenever an external contract you don't control would otherwise leak into your core — Stripe's object shapes, a legacy ERP, a partner's CSV. It localises the blast radius when they change.

**170. 🟡 Webhooks in vs webhooks out — what does each require architecturally?**
Receiving: signature verification, idempotency (they will redeliver), fast ACK then async processing, and replay tolerance. Sending: retry with backoff, dead-lettering, per-subscriber isolation so one slow endpoint doesn't block others, and signed payloads with a rotation story.

**171. 🔴 Why should a webhook handler return 200 before doing the work?**
Because providers time out quickly and retry aggressively; slow handlers cause duplicate deliveries and eventual endpoint disabling. Validate the signature, persist the raw event, return 200, then process from a queue. The template's Stripe webhook step should follow this.

**172. 🟡 What API decisions are hardest to change later?**
Resource naming and URL structure, identifier format (sequential vs UUID vs ULID — exposing sequential IDs also leaks volume), authentication scheme, pagination contract, and error code vocabulary. Anything an external consumer has hard-coded is effectively permanent.

---

# SECTION 9 — Load Balancing (173–205)

**173. 🟢 What is a load balancer and what problems does it solve besides distributing traffic?**
It's a reverse proxy distributing requests across backend instances. Beyond distribution it provides: a stable single entry point, health-based failover, zero-downtime deploys, TLS termination, and a natural place for rate limiting, WAF rules, and request logging.

**174. 🟢 What is the difference between Layer 4 and Layer 7 load balancing?**
L4 balances at TCP/UDP level using IP and port — fast, protocol-agnostic, no visibility into the request. L7 parses HTTP, so it can route by path, host, header, or cookie, terminate TLS, rewrite requests, and retry safely. L4 is faster and cheaper; L7 is smarter.

**175. 🟡 When would you deliberately choose L4 over L7?**
Non-HTTP protocols (raw TCP, database proxies, game servers, MQTT), extremely high throughput where per-request parsing cost matters, when you need to preserve end-to-end TLS to the backend, or when you want the lowest possible latency and don't need content-based routing.

**176. 🟢 Explain round robin, least connections, and weighted distribution.**
Round robin: each backend in turn — fine when requests are uniform and servers identical. Least connections: send to the backend with fewest active connections — better when request durations vary widely. Weighted: proportional to declared capacity — necessary with heterogeneous instance sizes or during canary rollouts.

**177. 🟡 When does round robin perform badly?**
When request cost is highly variable. A backend that receives three 10-second report queries in a row keeps getting new traffic while an idle peer sits free, producing tail-latency spikes. Least-connections or least-response-time handles this far better.

**178. 🟡 What is the "power of two choices" algorithm and why is it popular?**
Pick two backends at random and send the request to whichever has fewer active connections. It gets almost all the benefit of global least-connections without requiring global state, which makes it ideal for distributed load balancers and service meshes.

**179. 🟡 What is IP hash / consistent hashing used for in load balancing?**
Routing the same key (client IP, session ID, cache key) to the same backend consistently — useful for cache locality and stateful services. Consistent hashing specifically minimises how many keys remap when a node is added or removed, unlike modulo hashing which reshuffles nearly everything.

**180. 🔴 Why is plain modulo hashing (`hash % N`) a bad sharding/balancing strategy?**
Because changing N remaps roughly all keys, causing a mass cache miss storm or state relocation. Consistent hashing with virtual nodes remaps only ~1/N of keys when a node joins or leaves, keeping disruption proportional to the change.

**181. 🟡 What is sticky session (session affinity) and why is it usually a smell?**
The LB pins a client to one backend, typically via a cookie. It's a smell because it makes your fleet stateful: you lose even distribution, deploys drop sessions, autoscaling can't drain cleanly, and one hot user pins load to one node. Prefer externalising session state to Redis or using stateless JWTs.

**182. 🟡 When is session affinity legitimately necessary?**
Long-lived WebSocket connections, in-progress file uploads, server-side streaming, legacy apps with in-memory sessions you can't refactor, and local caches expensive to warm. Even then, treat it as a constraint to remove rather than a design principle.

**183. 🟢 What is a health check and what's the difference between liveness and readiness?**
Liveness: "is this process alive?" — failing it should restart the instance. Readiness: "can this instance serve traffic right now?" — failing it should remove it from the LB pool without restarting. Conflating them causes restart loops during transient dependency outages.

**184. 🔴 Why can a health check that pings the database cause a total outage?**
If the database blips, every instance fails readiness simultaneously, the LB removes the entire pool, and you go from degraded to 100% down. Health checks should verify the instance itself; report dependency health separately and let the app degrade gracefully instead of removing itself.

**185. 🟡 What is a shallow vs deep health check, and how do you use both?**
Shallow: process responds, returns 200 — used by the LB at high frequency. Deep: checks DB, cache, and downstream connectivity — used by monitoring and dashboards at low frequency. Never wire a deep check to the LB's removal decision.

**186. 🟡 What is connection draining / graceful shutdown and why does it matter for deploys?**
On shutdown the instance stops accepting new connections, finishes in-flight requests, then exits. Without it, every deploy or scale-in kills active requests and returns 502s. Requires the app to handle SIGTERM, fail readiness first, then close after a drain window shorter than the LB's timeout.

**187. 🔴 Walk through the correct shutdown sequence for a pod behind a load balancer.**
Receive SIGTERM → immediately fail readiness → keep serving in-flight requests → wait for the LB to notice and stop sending traffic (a few health-check intervals) → close keep-alive connections → drain background work → exit. Shutting down instantly on SIGTERM is the most common cause of deploy-time 502s.

**188. 🟡 What is an active-passive vs active-active load balancer setup?**
Active-passive: one LB serves traffic, a standby takes over on failure (via a floating IP/VRRP) — simpler, wastes capacity, has failover delay. Active-active: all LBs serve traffic simultaneously (DNS or anycast in front) — better utilisation and no failover gap, but harder state sharing and health coordination.

**189. 🟡 How is DNS-based load balancing different from a real load balancer?**
DNS returns different IPs to different clients — it's coarse, cached by resolvers (TTL means slow failover), and blind to backend health unless paired with health-checked DNS. It's for geographic/multi-region distribution, not fine-grained balancing. Use it *above* real load balancers, not instead of them.

**190. 🔴 What is anycast and how does it help global load balancing?**
The same IP is announced from multiple locations, and BGP routes each user to the topologically nearest one. It gives instant, DNS-cache-independent geographic routing and absorbs DDoS by spreading it across POPs. It's how CDNs and modern global LBs work.

**191. 🟡 What is GSLB (global server load balancing) and when do you need it?**
Distributing traffic across regions based on geography, latency, capacity, or health. You need it for multi-region active-active deployments, disaster recovery failover, and data-residency routing. It introduces the hard question of what your data layer does across regions.

**192. 🔴 Why is multi-region active-active hard even with perfect load balancing?**
Because the database is the problem, not the traffic. You must choose between a single-writer region (cross-region write latency), multi-writer with conflict resolution (CRDTs, last-write-wins data loss), or partitioned data by region (no global queries). The load balancer is the easy 10% of the work.

**193. 🟡 Where does TLS termination belong and what are the options?**
Terminate at the LB/CDN (simplest, enables L7 features and offloads crypto), re-encrypt to the backend (TLS to LB, new TLS to app — required for compliance/zero-trust), or passthrough (LB is L4, backend terminates — maximum privacy, no L7 features). Choose based on whether the internal network is trusted.

**194. 🟡 What headers must an L7 load balancer set, and what's the security risk?**
`X-Forwarded-For`, `X-Forwarded-Proto`, `X-Forwarded-Host` (or the standard `Forwarded`). The risk: if the app trusts these blindly and the LB doesn't overwrite client-supplied values, an attacker spoofs their IP — defeating rate limiting, geo rules, and audit logs. Always strip-and-set at the trusted edge and configure trusted proxy hops in the app.

**195. 🔴 Explain the thundering herd problem in a load-balanced system.**
When a backend recovers or a cache expires, all clients or all LBs rush it simultaneously, knocking it over again. Mitigations: jittered retries and exponential backoff, staggered health-check intervals, request coalescing/single-flight on cache misses, and slow-start ramping for newly healthy backends.

**196. 🟡 What is LB slow start and why does it matter?**
A newly added backend receives gradually increasing traffic instead of an immediate full share. It matters because fresh instances have cold JIT, empty caches, and unwarmed connection pools — full traffic immediately means high latency or a crash, which flaps it out of the pool.

**197. 🔴 What is a circuit breaker and how does it complement load balancing?**
A client-side (or proxy-side) mechanism that stops sending requests to a failing dependency after an error threshold, fails fast, then probes with limited traffic (half-open) before restoring. The LB removes dead backends; the circuit breaker protects you from *slow* ones, which are far more dangerous than dead ones.

**198. 🔴 Why are slow backends worse than dead backends?**
A dead backend fails health checks and is removed in seconds. A slow one passes health checks while holding every request thread/connection, so latency propagates upward and exhausts pools throughout the call chain — the classic cascading failure. Defend with aggressive timeouts, bulkheads, and outlier detection.

**199. 🟡 What is outlier detection / passive health checking?**
The LB observes real request outcomes (5xx rate, latency percentile) and ejects a misbehaving backend even though it passes active health checks, reinstating it after a cool-off. It catches partial failures — one bad node with a corrupt cache or a failing disk — that synthetic checks miss.

**200. 🟡 How do you decide the LB timeout values?**
Set the LB timeout slightly above your p99 backend latency, and make sure it's *shorter* than the client timeout and *longer* than the backend's own timeout. Mismatched timeouts across layers create orphaned work: the client gave up but the backend is still burning a DB connection.

**201. 🔴 What is a bulkhead and how does it apply at the load balancing layer?**
Isolating resources so failure in one area can't consume everything — separate connection pools, thread pools, or entire LB target groups per workload. Practically: don't put your latency-critical checkout API and your slow report-export API behind the same pool with shared limits.

**202. 🟡 How does autoscaling interact with load balancing, and what goes wrong?**
The LB must discover new instances and drain removed ones. Failure modes: scaling on CPU when the bottleneck is the database (you scale into a connection storm), scale-up slower than traffic growth (scaling lag), flapping from an over-tight threshold, and scale-in killing in-flight requests without draining.

**203. 🔴 What metrics should drive autoscaling behind a load balancer?**
Prefer request-rate-per-instance or queue depth / concurrent requests over raw CPU, since I/O-bound services saturate long before CPU does. Combine with a latency SLO guard, and always set a max so a traffic spike (or a retry storm) can't scale you into a database outage or a five-figure bill.

**204. 🟡 What is the difference between a load balancer, a reverse proxy, an API gateway, and a CDN?**
All are intermediaries. Reverse proxy: forwards requests, may cache/rewrite. Load balancer: a reverse proxy specialised in distribution and health. API gateway: adds auth, rate limiting, routing by API contract, and developer-facing concerns. CDN: a globally distributed caching reverse proxy at the edge. Real stacks layer them: CDN → LB → gateway → services.

**205. 🔴 In a Vercel/Supabase-style managed stack, where is the load balancer and what do you still own?**
The platform owns edge routing, TLS, and scaling of your functions; the database provider owns its pooler and failover. You still own timeout budgets, retry/idempotency semantics, connection pool sizing, graceful handling of cold starts, and rate limiting your own endpoints. Managed means invisible, not absent — and the failure modes are still yours to design around.

---

# SECTION 10 — Caching & CDN (206–220)

**206. 🟢 Name the layers of caching in a typical web architecture.**
Browser cache → CDN/edge cache → reverse proxy cache → application in-memory cache → distributed cache (Redis) → database buffer pool/query cache → materialised views. Each layer has different invalidation semantics, and confusion between them is where stale-data bugs live.

**207. 🟢 What's the difference between cache-aside, read-through, and write-through?**
Cache-aside: app checks cache, on miss reads DB and populates (most common, app controls policy). Read-through: cache library fetches on miss transparently. Write-through: writes go to cache and DB synchronously (consistent, slower writes). Write-behind: writes to cache, async to DB (fast, risks data loss).

**208. 🟡 Why is cache invalidation genuinely hard?**
Because correctness requires knowing every cached derivation of a piece of data and every place it lives, across layers you don't control (browsers, ISP proxies, CDN POPs). TTL-based expiry is a probabilistic compromise; event-based invalidation is precise but easy to leave incomplete.

**209. 🟡 TTL-based vs event-based invalidation — when do you use each?**
TTL when staleness is tolerable and the data changes unpredictably (product listings, feeds). Event-based (purge on write) when correctness matters and writes are relatively rare (pricing, permissions, published content). Common production answer: both — short TTL as a safety net, purge on write for immediacy.

**210. 🔴 What is a cache stampede and how do you prevent it?**
Many concurrent requests miss the same expired key and all hit the origin at once. Prevent with single-flight/request coalescing (one request recomputes, others wait), probabilistic early expiration, or stale-while-revalidate serving the old value while one worker refreshes.

**211. 🟡 Explain `stale-while-revalidate` and `stale-if-error`.**
`stale-while-revalidate` serves the expired cached response immediately while asynchronously fetching a fresh one — users never wait for a revalidation. `stale-if-error` serves stale content when the origin returns an error, turning a potential outage into slightly outdated content. Both are cheap resilience wins.

**212. 🟡 What is the difference between `Cache-Control: private`, `public`, and `no-store`?**
`public`: any cache including CDNs may store it. `private`: only the end-user's browser may store it (use for personalised responses). `no-store`: nobody stores it (auth tokens, sensitive data). Mislabelling a personalised response as `public` is how one user's data gets served to another.

**213. 🔴 What role does the `Vary` header play and how does it hurt hit rates?**
`Vary` tells caches which request headers make responses differ (e.g. `Accept-Encoding`, `Accept-Language`). Each distinct value combination is a separate cache entry, so `Vary: User-Agent` effectively disables caching by fragmenting into thousands of variants. Vary on the fewest, lowest-cardinality headers possible.

**214. 🟡 What should you cache at the CDN and what must you never cache there?**
Cache: static assets with content-hashed filenames (immutable, long max-age), public pages, public API GETs. Never: authenticated responses, `Set-Cookie` responses, anything keyed to a user without a per-user cache key. Content-hashing assets is what lets you use `max-age=31536000, immutable` safely.

**215. 🟡 What is a cache key and why is designing it the whole game?**
The key determines what counts as "the same request." Too coarse and you serve wrong content across users/locales; too fine (user ID, timestamp, tracking query params) and hit rate collapses to zero. Normalise: strip marketing query params, lowercase paths, and include only meaningful dimensions.

**216. 🔴 Where does a cache belong: in-process or distributed?**
In-process (LRU in memory) is fastest and free but inconsistent across instances and lost on deploy — fine for immutable reference data and short TTLs. Distributed (Redis) is consistent across the fleet and survives deploys, at the cost of a network hop and an operational dependency. Many systems use both, in tiers.

**217. 🟡 What cache eviction policies exist and when does each fit?**
LRU (default, good for general locality), LFU (better when a stable set of hot keys dominates), FIFO (simple, rarely optimal), TTL-only (predictable staleness), and random (surprisingly decent, cheap). Choose based on whether your access pattern is recency-driven or frequency-driven.

**218. 🔴 How do you cache authenticated content without leaking data?**
Split the response: cache the shared shell publicly and fetch personalised fragments client-side; or use edge caching with a cache key that includes an auth-derived segment (plan tier, role) rather than user identity; or use private browser caching with short max-age and `Vary: Cookie`. Never cache a user-specific response in a shared cache.

**219. 🟡 How do you measure whether a cache is worth keeping?**
Hit rate, origin load reduction, and p95 latency delta — plus incident count attributable to staleness. A cache with a 20% hit rate and two stale-data incidents per quarter is a net negative; delete it and fix the query instead.

**220. 🔴 When is adding a cache the wrong fix?**
When the underlying query is slow due to a missing index or an N+1 — you're papering over a 10-minute fix with permanent complexity and a new consistency problem. Also when data is write-heavy or read once, where the cache never gets a chance to pay for itself.

---

# SECTION 11 — Scaling, Queues & Async Processing (221–233)

**221. 🟢 What is the first question to ask before scaling anything?**
"What is the actual bottleneck?" Measure before acting: CPU, memory, DB time, external API latency, event-loop lag, lock contention. Scaling the wrong tier costs money and hides the real problem — often you scale the app and simply deliver more load to an already-saturated database.

**222. 🟢 What makes a service horizontally scalable?**
Statelessness: no in-memory sessions, no local file storage, no in-process schedulers or locks, no assumption of "the" instance. All shared state lives in the database, cache, or object storage, so any request can be served by any instance.

**223. 🟡 When should work move to a queue instead of the request path?**
When it's slow (>1–2s), can fail independently, must be retried, calls unreliable third parties, or fans out to multiple side effects. Email, PDF generation, image processing, webhook delivery, and analytics events all belong off the request thread — return 202 and a status handle.

**224. 🟡 Compare a task queue (Celery/BullMQ/Sidekiq) with a message broker/log (Kafka, Kinesis).**
Task queues are job-oriented: one consumer per job, retries, scheduling, priorities — right for "do this work." Logs are event-oriented: durable ordered streams, many independent consumers replaying at their own offset — right for "this happened, whoever cares." Using Kafka as a job queue, or Celery as an event bus, fights the tool.

**225. 🔴 What guarantees does your queue give: at-most-once, at-least-once, exactly-once?**
Most systems give at-least-once — duplicates will happen. "Exactly-once" is either transactional within one system or emulated with idempotent consumers plus deduplication keys. The practical rule: design every consumer to be idempotent and stop worrying about the label.

**226. 🟡 What is a dead letter queue and why is it non-optional?**
A destination for messages that failed after all retries. Without it, poison messages either block the queue forever or vanish silently. With it, you get an inspectable backlog of failures, alerting on its depth, and a replay path once the bug is fixed.

**227. 🔴 What is backpressure and what happens without it?**
Backpressure is a downstream component signalling upstream to slow down. Without it, queues grow unboundedly, memory exhausts, latency climbs past usefulness, and the system fails catastrophically instead of degrading. Implement via bounded queues, rate limiting producers, and load shedding at the edge.

**228. 🟡 What is load shedding and when should you do it?**
Deliberately rejecting a fraction of requests (cheaply, with 429/503) to keep the rest healthy when overloaded. Do it when you're past capacity: serving 70% of users well beats serving 100% of them a timeout. Shed low-priority traffic first — bots, background sync, non-critical endpoints.

**229. 🟡 How do you handle scheduled/cron work in a horizontally scaled system?**
Not with an in-process timer on every instance (you'll run the job N times). Use a leader-election lock (Redis/DB advisory lock), a dedicated scheduler service, or the platform's scheduler (cloud cron, Supabase pg_cron, Vercel cron) with an idempotent handler.

**230. 🔴 What is the outbox pattern and what problem does it solve?**
Writing an event into an `outbox` table inside the same DB transaction as the business change, then a relay publishes it to the broker. It solves the dual-write problem: without it, you can commit the DB change and fail to publish (or vice versa), leaving systems permanently inconsistent.

**231. 🟡 How do you design retries so they don't cause an outage?**
Exponential backoff with jitter, a hard retry budget, retry only idempotent/safe operations, respect `Retry-After`, and add a circuit breaker. Naive fixed-interval retries across a whole fleet turn a brief downstream blip into a synchronised, self-sustaining retry storm.

**232. 🔴 What's the difference between scaling reads and scaling writes?**
Reads scale relatively easily: caching, replicas, CDN, denormalised views. Writes are the hard ceiling — they must reach the primary, contend on locks, and are bounded by disk and replication. Options are batching, partitioning/sharding, moving writes off the hot path via queues, or reducing write volume outright.

**233. 🔴 What capacity questions should the TRD's "Concurrent users: 500" line actually answer?**
Concurrent *users* isn't a load figure. You need requests per second at peak, read/write ratio, average and p99 payload sizes, expected data growth per month, and burst shape (steady vs event-driven spikes). Without those, no one can size instances, pools, or budgets.

---

# SECTION 12 — Auth & Security Architecture (234–243)

**234. 🟢 Sessions vs JWTs — what's the real trade-off?**
Server sessions are revocable instantly and small on the wire, but require a shared session store lookup per request. JWTs are stateless and verifiable anywhere (great for distributed services and edge), but can't be revoked before expiry without reintroducing state. Short-lived access tokens + refresh tokens is the standard compromise.

**235. 🟡 Why is "just use JWTs, they scale better" often wrong?**
Because the scaling problem it solves (a session store lookup) is a sub-millisecond Redis GET, while the problem it creates (no instant revocation, token bloat, key rotation, clock skew, storing tokens safely in the browser) is architectural. Use JWTs when you genuinely need stateless cross-service verification.

**236. 🟡 Where should tokens be stored in a browser and why?**
`httpOnly`, `Secure`, `SameSite` cookies — inaccessible to JavaScript, so XSS can't exfiltrate them (you must then handle CSRF). `localStorage` is convenient but readable by any injected script, making a single XSS a full account takeover. Choose cookies plus CSRF protection.

**237. 🟡 What's the difference between authentication and authorisation, architecturally?**
Authentication establishes identity once, at the edge. Authorisation is a per-resource decision that must be enforced at the data layer — every query, every endpoint. Systems fail when authz is enforced only in the UI or only in one middleware, and a direct API call bypasses it.

**238. 🔴 Compare RBAC, ABAC, and ReBAC.**
RBAC: permissions attached to roles (admin/user/viewer) — simple, coarse, explodes combinatorially with edge cases. ABAC: policies over attributes (department, region, time) — flexible, harder to audit. ReBAC: permissions derived from relationships in a graph (Zanzibar/OpenFGA) — right for shared-document and hierarchy models like Drive/Notion.

**239. 🟡 Where should authorisation logic live in the template's Supabase-style stack?**
Primarily in RLS policies, so it's enforced regardless of which client calls in — with the caveat that service-role keys bypass RLS entirely, so any server route using that key must re-enforce authz itself. Duplicating rules in application code without a single source of truth is how they drift.

**240. 🔴 Why is "protected by auth middleware" insufficient as a security requirement?**
Because middleware answers "is this person logged in?", not "may *this* person touch *this* record?" Object-level authorisation (IDOR protection) is the most common real-world API vulnerability: valid token, someone else's `/api/projects/:id`.

**241. 🟡 Where do you enforce rate limiting on auth endpoints and why does it matter architecturally?**
At the edge and per-identifier (IP + email/account), with progressive delays and lockouts. It matters because login, signup, password reset, and OTP endpoints are the cheapest attack surface for credential stuffing and enumeration — and each attempt costs you a bcrypt/argon2 hash, which is CPU-expensive by design.

**242. 🔴 How does secret management differ between a monolith and a distributed system?**
A monolith has one env var set. Distributed systems need a secret store (Vault, AWS Secrets Manager, Doppler) with per-service scoping, rotation without redeploy, audit logging, and short-lived credentials. Environment variables copy-pasted across ten services means rotation is a manual archaeology project.

**243. 🟡 What is defence in depth in a web architecture?**
Independent controls at each layer: WAF/rate limiting at the edge, authn at the gateway, authz in the app, RLS in the database, encryption at rest, least-privilege service accounts, and monitoring/alerting on anomalies. Any single control will eventually fail; the design assumption is that a breach of one layer isn't a breach of the system.

---

# SECTION 13 — Deployment, Observability & Cost Architecture (244–250)

**244. 🟢 Compare blue-green, canary, and rolling deployments.**
Blue-green: two full environments, instant cutover and instant rollback, doubles infra cost briefly and needs schema compatibility. Canary: route a small traffic percentage to the new version, watch metrics, then ramp — best risk/cost ratio, needs good observability. Rolling: replace instances gradually — cheapest, but both versions run simultaneously and rollback is slow.

**245. 🟡 Why must database migrations be decoupled from application deploys?**
Because during any non-atomic deploy, old and new code run against the same schema simultaneously. A destructive migration (dropping or renaming a column) breaks whichever version doesn't expect it, and makes rollback impossible. Expand/contract keeps every intermediate state valid.

**246. 🟡 What are the three pillars of observability and what does each answer?**
Metrics: "is something wrong, and how much?" (cheap, aggregated, alertable). Logs: "what exactly happened in this case?" (detailed, expensive at volume). Traces: "where did the time go across services?" (essential in distributed systems). You need all three; alerting on metrics, debugging with traces, confirming with logs.

**247. 🔴 What should you alert on, and what should you merely graph?**
Alert on symptoms users feel — error rate, latency SLO burn, queue depth growth, saturation trends. Graph causes — CPU, memory, individual query times. Alerting on causes produces pages for conditions nobody experienced; alerting on symptoms produces pages that always matter.

**248. 🟡 What is an SLI, SLO, and error budget, and how do they shape architecture?**
SLI: a measured indicator (p99 latency, success rate). SLO: the target (99.9% of requests under 500ms). Error budget: the allowed failure (0.1%). They shape architecture by making reliability a spend decision — if you're burning budget, you stop shipping features and fix reliability; if you're under-spending it, you're over-engineered.

**249. 🔴 How does architecture drive cloud cost, and where do costs usually hide?**
The big drivers are always-on compute, data egress, cross-AZ/region traffic, managed service tiers, and log/metric ingestion volume. Hidden costs: chatty microservices paying cross-AZ fees on every hop, verbose debug logging at scale, unbounded retries, and orphaned resources. Cost is a non-functional requirement — put it in the TRD alongside latency.

**250. 🔴 If you could add one section to the template's TRD, what would it be and why?**
A "Non-Functional Requirements & Trade-offs" section stating expected load (RPS, not user counts), consistency requirements per entity, failure/degradation behaviour for each dependency, cost ceiling, and — crucially — the *rejected* alternatives with reasons. The template captures what will be built; it doesn't capture the constraints that make the design defensible or the reasoning a future engineer needs to change it safely.

---

# SECTION 14 — Networking & Protocol Fundamentals (251–265)

**251. 🟢 Why does an architect need to understand the TCP handshake and TLS handshake?**
Because every new connection costs round trips — roughly 1 RTT for TCP plus 1–2 for TLS before a single byte of your response moves. On a 150ms transcontinental link that's 300–450ms of pure setup, which is why connection reuse, keep-alive, and edge termination often beat any application optimisation you could make.

**252. 🟢 What is HTTP keep-alive and why does it matter behind a load balancer?**
It reuses one TCP connection for multiple requests, amortising handshake cost. Behind an LB it matters twice: client↔LB keep-alive saves handshakes for users, and LB↔backend connection pooling saves them internally. Misconfigured idle timeouts (LB longer than backend) produce mysterious intermittent 502s when the backend closes a connection the LB still believes is open.

**253. 🟡 What did HTTP/2 change architecturally, and what did it not fix?**
It added multiplexing (many streams on one connection), header compression, and server push (now largely deprecated). It killed the need for domain sharding and asset concatenation. It did not fix TCP head-of-line blocking: one lost packet still stalls every multiplexed stream on that connection.

**254. 🟡 What problem does HTTP/3 / QUIC solve?**
It moves to UDP with per-stream loss recovery, eliminating TCP head-of-line blocking, and merges the transport and TLS handshakes into ~1 RTT (0-RTT on resumption). It also survives network changes (Wi-Fi → cellular) via connection IDs, which matters enormously for mobile.

**255. 🟡 When does HTTP/3 not help you?**
On low-loss wired networks with already-warm connections, where the gains are marginal. It also complicates operations: some corporate firewalls block UDP/443, debugging tooling is less mature, and not every load balancer or observability agent understands it fully.

**256. 🔴 Explain head-of-line blocking at three different layers.**
TCP: a lost packet stalls all multiplexed HTTP/2 streams. HTTP/1.1 pipelining: a slow response blocks subsequent ones on the same connection. Application/queue: one poison message or slow consumer blocks the partition behind it. The same pattern, three layers — and the fix is always independent lanes (QUIC streams, separate connections, partitioned queues).

**257. 🟡 What is the difference between latency and bandwidth, and which usually limits web performance?**
Bandwidth is throughput; latency is round-trip delay. For typical web workloads latency dominates — beyond about 5 Mbps, extra bandwidth barely improves page load, while every 20ms of RTT reduction is directly felt. This is why CDNs and edge termination work: they attack latency, not bandwidth.

**258. 🟡 Why do CORS problems appear at the architecture level, not just as a browser annoyance?**
CORS is a symptom of a cross-origin architecture: separate frontend and API domains. It forces preflight OPTIONS round trips on non-simple requests, complicates cookie-based auth (`SameSite`, `credentials: include`), and pushes teams toward token-in-localStorage anti-patterns. Same-origin deployment (API under `/api` on the same domain, or a proxy) removes the whole class of problem.

**259. 🟡 What is DNS TTL and how does it constrain your failover plan?**
TTL is how long resolvers cache your record. A 300s TTL means up to 5 minutes (often longer — some resolvers ignore TTLs) before traffic moves after a DNS change. If your DR plan is "update DNS," your real RTO includes that propagation window. Anycast or an LB-level switch fails over far faster.

**260. 🔴 Why is DNS a single point of failure that people forget to design for?**
Because it's the first lookup in every request and often delegated to one provider. A DNS provider outage takes you down even though every server is healthy. Mitigate with two independent DNS providers serving the same zone, sensible TTLs, and monitoring that resolves from outside your network.

**261. 🟡 What is the difference between a public and private subnet, and where does each tier belong?**
Public subnets have a route to an internet gateway; private ones don't. Load balancers and bastion hosts go public; application servers and databases go private, reaching outbound internet via NAT. A database with a public IP is one misconfigured security group away from being a breach.

**262. 🔴 Why does cross-AZ traffic matter for both latency and cost?**
Availability zones are physically separate data centres — typically sub-millisecond apart but billed per GB for inter-AZ transfer. A chatty microservice architecture that makes ten cross-AZ hops per request pays that ten times, and the latency compounds. Zone-aware routing keeps a request within one AZ where redundancy allows.

**263. 🟡 What is mTLS and when is it worth the operational cost?**
Mutual TLS, where both client and server present certificates — the service identity primitive of zero-trust networking. Worth it for regulated environments, cross-boundary service calls, and partner integrations. The cost is certificate lifecycle management, which is why teams adopt it through a mesh rather than by hand.

**264. 🟡 What does a WAF give you that application-level validation doesn't?**
Blocking at the edge before requests consume your compute, virtual patching for a known CVE while you deploy a real fix, bot and scraper mitigation, and rule sets maintained by someone whose full-time job is tracking attacks. It's a complement to input validation, never a replacement.

**265. 🔴 How do you architect for DDoS resilience?**
Push absorption to a provider with anycast capacity (Cloudflare, CloudFront, Shield), keep origin IPs hidden behind it, rate limit and challenge at the edge, cache aggressively so attacks hit the CDN not your origin, ensure autoscaling has a hard ceiling so you fail rather than bankrupt yourself, and design graceful degradation so read-only mode is possible.

---

# SECTION 15 — File Storage & Media Architecture (266–277)

**266. 🟢 Why should file uploads never pass through your application server?**
Because they occupy a request thread or function for the entire upload duration, consume memory or disk, hit body-size limits, and multiply bandwidth cost (client→app→storage). Use presigned URLs so the client uploads directly to object storage and only tells your API the resulting key.

**267. 🟡 How does the presigned-URL upload flow work end to end?**
Client requests permission → your API authorises, generates a short-lived presigned PUT URL with constrained content type and size → client uploads directly to S3/R2/Supabase Storage → client (or a storage event webhook) notifies your API → API validates and records the object key in the database. The DB stores the key, never the file.

**268. 🟡 What can go wrong with presigned uploads and how do you defend?**
Orphaned objects (upload succeeds, callback never arrives — sweep with a lifecycle rule or reconciliation job), lying clients (enforce content-type and size in the policy, verify server-side after upload), and malicious files (scan asynchronously before making them public). Never trust the client-reported filename or MIME type.

**269. 🟡 S3 vs Cloudflare R2 vs Supabase Storage — how do you choose?**
S3: deepest ecosystem, most features, but egress is billed and expensive. R2: S3-compatible API with zero egress fees — compelling for media-heavy or public-download workloads. Supabase Storage: integrated with your auth and RLS policies, simplest when you're already on Supabase and volumes are modest. Egress pricing is usually the deciding number.

**270. 🔴 Why is egress cost an architectural constraint rather than a billing detail?**
Because it can exceed your compute bill by an order of magnitude at media scale, and it silently penalises certain designs — serving video from origin, cross-region replication, chatty data export APIs. Architectures that look elegant on a whiteboard become unaffordable purely through data movement.

**271. 🟡 Where should image resizing and transformation happen?**
Not in your app on the request path. Options: an image CDN with URL-based transforms (Cloudinary, imgix, Cloudflare Images, Vercel Image), an on-demand edge function with caching, or an async pipeline generating variants on upload. Pick based on how predictable your variant set is — fixed variants favour pre-generation, arbitrary sizes favour on-demand plus cache.

**272. 🟡 How do you serve private files securely without proxying them?**
Short-lived presigned GET URLs generated after an authorisation check, or signed CDN URLs/cookies. The authorisation decision happens in your app; the bytes are served by storage or CDN. Making a bucket public "temporarily" is how private documents end up in search results.

**273. 🟡 What metadata should the database store about a file, and what should it never store?**
Store: storage key, bucket, content type, byte size, checksum, owner, upload timestamp, processing status, and derived variant keys. Never store the file bytes themselves in the relational database — it bloats backups, wrecks replication, and makes every restore an ordeal.

**274. 🔴 When is storing files in the database actually acceptable?**
Small binaries (under a few tens of KB) that must be transactionally consistent with a row and are read rarely — signatures, small thumbnails, encrypted key material. Even then, know that you're trading backup size and replication throughput for atomicity.

**275. 🟡 How do you handle file deletion consistently with soft-deleted database records?**
Decouple them: soft-delete the row immediately, enqueue an object deletion after the retention window, and reconcile periodically for orphans in both directions. Deleting the object synchronously with the row makes undo impossible and leaves broken references if the transaction rolls back.

**276. 🔴 What is data lifecycle management in object storage and why does it belong in the design?**
Rules that transition objects to cheaper tiers (infrequent access, glacier) or expire them after N days. It belongs in the design because storage grows monotonically and silently — a product with no lifecycle policy pays forever for temp uploads, old exports, and abandoned drafts nobody will ever read.

**277. 🟡 What does the template's "Avatar upload to file storage" step need that the checklist doesn't mention?**
Size and dimension limits, content-type allowlisting, server-side verification that the file is actually an image, variant generation, a CDN-cacheable URL with cache busting on change, deletion of the previous avatar, and a default/fallback. "Upload works" and "upload is production-safe" are quite different deliverables.

---

# SECTION 16 — Payments, Billing & Third-Party Integrations (278–289)

**278. 🟢 Why must the payment provider — not your database — be the source of truth for subscription state?**
Because the provider owns the billing lifecycle: renewals, dunning, proration, chargebacks, and involuntary churn all happen there, often without any action from your app. Your `subscriptions` table is a local cache of their state, kept fresh by webhooks and reconciled periodically.

**279. 🟡 Why should you never grant access based on the checkout redirect?**
Because the redirect is client-controlled and can be forged, skipped, or lost when the user closes the tab. Payment confirmation must come from a verified server-to-server webhook. The redirect is a UX affordance only — show "processing" and let the webhook flip the entitlement.

**280. 🟡 What makes a webhook handler production-grade?**
Signature verification, replay/duplicate tolerance via the event ID, ordering tolerance (events can arrive out of order — compare timestamps or refetch current state), fast 200 then async processing, dead-lettering on repeated failure, and idempotent state transitions. Assume every event will arrive twice and one will arrive late.

**281. 🔴 How do you handle webhook events arriving out of order?**
Don't apply deltas; apply state. On any relevant event, either compare the event's timestamp/version against what you've stored and ignore older ones, or refetch the authoritative object from the provider's API and overwrite. Sequential "subscription updated then canceled" logic breaks the first time they swap.

**282. 🟡 What is an idempotency key and where does it belong in a payment flow?**
A client-generated unique key sent with a mutating request so the provider returns the original result on retry instead of charging twice. It belongs on every create-charge, create-subscription, and refund call — and you should persist it alongside your local intent record so retries after a crash reuse the same key.

**283. 🔴 Design the state machine for a subscription. What states and transitions matter?**
States: none → pending → active → past_due → canceled/unpaid, plus trialing and paused. Transitions are driven by provider events, not user clicks. Critically, define entitlement per state: `past_due` usually keeps access during a grace window, `canceled` keeps access until period end, `unpaid` revokes. The template's three-value status column under-models this.

**284. 🟡 How should feature gating be implemented so it's not scattered across the codebase?**
One entitlement resolver: given a user, return a capability set (`can_export`, `seat_limit`, `api_rate`). UI and API both ask that resolver; nothing checks `plan === 'pro'` inline. When you add a plan or a grandfathered customer, you change one function instead of forty conditionals.

**285. 🔴 How do you reconcile billing state if webhooks are missed for a day?**
Run a scheduled reconciliation that pages through the provider's subscriptions/events API and diffs against your table, correcting drift and alerting on mismatches. Webhooks are an optimisation for freshness; reconciliation is the correctness guarantee. Systems without it discover the gap when a customer complains.

**286. 🟡 What are the architectural implications of usage-based billing versus flat subscriptions?**
Usage-based requires accurate, durable, idempotent metering (an append-only event log, not a counter you increment), aggregation and reporting the customer can audit, rate limiting tied to entitlements, and a story for late-arriving or duplicated usage events. It's a data pipeline problem, not a billing-page problem.

**287. 🟡 How should you architect around a third-party API you don't control?**
Wrap it in an anti-corruption layer, set aggressive timeouts, add retries with backoff and a circuit breaker, cache what you can, define explicit degraded behaviour when it's down, monitor its latency and error rate as first-class metrics, and keep credentials rotatable. Assume it will have an outage during your launch week.

**288. 🔴 What is the difference between a hard dependency and a soft dependency, and why classify them?**
A hard dependency breaks the core flow when down (payment provider during checkout, auth provider at login). A soft one degrades a secondary feature (analytics, email, recommendations). Classifying them tells you where to invest in fallbacks, what to put behind a circuit breaker, and which outages justify a page at 3am.

**289. 🟡 How do you test integrations you don't control?**
Provider sandboxes and test cards for happy paths, recorded fixtures (VCR-style) for deterministic CI, contract tests against the documented schema, and deliberate fault injection (timeouts, 500s, malformed payloads) to verify your degraded behaviour. Never let CI depend on a live third-party endpoint.

---

# SECTION 17 — AI / LLM Application Architecture (290–306)

**290. 🟢 What changes architecturally when an LLM call enters the request path?**
Latency jumps from milliseconds to seconds, cost becomes per-request and variable, output is non-deterministic, failures include content failures not just HTTP errors, and rate limits are provider-imposed. Every assumption behind a synchronous CRUD endpoint — fast, cheap, deterministic, retryable — stops holding.

**291. 🟢 Why is streaming the default UX pattern for LLM responses?**
Because a 12-second wait with a spinner feels broken while the same 12 seconds with tokens appearing feels responsive. Architecturally it means Server-Sent Events or WebSockets, a runtime that supports long-lived streaming responses, and load balancer/proxy configuration that doesn't buffer or time out mid-stream.

**292. 🟡 What breaks when you put a streaming LLM endpoint behind a default load balancer or CDN?**
Response buffering (the proxy holds the whole body, destroying the streaming effect), idle timeouts cutting long generations, and compression middleware that waits for the full payload. You need `X-Accel-Buffering: no` or equivalent, raised timeouts, and a platform that supports streaming responses end to end.

**293. 🟡 Where should the LLM API key live and why is this non-negotiable?**
Server-side only. A key in the browser is a key on someone's cloud bill within hours — LLM keys are actively scraped. All model calls proxy through your backend, which also gives you the only place to enforce per-user rate limits, spend caps, logging, and prompt injection defences.

**294. 🟡 How do you control LLM cost architecturally rather than by hoping?**
Per-user and per-org token budgets enforced before the call, model routing (cheap model for classification/routing, expensive one only when needed), aggressive prompt caching, output token limits, caching of identical requests, and truncation strategies for context. Cost controls belong in code, not in a monthly dashboard review.

**295. 🔴 What is model routing and when is it worth building?**
Sending each request to the cheapest model that can handle it — a small model for extraction and classification, a large one for reasoning, sometimes with escalation on low confidence. Worth building once LLM spend is a material line item and you can measure quality per tier. Premature routing adds complexity and an evaluation burden you can't yet meet.

**296. 🟡 What is RAG and what are its actual failure modes?**
Retrieval-augmented generation: fetch relevant context, then generate grounded on it. Failure modes are mostly retrieval, not generation — poor chunking splitting an answer across chunks, embeddings that miss keyword matches, no reranking so the right passage sits at position 40, and stale indexes. If RAG answers badly, inspect the retrieved chunks before blaming the model.

**297. 🔴 Why is hybrid search usually better than pure vector search?**
Because dense embeddings capture semantics but miss exact tokens — product codes, error strings, names, acronyms. Keyword/BM25 catches those but misses paraphrase. Combining both (reciprocal rank fusion) plus a cross-encoder reranker consistently beats either alone, which is why "just add a vector DB" underdelivers.

**298. 🟡 What decisions define a chunking strategy and why do they matter so much?**
Chunk size, overlap, and boundary respect (headings, paragraphs, code blocks), plus what metadata rides along (source, section, permissions, timestamp). They matter because chunking determines what can ever be retrieved — no reranker or model recovers information split awkwardly across two chunks.

**299. 🔴 How do you enforce authorisation in a RAG system?**
Filter at retrieval time using metadata the index carries (tenant, ACL, document visibility), never after generation. If a restricted document reaches the context window, it has effectively leaked — the model may paraphrase it. Also re-check permissions at answer time, since access can be revoked after indexing.

**300. 🟡 What is prompt injection and why can't it be fully solved by validation?**
Untrusted content (a fetched web page, a user-uploaded document, an email) contains instructions the model may follow. It can't be solved by filtering because there's no syntactic boundary between data and instruction in natural language. Mitigate architecturally: least-privilege tools, human confirmation for irreversible actions, output validation, and never letting model output directly execute privileged operations.

**301. 🔴 How does agentic behaviour change the architecture compared to a single completion call?**
You get multi-step loops with unpredictable duration and cost, tool calls with side effects, and partial failures mid-run. That demands durable execution (a workflow engine or persisted state machine), step-level timeouts and iteration caps, per-run budgets, idempotent tools, full trace logging, and an approval gate for anything destructive.

**302. 🟡 Why should long LLM workloads not be synchronous HTTP requests?**
Because they exceed serverless and gateway timeouts, hold connections open, can't survive a deploy, and give the user no recovery if the tab closes. Use a job model: submit → job ID → poll or stream progress → durable result. This is the same argument as any long-running task, just with worse latency.

**303. 🔴 What does an evaluation pipeline look like and why is it architecture, not QA?**
A versioned dataset of inputs with expected properties, automated scoring (exact match, rubric-graded by a model, or human review), run on every prompt or model change, with results tracked over time. It's architecture because without it you cannot safely change a prompt, upgrade a model, or tune retrieval — you're deploying blind.

**304. 🟡 How do you handle provider outages and model deprecations?**
Abstract behind your own interface, keep at least one alternate provider wired and tested, pin model versions explicitly rather than using floating aliases, monitor deprecation notices, and define degraded behaviour (queue the request, fall back to a smaller model, or return a clear failure). Model APIs deprecate faster than almost any other dependency you have.

**305. 🟡 What should you log for an LLM feature, and what must you not log?**
Log: model, prompt version, token counts, latency, cost, tool calls, retrieval IDs, and a trace ID. Be careful with raw prompt and output content — it frequently contains user PII, so apply the same retention, redaction, and consent rules as any other sensitive data store, and check the provider's data retention terms.

**306. 🔴 When should an AI feature not be in the product at all?**
When the failure mode is unacceptable and you can't gate it — irreversible actions without review, regulated advice, or anything where a confident wrong answer causes real harm. Also when a deterministic solution exists: a regex, a lookup table, or a form field beats a model on cost, latency, and reliability every time.

---

# SECTION 18 — Environments, Config, Feature Flags & CI/CD (307–318)

**307. 🟢 What's the difference between configuration and secrets, and why keep them separate?**
Config is non-sensitive and environment-varying (API base URLs, feature toggles, log level); secrets grant access (keys, tokens, DB passwords). Separate them because they have different lifecycles: config is reviewed in pull requests, secrets are rotated, audited, and access-controlled. Mixing them means secrets end up in git history.

**308. 🟢 Why is `NEXT_PUBLIC_` (or any client-exposed prefix) an architectural hazard?**
Because it inlines the value into the browser bundle at build time. Anything with that prefix is public forever, including in cached bundles. The template correctly lists the Supabase anon key as public and the service role key as not — one prefix mistake there turns RLS into decoration.

**309. 🟡 What does dev/prod parity mean and where do teams break it?**
Environments should differ only in scale and data, not in kind. Teams break it with SQLite locally and Postgres in prod, no HTTPS locally, mocked auth in dev, different Node versions, and no load balancer in staging. Every difference is a class of bug that can only be found in production.

**310. 🟡 Do you need a staging environment, and what makes one useful versus theatre?**
Useful staging has production-shaped data volume, the same infrastructure topology, real third-party sandboxes, and is actually used before every release. Staging with 12 rows and mocked integrations catches nothing and creates false confidence — in that case, feature flags plus fast rollback in production are more honest.

**311. 🔴 Feature flags vs long-lived branches — what's the architectural argument?**
Flags let you merge continuously, decouple deploy from release, ship dark, ramp gradually, and kill a bad feature without a rollback deploy. Long-lived branches accumulate merge risk and delay integration feedback. The cost of flags is real: dead flags become permanent conditional complexity, so they need an expiry and cleanup discipline.

**312. 🟡 What types of feature flags exist and why does the distinction matter?**
Release toggles (short-lived, remove after rollout), experiment toggles (A/B, removed after the decision), ops toggles (kill switches, kept indefinitely), and permission toggles (entitlements — arguably not flags at all). Treating them all the same is why codebases accumulate hundreds of stale conditionals nobody dares delete.

**313. 🟡 What should a CI pipeline do, in what order, and why?**
Fast feedback first: lint and typecheck, then unit tests, then build, then integration tests, then deploy to a preview environment, then E2E smoke on the critical path. Ordering matters because a 40-second typecheck failure is far better than discovering the same bug after a 12-minute test suite.

**314. 🔴 What does "deploy is not release" mean and why is it powerful?**
Deployment puts code on servers; release exposes behaviour to users. Decoupling them via flags means you deploy small increments continuously with low risk and choose the release moment independently — including per-cohort. It converts scary Friday releases into a config change you can reverse in seconds.

**315. 🟡 What makes a rollback plan credible?**
A tested path back, forward-compatible database migrations so the previous version still runs, no destructive schema changes in the same deploy, artifact immutability (redeploy the exact previous build, not a rebuild), and a documented decision threshold. A rollback plan nobody has executed is an aspiration.

**316. 🟡 What belongs in infrastructure-as-code, and what's the cost of dashboard-clicked infra?**
Everything reproducible: networks, databases, queues, LBs, DNS, IAM, alerts. Dashboard-clicked infra can't be reviewed, diffed, recreated in another region, or rebuilt after an accident, and its configuration lives only in one person's memory. The cost surfaces precisely when you're under pressure.

**317. 🔴 Trunk-based development vs GitFlow — how does the choice interact with architecture?**
Trunk-based (short-lived branches, flags, continuous merge) suits continuously deployed services and requires strong automated testing. GitFlow suits versioned/on-prem releases with support branches. Choosing GitFlow for a SaaS product usually means slow integration and painful merges; choosing trunk-based without test coverage means shipping breakage quickly.

**318. 🟡 How should environment configuration be validated?**
At process start, parse and validate the entire config against a schema (Zod/Pydantic) and refuse to boot if anything is missing or malformed. Failing at startup with "STRIPE_WEBHOOK_SECRET is required" beats failing at 2am inside a payment handler with an undefined variable.

---

# SECTION 19 — Testing, Data Privacy & Compliance Architecture (319–330)

**319. 🟢 What is the testing pyramid and why does its shape matter for architecture?**
Many fast unit tests, fewer integration tests, very few end-to-end tests. The shape matters because inverted pyramids (mostly E2E) give slow, flaky feedback that teams learn to ignore. Architecture influences the shape: pure functions and clean boundaries are unit-testable; logic buried in controllers and SQL is not.

**320. 🟡 What should be tested at the integration level rather than mocked?**
Anything where the interaction *is* the risk: database queries and migrations, RLS policies, auth middleware, serialisation boundaries, and queue consumers. Mocking your own database means your tests validate your mocks. Use real Postgres in a container — it's cheap and catches the bugs that matter.

**321. 🔴 What is contract testing and when does it become necessary?**
Consumer and provider each test against a shared contract (Pact, OpenAPI schema validation) so a provider change that breaks a consumer fails in the provider's CI. It becomes necessary once services deploy independently — at that point, integration tests across the whole system are too slow and contract drift is the dominant failure.

**322. 🟡 How do you handle test data without either flakiness or a privacy incident?**
Generate synthetic data with factories, seed deterministically per test, isolate with transactions or per-test schemas, and never copy production data with real PII into lower environments. If you must use production-shaped data, anonymise irreversibly at export time, not "later."

**323. 🟡 Why are flaky tests an architectural problem, not just an annoyance?**
Because they destroy the signal: once a suite is 3% flaky, engineers retry rather than investigate, and real regressions ship. Flakiness usually points at real design issues — shared mutable state, time dependence, ordering assumptions, race conditions — that will also bite in production.

**324. 🔴 What does GDPR's right to erasure require architecturally?**
The ability to find every copy of a person's data — primary tables, denormalised copies, caches, search indexes, logs, analytics warehouse, backups, and third-party subprocessors — and delete or irreversibly anonymise it within a deadline. Systems that spray PII into logs and event streams discover this is a months-long project.

**325. 🔴 How do soft deletes and immutable backups conflict with deletion requirements?**
Soft deletes leave the data present and often still queryable; backups by design preserve it. The usual reconciliation is documented retention windows (backups expire in N days and are never restored selectively), crypto-shredding (delete the per-user encryption key so backup ciphertext is unrecoverable), and hard-deleting from live systems immediately.

**326. 🟡 What is data minimisation and why is it the cheapest privacy control?**
Collect and retain only what you need, for only as long as you need it. It's cheapest because data you never stored can't leak, can't be subpoenaed, doesn't need encryption or deletion tooling, and doesn't appear in your breach notification. Every optional field on a signup form is a permanent liability.

**327. 🟡 How should PII be segregated in the schema?**
Isolate identifying fields into a dedicated table or service with tighter access controls, reference it by an internal ID everywhere else, and encrypt sensitive columns at the application level where warranted. This makes access auditable, deletion tractable, and analytics safe on the non-PII side.

**328. 🔴 What is data residency and how does it constrain architecture?**
A legal requirement that certain data stay within a jurisdiction. It forces region-partitioned storage, region-aware routing at the edge, careful selection of subprocessors, and often duplicated infrastructure per region — which then breaks global queries, global uniqueness constraints, and single-pane analytics.

**329. 🟡 What should an audit log capture, and what makes it trustworthy?**
Who, what, when, from where, before/after values, and a correlation ID — written append-only to storage the application cannot modify or delete, with its own retention policy. An audit log the app can rewrite is evidence of nothing, which defeats its entire purpose in a compliance review.

**330. 🔴 How do you decide the retention period for logs, events, and user data?**
Balance debugging need (usually 7–30 days for verbose logs), legal/regulatory minimums (often years for financial records), privacy maximums (delete when the purpose expires), and cost (log ingestion is frequently a top-three cloud line item). Write it down per data category — undefined retention defaults to "forever," which is the worst option on every axis.

---

## How to Use This Question Bank

| Use case | Suggested approach |
|---|---|
| **Interview screening (30 min)** | Pick 6–8 🟢/🟡 questions across sections 1, 2, 5, 9 |
| **AI/LLM engineer interview** | Section 17, plus Q108, 160, 302 on vectors, streaming and async |
| **Senior/architect interview (60 min)** | 4–5 🔴 questions; follow each with "what would change your answer?" |
| **Team learning sessions** | One section per week; have engineers answer before reading the answers |
| **Design review checklist** | Sections 1, 7, 9, 11, 13, 18 map directly to review agenda items |
| **Pre-launch readiness** | Sections 12, 14, 16, 18, 19 — security, DDoS, billing, rollback, retention |
| **Self-assessment** | Answer without reading; anything you can't answer in 2 minutes is a study target |

**Follow-up prompts that separate memorised from understood answers:**

- "When would that be the wrong choice?"
- "What would you measure to know you picked correctly?"
- "What's the cheapest reversible version of that decision?"
- "What breaks first when traffic goes up 10×?"
- "Who is on call for this, and what does their runbook say?"

---

*Generated as a companion to the Vibe Coding Pre-Build Template — 330 questions, 19 sections, mixed difficulty with answer keys.*
