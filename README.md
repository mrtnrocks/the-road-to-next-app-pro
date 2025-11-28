# The Road to Next (PRO)

The finished application that you get after completing the first journey of The Road to Next. Here you can already download it as a starter kit and start your own journey.

## Installation

1. Clone the repository
2. Add your own `.env` file with the following content [0] coming from [Supabase](https://supabase.com/) [1]
3. Run `npm install` to install the dependencies (maybe you have to append the `--force` flag)
4. Run the database migration `npx prisma db push` to create the DB tables

[0]

```sh
// .env
DATABASE_URL="postgres://postgres.[project]:[password]@aws-0-[aws-region].pooler.supabase.com:6543/postgres?pgbouncer=true"

DIRECT_URL="postgres://postgres.[project]:[password]@aws-0-[aws-region].pooler.supabase.com:5432/postgres"
```

[1]

![Screenshot 2024-11-20 at 14 48 38](https://github.com/user-attachments/assets/3be82183-fb18-4845-a044-82c2c02787d8)

## Usage

```sh
npm run prisma-seed
```

```sh
npm run dev
```

```sh
npx prisma studio
```

```sh
npm run email
```

```sh
npx inngest-cli@latest dev
```

```sh
stripe listen --forward-to localhost:3000/api/stripe
```

An `agents.md` (or `.cursorrules`) file is a powerful way to align AI coding assistants with your specific architectural patterns. Based on the deep analysis of your repository, I have drafted a comprehensive `agents.md` file.

It focuses on your **Feature-Based Architecture**, **Server Action patterns**, and specific libraries like **Nuqs** and **Lucia** that define your codebase's unique flavor.

### `agents.md`

````markdown
# Project Context
You are working on "The Road to Next (Pro)", a modern SaaS application built with Next.js 15, TypeScript, and Prisma.

## Tech Stack
- **Framework:** Next.js 15 (App Router)
- **Language:** TypeScript (Strict mode)
- **Database:** PostgreSQL (via Supabase), Prisma ORM
- **Styling:** Tailwind CSS, Shadcn UI (Radix Primitives), Lucide Icons
- **State Management:** Server State (React Query), URL State (Nuqs)
- **Auth:** Lucia Auth (Session based), Argon2 hashing
- **Forms:** React Server Actions, Zod validation, `useActionState` hook
- **Infrastructure:** AWS S3 (Storage), Inngest (Background Jobs), Stripe (Payments), Resend (Email)

## Architecture & Directory Structure
This project follows a **Feature-Based Architecture**. Code related to a specific domain concept (e.g., Tickets, Comments, Auth) is grouped together in `src/features/`.

### Folder Structure
- `src/app/`: Next.js App Router routes.
  - `(authenticated)/`: Protected routes requiring login.
  - `api/`: Route handlers (e.g., Webhooks, Cron).
- `src/features/<feature-name>/`: Domain logic.
  - `actions/`: Server Actions (mutations). MUST return `ActionState`.
  - `components/`: Feature-specific UI components.
  - `queries/`: Data fetching functions (used by Server Components).
  - `utils/`: Feature-specific helpers.
  - `types.ts`: Feature-specific type definitions.
- `src/components/`: Shared/Generic UI components (buttons, inputs, cards).
- `src/lib/`: 3rd party library initialization (Prisma, Stripe, S3, etc.).
- `src/paths.ts`: Centralized route path definitions. **ALWAYS use this for internal links.**

## Coding Standards & Patterns

### 1. Data Fetching (Server Components)
- Fetch data directly in Server Components using functions from `src/features/<feature>/queries/`.
- Do **NOT** use `fetch` directly in components; encapsulate logic in query functions.
- Example: `const ticket = await getTicket(ticketId);`

### 2. Mutations (Server Actions)
- All mutations must be Server Actions located in `src/features/<feature>/actions/`.
- Actions must return the `ActionState` type defined in `src/components/form/utils/to-action-state.ts`.
- Use `zod` for input validation.
- Always handle authorization checks (e.g., `getAuthOrRedirect`, `isOwner`) inside the action.
- **Pattern:**
  ```typescript
  export const myAction = async (_actionState: ActionState, formData: FormData) => {
    const { user } = await getAuthOrRedirect();
    try {
        // validation and logic
    } catch (error) {
        return fromErrorToActionState(error, formData);
    }
    return toActionState("SUCCESS", "Message", null, data);
  };
````

### 3\. Forms

  - Use the `useActionState` hook for form handling.
  - Use the shared `<Form>` component from `src/components/form/form.tsx`.
  - Use `<FieldError>` for validation messages.
  - Use `<SubmitButton>` which handles the pending state automatically.

### 4\. URL State Management

  - Use `nuqs` for managing state in the URL (search parameters, tabs, pagination).
  - Define parsers in `src/features/<feature>/search-params.ts`.
  - Use `searchParamsCache` in Server Components.
  - Use `useQueryState` or `useQueryStates` in Client Components.

### 5\. Authentication

  - Use `getAuth()` for optional auth or `getAuthOrRedirect()` for protected routes.
  - Use `src/features/auth/utils/is-owner.ts` for ownership checks.
  - Auth logic resides in `src/features/auth/`.

### 6\. UI & Styling

  - Use Tailwind CSS for styling.
  - Use `clsx` and `tailwind-merge` (via `cn()` utility) for conditional classes.
  - Use Shadcn UI components from `src/components/ui/`.
  - **Visual Hierarchy:** Use `<Heading>` for page titles and `<CardCompact>` for forms/content sections.

## Rules for the Agent

1.  **Centralized Paths:** NEVER hardcode URL strings. Import and use path helpers from `src/paths.ts`.
2.  **Feature Isolation:** If you create new logic, determine if it belongs to an existing feature or a new one. Do not dump code in `src/utils` if it belongs to a feature.
3.  **Type Safety:** Do not use `any`. Use Prisma generated types or Zod inferred types.
4.  **Error Handling:** Always use `fromErrorToActionState` in Server Actions to ensure consistent error reporting to the UI.
5.  **Pagination:** Use the shared `<Pagination>` component and `PaginatedData` type.

<!-- end list -->

```

### Recommendations on how to use this:

1.  **VS Code:** Create a file named `.cursorrules` (if using Cursor) or `agents.md` in the root of your project and paste the content above.
2.  **Onboarding:** Tell your team to reference this file when writing new features to ensure they follow the "Feature-based" structure, which is the strongest architectural decision in this repo.
3.  **Evolution:** As you add new global patterns (e.g., a new way to handle file uploads), update this file so the "Senior Developer" intent is preserved.
```
