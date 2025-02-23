# OpenUI Technical Documentation

## System Overview
OpenUI is a sophisticated UI generation platform that leverages Large Language Models (LLMs) to create responsive, accessible, and theme-compatible user interfaces. The system combines modern frontend technologies with a robust backend architecture to provide an interactive UI development experience.

### Key Features
1. AI-Powered UI Generation
   - Multiple LLM provider support
   - Real-time code generation
   - Interactive refinement
   - Framework conversion

2. Development Features
   - Live preview with Monaco editor
   - Theme customization
   - Responsive design testing
   - Component annotation

3. Security & Performance
   - OAuth authentication
   - Token usage tracking
   - Rate limiting
   - Efficient caching

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

## Technical Implementation Details

### 1. Stream Processing
- Real-time LLM response streaming
- WebSocket-based UI updates
- Efficient token tracking
- Error handling and recovery

### 2. Real-time Updates
- Monaco editor integration
- Live preview system
- Theme switching
- Responsive design testing

### 3. Resource Management
1. Token Usage
   - Daily quotas
   - Provider-specific tracking
   - Cost multipliers
   - Usage analytics

2. Database Optimization
   - Connection pooling
   - WAL journaling
   - Index optimization
   - Query efficiency

3. Cache Management
   - Browser caching
   - API response caching
   - Asset optimization
   - Memory efficiency

### 4. Security Implementation
1. Authentication Flow
   - GitHub OAuth integration
   - Session management
   - Token validation
   - Credential storage

2. Authorization System
   - Resource ownership
   - Access control
   - Rate limiting
   - Token quotas

3. Data Protection
   - Secure storage
   - Error handling
   - Audit logging
   - Input validation

## System Integration

### Component Communication
1. Frontend to Backend
   - REST API endpoints
   - WebSocket connections
   - Event streaming
   - Error handling

2. Backend to LLM
   - Provider routing
   - Response streaming
   - Token management
   - Error recovery

3. Database Integration
   - ORM mapping
   - Transaction management
   - Migration system
   - Backup strategy

### Performance Optimization
1. Frontend Performance
   - Code splitting
   - Lazy loading
   - Asset optimization
   - Cache management

2. Backend Efficiency
   - Connection pooling
   - Query optimization
   - Resource caching
   - Load balancing

3. Database Performance
   - Index optimization
   - Query tuning
   - Connection management
   - Cache strategy

## Reliability & Monitoring

### 1. Error Handling
- Graceful degradation
- Fallback strategies
- User feedback
- Recovery procedures

### 2. Logging System
- Application logs
- Error tracking
- Usage metrics
- Performance monitoring

### 3. Backup Strategy
- Database backups
- Configuration backups
- Recovery procedures
- Data integrity checks

### 4. Monitoring
- System health checks
- Performance metrics
- Usage analytics
- Error tracking

## Future Roadmap

### 1. Feature Enhancements
- Additional LLM providers
- Enhanced framework support
- Advanced theming
- Collaboration tools

### 2. Technical Improvements
- Performance optimization
- Security enhancements
- Monitoring improvements
- API extensions

### 3. Developer Experience
- Enhanced documentation
- Developer tools
- Plugin system
- API improvements

### 4. Infrastructure
- Scaling strategy
- Deployment automation
- Monitoring enhancements
- Backup improvements
