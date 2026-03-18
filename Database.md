erDiagram

    ROLES {
        BIGINT id PK
        VARCHAR name
    }

    USERS {
        BIGINT id PK
        VARCHAR full_name
        VARCHAR email
        VARCHAR phone
        VARCHAR password_hash
        BOOLEAN is_active
        DATETIME created_at
        DATETIME updated_at
    }

    USER_ROLES {
        BIGINT user_id PK, FK
        BIGINT role_id PK, FK
    }

    REFRESH_TOKENS {
        BIGINT id PK
        BIGINT user_id FK
        VARCHAR token_hash
        DATETIME expires_at
        DATETIME revoked_at
        DATETIME created_at
    }

    OWNERS {
        BIGINT id PK
        BIGINT user_id FK
        VARCHAR company_name
        VARCHAR br_number
        VARCHAR address
        DATETIME created_at
    }

    DRIVERS {
        BIGINT id PK
        BIGINT owner_id FK
        VARCHAR full_name
        VARCHAR phone
        VARCHAR nic
        VARCHAR license_no
        BOOLEAN is_active
        DATETIME created_at
    }

    ROUTES {
        BIGINT id PK
        VARCHAR code
        VARCHAR name
        VARCHAR status
        BIGINT created_by_user_id FK
        DATETIME created_at
        DATETIME updated_at
    }

    STOPS {
        BIGINT id PK
        VARCHAR name
        DECIMAL latitude
        DECIMAL longitude
        POINT location
        DATETIME created_at
    }

    ROUTE_STOPS {
        BIGINT route_id PK, FK
        BIGINT stop_id PK, FK
        INT stop_order
    }

    BUSES {
        BIGINT id PK
        BIGINT owner_id FK
        VARCHAR plate_no
        VARCHAR bus_no
        INT capacity
        BOOLEAN is_active
        DECIMAL last_lat
        DECIMAL last_lng
        POINT last_location
        DATETIME last_location_updated_at
        DATETIME created_at
        DATETIME updated_at
    }

    BUS_ROUTE_ASSIGNMENTS {
        BIGINT id PK
        BIGINT bus_id FK
        BIGINT route_id FK
        DATE start_date
        DATE end_date
        BOOLEAN is_active
        DATETIME created_at
    }

    BUS_DRIVER_ASSIGNMENTS {
        BIGINT id PK
        BIGINT bus_id FK
        BIGINT driver_id FK
        DATE start_date
        DATE end_date
        BOOLEAN is_active
        DATETIME created_at
    }

    TRIPS {
        BIGINT id PK
        BIGINT bus_id FK
        BIGINT route_id FK
        BIGINT driver_id FK
        VARCHAR status
        DATETIME scheduled_start
        DATETIME scheduled_end
        DATETIME actual_start
        DATETIME actual_end
        DATETIME created_at
    }

    FEEDBACK {
        BIGINT id PK
        BIGINT user_id FK
        BIGINT bus_id FK
        BIGINT trip_id FK
        TINYINT rating
        VARCHAR category
        TEXT message
        VARCHAR status
        DATETIME created_at
        DATETIME updated_at
    }

    INCIDENTS {
        BIGINT id PK
        BIGINT reported_by_user_id FK
        BIGINT bus_id FK
        BIGINT trip_id FK
        VARCHAR type
        TEXT description
        VARCHAR status
        BIGINT resolved_by_user_id FK
        TEXT resolved_note
        DATETIME resolved_at
        DATETIME created_at
        DATETIME updated_at
    }

    WALLETS {
        BIGINT id PK
        BIGINT user_id FK
        BIGINT balance_cents
        VARCHAR currency
        DATETIME created_at
        DATETIME updated_at
    }

    WALLET_TRANSACTIONS {
        BIGINT id PK
        BIGINT wallet_id FK
        VARCHAR type
        BIGINT amount_cents
        VARCHAR reference
        JSON metadata
        DATETIME created_at
    }

    FARE_PAYMENTS {
        BIGINT id PK
        BIGINT user_id FK
        BIGINT bus_id FK
        BIGINT trip_id FK
        BIGINT amount_cents
        DATETIME paid_at
        VARCHAR payment_method
        VARCHAR status
        VARCHAR reference
    }

    REVENUE_DAILY {
        BIGINT id PK
        BIGINT owner_id FK
        BIGINT bus_id FK
        DATE revenue_date
        BIGINT total_amount_cents
        INT transactions_count
        DATETIME created_at
    }

    USERS ||--o{ USER_ROLES : has
    ROLES ||--o{ USER_ROLES : assigned_to

    USERS ||--o{ REFRESH_TOKENS : owns
    USERS ||--|| OWNERS : becomes
    OWNERS ||--o{ DRIVERS : manages
    OWNERS ||--o{ BUSES : owns

    USERS ||--o{ ROUTES : creates

    ROUTES ||--o{ ROUTE_STOPS : contains
    STOPS ||--o{ ROUTE_STOPS : included_in

    BUSES ||--o{ BUS_ROUTE_ASSIGNMENTS : assigned_to
    ROUTES ||--o{ BUS_ROUTE_ASSIGNMENTS : receives

    BUSES ||--o{ BUS_DRIVER_ASSIGNMENTS : assigned_driver
    DRIVERS ||--o{ BUS_DRIVER_ASSIGNMENTS : assigned_bus

    BUSES ||--o{ TRIPS : performs
    ROUTES ||--o{ TRIPS : follows
    DRIVERS ||--o{ TRIPS : drives

    USERS ||--o{ FEEDBACK : submits
    BUSES ||--o{ FEEDBACK : receives
    TRIPS ||--o{ FEEDBACK : linked_to

    USERS ||--o{ INCIDENTS : reports
    USERS ||--o{ INCIDENTS : resolves
    BUSES ||--o{ INCIDENTS : involves
    TRIPS ||--o{ INCIDENTS : linked_to

    USERS ||--|| WALLETS : owns
    WALLETS ||--o{ WALLET_TRANSACTIONS : contains

    USERS ||--o{ FARE_PAYMENTS : makes
    BUSES ||--o{ FARE_PAYMENTS : receives
    TRIPS ||--o{ FARE_PAYMENTS : linked_to

    OWNERS ||--o{ REVENUE_DAILY : has
    BUSES ||--o{ REVENUE_DAILY : contributes
