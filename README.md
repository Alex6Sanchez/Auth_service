```mermaid
graph TD
    %% --- Styles ---
    classDef auth fill:#f9f,stroke:#333,stroke-width:2px;
    classDef backend fill:#bbf,stroke:#333,stroke-width:2px;
    classDef db fill:#dfd,stroke:#333,stroke-width:2px;
    classDef ui fill:#ff9,stroke:#333,stroke-width:2px;
    classDef kc fill:#f96,stroke:#333,stroke-width:4px,color:white;

    %% --- Frontend Layer ---
    subgraph "Frontend Layer (Browser)"
        UI_Login[Login UI]:::ui
        UI_A[Module A Frontend]:::ui
        UI_B[Module B Frontend]:::ui
        
        %% Shared Storage
        Cookie[("🍪 Shared Cookie (JWT)<br/>Domain: .company.com")]:::ui
    end

    %% --- Backend Layer ---
    subgraph "Backend Layer (Spring Boot)"
        Auth_Svc[<b>Auth Service</b><br/>(The Mask / OAuth Client)]:::auth
        
        subgraph "Resource Server A"
            Mod_A[<b>Module A Backend</b><br/>(JWT Decoder)]:::backend
            DB_A[(Module A DB<br/>User Data + Roles)]:::db
        end
        
        subgraph "Resource Server B"
            Mod_B[<b>Module B Backend</b><br/>(JWT Decoder)]:::backend
            DB_B[(Module B DB<br/>User Data + Admin)]:::db
        end
    end

    %% --- Identity Provider ---
    subgraph "Identity Provider"
        KC[<b>Keycloak Server</b><br/>(Master User Record)]:::kc
    end

    %% --- Connections ---
    %% Login Flow
    UI_Login -- "1. Credentials" --> Auth_Svc
    Auth_Svc -- "2. Verify & Get Token" --> KC
    Auth_Svc -- "3. Set Encryption Cookie" --> Cookie
    
    %% SSO & Access Flow
    Cookie -.->|"Auto-Injects Token"| UI_A
    Cookie -.->|"Auto-Injects Token"| UI_B
    
    UI_A -- "4. API Call + Bearer Token" --> Mod_A
    UI_B -- "API Call + Bearer Token" --> Mod_B
    
    %% Validation & Sync
    Mod_A -- "Fetch Public Keys (JWKS)" --> KC
    Mod_B -- "Fetch Public Keys (JWKS)" --> KC
    
    Auth_Svc -.->|"Sync User Event"| Mod_A
    Auth_Svc -.->|"Sync User Event"| Mod_B
    
    Mod_A <--> DB_A
    Mod_B <--> DB_B
