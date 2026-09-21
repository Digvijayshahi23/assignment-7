# To-Do List App API Design

## Overview of CRUD Operations
This API design follows RESTful principles to fulfil the core CRUD (Create, Read, Update, Delete) operations for a To-Do List application. 
- **Create**: Add new tasks to the list.
- **Read**: Retrieve all tasks or a specific task by its identifier.
- **Update**: Modify an existing task's details or its completion status.
- **Delete**: Remove a task from the list.

## API Endpoints

### 1. Add Task (Create)
- **Endpoint URL:** `/api/v1/tasks`
- **HTTP Method:** `POST`
- **Description:** Creates a new task in the to-do list.
- **Expected Request Body:**
  ```json
  {
    "title": "Buy groceries",
    "description": "Milk, Bread, Eggs, and Butter" 
  }
  ```
  *(Note: `description` is optional, `title` is required).*
- **Logic for Unique Identifier:** The server will generate a unique identifier (e.g., UUID v4) upon receiving the request. It will also automatically set the `completed` status to `false` and generate a `createdAt` timestamp.
- **Expected Response (201 Created):**
  ```json
  {
    "id": "123e4567-e89b-12d3-a456-426614174000",
    "title": "Buy groceries",
    "description": "Milk, Bread, Eggs, and Butter",
    "completed": false,
    "createdAt": "2026-09-22T10:00:00Z"
  }
  ```
- **Reasoning:** A `POST` request to the base collection resource (`/api/v1/tasks`) is the standard RESTful way to create a new resource. The client only sends the data it knows (title/description), and the server is responsible for assigning the ID and initial state.

### 2. Get All Tasks (Read)
- **Endpoint URL:** `/api/v1/tasks`
- **HTTP Method:** `GET`
- **Description:** Retrieves a list of all tasks.
- **Expected Request Body:** None
- **Expected Response (200 OK):**
  ```json
  [
    {
      "id": "123e4567-e89b-12d3-a456-426614174000",
      "title": "Buy groceries",
      "description": "Milk, Bread, Eggs, and Butter",
      "completed": false,
      "createdAt": "2026-09-22T10:00:00Z"
    },
    {
      "id": "987fcdeb-51a2-43d7-9012-3456789abcde",
      "title": "Finish Assignment 7",
      "description": "Plan APIs for To-Do List app",
      "completed": true,
      "createdAt": "2026-09-21T15:30:00Z"
    }
  ]
  ```
- **Reasoning:** A `GET` request to the base collection retrieves the list of resources. This allows the frontend React app to fetch and display the initial state of the to-do list on load.

### 3. Get Single Task (Read)
- **Endpoint URL:** `/api/v1/tasks/:id`
- **HTTP Method:** `GET`
- **Description:** Retrieves the details of a specific task by its unique ID.
- **Expected Request Body:** None
- **Expected Response (200 OK):**
  ```json
  {
    "id": "123e4567-e89b-12d3-a456-426614174000",
    "title": "Buy groceries",
    "description": "Milk, Bread, Eggs, and Butter",
    "completed": false,
    "createdAt": "2026-09-22T10:00:00Z"
  }
  ```
- **Reasoning:** Useful if the React app has a detailed view for a specific task. By appending the `:id` parameter to the URL path, we identify the exact resource to fetch.

### 4. Update Task (Update)
- **Endpoint URL:** `/api/v1/tasks/:id`
- **HTTP Method:** `PUT` (or `PATCH` for partial updates)
- **Description:** Updates the properties of an existing task (e.g., editing the title or marking it as completed).
- **Expected Request Body:**
  ```json
  {
    "title": "Buy groceries and snacks",
    "description": "Milk, Bread, Eggs, Butter, and Chips",
    "completed": true
  }
  ```
- **Expected Response (200 OK):**
  ```json
  {
    "id": "123e4567-e89b-12d3-a456-426614174000",
    "title": "Buy groceries and snacks",
    "description": "Milk, Bread, Eggs, Butter, and Chips",
    "completed": true,
    "updatedAt": "2026-09-22T12:00:00Z"
  }
  ```
- **Reasoning:** `PUT` or `PATCH` to the specific resource URL (`/api/v1/tasks/:id`) modifies it. This will be used when the user clicks a checkbox to toggle completion or edits the text of a task.

### 5. Delete Task (Delete)
- **Endpoint URL:** `/api/v1/tasks/:id`
- **HTTP Method:** `DELETE`
- **Description:** Deletes a specific task from the to-do list.
- **Expected Request Body:** None
- **Expected Response (204 No Content):** (No body returned, just the status code)
- **Reasoning:** The `DELETE` method explicitly indicates the intent to remove the resource identified by the URL. This will be called when the user clicks the delete/trash icon next to a task.

## Potential Challenges in Implementation

1. **State Management Synchronization:** When the React frontend calls these APIs (e.g., POST to add a task), it must smoothly update the local UI state. If the API request fails, the UI needs to handle the error gracefully without leaving the user in an inconsistent state. Optimistic UI updates (updating the UI before the API confirms success) can be complex to implement robustly.
2. **Data Validation:** Ensuring the backend properly validates the request body (e.g., rejecting a `POST` request without a `title`, or ensuring `completed` is a boolean). The frontend also needs to mirror this validation.
3. **CORS (Cross-Origin Resource Sharing):** When developing locally, the React app (often running on port 3000 or 5173) and the backend API (e.g., port 5000) will be on different origins. The backend needs to be configured to accept requests from the frontend's origin.
4. **Error Handling:** Designing a consistent error response structure (e.g., always returning `{ "error": "Message" }` with appropriate 4xx/5xx status codes) so the frontend can display meaningful error messages to the user.
5. **Pagination/Filtering (Future-proofing):** As the to-do list grows, returning all tasks might become slow. Planning for query parameters like `/api/v1/tasks?completed=true` or pagination `/api/v1/tasks?page=1&limit=10` might be necessary later.
