# Agentic AI Cloud Backend

**Agentic AI for Cloud Operations** is an intelligent automation platform that empowers cloud operators to manage OpenStack infrastructure through natural language commands. Designed for DevOps teams and cloud administrators, this solution combines large language models (LLMs) with OpenStack's robust API ecosystem to enable intuitive infrastructure management. Simply describe your cloud operations in plain English (e.g., _"Create an S.4 VM named dev-box with 100GB storage"_), and the system automatically translates your intent into secure, validated OpenStack API calls - complete with confirmation workflows and real-time resource monitoring. Built with Python, FastAPI, and modern AI frameworks like LangChain, this enterprise-ready solution features JWT authentication, audit logging, and seamless integration with OpenStack services (Nova, Neutron, Cinder). Whether you're managing private clouds or hybrid infrastructure, Agentic AI brings human-like understanding to cloud operations while maintaining full API-level control and compliance.

<img src="assets/Cloudops screenshot.png">
# Architecture diagram
<img src="assets/Archtiture diagram final.jpg">


# Workflow Diagram
<img src="assets/Workflow Ai_agent.png">


```markdown
# Agentic AI for Cloud Operations - Backend Documentation

## Overview
The backend system processes natural language commands, executes OpenStack operations, and manages secure workflows. Built with Python/FastAPI, it integrates NLP processing, cloud API orchestration, and real-time communication.

## Architecture Diagram
```mermaid
flowchart TD
    A[User Command] --> B[NLP Processing]
    B --> C{Requires Confirmation?}
    C -->|Yes| D[Pending Actions]
    C -->|No| E[Execute API]
    D --> F[User Confirmation]
    F -->|Confirm| E
    F -->|Cancel| G[Action Aborted]
    E --> H[OpenStack APIs]
    H --> I[Update Database]
    I --> J[Real-Time Updates]
```

## Key Features
- Natural language to OpenStack API translation
- User confirmation workflows
- Real-time resource monitoring
- JWT authentication
- Action logging & audit trails

## API Endpoints

### Process Command
```http
POST /api/commands
Content-Type: application/json

{
  "text": "Create S.4 VM named dev-box",
  "user_id": "admin-123"
}
```

### Confirm Action
```http
POST /api/confirm
Content-Type: application/json

{
  "confirmation_id": "uuid-1234",
  "decision": "confirm"
}
```

### Get Resources
```http
GET /api/resources
Authorization: Bearer <token>
```

## Database Schema
```sql
CREATE TABLE command_logs (
  id UUID PRIMARY KEY,
  user_id VARCHAR(255) NOT NULL,
  action VARCHAR(50) NOT NULL,
  parameters JSONB NOT NULL,
  status VARCHAR(20) NOT NULL,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);
```

## Development Setup

### Requirements
- Python 3.10+
- PostgreSQL 14+
- Redis 6+

### Installation
```bash
# Clone repository
git clone https://github.com/yourrepo/cloudops-ai.git
cd cloudops-ai/backend

# Create virtual environment
python -m venv venv
source venv/bin/activate

# Install dependencies
pip install -r requirements.txt
```

### Configuration
Create `.env` file:
```ini
OPENSTACK_AUTH_URL=https://api.yourcloud.com/v3
OPENSTACK_USERNAME=admin
OPENSTACK_PASSWORD=*****
OPENAI_API_KEY=sk-****
JWT_SECRET=your-secret-key-here
DATABASE_URL=postgresql://user:pass@localhost/cloudops
```

### Running
```bash
uvicorn main:app --reload --port 8000
```

## Deployment
```yaml
# docker-compose.prod.yml
version: '3.8'

services:
  backend:
    image: yourregistry/cloudops-backend:1.0
    environment:
      - OPENSTACK_AUTH_URL=${OS_AUTH_URL}
      - OPENAI_API_KEY=${OPENAI_KEY}
    ports:
      - "8000:8000"
    depends_on:
      - redis
      - db

  redis:
    image: redis:alpine

  db:
    image: postgres:14
    environment:
      POSTGRES_PASSWORD: ${DB_PASSWORD}
```

## Testing
```bash
# Unit tests
pytest tests/unit/

# Integration tests
pytest tests/integration/

# API tests
pytest tests/api/
```

## Monitoring Endpoints
```http
GET /health
GET /metrics
```

## License
Apache 2.0 License - See [LICENSE](LICENSE)

## Project Structure
```
Ai-cloud-agent/
├── Frontend/
├── app/
│   ├── api/
│   │   └── v1/
│   │       ├── nlp_routes.py
│   │       ├── cloud_routes.py
│   │       └── confirm_routes.py
│   ├── core/
│   │   ├── config.py
│   │   └── logger.py
│   ├── services/
│   │   ├── nlp_engine.py
│   │   ├── cloud_executor.py
│   │   └── confirmer.py
│   ├── db/
│   │   ├── models.py
│   │   ├── session.py
│   │   └── crud.py
│   ├── schemas/
│   │   ├── nlp.py
│   │   ├── cloud.py
│   │   └── base.py
│   ├── utils/
│   │   ├── validators.py
│   │   └── openstack_auth.py
│   └── main.py
├── nlp_model/
│   ├── data/
│   ├── domain.yml
│   ├── config.yml
│   └── actions/
│       └── custom_actions.py
├── tests/
│   ├── conftest.py
│   ├── test_nlp.py
│   ├── test_api.py
│   └── test_openstack.py
├── .env
├── requirements.txt
├── Dockerfile
├── docker-compose.yml
└── .github/
    └── workflows/
        └── ci.yml
```
