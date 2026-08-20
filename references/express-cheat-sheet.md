# Express Cheat Sheet

A quick reference for common Express patterns and the JavaScript concepts frequently used with them.

## Setup

```bash
npm init -y
npm install express
```

```js
const express = require("express");

const app = express();
const PORT = 3000;

app.use(express.json());
app.use(express.static("public"));

app.listen(PORT, () => {
  console.log(`Server running at http://localhost:${PORT}`);
});
```

| Expression | Purpose |
| --- | --- |
| `express()` | Creates an Express application |
| `app.use()` | Registers middleware |
| `express.json()` | Parses JSON request bodies |
| `express.static()` | Serves static files |
| `app.listen()` | Starts the server |

## Routes

```js
app.get("/api/movies", (req, res) => {
  res.json(movies);
});

app.post("/api/movies", (req, res) => {
  const movie = req.body;
  res.status(201).json(movie);
});
```

Common route methods include `app.get()`, `app.post()`, `app.put()`, `app.patch()`, and `app.delete()`.

## Request data

| Property | Contains | Example URL or use |
| --- | --- | --- |
| `req.params` | Route parameters | `/api/movies/42` |
| `req.query` | Query-string values | `/api/movies?page=2` |
| `req.body` | Parsed request body | JSON sent by a client |
| `req.headers` | Request headers | Content type, authorization |

### Route parameters

```js
app.get("/api/movies/:id", (req, res) => {
  const movie = movies.find((item) => item.id === Number(req.params.id));

  if (!movie) {
    return res.status(404).json({ error: "Movie not found" });
  }

  res.json(movie);
});
```

Express decodes route parameters. Use `encodeURIComponent()` on dynamic path segments when building URLs in the client.

### Query parameters

For `/api/movies?page=2`:

```js
const page = Number(req.query.page ?? 1);
```

Query and route parameter values arrive as strings unless the application converts them.

## Responses

| Method | Purpose |
| --- | --- |
| `res.send()` | Sends text, HTML, or another supported value |
| `res.json()` | Sends JSON |
| `res.sendFile()` | Sends a file using an absolute path |
| `res.status()` | Sets the HTTP response status |

```js
res.send("Hello World");
res.json({ message: "Hello" });
res.status(404).json({ error: "Not found" });
```

Common statuses:

- `200 OK` — successful request
- `201 Created` — a resource was created
- `400 Bad Request` — invalid client input
- `404 Not Found` — resource or route not found
- `500 Internal Server Error` — unexpected server error

### Sending a file

```js
const path = require("path");

app.get("/", (req, res) => {
  res.sendFile(path.join(__dirname, "public", "index.html"));
});
```

When `express.static("public")` is enabled, `public/index.html` is normally served automatically at `/`.

## Middleware and error handling

Middleware runs in registration order. Call `next()` when it should pass control onward.

```js
app.use((req, res, next) => {
  console.log(req.method, req.url);
  next();
});
```

Add a not-found handler after all routes:

```js
app.use((req, res) => {
  res.status(404).json({ error: "Route not found" });
});
```

Add error-handling middleware last. Its four parameters are significant:

```js
app.use((error, req, res, next) => {
  console.error(error);
  res.status(500).json({ error: "Internal server error" });
});
```

## Related JavaScript references

These are JavaScript and browser APIs rather than Express features, but they commonly appear in small Express projects.

### Array methods

```js
const titles = movies.map((movie) => movie.title);
const movie = movies.find((item) => item.id === id);
const recentMovies = movies.filter((movie) => movie.year > 2020);
const hasRecentMovie = movies.some((movie) => movie.year > 2020);
const allHaveTitles = movies.every((movie) => Boolean(movie.title));
```

### Fetch and async/await

```js
async function getMovies() {
  const response = await fetch("/api/movies");

  if (!response.ok) {
    throw new Error(`Request failed: ${response.status}`);
  }

  return response.json();
}
```

### DOM and events

```js
const button = document.querySelector("button");
const list = document.getElementById("movie-list");

button.addEventListener("click", () => {
  list.textContent = "Loading...";
});
```

Useful DOM APIs include `querySelector()`, `querySelectorAll()`, `textContent`, `appendChild()`, and `classList`.

## Quick debugging checks

### `Cannot GET /...`

- Is the URL correct?
- Does a route with that HTTP method exist?
- Are route parameters spelled correctly?
- Was the server restarted after the change?

### The server does not start

- Read the terminal error.
- Run `npm install` if dependencies are missing.
- Check `package.json` and the start script.
- Check for syntax errors and unsaved files.

### Fetch fails

- Does the API work when opened directly?
- What appears in the browser Network and Console panels?
- Is `response.ok` false?
- Does the response contain valid JSON?
