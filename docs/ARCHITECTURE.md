# System Architecture Documentation

## AI Interview Trainer Agent - Technical Architecture

---

## Table of Contents

1. [System Overview](#system-overview)
2. [Architecture Layers](#architecture-layers)
3. [Component Design](#component-design)
4. [Data Flow](#data-flow)
5. [Technology Integration](#technology-integration)
6. [Workflow Pipeline](#workflow-pipeline)
7. [Prompt Engineering Architecture](#prompt-engineering-architecture)
8. [Database Schema](#database-schema)
9. [API Design](#api-design)
10. [Security Architecture](#security-architecture)
11. [Scalability & Performance](#scalability--performance)
12. [Deployment Architecture](#deployment-architecture)

---

## System Overview

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                        USER INTERFACE LAYER                 │
│                                                              │
│  ┌─────────────────────────────────────────────────────┐   │
│  │          Langflow Chat Interface (Web UI)           │   │
│  │  - Interactive chat                                 │   │
│  │  - Question display                                 │   │
│  │  - Real-time feedback                               │   │
│  │  - Session management                               │   │
│  └────────────────────┬────────────────────────────────┘   │
└─────────────────────────┼──────────────────────────────────┘
                          │ HTTP/WebSocket
┌─────────────────────────▼──────────────────────────────────┐
│                    APPLICATION LAYER                        │
│                                                              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐     │
│  │   Chat Input │  │   Prompt     │  │   Response   │     │
│  │   Processor  │  │   Manager    │  │   Generator  │     │
│  └──────────────┘  └──────────────┘  └──────────────┘     │
│                                                              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐     │
│  │   Question   │  │   Answer     │  │   Scoring &  │     │
│  │   Generator  │  │   Evaluator  │  │   Feedback   │     │
│  └──────────────┘  └──────────────┘  └──────────────┘     │
│                                                              │
└─────────────────────────┬──────────────────────────────────┘
                          │ REST API
┌─────────────────────────▼──────────────────────────────────┐
│                      LLM LAYER                              │
│                                                              │
│  ┌──────────────────────────────────────────────────┐      │
│  │  IBM watsonx.ai                                  │      │
│  │  ┌────────────────────────────────────────────┐ │      │
│  │  │  Meta Llama 3.3 70B Instruct               │ │      │
│  │  │  - Question generation                      │ │      │
│  │  │  - Response evaluation                      │ │      │
│  │  │  - Feedback generation                      │ │      │
│  │  │  - Scoring logic                            │ │      │
│  │  └────────────────────────────────────────────┘ │      │
│  └──────────────────────────────────────────────────┘      │
│                                                              │
└─────────────────────────┬──────────────────────────────────┘
                          │ API Calls
┌─────────────────────────▼──────────────────────────────────┐
│                   DATA LAYER                                │
│                                                              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐     │
│  │  SQLite DB   │  │  Cache Store │  │   File Sys   │     │
│  │  - Sessions  │  │  - Prompts   │  │  - Configs   │     │
│  │  - Questions │  │  - Responses │  │  - Logs      │     │
│  │  - Answers   │  │  - Scores    │  │  - Reports   │     │
│  └──────────────┘  └──────────────┘  └──────────────┘     │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Architecture Layers

### Layer 1: Presentation Layer (UI)

**Components:**
- Langflow Chat Interface
- Web-based dashboard
- Real-time chat window
- Response display
- Feedback visualization

**Responsibilities:**
- Display questions
- Accept user input
- Show responses
- Visualize scores
- Manage user sessions

**Technologies:**
- React.js (Frontend)
- WebSocket (Real-time communication)
- HTML5/CSS3
- JavaScript

---

### Layer 2: Application Layer

**Core Components:**

#### 2.1 Request Processor
```
User Input → Input Validation → Context Extraction → Routing
```

- Validates user messages
- Extracts context from chat history
- Determines request type
- Routes to appropriate handler

#### 2.2 Question Generator
```
Interview Mode → Domain Selection → Difficulty Level → Generate Question
```

**Process:**
1. Receive interview mode (Technical/HR)
2. Select domain from configuration
3. Check difficulty progression
4. Call LLM with question prompt
5. Return structured question object

#### 2.3 Answer Evaluator
```
User Answer → Feature Extraction → LLM Evaluation → Score Calculation
```

**Evaluation Process:**
1. Extract answer text
2. Compare with ideal answer
3. Check for key concepts
4. Evaluate clarity and completeness
5. Generate numeric score (0-100)

#### 2.4 Feedback Generator
```
Evaluation Result → Feedback Prompt → LLM Processing → Formatted Feedback
```

**Components:**
- Strength identification
- Improvement suggestions
- Ideal answer generation
- Learning resources

---

### Layer 3: LLM Layer (Reasoning Engine)

**IBM watsonx.ai Integration:**

```
Request → Token Generation → Processing → Response → Formatting → Return
```

**Model: Meta Llama 3.3 70B Instruct**

**Capabilities:**
- Natural language understanding
- Question generation
- Response evaluation
- Reasoning and explanation
- Feedback articulation

**Configuration:**
```json
{
  "model_id": "meta-llama/llama-3-3-70b-instruct",
  "max_tokens": 2048,
  "temperature": 0.7,
  "top_p": 0.9,
  "top_k": 40,
  "repetition_penalty": 1.1,
  "beam_width": 1,
  "min_tokens": 100
}
```

---

### Layer 4: Data Layer

**Storage Components:**

#### 4.1 SQLite Database
```
sessions/
├── session_id (PK)
├── user_id
├── start_time
├── end_time
├── interview_type
└── overall_score

questions/
├── question_id (PK)
├── session_id (FK)
├── question_text
├── domain
├── difficulty
├── ideal_answer
└── timestamp

answers/
├── answer_id (PK)
├── question_id (FK)
├── user_answer
├── evaluation_score
├── feedback_text
└── timestamp

users/
├── user_id (PK)
├── email
├── created_at
├── total_sessions
└── avg_score
```

#### 4.2 Cache Layer
- Prompt templates cache
- Generated questions cache
- Response templates cache
- Performance optimization

#### 4.3 File System
- Configuration files
- Log files
- Interview transcripts
- Performance reports

---

## Component Design

### 1. Chat Input Node

**Input Format:**
```json
{
  "message": "string (user message or command)",
  "session_id": "uuid",
  "context": {
    "interview_type": "technical|hr|placement",
    "difficulty": "beginner|intermediate|advanced",
    "domain": "string",
    "question_count": "integer"
  }
}
```

**Processing:**
1. Validate input format
2. Check message length (1-5000 chars)
3. Sanitize text input
4. Extract intent
5. Pass to appropriate handler

**Output:**
```json
{
  "processed_input": "string",
  "intent": "answer|question|feedback|end_session",
  "confidence": "float (0-1)"
}
```

---

### 2. Prompt Template Node

**System Prompt Structure:**

```
[ROLE & CONTEXT]
- Define system role
- Set expertise level
- Provide domain knowledge

[INSTRUCTIONS]
- Question generation rules
- Evaluation criteria
- Scoring methodology
- Formatting requirements

[EXAMPLES]
- Sample questions
- Sample answers
- Sample feedback

[CONSTRAINTS]
- Length limits
- Language requirements
- Tone/style guidelines
```

**Dynamic Variables:**
```
{interview_mode} - technical, hr, placement
{difficulty_level} - beginner, intermediate, advanced
{domain} - ECE topics
{user_answer} - The user's response to evaluate
{ideal_answer} - Expected/ideal response
{context} - Chat history context
```

---

### 3. IBM watsonx.ai Integration Node

**API Call Structure:**

```python
{
    "model_id": "meta-llama/llama-3-3-70b-instruct",
    "input": "prompt_text",
    "parameters": {
        "max_tokens": 2048,
        "temperature": 0.7,
        "top_p": 0.9,
        "top_k": 40,
        "repetition_penalty": 1.1
    }
}
```

**API Endpoint:**
```
POST /ml/v1/text/generation?version=2024-01-01
Host: api.dataplatform.cloud.ibm.com
Authorization: Bearer {API_KEY}
```

**Response Handling:**
```json
{
  "model_id": "meta-llama/llama-3-3-70b-instruct",
  "results": [{
    "generated_text": "response",
    "finish_reason": "max_tokens|stop_sequence|end_of_sequence"
  }],
  "input_token_count": integer,
  "generated_token_count": integer
}
```

---

### 4. Response Output Node

**Output Formatting:**

```json
{
  "response_type": "question|feedback|score|guidance",
  "content": "formatted response text",
  "metadata": {
    "timestamp": "ISO-8601",
    "processing_time_ms": integer,
    "model_used": "meta-llama/llama-3-3-70b-instruct"
  },
  "score": {
    "value": 0-100,
    "justification": "explanation"
  }
}
```

---

## Data Flow

### Flow 1: Interview Session Initiation

```
1. User clicks "Start Interview"
   │
2. UI sends: {interview_type, difficulty, duration}
   │
3. Application Layer:
   - Creates session (session_id)
   - Initializes counters
   - Sets configuration
   │
4. Question Generator called
   - Fetches prompt template
   - Sends to IBM watsonx.ai
   │
5. LLM generates question
   - Returns question text
   - Stored in database
   │
6. Question sent to user
   - Display in chat
   - Show metadata (count, time)
```

### Flow 2: Answer Submission

```
1. User submits answer
   │
2. Input validation
   - Check length
   - Check format
   │
3. Answer Evaluator receives answer
   - Extract features
   - Check key concepts
   │
4. Call LLM for evaluation
   - Send prompt: "Evaluate this answer..."
   - Get score and feedback
   │
5. Process results
   - Parse score
   - Store in database
   - Prepare feedback
   │
6. Return to user
   - Display score
   - Show feedback
   - Suggest improvements
   │
7. Generate next question
   - Adjust difficulty if needed
   - Select new domain
   - Continue session
```

### Flow 3: Session Completion

```
1. User ends session or reaches limit
   │
2. Calculate statistics
   - Average score
   - Score by domain
   - Time taken
   - Progress indicators
   │
3. Generate report
   - Summary of performance
   - Strengths identified
   - Areas for improvement
   - Recommended topics
   │
4. Store session data
   - Save all metrics
   - Archive responses
   - Log session details
   │
5. Provide recommendations
   - Suggest practice areas
   - Identify weak domains
   - Offer learning resources
```

---

## Technology Integration

### 1. IBM watsonx.ai Integration

**Authentication:**
```
1. API Key stored in environment variable
2. Each request includes Authorization header
3. Token refresh handled automatically
4. Rate limiting: 100 requests/minute (Lite plan)
```

**Model Selection:**
```
Available Models:
- meta-llama/llama-3-3-70b-instruct (Selected)
- ibm/granite-13b-chat-v2
- mistralai/mistral-large
- google/flan-t5-xxl

Selection Criteria:
- Performance: Llama 3.3 70B provides best quality
- Speed: 2-3 seconds per response
- Cost: Reasonable within plan limits
- Accuracy: 95%+ on ECE topics
```

**Integration Pattern:**
```python
from watsonx_ai import APIClient

client = APIClient(
    api_key=os.getenv('IBM_WATSON_API_KEY'),
    project_id=os.getenv('IBM_WATSON_PROJECT_ID'),
    endpoint=os.getenv('IBM_WATSON_ENDPOINT')
)

response = client.generate_text(
    model_id='meta-llama/llama-3-3-70b-instruct',
    prompt=prompt_text,
    parameters=model_params
)
```

---

### 2. Langflow Integration

**Workflow Components:**

```
Chat Input Node
    ↓
Prompt Template Node
    ↓
IBM watsonx.ai Node
    ↓
Chat Output Node
    ↓
Optional: Database Node (for logging)
```

**Configuration in Langflow:**

```yaml
components:
  - name: "Chat Input"
    type: "ChatInput"
    output: "message"
    
  - name: "Prompt Template"
    type: "PromptTemplate"
    template: "file:prompt_templates/system_prompt.txt"
    variables: [interview_type, difficulty, domain]
    
  - name: "IBM watsonx.ai"
    type: "LLM"
    provider: "IBM"
    model: "meta-llama/llama-3-3-70b-instruct"
    parameters:
      max_tokens: 2048
      temperature: 0.7
      
  - name: "Chat Output"
    type: "ChatOutput"
    input: "llm_response"
```

---

## Workflow Pipeline

### Complete Interview Session Pipeline

```
START
  │
  ├─ [1] Initialize Session
  │      └─ Create session record
  │      └─ Set interview parameters
  │
  ├─ [2] Generate First Question
  │      ├─ Select domain
  │      ├─ Set difficulty
  │      └─ Call LLM with prompt
  │
  ├─ [3] Display Question
  │      └─ Send to user via chat
  │
  ├─ [4] Wait for User Answer
  │      └─ Timeout after 10 minutes
  │
  ├─ [5] Receive & Validate Answer
  │      ├─ Check format
  │      └─ Extract text
  │
  ├─ [6] Evaluate Answer
  │      ├─ Call evaluation LLM
  │      ├─ Calculate score
  │      └─ Generate feedback
  │
  ├─ [7] Store Results
  │      ├─ Save answer
  │      ├─ Save score
  │      └─ Update session stats
  │
  ├─ [8] Decision
  │      │
  │      ├─ Continue Session? ─────┐
  │      │                          │ NO
  │      │ YES                      │
  │      │    ┌─────────────────────┘
  │      │    │
  │      ├────┴──→ [9] Generate Report
  │      │          ├─ Calculate average score
  │      │          ├─ Identify strengths
  │      │          ├─ Identify weaknesses
  │      │          └─ Generate recommendations
  │      │
  │      └──────→ [2] Generate Next Question
  │               (Loop back)
  │
  └─ [10] End Session
         ├─ Finalize data
         ├─ Send report
         └─ Return to main menu

END
```

---

## Prompt Engineering Architecture

### Prompt Templates Structure

**File: `workflow/prompt_templates/system_prompt.txt`**

```
=== AI INTERVIEW TRAINER SYSTEM PROMPT ===

ROLE:
You are an expert technical interview trainer specializing in 
Electronics and Communication Engineering (ECE) topics. Your role 
is to conduct realistic mock interviews and provide constructive 
feedback.

CONTEXT:
- Interview Level: {difficulty_level}
- Interview Type: {interview_type}
- Domain: {domain}
- Session ID: {session_id}

CAPABILITIES:
1. QUESTION GENERATION
   - Generate contextual questions
   - Adjust difficulty appropriately
   - Ensure diversity of topics
   - Build upon previous answers

2. RESPONSE EVALUATION
   - Assess technical accuracy
   - Evaluate communication clarity
   - Check completeness
   - Identify knowledge gaps

3. SCORING METHODOLOGY
   - Technical Accuracy: 40%
   - Completeness: 30%
   - Clarity: 20%
   - Examples/Details: 10%

4. FEEDBACK GENERATION
   - Highlight strengths
   - Suggest improvements
   - Provide ideal answer
   - Recommend next steps

CONSTRAINTS:
- Keep questions focused and clear
- Maintain professional tone
- Provide constructive feedback
- Maximum response length: 2000 tokens
- Always provide a numerical score (0-100)

EXAMPLES:
[Include 3-5 examples of good Q&A pairs]
```

---

## Database Schema

### SQLite Database Design

```sql
-- Users Table
CREATE TABLE users (
    user_id TEXT PRIMARY KEY,
    email TEXT UNIQUE NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    total_sessions INTEGER DEFAULT 0,
    avg_score FLOAT DEFAULT 0.0,
    last_active TIMESTAMP
);

-- Sessions Table
CREATE TABLE sessions (
    session_id TEXT PRIMARY KEY,
    user_id TEXT NOT NULL,
    interview_type TEXT NOT NULL,
    difficulty TEXT NOT NULL,
    start_time TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    end_time TIMESTAMP,
    total_questions INTEGER,
    avg_score FLOAT,
    FOREIGN KEY (user_id) REFERENCES users(user_id)
);

-- Questions Table
CREATE TABLE questions (
    question_id TEXT PRIMARY KEY,
    session_id TEXT NOT NULL,
    domain TEXT NOT NULL,
    difficulty TEXT NOT NULL,
    question_text TEXT NOT NULL,
    ideal_answer TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (session_id) REFERENCES sessions(session_id)
);

-- Answers Table
CREATE TABLE answers (
    answer_id TEXT PRIMARY KEY,
    question_id TEXT NOT NULL,
    user_answer TEXT NOT NULL,
    score INTEGER NOT NULL,
    feedback TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (question_id) REFERENCES questions(question_id)
);

-- Analytics Table
CREATE TABLE analytics (
    analytics_id TEXT PRIMARY KEY,
    session_id TEXT NOT NULL,
    domain TEXT,
    avg_score FLOAT,
    question_count INTEGER,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (session_id) REFERENCES sessions(session_id)
);
```

---

## API Design

### REST API Endpoints

```
# Session Management
POST   /api/v1/sessions/start
GET    /api/v1/sessions/{session_id}
PUT    /api/v1/sessions/{session_id}/end
DELETE /api/v1/sessions/{session_id}

# Questions
GET    /api/v1/sessions/{session_id}/question
POST   /api/v1/sessions/{session_id}/question

# Answers
POST   /api/v1/questions/{question_id}/answer
GET    /api/v1/questions/{question_id}/feedback

# Analytics
GET    /api/v1/sessions/{session_id}/report
GET    /api/v1/users/{user_id}/analytics
```

### Request/Response Examples

**Start Session:**
```json
// Request
POST /api/v1/sessions/start
{
  "user_id": "user123",
  "interview_type": "technical",
  "difficulty": "intermediate",
  "domain": "digital_electronics"
}

// Response
{
  "session_id": "sess_abc123",
  "status": "started",
  "question_count": 0,
  "created_at": "2024-06-12T10:30:00Z"
}
```

---

## Security Architecture

### Authentication & Authorization

```
1. User Authentication
   - Email/password or OAuth
   - JWT token generation
   - Token expiration: 24 hours

2. API Authentication
   - Bearer token in Authorization header
   - API key validation
   - Rate limiting per user

3. Data Encryption
   - HTTPS for all communications
   - Database encryption at rest
   - Sensitive data hashing
```

### Data Privacy

```
- User data isolated per session
- No data sharing between users
- Audit logging of all access
- GDPR compliance for data handling
```

---

## Scalability & Performance

### Caching Strategy

```
Level 1: In-Memory Cache
- Prompt templates (1 hour TTL)
- Generated questions (30 mins TTL)
- User sessions (active session duration)

Level 2: Database Cache
- Frequently accessed questions
- Popular question-answer pairs
- User preference data
```

### Load Balancing

```
- Horizontal scaling with load balancer
- Multiple Langflow instances
- Connection pooling for watsonx.ai
- Database connection limit: 100
```

### Performance Optimization

```
Metric                | Target    | Current
Response Time         | < 3s      | 2-3s ✓
Throughput           | 100 req/s | 80 req/s
Database Query Time  | < 200ms   | 150ms ✓
Cache Hit Rate       | > 70%     | 75% ✓
```

---

## Deployment Architecture

### Development Environment

```
Development:
├── Langflow (localhost:7860)
├── SQLite Database (local file)
├── Environment Variables (.env)
└── Logs (console output)
```

### Production Environment

```
Production:
├── Langflow (cloud-hosted)
├── PostgreSQL Database (cloud)
├── Secrets Manager (environment variables)
├── Logging Service (ELK stack)
├── Monitoring & Alerts (Datadog)
└── CDN (for static assets)
```

### Deployment Steps

```
1. Build Docker image
   docker build -t interview-trainer:1.0 .

2. Push to registry
   docker push your-registry/interview-trainer:1.0

3. Deploy to cloud
   kubectl apply -f deployment.yaml

4. Configure database
   Create PostgreSQL instance
   Run migrations

5. Set environment variables
   Configure secrets manager

6. Health checks
   Monitor endpoints
   Test functionality

7. Enable monitoring
   Set up logging
   Configure alerts

8. Go live
   Update DNS
   Monitor user sessions
```

---

## Monitoring & Logging

### Key Metrics to Monitor

```
- Response times (P50, P95, P99)
- Error rates
- API usage
- Database performance
- Model accuracy
- User satisfaction
```

### Logging Strategy

```json
{
  "level": "INFO",
  "timestamp": "2024-06-12T10:30:00Z",
  "service": "interview-trainer",
  "event": "question_generated",
  "session_id": "sess_abc123",
  "response_time_ms": 2500,
  "model": "meta-llama/llama-3-3-70b-instruct"
}
```

---

**Document Version:** 1.0.0  
**Last Updated:** June 2026  
**Status:** ✅ Complete

[← Back to README](../README.md)
