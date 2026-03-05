# 🎨 SyncSpace - Visual Architecture Guide

## System Architecture Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                         CLIENT (Port 5173)                       │
│                    React + Vite + Redux Toolkit                  │
├─────────────────────────────────────────────────────────────────┤
│                                                                   │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐         │
│  │   Pages      │  │  Components  │  │   Redux      │         │
│  │              │  │              │  │              │         │
│  │ • Login      │  │ • Header     │  │ • authSlice  │         │
│  │ • Dashboard  │  │ • Sidebar    │  │ • workspace  │         │
│  │ • Kanban     │  │ • Notif.     │  │ • boardSlice │         │
│  │ • Workspace  │  │ • Layout     │  │ • taskSlice  │         │
│  └──────────────┘  └──────────────┘  └──────────────┘         │
│                                                                   │
│  ┌─────────────────────────────────────────────────┐           │
│  │           Services (API + Socket.IO)             │           │
│  │  • Axios HTTP Client                             │           │
│  │  • Socket.IO Client                              │           │
│  │  • Real-time Event Handlers                      │           │
│  └─────────────────────────────────────────────────┘           │
│                                                                   │
└────────────────┬──────────────────┬─────────────────────────────┘
                 │                  │
                 │ HTTP (REST)      │ WebSocket
                 │                  │
┌────────────────▼──────────────────▼─────────────────────────────┐
│                         SERVER (Port 5000)                       │
│                    Node.js + Express + Socket.IO                 │
├─────────────────────────────────────────────────────────────────┤
│                                                                   │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐         │
│  │   Routes     │  │ Controllers  │  │  Middleware  │         │
│  │              │  │              │  │              │         │
│  │ • /auth      │  │ • authCtrl   │  │ • auth.js    │         │
│  │ • /workspace │  │ • workspace  │  │ • upload.js  │         │
│  │ • /boards    │  │ • boardCtrl  │  │ • errorHand. │         │
│  │ • /tasks     │  │ • taskCtrl   │  │              │         │
│  │ • /docs      │  │ • docCtrl    │  │              │         │
│  │ • /chat      │  │ • chatCtrl   │  │              │         │
│  │ • /files     │  │ • fileCtrl   │  │              │         │
│  │ • /notify    │  │ • notifCtrl  │  │              │         │
│  └──────────────┘  └──────────────┘  └──────────────┘         │
│                                                                   │
│  ┌─────────────────────────────────────────────────┐           │
│  │           Socket.IO Event Handlers               │           │
│  │  • task-created, task-updated, task-moved        │           │
│  │  • document-change, chat-message                 │           │
│  │  • notification, typing                          │           │
│  └─────────────────────────────────────────────────┘           │
│                                                                   │
└────────────────────────────┬───────────────────────────────────┘
                             │
                             │ Mongoose ODM
                             │
┌────────────────────────────▼───────────────────────────────────┐
│                     MongoDB (Port 27017)                        │
│                         Database                                │
├─────────────────────────────────────────────────────────────────┤
│                                                                   │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐          │
│  │  Users   │ │  Teams   │ │Workspace │ │  Boards  │          │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘          │
│                                                                   │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐          │
│  │  Tasks   │ │ Documents│ │ Messages │ │  Files   │          │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘          │
│                                                                   │
│  ┌──────────┐ ┌──────────┐                                      │
│  │ Comments │ │ Notific. │                                      │
│  └──────────┘ └──────────┘                                      │
│                                                                   │
└─────────────────────────────────────────────────────────────────┘
```

---

## Data Flow Diagram

### 1. User Authentication Flow

```
┌──────────┐       ┌──────────┐       ┌──────────┐       ┌──────────┐
│  User    │       │  React   │       │  Express │       │ MongoDB  │
│  Browser │       │   App    │       │  Server  │       │   DB     │
└────┬─────┘       └────┬─────┘       └────┬─────┘       └────┬─────┘
     │                  │                   │                   │
     │  Enter Creds    │                   │                   │
     ├────────────────>│                   │                   │
     │                  │   POST /login    │                   │
     │                  ├─────────────────>│                   │
     │                  │                   │  Find User       │
     │                  │                   ├─────────────────>│
     │                  │                   │  User Data       │
     │                  │                   │<─────────────────┤
     │                  │                   │ Verify Password  │
     │                  │                   │ Generate JWT     │
     │                  │   JWT Token      │                   │
     │                  │<─────────────────┤                   │
     │  Display Dashboard                  │                   │
     │<─────────────────┤                   │                   │
     │                  │  Store Token     │                   │
     │                  │  in localStorage │                   │
