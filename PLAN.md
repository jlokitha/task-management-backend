# PLAN.md — Task Management System (Phase 1)

**Backend choice:** NestJS — chosen for its modular, testable, and enterprise-ready architecture; built-in TypeScript support, dependency injection, guards, interceptors and seamless integration with validation libraries (class-validator), Prisma/TypeORM and JWT workflows. This yields maintainable, scalable code and enforces clean separation of concerns.

**Architecture overview:**  
- API: NestJS (REST) — Auth module, Tasks module, shared Infrastructure (validation, logging, error handling).  
- DB: PostgreSQL (hosted) — tables: users, tasks (priority, status, dueDate).  
- Auth: JWT access token (short-lived) + refresh token (httpOnly cookie) handled in Auth module.  
- Deployment: Backend to Render/Railway/Container; Database hosted (Supabase/Neon/RDS).  
- Flow: Browser ↔ Next.js (fetches protected endpoints using httpOnly cookie) ↔ NestJS API ↔ Postgres.

**Security considerations (client + server):**  
- **Password hashing:** bcrypt with pepper/salt; never store plaintext.  
- **Rate limiting:** Throttle auth endpoints (e.g., 5 req/60s) and global limits via `@nestjs/throttler`.  
- **Input validation & sanitization:** DTOs + `class-validator` on server; client-side form validation.  
- **Auth storage:** use httpOnly, Secure cookies for refresh tokens; store access token in memory (not localStorage) to reduce XSS risk.  
- **CSRF protection:** Double-submit cookie / origin checks when using cookies; set SameSite.  
- **CSP & Security headers:** Configure CSP, HSTS, X-Frame-Options, X-XSS-Protection via middleware.  
- **JWT validation & authorization:** Guards to ensure users only access their tasks; verify token signature, expiry.  
- **Error handling:** Generic error messages returned to client; no stack traces in production logs.  
- **Rate & brute-force protection:** Login/register endpoints stricter; consider IP + account throttling.  
- **Logging & monitoring:** Mask sensitive fields in logs, audit auth events.  
