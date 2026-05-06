
Internal dash - account management tab
Justrice beta
Popups

Text messaging SMS


---

ROI Attribution
Chloe stats - pull integrations - ROI card in analytics page

Lawbrokr lead magnet - capture lead details from existing landing page forms **ai magic optimization**

---

The following are enhancements that can be made to improve popups and also ensure that it has analytics so that it can support tracking detailed analytics.

1. Add support for all of the CTA buttons in the popups (ignoring those for closing it) to append to the URL the engaged_popup parameter with the popup identifier. This allows for tracking views and conversions and attributing it to the popup.
    
    1. For reference look at the clips.js implementation there is an `appendEngagedClipParam` function, you want to add a similar function for all of the popup CTA buttons to include as part of the URL the engaged_popup parameter and set the popup id
    2. For clips the token is the clip that is specified, this would be the same for popups where there can be a `popup` defined OR alternatively if none is defined then the Rails API will make a best effort to determine the default configured popup and will regardless return the `popup` as part of the response. For testing purposes if you want to update your Mock API to return the popup you can include in the JSON payload `"popup": "popup_69b8ffaed7bb57f394fad11f98a52945"`
    
2. By default if no popup is provided to the Rails API then it will attempt to determine the default popup configured for the law firm. You need to also add support where if a user explicitly configures the popup then that is used and provided to the API url. You can look at Clips and mimic the implementation for the API request as it would be the same for Pop Ups except Pop Ups attempts to return the default popup for the firm if none is provided.
    1. The following is an example configuration where a popup is explicitly provided, like how clips is done:

<script>
    window.LawbrokrConfig = {
        popup: 'popup_69b8ffaed7bb57f394fad11f98a52945'
    };
</script>

1. Add support for robust defaults to ensure a default popup is displayed if a user forgets to configure styles/fonts/colors for it.
    1. First look at the code for Clips for making the API request and ensure that for Pop Ups it's similar and handles performing the API requests and retrying on error and dealing with delayed initialization and other common challenges when operating in different website platform environments.
    2. Add support for a set of defaults that can be used if a user has not configured them for the Pop Ups, these should make the defaults match the first pop up theme (black and white theme) so that even if a user does not configure it and many fields are null the default pop up will still look good.
    3. NOTE: There are many cases where a user will not have a popup configured, similarly to how Clips is implemented, if no Popup is configured then there will be a `204` error code returned see the Clips implementation in `fetchClipDetails` for reference, you will want to stop retrying and not render the Popup as there has not been any configured.

---

# Justice Sync — Simple Overview

A high-level picture of how the pieces fit together.
For the detailed version, see the "Justice Sync — Architecture" section below.

> View in VS Code: **Cmd+Shift+V**. If diagrams show as text, install the
> "Markdown Preview Mermaid Support" extension.

## The big picture

```mermaid
flowchart LR
    User([User])
    API["Justice Sync API<br/>(FastAPI)"]
    AI["AI Chat Agents<br/>(Vertex AI / Gemini)"]
    Sources["Ad & Marketing Sources<br/>Google Ads · Meta · CallRail · DataForSEO"]
    DB[("PostgreSQL<br/>app data + chat history")]

    User -->|asks question| API
    API -->|chat| AI
    AI -->|reads data| DB
    Sources -->|sync| DB
    API -->|reads/writes| DB
```

## What each part does

```mermaid
flowchart TB
    subgraph Front["1 · Front door"]
      A[FastAPI app<br/>login, chat, dashboards]
    end

    subgraph Brain["2 · The brain"]
      B[AI agents that answer<br/>questions about your ad data]
    end

    subgraph Pipes["3 · The pipes"]
      C[Sync jobs that pull data<br/>from external ad platforms]
    end

    subgraph Store["4 · The memory"]
      D[(Postgres: ad metrics,<br/>users, chat history)]
      E[(Redis: cache)]
    end

    Front --> Brain
    Brain --> Store
    Pipes --> Store
    Front --> Store
```

## A chat request, simply

```mermaid
flowchart LR
    Q[User asks a question] --> R[API receives it]
    R --> S[AI agent picks the right tool]
    S --> T[Tool reads from Postgres]
    T --> U[AI writes an answer]
    U --> V[Answer + history saved]
    V --> W[Reply sent to user]
```

## Where things live

