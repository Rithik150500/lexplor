# LeXploR - Legal Research Assistant

LeXploR is an AI-powered legal research assistant designed to help legal professionals analyze legal matters, identify relevant case law, and generate comprehensive legal memos.

## Table of Contents

- [Overview](#overview)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [Installation](#installation)
  - [Environment Variables](#environment-variables)
- [Development](#development)
  - [Running Locally](#running-locally)
  - [Database Management](#database-management)
  - [Authentication](#authentication)
- [API Documentation](README-API.md)
- [Testing](#testing)
- [Deployment](#deployment)
- [Contributing](#contributing)

## Overview

LeXploR is a Next.js application that provides an intuitive interface for legal research. It connects to a Python backend for natural language processing and legal analysis. The application allows users to:

- Submit legal matters for analysis
- Upload and process case files (PDF, DOCX, TXT)
- Receive structured legal memos with issues, discussion, and conclusions
- View relevant case law with detailed summaries
- Ask follow-up questions through an interactive chat interface
- Save and manage research sessions
- Drag-and-drop file organization with DnD Kit
- Interactive data visualization with Recharts
- PDF document viewing and manipulation

## Tech Stack

- **Frontend**:

  - Next.js 15 (App Router)
  - React 19
  - TypeScript 5
  - Tailwind CSS 3.4
  - shadcn/ui components
  - Zustand 5 (state management)
  - Clerk (authentication)
  - DnD Kit (drag and drop)
  - Framer Motion (animations)
  - Recharts (data visualization)
  - React PDF (PDF viewing)

- **Backend**:

  - Next.js API routes (TypeScript)
  - Python Flask API (for NLP and legal analysis)
  - PostgreSQL (via Prisma ORM)

- **Libraries**:
  - Mammoth (DOCX processing)
  - pdf-parse, pdf-lib, pdfjs-dist (PDF processing)
  - React Markdown
  - Framer Motion
  - Zod (validation)
  - Sonner (toasts)
  - Vaul (drawer components)

## Project Structure

```
lexplor-nextjs/
├── src/
│   ├── app/                    # Next.js App Router pages
│   │   ├── api/                # API routes
│   │   ├── analysis/           # Analysis page
│   │   ├── history/            # History page
│   │   ├── profile/            # User profile page
│   │   ├── docs/               # Documentation page
│   │   └── ...
│   ├── components/             # React components
│   │   ├── ui/                 # UI components (shadcn)
│   │   ├── chat-interface.tsx  # Main chat interface
│   │   ├── analysis-page.tsx   # Analysis page component
│   │   └── ...
│   ├── lib/                    # Utility functions and shared code
│   │   ├── store.ts            # Zustand store
│   │   └── utils.ts            # Utility functions
│   ├── hooks/                  # Custom React hooks
│   ├── types/                  # TypeScript type definitions
│   └── ...
├── prisma/                     # Prisma schema and migrations
│   └── schema.prisma           # Database schema
├── public/                     # Static assets
├── middleware.ts               # Next.js middleware (auth)
└── ...
```

## Getting Started

### Prerequisites

- Node.js 18+ and pnpm
- PostgreSQL database
- Python 3.8+ (for backend API)
- Clerk account for authentication

### Installation

1. Clone the repository:

   ```bash
   git clone https://github.com/your-org/lexplor-nextjs.git
   cd lexplor-nextjs
   ```

2. Install dependencies:

   ```bash
   pnpm install
   ```

3. Set up the database:

   ```bash
   npx prisma generate
   npx prisma db push
   ```

4. Set up environment variables (see below)

5. Start the development server:
   ```bash
   pnpm dev
   ```

### Environment Variables

Create a `.env.local` file in the root directory with the following variables:

```
# Database
DATABASE_URL="postgresql://postgres:postgres@localhost:5432/lexplor?schema=public"

# Clerk Auth
NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=your_clerk_publishable_key
CLERK_SECRET_KEY=your_clerk_secret_key
NEXT_PUBLIC_CLERK_SIGN_IN_URL=/sign-in
NEXT_PUBLIC_CLERK_SIGN_UP_URL=/sign-up
NEXT_PUBLIC_CLERK_AFTER_SIGN_IN_URL=/
NEXT_PUBLIC_CLERK_AFTER_SIGN_UP_URL=/
CLERK_WEBHOOK_SIGNING_SECRET=your_clerk_webhook_signing_secret

# Backend API URL
BACKEND_URL=http://localhost:5000
```

## Development

### Running Locally

1. Start the Next.js development server with Turbopack:

   ```bash
   pnpm dev
   ```

2. Start the Python backend server (in a separate terminal):

   ```bash
   cd backend
   python app.py
   ```

3. Access the application at `http://localhost:3000`

### Database Management

We use Prisma ORM to manage the database. The schema is defined in `prisma/schema.prisma`.

To update the database schema:

1. Modify the schema in `prisma/schema.prisma`
2. Run migrations:
   ```bash
   npx prisma migrate dev --name your_migration_name
   ```

To view and manage data:

```bash
npx prisma studio
```

### Authentication

Authentication is handled by Clerk. The configuration is in:

- `middleware.ts` - Protects routes and handles redirects
- `app/layout.tsx` - Wraps the application with `ClerkProvider`
- `components/user-profile-provider.tsx` - Syncs Clerk user data with our database

## Testing

Currently, the project does not have automated tests. This is an area for improvement.

To manually test the application:

1. Test user authentication flows (sign up, sign in, profile management)
2. Test legal matter submission with various inputs
3. Test case file uploads with different file formats
4. Test the chat interface with follow-up questions
5. Test session management (saving, loading, deleting)
6. Test drag-and-drop functionality
7. Test PDF document viewing and manipulation
8. Test data visualization components

## Deployment

### Vercel Deployment

1. Connect your GitHub repository to Vercel
2. Configure environment variables in the Vercel dashboard
3. Deploy the application

### Database Deployment

1. Set up a PostgreSQL database (e.g., on Neon, Supabase, or Railway)
2. Update the `DATABASE_URL` environment variable with the production database URL
3. Run migrations on the production database:
   ```bash
   npx prisma migrate deploy
   ```

### Python Backend Deployment

The Python backend can be deployed to:

- Heroku
- Google Cloud Run
- AWS Lambda
- Digital Ocean App Platform

Update the `BACKEND_URL` environment variable to point to your deployed backend.

## Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/your-feature-name`
3. Commit your changes: `git commit -m 'Add some feature'`
4. Push to the branch: `git push origin feature/your-feature-name`
5. Open a pull request

### Code Style

- Follow the existing code style
- Use TypeScript for type safety
- Use ESLint and Prettier for code formatting
- Write meaningful commit messages

### Development Workflow

1. Pick an issue from the issue tracker
2. Create a branch for the issue
3. Implement the feature or fix
4. Write tests (when we add testing)
5. Submit a pull request
6. Code review
7. Merge to main branch

---

## License

[MIT License](LICENSE)
