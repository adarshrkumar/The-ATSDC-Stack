# ATSDC Stack Application

This is the main Astro application for the ATSDC Stack.

## 🚀 Quick Start

### Prerequisites

- Node.js >= 18.0.0
- PostgreSQL database (Vercel Postgres, Neon, or local)
- API keys for BetterAuth, OpenAI, and optionally Exa

### Installation

```bash
# Install dependencies
npm install

# Copy environment template
cp .env.example .env

# Configure your .env file with your credentials
```

### Environment Variables

Create a `.env` file with the following variables:

```env
# Database
DATABASE_URL="postgresql://user:password@localhost:5432/dbname"

# BetterAuth
BETTER_AUTH_SECRET="your-secret-key-min-32-chars"
BETTER_AUTH_URL="http://localhost:4321"

# OpenAI (for Vercel AI SDK)
OPENAI_API_KEY="sk-..."

# Exa Search (optional)
EXA_API_KEY="..."
```

### Database Setup

```bash
# Push schema to database
npm run db:push

# Or generate migrations
npm run db:generate
npm run db:migrate

# Open Drizzle Studio (database GUI)
npm run db:studio
```

### Development

```bash
# Start dev server
npm run dev
```

Visit `http://localhost:4321`

## 📝 Available Scripts

- `npm run dev` - Start development server
- `npm run build` - Build for production
- `npm run preview` - Preview production build
- `npm run astro` - Run Astro CLI commands
- `npm run db:generate` - Generate database migrations
- `npm run db:migrate` - Run database migrations
- `npm run db:push` - Push schema changes to database
- `npm run db:studio` - Open Drizzle Studio

## 📁 Project Structure

```text
src/
├── components/         # Reusable Astro components
├── db/                 # Database schema and client
│   ├── initialize.ts   # Database initialization
│   ├── schema.ts       # Drizzle ORM schemas
│   └── validations.ts  # Zod validation schemas
├── layouts/            # Page layouts
│   └── Layout.astro
├── lib/                # Utility libraries
│   ├── config.ts       # App configuration
│   ├── content-converter.ts # Markdown/HTML conversion
│   ├── dom-utils.ts    # DOM manipulation
│   └── exa-search.ts   # AI-powered search
├── pages/              # Routes and pages
│   ├── api/            # API endpoints
│   │   ├── chat.ts     # AI chat endpoint
│   │   └── posts.ts    # Posts CRUD
│   └── index.astro     # Home page
└── styles/             # SCSS stylesheets
    ├── variables/      # SCSS variables and mixins
    ├── components/     # Component styles
    ├── pages/          # Page styles
    ├── reset.scss      # CSS reset
    └── global.scss     # Global styles
```

## 🎨 SCSS Architecture

This app uses a strict SCSS architecture:

- **No inline `<style>` tags** in `.astro` files
- **All styles in external SCSS files** for better maintainability
- **Data attributes for modifiers** (preferred over BEM)
- **Semantic class names** (no utility classes)

Example:

```astro
---
import '@/styles/components/button.scss';
---
<button class="btn" data-variant="primary" data-size="lg">
    Click Me
</button>
```

## 🗄️ Database

### Schema Definition

Define your database schema in `src/db/schema.ts` using Drizzle ORM:

```typescript
export const posts = pgTable('posts', {
    id: varchar('id', { length: 21 })
        .primaryKey()
        .$defaultFn(() => nanoid()),
    title: varchar('title', { length: 255 }).notNull(),
    content: text('content').notNull(),
    createdAt: timestamp('created_at').defaultNow().notNull(),
});
```

### Validation

Define Zod schemas in `src/db/validations.ts`:

```typescript
export const createPostSchema = z.object({
    title: z.string().min(1).max(255),
    content: z.string().min(1),
});
```

## 🔐 Authentication

Authentication is handled by BetterAuth. Configure in `src/lib/auth.ts`:

```typescript
import { betterAuth } from 'better-auth';
import { drizzleAdapter } from 'better-auth/adapters/drizzle';
import { db } from '@/db/initialize';

export const auth = betterAuth({
    database: drizzleAdapter(db, { provider: 'pg' }),
    emailAndPassword: { enabled: true },
});
```

## 🤖 AI Features

### Vercel AI SDK

Chat endpoint example in `src/pages/api/chat.ts`:

```typescript
import { OpenAI } from 'ai';

export const POST: APIRoute = async ({ request }) => {
    // AI chat implementation
};
```

### Exa Search

AI-powered search utilities in `src/lib/exa-search.ts`.

## 📱 Progressive Web App

This app includes PWA support with offline capabilities:

- Service worker auto-generated
- Installable on mobile/desktop
- Offline caching configured in `astro.config.mjs`

## 🚀 Deployment

### Vercel (Recommended)

```bash
# Install Vercel CLI
npm i -g vercel

# Deploy
vercel
```

Make sure to set these environment variables in your Vercel project settings:

- `DATABASE_URL`
- `BETTER_AUTH_SECRET`
- `BETTER_AUTH_URL`
- `OPENAI_API_KEY`
- `EXA_API_KEY` (optional)

## 📚 Documentation

- [Astro Documentation](https://docs.astro.build)
- [Drizzle ORM](https://orm.drizzle.team)
- [BetterAuth](https://betterauth.dev/docs)
- [Vercel AI SDK](https://sdk.vercel.ai/docs)
- [Zod](https://zod.dev)
- [Exa Search](https://docs.exa.ai)

## 🛠️ Utilities

### Content Conversion

```typescript
import { htmlToMarkdown, markdownToHtml } from '@/lib/content-converter';

const markdown = htmlToMarkdown('<h1>Hello</h1>');
const html = markdownToHtml('# Hello');
```

### DOM Manipulation

```typescript
import { extractText, findLinks } from '@/lib/dom-utils';

const text = extractText(htmlString);
const links = findLinks(htmlString);
```

### AI Search

```typescript
import { searchWithExa } from '@/lib/exa-search';

const results = await searchWithExa('your query');
```

## 📄 License

MIT
