# 9.6 Secrets Project

A web application that allows users to share and view secrets anonymously after authentication. Users can register, log in locally or via Google OAuth, submit their own secrets, and view secrets shared by others.

## Features

- User registration and login with local authentication
- Google OAuth integration for easy login
- Secure password hashing using bcrypt
- Session management with express-session
- MongoDB Atlas / Mongoose for storing user data and secrets
- EJS templating for dynamic web pages
- Responsive design with CSS

## Tech Stack

- **Backend**: Node.js, Express.js
- **Database**: MongoDB (via Mongoose)
- **Authentication**: Passport.js (Local and Google OAuth2 strategies)
- **Templating**: EJS
- **Security**: bcrypt for password hashing
- **Session Management**: express-session

## Prerequisites

- Node.js (v14 or higher)
- MongoDB Atlas Cluster (or local MongoDB instance)
- Google OAuth credentials (for Google login)

## Installation

1. Clone or download the project files.

2. Navigate to the project directory:
   ```
   cd 9.6+Secrets+Project
   ```

3. Install dependencies:
   ```
   npm install
   ```

4. Create a `.env` file in the root directory with the following variables:
   ```
   SESSION_SECRET=your_session_secret_here
   MONGO_URI=your_mongodb_connection_string
   GOOGLE_CLIENT_ID=your_google_client_id
   GOOGLE_CLIENT_SECRET=your_google_client_secret
   ```

## Usage

1. Start the server:
   ```
   node index.js
   ```

2. Open your browser and navigate to `http://localhost:3000`

3. Register a new account or log in with existing credentials

4. Submit your secret or view secrets from other users

## How It Works: Project Structure & Logic

### 1. Database Connection (`db.js`)
The application uses `mongoose` to connect to MongoDB Atlas. The connection string is pulled from the environment variables for security.

### 2. User Schema (`index.js`)
Unlike SQL, MongoDB is schemaless, but we use Mongoose to define a `userSchema`. It stores:
- `email`: Unique identifier.
- `password`: Hashed using bcrypt.
- `secret`: A string containing the user's submitted secret.

### 3. Authentication Flow
We use **Passport.js** to handle two types of authentication:

- **Local Strategy**: 
  - **Registration**: When a user signs up, `bcrypt` hashes the password before saving it to MongoDB.
  - **Login**: Passport compares the provided password with the hashed version in the database.
- **Google OAuth 2.0 Strategy**:
  - Users can sign in with Google. If the user doesn't exist in our database, a new record is created with a placeholder password.

### 4. Session Management
`express-session` maintains the user state across different pages. Once logged in, `passport.serializeUser` and `deserializeUser` manage the user object within the session, allowing us to check `req.isAuthenticated()` on protected routes.

### 5. Application Routes

- **Public Routes**:
  - `GET /`: The home page.
  - `GET /login` & `GET /register`: Authentication forms.

- **Protected Routes (Middleware-guarded)**:
  - `GET /secrets`: Retrieves the authenticated user's secret from MongoDB and displays it.
  - `GET /submit`: Displays a form to submit a new secret.
  - `POST /submit`: Updates the user's document in MongoDB with the new secret using `findOneAndUpdate`.

- **Authentication Routes**:
  - `POST /login` & `POST /register`: Handlers for local auth.
  - `GET /auth/google`: Redirects to Google's consent screen.
  - `GET /logout`: Ends the session and redirects home.

## Database Schema (MongoDB Collection: Users)

- `_id`: (ObjectId - Automatic)
- `email`: (String, Unique)
- `password`: (String - Hashed)
- `secret`: (String)

## Security Features

- `GET /` - Home page
- `GET /login` - Login page
- `GET /register` - Registration page
- `GET /secrets` - View secrets (authenticated users only)
- `GET /submit` - Submit a secret page (authenticated users only)
- `POST /login` - Process login
- `POST /register` - Process registration
- `POST /submit` - Submit a secret
- `GET /logout` - Logout
- `GET /auth/google` - Initiate Google OAuth
- `GET /auth/google/secrets` - Google OAuth callback

## Database Schema

The application uses a single `users` table:

- `id` (SERIAL PRIMARY KEY)
- `email` (VARCHAR(255) UNIQUE NOT NULL)
- `password` (VARCHAR(255)) - Hashed password or "google" for OAuth users
- `secret` (TEXT) - User's submitted secret

## Security Features

- Passwords are hashed using bcrypt before storage
- Session-based authentication
- Protection against unauthorized access to secret pages
- Secure handling of user credentials

## Contributing

This is a learning project. Feel free to fork and modify for your own purposes.

## License

ISC
