# ConflictCanvas

**Real-Time Collaboration. Intelligent Conflict Resolution. Zero Silent Data Loss.**

ConflictCanvas is a collaborative workspace for remote teams with intelligent version-based conflict resolution. When two users edit the same task simultaneously, ConflictCanvas detects the conflict instead of silently overwriting someone's work.

## Features

### Core Features
- **Real-Time Collaboration**: WebSocket-powered live updates across all connected clients
- **Intelligent Conflict Resolution**: Version-based conflict detection with smart merge capabilities
- **Project Management**: Create projects, manage tasks, track progress
- **Interactive Dashboard**: Responsive project and task charts with selectable details
- **AI Analyzer**: Prepare a project or task brief and open Claude, ChatGPT, NVIDIA Nemotron, or Codex
- **Kanban Board**: Drag-and-drop task management with status columns
- **Team Collaboration**: Real-time comments, file uploads, activity timeline
- **Online Presence**: See who's online and what they're working on

### Conflict Resolution
- **Version Tracking**: Every task has a version number that increments on each update
- **Conflict Detection**: Backend detects when baseVersion ≠ serverVersion
- **Smart Merge**: Automatically merges changes when users modify different fields
- **Conflict UI**: Visual interface for resolving same-field conflicts
- **Demo Mode**: Live conflict simulation for demonstrations

### Visual Design
- **3D Collaboration Scene**: Three.js visualization of team collaboration
- **Premium SaaS Interface**: Dark theme with glassmorphism and subtle gradients
- **Responsive Design**: Mobile navigation, charts, project forms, and conflict dialogs adapt to desktop, tablet, and phone widths
- **Smooth Animations**: GSAP-powered transitions and micro-interactions

### Dashboard and Projects
- Create projects from the dashboard or Projects page, including status, priority, deadline, and selected team members
- In demo mode, projects are saved in browser local storage and remain available after a page reload
- The task distribution chart summarizes task statuses; select a chart segment to see matching task names and counts
- The project progress chart summarizes project completion; select a bar to see its progress, status, and deadline
- Dashboard charts and project data refresh after a project is created
- On phones, use the menu button in the top bar to open the navigation sidebar

### AI Analyzer
- Open **AI Analyzer** from the sidebar or the dashboard shortcut
- Select a project and task, enter an analysis focus, and choose whether to include project/task context
- Review the generated brief before copying it or opening an agent
- Provider links open the provider's site in a new tab. Opening an agent also attempts to copy the brief to the clipboard
- Claude opens at `https://claude.ai/new`; ChatGPT at `https://chatgpt.com/`; Nemotron at NVIDIA's `https://build.nvidia.com/`; and Codex at `https://chatgpt.com/codex`
- Recent launches record the provider, selected project/task, and time in this browser's local storage; the history can be cleared from the Analyzer page
- The Analyzer prepares and tracks briefs locally; it does not call provider APIs or return AI-generated answers inside ConflictCanvas. Sign in to the provider in the opened tab and submit the copied brief there

## Technology Stack

### Frontend
- HTML5, CSS3, JavaScript ES6+
- Three.js (3D visualization)
- GSAP (animations)
- Chart.js (charts)
- Lucide Icons
- Google Fonts (Space Grotesk, Inter, JetBrains Mono)

### Backend
- Python 3.10+
- FastAPI
- PostgreSQL
- WebSockets
- JWT Authentication

## Project Structure

```
conflictcanvas/
├── frontend/
│   ├── index.html
│   ├── pages/           # HTML page templates
│   ├── css/             # Stylesheets
│   ├── js/              # JavaScript modules
│   ├── 3d/              # Three.js scenes
│   └── assets/          # Images, icons, models
├── backend/
│   ├── main.py          # FastAPI application
│   ├── database.py      # Database configuration
│   ├── models/          # SQLAlchemy models
│   ├── schemas/         # Pydantic schemas
│   ├── routes/          # API routes
│   ├── services/        # Business logic
│   └── auth/            # Authentication
├── database/
│   ├── schema.sql       # Database schema
│   └── seed.sql         # Seed data
├── requirements.txt
├── .env.example
└── README.md
```