```

### 2. Real-Time Task Update Flow

```
┌──────────┐       ┌──────────┐       ┌──────────┐       ┌──────────┐
│ User A   │       │ Socket.IO│       │  Express │       │ User B   │
│ Browser  │       │  Server  │       │  API     │       │ Browser  │
└────┬─────┘       └────┬─────┘       └────┬─────┘       └────┬─────┘
     │                  │                   │                   │
     │  Drag Task      │                   │                   │
     ├────────────────>│                   │                   │
     │                  │  PUT /tasks/:id  │                   │
     │                  ├─────────────────>│                   │
     │                  │                   │  Update DB       │
     │                  │   Updated Task   │                   │
     │                  │<─────────────────┤                   │
     │  Update UI      │                   │                   │
     │<─────────────────┤                   │                   │
     │                  │  Emit task-moved │                   │
     │                  ├──────────────────────────────────────>│
     │                  │                   │  Update UI (Live)│
     │                  │                   │                   │
```

---

## Component Hierarchy

```
App
├── BrowserRouter
│   └── Routes
│       ├── /login ────────────────> Login
│       ├── /register ─────────────> Register
│       └── / (PrivateRoute)
│           └── Layout
│               ├── Header
│               │   ├── Logo
│               │   ├── NotificationDropdown
│               │   └── UserMenu
│               ├── Sidebar
│               │   └── Navigation Links
│               └── Outlet (Main Content)
│                   ├── Dashboard
│                   │   ├── WorkspaceCard (multiple)
│                   │   └── CreateWorkspaceModal
│                   ├── WorkspaceDetail
│                   │   ├── Stats
│                   │   ├── RecentBoards
│                   │   ├── RecentDocuments
│                   │   └── TeamMembers
│                   ├── KanbanBoard
│                   │   ├── DragDropContext
│                   │   │   └── Column (multiple)
│                   │   │       └── TaskCard (multiple)
│                   │   └── CreateTaskModal
│                   ├── DocumentEditor
│                   ├── Chat
│                   └── Files
```

---

## Redux Store Structure

```javascript
{
  auth: {
    user: { _id, name, email, role, avatar },
    token: "jwt_token_here",
    isAuthenticated: true,
    loading: false,
    error: null
  },
  
  workspace: {
    workspaces: [{ _id, name, description, members, boards }],
    currentWorkspace: { _id, name, ... },
    loading: false,
    error: null
  },
  
  board: {
    boards: [{ _id, name, columns, workspace }],
    currentBoard: { _id, name, ... },
    loading: false,
    error: null
  },
  
  task: {
    tasks: [{ _id, title, columnId, order, priority }],
    currentTask: { _id, ... },
    loading: false,
    error: null
  },
  
  document: {
    documents: [{ _id, title, content, collaborators }],
    currentDocument: { _id, ... },
    loading: false,
    error: null
  },
  
  chat: {
    messages: [{ _id, content, sender, workspace }],
    typingUsers: [{ userId, userName }],
    loading: false,
    error: null
  },
  
  notification: {
    notifications: [{ _id, title, message, read }],
    unreadCount: 5,
    loading: false,
    error: null
  }
}
```

---

## Database Schema Relationships

```
User ──────────────┐
  │                │
  │ workspaces     │ teams
  │                │
  ▼                ▼
Workspace ◄───── Team
  │
  │ boards, documents, files
  │
  ├───────> Board
  │           │
  │           │ tasks
  │           │
  │           └───> Task
  │                   │
  │                   │ comments, assignees
  │                   │
  │                   └───> Comment
  │                           │
  │                           │ author, mentions
  │                           │
  │                           └───> User
  │
  ├───────> Document
  │           │
  │           │ collaborators, versions
  │           │
  │           └───> User
  │
  ├───────> File
  │           │
  │           │ uploadedBy, versions
  │           │
  │           └───> User
  │
  └───────> Message
              │
              │ sender, mentions
              │
              └───> User

Notification
  │
  │ recipient, sender
  │
  └───> User
```

---

## API Request/Response Examples

### Create Task

**Request:**
```http
POST /api/tasks
Authorization: Bearer jwt_token_here
Content-Type: application/json

