# Simply Docs: Multi-Site Monorepo Architecture

Welcome to the `simply-docs` repository. This is a monolithic repository (monorepo) that powers the entire distributed documentation and project ecosystem for the `simplysanjeev` network.

This infrastructure is built using **MkDocs Material**, engineered for automated CI/CD deployments via **Netlify**, and uses edge-routed custom DNS through **GoDaddy**.

---

## 🏗️ The Ecosystem

This repository manages multiple distinct engineering subjects and projects. By utilizing a monorepo, all sites share a unified design aesthetic (via a root `base.yml`) while deploying to completely isolated custom subdomains.

### 📚 Technical Knowledge Domains
* **[Database Management (DBMS)](https://dbms.simplysanjeev.com)**: Advanced database internals, SQL, and query optimization.
* **[Java Backend](https://java.simplysanjeev.com)**: Distributed systems, JVM architecture, and core Java engineering.
* **[System Design](https://system-design.simplysanjeev.com)**: High-level architectural blueprints and scalability patterns.
* **[Operating Systems (OS)](https://os.simplysanjeev.com)**: Core kernel concepts, concurrency, and memory management.

### 🚀 Projects & Playbooks
* **[Simply Built - The Architecture Notes](https://project.simplysanjeev.com)**: The technical deployment logs, DNS routing strategies, and infrastructure blueprints for this exact multi-site network.
* **[SimplyZip Compressor](https://github.com/your-username/simplyzip)**: Documentation for my custom Java-based file compressor utilizing Huffman Coding and LZ77 algorithms.

---

## ⚙️ Architecture & Local Setup

### Prerequisites
* Python 3.x
* Pip

### 1. Install Dependencies
Instead of maintaining multiple virtual environments, all dependencies are mapped in the root repository.

```bash
    pip install -r requirements.txt
```

### 2. Run a Local Server
To test a specific site locally, run the MkDocs server from the root directory and point it to the targeted configuration file. For example, to preview the Java documentation:

```bash
    python -m mkdocs serve -f java/mkdocs.yml
```

*The local development server will spin up at `http://127.0.0.1:8000`.*

!!! tip "Troubleshooting: Port 8000 Already in Use"
    If you try to switch between documentation folders and get an error stating that port 8000 is already occupied, you can forcefully clear the process holding the port using this terminal command:

```bash
        kill -9 $(lsof -ti :8000)
```

---

## 🌐 Continuous Deployment (Netlify)

This repository is strictly configured for automated CI/CD. Every push to the `main` branch triggers an automated build pipeline on Netlify's edge network.

**Standardized Netlify Build Configuration:**
* **Base directory:** ` ` *(Left completely blank to preserve root access to `base.yml`)*
* **Build command:** `pip install -r requirements.txt && python -m mkdocs build -f <folder_name>/mkdocs.yml`
* **Publish directory:** `<folder_name>/site`

> **Want to replicate this architecture?** Read the complete step-by-step deployment playbook at [project.simplysanjeev.com](https://project.simplysanjeev.com).

---

## 📜 License
This documentation structure and architecture playbook is open-sourced under the **MIT License**. You are free to clone this repository and adapt the CI/CD monorepo architecture for your own deployment pipelines.