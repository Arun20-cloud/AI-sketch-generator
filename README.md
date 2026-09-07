# AI Sketch Generator

An AI-powered forensic sketch generation and identification application that generates realistic police-style suspect sketches from textual descriptions. The platform provides user authentication, sketch management, gallery search, refinement feedback, and administrative functionality.

## Features

* **AI-Powered Sketch Generation**

  * Generate forensic-style suspect sketches from textual descriptions.
  * Uses Pollinations.ai with the Flux model for image generation.
  * Generates monochrome, pencil-style police composite sketches.

* **User Authentication**

  * User registration and login.
  * Session-based authentication.
  * Secure password handling using bcrypt.
  * Role-based access for regular users and administrators.

* **Sketch Management**

  * Store generated sketches with case information and location.
  * View previously generated sketches.
  * Search sketches by description, case number, or location.
  * View individual sketch details.

* **Sketch Refinement**

  * Add feedback to previously generated sketches.
  * Record refinement type, user information, and timestamps.
  * Allows authorized users to provide additional sketch requirements.

* **Admin Dashboard**

  * Administrative user management.
  * System statistics.
  * Administrative access to application data.

* **Cloud Storage**

  * Generated images are uploaded to Vercel Blob Storage.
  * MongoDB is used for persistent sketch and user data.

* **Fallback Storage**

  * Includes in-memory storage fallback when MongoDB is unavailable.

## Technology Stack

### Frontend

* Next.js 15
* React 19
* TypeScript
* Tailwind CSS
* Radix UI
* Lucide React

### Backend

* Next.js API Routes
* TypeScript
* REST APIs
* Node.js

### AI

* Pollinations.ai
* Flux image generation model
* Generative AI

### Database & Storage

* MongoDB
* Vercel Blob Storage
* In-memory fallback storage

### Authentication & Security

* bcryptjs
* JWT/session authentication
* Role-based authorization

## Application Architecture

```text
User
  │
  ▼
Next.js Frontend
  │
  ├── Authentication
  │
  ├── Sketch Generation
  │
  ├── Sketch Gallery
  │
  ├── Sketch Refinement
  │
  └── Admin Dashboard
          │
          ▼
     Next.js API Routes
          │
     ┌────┴─────────────┐
     ▼                  ▼
Pollinations.ai      MongoDB
     │                  │
     ▼                  │
Generated Image        │
     │                  │
     └──────┬───────────┘
            ▼
       Vercel Blob
       Image Storage
```

## Project Structure

```text
AI-Sketch-Final-main/
│
├── app/
│   ├── admin/
│   │   └── page.tsx
│   │
│   ├── api/
│   │   ├── admin/
│   │   ├── auth/
│   │   ├── generate-sketch/
│   │   ├── sketches/
│   │   └── test-db/
│   │
│   ├── dashboard/
│   ├── gallery/
│   ├── generate/
│   ├── login/
│   ├── register/
│   ├── globals.css
│   └── layout.tsx
│
├── components/
│   ├── auth-provider.tsx
│   ├── theme-provider.tsx
│   └── ui/
│
├── hooks/
│
├── lib/
│   ├── auth.ts
│   ├── mongodb.ts
│   ├── storage-fallback.ts
│   └── utils.ts
│
├── public/
│
├── next.config.mjs
├── package.json
├── pnpm-lock.yaml
├── postcss.config.mjs
├── tsconfig.json
└── README.md
```

## How It Works

### 1. User Authentication

Users register or log in through the authentication interface. The application maintains the authenticated session and uses the user's role to control access to application functionality.

### 2. Generate a Sketch

The user provides a description of the person along with optional case information and location.

The request is sent to:

```text
POST /api/generate-sketch
```

The backend:

1. Validates the user's session.
2. Validates the submitted description.
3. Builds an enhanced forensic sketch prompt.
4. Sends the prompt to Pollinations.ai.
5. Retrieves the generated image.
6. Uploads the image to Vercel Blob Storage.
7. Stores sketch metadata in MongoDB.
8. Returns the generated sketch information to the frontend.

### 3. Sketch Storage

Each generated sketch can contain information such as:

```text
User ID
User Name
User Role
Description
Case Number
Location
Image URL
Creation Date
Updated Date
Status
Refinement History
```