| Folder | What it is |
|---|---|
| `app/` | The FastAPI web service — routes, auth, settings, scheduler |
| `chat/` | The AI agents and chat history storage |
| `integrations/` | One folder per external data source (Google Ads, Meta, CallRail, DataForSEO, internal sync) |
| `schemas/` | SQL files that define the Postgres tables |
| `docker/` · `deploy/` | How to package and run it |
| `tests/` | Test suite |

---

# Justice Sync — Architecture

VS Code renders Mermaid in the built-in Markdown preview (Cmd+Shift+V). If a
diagram below appears as raw text, install the **"Markdown Preview Mermaid
Support"** extension (`bierner.markdown-mermaid`).

## 1. High-level system

```mermaid
flowchart LR
    User([Browser / Client])

    subgraph FastAPI["FastAPI service · app/app.py"]
        Auth[/auth · session cookies/]
        Chat[/chat/]
        ChatHist[/chat_history/]
        Conns[/connections/]
        GAds[/google_ads/]
        MAds[/meta_ads/]
        CR[/callrail/]
        SR[/semrush/]
        ISync[/internal_sync/]
        Users[/users/]
    end

    subgraph Agents["chat/ · Vertex AI agents (google-adk + google-genai)"]
        Root[agent.py · root agent]
        Analytics[analytics_agent]
        GAdsAg[google_ads_agent]
        MAdsAg[meta_ads_agent]
        CRAg[callrail_agent]
        DFSAg[dfs_agent]
        ConnAg[connections_agent]
    end

    subgraph Integrations["integrations/ · external data sources"]
        IGAds[google_ads]
        IMAds[meta_ads]
        ICR[callrail]
        IDFS[dataforseo]
        IConn[connections · encrypted creds]
        IISync[internal_sync]
    end

    subgraph Jobs["app/jobs/ · APScheduler"]
        Sched[Periodic syncs]
    end

    subgraph Data["Data stores"]
        PG[("Primary Postgres<br/>schemas/0001–0009")]
        IntPG[("Internal 'justice' Postgres<br/>read-only source")]
        Redis[("Redis · cache")]
    end

    subgraph External["External APIs"]
        Vertex["Vertex AI / Gemini"]
        GAdsAPI["Google Ads API"]
        MAdsAPI["Meta Ads API"]
        CRAPI["CallRail API"]
        DFSAPI["DataForSEO API"]
        Sentry["Sentry"]
    end

    User -->|HTTPS| FastAPI

    Auth --> PG
    Chat --> Root
    ChatHist --> PG
    Conns --> IConn
    GAds --> IGAds
    MAds --> IMAds
    CR --> ICR
    SR --> IDFS
    ISync --> IISync
    Users --> PG

    Root --> Analytics & GAdsAg & MAdsAg & CRAg & DFSAg & ConnAg
    Root -.->|LLM| Vertex
    Analytics & GAdsAg & MAdsAg & CRAg & DFSAg & ConnAg -.->|LLM| Vertex

    GAdsAg --> IGAds
    MAdsAg --> IMAds
    CRAg --> ICR
    DFSAg --> IDFS
    ConnAg --> IConn
    Analytics --> PG

    IGAds <--> GAdsAPI
    IMAds <--> MAdsAPI
    ICR <--> CRAPI
    IDFS <--> DFSAPI
    IGAds & IMAds & ICR & IDFS & IConn --> PG
    IISync --> IntPG
    IISync --> PG

    Sched --> IGAds & IMAds & ICR & IDFS & IISync

    FastAPI <--> Redis
    FastAPI -.->|errors| Sentry
```

## 2. Chat request lifecycle

```mermaid
sequenceDiagram
    autonumber
    participant U as User
    participant API as app/routers/chat.py
    participant W as chat/history/db_writer
    participant A as chat/agent.py (root)
    participant Sub as Sub-agent (e.g. google_ads_agent)
    participant I as integrations/<source>
    participant V as Vertex AI / Gemini
    participant DB as Postgres (primary)

    U->>API: POST /chat (message, conversation_id?)
    API->>W: insert user message
    W->>DB: INSERT chat_history.messages
    API->>A: run(message, history)
    A->>V: prompt + tool defs
    V-->>A: tool call (e.g. fetch ad spend)
    A->>Sub: delegate
    Sub->>I: db_reader / API client
    I->>DB: SELECT
    I-->>Sub: rows
    Sub-->>A: tool result
    A->>V: continue with tool result
    V-->>A: final answer
    A-->>API: response
    API->>W: insert justice message (+ tool metadata)
    W->>DB: INSERT chat_history.messages
    API-->>U: streamed / JSON answer
```

