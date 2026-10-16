# FinTrack

A collaborative personal finance web application with an Express API, MongoDB persistence, and a vanilla JavaScript interface. Track accounts, categories, transactions, and budgets from a single application.


## What it implements

- Registration and login with bcrypt password hashing and JWT bearer authentication.
- Account, category, transaction, and budget management using Mongoose models.
- Transaction search, filtering, pagination, and dashboard summaries.
- Profile updates, password changes, and account deletion.
- PDF statement text extraction with PDF.js and transaction extraction through the OpenAI API (`gpt-4o` in the current source).

Express serves both the HTML interface and the API. Requests under `/api` pass through authentication middleware; `/register` and `/login` are public.

## Run locally

Prerequisites: Node.js 22.13+ (for the current PDF.js dependency), npm, and a running MongoDB instance. The PDF parser also needs an OpenAI API key with access to the configured model.

```bash
git clone https://github.com/mr-Mariano/fintrack.git
cd fintrack/BACKEND
npm ci
cp .env.example .env
```

Replace the example values in `BACKEND/.env`:

```dotenv
PORT=3000
MONGO_URI=mongodb://127.0.0.1:27017/fintrack
SALT_ROUNDS=10
JWT_SECRET=replace-with-a-long-random-local-secret
OPENAI_API_KEY=replace-with-your-own-api-key
```

Use `SALT_ROUNDS=10` rather than the placeholder in the supplied example. Keep `.env` private. The OpenAI key is needed when invoking PDF parsing; the other workflows use MongoDB.

Before using the local interface, change `BASE_URI` in `FRONTEND/js/env.js` to `http://localhost:3000/`. The committed value points to the hosted application and affects login redirects.

```bash
node server.js
```

Open `http://localhost:3000/` and register a local account. No separate frontend server is required. The repository's `npm start` invokes `nodemon`, which is not declared in `package.json`; the direct command above avoids that missing dependency.

## API entry points

| Path | Purpose |
| --- | --- |
| `POST /register`, `POST /login` | Create an account and authenticate |
| `/api/users/me`, `/api/users` | Current profile and profile changes |
| `/api/accounts` | Account management |
| `/api/categories` | Category management |
| `/api/transactions` | Transactions, filters, and pagination |
| `/api/budgets` | Budget management |
| `POST /api/documents/parse` | Multipart PDF upload using the `file` field |

Protected requests require `Authorization: Bearer <token>`. Consult `BACKEND/routes/` for the methods and identifiers supported by each resource.

## My contribution and collaboration

I implemented substantial parts of the application across the backend and interface: authentication flows, MongoDB/API integration for accounts, categories and transactions, dashboard behavior, budget management, profile functionality, and transaction search. Examples include commits [698d644](https://github.com/mr-Mariano/fintrack/commit/698d6445de), [ad188ba](https://github.com/mr-Mariano/fintrack/commit/ad188ba9a9), and [5286891](https://github.com/mr-Mariano/fintrack/commit/5286891875).

This was a team project. Gabocota contributed PDF/OpenAI parsing, charts, fixes, and other interface work; Enrique Milo / EnriqueM05 contributed accounts and profile interface work, document controls, transaction controls, and fixes. See the [full history](https://github.com/mr-Mariano/fintrack/commits/main/) for shared work and subsequent changes. GitHub also lists DynoQuique among repository contributors. These contributions are not represented as an individual ownership percentage.

## Scope and verification

This is a project demonstration, not a banking integration or audited financial system. PDF uploads are limited to 10 MB; extraction sends up to 12,000 characters of extracted text to OpenAI. Use synthetic statements for demonstrations and review extracted transactions before saving them. Scanned documents without extractable text are not supported by the current extraction path.

`npm test` is a placeholder that exits with an error. For a manual check, register, log in, create a category and account, add a transaction, filter it, create a budget, and update the profile. Runtime and external-provider behavior require a configured local environment.
