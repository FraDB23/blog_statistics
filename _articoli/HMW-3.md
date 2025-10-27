---
layout: page
title: "Homework 3"
permalink: /articoli/HMW-3/
---

**Author:** {{ site.author }}  
**Matricola:** {{ site.author_student_number }}  
**Email:** [{{ site.author_email }}](mailto:{{ site.author_email }})  
**Course:** {{ site.course }} — {{ site.university }}  
**Academic Year:** {{ site.academic_year }}

---

# Decrypting a text naively encrypted with RSA

The purpose of this third homework is that of showing how to decrypt a text naively encrypted with RSA with statistical distribution of letters.
"Naively encrypted" means encrypted in a simple or insecure way. For example, using RSA without padding or applying it directly to each letter or small block of text, which makes it vulnerable to attacks.

In this homework i will show first the algorithm that decypts naively a text using RSA letter by letter and then an algorithm that calculates the distribution of letters of the encrypted text. Afterwards we will compare the distribution of letters of the encrypted text with the statistical distribution of letters in the english language revealing decriptying the encypted letters.

---

## Naively encrypting a text with RSA

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Naive RSA</title>
</head>
<body>
  <h1>Naive RSA</h1>

  <!-- Input fields for the two prime numbers p and q -->
  <label for="pInput">Prime p (integer):</label>
  <input id="pInput" type="text" value="5" />
  <br/>
  <label for="qInput">Prime q (integer):</label>
  <input id="qInput" type="text" value="7" />
  <br/>
  <!-- Input field for the plaintext message -->
  <label for="plainInput">Plain text (M):</label>
  <br/>
  <textarea id="plainInput" rows="4" cols="60">The morning sun rises slowly over the quiet town and the light spreads like a soft wave across the rooftops. People begin to step outside their houses with sleepy eyes and calm hearts. The air feels cool and gentle while birds sing in short bursts that sound like tiny bells. Every street smells faintly of bread and coffee and the world seems simple for a few quiet minutes before the rush of the day begins.
As the hours move forward the town fills with voices and footsteps that blend into one long familiar rhythm. Children laugh as they run to school and old men sit on benches telling the same stories again and again. The sound of doors opening and closing becomes a kind of music that repeats through the day. In these ordinary moments people find comfort because nothing feels strange or broken when life follows its gentle pattern.
When evening arrives the sky turns soft and gold and the wind carries the smell of flowers and rain. Lights appear one by one and the streets grow slower and more peaceful. Families gather around tables while small shops close their doors and the town sighs like a living creature ready for rest. Night will come quietly and the cycle will begin again just as it always does calm and steady like the beating of a heart.
  </textarea>
  <br/>

  <!-- Button to start encryption -->
  <button id="encryptBtn">Encrypt (naive)</button>

  <!-- Output area for the ciphertext -->
  <h2>Ciphertext</h2>
  <textarea id="cipherOutput" rows="6" cols="60">—</textarea>
  <div id="message" style="color: red;"></div>

  <script>
    // Compute greatest common divisor (Euclidean algorithm) using BigInt
    function gcdBig(a, b) {
      a = BigInt(a); b = BigInt(b);
      while (b !== 0n) { const t = a % b; a = b; b = t; }
      return a < 0n ? -a : a;
    }

    // Extended Euclidean algorithm: returns gcd and Bézout coefficients
    function egcdBig(a, b) {
      a = BigInt(a); b = BigInt(b);
      if (b === 0n) return { g: a, x: 1n, y: 0n };
      let x0 = 1n, y0 = 0n, x1 = 0n, y1 = 1n;
      while (b !== 0n) {
        const q = a / b, r = a % b;
        const nx = x0 - q * x1, ny = y0 - q * y1;
        a = b; b = r; x0 = x1; y0 = y1; x1 = nx; y1 = ny;
      }
      return { g: a, x: x0, y: y0 };
    }

    // Compute modular inverse of e mod m using extended GCD
    function modInverse(e, m) {
      const { g, x } = egcdBig(e, m);
      let inv = x % m;
      if (inv < 0n) inv += m;
      return inv;
    }

    // Fast modular exponentiation (base^exp mod mod)
    function powMod(base, exp, mod) {
      base = BigInt(base) % BigInt(mod);
      exp = BigInt(exp); mod = BigInt(mod);
      let result = 1n;
      while (exp > 0n) {
        if (exp & 1n) result = (result * base) % mod;
        base = (base * base) % mod;
        exp >>= 1n;
      }
      return result;
    }

    // Generate RSA keys from primes p and q
    function generateKeys(p, q) {
      p = BigInt(p); q = BigInt(q);
      const N = p * q; // modulus
      const phiN = (p - 1n) * (q - 1n); // Euler's totient
      let e = 65537n; // typical RSA public exponent
      // ensure e and phi(N) are coprime
      if (gcdBig(e, phiN) !== 1n) {
        e = 3n;
        while (gcdBig(e, phiN) !== 1n) e += 2n;
      }
      const d = modInverse(e, phiN); // private exponent
      return { N, e, d, phiN };
    }

    // Convert numeric cipher value to a letter (A–Z)
    function mapCtoLetter(C) {
      const idx = Number(C % 26n);
      return String.fromCharCode(65 + idx);
    }

    // Check if a character is an ASCII letter
    function isAsciiLetter(ch) {
      return /^[A-Za-z]$/.test(ch);
    }

    // Encrypt only letters, leaving other characters unchanged
    function encryptLettersOnly(M, e, N) {
      let out = '';
      for (let i = 0; i < M.length; i++) {
        const ch = M[i];
        if (ch === ' ') { out += ' '; continue; } // preserve spaces
        if (!isAsciiLetter(ch)) { out += ch; continue; } // preserve punctuation, etc.
        const upper = ch.toUpperCase();
        const m = BigInt(upper.charCodeAt(0)); // map letter to ASCII code
        const C = powMod(m, e, N); // RSA encryption: C = m^e mod N
        out += mapCtoLetter(C); // map cipher number back to a letter
      }
      return out;
    }

    // Grab DOM elements
    const pInput = document.getElementById('pInput');
    const qInput = document.getElementById('qInput');
    const plainInput = document.getElementById('plainInput');
    const encryptBtn = document.getElementById('encryptBtn');
    const cipherOutput = document.getElementById('cipherOutput');
    const message = document.getElementById('message');

    // Event listener for encryption button
    encryptBtn.addEventListener('click', () => {
      message.textContent = '';
      try {
        const p = BigInt(pInput.value.trim());
        const q = BigInt(qInput.value.trim());
        const M = plainInput.value || '';
        // Generate RSA keys
        const { N, e } = generateKeys(p, q);
        // Encrypt plaintext
        const cipherText = encryptLettersOnly(M, e, N);
        cipherOutput.value = cipherText;
      } catch (err) {
        // Display any error message
        message.textContent = err.message || String(err);
        cipherOutput.value = '—';
      }
    });
  </script>
