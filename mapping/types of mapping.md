# 8 TYPES OF SECURITY MAPPING
## Reconnaissance → Attack Surface → Authorization → Logic

This framework uses 8 complementary maps to understand a target before
deep vulnerability testing.

The goal is not simply to "find endpoints".

The goal is to build a model of:

    ASSETS → APPLICATIONS → ENDPOINTS → APIs → OBJECTS
          → IDENTITIES → ROLES → DATA/FLOWS

Each map answers a different security question.

──────────────────────────────────────────────────────────────────────
1. ASSET MAPPING
──────────────────────────────────────────────────────────────────────

QUESTION:
    What exists?

PURPOSE:
    Build the external attack surface.

MAP:

    Target
      ├── Domains
      ├── Subdomains
      ├── IP addresses
      ├── Ports
      ├── Web applications
      ├── APIs
      ├── Mobile applications
      ├── Cloud assets
      └── Third-party integrations

EXAMPLE:

    target.com
      ├── www.target.com
      ├── api.target.com
      ├── app.target.com
      ├── static.target.com
      └── dev.target.com

AUTOMATION:
    ██████████  HIGH

GOOD CANDIDATES FOR AUTOMATION:

    ├── Subdomain discovery
    ├── DNS enumeration
    ├── Certificate transparency
    ├── DNS resolution
    ├── IP correlation
    ├── Port/service discovery
    ├── HTTP probing
    ├── Technology fingerprinting
    └── Asset deduplication

HUMAN REASONING:

    ├── Which assets matter?
    ├── Which assets are related?
    ├── Which assets appear unusual?
    └── Which assets deserve manual attention?

OUTPUT:

    asset_map.json
    asset_inventory.csv


──────────────────────────────────────────────────────────────────────
2. APPLICATION MAPPING
──────────────────────────────────────────────────────────────────────

QUESTION:
    What can the application do?

PURPOSE:
    Understand functionality rather than infrastructure.

MAP:

    Application
      ├── Authentication
      ├── Registration
      ├── Password reset
      ├── Profile
      ├── Search
      ├── Products
      ├── Cart
      ├── Checkout
      ├── Orders
      ├── Wishlist
      └── Account management

EXAMPLE:

    ACCOUNT
      ├── View profile
      ├── Edit profile
      ├── Change password
      ├── Manage addresses
      ├── View orders
      └── Delete account

AUTOMATION:
    ███████░░░  MEDIUM-HIGH

AUTOMATABLE:

    ├── Crawl application
    ├── Extract links
    ├── Extract forms
    ├── Identify JavaScript routes
    ├── Parse OpenAPI/Swagger
    ├── Detect common application paths
    └── Build page/function inventory

HUMAN REASONING:

    ├── Understand business purpose
    ├── Identify sensitive functionality
    ├── Identify unusual workflows
    └── Determine which functions interact

OUTPUT:

    application_map.json


──────────────────────────────────────────────────────────────────────
3. ENDPOINT MAPPING
──────────────────────────────────────────────────────────────────────

QUESTION:
    What HTTP interfaces exist?

PURPOSE:
    Convert application functionality into concrete requests.

MAP:

    FUNCTION
       ↓
    HTTP REQUEST
       ↓
    METHOD + HOST + PATH
       ↓
    PARAMETERS
       ↓
    RESPONSE

EXAMPLE:

    View Order
       ↓
    GET /api/orders/{orderId}

    Update Address
       ↓
    PUT /api/addresses/{addressId}

    Add Product
       ↓
    POST /api/cart/items

RECORD:

    ├── Method
    ├── Host
    ├── Path
    ├── Query parameters
    ├── Body parameters
    ├── Headers
    ├── Authentication
    ├── Content-Type
    ├── Status code
    └── Response structure

AUTOMATION:
    █████████░  VERY HIGH

AUTOMATABLE:

    ├── Burp exports
    ├── Proxy traffic
    ├── Crawlers
    ├── JavaScript extraction
    ├── OpenAPI parsing
    ├── Postman collection parsing
    ├── Endpoint normalization
    └── Duplicate detection

HUMAN REASONING:

    ├── Which endpoints are security-sensitive?
    ├── Which parameters represent objects?
    ├── Which endpoints change state?
    └── Which endpoints deserve authorization testing?

OUTPUT:

    endpoint_map.json


──────────────────────────────────────────────────────────────────────
4. API / SERVICE MAPPING
──────────────────────────────────────────────────────────────────────

QUESTION:
    How is the backend architecture communicating?

PURPOSE:
    Understand APIs, services and trust boundaries.

MAP:

                    FRONTEND
                       │
                       ▼
                   API GATEWAY
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
       USER API     ORDER API    PRODUCT API
          │            │            │
          ▼            ▼            ▼
       USER DB      ORDER DB    PRODUCT DB

