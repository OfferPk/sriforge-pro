\# SRIForge Pro \- User Manual & Integration Guide

Welcome to \*\*SRIForge Pro\*\*, the professional Subresource Integrity (SRI) studio, hash synthesizer, and supply chain security auditor.

\---

\#\# 1\. Quickstart: Web Studio

\#\#\# Running Offline  
1\. Extract \`sriforge-pro-v1.0.0.zip\` or clone the repository.  
2\. Double-click \`source-code/index.html\` in Google Chrome, Mozilla Firefox, Apple Safari, or Microsoft Edge.  
3\. The studio loads instantly with zero external dependencies, build steps, or server setups.

\#\#\# Selecting Presets  
Use the preset dropdown in the navigation header:  
\- \*\*Hardened PCI DSS 4.0 Checkout:\*\* Production-ready e-commerce checkout page with Stripe, Tailwind, AlpineJS, and FontAwesome with SHA-384/SHA-512 digests (100/100, Grade A+).  
\- \*\*Compromised Supply Chain Legacy App:\*\* Demonstrates common high-risk antipatterns (\`polyfill.io\`, \`@latest\` floating versions, missing integrity, missing CORS) (Grade F).  
\- \*\*Modern Static WebApp / Analytics:\*\* Demonstrates secure static asset configurations with pinned CDNs (Grade A).

\---

\#\# 2\. Using the Supply Chain & SRI Auditor

1\. Open the \*\*Supply Chain \&amp; SRI Auditor\*\* tab.  
2\. View your document's \*\*Supply Chain Score (0–100%)\*\* and \*\*Security Grade (A+ to F)\*\*.  
3\. Review \*\*Identified Supply Chain Vulnerabilities\*\*:  
   \- Each card details the severity badge (Critical, High, Medium, Low), the point penalty, root cause, and technical remediation instructions.  
4\. Click \*\*Auto-Harden HTML\*\* to automatically replace unauthenticated links with pinned, SHA-384 hashed tags with \`crossorigin="anonymous"\`.  
5\. Review \*\*Verified Security Controls\*\* to confirm PCI DSS Requirement 6.4.3 readiness, CORS compliance, and version pinning.

\---

\#\# 3\. Calculating Hashes with the Synthesizer

1\. Navigate to the \*\*SRI Hash Synthesizer\*\* tab.  
2\. Select your resource type (\*\*JavaScript Dependency\*\* or \*\*Stylesheet CSS\*\*).  
3\. Enter the target CDN URL and paste the source code.  
4\. Select the digest algorithm:  
   \- \*\*SHA-384\*\* (W3C Recommended)  
   \- \*\*SHA-512\*\* (Maximum Security)  
   \- \*\*SHA-256\*\* (Standard)  
   \- \*\*Dual Hash Bundle\*\* (\`sha384-... sha512-...\`)  
5\. Click \*\*Calculate Cryptographic SRI Hash\*\*.  
6\. Copy the synthesized HTML \`\<script\>\` / \`\<link\>\` tag or the high-priority \`\<link rel="preload"\>\` tag.

\---

\#\# 4\. Simulating CDN Tampering

1\. Open the \*\*CDN Tamper \&amp; Attack Simulator\*\* tab.  
2\. Review the authentic asset content and simulated malicious payload (e.g., Magecart credit card skimmer).  
3\. Toggle \*\*Simulate CDN Compromise\*\* to inject the payload into the asset stream.  
4\. Toggle \*\*Enable CORS\*\* to observe how missing \`crossorigin="anonymous"\` impacts verification.  
5\. Click \*\*Simulate Browser Integrity Check\*\*:  
   \- The simulator reports whether the browser will \*\*EXECUTE\*\* or \*\*BLOCK\*\* the script.  
   \- View the exact simulated browser DevTools console error that prevents code execution.

\---

\#\# 5\. Build Tool Integrations

Navigate to the \*\*Build Tool Integrations\*\* tab for copy-pasteable configurations:  
\- \*\*Webpack 5:\*\* Automated bundle hashing with \`webpack-subresource-integrity\`.  
\- \*\*Vite 5:\*\* Zero-config SRI hashing via \`vite-plugin-sri\`.  
\- \*\*Next.js App Router:\*\* Production chunk integrity via \`next.config.js\`.  
\- \*\*Rollup:\*\* Library distribution hashing via \`rollup-plugin-sri\`.

\---

\#\# 6\. Python CLI Usage & CI/CD Security Gating

The standalone CLI is located at \`build/sriforge-cli.py\`. It requires Python 3.8+ with zero third-party dependencies.

\#\#\# Command Reference

\#\#\#\# 1\. Audit an HTML File or Template  
\`\`\`bash  
\# Audit an HTML file  
python3 sriforge-cli.py audit ./index.html

\# Enforce strict security in CI/CD (exits with code 1 on Critical or High risks)  
python3 sriforge-cli.py audit ./index.html \--strict  
\`\`\`

\#\#\#\# 2\. Generate an SRI Hash  
\`\`\`bash  
python3 sriforge-cli.py hash ./dist/app.js \--algo sha384 \--tag script \--url "https\://cdn.example.com/app.js"  
\`\`\`

\#\#\#\# 3\. Verify Asset File Integrity  
\`\`\`bash  
python3 sriforge-cli.py verify ./dist/app.js "sha384-..."  
\`\`\`

\#\#\# GitHub Actions Pre-Commit Workflow Example  
Add this job to \`.github/workflows/sri-lint.yml\`:  
\`\`\`yaml  
name: Supply Chain & SRI Security Audit

on: \[push, pull\_request\]

jobs:  
  sri-audit:  
    runs-on: ubuntu-latest  
    steps:  
      \- uses: actions/checkout@v4  
      \- name: Set up Python  
        uses: actions/setup-python@v5  
        with:  
          python-version: '3.11'  
      \- name: Run SRIForge Pro Audit  
        run: |  
          python3 build/sriforge-cli.py audit ./public/index.html \--strict  
\`\`\`  