</body>
</html>
```

**OUTPUT:** 
OGI HERIHIB NPI RHNIN NGEMGY ELIR OGI QPHIO OEMI ZID OGI GHBGO NFRIZDN GHKI Z NEAO MZLI ZCRENN OGI REEAOEFN. FIEFGI AIBHI OE NOIF EPONHDI OGIHR GEPNIN MHOG NGIIFY IYIN ZID CZGH GIZRON. OGI ZHR AIIGN CEEG ZID BIIOGI MGHGI AHRDN NHIB HI NGERO APRNON OGZO NEPID GHKI OHIY AIGGN. ILIRY NORIIO NHIGGN AZHIOGY EA ARIZD ZID CEAAII ZID OGI MERGD NIIHN NHHFGI AER Z AIM QPHIO HHIPOIN AIAERI OGI RPNG EA OGI DZY AIBHIN.
ZN OGI GEPRN HELI AERMZRD OGI OEMI AHGGN MHOG LEHCIN ZID AEEONOIFN OGZO AGIID HIOE EII GEIB AZHHGHZR RGYOGH. CGHGDRII GZPBG ZN OGIY RPI OE NCGEEG ZID EGD HII NHO EI AIICGIN OIGGHIB OGI NZHI NOERHIN ZBZHI ZID ZBZHI. OGI NEPID EA DEERN EFIIHIB ZID CGENHIB AICEHIN Z KHID EA HPNHC OGZO RIFIZON OGREPBG OGI DZY. HI OGINI ERDHIZRY HEHIION FIEFGI AHID CEHAERO AICZPNI IEOGHIB AIIGN NORZIBI ER AREKII MGII GHAI AEGGEMN HON BIIOGI FZOOIRI.
MGII ILIIHIB ZRRHLIN OGI NKY OPRIN NEAO ZID BEGD ZID OGI MHID CZRRHIN OGI NHIGG EA AGEMIRN ZID RZHI. GHBGON ZFFIZR EII AY EII ZID OGI NORIION BREM NGEMIR ZID HERI FIZCIAPG. AZHHGHIN BZOGIR ZREPID OZAGIN MGHGI NHZGG NGEFN CGENI OGIHR DEERN ZID OGI OEMI NHBGN GHKI Z GHLHIB CRIZOPRI RIZDY AER RINO. IHBGO MHGG CEHI QPHIOGY ZID OGI CYCGI MHGG AIBHI ZBZHI JPNO ZN HO ZGMZYN DEIN CZGH ZID NOIZDY GHKI OGI AIZOHIB EA Z GIZRO.

---

## Decripting text by using statistical distribution of letters

The distribution of letters in the english letters is the following:

| Letter | Frequency (%) |
|:------:|:-------------:|
| E | 12.70 |
| T | 9.06 |
| A | 8.17 |
| O | 7.51 |
| I | 6.97 |
| N | 6.75 |
| S | 6.33 |
| H | 6.09 |
| R | 5.99 |
| D | 4.25 |
| L | 4.03 |
| C | 2.78 |
| U | 2.76 |
| M | 2.41 |
| W | 2.36 |
| F | 2.23 |
| G | 2.02 |
| Y | 1.97 |
| P | 1.93 |
| B | 1.49 |
| V | 0.98 |
| K | 0.77 |
| J | 0.15 |
| X | 0.15 |
| Q | 0.10 |
| Z | 0.07 |

By comparing it with the distribution of letters in an encripted text we can decode the text. 
Here is the code (in JS) that does it:

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Simple Naive RSA Frequency Decryptor</title>
</head>
<body>
  <label for="cipher">Ciphertext:</label><br/>
  <textarea id="cipher" rows="12" cols="80">OGI HERIHIB NPI RHNIN NGEMGY ELIR OGI QPHIO OEMI ZID OGI GHBGO NFRIZDN GHKI Z NEAO MZLI ZCRENN OGI REEAOEFN. FIEFGI AIBHI OE NOIF EPONHDI OGIHR GEPNIN MHOG NGIIFY IYIN ZID CZGH GIZRON. OGI ZHR AIIGN CEEG ZID BIIOGI MGHGI AHRDN NHIB HI NGERO APRNON OGZO NEPID GHKI OHIY AIGGN. ILIRY NORIIO NHIGGN AZHIOGY EA ARIZD ZID CEAAII ZID OGI MERGD NIIHN NHHFGI AER Z AIM QPHIO HHIPOIN AIAERI OGI RPNG EA OGI DZY AIBHIN.
ZN OGI GEPRN HELI AERMZRD OGI OEMI AHGGN MHOG LEHCIN ZID AEEONOIFN OGZO AGIID HIOE EII GEIB AZHHGHZR RGYOGH. CGHGDRII GZPBG ZN OGIY RPI OE NCGEEG ZID EGD HII NHO EI AIICGIN OIGGHIB OGI NZHI NOERHIN ZBZHI ZID ZBZHI. OGI NEPID EA DEERN EFIIHIB ZID CGENHIB AICEHIN Z KHID EA HPNHC OGZO RIFIZON OGREPBG OGI DZY. HI OGINI ERDHIZRY HEHIION FIEFGI AHID CEHAERO AICZPNI IEOGHIB AIIGN NORZIBI ER AREKII MGII GHAI AEGGEMN HON BIIOGI FZOOIRI.
MGII ILIIHIB ZRRHLIN OGI NKY OPRIN NEAO ZID BEGD ZID OGI MHID CZRRHIN OGI NHIGG EA AGEMIRN ZID RZHI. GHBGON ZFFIZR EII AY EII ZID OGI NORIION BREM NGEMIR ZID HERI FIZCIAPG. AZHHGHIN BZOGIR ZREPID OZAGIN MGHGI NHZGG NGEFN CGENI OGIHR DEERN ZID OGI OEMI NHBGN GHKI Z GHLHIB CRIZOPRI RIZDY AER RINO. IHBGO MHGG CEHI QPHIOGY ZID OGI CYCGI MHGG AIBHI ZBZHI JPNO ZN HO ZGMZYN DEIN CZGH ZID NOIZDY GHKI OGI AIZOHIB EA Z GIZRO.</textarea>
  <br/>
  <button id="decryptBtn">Decrypt (naive frequency)</button>
  <br/><br/>
  <label for="plain">Decrypted guess:</label><br/>
  <textarea id="plain" rows="12" cols="80" readonly></textarea>

  <script>
    // ------------------------------
    // Reference English letter frequencies
    // ------------------------------
    // These are the target percentage frequencies for letters in typical English text.
    // They are used here only to rank plaintext letter likelihoods for a naive substitution:
    //  - E is the most frequent (12.70%), then T, A, O, ... down to Z (0.07%).
    // Note: This is a coarse statistical model (monogram frequencies) and is only
    //       appropriate for very basic frequency substitution heuristics.
    const ENGLISH_FREQ = {
      'A': 8.17, 'B': 1.49, 'C': 2.78, 'D': 4.25, 'E': 12.70, 'F': 2.23, 'G': 2.02,
      'H': 6.09, 'I': 6.97, 'J': 0.15, 'K': 0.77, 'L': 4.03, 'M': 2.41, 'N': 6.75,
      'O': 7.51, 'P': 1.93, 'Q': 0.10, 'R': 5.99, 'S': 6.33, 'T': 9.06, 'U': 2.76,
      'V': 0.98, 'W': 2.36, 'X': 0.15, 'Y': 1.97, 'Z': 0.07
    };

    // ENGLISH_ORDER is an array of letters sorted by descending frequency.
    // We use this order to create a naive mapping: most frequent cipher letter -> 'E',
    // second most frequent -> 'T', and so on.
    const ENGLISH_ORDER = Object.keys(ENGLISH_FREQ).sort((a,b) => ENGLISH_FREQ[b] - ENGLISH_FREQ[a]);

    // ------------------------------
    // computeFrequencies(text)
    // ------------------------------
    // Count occurrences of each uppercase letter A–Z in the provided text.
    // Returns an object { counts, total } where:
    //  - counts is a map like { 'A': 12, 'B': 3, ... }
    //  - total is the total number of letters counted (sum of counts)
    //
    // Notes:
    //  - Non-letter characters (spaces, punctuation, digits) are ignored for the counts.
    //  - Input is converted to uppercase to keep counting case-insensitive.
    function computeFrequencies(text) {
      const counts = {};
      // initialize counts for A..Z to 0 for deterministic ordering later
      for (let c = 65; c <= 90; c++) counts[String.fromCharCode(c)] = 0;
      let total = 0;
      for (const ch of text.toUpperCase()) {
        // only consider ASCII letters A-Z
        if (ch >= 'A' && ch <= 'Z') {
          counts[ch] = (counts[ch] || 0) + 1;
          total++;
        }
      }
      return { counts, total };
    }

    // ------------------------------
    // mappingByFrequency(countsObj)
    // ------------------------------
    // Build a naive substitution mapping from ciphertext letters to guessed plaintext letters.
    // Steps:
    // 1. Convert counts object into an array of [letter, count] entries.
    // 2. Sort entries by descending count (most frequent cipher letter first).
    //    - Tie-break by letter lexicographically for deterministic behavior.
    // 3. For each cipher letter in that sorted list, assign the corresponding letter
    //    from ENGLISH_ORDER (E, T, A, ...).
    //
    // Result is a mapping like { 'G': 'E', 'H': 'T', ... }.
    function mappingByFrequency(countsObj) {
      const entries = Object.entries(countsObj);
      entries.sort((a,b) => {
        if (b[1] === a[1]) return a[0].localeCompare(b[0]); // deterministic tie-break
        return b[1] - a[1]; // descending by count
      });
      const mapping = {};
      for (let i = 0; i < entries.length; i++) {
        const cipher = entries[i][0];
        // Map the i-th most frequent cipher letter to the i-th most frequent English letter.
        // If ENGLISH_ORDER runs out (shouldn't happen here), fallback to '?'.
        mapping[cipher] = ENGLISH_ORDER[i] || '?';
      }
      return mapping;
    }

    // ------------------------------
    // applyMapping(text, mapping)
    // ------------------------------
    // Apply the substitution mapping to the input text.
    // - Non-letter characters (spaces, punctuation) are preserved.
    // - Letters are converted to uppercase, mapped, and the mapped letter is appended.

    function applyMapping(text, mapping) {
      let out = '';
      for (const ch of text) {
        const up = ch.toUpperCase();
        if (up >= 'A' && up <= 'Z') {
          // If a mapping for this cipher letter exists, use it; otherwise use '?'.
          const mapped = mapping[up] || '?';
          out += mapped;
        } else {
          // Preserve spaces, punctuation, line breaks, etc.
          out += ch;
        }
      }
      return out;
    }
    document.getElementById('decryptBtn').addEventListener('click', () => {
      const cipher = document.getElementById('cipher').value || '';
      const { counts } = computeFrequencies(cipher);   // get letter counts
      const mapping = mappingByFrequency(counts);      // create naive mapping
      const plainGuess = applyMapping(cipher, mapping); // apply substitution
      document.getElementById('plain').value = plainGuess; // show result
    });

    // ------------------------------
    // Auto-run once on load to show an initial guess immediately
    // ------------------------------
    document.getElementById('decryptBtn').click();
  </script>
</body>
</html>
```