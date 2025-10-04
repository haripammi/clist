flowchart LR
  %% External sources
  A[Contest Sites<br/>Input: HTTP API] 

  %% Scheduler triggers
  Scheduler[Scheduler<br/>Technology: Celery Beat / Cron<br/>Input: Time triggers<br/>Output: Runs Scraper Commands]

  %% Scrapers
  ScraperCmds[Scraper Commands<br/>Technology: Python scripts<br/>Input: Scheduler, Contest Sites<br/>Output: Pushes data to Redis Queue]

  %% Task Queue
  Queue[Redis Queue<br/>Technology: Redis<br/>Input: Scraper data<br/>Output: Tasks for Celery Workers]

  %% Workers
  Workers[Celery Workers<br/>Technology: Python, Celery<br/>Input: Tasks from Redis Queue<br/>Output: Write to Postgres DB, trigger Notifications, emit metrics/errors]

  %% Database
  DB[Postgres DB<br/>Technology: Postgres<br/>Input: Writes from Workers, Reads from Django Views<br/>Output: Contest/Event/Account data]

  %% Web/API
  Users[Users<br/>Input: Web/API requests] 
  Views[Django Views<br/>Technology: Python, Django<br/>Input: Users<br/>Output: HTML/JSON responses]
  Templates[HTML Templates<br/>Technology: Django templates<br/>Input: Views context<br/>Output: Rendered HTML]
  Static[Static Assets<br/>Technology: CSS/JS<br/>Input: Served by Views<br/>Output: Browser UI]

  %% Notifications
  Notify[Notification Workers<br/>Technology: Python<br/>Input: Contest events from Workers<br/>Output: Emails, Telegram messages, RSS feeds]
  Email[Email - SMTP]
  Telegram[Telegram Bot API]
  RSS[RSS Feeds]

  %% Observability
  Grafana[Grafana - Metrics<br/>Input: Metrics from Workers/Views]
  Netdata[Netdata - Metrics<br/>Input: Metrics from Workers/Views]
  Sentry[Sentry - Errors<br/>Input: Errors from Workers/Views]

  %% Flows
  Scheduler --> ScraperCmds
  A --> ScraperCmds
  ScraperCmds --> Queue
  Queue --> Workers
  Workers --> DB
  Workers --> Notify
  Users --> Views
  Views --> DB
  Views --> Templates
  Views --> Static
  Notify --> Email
  Notify --> Telegram
  Notify --> RSS
  Workers --> Grafana
  Views --> Grafana
  Workers --> Sentry
  Views --> Sentry