## 3. Repository layout

```mermaid
flowchart TB
    Root[justice-sync/]

    Root --> AppDir[app/]
    Root --> ChatDir[chat/]
    Root --> IntDir[integrations/]
    Root --> SchemaDir[schemas/]
    Root --> Scripts[scripts/]
    Root --> Tests[tests/]
    Root --> Docker[docker/ + docker-compose.yml]
    Root --> Deploy[deploy/ stack.yaml]
    Root --> Docs[README · DEPLOY_GCP · FEATURES]

    AppDir --> AppApp[app.py · FastAPI entrypoint]
    AppDir --> AppDb[db.py · SQLAlchemy + psycopg2]
    AppDir --> AppCfg[config/settings.py]
    AppDir --> AppAuth[auth/session.py]
    AppDir --> AppRouters[routers/*]
    AppDir --> AppJobs[jobs/ · APScheduler]

    ChatDir --> CAgent[agent.py · root]
    ChatDir --> CSub[*_agent.py · per-source]
    ChatDir --> CHist[history/ db_reader · db_writer · models]
    ChatDir --> CConn[db_connector.py]

    IntDir --> IGA[google_ads/]
    IntDir --> IMA[meta_ads/]
    IntDir --> ICR2[callrail/]
    IntDir --> IDFS2[dataforseo/]
    IntDir --> ICN[connections/ · encryption]
    IntDir --> IIS[internal_sync/]

    SchemaDir --> S1[0001 core_setup]
    SchemaDir --> S2[0002 google_ads]
    SchemaDir --> S3[0003 meta_ads]
    SchemaDir --> S4[0004 dataforseo]
    SchemaDir --> S5[0005 callrail]
    SchemaDir --> S6[0006 internal_data]
    SchemaDir --> S7[0007 law_firm_connections]
    SchemaDir --> S8[0008 chat_history]
    SchemaDir --> S9[0009 sessions]
```

## 4. Postgres schemas (primary DB)

```mermaid
erDiagram
    CONVERSATIONS ||--o{ MESSAGES : has
    USERS ||--o{ CONVERSATIONS : owns
    USERS ||--o{ SESSIONS : has
    USERS ||--o{ CONNECTIONS : owns
    CONNECTIONS ||--o{ GOOGLE_ADS_DATA : feeds
    CONNECTIONS ||--o{ META_ADS_DATA : feeds
    CONNECTIONS ||--o{ CALLRAIL_DATA : feeds
    CONNECTIONS ||--o{ DATAFORSEO_DATA : feeds

    CONVERSATIONS {
      uuid id PK
      bigint user_id
      text title
      timestamptz created_at
      timestamptz updated_at
    }
    MESSAGES {
      uuid id PK
      uuid conversation_id FK
      bigint user_id
      text role "user|justice"
      text content
      jsonb metadata
      timestamptz created_at
    }
    SESSIONS {
      text id PK
      bigint user_id
      timestamptz expires_at
    }
    CONNECTIONS {
      uuid id PK
      bigint user_id
      text source "google_ads|meta_ads|callrail|dataforseo"
      bytea encrypted_credentials
    }
```

## 5. Deployment targets

```mermaid
flowchart LR
    subgraph LocalDev["Local dev"]
        DC[docker-compose.yml]
        DC --> APIc[justice-sync API]
        DC --> PGc[(Postgres)]
        DC --> RDc[(Redis)]
    end

    subgraph Swarm["Self-hosted (deploy/stack.yaml)"]
        SS[Docker Swarm stack]
    end

    subgraph GCP["GCP (DEPLOY_GCP.md — recommended)"]
        AR[Artifact Registry]
        CR2[Cloud Run · API]
        CRJ[Cloud Run Jobs · syncs]
        CSch[Cloud Scheduler]
        CSQL[(Cloud SQL · Postgres)]
        MR[(Memorystore · Redis)]
        SM[Secret Manager]
        VAI[Vertex AI / Gemini]
        CB[Cloud Build]
    end

    CB --> AR --> CR2
    AR --> CRJ
    CSch --> CRJ
    CR2 --> CSQL
    CR2 --> MR
    CR2 --> SM
    CR2 -.-> VAI
    CRJ --> CSQL
```