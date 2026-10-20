/\*\*  
 \* SRIForge Pro \- Supply Chain & HTML Asset Auditor  
 \* Scans HTML/templates for Subresource Integrity (SRI) coverage, CORS compliance,  
 \* compromised CDN hostnames, and PCI DSS 4.0 Requirement 6.4.3 readiness.  
 \*/

class SupplyChainAuditor {  
  constructor(htmlOrAssets) {  
    if (typeof htmlOrAssets \=== 'string') {  
      this.rawInput \= htmlOrAssets;  
      this.assets \= this.extractAssetsFromHtml(htmlOrAssets);  
    } else if (Array.isArray(htmlOrAssets)) {  
      this.assets \= htmlOrAssets;  
      this.rawInput \= JSON.stringify(htmlOrAssets, null, 2);  
    } else {  
      this.assets \= \[\];  
      this.rawInput \= "";  
    }  
  }

  extractAssetsFromHtml(html) {  
    const assets \= \[\];  
      
    // Regex to match \<script\> tags with src  
    const scriptRegex \= /\<script\\b(\[^\>\]\*?)\>/gi;  
    let match;  
    while ((match \= scriptRegex.exec(html)) \!== null) {  
      const attrs \= match\[1\];  
      const srcMatch \= attrs.match(/\\bsrc\\s\*=\\s\*\["'\](\[^"'\]+)\["'\]/i);  
      if (srcMatch) {  
        const src \= srcMatch\[1\];  
        const integrityMatch \= attrs.match(/\\bintegrity\\s\*=\\s\*\["'\](\[^"'\]+)\["'\]/i);  
        const crossoriginMatch \= attrs.match(/\\bcrossorigin\\s\*=\\s\*\["'\](\[^"'\]+)\["'\]/i);  
        assets.push({  
          type: 'script',  
          url: src,  
          integrity: integrityMatch ? integrityMatch\[1\] : null,  
          crossorigin: crossoriginMatch ? crossoriginMatch\[1\] : null,  
          rawTag: match\[0\]  
        });  
      }  
    }

    // Regex to match \<link rel="stylesheet"\> tags  
    const linkRegex \= /\<link\\b(\[^\>\]\*?)\>/gi;  
    while ((match \= linkRegex.exec(html)) \!== null) {  
      const attrs \= match\[1\];  
      const relMatch \= attrs.match(/\\brel\\s\*=\\s\*\["'\](\[^"'\]\*stylesheet\[^"'\]\*)\["'\]/i);  
      const hrefMatch \= attrs.match(/\\bhref\\s\*=\\s\*\["'\](\[^"'\]+)\["'\]/i);  
      if (relMatch && hrefMatch) {  
        const href \= hrefMatch\[1\];  
        const integrityMatch \= attrs.match(/\\bintegrity\\s\*=\\s\*\["'\](\[^"'\]+)\["'\]/i);  
        const crossoriginMatch \= attrs.match(/\\bcrossorigin\\s\*=\\s\*\["'\](\[^"'\]+)\["'\]/i);  
        assets.push({  
          type: 'stylesheet',  
          url: href,  
          integrity: integrityMatch ? integrityMatch\[1\] : null,  
          crossorigin: crossoriginMatch ? crossoriginMatch\[1\] : null,  
          rawTag: match\[0\]  
        });  
      }  
    }

    return assets;  
  }

  isThirdPartyUrl(url) {  
    if (\!url) return false;  
    return url.startsWith('http\://') || url.startsWith('https\://') || url.startsWith('//');  
  }

  audit() {  
    const findings \= \[\];  
    const passed \= \[\];  
    let score \= 100;

    const KNOWN\_COMPROMISED\_CDNS \= \[  
      'polyfill.io',  
      'polyfill.com',  
      'bootcss.com',  
      'bootcdn.net',  
      'staticfile.org'  
    \];

    const thirdPartyScripts \= this.assets.filter(a \=\> a.type \=== 'script' && this.isThirdPartyUrl(a.url));  
    const thirdPartyStyles \= this.assets.filter(a \=\> a.type \=== 'stylesheet' && this.isThirdPartyUrl(a.url));

    // Check 1: Known Compromised or Hijacked CDN Domains  
    const compromisedAssets \= this.assets.filter(a \=\> {  
      const lower \= a.url.toLowerCase();  
      return KNOWN\_COMPROMISED\_CDNS.some(domain \=\> lower.includes(domain));  
    });

    if (compromisedAssets.length \> 0\) {  
      findings.push({  
        id: 'COMPROMISED\_CDN\_DETECTED',  
        severity: 'CRITICAL',  
        title: 'High-Risk / Compromised CDN Domain Detected',  
        deduction: 35,  
        description: \`Assets found referencing known supply-chain compromised hostnames (\${KNOWN\_COMPROMISED\_CDNS.join(', ')}): \[\${compromisedAssets.map(a \=\> a.url).join(', ')}\]. Attacks like the 2024 polyfill.io supply chain takeover serve malware and Magecart credit card skimmers to mobile devices.\`,  
        remediation: 'Immediately migrate away from compromised CDN providers. Self-host the dependencies or utilize reputable mirrors with strict SRI integrity hashes.'  
      });  
      score \-= 35;  
    } else {  
      passed.push({  
        id: 'NO\_COMPROMISED\_HOSTS',  
        title: 'Clean Host Reputation',  
        description: 'No known hijacked or malicious CDN endpoints detected.'  
      });  
    }

    // Check 2: Third-Party Scripts Missing SRI Integrity  
    const missingSriScripts \= thirdPartyScripts.filter(a \=\> \!a.integrity);  
    if (missingSriScripts.length \> 0\) {  
      findings.push({  
        id: 'SCRIPTS\_MISSING\_SRI',  
        severity: 'CRITICAL',  
        title: 'Third-Party Scripts Missing Subresource Integrity (SRI)',  
        deduction: 30,  
        description: \`\${missingSriScripts.length} third-party script(s) loaded without an 'integrity' cryptographic hash: \[\${missingSriScripts.map(a \=\> a.url).join(', ')}\]. If the CDN host is compromised, attackers can execute arbitrary JavaScript in user sessions.\`,  
        remediation: "Generate SHA-384 or SHA-512 integrity hashes and attach 'integrity=\\"sha384-...\\" crossorigin=\\"anonymous\\"' to every script tag."  
      });  
      score \-= 30;  
    } else if (thirdPartyScripts.length \> 0\) {  
      passed.push({  
        id: 'SCRIPTS\_SRI\_ENFORCED',  
        title: 'Full Script SRI Coverage',  
        description: 'All external JavaScript dependencies enforce cryptographic integrity hashes.'  
      });  
    }

    // Check 3: Missing crossorigin on SRI Assets  
    const missingCors \= this.assets.filter(a \=\> a.integrity && \!a.crossorigin && this.isThirdPartyUrl(a.url));  
    if (missingCors.length \> 0\) {  
      findings.push({  
        id: 'MISSING\_CROSSORIGIN\_ON\_SRI',  
        severity: 'HIGH',  
        title: "Missing 'crossorigin=\\"anonymous\\"' on SRI-Protected Asset",  
        deduction: 20,  
        description: \`W3C Fetch specification requires CORS credentials mode for cross-origin SRI validation. The following assets define 'integrity' but omit 'crossorigin': \[\${missingCors.map(a \=\> a.url).join(', ')}\]. Browsers will either fail open or block the resource completely.\`,  
        remediation: "Add crossorigin=\\"anonymous\\" to all cross-origin tags utilizing the integrity attribute."  
      });  
      score \-= 20;  
    } else if (this.assets.some(a \=\> a.integrity)) {  
      passed.push({  
        id: 'CORS\_SPEC\_COMPLIANT',  
        title: 'CORS Handshake Attributes Present',  
        description: 'Cross-origin assets properly declare crossorigin="anonymous" to enable browser SRI verification.'  
      });  
    }

    // Check 4: Weak or Deprecated Hashes (MD5, SHA-1)  
    const weakHashAssets \= this.assets.filter(a \=\> {  
      if (\!a.integrity) return false;  
      const lower \= a.integrity.toLowerCase();  
      return lower.startsWith('sha1-') || lower.startsWith('md5-');  
    });

    if (weakHashAssets.length \> 0\) {  
      findings.push({  
        id: 'WEAK\_SRI\_ALGORITHM',  
        severity: 'HIGH',  
        title: 'Weak / Deprecated Hash Algorithm in SRI (SHA-1 / MD5)',  
        deduction: 20,  
        description: \`Assets using obsolete hash functions: \[\${weakHashAssets.map(a \=\> a.url).join(', ')}\]. SHA-1 and MD5 suffer from known collision vulnerabilities and are blocked by modern browsers per W3C SRI spec.\`,  
        remediation: 'Upgrade all integrity hashes to SHA-384 or SHA-512.'  
      });  
      score \-= 20;  
    } else {  
      passed.push({  
        id: 'STRONG\_HASH\_ALGORITHMS',  
        title: 'Strong Cryptographic Digest Functions',  
        description: 'Integrity hashes utilize robust SHA-256, SHA-384, or SHA-512 algorithms.'  
      });  
    }

    // Check 5: Unversioned or Floating CDN Tags (@latest, unversioned)  
    const floatingAssets \= this.assets.filter(a \=\> {  
      const lower \= a.url.toLowerCase();  
      return lower.includes('@latest') || lower.includes('/master/') || lower.includes('/main/');  
    });

    if (floatingAssets.length \> 0\) {  
      findings.push({  
        id: 'FLOATING\_CDN\_VERSION',  
        severity: 'HIGH',  
        title: 'Floating or Unpinned CDN Version Reference (@latest)',  
        deduction: 15,  
        description: \`Assets referencing dynamic or unpinned versions: \[\${floatingAssets.map(a \=\> a.url).join(', ')}\]. When the CDN provider updates the library upstream, the file digest changes immediately, causing silent website outages due to SRI hash mismatches.\`,  
        remediation: 'Pin exact semantic versions in CDN URLs (e.g. package@1.4.2 instead of package@latest).'  
      });  
      score \-= 15;  
    } else {  
      passed.push({  
        id: 'PINNED\_VERSIONS',  
        title: 'Pinned Semantic Versioning',  
        description: 'CDN asset URLs pin specific release versions, preventing unexpected hash drift.'  
      });  
    }

    // Check 6: Plaintext Insecure HTTP Asset URLs  
    const httpAssets \= this.assets.filter(a \=\> a.url.startsWith('http\://'));  
    if (httpAssets.length \> 0\) {  
      findings.push({  
        id: 'INSECURE\_HTTP\_ASSETS',  
        severity: 'MEDIUM',  
        title: 'Plaintext Insecure HTTP Asset URLs',  
        deduction: 15,  
        description: \`Assets loaded over unencrypted HTTP: \[\${httpAssets.map(a \=\> a.url).join(', ')}\]. Attackers can intercept and modify responses via Man-in-the-Middle (MitM) attacks.\`,  
        remediation: 'Upgrade all asset links to HTTPS.'  
      });  
      score \-= 15;  
    } else {  
      passed.push({  
        id: 'HTTPS\_ENFORCEMENT',  
        title: 'Encrypted Asset Delivery (HTTPS)',  
        description: 'All remote assets are fetched via secure HTTPS channels.'  
      });  
    }

    // Check 7: Third-Party Stylesheets Missing SRI Integrity  
    const missingSriStyles \= thirdPartyStyles.filter(a \=\> \!a.integrity);  
    if (missingSriStyles.length \> 0\) {  
      findings.push({  
        id: 'STYLES\_MISSING\_SRI',  
        severity: 'MEDIUM',  
        title: 'Third-Party Stylesheets Missing SRI Integrity',  
        deduction: 10,  
        description: \`\${missingSriStyles.length} external stylesheet(s) loaded without SRI: \[\${missingSriStyles.map(a \=\> a.url).join(', ')}\]. Malicious CSS can be used for CSS-based data exfiltration and UI phishing.\`,  
        remediation: 'Generate SRI integrity hashes for external CSS files and set crossorigin="anonymous".'  
      });  
      score \-= 10;  
    } else if (thirdPartyStyles.length \> 0\) {  
      passed.push({  
        id: 'STYLES\_SRI\_ENFORCED',  
        title: 'Stylesheet Integrity Enforced',  
        description: 'External stylesheets are cryptographically verified with SRI.'  
      });  
    }

    // Check 8: PCI DSS 4.0 Requirement 6.4.3 Compliance  
    // If scripts lack integrity on payment/checkout templates  
    if (missingSriScripts.length \> 0\) {  
      findings.push({  
        id: 'PCI\_DSS\_643\_GAP',  
        severity: 'MEDIUM',  
        title: 'PCI DSS 4.0 Requirement 6.4.3 Script Integrity Gap',  
        deduction: 10,  
        description: "PCI DSS v4.0 (effective March 2025\) requires entities to manage and confirm the integrity of all scripts loaded into consumer browsers during checkout. Missing SRI hashes on third-party scripts constitutes a non-compliance finding.",  
        remediation: "Ensure all payment page scripts are authorized, inventoried, and secured with SRI hashes."  
      });  
      score \-= 10;  
    } else {  
      passed.push({  
        id: 'PCI\_DSS\_COMPLIANCE',  
        title: 'PCI DSS 4.0 Requirement 6.4.3 Readiness',  
        description: 'All scripts meet strict cryptographic verification standards.'  
      });  
    }

    // Check 9: Hash Algorithm Robustness (Prefer SHA-384 or SHA-512 over SHA-256)  
    const onlySha256 \= this.assets.filter(a \=\> a.integrity && a.integrity.toLowerCase().startsWith('sha256-'));  
    if (onlySha256.length \> 0 && \!this.assets.some(a \=\> a.integrity && (a.integrity.includes('sha384-') || a.integrity.includes('sha512-')))) {  
      findings.push({  
        id: 'UPGRADE\_TO\_SHA384',  
        severity: 'LOW',  
        title: 'Recommendation: Upgrade SHA-256 to SHA-384 or SHA-512',  
        deduction: 5,  
        description: "While SHA-256 is secure, the W3C SRI specification explicitly recommends SHA-384 for stronger collision resistance without measurable performance penalty.",  
        remediation: "Use SHA-384 as the primary SRI hashing algorithm."  
      });  
      score \-= 5;  
    } else if (this.assets.some(a \=\> a.integrity)) {  
      passed.push({  
        id: 'RECOMMENDED\_DIGEST\_USED',  
        title: 'W3C Recommended Digest Standards',  
        description: 'Integrity hashes employ recommended SHA-384 or SHA-512 algorithms.'  
      });  
    }

    score \= Math.max(0, Math.min(100, score));

    let grade \= 'F';  
    let status \= 'Critical Supply Chain Risk';  
    if (score \>= 95\) { grade \= 'A+'; status \= 'Production Hardened'; }  
    else if (score \>= 85\) { grade \= 'A'; status \= 'Secure'; }  
    else if (score \>= 75\) { grade \= 'B'; status \= 'Moderate Risk'; }  
    else if (score \>= 60\) { grade \= 'C'; status \= 'Elevated Risk'; }  
    else if (score \>= 40\) { grade \= 'D'; status \= 'High Risk'; }

    return {  
      score,  
      grade,  
      status,  
      timestamp: new Date().toISOString(),  
      totalAssetsAudited: this.assets.length,  
      thirdPartyCount: thirdPartyScripts.length \+ thirdPartyStyles.length,  
      findingsCount: findings.length,  
      passedCount: passed.length,  
      findings,  
      passed,  
      summary: {  
        critical: findings.filter(f \=\> f.severity \=== 'CRITICAL').length,  
        high: findings.filter(f \=\> f.severity \=== 'HIGH').length,  
        medium: findings.filter(f \=\> f.severity \=== 'MEDIUM').length,  
        low: findings.filter(f \=\> f.severity \=== 'LOW').length  
      }  
    };  
  }  
}

if (typeof module \!== 'undefined' && module.exports) {  
  module.exports \= { SupplyChainAuditor };  
}  
