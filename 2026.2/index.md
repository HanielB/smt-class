---
layout: home
title: Theory and Practice of SMT Solving
nav_exclude: true
seo:
  type: Course
  name: Theory and Practice of SMT Solving
---

# {{ site.tagline }}
{: .mb-2 }
{{ site.description }}
{: .fs-6 .fw-300 }

{% if site.announcements %}
{{ site.announcements.last }}
[Avisos](announcements.md){: .btn .btn-outline .fs-3 }
{% endif %}

---

- [Syllabus](plan.pdf)
- Instructor:
  - [Haniel Barbosa](https://homepages.dcc.ufmg.br/~hbarbosa/), Office 4323, DCC, hbarbosa@dcc.ufmg.br

---

## Outline
{: .no_toc .text-delta }

1. TOC
{:toc}

---

{: .no_toc .mb-2 }

A deep dive into how SMT solvers work.

There is no required textbook for the course. We will use a variety of reading
materials which will be linked from the lecture notes. That said, the following
books provide important background:

- H.B. Enderton. A Mathematical Introduction to Logic (Second Edition), Academic Press.
- D. Kroening, O. Strichman. Decision Procedures, Springer.
- A. Bradley, Z. Manna. The Calculus of Computation, Springer-Verlag.
- A. Biere, M. Heule, H. van Maaren, T. Walsh.  Handbook of Satisfiability (Second Edition), IOS Press.

{: .fs-6 .fw-300 }

# Topic 1: SAT solving

## Propositional Logic
## Resolution
## Efficient CNF conversion
## DPLL and CDCL
## Efficient SAT solving

# Topic 2: CDCL(T) Architecture

# TODO
