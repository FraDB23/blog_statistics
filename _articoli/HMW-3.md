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

