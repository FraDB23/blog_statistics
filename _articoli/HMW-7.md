---
layout: page
title: "Homework 7"
permalink: /articoli/HMW-7/
---

**Author:** {{ site.author }}  
**Matricola:** {{ site.author_student_number }}  
**Email:** [{{ site.author_email }}](mailto:{{ site.author_email }})  
**Course:** {{ site.course }} — {{ site.university }}  
**Academic Year:** {{ site.academic_year }}

---

Given a server that receives weekly security updates for n weeks and m attackers who can breach the system with probability p, generate random trajectories of the servers cumulative scores, assign +1 if the server remains secure in a week and -1 if it is breached, draw a parallel with a random walk and count how many trajectories reach each possible total score in your simulation and how those figures converge to the binomial distribution as n and m diverge

---

{% include attacked-server-widget.html %}

---

## Code

```html

{% include attacked-server-widget.html %}

```