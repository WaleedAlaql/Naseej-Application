<div align="center">

<img src="assets/images/NaseejLogo.png" width="150" alt="NaseejLogo.png">

**Neighborhood demand platform powered by AI**

منصة يطلب فيها سكان الحي المحلات والخدمات اللي تنقصهم، ويستخدم المستثمرون هذا الطلب الموثّق لفتح المشروع في المكان الصح

![Java](https://img.shields.io/badge/Java-17+-ED8B00?logo=openjdk&logoColor=white)
![Spring Boot](https://img.shields.io/badge/Spring_Boot-3-6DB33F?logo=springboot&logoColor=white)
![Spring Security](https://img.shields.io/badge/Spring_Security-JWT-6DB33F?logo=springsecurity&logoColor=white)
![MySQL](https://img.shields.io/badge/MySQL-4479A1?logo=mysql&logoColor=white)
![Claude](https://img.shields.io/badge/Claude_AI-D97757?logo=anthropic&logoColor=white)
![n8n](https://img.shields.io/badge/n8n-EA4B71?logo=n8n&logoColor=white)
![Moyasar](https://img.shields.io/badge/Moyasar-Payments-1D4ED8)
![Google Places](https://img.shields.io/badge/Google_Places-4285F4?logo=googlemaps&logoColor=white)
![WhatsApp](https://img.shields.io/badge/WhatsApp-25D366?logo=whatsapp&logoColor=white)
![Gmail](https://img.shields.io/badge/Gmail-EA4335?logo=gmail&logoColor=white)

</div>

---

Naseej lets neighborhood residents request the shops and services they're missing, and investors use that verified demand to choose the right project and location. AI screens every idea before it goes live, residents join for free as founding members, and investors unlock completed requests with a one-time fee or a success partnership, or buy a request early, before its votes complete, at a lower price. The pilot starts in Riyadh neighborhoods.

## 📑 Table of Contents

- [Features](#-features)
- [Architecture](#️-architecture)
- [Use Case Diagram](#use-case-diagram)
- [Class Diagram](#class-diagram)
- [Authentication & Roles](#-authentication--roles)
- [Project Structure](#-project-structure)
- [Getting Started](#-getting-started)
- [Web App](#-web-app)
- [Team](#-team)
- [Endpoints](#-endpoints)
- [Contact](#-contact)

## ✨ Features

- **Residents** post requests by text or voice in their own dialect, join existing requests for free as founding members, and see demand on a neighborhood map. A member counts only after email and residence verification.
- **Idea owners** get AI screening before publishing and an AI-drafted business model they can edit by chat.
- **Investors** search by neighborhood, capital or project name, read a short AI feasibility study, unlock opportunities through Moyasar or a success partnership, and sign contracts inside the platform.
- **Everyone** gets WhatsApp and email alerts when a request completes, an idea is adopted or a business opens.

## 🏗️ Architecture

<p align="center">
  <a href="assets/docs/ArchitectureDiagram.svg">
    <img src="assets/docs/ArchitectureDiagram.svg" alt="Naseej's Architecture Diagram" width="100%">
  </a>
</p>

## Use Case Diagram

<p align="center">
  <a href="assets/docs/UseCaseDiagram.svg">
    <img src="assets/docs/UseCaseDiagram.svg" alt="Naseej's Use Case Diagram" width="100%">
  </a>
</p>

## Class Diagram

<p align="center">
  <a href="assets/docs/ClassDiagram.svg">
    <img src="assets/docs/ClassDiagram.svg" alt="Naseej's Class Diagram" width="100%">
  </a>
</p>

## 🔐 Authentication & Roles

Login is stateless with JWT access tokens (15 minutes) and refresh tokens (7 days). Users sign up with Google or with email and password; email accounts verify with a 6-digit OTP (valid 5 minutes, locked after 5 wrong attempts), and the same code also allows passwordless login and password reset. A mobile number (`05` + 8 digits) is required. Investors add a commercial registration (CR, 10 digits starting with `7`) the first time they unlock an opportunity; browsing is open, and unlocking opens once an admin verifies the CR.

All endpoints start with `/api/v1`. Sign-up, login, OTP, password reset, token refresh, Google sign-in, `/public/**`, the neighborhood lookups and Moyasar's callbacks are public. Everything else is protected by role in `SecurityConfig`; the raw CRUD and AI endpoints take ids in the request body, so they are admin only.

| Role | Can |
|---|---|
| `PENDING` | Google user before choosing a membership |
| `RESIDENT` | Post ideas, vote as a founding member from a saved home address (within 8 km), follow their ideas and projects |
| `INVESTOR` | Search, unlock and adopt opportunities, propose ideas, sign contracts, post project progress |
| `ADMIN` | Manage members, verify investors' CRs, hide ideas, refund payments, run the contract deadline check |


## 📁 Project Structure

```
com.waleed.naseej
├── config       # SecurityConfig, scheduling, RestTemplate, the first admin and the demo data seeders
├── controller   # REST endpoints (24 controllers)
├── dto
│   ├── in       # Request bodies with validation
│   └── out      # Response bodies
├── entity       # JPA entities
├── enums        # Role, IdeaStatus, ContractStatus, InvestorStatus and other enums
├── exception    # Custom exceptions and GlobalExceptionHandler
├── repository   # Database access
├── security     # JwtService, JwtAuthenticationFilter, OAuth2LoginSuccessHandler
└── service      # Business logic; ClaudeService, PaymentService (Moyasar), N8nService, GooglePlacesService and OsmService call the outside services
src/main/resources
├── application.properties
└── static       # The website (see Web App)
```

## 🚀 Getting Started

1. Clone the repository: `git clone https://github.com/AmiraAldajani/Naseej.git`
2. Create a MySQL database, copy `.env.example` to `.env` in the project root and fill it in (below). The app reads `.env` on startup; real environment variables win over it.
3. Import the n8n workflows and add a Gmail OAuth2 credential from the platform's official account. Without n8n, the email code is printed in the console.
4. Run `./mvnw spring-boot:run` (Java 17). Use Moyasar's test keys and test cards during development.
5. Or run it in Docker: `docker build -t naseej .` then `docker run -p 8080:8080 --env-file .env naseej`.

<details>
<summary>Environment variables</summary>

```properties
# Database
DATABASE_URL=jdbc:mysql://localhost:3306/naseej
DATABASE_USERNAME=
DATABASE_PASSWORD=

# Security (32+ characters)
JWT_SECRET=

# Google sign-in (redirect URI: http://localhost:8080/api/v1/login/oauth2/code/google)
GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=

# n8n: OTP emails and platform events (empty = console / log only)
N8N_OTP_WEBHOOK_URL=
N8N_EVENTS_WEBHOOK_URL=
N8N_WEBHOOK_SECRET=

# First admin, created on startup
ADMIN_EMAIL=
ADMIN_PASSWORD=

# Demo data (false in production)
DEMO_DATA=true

# Moyasar
MOYASAR_API_KEY=
MOYASAR_PUBLISHABLE_KEY=
MOYASAR_WEBHOOK_SECRET=
MOYASAR_CALLBACK_URL=http://localhost:8080/api/v1/payments/moyasar-callback

# Claude
CLAUDE_API_KEY=
CLAUDE_MODEL=

# Optional: competitors from Google Places (empty = OpenStreetMap)
GOOGLE_PLACES_API_KEY=

# Optional: branded map tiles (empty = OpenStreetMap)
MAPTILER_API_KEY=
MAPTILER_STYLE=streets-v2

# Service fee (SAR, before VAT) when an investor launches their own idea; 0 closes owner launches
OWNER_LAUNCH_FEE_SAR=1500
```

</details>

<details>
<summary>Test accounts (development only)</summary>

With `DEMO_DATA=true`, the first start fills an empty database with neighborhoods, residents who voted, ideas at every stage and investors in every verification state. Every demo account's password is `Demo12345`.

| Role | Email | Password | Notes |
|---|---|---|---|
| Admin | `ADMIN_EMAIL` | `ADMIN_PASSWORD` | From `.env`, created on startup |
| Resident | `resident1@demo.naseej.sa` | `Demo12345` | Resident in النرجس |
| Investor | `investor1@demo.naseej.sa` | `Demo12345` | CR verified, budget above 700K |
| Investor | `investor2@demo.naseej.sa` | `Demo12345` | CR verified |
| Investor | `investor3@demo.naseej.sa` | `Demo12345` | CR pending: can browse, can't unlock |
| Investor | `investor4@demo.naseej.sa` | `Demo12345` | CR rejected |

</details>

## 🌐 Web App

The website lives in `src/main/resources/static` and is served by the same Spring Boot app, so there is nothing extra to install or build.

1. Start the backend (`./mvnw spring-boot:run`).
2. Open **http://localhost:8080/api/v1/** (the pages share the `/api/v1` context path with the API).
3. In Google Cloud, the authorized redirect URI stays `http://localhost:8080/api/v1/login/oauth2/code/google`. After Google sign-in the browser is sent to `oauth-callback.html` with the tokens.
4. To try unlocking with a payment, set Moyasar **test** keys in `.env`; checkout runs in Moyasar's sandbox.

| Page | Who | What it does |
|---|---|---|
| `index.html` | everyone | Landing page, live numbers, map of open ideas near the visitor (asks for location) |
| `register.html` / `verify.html` | new users | Email sign-up in steps: account type → details → (residents) home address pin → email code. Investors only create the account; their preferences come next in onboarding |
| `login.html` | everyone | Log in with email and password or with Google; reset a forgotten password with an email code |
| `oauth-callback.html` | Google sign-in | Receives the tokens after Google and sends the user on (to `choose-role.html` if they have no membership yet) |
| `choose-role.html` | Google sign-up | Step 1 membership, step 2 the resident's home address or the investor's last confirmation |
| `onboarding.html` | new investors | Three skippable questions after sign-up: budget (preset or custom range), fields (or "something else" in their own words), neighborhoods |
| `resident.html` | residents | Map around the resident's saved home with the voting radius, vote / view details (no vote counts). Residents vote from their saved addresses, never from the device location |
| `idea.html` | everyone logged in | Idea page; founder and investors also see votes and the business model. Investors get `idea-investor.js`, everyone else `idea-resident.js` |
| `propose.html` | residents, investors | Five steps: the idea → the place → why → AI check → review. A passed idea waits as a private draft until the author publishes it; similar ideas are rejected. Investors can then adopt their own idea |
| `investor.html` | investors | **For you**: the list and the map side by side. *Your fit* shows the opportunities that match the investor's preferences (ready-to-unlock first); *All* shows every opportunity with field and budget filters. Zoomed out, the map groups pins by city |
| `ask.html` | investors | **Ask AI**: a question in plain words → the filters the AI understood → matching opportunities |
| `unlock.html` | investors | One payment (Moyasar, AI-priced; lower before the votes complete) or 5% success partnership (at 100% of the votes); asks for the CR here if it's missing |
| `payment-return.html` | investors | Where Moyasar sends the browser back after checkout; confirms the payment with the server |
| `contract.html` | investors, founders | The contract after unlocking/adopting, in three steps: purchase → investor signs → active (the idea owner doesn't sign) |
| `projects.html` | investors, founders | Investors (`projects-investor.js`): two tabs, projects (status, deadline, progress) and their ideas. Residents (`projects-resident.js`): their ideas and drafts, and the projects that came from them |
| `account.html` | everyone | **Account** (from the avatar menu). Investors (`account-investor.js`): details, investment preferences, CR verification, WhatsApp alerts, language. Residents (`account-resident.js`): profile, password and up to two saved home addresses |
| `agreement.html` | investors, founders | A readable copy of a project's agreement |
| `admin.html` | admin | Dashboard charts, members, investor verification and neighborhoods |
| `styleguide.html` | developers | The design system: colors, type, buttons, forms, steps, AI blocks and notices |

Libraries load from CDNs: Leaflet 1.9.4 and Chart.js 4.4.1. Map tiles come from `GET /public/map-config`: MapTiler when `MAPTILER_API_KEY` is set, OpenStreetMap otherwise.

**One design, two experiences.** Every page shares one look (`assets/css/app.css`, `components.js`, the header with the notifications bell and the footer in `ui.js`). Investor pages keep the investor flow and wording; resident, landing, sign-up and admin pages keep theirs. Pages both roles open (`idea`, `projects`, `account`) load a small module per role: `<page>.js` picks `<page>-investor.js` or `<page>-resident.js`. Where the two sides word the same thing differently, the investor pages use an `…Inv.` copy of the key in `strings.js` (e.g. `projInv.myIdeas`).

**Language and voice.** Arabic is the default (English is one tap away and remembered). All text lives in `assets/js/strings.js`, so the wording can change without touching the pages. The investor side speaks casual Saudi dialect, gender-neutral where possible, and stays calm and plain on payment and contract screens.

**Demo data for the investor side.** In Postman, the folder **16 - Investor demo dataset** (in both collections) fills an empty database with a full investor experience: 11 neighborhoods in Riyadh, Jeddah and Al Khobar, 176 residents, 14 ideas with business models, AI checks and competitors, real resident votes (7 ideas ready to unlock, the rest still collecting), feasibility reports and three investor accounts (`investor.demo@naseej.test` verified with preferences and a draft contract, `investor.two@naseej.test` verified, `investor.new@naseej.test` brand new). Set `adminEmail` / `adminPassword` in the collection variables, then *Run folder*. Every account's password is `demoPassword` (default `Demo12345`). Running it again only adds what's missing. Unlock prices are quoted by Claude, so set `CLAUDE_API_KEY` to try the payment option.

**Investor flow.** Sign up (no CR) → onboarding questions → For you. The CR is asked for only when the investor first unlocks an opportunity (or from the account page); until an admin verifies it, browsing is open and unlocking is not. The menu has For you (with the map), Ask AI and My projects; the account opens from the avatar menu.

### Website endpoints

| Method | Path | Role |
|---|---|---|
| GET | `/public/stats`, `/public/ideas-nearby`, `/public/nearest-neighborhood` | public |
| PUT | `/auth/location` | logged in |
| GET | `/discover/map`, `/discover/idea/{id}`, `/discover/my-ideas` | resident, investor, admin |
| POST | `/discover/propose`, `/discover/retry-ai/{id}` | resident, investor |
| POST | `/discover/vote/{id}` | resident |
| GET | `/explore/cities`, `/explore/neighborhoods/{city}`, `/explore/opportunities/{neighborhoodId}` | investor |
| GET / POST | `/unlock/quote/{ideaId}`, `/unlock/pay/{ideaId}`, `/unlock/confirm-payment/{ideaId}`, `/unlock/partnership/{ideaId}`, `/unlock/adopt/{ideaId}` | investor (verified to unlock) |
| GET / POST | `/projects/mine`, `/projects/stages`, `/projects/stage/{contractId}` | resident, investor |
| GET / PUT | `/admin/stats`, `/admin/investors`, `/admin/investors/verify/{id}`, `/admin/investors/reject/{id}` | admin |

### Logic endpoints added

| Method | Path | Role | What it does |
|---|---|---|---|
| DELETE | `/discover/vote/{id}` | resident | Take a vote back while the idea is still collecting votes |
| GET | `/discover/my-votes` | resident | Ideas the resident voted for, to follow them until they open |
| PUT | `/discover/idea/{id}` | founder | Edit title/description of a published idea (title locked once someone voted) |
| DELETE | `/discover/idea/{id}` | founder | Withdraw a published idea or delete a rejected one |
| PUT | `/discover/idea/{id}/business-model/chat` | founder | Change the business model in your own words (Claude rewrites it) |
| PUT | `/auth/me` | logged in | Edit profile; a new CR number sends an investor back to admin review. Investors also send `investmentBudget` (a preset or `CUSTOM` with `budgetMin` / `budgetMax`), `investmentFields`, `investmentFieldsOther` (own words; the AI matches it to idea categories) and `investmentAreas` (neighborhood ids). An empty string clears a field |
| POST | `/explore/ask` | investor | `{ "question": "…" }` → the filters the AI read (city, neighborhood, category, capital, ready to open) and the matching opportunities |
| POST | `/projects/{contractId}/sign-otp` | investor | Email the 6-digit signing code to the investor |
| POST | `/projects/{contractId}/sign` | investor | Sign: typed full name + drawn signature + code; the contract becomes active right away (the idea owner doesn't sign) |
| GET | `/projects/{contractId}/contract` | investor, founder | Print-ready contract page (Arabic); "Save as PDF" in the browser |
| GET | `/payments/mine` | investor | Own payments and invoices |
| POST | `/subscriptions/subscribe` | investor | Start a BASIC (299) or PRO (999) monthly subscription through Moyasar |
| GET | `/subscriptions/mine` | investor | Own subscriptions (checks pending payments with Moyasar) |
| PUT | `/subscriptions/cancel` | investor | Cancel; a paid month keeps working until its end date |
| GET | `/explore/search` | investor | Search by words, city, category and `maxCapital` (from the business model) |
| GET | `/explore/rising` | investor | Requests that gained the most votes in the last `days` (default 7) |
| PUT | `/admin/users/{id}/disable` | admin | Disable an account (`?disabled=false` re-enables it) |
| PUT | `/admin/ideas/{id}/hide` | admin | Hide an idea from the map and lists (`?hidden=false` restores it) |
| POST | `/admin/payments/{id}/refund` | admin | Refund through Moyasar's Refund API and undo what the payment unlocked |
| POST | `/admin/contracts/expire-overdue` | admin | Run the deadline check now: 14 days to sign, 180 days to open (it also runs every night) |
| GET / PUT | `/account/profile` | logged in | Own profile (name, phone, language; investors also company, CR, budget range and fields) |
| GET / POST / PUT / DELETE | `/account/addresses`, `/account/addresses/{id}`, `/account/addresses/{id}/primary`, `/account/addresses/preview` | logged in | Saved home addresses (residents vote from them; up to two, one change every 30 days). `preview` shows what a change would do to the votes before saving |
| GET / PUT | `/notifications`, `/notifications/unread-count`, `/notifications/read-all`, `/notifications/{id}/read` | logged in | The bell in the header: idea adopted, vote target reached, project stage |
| PUT / DELETE | `/discover/draft/{id}` | founder | Edit or drop a draft before publishing |
| POST | `/discover/publish/{id}` | founder | Publish a draft that passed the AI check (the check must be under a day old) |
| GET | `/public/search-places` | public | Address search for the home pin and the propose map (OpenStreetMap) |
| GET / POST | `/explore/top`, `/explore/match`, `/explore/ai-search` | investor | Strongest opportunities, matches by city / neighborhood / budget range, and the AI search with a reason per result |
| GET / POST | `/unlock/checkout/{ideaId}`, `/unlock/owner-terms/{ideaId}`, `/unlock/owner-checkout/{ideaId}`, `/unlock/owner-confirm/{ideaId}` | investor | Moyasar payment form checkout, and the paid launch of the investor's own idea (service fee `OWNER_LAUNCH_FEE_SAR`). The investor pages use `/unlock/pay` and `/unlock/adopt` |
| GET | `/unlock/agreement/{contractId}` | investor, founder, admin | The agreement behind a project |
| GET / POST / PUT / DELETE | `/admin/users`, `/admin/users/{id}`, `/admin/users/{id}/password`, `/admin/users/{id}/suspend`, `/admin/users/{id}/reactivate` | admin | Member management |

**Contract flow after adoption:** unlocking or adopting creates a `DRAFT` contract → the investor signs → `ACTIVE` right away, and the 180-day opening deadline starts. The idea owner doesn't sign and gets no discount; they're notified when the contract is active. The investor must sign within 14 days of the purchase. Once, in the last 30 days before the opening deadline, an investor who posted progress in the last 30 days can extend it by 60 days (`POST /projects/{contractId}/extend`); progress can also be posted on the current stage with a note. An investor who adopts their own idea signs the same way. A contract that isn't signed or doesn't open by its deadline is terminated and the idea goes back to the other investors (`COMPLETED`) or back to voting (`PUBLISHED`).

The raw CRUD and AI endpoints (`/ideas/**`, `/payments/**`, `/contracts/**`, …) take user ids in the request body, so they are now **admin only**; the website uses the endpoints above, which read the user from the token.

## 👥 Team

<table>
  <tr>
    <td align="center" width="33%">
      <a href="https://github.com/AHMEDALHAMAMI"><img src="https://github.com/AHMEDALHAMAMI.png" width="100" alt="Member"><br><b>Ahmad Alhamami</b></a><br>
      <sub><img src="https://cdn.simpleicons.org/anthropic" width="16"> AI feature</sub><br><br>
      User, Idea, FoundingMember, Subscription, Neighborhood<br><br>
      <b>66</b> extra endpoints
    </td>
    <td align="center" width="33%">
      <a href="https://github.com/WaleedAlaql"><img src="https://github.com/WaleedAlaql.png" width="100" alt="Member"><br><b>Waleed Alaql</b></a><br>
      <sub><img src="https://cdn.simpleicons.org/anthropic" width="16"> AI feature</sub><br><br>
      Contract, Payment, PartnershipAgreement, ContractSignature, ProjectUpdate<br><br>
      <b>65</b> extra endpoints
    </td>
    <td align="center" width="33%">
      <a href="https://github.com/AmiraAldajani"><img src="https://github.com/AmiraAldajani.png" width="100" alt="Member"><br><b>Amira Aldajani</b></a><br>
      <sub><img src="https://cdn.simpleicons.org/anthropic" width="16"> AI feature</sub><br><br>
      IdeaCheck, BusinessModel, OpportunityAccess, FeasibilityReport, NearbyPlace<br><br>
      <b>66</b> extra endpoints
    </td>
  </tr>
</table>

## 🧩 Endpoints

All paths start with `/api/v1`, e.g. `http://localhost:8080/api/v1/auth/login`. Send the access token as `Authorization: Bearer <token>`. <img src="https://cdn.simpleicons.org/anthropic" width="16" alt="AI"> marks endpoints that call Claude.

<details open>
<summary>&nbsp;<b>198 Endpoints</b></summary>

#### Auth (13)

<sub>Sign-up, login, OTP, password reset, refresh and Google sign-in are public; the rest need a token (a `PENDING` Google user can call them).</sub>

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/auth/register` | Sign up with email and password (name, mobile, membership type; residents also pin their home address); sends a 6-digit email code |
| `POST` | `/auth/verify-otp` | Verify the 6-digit email code and get access and refresh tokens |
| `POST` | `/auth/send-otp` | Send a new code: resend for sign-up, passwordless login, or password reset |
| `POST` | `/auth/login` | Log in and get access and refresh tokens |
| `POST` | `/auth/reset-password` | Set a new password with the emailed code |
| `POST` | `/auth/refresh` | Get a new access token from the refresh token |
| `PUT` | `/auth/select-role` | Choose resident or investor after Google sign-in |
| `GET` | `/auth/me` | The logged-in user |
| `PUT` | `/auth/me` | Edit profile; investors also set budget, fields (own words matched to categories by AI) and neighborhoods. A new CR sends the investor back to admin review |
| `PUT` | `/auth/location` | Save the browser's location (non-residents; residents vote from their saved addresses) |
| `POST` | `/auth/logout` | Log out (ends the refresh token) |
| `GET` | `/oauth2/authorization/google` | Start Google sign-in |
| `GET` | `/login/oauth2/code/google` | Google's redirect back; sends the browser to `oauth-callback.html` with the tokens |

#### Account (8)

<sub>Resident, investor or admin — always the logged-in user's own account.</sub>

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/account/profile` | Own profile (name, phone, language; investors also company, CR, budget range and fields) |
| `PUT` | `/account/profile` | Edit own profile |
| `GET` | `/account/addresses` | Saved home addresses (up to two) |
| `POST` | `/account/addresses` | Add a home address |
| `PUT` | `/account/addresses/{id}` | Change an address (one change every 30 days) |
| `PUT` | `/account/addresses/{id}/primary` | Make an address the primary one |
| `DELETE` | `/account/addresses/{id}` | Remove an address |
| `POST` | `/account/addresses/preview` | What a change would do (votes withdrawn, monthly change used) before saving it |

#### Public (5)

<sub>No login needed.</sub>

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/public/stats` | Live numbers for the landing page |
| `GET` | `/public/ideas-nearby` | Open ideas around a point (`lat`, `lng`, optional `radiusKm`) |
| `GET` | `/public/nearest-neighborhood` | The neighborhood closest to a point, for sign-up |
| `GET` | `/public/search-places` | Address search for the home pin and the propose map (OpenStreetMap) |
| `GET` | `/public/map-config` | Map tile settings for the website (MapTiler with a key, OpenStreetMap without) |

#### Notifications (4)

<sub>Resident, investor or admin — only their own notifications.</sub>

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/notifications` | Own notifications (the bell in the header) |
| `GET` | `/notifications/unread-count` | Number of unread notifications |
| `PUT` | `/notifications/read-all` | Mark all as read |
| `PUT` | `/notifications/{id}/read` | Mark one as read |

#### Discover (14)

<sub>Resident, investor or admin; voting is residents only.</sub>

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/discover/map` | Ideas on the map around the user (a saved address for residents) |
| `GET` | `/discover/idea/{id}` | Idea page; the founder and investors also see votes and the business model |
| `GET` | `/discover/my-ideas` | The user's own ideas and drafts |
| `POST` | `/discover/propose` | <img src="https://cdn.simpleicons.org/anthropic" width="14" alt="AI"> Propose an idea; the AI check runs and a passed idea waits as a private draft |
| `PUT` | `/discover/draft/{id}` | <img src="https://cdn.simpleicons.org/anthropic" width="14" alt="AI"> Edit and recheck a draft or an idea that didn't pass |
| `POST` | `/discover/publish/{id}` | <img src="https://cdn.simpleicons.org/anthropic" width="14" alt="AI"> Publish a draft that passed the AI check (under a day old); drafts the business model |
| `DELETE` | `/discover/draft/{id}` | Drop a draft before publishing |
| `POST` | `/discover/retry-ai/{id}` | <img src="https://cdn.simpleicons.org/anthropic" width="14" alt="AI"> Run the AI check again on an idea |
| `POST` | `/discover/vote/{id}` | Vote as a founding member (from a saved address within 8 km) |
| `DELETE` | `/discover/vote/{id}` | Take a vote back while the idea is still collecting votes |
| `GET` | `/discover/my-votes` | Ideas the resident voted for, to follow them until they open |
| `PUT` | `/discover/idea/{id}` | Founder edits a published idea (title locked once someone voted) |
| `DELETE` | `/discover/idea/{id}` | Founder withdraws a published idea or deletes a rejected one |
| `PUT` | `/discover/idea/{id}/business-model/chat` | <img src="https://cdn.simpleicons.org/anthropic" width="14" alt="AI"> Founder changes the business model in their own words |

#### Explore (9)

<sub>Investors.</sub>

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/explore/ask` | <img src="https://cdn.simpleicons.org/anthropic" width="14" alt="AI"> A question in plain words → the filters the AI read and the matching opportunities |
| `GET` | `/explore/cities` | Cities with opportunity counts |
| `GET` | `/explore/neighborhoods/{city}` | Neighborhoods in a city with their numbers |
| `GET` | `/explore/opportunities/{neighborhoodId}` | Opportunities in a neighborhood |
| `GET` | `/explore/search` | Search by words, city, category and `maxCapital` |
| `GET` | `/explore/rising` | Requests that gained the most votes in the last `days` (default 7) |
| `GET` | `/explore/top` | Strongest opportunities |
| `GET` | `/explore/match` | Matches by city, neighborhood and budget range |
| `POST` | `/explore/ai-search` | <img src="https://cdn.simpleicons.org/anthropic" width="14" alt="AI"> AI search with a reason for each result |

#### Unlock (10)

<sub>Investors (unlocking needs a verified CR); the agreement is also readable by the founder and admins.</sub>

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/unlock/quote/{ideaId}` | <img src="https://cdn.simpleicons.org/anthropic" width="14" alt="AI"> AI-priced unlock quote (lower before the votes complete) |
| `POST` | `/unlock/pay/{ideaId}` | Start the one-time unlock payment with Moyasar |
| `POST` | `/unlock/checkout/{ideaId}` | Open Moyasar's payment form for the current price |
| `POST` | `/unlock/confirm-payment/{ideaId}` | Confirm the Moyasar payment and unlock the idea (creates the draft contract) |
| `POST` | `/unlock/partnership/{ideaId}` | Unlock through a 5% success partnership (at 100% of the votes), signed with the typed full name |
| `POST` | `/unlock/adopt/{ideaId}` | Adopt the investor's own idea with no fee |
| `GET` | `/unlock/owner-terms/{ideaId}` | Service agreement terms for launching the investor's own idea |
| `POST` | `/unlock/owner-checkout/{ideaId}` | Sign the service agreement and open Moyasar's form for the launch fee |
| `POST` | `/unlock/owner-confirm/{ideaId}` | Confirm the launch fee with Moyasar and start the project |
| `GET` | `/unlock/agreement/{contractId}` | Readable copy of a project's agreement |

#### Projects (7)

<sub>Investors and founders of their own projects.</sub>

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/projects/mine` | My projects: status, deadline and progress |
| `GET` | `/projects/stages` | The project stages, in order |
| `POST` | `/projects/stage/{contractId}` | Investor posts progress on the current stage with a note |
| `POST` | `/projects/{contractId}/sign-otp` | Email the 6-digit signing code to the investor |
| `POST` | `/projects/{contractId}/sign` | Sign with typed full name, drawn signature and code; the contract is active right away |
| `POST` | `/projects/{contractId}/extend` | The one 60-day extension of the opening deadline (last 30 days, with progress in the last 30 days) |
| `GET` | `/projects/{contractId}/contract` | Print-ready contract page in Arabic ("Save as PDF" in the browser) |

#### Admin (15)

<sub>Admin.</sub>

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/admin/stats` | Dashboard numbers and charts |
| `GET` | `/admin/users` | All members |
| `POST` | `/admin/users` | Add a member (email verified and active) |
| `PUT` | `/admin/users/{id}` | Edit a member |
| `PUT` | `/admin/users/{id}/suspend` | Suspend a member |
| `PUT` | `/admin/users/{id}/reactivate` | Reactivate a suspended member |
| `PUT` | `/admin/users/{id}/password` | Set a new password for a member (ends their sessions) |
| `DELETE` | `/admin/users/{id}` | Delete a member with their addresses and votes |
| `GET` | `/admin/investors` | Investors by CR verification status (`?status=`, default `PENDING`) |
| `PUT` | `/admin/investors/verify/{id}` | Verify an investor's CR |
| `PUT` | `/admin/investors/reject/{id}` | Reject an investor's CR |
| `PUT` | `/admin/users/{id}/disable` | Disable an account (`?disabled=false` re-enables it) |
| `PUT` | `/admin/ideas/{id}/hide` | Hide an idea from the map and lists (`?hidden=false` restores it) |
| `POST` | `/admin/payments/{id}/refund` | Refund through Moyasar's Refund API and undo what the payment unlocked |
| `POST` | `/admin/contracts/expire-overdue` | Run the deadline check now: 14 days to sign, 180 days to open (also runs every night) |

#### Users (5)

<sub>Admin.</sub>

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/users/get-all` | All users |
| `POST` | `/users/create` | Create a user |
| `PUT` | `/users/update/{id}` | Update a user |
| `DELETE` | `/users/delete/{id}` | Delete a user |
| `GET` | `/users/get/{id}` | Get a user by id |

#### Neighborhoods (7)

<sub>Lookups (`GET`) are public for the sign-up page; changes are admin only.</sub>

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/neighborhoods/get-all` | All neighborhoods |
| `POST` | `/neighborhoods/create` | Create a neighborhood |
| `PUT` | `/neighborhoods/update/{id}` | Update a neighborhood |
| `DELETE` | `/neighborhoods/delete/{id}` | Delete a neighborhood |
| `GET` | `/neighborhoods/get/{id}` | Get a neighborhood by id |
| `GET` | `/neighborhoods/get-by-city/{city}` | Neighborhoods in a city |
| `GET` | `/neighborhoods/search/{keyword}` | Search neighborhoods by name |

#### Ideas (5)

<sub>Admin.</sub>

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/ideas/get-all` | All ideas |
| `POST` | `/ideas/create` | Create a idea |
| `PUT` | `/ideas/update/{id}` | Update a idea |
| `DELETE` | `/ideas/delete/{id}` | Delete a idea |
| `GET` | `/ideas/get/{id}` | Get a idea by id |

#### Founding Members (5)

<sub>Admin.</sub>

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/founding-members/get-all` | All founding members |
| `POST` | `/founding-members/create` | Create a founding member |
| `PUT` | `/founding-members/update/{id}` | Update a founding member |
| `DELETE` | `/founding-members/delete/{id}` | Delete a founding member |
| `GET` | `/founding-members/get/{id}` | Get a founding member by id |

#### Idea Checks (8)

<sub>Admin.</sub>

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/idea-checks/get-all` | All idea checks |
| `POST` | `/idea-checks/create` | Create a idea check |
| `PUT` | `/idea-checks/update/{id}` | Update a idea check |
| `DELETE` | `/idea-checks/delete/{id}` | Delete a idea check |
| `GET` | `/idea-checks/get/{id}` | Get a idea check by id |
| `POST` | `/idea-checks/run/{ideaId}` | <img src="https://cdn.simpleicons.org/anthropic" width="14" alt="AI"> Run the AI screening on an idea |
| `GET` | `/idea-checks/get-by-idea/{ideaId}` | Screening result of an idea |
| `GET` | `/idea-checks/get-by-decision/{decision}` | Checks by decision (`SUITABLE`, `SIMILAR_EXISTS`, `SATURATED`) |

#### Business Models (9)

<sub>Admin.</sub>

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/business-models/get-all` | All business models |
| `POST` | `/business-models/create` | Create a business model |
| `PUT` | `/business-models/update/{id}` | Update a business model |
| `PUT` | `/business-models/revise-by-chat/{id}` | <img src="https://cdn.simpleicons.org/anthropic" width="14" alt="AI"> Revise a business model from a chat message |
| `DELETE` | `/business-models/delete/{id}` | Delete a business model |
| `GET` | `/business-models/get/{id}` | Get a business model by id |
| `POST` | `/business-models/generate/{ideaId}` | <img src="https://cdn.simpleicons.org/anthropic" width="14" alt="AI"> Generate the business model for an idea |
| `GET` | `/business-models/get-by-idea/{ideaId}` | Business model of an idea |
| `GET` | `/business-models/search-by-capital` | Business models within a maximum capital (`?max=`) |

#### Feasibility Reports (8)

<sub>Admin.</sub>

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/feasibility-reports/get-all` | All feasibility reports |
| `POST` | `/feasibility-reports/create` | Create a feasibility report |
| `PUT` | `/feasibility-reports/update/{id}` | Update a feasibility report |
| `DELETE` | `/feasibility-reports/delete/{id}` | Delete a feasibility report |
| `GET` | `/feasibility-reports/get/{id}` | Get a feasibility report by id |
| `POST` | `/feasibility-reports/generate/{ideaId}` | <img src="https://cdn.simpleicons.org/anthropic" width="14" alt="AI"> Generate the feasibility study for an idea |
| `GET` | `/feasibility-reports/preview/{ideaId}` | Short feasibility preview before unlocking |
| `GET` | `/feasibility-reports/get-full/{ideaId}` | Full report for an investor who unlocked the idea (`?investorId=`) |

#### Nearby Places (8)

<sub>Admin.</sub>

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/nearby-places/get-all` | All nearby places |
| `POST` | `/nearby-places/create` | Create a nearby place |
| `PUT` | `/nearby-places/update/{id}` | Update a nearby place |
| `DELETE` | `/nearby-places/delete/{id}` | Delete a nearby place |
| `GET` | `/nearby-places/get/{id}` | Get a nearby place by id |
| `GET` | `/nearby-places/get-by-idea/{ideaId}` | Competitors found near an idea |
| `GET` | `/nearby-places/count-by-idea/{ideaId}` | Count competitors within a radius (`?radiusKm=`, default 2) |
| `POST` | `/nearby-places/refresh/{ideaId}` | Refresh competitors from Google Places (OpenStreetMap without a key) |

#### Opportunity Access (9)

<sub>Admin.</sub>

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/opportunity-access/get-all` | All access records |
| `POST` | `/opportunity-access/create` | Create a access record |
| `PUT` | `/opportunity-access/update/{id}` | Update a access record |
| `DELETE` | `/opportunity-access/delete/{id}` | Delete a access record |
| `GET` | `/opportunity-access/get/{id}` | Get a access record by id |
| `POST` | `/opportunity-access/unlock` | Grant an investor access to an idea (paid or partnership) |
| `GET` | `/opportunity-access/check` | Whether an investor has access to an idea (`?investorId=&ideaId=`) |
| `GET` | `/opportunity-access/get-by-investor/{investorId}` | Opportunities an investor unlocked |
| `GET` | `/opportunity-access/count-by-idea/{ideaId}` | How many investors unlocked an idea |

#### Contracts (9)

<sub>Admin.</sub>

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/contracts/get-all` | All contracts |
| `POST` | `/contracts/create` | Create a contract |
| `PUT` | `/contracts/update/{id}` | Update a contract |
| `DELETE` | `/contracts/delete/{id}` | Delete a contract |
| `GET` | `/contracts/get/{id}` | Get a contract by id |
| `POST` | `/contracts/draft` | Draft a contract for an unlocked idea |
| `POST` | `/contracts/sign/{id}` | Sign a contract |
| `POST` | `/contracts/terminate/{id}` | Terminate a contract |
| `POST` | `/contracts/add-update/{id}` | Add a project update (stage and note) to a contract |

#### Contract Signatures (7)

<sub>Admin.</sub>

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/contract-signatures/get-all` | All contract signatures |
| `POST` | `/contract-signatures/create` | Create a contract signature |
| `PUT` | `/contract-signatures/update/{id}` | Update a contract signature |
| `DELETE` | `/contract-signatures/delete/{id}` | Delete a contract signature |
| `GET` | `/contract-signatures/get/{id}` | Get a contract signature by id |
| `POST` | `/contract-signatures/sign/{contractId}` | Record a signature on a contract |
| `GET` | `/contract-signatures/check/contract/{contractId}` | A contract's signing status |

#### Partnership Agreements (7)

<sub>Admin.</sub>

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/partnership-agreements/get-all` | All partnership agreements |
| `POST` | `/partnership-agreements/create` | Create a partnership agreement |
| `PUT` | `/partnership-agreements/update/{id}` | Update a partnership agreement |
| `DELETE` | `/partnership-agreements/delete/{id}` | Delete a partnership agreement |
| `GET` | `/partnership-agreements/get/{id}` | Get a partnership agreement by id |
| `POST` | `/partnership-agreements/accept-terms/{id}` | Accept the partnership terms with a typed signature |
| `GET` | `/partnership-agreements/check/access/{accessId}` | Whether the terms of an access were accepted |

#### Payments (13)

<sub>Admin, except `/payments/mine` (investor) and the two Moyasar routes (`webhook/moyasar`, `moyasar-callback`), which Moyasar calls without a token.</sub>

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/payments/get-all` | All payments |
| `POST` | `/payments/create` | Create a payment |
| `PUT` | `/payments/update/{id}` | Update a payment |
| `DELETE` | `/payments/delete/{id}` | Delete a payment |
| `GET` | `/payments/get/{id}` | Get a payment by id |
| `GET` | `/payments/get-status/{id}` | A payment's status from Moyasar (Moyasar payment id) |
| `POST` | `/payments/confirm/{id}` | Mark a payment as paid |
| `POST` | `/payments/refund/{id}` | Mark a payment as refunded |
| `POST` | `/payments/webhook/moyasar` | Moyasar webhook (checks `X-Webhook-Secret`) |
| `POST` | `/payments/initiate-moyasar` | Create a Moyasar payment |
| `POST` | `/payments/sync-moyasar/{id}` | Sync a payment's status with Moyasar |
| `GET` | `/payments/moyasar-callback` | Where Moyasar returns the browser after 3-D Secure |
| `GET` | `/payments/mine` | Investor's own payments and invoices |

#### Project Updates (5)

<sub>Admin.</sub>

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/project-updates/get-all` | All project updates |
| `POST` | `/project-updates/create` | Create a project update |
| `PUT` | `/project-updates/update/{id}` | Update a project update |
| `DELETE` | `/project-updates/delete/{id}` | Delete a project update |
| `GET` | `/project-updates/get/{id}` | Get a project update by id |

#### Subscriptions (8)

<sub>Admin, except `subscribe`, `mine` and `cancel` (investor).</sub>

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/subscriptions/get-all` | All subscriptions |
| `POST` | `/subscriptions/create` | Create a subscription |
| `PUT` | `/subscriptions/update/{id}` | Update a subscription |
| `DELETE` | `/subscriptions/delete/{id}` | Delete a subscription |
| `GET` | `/subscriptions/get/{id}` | Get a subscription by id |
| `POST` | `/subscriptions/subscribe` | Start a BASIC (299 SAR) or PRO (999 SAR) monthly subscription through Moyasar |
| `GET` | `/subscriptions/mine` | Investor's own subscriptions (checks pending payments with Moyasar) |
| `PUT` | `/subscriptions/cancel` | Cancel; a paid month keeps working until its end date |

</details>

## 📬 Contact

<table>
  <tr>
    <td align="center" width="33%">
      <a href="https://www.linkedin.com/in/ahmedalhamami"><img src="https://github.com/AHMEDALHAMAMI.png" width="100" alt="Ahmad Alhamami"><br><b>Ahmad Alhamami</b></a><br><br>
      <a href="https://www.linkedin.com/in/ahmedalhamami"><img src="https://img.shields.io/badge/LinkedIn-0A66C2?style=flat&logo=linkedin&logoColor=white" alt="LinkedIn"></a>
    </td>
    <td align="center" width="33%">
      <a href="https://www.linkedin.com/in/waleed-alaql"><img src="https://github.com/WaleedAlaql.png" width="100" alt="Waleed Alaql"><br><b>Waleed Alaql</b></a><br><br>
      <a href="https://www.linkedin.com/in/waleed-alaql"><img src="https://img.shields.io/badge/LinkedIn-0A66C2?style=flat&logo=linkedin&logoColor=white" alt="LinkedIn"></a>
    </td>
    <td align="center" width="33%">
      <a href="https://www.linkedin.com/in/amira-aldajani-2b0296222/"><img src="https://github.com/AmiraAldajani.png" width="100" alt="Amira Aldajani"><br><b>Amira Aldajani</b></a><br><br>
      <a href="https://www.linkedin.com/in/amira-aldajani-2b0296222/"><img src="https://img.shields.io/badge/LinkedIn-0A66C2?style=flat&logo=linkedin&logoColor=white" alt="LinkedIn"></a>
    </td>
  </tr>
</table>