## Getting Started

### Prerequisites
- Python 3.10+
- PostgreSQL 14+
- Node.js 18+ (for frontend development, optional)

### Backend Setup

1. Clone the repository:
```bash
git clone <repository-url>
cd conflictcanvas
```

2. Create a virtual environment:
```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

3. Install dependencies:
```bash
pip install -r requirements.txt
```

4. Configure environment variables:
```bash
cp .env.example .env
# Edit .env with your database credentials
```

5. Set up PostgreSQL:
```bash
createdb conflictcanvas
```

6. Run the backend:
```bash
cd backend
python main.py
```

The API will be available at `http://localhost:8000`

### Frontend Setup

1. Serve the frontend:
```bash
cd frontend
python -m http.server 3000
```

Or use any static file server of your choice.

2. Open `http://localhost:3000` in your browser

### Demo Mode

The frontend demo mode works without a backend and uses browser local storage for demo projects and AI launch history:

1. Open the application
2. Click "Try Live Conflict Demo" in the sidebar
3. Choose changes for two simulated users and start the simulation
4. If both users change the same field, choose which version to keep; if they change different fields, the changes are merged automatically

To analyze a task, open **AI Analyzer**, choose the project and task, review the brief, and open one of the listed providers. The brief is copied when clipboard access is available; otherwise, it remains visible for manual copying. Provider accounts and any AI usage are handled by the provider, not by this application.

## API Endpoints

### Authentication
- `POST /auth/register` - Register a new user
- `POST /auth/login` - Login and get JWT token
- `GET /auth/me` - Get current user

### Projects
- `GET /projects` - List all projects
- `POST /projects` - Create a new project
- `GET /projects/{id}` - Get project details
- `PUT /projects/{id}` - Update project
- `DELETE /projects/{id}` - Delete project

### Tasks
- `GET /projects/{id}/tasks` - List project tasks
- `GET /tasks/{id}` - Get task details
- `POST /tasks` - Create a new task
- `PUT /tasks/{id}` - Update task (with conflict detection)
- `DELETE /tasks/{id}` - Delete task

### Conflicts
- `GET /conflicts` - List all conflicts
- `GET /conflicts/{id}` - Get conflict details
- `POST /conflicts/{id}/resolve` - Resolve a conflict

### WebSocket
- `WS /ws/projects/{project_id}` - Real-time project updates

## Conflict Resolution

### How It Works

1. **Version Tracking**: Every task has a `version` field
2. **Base Version**: When a user opens a task, they capture the `baseVersion`
3. **Update Check**: When updating, the backend compares `baseVersion` with `serverVersion`
4. **Conflict Detection**: If versions don't match, a conflict is recorded
5. **Resolution**: Users choose to keep their version, the latest version, or smart merge

### Smart Merge

When users modify different fields:
- User A changes: `status = DONE`
- User B changes: `priority = CRITICAL`
- Result: Both changes are merged automatically

### True Conflict

When users modify the same field:
- User A changes: `status = DONE`
- User B changes: `status = IN_REVIEW`
- Result: Conflict UI opens for manual resolution

## Deployment

### Frontend (Vercel/Netlify)
1. Build the frontend (if using a build tool)
2. Deploy to Vercel or Netlify
3. Configure environment variables

### Backend (Render/Railway)
1. Deploy the FastAPI application
2. Set up PostgreSQL database
3. Configure environment variables
4. Configure CORS for your frontend domain

### Database (PostgreSQL Cloud)
- Use Supabase, Neon, or Railway PostgreSQL
- Update `DATABASE_URL` in environment variables

## Development

### Running Tests
```bash
# Backend tests
pytest backend/tests/

# Frontend tests (if configured)
npm test
```

### Code Style
- Backend: PEP 8, Black formatter
- Frontend: ESLint, Prettier

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

## License

MIT License - see LICENSE file for details

## Acknowledgments

- Three.js for 3D graphics
- FastAPI for the backend framework
- GSAP for animations
- Chart.js for data visualization
