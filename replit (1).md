# Workspace

## Overview

pnpm workspace monorepo using TypeScript. Each package manages its own dependencies.

## Stack

- **Monorepo tool**: pnpm workspaces
- **Node.js version**: 24
- **Package manager**: pnpm
- **TypeScript version**: 5.9
- **API framework**: Express 5
- **Database**: PostgreSQL + Drizzle ORM
- **Validation**: Zod (`zod/v4`), `drizzle-zod`
- **API codegen**: Orval (from OpenAPI spec)
- **Build**: esbuild (CJS bundle)

## Structure

```text
artifacts-monorepo/
├── artifacts/              # Deployable applications
│   ├── api-server/         # Express API server
│   └── esus-pec/           # e-SUS PEC Support Management web app (React + Vite)
├── lib/                    # Shared libraries
│   ├── api-spec/           # OpenAPI spec + Orval codegen config
│   ├── api-client-react/   # Generated React Query hooks
│   ├── api-zod/            # Generated Zod schemas from OpenAPI
│   └── db/                 # Drizzle ORM schema + DB connection
├── scripts/                # Utility scripts (single workspace package)
│   └── src/                # Individual .ts scripts
├── pnpm-workspace.yaml     # pnpm workspace
├── tsconfig.base.json      # Shared TS options
├── tsconfig.json           # Root TS project references
└── package.json            # Root package with hoisted devDeps
```

## Application: e-SUS PEC Gestão de Suporte

A professional support management platform for e-SUS PEC healthcare software, built for PluralMed.

### Features
- Dashboard with KPI cards (total today, total month, evolution vs previous month)
- Bar chart: atendimentos per municipality
- Pie chart: atendimentos by priority (baixa/media/alta)
- Line chart: daily evolution
- Register atendimentos modal (quick form with municipality, date, description, priority)
- History page with filters and export (PDF/CSV)
- View-only mode via `?mode=view` URL parameter
- Real-time data refresh via React Query
- PluralMed brand identity (teal/green color scheme with logo)

### Municipalities
- Ubajara, Tianguá, Jijoca de Jericoacoara, Marco

### API Endpoints
- `GET /api/atendimentos` - List with filters (municipio, prioridade, dataInicio, dataFim, mes, ano)
- `POST /api/atendimentos` - Create new atendimento
- `GET /api/atendimentos/:id` - Get single atendimento
- `DELETE /api/atendimentos/:id` - Delete atendimento
- `GET /api/dashboard/stats` - Dashboard statistics (mes, ano params)
- `GET /api/dashboard/daily` - Daily count evolution (mes, ano params)

### Database Schema
- `atendimentos` table: id, municipio, data, descricao, prioridade, created_at

## TypeScript & Composite Projects

Every package extends `tsconfig.base.json` which sets `composite: true`. The root `tsconfig.json` lists all packages as project references.

## Root Scripts

- `pnpm run build` — runs `typecheck` first, then recursively runs `build` in all packages
- `pnpm run typecheck` — runs `tsc --build --emitDeclarationOnly` using project references

## Packages

### `artifacts/api-server` (`@workspace/api-server`)

Express 5 API server. Routes in `src/routes/` use `@workspace/api-zod` for validation and `@workspace/db` for persistence.

### `artifacts/esus-pec` (`@workspace/esus-pec`)

React + Vite web app for e-SUS PEC support management. Uses Recharts for charts, Framer Motion for animations, and Tailwind CSS for styling.

### `lib/db` (`@workspace/db`)

Database layer using Drizzle ORM with PostgreSQL. Contains `atendimentos` table schema.

### `lib/api-spec` (`@workspace/api-spec`)

OpenAPI 3.1 spec + Orval codegen config. Run codegen: `pnpm --filter @workspace/api-spec run codegen`

### `lib/api-zod` (`@workspace/api-zod`)

Generated Zod schemas from the OpenAPI spec.

### `lib/api-client-react` (`@workspace/api-client-react`)

Generated React Query hooks from the OpenAPI spec.