POSSIBLE DISCOVERY:

    ├── REST
    ├── GraphQL
    ├── WebSockets
    ├── API gateways
    ├── Microservices
    ├── Internal API references
    └── Third-party APIs

AUTOMATION:
    ████████░░  HIGH

AUTOMATABLE:

    ├── OpenAPI discovery
    ├── API endpoint extraction
    ├── GraphQL schema extraction
    ├── JavaScript API references
    ├── Host correlation
    ├── Request/response clustering
    └── API version detection

HUMAN REASONING:

    ├── Identify trust boundaries
    ├── Understand service relationships
    ├── Determine authentication differences
    ├── Identify inconsistent authorization
    └── Identify interesting cross-service flows

OUTPUT:

    api_map.json


──────────────────────────────────────────────────────────────────────
5. OBJECT / RESOURCE MAPPING
──────────────────────────────────────────────────────────────────────

QUESTION:
    What objects does the application protect?

THIS IS ONE OF THE MOST IMPORTANT MAPS FOR AUTHORIZATION TESTING.

MAP:

    USER
      │
      ├── userId
      ├── addressId
      ├── orderId
      ├── cartId
      └── wishlistId

    ORDER
      ├── orderId
      ├── userId
      ├── productId
      └── addressId

    PRODUCT
      └── productId

LOOK FOR IDENTIFIERS:

    ├── Numeric IDs
    ├── UUIDs
    ├── GUIDs
    ├── Slugs
    ├── Account IDs
    ├── Order IDs
    └── Resource IDs

AUTOMATION:
    ███████░░░  MEDIUM-HIGH

AUTOMATABLE:

    ├── Extract IDs from requests
    ├── Detect identifier patterns
    ├── Track where IDs appear
    ├── Correlate IDs between endpoints
    ├── Build object relationships
    └── Detect repeated object references

HUMAN REASONING:

    ├── Determine ownership
    ├── Determine sensitivity
    ├── Understand relationships
    ├── Identify cross-object operations
    └── Decide what authorization assumptions should exist

OUTPUT:

    object_map.json


──────────────────────────────────────────────────────────────────────
6. IDENTITY / SESSION MAPPING
──────────────────────────────────────────────────────────────────────

QUESTION:
    How does the application know who the user is?

MAP:

    USER
      │
      ▼
    LOGIN
      │
      ▼
    SESSION / TOKEN
      │
      ├── Cookie
      ├── JWT
      ├── Access token
      ├── Refresh token
      └── Session identifier
              │
              ▼
          API REQUEST

MAP:

    ├── Authentication endpoints
    ├── Session creation
    ├── Session termination
    ├── Cookies
    ├── Tokens
    ├── JWT claims
    ├── Refresh mechanisms
    ├── Password reset
    └── Account recovery

AUTOMATION:
    ███████░░░  MEDIUM-HIGH

AUTOMATABLE:

    ├── Extract cookies
    ├── Extract tokens
    ├── Decode JWT structure
    ├── Identify token locations
    ├── Correlate sessions
    └── Build authentication flow

HUMAN REASONING:

    ├── Understand identity lifecycle
    ├── Determine trust relationships
    ├── Understand account recovery
    └── Identify security-sensitive transitions

OUTPUT:

    identity_map.json


──────────────────────────────────────────────────────────────────────
7. ROLE / AUTHORIZATION MAPPING
──────────────────────────────────────────────────────────────────────

QUESTION:
    WHO can perform WHICH action on WHICH object?

THIS IS THE CORE AUTHORIZATION MAP.

MODEL:

                 SUBJECT
                    │
                    ▼
                  ROLE
                    │
                    ▼
                  ACTION
                    │
                    ▼
                  OBJECT
                    │
                    ▼
                RESOURCE

EXAMPLE:

    User A
      │
      └── Customer
             │
             └── READ
                   │
                   └── Order 123

CREATE A MATRIX:

                         User A    User B    Admin
    ------------------------------------------------
    Own profile             ✓         ✓        ✓
    Own order               ✓         ✓        ✓
    Other user's order      ?         ?        ✓
    Own address             ✓         ✓        ✓
    Other user's address    ?         ?        ✓

The '?' represents something that requires controlled verification.

AUTOMATION:
    ██████░░░░  MEDIUM

AUTOMATABLE:

    ├── Compare requests
    ├── Compare responses
    ├── Detect authorization differences
    ├── Build permission matrices
    ├── Correlate users to resources
    └── Identify endpoints requiring authorization

HUMAN REASONING:

    ██████████  CRITICAL

    ├── Is the action supposed to be allowed?
    ├── Who owns the object?
    ├── What should the role be able to do?
    ├── Is cross-user access legitimate?
    └── Does the behavior represent a vulnerability?

OUTPUT:

    authorization_matrix.csv


