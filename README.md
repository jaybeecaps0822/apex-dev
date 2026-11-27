# APEX

**Standardized Physical Therapy Examination & Documentation**  
San Pedro College · Physical Therapy Department

APEX is a responsive, installable clinical documentation app for PT students and clinical instructors. This repository is an early build scaffold: the current working slice includes authentication, patient records, SOAP section drafts/review, tasks, notifications, and instructor student management. It is not yet a complete reproduction of every field and screen in the source kit and is not approved for real patient data or clinical deployment.

## Source-of-truth rules

- `APEX PROGRAMMER KIT.pdf` defines workflow, clinical content, fields, states, roles, and the prohibition on system-generated clinical content.
- `Photos.pdf` defines appearance: palette, layout, navigation, cards, tables, and branding.
- Where they conflict, the kit wins on **what** and the Canva designs win on **how**. Decisions and scope are recorded in `../BLUEPRINT_ANALYSIS_AND_DEVELOPMENT_PLAN.md`.

## Stack

- npm workspaces monorepo
- React 18 + TypeScript + Vite + Tailwind + TanStack Query
- Express + TypeScript + Prisma + PostgreSQL
- Shared enums and Zod input schemas in `packages/shared`
- JWT access token (15 minutes) + rotating refresh token in HttpOnly cookie
- PWA shell; Android native packaging (Capacitor) remains a later packaging stage

## Requirements

- Node.js 20+
- PostgreSQL 17 (Postgres 16+ should work)
- npm 10+

## Local development

1. Create `apps/api/.env` from `apps/api/.env.example` and set a long random JWT access/refresh secret. Use `EMAIL_DEV_MODE=console` for local development.
2. Create a PostgreSQL database and user; update `DATABASE_URL`.
3. Install dependencies and generate the client:
   ```sh
   npm install
   npx prisma generate --schema apps/api/prisma/schema.prisma
   ```
4. Apply migrations and optionally load demo data:
   ```sh
   npm run db:migrate
   npm run db:seed -w apps/api
   ```
5. Start the API and Vite app:
   ```sh
   npm run dev
   ```
   Web: <http://localhost:5173> · API health: <http://localhost:4000/api/health>

### Demo accounts (development only)

All use `Apex2026!`:

- Student: `aj.reyes@sanpedro.edu.ph`
- Clinical Instructor: `instructor@sanpedro.edu.ph`
- Other demo students: `karla.gomez@sanpedro.edu.ph`, `mark.deleon@sanpedro.edu.ph`, `pearl.dinopol@sanpedro.edu.ph`

The seed script creates fictitious demo records. Do not use real patient information in development. To destructively re-seed: `SEED_WIPE=1 npm run db:seed -w apps/api`.

## Useful commands

- `npm run dev` — API + web dev servers
- `npm run lint` — TypeScript checks
- `npm run build` — production builds
- `npm run db:migrate` — create/apply local Prisma migration
- `npm run db:validate` — validate the Prisma schema using `apps/api/.env`
- `npm run db:deploy -w apps/api` — apply checked-in migrations
- `npm run db:seed -w apps/api` — seed development data
- `npm test` — test workspaces

## Security and privacy

- Never commit `.env`, production credentials, or secrets.
- Production must use HTTPS, secure cookie settings, managed backups, and a restricted institutional email domain.
- Configure CORS/APP origins explicitly. Set strong, independently generated JWT secrets.
- Clinical records are sensitive personal information. Before any real use, obtain San Pedro College approval, complete a privacy impact assessment, retention/access policy, backup/restore test, incident plan, and RA 10173 review with the institution's privacy officer. This scaffold is not yet certified or production-hardened.
- Clinical text is user-authored. APEX must not generate diagnoses, prognosis, goals, interventions, or other clinical conclusions.

## Project layout

```text
apps/api/       Express API, Prisma schema/migrations, demo seed
apps/web/       React application, PWA shell, responsive UI
packages/shared Shared enums, clinical legends, and Zod schemas
docs/           stage reports and implementation notes
```
