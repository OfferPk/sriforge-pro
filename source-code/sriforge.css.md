/\* SRIForge Pro \- Cybersecurity Dark Theme \*/  
:root {  
  \--bg-primary: \#0a0d14;  
  \--bg-secondary: \#101622;  
  \--bg-tertiary: \#172133;  
  \--bg-card: \#131b2b;  
  \--border-color: \#233047;  
  \--border-focus: \#06b6d4;  
  \--text-primary: \#f1f5f9;  
  \--text-secondary: \#94a3b8;  
  \--text-muted: \#64748b;  
    
  \--accent-cyan: \#06b6d4;  
  \--accent-blue: \#3b82f6;  
  \--accent-emerald: \#10b981;  
  \--accent-amber: \#f59e0b;  
  \--accent-rose: \#f43f5e;  
  \--accent-purple: \#8b5cf6;

  \--font-mono: 'JetBrains Mono', 'Fira Code', 'SF Mono', Consolas, monospace;  
  \--font-sans: \-apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;  
  \--radius-sm: 6px;  
  \--radius-md: 10px;  
  \--radius-lg: 16px;  
  \--shadow-sm: 0 2px 4px rgba(0, 0, 0, 0.4);  
  \--shadow-md: 0 4px 12px rgba(0, 0, 0, 0.5);  
}

\* {  
  box-sizing: border-box;  
  margin: 0;  
  padding: 0;  
}

body {  
  background-color: var(--bg-primary);  
  color: var(--text-primary);  
  font-family: var(--font-sans);  
  line-height: 1.5;  
  font-size: 14px;  
  min-height: 100vh;  
}

/\* Navbar \*/  
.navbar {  
  display: flex;  
  align-items: center;  
  justify-content: space-between;  
  padding: 12px 24px;  
  background-color: var(--bg-secondary);  
  border-bottom: 1px solid var(--border-color);  
  position: sticky;  
  top: 0;  
  z-index: 100;  
}

.brand {  
  display: flex;  
  align-items: center;  
  gap: 12px;  
}

.brand-icon {  
  width: 32px;  
  height: 32px;  
  background: linear-gradient(135deg, var(--accent-cyan), var(--accent-blue));  
  border-radius: var(--radius-sm);  
  display: flex;  
  align-items: center;  
  justify-content: center;  
  box-shadow: 0 0 12px rgba(6, 182, 212, 0.5);  
}

.brand-icon svg {  
  width: 20px;  
  height: 20px;  
  fill: \#ffffff;  
}

.brand-title {  
  font-size: 18px;  
  font-weight: 700;  
  letter-spacing: \-0.5px;  
  color: \#ffffff;  
}

.brand-badge {  
  font-size: 11px;  
  font-weight: 600;  
  padding: 2px 6px;  
  background-color: rgba(6, 182, 212, 0.2);  
  color: var(--accent-cyan);  
  border: 1px solid rgba(6, 182, 212, 0.4);  
  border-radius: var(--radius-sm);  
}

.nav-actions {  
  display: flex;  
  align-items: center;  
  gap: 12px;  
}

.preset-select {  
  background-color: var(--bg-tertiary);  
  color: var(--text-primary);  
  border: 1px solid var(--border-color);  
  padding: 6px 12px;  
  border-radius: var(--radius-sm);  
  font-size: 13px;  
  cursor: pointer;  
  outline: none;  
}

.preset-select:focus {  
  border-color: var(--border-focus);  
}

.btn {  
  display: inline-flex;  
  align-items: center;  
  gap: 6px;  
  padding: 6px 14px;  
  border-radius: var(--radius-sm);  
  font-size: 13px;  
  font-weight: 600;  
  cursor: pointer;  
  border: none;  
  transition: all 0.15s ease-in-out;  
  text-decoration: none;  
}

.btn-primary {  
  background-color: var(--accent-cyan);  
  color: \#04121d;  
  font-weight: 700;  
}

.btn-primary:hover {  
  background-color: \#22d3ee;  
}

.btn-secondary {  
  background-color: var(--bg-tertiary);  
  color: var(--text-primary);  
  border: 1px solid var(--border-color);  
}

.btn-secondary:hover {  
  background-color: \#1e2b42;  
  border-color: \#33435e;  
}

/\* Tab Bar \*/  
.tab-bar {  
  display: flex;  
  gap: 8px;  
  padding: 12px 24px 0 24px;  
  background-color: var(--bg-secondary);  
  border-bottom: 1px solid var(--border-color);  
}

.tab-btn {  
  padding: 8px 16px;  
  background: none;  
  border: none;  
  color: var(--text-secondary);  
  font-size: 13px;  
  font-weight: 600;  
  cursor: pointer;  
  position: relative;  
  transition: color 0.15s;  
  display: flex;  
  align-items: center;  
  gap: 8px;  
}

.tab-btn:hover {  
  color: var(--text-primary);  
}

.tab-btn.active {  
  color: var(--accent-cyan);  
}

