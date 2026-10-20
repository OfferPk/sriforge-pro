\# SRIForge Pro (v1.0.0)

\> \*\*Client-Side Subresource Integrity (SRI) Hash Synthesizer, CDN Supply Chain Tamper Auditor, W3C SRI Level 2 Validator & PCI DSS 4.0 Script Defense Studio\*\*

\[\!\[Supply Chain Security\](https\://img.shields.io/badge/Supply\_Chain\_Health-100%25\_A%2B-emerald?style=flat-square)\](\#)  
\[\!\[Dependencies\](https\://img.shields.io/badge/Dependencies-0\_External-blue?style=flat-square)\](\#)  
\[\!\[Data Privacy\](https\://img.shields.io/badge/Telemetry-0\_Bytes\_(Offline)-purple?style=flat-square)\](\#)  
\[\!\[Standards\](https\://img.shields.io/badge/W3C-SRI\_Level\_2-cyan?style=flat-square)\](\#)  
\[\!\[PCI DSS\](https\://img.shields.io/badge/PCI\_DSS\_4.0-Requirement\_6.4.3-indigo?style=flat-square)\](\#)

\---

\#\# 1\. Problem & Product Overview

Third-party content delivery networks (CDNs) are essential for modern web performance, yet they introduce severe supply-chain risks. When a CDN host, account, or DNS record is compromised (as observed in the historic \*\*polyfill.io supply-chain takeover\*\* impacting \>100,000 domains), attackers can inject malicious JavaScript (such as Magecart credit card skimmers and credential harvesters) directly into client browsers.

\*\*Subresource Integrity (SRI)\*\* is a cryptographic standard enabling browsers to verify that fetched assets have not been altered. If a single byte is changed, the browser refuses to execute the script or apply the stylesheet.

Furthermore, under \*\*PCI DSS v4.0 (Requirement 6.4.3)\*\*, e-commerce merchants are legally mandated to maintain an inventory of all checkout scripts and confirm their cryptographic integrity.

\*\*SRIForge Pro\*\* is a 100% client-side studio and developer CLI tool that:  
1\. Generates standard W3C SRI hashes (SHA-256, SHA-384, SHA-512) and dual bundles.  
2\. Audits HTML templates against a 10-point supply-chain vulnerability matrix (detecting missing SRI, missing \`crossorigin="anonymous"\`, compromised CDNs like \`polyfill.io\`, weak SHA-1 hashes, and unversioned \`@latest\` links).  
3\. Simulates CDN tampering in real time, showing the exact browser DevTools console abort sequence.  
4\. Generates automated build-time configurations for \*\*Webpack 5\*\*, \*\*Vite 5\*\*, \*\*Next.js App Router\*\*, and \*\*Rollup\*\*.  
5\. Provides a standalone zero-dependency Python CLI tool for automated terminal auditing and CI/CD pre-commit security gating.

\---

\#\# 2\. Key Features

\- \*\*W3C SRI Level 2 Hash Synthesizer:\*\* Instantly computes SHA-256, SHA-384 (recommended), SHA-512, and dual-hash bundles from raw code or text.  
\- \*\*10-Point Supply Chain & Asset Auditor:\*\* Audits HTML files and templates, scoring supply-chain health (0–100%) and grading posture (A+ to F).  
\- \*\*Interactive CDN Tamper Simulator:\*\* Injects simulated Magecart payloads into asset streams to demonstrate browser-level execution termination and console reporting.  
\- \*\*PCI DSS 4.0 (Req 6.4.3) Compliance Verification:\*\* Verifies that all client-side scripts meet strict cryptographic verification standards.  
\- \*\*1-Click Auto-Harden:\*\* Replaces insecure and unpinned CDN links with pinned, SHA-384 hashed tags with \`crossorigin="anonymous"\`.  
\- \*\*Standalone Zero-Dependency Python CLI (\`sriforge-cli.py\`):\*\* Scans files, generates SRI tags, verifies asset integrity, and enforces pre-commit CI/CD security gating (exiting with code 1 if unauthenticated third-party scripts exist).  
\- \*\*3 Curated Industry Presets:\*\*  
  \- \*Hardened PCI DSS 4.0 Checkout\* (100/100, Grade A+)  
  \- \*Compromised Supply Chain Legacy App\* (0/100, Grade F)  
  \- \*Modern Static WebApp / Analytics\* (95/100, Grade A)

\---

\#\# 3\. Directory Layout

\`\`\`  
sriforge-pro/  
├── source-code/  
│   ├── index.html                  \# Developer dark-theme web studio  
│   ├── sriforge.css                \# Cybersecurity styling, score gauges & responsive layout  
│   ├── sri-engine.js               \# W3C SRI hash calculation & verification engine  
│   ├── supply-chain-auditor.js     \# 10-point static supply-chain vulnerability auditor  
│   ├── tamper-simulator.js         \# CDN tamper simulation & browser console emulator  
│   ├── framework-synthesizer.js    \# Multi-framework build integration synthesizer  
│   ├── app.js                      \# Application state controller & UI bindings  
│   └── presets/  
│       ├── pci-dss-ecommerce.json  \# Hardened PCI DSS checkout preset (100/100 A+)  
│       ├── compromised-supply-chain.json \# High-risk legacy preset (0/100 F)  
│       └── modern-static-webapp.json     \# Modern JAMstack preset (95/100 A)  
├── build/  
│   ├── sriforge-cli.py             \# Standalone Python 3 CLI auditor & hasher  
│   └── sriforge-pro-v1.0.0.zip     \# Distributable standalone archive  
├── documentation/  
│   ├── ARCHITECTURE.md             \# Threat models, specs, and architectural breakdown  
│   ├── USER\_GUIDE.md               \# User manual, CLI commands, and CI/CD setup  
│   └── MONETIZATION\_AND\_MARKETING\_PLAYBOOK.md \# Commercial tiers, pricing, and distribution  
├── screenshots/  
│   ├── sriforge-studio-preview.svg        \# Vector UI mockup of main auditor dashboard  
│   └── sri-tamper-simulation-preview.svg  \# Vector preview of CDN tamper simulator  
├── tests/  
│   └── test\_sriforge.py            \# Comprehensive 9-suite automated verification test  
└── README.md                       \# Master product documentation  
\`\`\`

\---

\#\# 4\. Quickstart

\#\#\# Running the Web Studio  
Open \`source-code/index.html\` directly in any modern browser. Zero build steps, node modules, or network connections required.

\#\#\# Using the Python CLI  
\`\`\`bash  
\# 1\. Audit an HTML file or template  
python3 build/sriforge-cli.py audit source-code/presets/pci-dss-ecommerce.json

\# 2\. Strict CI/CD gating (exits with code 1 if critical/high risks exist)  
python3 build/sriforge-cli.py audit source-code/presets/compromised-supply-chain.json \--strict

\# 3\. Calculate an SRI hash and generate HTML tag  
python3 build/sriforge-cli.py hash "console.log('App init');" \--algo sha384 \--tag script \--url "https\://cdn.example.com/app.js"

\# 4\. Verify an asset against an expected integrity hash  
python3 build/sriforge-cli.py verify ./dist/app.js "sha384-..."  
\`\`\`

\---

\#\# 5\. Automated Testing

Run the full automated test suite:  
\`\`\`bash  
python3 tests/test\_sriforge.py  
\`\`\`  
