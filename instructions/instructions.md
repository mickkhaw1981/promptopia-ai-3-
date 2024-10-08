# Enhanced Product Requirements Document (PRD) for Promptopia

## Project Overview

**Promptopia** is a web application designed to help users discover, create, and share creative AI prompts. The platform allows users to browse a variety of prompts for different AI tools, create and manage their own prompts, and interact with a community of prompt enthusiasts.

---

## Core Functionality

### User Capabilities

1. **Discover Prompts**: Users can search and browse creative prompts tailored for various AI tools.
2. **Create Prompts**: Users can design and save their own unique prompts with a title, description, prompt content, and the AI tool it's intended for.
3. **User Profiles**: Users have personal profiles where they can view and manage their prompts and profile information.
4. **CRUD Operations**: Full Create, Read, Update, and Delete operations are available for managing prompts.
5. **Search Functionality**: Users can search for prompts based on keywords, categories, or tools.

---

## Tech Stack and Packages

- **Framework**: Next.js 14
- **UI Libraries**: ShadCN, Tailwind CSS
- **Icons**: Lucid Icons
- **Authentication**: NextAuth.js
- **Database**: MongoDB
- **Data Validation**: Zod

---

## Epics and User Stories

### Epic 1: Homepage

#### User Stories

- **Viewing the Homepage**: As a non-logged-in user, I want to see the homepage with a navigation bar, hero section, and featured prompts.
- **Navigation Bar**: Includes a logo, search bar, navigation menu, and a sign-in/sign-up button.
- **Hero Section**: Contains a title, description, and a "Get Started" button.
- **Featured Prompts Section**: Showcases a selection of popular or recommended prompts.

#### Implementation Details

- **Framework**: Utilize Next.js 14 for server-side rendering and routing.
- **UI Components**: Use ShadCN components styled with Tailwind CSS.
- **Icons**: Implement Lucid Icons for visual elements in the navigation and prompts.

### Epic 2: User Authentication

#### User Stories

- **Sign-Up**: As a user, I want to sign up using my email and password or Google account.

  - The system checks if the user already exists.
  - If not, a new user is created in the `profiles` collection.
  - If the user exists, proceed and pass the `user_id` to functions like creating prompts.
  - Upon successful sign-up, redirect the user to the homepage.

- **Sign-In**: As a user, I want to sign in using my email and password or Google account.

  - The system checks if the user exists.
  - If the user exists, proceed and pass the `user_id` to functions.
  - If not, create a new user in the `profiles` collection.
  - Upon successful sign-in, redirect the user to the homepage.

- **Sign-Out**: As a user, I want to sign out of my account.
  - Upon sign-out, redirect the user to the homepage.

#### Implementation Details

- **Authentication Library**: Use NextAuth.js for handling authentication flows.
- **Database**: Store user data in MongoDB.
- **Data Validation**: Utilize Zod schemas to validate user inputs during sign-up and sign-in processes.

### Epic 3: Create Operation for Prompts

#### User Story

- **Creating a Prompt**: As a logged-in user, I want to create a new prompt by providing a title, description, prompt content, and the intended AI tool.

### Epic 4: Read Operation for Prompts

#### User Story

- **Reading a Prompt**: As a user, I want to view a prompt's details, including its title, description, content, and associated tool.

### Epic 5: Update Operation for Prompts

#### User Story

- **Updating a Prompt**: As a user, I want to edit an existing prompt that I have created.

### Epic 6: Delete Operation for Prompts

#### User Story

- **Deleting a Prompt**: As a user, I want to delete a prompt that I have created.

### Epic 7: Search Operation for Prompts

#### User Story

- **Searching Prompts**: As a user, I want to search for prompts using keywords, categories, or tool names.

#### Implementation Details

- **Database**: Use MongoDB's querying capabilities to search for prompts.
- **Data Validation**: Utilize Zod to validate search queries and parameters.

---

## Proposed File Structure

The project is organized to maintain clarity and efficiency, minimizing the number of files while ensuring scalability.

```
promptopia-ai
├── README.md
├── next-env.d.ts
├── next.config.mjs
├── package.json
├── package-lock.json
├── postcss.config.mjs
├── tailwind.config.ts
├── tsconfig.json
├── .env             // Environment variables (API keys, database URLs)
├── app
│   ├── favicon.ico
│   ├── globals.css
│   ├── layout.tsx
│   ├── page.tsx
│   ├── components
│   │   ├── Navbar.tsx
│   │   ├── Footer.tsx
│   │   ├── PromptCard.tsx
│   │   ├── LoginButton.tsx
│   │   └── PromptForm.tsx
│   ├── prompts
│   │   ├── [id].tsx      // Dynamic route for individual prompt pages
│   │   ├── create.tsx
│   │   └── edit.tsx
│   └── profile
│       └── [username].tsx  // Dynamic route for user profiles
├── pages
│   └── api
│       ├── auth
│       │   └── [...nextauth].js
│       ├── prompts
│       │   ├── index.ts      // Handles GET and POST requests
│       │   └── [id].ts       // Handles GET, PUT, DELETE requests for a prompt
│       └── search.ts
└── lib
    ├── db.ts
    └── validations.ts
```

