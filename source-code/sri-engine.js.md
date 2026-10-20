/\*\*  
 \* SRIForge Pro \- Subresource Integrity (SRI) Cryptographic Engine  
 \* Complies with W3C Subresource Integrity (SRI) Level 1 & Level 2 specifications.  
 \* Supports SHA-256, SHA-384, and SHA-512 with Base64 encoding.  
 \*/

class SriEngine {  
  constructor() {}

  /\*\*  
   \* Calculate SRI hash in Node.js or Browser  
   \* @param {string|Uint8Array} content \- Text or binary content  
   \* @param {string} algorithm \- 'sha256', 'sha384', or 'sha512'  
   \* @returns {Promise\<string\>} e.g. "sha384-d1..."  
   \*/  
  async generateHash(content, algorithm \= 'sha384') {  
    const algoLower \= algorithm.toLowerCase();  
    const validAlgos \= \['sha256', 'sha384', 'sha512'\];  
    if (\!validAlgos.includes(algoLower)) {  
      throw new Error(\`Unsupported SRI algorithm: \${algorithm}. Must be one of \${validAlgos.join(', ')}.\`);  
    }

    let bytes;  
    if (typeof content \=== 'string') {  
      bytes \= new TextEncoder().encode(content);  
    } else if (content instanceof Uint8Array) {  
      bytes \= content;  
    } else {  
      throw new Error('Content must be a string or Uint8Array.');  
    }

    // Node.js environment  
    if (typeof process \!== 'undefined' && process.versions && process.versions.node) {  
      const crypto \= require('crypto');  
      const hash \= crypto.createHash(algoLower).update(bytes).digest('base64');  
      return \`\${algoLower}-\${hash}\`;  
    }

    // Browser Web Crypto API  
    if (typeof window \!== 'undefined' && window.crypto && window.crypto.subtle) {  
      const webAlgoMap \= {  
        'sha256': 'SHA-256',  
        'sha384': 'SHA-384',  
        'sha512': 'SHA-512'  
      };  
      const hashBuffer \= await window.crypto.subtle.digest(webAlgoMap\[algoLower\], bytes);  
      const hashArray \= Array.from(new Uint8Array(hashBuffer));  
      const base64 \= btoa(String.fromCharCode.apply(null, hashArray));  
      return \`\${algoLower}-\${base64}\`;  
    }

    throw new Error('Cryptographic environment not available.');  
  }

  /\*\*  
   \* Generate bundle of multiple SRI hashes  
   \*/  
  async generateBundle(content, algorithms \= \['sha384', 'sha512'\]) {  
    const hashes \= await Promise.all(algorithms.map(algo \=\> this.generateHash(content, algo)));  
    return hashes.join(' ');  
  }

  /\*\*  
   \* Validate content against an SRI integrity string  
   \*/  
  async verifyIntegrity(content, integrityString) {  
    if (\!integrityString || typeof integrityString \!== 'string') {  
      return { valid: false, reason: 'Missing or empty integrity string' };  
    }

    const tokens \= integrityString.trim().split(/\\s+/).filter(Boolean);  
    if (tokens.length \=== 0\) {  
      return { valid: false, reason: 'No integrity tokens found' };  
    }

    // Determine strongest algorithm in tokens (sha512 \> sha384 \> sha256)  
    const priority \= { 'sha512': 3, 'sha384': 2, 'sha256': 1 };  
    let bestAlgo \= null;  
    let maxPriority \= 0;

    for (const token of tokens) {  
      const \[prefix\] \= token.split('-', 1);  
      const p \= priority\[prefix.toLowerCase()\] || 0;  
      if (p \> maxPriority) {  
        maxPriority \= p;  
        bestAlgo \= prefix.toLowerCase();  
      }  
    }

    if (\!bestAlgo) {  
      return { valid: false, reason: 'No supported algorithm tokens found (must be sha256, sha384, or sha512)' };  
    }

    // Candidate tokens matching best algorithm  
    const candidates \= tokens.filter(t \=\> t.toLowerCase().startsWith(\`\${bestAlgo}-\`));  
    const computedHash \= await this.generateHash(content, bestAlgo);

    const matches \= candidates.includes(computedHash);  
    return {  
      valid: matches,  
      algorithmUsed: bestAlgo,  
      computedHash,  
      providedTokens: candidates,  
      reason: matches ? 'Hash matched successfully' : \`Computed \${computedHash} does not match any provided \${bestAlgo} tokens\`  
    };  
  }

  /\*\*  
   \* Synthesize HTML tag with SRI attributes  
   \*/  
  generateHtmlTag(type, url, integrity, crossorigin \= 'anonymous') {  
    if (type \=== 'script') {  
      return \`\<script src="\${url}" integrity="\${integrity}" crossorigin="\${crossorigin}"\>\</script\>\`;  
    } else if (type \=== 'stylesheet') {  
      return \`\<link rel="stylesheet" href="\${url}" integrity="\${integrity}" crossorigin="\${crossorigin}"\>\`;  
    }  
    return '';  
  }  
}

if (typeof module \!== 'undefined' && module.exports) {  
  module.exports \= { SriEngine };  
}  
