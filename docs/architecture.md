# CLIST Architecture

## System Architecture Diagram

```mermaid
flowchart TB
    subgraph Data["Data Sources & Processing"]
        ContestSites["Contest Sites (External APIs)"]
        Scheduler["Scheduler (Cron + Django RQ)"]
        Workers["RQ Workers (Python Background Tasks)"]
        Queue["Redis Queue (Message Broker)"]
    end

    subgraph Core["Application Core"]
        DB[(PostgreSQL Database)]
        Django["Django (Python Web Framework)"]
        Channels["Django Channels (WebSocket Handler)"]
    end

    subgraph Web["Web Layer"]
        Nginx["Nginx (Web Server)"]
        Static["Static Files (CSS/JS/Images)"]
        Templates["Django Templates (HTML)"]
        WebSocket["Daphne (ASGI Server)"]
    end

    subgraph Ext["External Services"]
        Email["SMTP Server (Email)"]
        Telegram["Telegram Bot API (Notifications)"]
        OAuth["OAuth Providers (Authentication)"]
    end

    subgraph Obs["Observability"]
        Metrics["Grafana (Metrics Visualization)"]
        Logs["Loki (Log Aggregation)"]
        Errors["Sentry (Error Tracking)"]
    end

    %% Main Data Flow
    ContestSites --> Workers
    Workers <--> Queue
    Workers --> DB
    
    %% Web Flow
    Nginx --> Django
    Django --> DB
    Django --> Static
    Django --> Templates
    
    %% Integration Flow
    Django --> Ext
    WebSocket --> Channels
    Channels --> Queue

    %% Simple Monitoring
    Core --> Obs
    Data --> Obs

    classDef source fill:#dcedc8,stroke:#33691e
    classDef core fill:#bbdefb,stroke:#0d47a1
    classDef web fill:#e1bee7,stroke:#4a148c
    classDef external fill:#ffccbc,stroke:#bf360c
    classDef monitoring fill:#c8e6c9,stroke:#1b5e20

    class ContestSites,Scheduler,Workers,Queue source
    class DB,Django,Channels core
    class Nginx,Static,Templates,WebSocket web
    class Email,Telegram,OAuth external
    class Metrics,Logs,Errors monitoring
```

## Technology Stack Details

### Data Sources & Processing
- **Contest Sites**: External programming contest platforms accessed via HTTP APIs
- **Scheduler**: Combination of system Cron jobs and Django-RQ for scheduled tasks
- **RQ Workers**: Python-based background task processors using Django-RQ
- **Redis Queue**: Redis server acting as message broker and task queue

### Application Core
- **PostgreSQL**: Main relational database (v14.3)
- **Django**: Python web framework (with REST framework for APIs)
- **Channels**: Django Channels for WebSocket/real-time functionality

### Web Layer
- **Nginx**: High-performance web server and reverse proxy
- **Static Files**: Served through Nginx with compression
- **Templates**: Django template engine with Bootstrap
- **Daphne**: ASGI server for WebSocket handling

### External Services
- **Email**: SMTP server integration for notifications
- **Telegram**: Bot API integration for notifications and commands
- **OAuth**: Multiple OAuth providers for authentication (social login)

### Observability
- **Grafana**: Metrics visualization and dashboards
- **Loki**: Log aggregation and search
- **Sentry**: Real-time error tracking and monitoring

### Key Technologies Used
- Python 3.10
- Django
- PostgreSQL 14.3
- Redis
- Nginx
- Django RQ
- Django Channels
- Docker & Docker Compose