.tab-btn.active::after {  
  content: '';  
  position: absolute;  
  bottom: \-1px;  
  left: 0;  
  right: 0;  
  height: 2px;  
  background-color: var(--accent-cyan);  
}

/\* Main Container \*/  
.main-container {  
  padding: 24px;  
  max-width: 1400px;  
  margin: 0 auto;  
}

.tab-content {  
  display: none;  
}

.tab-content.active {  
  display: block;  
}

/\* Cards \*/  
.card {  
  background-color: var(--bg-card);  
  border: 1px solid var(--border-color);  
  border-radius: var(--radius-md);  
  padding: 20px;  
  margin-bottom: 20px;  
  box-shadow: var(--shadow-sm);  
}

.card-title {  
  font-size: 16px;  
  font-weight: 700;  
  margin-bottom: 12px;  
  display: flex;  
  align-items: center;  
  justify-content: space-between;  
}

/\* Auditor Score Grid \*/  
.audit-overview-grid {  
  display: grid;  
  grid-template-columns: 280px 1fr;  
  gap: 20px;  
  margin-bottom: 20px;  
}

.score-panel {  
  background: linear-gradient(145deg, \#121c2e, \#0e1524);  
  border: 1px solid var(--border-color);  
  border-radius: var(--radius-md);  
  padding: 24px;  
  text-align: center;  
  display: flex;  
  flex-direction: column;  
  justify-content: center;  
  align-items: center;  
}

.score-circle {  
  width: 110px;  
  height: 110px;  
  border-radius: 50%;  
  border: 6px solid var(--accent-emerald);  
  display: flex;  
  flex-direction: column;  
  align-items: center;  
  justify-content: center;  
  margin-bottom: 12px;  
  transition: all 0.3s;  
}

.score-val {  
  font-size: 32px;  
  font-weight: 800;  
  font-family: var(--font-mono);  
}

.score-max {  
  font-size: 11px;  
  color: var(--text-muted);  
  margin-top: \-4px;  
}

.grade-badge {  
  font-size: 16px;  
  font-weight: 800;  
  padding: 4px 14px;  
  border-radius: 20px;  
  margin-bottom: 6px;  
}

.grade-A-plus, .grade-A {  
  background-color: rgba(16, 185, 129, 0.2);  
  color: var(--accent-emerald);  
  border: 1px solid rgba(16, 185, 129, 0.4);  
}

.grade-B, .grade-C {  
  background-color: rgba(245, 158, 11, 0.2);  
  color: var(--accent-amber);  
  border: 1px solid rgba(245, 158, 11, 0.4);  
}

.grade-D, .grade-F {  
  background-color: rgba(244, 63, 94, 0.2);  
  color: var(--accent-rose);  
  border: 1px solid rgba(244, 63, 94, 0.4);  
}

.metric-strip {  
  display: grid;  
  grid-template-columns: repeat(4, 1fr);  
  gap: 12px;  
  margin-top: 14px;  
  width: 100%;  
}

.metric-chip {  
  background-color: var(--bg-tertiary);  
  border: 1px solid var(--border-color);  
  border-radius: var(--radius-sm);  
  padding: 8px 4px;  
  text-align: center;  
}

.metric-chip .chip-count {  
  font-size: 18px;  
  font-weight: 700;  
  font-family: var(--font-mono);  
}

.metric-chip .chip-label {  
  font-size: 10px;  
  color: var(--text-muted);  
  text-transform: uppercase;  
  font-weight: 600;  
}

.policy-info-box {  
  background-color: var(--bg-tertiary);  
  border: 1px solid var(--border-color);  
  border-radius: var(--radius-sm);  
  padding: 14px;  
  margin-bottom: 12px;  
}

.policy-row {  
  display: flex;  
  justify-content: space-between;  
  padding: 6px 0;  
  border-bottom: 1px solid rgba(255, 255, 255, 0.05);  
  font-size: 13px;  
}

.policy-row:last-child {  
  border-bottom: none;  
}

.policy-label {  
  color: var(--text-secondary);  
}

.policy-val {  
  font-family: var(--font-mono);  
  font-weight: 600;  
}

/\* Findings List \*/  
.findings-container {  
  display: flex;  
  flex-direction: column;  
  gap: 14px;  
  margin-top: 12px;  
}

.finding-card {  
  background-color: var(--bg-tertiary);  
  border-left: 4px solid var(--border-color);  
  border-radius: var(--radius-sm);  
  padding: 14px 18px;  
  transition: transform 0.15s;  
}

.finding-card:hover {  
  transform: translateX(2px);  
}

.finding-card.severity-CRITICAL {  
  border-left-color: var(--accent-rose);  
  background: linear-gradient(90deg, rgba(244, 63, 94, 0.08), var(--bg-tertiary));  
}

.finding-card.severity-HIGH {  
  border-left-color: var(--accent-amber);  
  background: linear-gradient(90deg, rgba(245, 158, 11, 0.08), var(--bg-tertiary));  
}

.finding-card.severity-MEDIUM {  
  border-left-color: \#eab308;  
  background: linear-gradient(90deg, rgba(234, 179, 8, 0.05), var(--bg-tertiary));  
}

.finding-card.severity-LOW {  
  border-left-color: var(--accent-blue);  
}

.finding-header {  
  display: flex;  
  align-items: center;  
  justify-content: space-between;  
  margin-bottom: 6px;  
}

.finding-title-group {  
  display: flex;  
  align-items: center;  
  gap: 8px;  
}

.sev-badge {  
  font-size: 10px;  
  font-weight: 800;  
  padding: 2px 6px;  
  border-radius: 4px;  
  text-transform: uppercase;  
}

.sev-CRITICAL { background: var(--accent-rose); color: \#fff; }  
.sev-HIGH { background: var(--accent-amber); color: \#000; }  
.sev-MEDIUM { background: \#eab308; color: \#000; }  
.sev-LOW { background: var(--accent-blue); color: \#fff; }

.finding-deduction {  
  font-size: 12px;  
  font-weight: 700;  
  color: var(--accent-rose);  
  font-family: var(--font-mono);  
}

.finding-desc {  
  color: var(--text-secondary);  
  font-size: 13px;  
  margin-bottom: 8px;  
}

.finding-remediation {  
  background-color: var(--bg-primary);  
  border: 1px solid var(--border-color);  
  border-radius: var(--radius-sm);  
  padding: 8px 12px;  
  font-size: 12px;  
  color: var(--accent-cyan);  
}

/\* Passed Controls \*/  
.passed-item {  
  display: flex;  
  align-items: flex-start;  
  gap: 10px;  
  padding: 10px;  
  border-bottom: 1px solid var(--border-color);  
  font-size: 13px;  
}

.passed-item:last-child {  
  border-bottom: none;  
}

.check-icon {  
  color: var(--accent-emerald);  
  font-weight: 800;  
}

/\* Forms & Inputs \*/  
.form-group {  
  margin-bottom: 14px;  
}

.form-group label {  
  display: block;  
  font-size: 12px;  
  font-weight: 600;  
  color: var(--text-secondary);  
  margin-bottom: 6px;  
}

.form-control {  
  width: 100%;  
  background-color: var(--bg-tertiary);  
  border: 1px solid var(--border-color);  
  border-radius: var(--radius-sm);  
  padding: 8px 12px;  
  color: var(--text-primary);  
  font-size: 13px;  
  outline: none;  
}

.form-control:focus {  
  border-color: var(--border-focus);  
}

textarea.form-control {  
  font-family: var(--font-mono);  
  font-size: 12px;  
  resize: vertical;  
}

/\* Simulator Grid \*/  
.simulator-grid {  
  display: grid;  
  grid-template-columns: 1fr 1fr;  
  gap: 20px;  
}

.sim-verdict-box {  
  padding: 16px;  
  border-radius: var(--radius-sm);  
  text-align: center;  
  margin-bottom: 16px;  
}

.sim-verdict-box.verdict-BLOCKED {  
  background-color: rgba(16, 185, 129, 0.15);  
  border: 1px solid var(--accent-emerald);  
  color: var(--accent-emerald);  
}

.sim-verdict-box.verdict-EXECUTED {  
  background-color: rgba(244, 63, 94, 0.15);  
  border: 1px solid var(--accent-rose);  
  color: var(--accent-rose);  
}

.sim-verdict-box.verdict-CORS\_FAILURE {  
  background-color: rgba(245, 158, 11, 0.15);  
  border: 1px solid var(--accent-amber);  
  color: var(--accent-amber);  
}

.verdict-title {  
  font-size: 20px;  
  font-weight: 800;  
  letter-spacing: 0.5px;  
}

.verdict-desc {  
  font-size: 13px;  
  margin-top: 4px;  
}

/\* Code Snippets \*/  
.code-block-wrapper {  
  position: relative;  
}

.code-pre {  
  background-color: \#080a10;  
  border: 1px solid var(--border-color);  
  border-radius: var(--radius-md);  
  padding: 16px;  
  font-family: var(--font-mono);  
  font-size: 12px;  
  color: \#e2e8f0;  
  overflow-x: auto;  
  line-height: 1.6;  
  max-height: 520px;  
}

.sub-tab-bar {  
  display: flex;  
  gap: 6px;  
  margin-bottom: 12px;  
}

.sub-tab-btn {  
  background-color: var(--bg-tertiary);  
  color: var(--text-secondary);  
  border: 1px solid var(--border-color);  
  padding: 6px 12px;  
  border-radius: var(--radius-sm);  
  font-size: 12px;  
  font-weight: 600;  
  cursor: pointer;  
}

.sub-tab-btn.active {  
  background-color: var(--accent-cyan);  
  color: \#04121d;  
  font-weight: 700;  
  border-color: var(--accent-cyan);  
}

/\* Responsive \*/  
@media (max-width: 900px) {  
  .audit-overview-grid, .simulator-grid {  
    grid-template-columns: 1fr;  
  }  
}  