──────────────────────────────────────────────────────────────────────
8. DATA / FLOW / TRUST-BOUNDARY MAPPING
──────────────────────────────────────────────────────────────────────

QUESTION:
    Where does sensitive information go?

MAP:

                    USER
                     │
                     ▼
                  FRONTEND
                     │
                     ▼
                    API
                     │
          ┌──────────┼──────────┐
          ▼          ▼          ▼
       USER DATA   ORDERS    PAYMENT
          │          │          │
          └──────────┼──────────┘
                     ▼
                  DATABASE
                     │
                     ▼
             THIRD-PARTY SERVICE

TRACK:

    ├── Personal information
    ├── Account information
    ├── Addresses
    ├── Orders
    ├── Authentication data
    ├── Payment-related information
    └── Third-party transfers

AUTOMATION:
    ███████░░░  MEDIUM-HIGH

AUTOMATABLE:

    ├── Extract response fields
    ├── Identify JSON keys
    ├── Detect sensitive-looking fields
    ├── Track data between requests
    ├── Build request/response relationships
    └── Generate flow graphs

HUMAN REASONING:

    ├── Determine sensitivity
    ├── Determine whether exposure is intended
    ├── Understand trust boundaries
    ├── Determine whether data crosses security boundaries
    └── Assess actual security impact

OUTPUT:

    data_flow_map.json


══════════════════════════════════════════════════════════════════════
                    AUTOMATION SUMMARY
══════════════════════════════════════════════════════════════════════

MAP                         AUTOMATION        HUMAN REASONING
─────────────────────────────────────────────────────────────────────

1. Asset Mapping             ██████████         ██░░░░░░░░
2. Application Mapping       ███████░░░         ██████░░░░
3. Endpoint Mapping          █████████░         ████░░░░░░
4. API/Service Mapping       ████████░░         ██████░░░░
5. Object Mapping            ███████░░░         ████████░░
6. Identity Mapping          ███████░░░         ████████░░
7. Authorization Mapping     ██████░░░░         ██████████
8. Data/Flow Mapping         ███████░░░         ████████░░


══════════════════════════════════════════════════════════════════════
                         THE FRAMEWORK
══════════════════════════════════════════════════════════════════════

                 ┌─────────────────────┐
                 │       TARGET        │
                 └──────────┬──────────┘
                            │
                            ▼
                    ┌───────────────┐
                    │ 1. ASSET MAP  │
                    └───────┬───────┘
                            │
                            ▼
                  ┌───────────────────┐
                  │ 2. APPLICATION MAP│
                  └─────────┬─────────┘
                            │
                            ▼
                  ┌───────────────────┐
                  │ 3. ENDPOINT MAP   │
                  └─────────┬─────────┘
                            │
                            ▼
                    ┌──────────────┐
                    │ 4. API MAP   │
                    └──────┬───────┘
                           │
                           ▼
                   ┌───────────────┐
                   │ 5. OBJECT MAP  │
                   └───────┬───────┘
                           │
                           ▼
                 ┌──────────────────┐
                 │ 6. IDENTITY MAP  │
                 └────────┬─────────┘
                          │
                          ▼
                 ┌──────────────────┐
                 │ 7. AUTHZ MAP     │
                 └────────┬─────────┘
                          │
                          ▼
                 ┌──────────────────┐
                 │ 8. DATA/FLOW MAP │
                 └────────┬─────────┘
                          │
                          ▼
                 ┌──────────────────┐
                 │ ATTACK HYPOTHESES│
                 └────────┬─────────┘
                          │
                          ▼
                 ┌──────────────────┐
                 │ MANUAL VALIDATION│
                 └────────┬─────────┘
                          │
                          ▼
                    VULNERABILITY


══════════════════════════════════════════════════════════════════════
                       CORE PRINCIPLE
══════════════════════════════════════════════════════════════════════

AUTOMATION SHOULD ANSWER:

    "WHAT EXISTS?"

    "WHAT CONNECTS?"

    "WHAT CHANGED?"

    "WHERE IS THE EVIDENCE?"

HUMAN REASONING SHOULD ANSWER:

    "WHAT SHOULD HAPPEN?"

    "WHO SHOULD BE ALLOWED?"

    "WHY IS THIS INTERESTING?"

    "DOES THIS REPRESENT A SECURITY BOUNDARY FAILURE?"

Therefore:

    AUTOMATION → DISCOVERY + COLLECTION + CORRELATION

    HUMAN      → MODEL + HYPOTHESIS + VALIDATION

The objective is NOT to build a scanner.

The objective is to build a TARGET MODEL.

Once the target model is complete, vulnerability hunting becomes:

    MAP
      ↓
    FIND SECURITY BOUNDARY
      ↓
    FORM HYPOTHESIS
      ↓
    TEST
      ↓
    VALIDATE
      ↓
    REPORT

This prevents random endpoint hunting and gives the methodology
a repeatable path even when the target changes.
