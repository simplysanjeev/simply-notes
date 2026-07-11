# Architecture Playbook: Multi-Site MkDocs Monorepo

When managing technical documentation across multiple distinct engineering subjects (like Database Management, Java, and System Design), maintaining separate Git repositories for every topic quickly becomes a maintenance bottleneck.

To solve this, I designed a **monorepo architecture**. This single repository holds all individual documentation sites, shares a unified Material design system, and deploys them to entirely separate custom subdomains via Netlify and GoDaddy.

Here is the complete blueprint of the infrastructure.

---

## Phase 1: Repository Architecture

To ensure a consistent design aesthetic across all sites without repeating code, the architecture uses a shared configuration model. The `mkdocs-material` theme is configured once at the root level, and individual subject folders inherit those rules.

### The Directory Structure
```text
    simply-docs/
    ├── requirements.txt      # Infrastructure as Code for Netlify dependencies
    ├── base.yml              # Global Material theme and color configurations
    ├── dbms/
    │   ├── mkdocs.yml        # Inherits base.yml, holds DBMS-specific navigation
    │   └── docs/             # Markdown files and /images folder
    └── java/
        ├── mkdocs.yml        # Inherits base.yml, holds Java-specific navigation
        └── docs/             # Markdown files and /images folder
```

### Dependency Provisioning

Instead of writing brittle installation scripts in the CI/CD UI, we use standard Python dependency management. Adding a `requirements.txt` file to the root directory triggers Netlify's native Python environment manager.
```text
    mkdocs-material
```

---

## Phase 2: Continuous Deployment (Netlify)

Because the individual sites (`dbms`, `java`, etc.) must look outside their own folders to read the shared `base.yml` file, the Netlify build container must execute its commands from the **root** of the repository, but only publish the targeted folder.

For each site, a new Netlify project is connected to the same repository with the following specific build settings:

* **Base directory:** *(Leave completely blank to preserve root access)*
* **Build command:** `pip install mkdocs-material && python -m mkdocs build -f dbms/mkdocs.yml`
* **Publish directory:** `dbms/site`

!!! tip "Scaling the Architecture"

    To deploy a new subject, simply create a new site in Netlify connected to the same repo, and swap the word `dbms` for the new folder name (like `java` or `project`) in the build and publish commands.

---

## Phase 3: DNS & Subdomain Routing (GoDaddy)

Netlify generates edge-tracking URLs (e.g., `random-name.netlify.app`) by default. To route traffic to clean, professional subdomains (e.g., `dbms.simplysanjeev.com`), the DNS is managed via GoDaddy.

### Step 1: Root Domain Ownership Verification
To prevent domain hijacking, Netlify requires proof of ownership for the root domain.

1. Add the custom domain in Netlify (**Site configuration > Domain management**).
2. Copy the provided verification token (e.g., `netlify-verification=xxxx`).
3. In GoDaddy DNS settings, create a **TXT Record**:
    * **Name:** `@`
    * **Value:** `[Your Netlify Token]`

### Step 2: CNAME Mapping
Once verified, the actual traffic for the subdomain is routed using a Canonical Name (CNAME) record.

* **Type:** `CNAME`
* **Name:** `dbms` *(Prefix only)*
* **Value:** `random-name.netlify.app` *(Target Netlify URL)*

!!! success "Automated SSL Provisioning"
    Once the DNS records propagate globally (typically 15–30 minutes), Netlify's Let's Encrypt integration automatically detects the CNAME and provisions a free SSL certificate, securing the site over HTTPS.

---

## Phase 4: Cross-Domain Redirects (Brand Protection)

To protect the brand and capture traffic from secondary Top-Level Domains (TLDs), traffic from the `.in` domain is strictly routed to the primary `.com` domain.

Instead of consuming extra Netlify build minutes to handle redirects, this is managed purely at the DNS level using GoDaddy's **Subdomain Forwarding**.

Inside the DNS settings for the secondary domain (`.in`):

* **Subdomain:** `dbms`
* **Forward to:** `https://dbms.simplysanjeev.com`
* **Forward type:** `Permanent (301)`
* **Settings:** `Forward only`

This creates a seamless, instant bounce to the secure `.com` environment.

---

## Phase 5: Asset & Image Handling

To maximize Netlify's enterprise-grade Content Delivery Network (CDN), image hosting is completely internalized within the Git tree.

* Images are stored locally in the targeted subject folder: `dbms/docs/images/`.
* Markdown files reference images using standard relative paths: `![Schema](images/schema.png)`.

!!! warning "Naming Convention Rule"
    All image filenames strictly use lowercase characters and hyphens (e.g., `b-tree-index.png`). Avoid spaces or uppercase letters to prevent path resolution failures in Netlify's case-sensitive Linux build nodes.