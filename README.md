# Habib Data Hub

A cross-platform VTU (Virtual Top-Up) mobile application — buy airtime, data,
pay electricity bills, subscribe to cable TV, and purchase WAEC/NECO/JAMB
exam PINs — built with **Flutter** (iOS + Android) and a **Node.js/Express +
PostgreSQL** backend.

## What's in this repository

```
habib-data-hub/
├── backend/          Node.js + Express REST API, PostgreSQL database
│   ├── migrations/schema.sql      Full DB schema
│   ├── src/
│   │   ├── config/                DB connection, migration runner
│   │   ├── controllers/           Request handlers (business logic)
│   │   ├── middleware/            JWT auth, validation, error handling
│   │   ├── models/                Query layer (no heavy ORM — plain SQL via pg)
│   │   ├── routes/                Express routers
│   │   ├── services/              VTU provider, Paystack, Flutterwave, PDF, push
│   │   └── server.js
│   ├── .env.example
│   └── package.json
├── mobile/            Flutter app (Android + iOS), clean architecture, Riverpod
│   ├── lib/
│   │   ├── core/                  theme, network client, shared widgets, constants
│   │   └── features/              auth, dashboard, wallet, services (VTU), transactions,
│   │                               referral, support, profile — each with data/presentation layers
│   └── pubspec.yaml
└── docs/
    ├── API_DOCUMENTATION.md
    ├── SETUP_GUIDE.md
    └── DEPLOYMENT_GUIDE.md
```

## Feature coverage

| Feature | Status |
|---|---|
| Registration & login (JWT + refresh tokens) | ✅ Implemented |
| Wallet balance & transaction history (with search) | ✅ Implemented |
| Fund wallet — Paystack & Flutterwave | ✅ Implemented (server-verified) |
| Buy Data — MTN, Airtel, Glo, 9mobile | ✅ Implemented |
| Buy Airtime | ✅ Implemented |
| Electricity bill payment | ✅ Implemented |
| Cable TV — DSTV, GOtv, Startimes | ✅ Implemented |
| WAEC / NECO / JAMB PIN purchase | ✅ Implemented |
| Referral system with commission | ✅ Implemented (signup bonus; commission hook ready) |
| Push notifications | ✅ Wired (needs your Firebase project keys) |
| PDF transaction receipts | ✅ Implemented |
| Profile management + transaction PIN | ✅ Implemented |
| Light / dark mode | ✅ Implemented |
| WhatsApp / email support | ✅ Implemented |
| Automatic VTU order processing | ✅ Implemented (adapter pattern — plug in your provider) |
| Admin panel (users, pricing, margins, transactions) | ✅ Backend API implemented — see note below |
| Analytics dashboard (daily/weekly/monthly) | ✅ Backend API implemented — see note below |
| Encrypted sensitive data | ✅ Passwords/PINs bcrypt-hashed, JWT auth, HTTPS-ready, secure mobile token storage |

**Admin panel note:** the backend fully implements every admin capability
(`/api/admin/*` — user management, pricing/margins, transaction oversight,
sales analytics). What's not included yet is a separate admin **web**
front-end — that's a natural next build (a React/Next.js dashboard hitting
these same endpoints), since admins typically want a desktop UI rather than
the mobile app.

## Quick start

See `docs/SETUP_GUIDE.md` for full local setup, and `docs/DEPLOYMENT_GUIDE.md`
for shipping the backend and publishing to the Play Store / App Store.

```bash
# Backend
cd backend
cp .env.example .env    # fill in your real keys
npm install
npm run migrate         # applies migrations/schema.sql
npm run dev

# Mobile
cd mobile
flutter pub get
flutter run --dart-define=API_BASE_URL=http://10.0.2.2:5000/api
```

## Architecture notes

- **Backend**: plain `pg` (no ORM) for full control over SQL and easy
  auditing; all money-moving operations run inside PostgreSQL transactions
  with row-level locks (`SELECT ... FOR UPDATE`) so concurrent purchases
  can't double-spend a wallet.
- **VTU provider**: `src/services/vtuService.js` is a thin adapter. Swap its
  internals for your actual aggregator (VTpass, Clubkonnect, Nomba, etc.) —
  nothing else in the codebase needs to change.
- **Mobile**: clean architecture per feature (`data/` for repositories and
  models, `presentation/` for Riverpod providers and screens). `Dio` handles
  networking with an interceptor that attaches the JWT and silently refreshes
  it on 401s. Tokens live in `flutter_secure_storage` (Keychain/EncryptedSharedPreferences), never in plain prefs.
- **Payments**: the app opens Paystack/Flutterwave's hosted checkout in an
  in-app WebView, then the **backend** verifies the transaction server-side
  before crediting the wallet — client-side callbacks are never trusted.

## Before you publish

This is a production-grade **starter**, not a zero-touch published app. Before
submitting to the stores you still need to: plug in real VTU/Paystack/
Flutterwave/Firebase credentials, add your own app icon/splash art and legal
pages (privacy policy, terms), do a security review, and complete store
listing assets — all covered in `docs/DEPLOYMENT_GUIDE.md`.
