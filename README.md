# E-comerce_backend_auth

A training exercise: an Express + MongoDB API for user authentication and user management, with email verification, token-based login, profile endpoints and an admin-only user list.

The repository name comes from a larger backend homework set. The code currently on `main` covers the authentication and user-management exercise only (branch `exercise/02_user-management_new`); there are no product, cart or order features. All code lives in [`project2/`](project2/).

## What it practices

- Structuring an Express app into routes, middleware and Mongoose models
- Input validation with regular expressions (name, email, password strength)
- Email verification links sent through Nodemailer (Gmail)
- Bearer-token authentication middleware and a role-based admin guard
- Mongoose schema hooks (hashing the password in a `pre('save')` hook)

## Tech stack

| Layer | Technology |
| --- | --- |
| Runtime | Node.js (CommonJS) |
| Framework | Express 4.21 |
| Database | MongoDB with Mongoose 8.11 |
| Email | Nodemailer 6.10 (Gmail service) |
| Dev server | nodemon |

## API

| Method | Path | Auth | Description |
| --- | --- | --- | --- |
| POST | `/api/auth/register` | Public | Validate input, create a user and email a verification link |
| GET | `/api/auth/verify/:token` | Public | Verify the account (token valid for 1 hour) |
| POST | `/api/auth/login` | Public | Return a session token (account must be verified) |
| POST | `/api/auth/password/reset` | Public | Generate a reset token and email a reset link |
| GET | `/api/users/profile` | Bearer token | Current user's profile |
| PUT | `/api/users/profile` | Bearer token | Update name and/or password |
| GET | `/api/admin/users` | Bearer token, `admin` role | List all users |

## Data model

A single Mongoose model, `User` (`project2/models/User.js`): `name`, `email` (unique), `password`, `role` (`admin` | `user`), `isVerified`, `verifyToken`/`verifyTokenExpires`, `resetToken`/`resetTokenExpires`, `token` (current login token), plus timestamps.

## Project structure

```text
project2/
├── server.js                  # Express app, MongoDB connection, route mounting (port 3000)
├── routes/
│   ├── auth.js                # register, verify, login, password reset request
│   ├── user.js                # profile read/update
│   └── admin.js               # admin-only user list
├── middleware/authMiddleware.js  # authenticate + adminAuth
└── models/User.js             # User schema and password helpers
```

## Getting started

Prerequisites: Node.js, npm, a MongoDB instance, and a Gmail account with an app password (registration fails if the verification email cannot be sent).

```bash
cd project2
npm install
cp .env.example .env    # set MONGO_URI, SMTP_USER, SMTP_PASS
npm run dev             # nodemon server.js, listens on http://localhost:3000
```

## Environment variables

| Name | Required | Description |
| --- | --- | --- |
| `MONGO_URI` | Yes | MongoDB connection string |
| `SMTP_USER` | Yes | Gmail address used to send emails |
| `SMTP_PASS` | Yes | Gmail app password |

## Known limitations

These are worth knowing before reusing any of this code:

- Passwords are hashed with unsalted SHA-256; a real application should use bcrypt or argon2.
- Login, verification and reset tokens are generated with `Math.random()` and the login token never expires.
- `role` is read from the registration body, so a client can register itself as `admin`.
- The password reset flow stops after emailing the link; there is no endpoint that accepts the reset token.
- `PUT /api/users/profile` changes the password without asking for the current one.
- No tests.

## Acknowledgements

The work was submitted as pull request #8, which was reviewed and merged by [@tms-anhle](https://github.com/tms-anhle).

## Author

**Lê Văn Trung Tính** — Software Engineer | AI Product Builder

- Website: [trungtinh.site](https://trungtinh.site)
- GitHub: [@tinhne](https://github.com/tinhne)
