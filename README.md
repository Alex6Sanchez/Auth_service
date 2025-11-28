graph TD
    %% --- Styles ---
    classDef auth fill:#f9f,stroke:#333,stroke-width:2px;
    classDef backend fill:#bbf,stroke:#333,stroke-width:2px;
    classDef ui fill:#ff9,stroke:#333,stroke-width:2px;
    classDef kc fill:#f96,stroke:#333,stroke-width:4px,color:white;

    %% --- Nodes ---
    subgraph Frontend_Layer
        UI_Login(Login UI):::ui
        UI_A(Module A Frontend):::ui
        UI_B(Module B Frontend):::ui
        Cookie(Shared Cookie .company.com):::ui
    end

    subgraph Backend_Layer
        Auth_Svc(Auth Service - The Mask):::auth
        Mod_A(Module A Backend - JWT Decoder):::backend
        Mod_B(Module B Backend - JWT Decoder):::backend
    end

    subgraph Identity_Provider
        KC(Keycloak Server):::kc
    end

    %% --- Links ---
    UI_Login --> Auth_Svc
    Auth_Svc --> KC
    Auth_Svc -- Set Cookie --> Cookie
    
    Cookie -.-> UI_A
    Cookie -.-> UI_B
    
    UI_A --> Mod_A
    UI_B --> Mod_B
    
    Mod_A -- Fetch Keys --> KC
    Mod_B -- Fetch Keys --> KC
    
    Auth_Svc -.-> Mod_A
    Auth_Svc -.-> Mod_B
