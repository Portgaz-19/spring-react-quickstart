# Spring-React QuickStart: Project Brief

Use this file as the source of truth for the project. Read it fully before writing any code. If something here is unclear or conflicts with what you were asked, stop and ask before assuming.

## 1. What we are building

A web app where a developer picks options for a full-stack project (React frontend + Spring Boot backend) in a short wizard, then downloads a ready-to-run zip of that customized project.

Problem: setting up the same boilerplate (routing, DB, auth, styling, build tooling) wastes hours on every new project. Existing starters are rigid or bloated. This lets people choose the stack pieces they want.

## 2. Who is building it

A student developer building a first micro SaaS as a side project. Comfortable with Java and Spring Boot. New to SaaS, payments and deployment. Explain non-obvious decisions briefly as you go. Target: free MVP finished in about one week at 4-5 hours a day.

## 3. MVP scope (this week)

In scope:
- One master template project (Spring Boot + React/Vite) that compiles and runs.
- A generator service that takes a JSON config and returns a customized zip.
- A React wizard (3 steps + summary) that calls the generator and downloads the zip.
- A script that generates every option combination and verifies each one builds.
- Deployment and a README.

Out of scope for now (do NOT build yet):
- Payments, Stripe or any other provider, webhooks, Pro modules, accounts, user database.
- Gradle, MySQL, OAuth, Material UI.

## 4. MVP options

| Option | Choices |
|---|---|
| Build tool | Maven only (fixed) |
| Database | H2 or PostgreSQL |
| Authentication | None or JWT |
| Styling | Tailwind CSS or plain CSS |

That is 2 x 2 x 2 = 8 combinations. Every one must produce a project that builds.

## 5. Tech stack

- Frontend: React (Vite), Tailwind CSS, React Hook Form
- Backend (the generator): Java 21, Spring Boot, Spring Web
- Zipping: `java.util.zip` or Apache Commons Compress, streamed (do not build whole projects in memory)
- Template rendering: Mustache or FreeMarker for conditional file content
- Hosting later: Vercel/Netlify (frontend), Render or Railway (backend)

## 6. Suggested repo structure

```
spring-react-quickstart/
  PROJECT_BRIEF.md
  generator-backend/        # Spring Boot app exposing POST /api/generate
  frontend/                 # React wizard
  templates/
    base/                   # master template files always included
    options/
      db-h2/
      db-postgres/
      auth-none/
      auth-jwt/
      style-tailwind/
      style-plain/
  scripts/
    verify-all-combinations.sh   # generates all 8 zips and builds each
```

Keep the template files as real, runnable files where possible, and use placeholders only where a file must change per option.

## 7. API contract

`POST /api/generate`

Request body:
```json
{
  "projectName": "my-app",
  "groupId": "com.example",
  "database": "h2 | postgres",
  "auth": "none | jwt",
  "styling": "tailwind | plain"
}
```

Response: `application/zip`, streamed, with a `Content-Disposition` attachment filename based on `projectName`.

Validation: reject unknown option values and invalid names (letters, numbers and hyphens only for projectName; valid Java package for groupId) with a 400 and a clear message. Never let user input become a file path.

## 8. Build order

1. Hand-build ONE working master template (Spring Boot + React). Confirm it runs. Commit.
2. Identify which files and lines change per option. Write that down before coding the generator.
3. Build the generator service for one option, then add the rest.
4. Add `POST /api/generate` and test with Postman or curl.
5. Write `verify-all-combinations.sh`. All 8 combinations must build.
6. Build the React wizard and the blob download.
7. Deploy, then write the README.

## 9. Rules for the AI agent

- Work in small steps. After each step, say what changed and how to run or test it.
- Commit to Git after each working step.
- Show a command before running anything that installs packages or deletes files, and do not delete anything outside this repo.
- Do not add features outside section 3, and do not add payment code.
- Prefer simple and readable over clever. This is a learning project.
- Never put secrets in the repo. Use environment variables and a `.env.example`.
- Security basics: sanitize all user input used in the generator, and prevent path traversal (zip slip) when writing or reading files.
- Tell me about problems and assumptions instead of working around them silently.

## 10. Definition of done for the MVP

- All 8 combinations generate and build successfully, proven by the verify script.
- The wizard downloads a working zip in the browser.
- Frontend and backend are deployed and talk to each other (CORS configured).
- README explains what the tool does, how to run it locally, and how to add a new option.
