# Programmatic Authorization & Delegated Access via OAuth 2.0 (M2M)

### Architectural Overview
To understand modern Identity and Access Management, a security engineer must enforce the industry's golden rule: **OpenID Connect (OIDC) is for Authentication (Identity), while OAuth 2.0 is strictly for Authorization (Access).**

In this implementation, I shifted away from user-facing identity flows to deploy an **OAuth 2.0 Machine-to-Machine (M2M) Client Credentials Flow**. This architecture bypasses interactive browser logins and session cookies entirely, enabling backend servers to establish secure, programmatic communication channels. Authentication is achieved using cryptographically secure client keys to exchange a raw grant directly for a stateless **Access Token**.

*   **Authorization Server:** Auth0 Tenant
*   **Client Application:** Internal Processing Engine (Backend Service)
*   **Resource Server (API):** Corporate Invoice API (`https://api.mycompany.com/invoices`)

---

## Implementation Steps & Architecture Configuration

### 1. Registering the Protected Resource (API Profile)
* Inside the Auth0 console, I initialized an API profile to act as our protected **Resource Server**, designating `https://api.mycompany.com/invoices` as the unique, immutable **Audience identifier**.
* **Defining Access Boundaries (RBAC):** Rather than allowing blanket entry, I configured granular permissions within the API by registering the specific scope: `read:invoices`. This defines the exact programmatic boundaries of what an authorized client can execute.

<img width="1470" height="956" alt="Screenshot 2026-07-09 at 8 24 09 AM" src="https://github.com/user-attachments/assets/c0e8ca3e-be6c-4d57-8a3f-969246ab5b27" />

<img width="1470" height="956" alt="Screenshot 2026-07-09 at 8 24 29 AM" src="https://github.com/user-attachments/assets/0e1681a3-b1da-4e51-a9e0-e6ac8c80e4cd" />

### 2. Initializing and Authorizing the Machine Client
* I created a **Machine-to-Machine (M2M)** Application profile named `Internal Processing Engine` to represent our backend server infrastructure.
* **Granting Delegated Access:** Upon initialization, I linked this client application directly to the `Corporate Invoice API`. Rather than granting full admin rights, I explicitly checked the box for the `read:invoices` scope to enforce a strict model of least privilege. This ensures the machine can only read data and is architecturally barred from altering or deleting resources.

<img width="1470" height="956" alt="Screenshot 2026-07-09 at 8 29 03 AM" src="https://github.com/user-attachments/assets/4d0c6af9-b9af-4141-afae-d89243b5ad03" />

---

### 3. Programmatic Token Request & API Exchange
* To simulate a server-to-server transaction, I utilized **Hoppscotch** to execute an outbound raw HTTP POST request directly to the identity provider's token endpoint (`/oauth/token`).
* **The Payload Construction:** I constructed a structured JSON payload passing four critical authentication parameters:
    * `client_id` & `client_secret`: The cryptographically secure credentials belonging to the machine application.
    * `audience`: The target API identifier (`https://api.mycompany.com/invoices`).
    * `grant_type`: Explicitly set to `client_credentials`, telling the server to process a backend handshake rather than an interactive user login.
* **The Handshake Result:** The authorization server verified the machine credentials, validated the scope grant, and returned an HTTP 200 OK containing a stateless **Access Token**.

<img width="1470" height="956" alt="Screenshot 2026-07-09 at 8 38 47 AM" src="https://github.com/user-attachments/assets/f441071c-606b-4ed8-9c92-ba190349a6a3" />

---

## Token Cryptographic Breakdown & Analysis

### 4. Auditing the Stateless Access Token
* To verify the contents of the issued authorization voucher, I copied the raw string from Hoppscotch and parsed it using **JWT.io**.
* **The Structural Analysis:** Inspecting the decoded JSON payload revealed the definitive architectural boundary between OAuth 2.0 and OIDC:
    *   **`aud` (Audience):** Points cleanly to `https://api.mycompany.com/invoices`, confirming that this token is cryptographically bound to my specific backend resource server.
    *   **`scope`:** Displays exactly `read:invoices`, demonstrating the precise permission set delegated to the machine client.
    *   **Total Identity Absence:** The payload contains zero human identifiers—no names, no user emails, and no profile data. 
* **The Core Takeaway:** This completes the architectural proof of concept. An **Access Token** is not an identity passport; it is a stateless hotel keycard. It does not care *who* the user is, only *what* specific resources the bearer is authorized to access. 

<img width="1470" height="956" alt="Screenshot 2026-07-09 at 8 41 52 AM" src="https://github.com/user-attachments/assets/793b0d52-6fc7-4119-aacf-201981d42d7f" />
