## Intro
### What is OpenBao
- Key sprawl
- Auditability
- Access control
- Rotation
- Dynamic secrets
<br>
### What Engines we’re Demoing
- PKI
- Transit
- Database
- SSH

## Demos
### Demo 1 - CI/CD Deployment with SSH Certificates
- Deployed via **GitLab CI/CD** using **AppRole** authentication instead of static SSH keys
- Pipeline generates an ephemeral SSH key pair, gets it signed by OpenBao's **SSH CA** **engine**, and deploys using the certificate
- Certificate expires after 30 minutes — no long-lived credentials stored in **CI/CD** variables
- **CI/CD** variables only contain AppRole role\_id and secret\_id (no tokens, no SSH keys)
- ==Demo — CI/CD deploy changes to Web App, show Job Log with AppRole login + cert signing==
- ==Demo — Show dashboard SSH Certs tab with issued/expired certificates and requester identity==
- ==Demo — Show Audit Log tab with real-time activity events==
### Demo 2 - Dynamic Database Credentials
- Database credentials are managed by **OpenBao** — no static passwords
- Issued access is per user, and credentials expire after 1 hour (configurable)
- Our Web App uses **AppRole** to acquire database credentials from OpenBao at runtime
- Credentials rotate automatically — old users are revoked via DROP ROLE
- ==Demo — Get database credentials from OpenBao CLI, connect to PostgreSQL==
- ==Demo — Show Dashboard DB Leases tab with active leases, TTL countdowns, and rotation history==
### Demo 3 - Interactive SSH Access to Hosts
- **OpenBao** controls SSH access to hosts using its **SSH Certificate Authority**
- CA public key is trusted on target hosts (TrustedUserCAKeys in sshd config)
- Users request time-limited SSH certificates from OpenBao for specific principals
- The certificate is bound to the user's public key and expires automatically
- Same **SSH CA engine** used by **CI/CD** in Demo 1, but here we show the interactive user workflow
- ==Demo — User requests a certificate and connects to a host==
- ==Demo — Inspect certificate details: serial number, principals, validity period==
- ==Demo — Issue a short-lived cert (5 min) and show it expire==
### Demo 4 - PKI Engine and ACME Certificates
- **OpenBao** acts as an internal Certificate Authority (Root CA + Intermediate CA)
- ACME protocol support enabled — works with any standard ACME client (acme.sh, certbot)
- We use acme.sh which works with both public CAs (Let's Encrypt) and **OpenBao's** PKI engine
- ==Demo — Show self-signed cert on acme-demo.sdemo.tux42.au, replace it using ACME and OpenBao==
- ==Demo — Show issued certs on dashboard PKI tab==
### Demo 5 - Transit Engine: Binary Signing in CI/CD
- **OpenBao's** Transit engine signs binaries cryptographically without exposing keys
- Only the SHA-256 hash is sent to OpenBao — the full binary never leaves the CI runner
- Private signing keys never leave OpenBao — applications only interact via API
- Key rotation is supported — new versions don't invalidate old signatures
- ==Demo — Show how app-installer binary is built and signed through CI/CD using AppRole + Transit==
- ==Demo — Verify the signature, then tamper with the binary and show verification fails==
