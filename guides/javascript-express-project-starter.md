# JavaScript + Express Project Starter Guide

This guide provides a repeatable workflow for starting a small JavaScript and Express project. Use the [project checklist](../checklists/project-checklist.md) alongside it to track progress.

## Contents

1. [Plan before coding](#1-plan-before-coding)
2. [Create and initialize the project](#2-create-and-initialize-the-project)
3. [Start with version control](#3-start-with-version-control)
4. [Choose a project structure](#4-choose-a-project-structure)
5. [Manage configuration safely](#5-manage-configuration-safely)
6. [Create the Express server](#6-create-the-express-server)
7. [Design the API](#7-design-the-api-before-implementing-it)
8. [Validate external input](#8-validate-all-external-input)
9. [Add the frontend](#9-add-the-frontend-files)
10. [Design every UI state](#10-design-every-ui-state)
11. [Separate reusable views](#11-separate-reusable-views)
12. [Handle asynchronous errors](#12-handle-asynchronous-errors-consistently)
13. [Add development tools](#13-add-useful-development-tools)
14. [Test in layers](#14-test-in-layers)
15. [Apply a security baseline](#15-apply-a-practical-security-baseline)
16. [Keep documentation executable](#16-keep-documentation-executable)
17. [Prepare for deployment](#17-prepare-for-deployment)
18. [Follow a development order](#18-recommended-development-order)
19. [Use a debugging workflow](#19-debugging-workflow)
20. [Check the definition of done](#20-definition-of-done)

## 1. Plan before coding

Answer these questions first:

- What must the application do?
- Does it need a frontend, a backend, or both?
- What data will it use?
- Does the data belong in a JSON file or a database?
- Which API endpoints are required?
- What must be displayed?
- Which user interactions are required?

## 2. Create and initialize the project

```bash
mkdir project-name
cd project-name
npm init -y
npm install express
```

`npm init -y` creates `package.json`. Installing Express adds it as a dependency and creates `node_modules` together with the lockfile.

Add a development script to `package.json`:

```json
{
  "scripts": {
    "dev": "node server.js"
  }
}
```

Start the application with:

```bash
npm run dev
```

Stop it with <kbd>Ctrl</kbd> + <kbd>C</kbd>.

## 3. Start with version control

Initialize Git before the project becomes difficult to undo:

```bash
git init
git add .
git commit -m "chore: initialize project"
```

Create a `.gitignore` before the first commit:

```gitignore
node_modules/
.env
coverage/
dist/
*.log
.DS_Store
```

Do not ignore lockfiles such as `package-lock.json`. Commit them so everyone installs the same dependency versions.

Prefer small commits that describe one complete change:

```text
feat: add movie list endpoint
fix: handle missing movie IDs
docs: add local setup instructions
test: cover movie validation
```

## 4. Choose a project structure

### Simple frontend

```text
project/
├── index.html
├── script.js
└── style.css
```

### Express with a frontend

```text
project/
├── package.json
├── server.js
├── data.json
└── public/
    ├── index.html
    ├── script.js
    ├── style.css
    └── views.js
```

### Multiple pages

```text
project/
├── package.json
├── server.js
├── data.json
└── public/
    ├── movies.html
    ├── professionals.html
    ├── script.js
    ├── style.css
    └── views.js
```

Keep application logic in `script.js` and reusable HTML-generating functions in `views.js`.

For a project that grows beyond a few routes, use responsibilities rather than file types alone:

```text
project/
├── package.json
├── server.js
├── .env.example
├── src/
│   ├── app.js
│   ├── routes/
│   ├── controllers/
│   ├── services/
│   ├── middleware/
│   └── data/
├── public/
└── tests/
```

- **Routes** map URLs and HTTP methods to handlers.
- **Controllers** translate HTTP requests into application operations.
- **Services** contain reusable business logic.
- **Middleware** handles cross-cutting concerns such as logging and errors.
- **Data modules** isolate file or database access.

Start simple. Introduce these layers only when they remove real duplication or complexity.

## 5. Manage configuration safely

Values that differ between environments belong in environment variables:

```bash
npm install dotenv
```

```js
require("dotenv").config();

const PORT = Number(process.env.PORT) || 3000;
```

Commit a `.env.example` containing names and safe example values:

```dotenv
PORT=3000
DATABASE_URL=replace-with-local-database-url
```

Keep the real `.env` ignored. Never commit passwords, API keys, access tokens, or production connection strings. Validate required configuration when the application starts so missing values fail clearly.

## 6. Create the Express server

Create `server.js`:

```js
const express = require("express");

const app = express();
const PORT = Number(process.env.PORT) || 3000;

app.use(express.json());
app.use(express.static("public"));

app.get("/api/example", (req, res) => {
  res.json({ message: "Hello World" });
});

app.listen(PORT, () => {
  console.log(`Server running at http://localhost:${PORT}`);
});
```

Run the server and test the endpoint directly at `http://localhost:3000/api/example` before connecting the frontend.

`express.json()` is required before routes that read JSON from `req.body`. Middleware order matters.

## 7. Design the API before implementing it

Describe each endpoint with its method, path, input, successful response, and expected errors.

| Action | Method | Path | Typical success |
| --- | --- | --- | --- |
| List resources | `GET` | `/api/movies` | `200` |
| Read one resource | `GET` | `/api/movies/:id` | `200` |
| Create a resource | `POST` | `/api/movies` | `201` |
| Replace a resource | `PUT` | `/api/movies/:id` | `200` |
| Update part of a resource | `PATCH` | `/api/movies/:id` | `200` |
| Delete a resource | `DELETE` | `/api/movies/:id` | `204` |

Use nouns for resource paths and HTTP methods for actions. Keep response shapes consistent. One practical error format is:

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Title is required"
  }
}
```

## 8. Validate all external input

Route parameters, query parameters, request bodies, headers, files, and environment variables are all untrusted input.

```js
const crypto = require("node:crypto");

app.post("/api/movies", (req, res) => {
  const title = req.body.title?.trim();

  if (!title) {
    return res.status(400).json({
      error: {
        code: "VALIDATION_ERROR",
        message: "Title is required"
      }
    });
  }

  const movie = { id: crypto.randomUUID(), title };
  movies.push(movie);
  res.status(201).json(movie);
});
```

Validation answers whether input is allowed. Normalization makes valid input consistent—for example trimming a title or converting a numeric query parameter. Do both at the application boundary.

## 9. Add the frontend files

### `public/index.html`

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Project</title>
    <link rel="stylesheet" href="/style.css">
    <script src="/script.js" defer></script>
  </head>
  <body>
    <ul id="list"></ul>
  </body>
</html>
```

### `public/style.css`

```css
*,
*::before,
*::after {
  box-sizing: border-box;
}

body {
  margin: 0;
  font-family: Arial, Helvetica, sans-serif;
}
```

### `public/script.js`

```js
init();

async function init() {
  try {
    const response = await fetch("/api/example");

    if (!response.ok) {
      throw new Error(`Request failed: ${response.status}`);
    }

    const data = await response.json();
    renderMessage(data.message);
  } catch (error) {
    console.error(error);
  }
}

function renderMessage(message) {
  const list = document.getElementById("list");
  list.innerHTML = `<li>${message}</li>`;
}
```

For data that can come from users, create DOM nodes and set `textContent` instead of inserting raw values with `innerHTML`.

## 10. Design every UI state

A working interface needs more than its successful state. Plan for:

- **loading** — the request is still running
- **empty** — the request succeeded but there is nothing to show
- **success** — data is available
- **error** — the operation failed and the user can understand what happened
- **disabled or pending action** — prevent accidental duplicate submissions

Use semantic HTML, associate labels with form controls, keep keyboard focus visible, and do not communicate meaning through color alone.

## 11. Separate reusable views

When rendering becomes more complex, move markup generation into `views.js`:

```js
function createMovieCard(movie) {
  return `
    <article class="movie">
      <h2>${movie.title}</h2>
    </article>
  `;
}
```

Load `views.js` before `script.js`, or use JavaScript modules when the project is ready for them.

## 12. Handle asynchronous errors consistently

For a small application, `try`/`catch` inside async handlers is sufficient. As the app grows, use a wrapper that forwards rejected promises:

```js
function asyncHandler(handler) {
  return (req, res, next) => {
    Promise.resolve(handler(req, res, next)).catch(next);
  };
}

app.get("/api/movies", asyncHandler(async (req, res) => {
  const movies = await movieService.getAll();
  res.json(movies);
}));
```

Register a 404 handler after the routes and the four-argument error handler last. Log detailed errors on the server, but return a safe, useful message to the client.

## 13. Add useful development tools

A typical project benefits from automatic restart, formatting, linting, and tests:

```bash
npm install --save-dev nodemon prettier eslint vitest supertest
```

Example scripts:

```json
{
  "scripts": {
    "dev": "nodemon server.js",
    "start": "node server.js",
    "format": "prettier --write .",
    "lint": "eslint .",
    "test": "vitest run",
    "test:watch": "vitest"
  }
}
```

Choose tools intentionally and commit their configuration. A smaller, consistently used toolchain is better than a large unused one.

## 14. Test in layers

Do not rely only on clicking through the finished page.

1. **Pure functions:** test validation, transformation, and business rules quickly.
2. **API integration:** test routes, response bodies, errors, and status codes.
3. **Browser workflow:** test the most important user journey end to end.
4. **Manual exploration:** try unusual sequences and inspect usability.

For every important feature, cover at least one successful case, boundary values, invalid input, missing data, and dependency failure where relevant.

A good test checks observable behavior rather than private implementation details. Tests should be independent and produce the same result on every run.

## 15. Apply a practical security baseline

- Never trust or directly render external input.
- Keep secrets out of source control and client-side JavaScript.
- Use parameterized database queries instead of string concatenation.
- Limit request-body and file-upload sizes.
- Restrict CORS to the origins that actually need access.
- Add rate limiting to sensitive or public endpoints.
- Use secure, `httpOnly`, appropriate `sameSite` cookies for sessions.
- Do not reveal stack traces or internal details in production responses.
- Keep dependencies current and review audit findings rather than applying changes blindly.
- Use HTTPS in deployed environments.

Security requirements depend on the data and threat model. Authentication, authorization, password storage, payments, and personal data need dedicated design—not a copied snippet.

## 16. Keep documentation executable

The README should let someone unfamiliar with the project answer:

- What does this project do?
- What software is required?
- How do I install and configure it?
- How do I run it locally?
- How do I run checks and tests?
- Which environment variables are required?
- Where is the API documented?

Write commands that can be copied and run. Test the instructions from a clean clone or fresh directory before release.

For each API endpoint, document the method, path, inputs, example response, status codes, and authentication requirements. OpenAPI can become useful once the API is large or consumed by other teams.

## 17. Prepare for deployment

Before deployment:

- use `process.env.PORT` rather than a fixed production port
- define a production start command
- ensure dependencies install from the lockfile
- configure environment variables in the hosting platform
- verify that files and databases are stored persistently where required
- add a health endpoint if the platform uses health checks
- decide how logs, errors, backups, and migrations will be handled
- test a production-like build locally when possible

Example health endpoint:

```js
app.get("/health", (req, res) => {
  res.status(200).json({ status: "ok" });
});
```

After deployment, run a smoke test: open the application, exercise its main flow, verify an API request, and inspect the platform logs.

## 18. Recommended development order

1. Understand the task and list the endpoints.
2. Create the project and install dependencies.
3. Start the smallest possible Express server.
4. Implement one endpoint.
5. Test the endpoint independently.
6. Create the basic frontend.
7. Fetch data from the backend.
8. Render the result.
9. Add user interactions.
10. Add styling.
11. Add automated tests for core behavior.
12. Check security-sensitive boundaries.
13. Update the README and configuration example.
14. Test the complete workflow from a clean setup.
15. Deploy and run a smoke test.

Build one small piece at a time and verify it before adding the next one.

## 19. Debugging workflow

### The server does not start

1. Read the terminal error from the first relevant line.
2. Check that dependencies were installed.
3. Check the `package.json` script and the `server.js` filename.
4. Fix syntax errors and restart the server.

### `Cannot GET /...`

1. Confirm the URL and HTTP method.
2. Confirm that a matching route exists.
3. Check route parameters and spelling.
4. Restart the server if it does not reload automatically.

### A fetch request fails

1. Test the API endpoint directly.
2. Inspect the browser Network and Console panels.
3. Check `response.ok` and `response.status`.
4. Confirm that the response format matches the call to `response.json()`.

### The problem is unclear

1. Reproduce it reliably with the smallest input possible.
2. Read the first useful error and its stack trace.
3. Identify the boundary where expected and actual behavior diverge.
4. Inspect values at that boundary with a debugger or focused logging.
5. Change one assumption at a time.
6. Turn the fixed case into a regression test.

## 20. Definition of done

A feature is not complete merely because its happy path worked once. Before calling it done, confirm that:

- behavior matches the requirement and acceptance criteria
- invalid input and failures are handled
- names and structure make the code understandable
- automated checks pass
- no secret or debug artifact was added
- accessibility and responsive layout were considered
- documentation reflects the current behavior
- another developer can install and run the project
- the change has been reviewed as a complete user workflow

## Working principles

- Read the complete task before coding.
- Make one change at a time.
- Test after every meaningful step.
- Make it work before polishing the design.
- Keep data, application logic, and presentation separate.
- Use descriptive names and small functions.
- Treat console output as a debugging tool, not as the user interface.
