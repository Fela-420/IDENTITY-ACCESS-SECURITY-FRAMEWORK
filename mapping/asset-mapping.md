# ATTACK SURFACE MAPPING FRAMEWORK

## 1. DOMAINS & SUBDOMAINS
Objective: Uncover the root organization’s identity footprints and map every public-facing host.

── Root Domain Identification
ASN & Registrar Lookups:
Query autonomous system numbers (ASNs) and WHOIS data matching the target organization name to find parent/subsidiary root domains.

Reverse WHOIS & Ad-Hoc Relationships:
Search historical registration data using organization names or contact emails. Map shared tracking IDs (Google Analytics/AdSense) across separate root domains.

── Passive Subdomain Harvesting
Certificate Transparency (CT) Logs:
Search public CT logs using tools like crt.sh to extract every subdomain issued an SSL/TLS certificate.

Passive DNS Aggregators:
Query passive DNS databases (VirusTotal, SecurityTrails, Shodan) to find historical subdomains that may no longer be actively linked but remain live.

── Active Subdomain Enumeration
DNS Brute-Forcing:
Execute massive dictionary attacks against the target domain using highly optimized wordlists (e.g., best-dns-wordlist.txt) and high-speed resolvers (using massdns or puredns).

Permutation Fuzzing:
Generate variations of discovered subdomains by fuzzing prefixes and suffixes.

DNS Zone Transfers & Wildcard Detection:
Check for misconfigured DNS servers allowing AXFR zone transfers. Identify wildcard DNS records (*.target.com) to prevent false-positive discovery loops.


## 2. IP ADDRESSES & PORTS
Objective: Convert human-readable domains into physical infrastructure endpoints and map their access gates.

── IP Space Resolving & Mapping
DNS Resolution Matrix:
Resolve every discovered domain and subdomain to its respective IPv4 and IPv6 addresses.

IP Netblock Ownership:
Identify if target IPs belong to dedicated corporate netblocks (owned by the target) or shared hosting/cloud provider blocks.

CDN & WAF Layer Identification:
Determine if an IP points directly to origin servers or if it is shielded behind a CDN/WAF layer (Cloudflare, Akamai, AWS CloudFront).

── Port Discovery & Protocol Fingerprinting
Full-Range Port Scanning:
Scan all 65,535 TCP ports and critical UDP ports on discovered infrastructure using high-speed scanners (masscan or naabu).

Service Version Fingerprinting:
Probe open ports using nmap -sV to grab banners and identify the underlying service version (e.g., OpenSSH 8.9p1 Ubuntu).

Non-Standard Web Ports:
Flag atypical ports serving HTTP/HTTPS protocols (e.g., 8080, 8443, 9000, 5000, 3000) for closer inspection.


## 3. WEB APPLICATIONS & APIS
Objective: Profile the actual software engines driving the discovered endpoints.

── Web Application Fingerprinting
Technology Stack Profiling:
Inspect HTTP response headers (X-Powered-By, Server) and framework indicators (React components, Angular data attributes) using tools like Wappalyzer CLI.

Directory & Content Fuzzing:
Run systematic directory brute-forcing using context-driven wordlists to locate unlinked interfaces, backup files (.bak, .zip), configuration files (.env), and code repositories (/.git/).

SPA Router Mapping:
Analyze frontend JavaScript chunks (app.js, main.js) and .js.map source maps to pull hidden client-side routes (e.g., /admin-dashboard, /beta-features).

── API Surface Mapping
API Gateway Discovery:
Map API endpoints by parsing traffic and code for paths like /api/v1/, /v2/, or /rest/.

Schema & Documentation Harvesting:
Scan for exposed documentation endpoints, including Swagger UI (/swagger-ui.html, /api/docs) or OpenAPI specifications (/v2/api-docs).

GraphQL Introspection:
Check for endpoints listening at /graphql or /query and execute introspection queries to map queries, mutations, and types.


## 4. MOBILE APPLICATIONS
Objective: Map mobile ecosystems to extract hardcoded endpoints communicating back to the core infrastructure.

── Public Store Extraction
App Store Harvesting:
Search the Apple App Store and Google Play Store for all applications published under the target organization’s developer accounts.

Third-Party APK Repositories:
Pull older or regional variants of the applications from public mirrors (APKPure, APKMirror) to map legacy API connectivity.

── Static Binary Decompilation
APK/IPA Decompilation:
Decompile application binaries using jadx (for Android) or Cutter (for iOS).

String and Endpoint Extraction:
Extract embedded URLs, API endpoints, development/staging links, and third-party gateways using regex matching.

Secret Harvesting:
Search the decompiled source code for hardcoded API keys, Firebase database URLs, AWS access tokens, and private development credentials.


## 5. CLOUD ASSETS & THIRD-PARTY INTEGRATIONS
Objective: Map resources hosted outside standard corporate data centers and pinpoint external data pipelines.

── Cloud Infrastructure Discovery
Storage Bucket Discovery:
Search for exposed cloud storage containers matching target naming schemes across providers.

AWS:
target-*.s3.amazonaws.com

Google Cloud:
target-*.storage.googleapis.com

Azure:
target-*.blob.core.windows.net

Cloud Provider Enumeration:
Map public cloud footprints associated with the target’s authorized corporate infrastructure.

Serverless Endpoint Mapping:
Locate serverless execution endpoints such as:
- AWS Lambda
- Google Cloud Functions
- Azure Functions

── Third-Party Integration Audit
SaaS Identity & Platform Mapping:
Identify external corporate services linked via subdomains and integrations, such as:
- Zendesk
- Jira
- Okta
- Other SaaS platforms

DOM Script Extraction:
Audit script tags inside web applications to catalog third-party integrations processing user data, such as:
- Stripe
- Mixpanel
- Salesforce

Email Infrastructure & Trust Relationships:
Parse SPF, DKIM, and DMARC DNS records to map third-party platforms authorized to send emails or act on behalf of the company, such as:
- SendGrid
- Mailchimp
- Other email delivery providers
 
 