### Explanation of Structure

- **app/**: Contains all the front-end pages and components.
  - **components/**: Reusable UI components like Navbar, Footer, PromptCard, etc.
  - **prompts/**: Pages related to prompt operations (viewing, creating, editing).
  - **profile/**: User profile pages.
- **pages/api/**: Contains API routes for backend logic.
  - **auth/**: Authentication routes using NextAuth.js.
  - **prompts/**: API endpoints for prompt CRUD operations.
  - **search.ts**: API endpoint for search functionality.
- **lib/**: Utility functions and configurations.
  - **db.ts**: MongoDB connection setup.
  - **validations.ts**: Zod schemas for data validation.

---

## Documentation and Code Examples

### Using NextAuth.js for Authentication

**Adding an API Route for NextAuth.js**

Create a file at `pages/api/auth/[...nextauth].js`:

```javascript
import NextAuth from "next-auth";
import GithubProvider from "next-auth/providers/github";

export const authOptions = {
  // Configure one or more authentication providers
  providers: [
    GithubProvider({
      clientId: process.env.GITHUB_ID,
      clientSecret: process.env.GITHUB_SECRET,
    }),
    // ...add more providers here
  ],
};

export default NextAuth(authOptions);
```

**Checking if a User is Logged In**

Create a `LoginButton` component in `app/components/LoginButton.tsx`:

```jsx
import { useSession, signIn, signOut } from "next-auth/react";

export default function LoginButton() {
  const { data: session } = useSession();
  if (session) {
    return (
      <>
        Signed in as {session.user.email} <br />
        <button onClick={() => signOut()}>Sign out</button>
      </>
    );
  }
  return (
    <>
      Not signed in <br />
      <button onClick={() => signIn()}>Sign in</button>
    </>
  );
}
```

### Simplifying API Routes

**Handling Multiple Operations in a Single API Route**

Instead of separate files for each CRUD operation, use HTTP methods within a single file.

Create `pages/api/prompts/[id].ts`:

```typescript
import { NextApiRequest, NextApiResponse } from "next";
import { getSession } from "next-auth/react";
import db from "../../../lib/db";
import { promptSchema } from "../../../lib/validations";

export default async (req: NextApiRequest, res: NextApiResponse) => {
  const session = await getSession({ req });
  const { id } = req.query;

  if (!session) {
    return res.status(401).json({ message: "Unauthorized" });
  }

  switch (req.method) {
    case "GET":
      // Read prompt by ID
      break;
    case "PUT":
      // Update prompt by ID
      break;
    case "DELETE":
      // Delete prompt by ID
      break;
    default:
      res.setHeader("Allow", ["GET", "PUT", "DELETE"]);
      res.status(405).end(`Method ${req.method} Not Allowed`);
  }
};
```

---

## Additional Implementation Details

### Authentication Flow

- **Providers**: Configure NextAuth.js with providers such as Email/Password and Google.
- **Session Management**: Use `useSession` hook from NextAuth.js to manage user sessions.
- **Protected Routes**: Implement server-side checks to protect routes and API endpoints.

### Database Configuration

- **MongoDB Connection**: Use the `mongoose` library to connect to MongoDB in `lib/db.ts`.
- **Models**: Define schemas for users and prompts using Mongoose models.

### Data Validation with Zod

- **Validation Schemas**: Create Zod schemas in `lib/validations.ts` for user inputs and prompt data.
- **Usage**: Apply these schemas in API routes to validate request bodies.

### UI Components

- **Navbar**: Includes logo, navigation links, search bar, and authentication buttons.
- **Footer**: Contains site links and information.
- **PromptCard**: Displays prompt information in lists and featured sections.
- **PromptForm**: Used for creating and editing prompts, includes form validation.

### Styling and Theming

- **Tailwind CSS**: Utilize Tailwind for utility-first CSS styling.
- **ShadCN UI**: Use pre-built components from ShadCN for consistency.
- **Responsive Design**: Ensure the app is responsive across devices.

### Environmental Variables

- **.env File**: Store sensitive information like API keys and database URIs.
  - Include `.env` in `.gitignore` to prevent it from being pushed to version control.

---

## Visuals

### Homepage Mockup

![Homepage Mockup](<Screenshot 2024-10-07 at 2.59.26 PM.png>)

---

**Note to Developers**: Ensure to follow best practices in coding and security, particularly when handling user data and authentication flows. Regularly refer to this PRD throughout the development process for guidance.