{
  "title": "Implement user authentication",
  "description": "Add JWT-based auth system",
  "boardId": "board_id_here",
  "columnId": "todo",
  "priority": "high",
  "assignees": ["user_id_1", "user_id_2"]
}
```

**Response:**
```json
{
  "_id": "task_id_here",
  "title": "Implement user authentication",
  "description": "Add JWT-based auth system",
  "board": "board_id_here",
  "columnId": "todo",
  "order": 0,
  "priority": "high",
  "assignees": [
    {
      "_id": "user_id_1",
      "name": "John Doe",
      "email": "john@example.com",
      "avatar": ""
    }
  ],
  "createdBy": {
    "_id": "creator_id",
    "name": "Admin User",
    "email": "admin@example.com"
  },
  "createdAt": "2024-01-15T10:30:00.000Z",
  "updatedAt": "2024-01-15T10:30:00.000Z"
}
```

---

## Socket.IO Event Flow

### Task Movement Event

**Client Emits:**
```javascript
socket.emit('task-moved', {
  workspaceId: 'workspace_123',
  taskId: 'task_456',
  sourceColumnId: 'todo',
  destinationColumnId: 'in-progress',
  sourceIndex: 2,
  destinationIndex: 0
});
```

**Server Broadcasts:**
```javascript
socket.to(`workspace-${workspaceId}`).emit('task-moved', {
  taskId: 'task_456',
  sourceColumnId: 'todo',
  destinationColumnId: 'in-progress',
  sourceIndex: 2,
  destinationIndex: 0
});
```

**All Clients Receive:**
```javascript
socket.on('task-moved', (data) => {
  // Update Redux store
  dispatch(moveTaskRealtime(data));
  // UI updates automatically via React
});
```

---

## File Upload Flow

```
User Browser                 Frontend                 Backend                MongoDB
     │                          │                       │                      │
     │  Select File             │                       │                      │
     ├─────────────────────────>│                       │                      │
     │                          │ FormData with file   │                      │
     │                          ├──────────────────────>│                      │
     │                          │                       │ Multer middleware   │
     │                          │                       │ saves to /uploads   │
     │                          │                       │                      │
     │                          │                       │ Create File doc     │
     │                          │                       ├─────────────────────>│
     │                          │                       │  File saved         │
     │                          │                       │<─────────────────────┤
     │                          │  File metadata       │                      │
     │                          │<──────────────────────┤                      │
     │  Show success message   │                       │                      │
     │<─────────────────────────┤                       │                      │
     │                          │  Emit file-uploaded  │                      │
     │                          │  via Socket.IO       │                      │
     │                          ├──────────────────────>│                      │
     │                          │                       │ Broadcast to room   │
     │  Other users see new file│<──────────────────────┤                      │
```

---

## CSS Architecture

```
styles/
└── global.css
    ├── Reset & Base Styles
    ├── CSS Variables (Theme)
    │   ├── Colors
    │   ├── Shadows
    │   └── Border Radius
    ├── Typography
    ├── Utility Classes
    │   ├── Flexbox utilities
    │   ├── Spacing utilities
    │   └── Text utilities
    ├── Components
    │   ├── Buttons
    │   ├── Forms
    │   ├── Cards
    │   ├── Modals
    │   └── Badges
    └── Responsive Design

Component-specific CSS:
├── Layout.css
├── Header.css
├── Sidebar.css
├── NotificationDropdown.css
├── Dashboard.css
├── KanbanBoard.css
└── etc.
```

---

## Security Layers

```
┌─────────────────────────────────────────┐
│         FRONTEND SECURITY                │
├─────────────────────────────────────────┤
│ • Token stored in localStorage          │
│ • Automatic token refresh               │
│ • Protected routes (PrivateRoute)       │
│ • Input validation                      │
└─────────────────┬───────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────┐
│         NETWORK SECURITY                 │
├─────────────────────────────────────────┤
│ • HTTPS (production)                     │
│ • CORS configured                        │
│ • Rate limiting                          │
└─────────────────┬───────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────┐
│         BACKEND SECURITY                 │
├─────────────────────────────────────────┤
│ • JWT verification middleware            │
│ • Password hashing (bcrypt)              │
│ • Helmet.js security headers             │
│ • Input sanitization                     │
│ • Error handling                         │
└─────────────────┬───────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────┐
│         DATABASE SECURITY                │
├─────────────────────────────────────────┤
│ • Mongoose schema validation             │
│ • No password in queries (select: false)│
│ • Connection string in .env              │
└─────────────────────────────────────────┘
```

---

## Performance Optimizations

1. **Frontend**
   - Code splitting with React lazy loading
   - Redux Toolkit for optimized state updates
   - Memoization with React.memo
   - Debounced search inputs
   - Virtualized lists for large datasets

2. **Backend**
   - MongoDB indexing on frequently queried fields
   - Compression middleware
   - Connection pooling
   - Pagination on list endpoints
   - Selective field projection

3. **Real-Time**
   - Socket.IO rooms for targeted broadcasts
   - Optimistic UI updates
   - Debounced socket emissions
   - Connection state management

---

This visual guide helps you understand how all pieces fit together! 🎨
