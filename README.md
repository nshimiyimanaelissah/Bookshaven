# CentBook — production-oriented starter

CentBook is a multi-file application foundation for the educational publishing platform discussed with the owner. It preserves the CentBook name, navy/turquoise brand direction, USD pricing and Rwanda education catalogue. The supplied screenshot is retained as a brand reference; the frontend uses a clean text/mark lockup until a clean standalone logo asset is supplied.

## Included
- Express API, PostgreSQL data model (Prisma), Argon2 password hashing, hashed opaque sessions in HttpOnly cookies, rate limiting and Helmet.
- Email verification code flow (Resend adapter; requires credentials).
- Public published-book catalogue with Rwanda education filters.
- USD price fields stored as integer cents.
- View/download purchase requests, administrator approval, 30-day view entitlement, one-download marker.
- Single-admin bootstrap script, admin-only routes for publication review, payment review, user suspension and direct access grants.
- Administrator-initiated messaging and notification persistence models/endpoints.
- Responsive CentBook web frontend and dark-mode toggle.

## Run locally
Requirements: Node.js 20+, PostgreSQL 14+.

1. Copy `.env.example` to `.env` and set `DATABASE_URL`, a random `SESSION_SECRET`, `APP_URL`, and email provider values.
2. Create an empty PostgreSQL database named `centbook` (or update the URL).
3. Run `npm install`.
4. Run `npm run db:generate` then `npm run db:migrate`.
5. Set `ADMIN_INITIAL_PASSWORD` to a unique strong password in `.env`, then run `npm run admin:create`. Remove `ADMIN_INITIAL_PASSWORD` immediately after successful creation. Only one ADMIN role is permitted by the bootstrap script; the administrator's display name defaults to Elissa and email defaults to the supplied address.
6. Run `npm run dev` and open `http://localhost:3000`.

## Important unfinished production integrations
This is a working project foundation, **not yet a production-ready commercial service**. Do not take real payments or upload customer books until the following are completed and independently tested:

1. **Merchant checkout:** the current purchase API records a payment reference for manual review. It does not initiate MTN MoMo, Airtel Money or card checkout and cannot verify provider settlement. Integrate a contracted gateway's hosted checkout and signed webhook; reconcile amount, currency, merchant reference and final settled state server-side before approval. USD display does not guarantee the Rwanda merchant account can settle USD; confirm supported currencies and conversion with the provider.
2. **Private file storage:** `pdfKey` and `coverKey` are storage object keys, not public URLs. Configure private S3-compatible storage, virus scanning, file type/size limits, signed short-lived delivery URLs and an atomic one-download claim. The current content endpoint deliberately does not expose a file URL.
3. **Publisher upload UI:** the database and admin publication review endpoint are present, but a hardened multipart upload flow and publisher dashboard still need implementing.
4. **Messaging UI:** message and notification data/API are present; inbox UI, read receipts, pagination and admin compose UI remain to be built.
5. **Security hardening:** add CSRF protection for cookie-authenticated mutations, email resend/expiry controls, account recovery, audit logs, backup/restore, monitoring, privacy/terms pages, accessibility and external penetration testing. Add production CORS origins and HTTPS-only cookies.
6. **Admin verification:** payment approval route requires `providerVerified: true` in the request, but until the gateway webhook exists this is an explicit manual operator assertion, not an automated verification. The admin must verify funds in the merchant portal first.

## Rwanda catalogue
Nursery N1–N3; Primary P1–P6; Ordinary Level S1–S3; Advanced Level S4–S6; TVET Levels 1–5; Higher Education. Advanced-level pathways are included alongside legacy combination labels (PCM, PCB, MPC, MCB, MEG, MCE) so older material can still be classified. Confirm exact curriculum naming with the relevant education authority before public catalogue launch.

## USD rules
All prices are integer cents with `currency = USD`; e.g. `$2.50` is stored as `250`. Never use floating-point arithmetic for payment totals. Provider settlement currency and exchange-rate handling must be explicitly agreed with the merchant gateway.

## Existing design reference
`legacy-reference.html` is the prior browser-only CentBook iteration, retained for visual/feature reference only. Do not deploy it as the production application; it contains client-side storage patterns that are unsuitable for public accounts and payment entitlements.