### 4. Gallery

The gallery retrieves saved sketches and provides search functionality.

Users can search using:

* Description
* Case number
* Location

### 5. Refinement

Users can submit feedback for an existing sketch.

The refinement API records:

```text
User
Feedback
Refinement Type
Creation Date
```

Authorization checks ensure that only the sketch owner or an administrator can add refinement feedback.

## API Endpoints

### Authentication

```text
POST /api/auth/register
POST /api/auth/login
POST /api/auth/logout
GET  /api/auth/me
```

### Sketch Generation

```text
POST /api/generate-sketch
```

Example request:

```json
{
  "description": "Male, approximately 30 years old, short brown hair...",
  "caseNumber": "CASE-001",
  "location": "Bangalore"
}
```

### Sketch Management

```text
GET    /api/sketches
GET    /api/sketches/[id]
DELETE /api/sketches/[id]
POST   /api/sketches/[id]/refine
```

### Administration

```text
GET /api/admin/stats
GET /api/admin/users
```

## Environment Variables

Create a `.env.local` file in the project root.

```env
MONGODB_URI=your_mongodb_connection_string
BLOB_READ_WRITE_TOKEN=your_vercel_blob_token
```

Configure the required environment variables according to your MongoDB and Vercel Blob Storage setup.

## Installation

### Prerequisites

* Node.js
* pnpm
* MongoDB
* Vercel Blob Storage credentials

### Clone the Repository

```bash
git clone <your-repository-url>
cd AI-Sketch-Final-main
```

### Install Dependencies

```bash
pnpm install
```

### Configure Environment Variables

Create:

```text
.env.local
```

and add the required configuration.

### Run Development Server

```bash
pnpm dev
```

The application will be available at:

```text
http://localhost:3000
```

## Production Build

Create a production build:

```bash
pnpm build
```

Start the production server:

```bash
pnpm start
```

## Development

Run linting with:

```bash
pnpm lint
```

## Key Components

### `app/api/generate-sketch/route.ts`

Handles the complete sketch-generation workflow, including authentication, prompt creation, AI image generation, image storage, and database persistence.

### `lib/mongodb.ts`

Provides MongoDB connectivity and database access for persistent application data.

### `lib/auth.ts`

Handles authentication and session-related functionality.

### `lib/storage-fallback.ts`

Provides an in-memory storage mechanism when MongoDB is unavailable.

### `app/gallery/`

Provides the interface for browsing and searching generated sketches.

### `app/admin/`

Provides administrative functionality for managing users and viewing application statistics.

## Data Flow

```text
Description
     │
     ▼
Frontend Form
     │
     ▼
POST /api/generate-sketch
     │
     ▼
Authentication Check
     │
     ▼
Prompt Generation
     │
     ▼
Pollinations.ai / Flux
     │
     ▼
Generated Sketch
     │
     ├──────────────► Vercel Blob
     │                     │
     ▼                     ▼
MongoDB ◄──────────── Image URL
     │
     ▼
Frontend Response
     │
     ▼
Sketch Gallery
```

## Security

The application includes:

* Authenticated API access.
* Password hashing.
* Session validation.
* Role-based authorization.
* Ownership checks for sketch refinement.
* Protected administrative functionality.
* Input validation for required sketch descriptions.

## Use Cases

The application can be used as a prototype for:

* Forensic sketch generation
* Suspect description visualization
* AI-assisted investigation workflows
* Law-enforcement technology demonstrations
* Generative AI application development
* AI-powered image generation systems

## Disclaimer

This project is an AI-based prototype intended for research, educational, and demonstration purposes. AI-generated sketches should not be treated as definitive identification of real individuals and should be independently verified by qualified professionals.

## Future Enhancements

Potential improvements include:

* Face feature selection controls.
* Image upload and reference-image processing.
* Advanced facial attribute controls.
* Improved sketch refinement using image-to-image generation.
* Facial similarity matching.
* Automated case management.
* Audit logging.
* Advanced search and filtering.
* Model performance evaluation.
* Production-grade monitoring and error tracking.

## License

This project is intended for educational and development purposes. Add an appropriate open-source license if you plan to distribute the project publicly.
