```mermaid
flowchart TB
    subgraph Data["Data Sources & Processing"]
        ContestSites["Contest Sites"]
        Scheduler["Scheduler"]
        Workers["RQ Workers"]
        Queue["Redis Queue"]
    end

    subgraph Core["Application Core"]
        DB[(PostgreSQL)]
        Django["Django"]
        Channels["Channels"]
    end

    subgraph Web["Web Layer"]
        Nginx["Nginx"]
        Static["Static"]
        Templates["Templates"]
        WebSocket["WebSocket"]
    end

    subgraph Ext["External Services"]
        Email["Email"]
        Telegram["Telegram"]
        OAuth["OAuth"]
    end

    subgraph Obs["Observability"]
        Metrics["Grafana"]
        Logs["Loki"]
        Errors["Sentry"]
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

    classDef core fill:#e1f5fe,stroke:#01579b
    classDef web fill:#f3e5f5,stroke:#4a148c
    classDef external fill:#fbe9e7,stroke:#bf360c
    classDef monitoring fill:#e8f5e9,stroke:#1b5e20
    
    class Scheduler,Queue,Workers,DB,Django,Channels core
    class WebSocket,Nginx,Static,Templates web
    class Email,Telegram,OAuth external
    class Loki,Sentry,Grafana monitoring
```

    classDef core fill:#e1f5fe,stroke:#01579b
    classDef web fill:#f3e5f5,stroke:#4a148c
    classDef external fill:#fbe9e7,stroke:#bf360c
    classDef monitoring fill:#e8f5e9,stroke:#1b5e20
    
    class Scheduler,Queue,Workers,DB,Django,Channels,WebSocket core
    class Nginx,Static,Templates,Users web
    class Email,Telegram,OAuth external
    class Grafana,Loki,Sentry monitoring
```
