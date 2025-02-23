# OpenUI Technical Documentation

## Core Architecture

### Frontend (React + TypeScript)

#### Component Hierarchy
```
App
├── Chat (Main UI container)
│   ├── Prompt (User input)
│   ├── HTMLAnnotator (Preview system)
│   │   ├── CodeViewer (Monaco editor)
│   │   └── CurrentUIContext (UI state)
│   └── ShareDialog (Sharing functionality)
└── Components
    ├── UI primitives (button, input, etc.)
    └── Framework-specific components
```

#### State Management
- Jotai atoms for global state:
  - `uiStateAtom`: Current UI state and prompt
  - `historyAtomFamily`: Version history tracking
  - `uiThemeAtom`: Theme management
  - `modelSupportsImagesAtom`: Model capability detection

#### Key Features
1. Real-time Preview
   - IFrame-based rendering
   - Theme switching support
   - Responsive design testing (mobile/tablet/desktop)
   - Code editing with Monaco

2. Framework Conversion
   - HTML to React/Svelte/Web Components
   - Tailwind CSS preservation
   - Component structure maintenance

### Backend (FastAPI + Python)

#### API Structure
```
/v1
├── chat/completions (LLM interaction)
│   ├── OpenAI models (gpt-3.5-turbo, gpt-4-turbo)
│   ├── Groq models (fast inference)
│   ├── Ollama models (local deployment)
│   └── LiteLLM providers (Gemini, Claude, Cohere, Mistral)
├── models (Available models)
│   ├── GET: List available models
│   └── Model capability detection
├── session (Session management)
│   ├── GET: Retrieve session data
│   ├── DELETE: End session
│   └── Token usage tracking
├── share (Component sharing)
│   ├── POST /{id}: Create shared component
│   └── GET /{id}: Retrieve shared component
├── vote (Feedback system)
│   └── POST: Submit component feedback
├── login (OAuth initiation)
└── callback (OAuth completion)
```

#### Core Services

1. LLM Integration
   - Multiple provider support:
     - OpenAI (GPT-3.5, GPT-4)
     - Groq (fast inference)
     - Ollama (local models)
     - LiteLLM providers:
       - Gemini
       - Claude
       - Cohere
       - Mistral
   - Token usage tracking
   - Error handling and rate limiting

2. Session Management
   - OAuth authentication via GitHub
   - Token usage tracking
   - Usage quotas and limits
   - Session persistence

3. Database Layer (Peewee ORM)
   ```python
   class User(BaseModel):
       id = BinaryUUIDField(primary_key=True)
       username = CharField()
       email = CharField(null=True)
       created_at = DateTimeField()

   class Session(BaseModel):
       id = BinaryUUIDField(primary_key=True)
       user = ForeignKeyField(User)
       created_at = DateTimeField()
       updated_at = DateTimeField()
       data = JSONField()

   class Component(BaseModel):
       id = BinaryUUIDField(primary_key=True)
       user = ForeignKeyField(User)
       name = CharField()
       data = JSONField()

   class Vote(BaseModel):
       id = BinaryUUIDField(primary_key=True)
       user = ForeignKeyField(User)
       component = ForeignKeyField(Component)
       vote = BooleanField()
       created_at = DateTimeField()

   class Usage(BaseModel):
       input_tokens = IntegerField()
       output_tokens = IntegerField()
       user = ForeignKeyField(User)
       created_at = DateTimeField()
   ```

## Core Workflows

### 1. UI Generation Flow
1. User submits prompt through chat interface
2. Backend validates session and token usage
3. Request routed to appropriate LLM provider
4. Generated HTML/CSS streamed back to frontend
5. Real-time preview with Monaco editor
6. Support for iterative refinement via annotations

### 2. Framework Conversion
1. HTML/CSS code parsed and analyzed
2. Framework-specific transformation rules applied
3. Component structure maintained
4. Tailwind classes preserved
5. Framework-specific features utilized:
   - React: Hooks, JSX
   - Svelte: Reactive statements
   - Web Components: Shadow DOM

### 3. Session Management
1. GitHub OAuth authentication
2. Session creation and persistence
3. Token usage tracking
4. Usage quota enforcement
5. User data management

### 4. Error Handling
1. Validation Errors
   ```python
   @app.exception_handler(RequestValidationError)
   @app.exception_handler(ValidationError)
   async def validation_exception_handler(request, exc):
       # Handle request validation errors
   ```

2. Provider Errors
   ```python
   @app.exception_handler(ClientError)
   async def boto3_error_handler(request, exc):
       # Handle AWS/S3 errors
   ```

3. Generic Errors
   ```python
   @app.exception_handler(Exception)
   async def generic_exception_handler(request, exc):
       # Handle unexpected errors
   ```

## Development Setup

### Local Development
1. Frontend
   - Vite + React
   - TypeScript
   - Tailwind CSS
   - Monaco Editor

2. Backend
   - FastAPI
   - Uvicorn
   - SQLite/PostgreSQL
   - LiteLLM

### Testing
1. Frontend
   - React Testing Library
   - Jest
   - Component tests

2. Backend
   - pytest
   - Integration tests
   - API tests

### Deployment
1. Frontend
   - Static hosting
   - CDN distribution
   - Asset optimization

2. Backend
   - API deployment
   - Database migrations
   - Environment configuration
