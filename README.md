flowchart TD
    %% Styling for a professional, aesthetic look
    classDef frontend fill:#1e40af,stroke:#60a5fa,stroke-width:2px,color:#fff
    classDef backend fill:#166534,stroke:#4ade80,stroke-width:2px,color:#fff
    classDef database fill:#b45309,stroke:#fbbf24,stroke-width:2px,color:#fff
    classDef ai fill:#6b21a8,stroke:#c084fc,stroke-width:2px,color:#fff
    
    %% Entities
    Citizen([👤 Citizen])
    Police([👮 Law Enforcement])

    subgraph Client [Frontend: React + Vite]
        CitizenUI[Citizen Application]:::frontend
        PoliceUI[Police Dashboard]:::frontend
    end

    subgraph API_Layer [Backend: Node.js + Express]
        Router[API Gateway / Router]:::backend
        Auth[JWT Authentication]:::backend
        ReportGen[PDF Report Generator]:::backend
        GraphEngine[Network Graph Engine]:::backend
    end

    subgraph AI_Processing [AI & Data Processing Layer]
        OCR[Tesseract.js OCR]:::ai
        LLM((Gemini / OpenRouter)):::ai
        Analyzer[Fraud Analysis Controller]:::ai
        RAG[LangChain RAG Assistant]:::ai
    end

    subgraph Storage [Data Layer]
        MySQL[(MySQL: Relational DB)]:::database
        VectorDB[(ChromaDB: Vectors)]:::database
    end

    %% Data Flow: Citizen Reporting
    Citizen -->|Text, URL, Image| CitizenUI
    CitizenUI -->|POST /api/analyze| Router
    Router --> Auth
    Auth -->|Validated| Analyzer
    
    %% Branching: Image vs Text
    Analyzer -- Image Payload --> OCR
    OCR -- Extracted Text --> Analyzer
    
    %% AI Analysis
    Analyzer -- Formatted Prompt --> LLM
    LLM -- JSON Risk Score & Reasons --> Analyzer
    
    %% Storage & Response
    Analyzer -- Save Complaint & Entities --> MySQL
    Analyzer -- Return Risk Data --> CitizenUI
    
    %% PDF Generation
    CitizenUI -->|GET /api/report| Router
    Router --> ReportGen
    ReportGen -- Query Data --> MySQL
    MySQL -- Complaint Data --> ReportGen
    ReportGen -- Return PDF --> CitizenUI

    %% Chatbot Flow
    CitizenUI -->|Chat Query| Router
    Router --> RAG
    RAG <-->|Fetch NCRB/RBI Docs| VectorDB
    RAG -- Context + Query --> LLM
    LLM -- Verified Answer --> RAG
    RAG -- Return Message --> CitizenUI

    %% Police Dashboard Flow
    Police -->|Login| PoliceUI
    PoliceUI -->|GET /api/analytics| Router
    Router --> GraphEngine
    GraphEngine -- JOIN Complaints & Entities --> MySQL
    MySQL -- Relational Nodes & Edges --> GraphEngine
    GraphEngine -- Render Scam Network --> PoliceUI
