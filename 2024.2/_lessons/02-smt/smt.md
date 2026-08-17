---
layout: page
title: SMT solving
---

# SMT Solving
{: .no_toc .mb-2 }

- TOC
{:toc}

## Readings
{: .no_toc .mb-2 }

References to the **Decision Procedures** book will noted as *DP*, while to the **Calculus of Computations** book will be *CC*.

- From SAT to SMT
  - [Slides by Cesare Tinelli]({{ site.baseurl }}{% link _lessons/02-smt/cesare-cdclt.pdf %})

- First-order theories
  - [Slides by Cesare Tinelli]({{ site.baseurl }}{% link _lessons/02-smt/cesare-fol.pdf %})
  - CC: 3
  - DP: 4

- Theory solvers (EUF, IDL, Arrays)
  - CC: 3
  - DP: 4, 5.7, 7
  - [Slides by Bruno Andreotti]({{ site.baseurl }}{% link _lessons/02-smt/bruno-cc.pdf %}) on congruence closure
  - [Slides by Cesare Tinelli]({{ site.baseurl }}{% link _lessons/02-smt/cesare-th-auf-idl.pdf %})

- Theory solvers (Linear Real Arithmetic)
  - DP: 5.1, 5.2
  - [Slides by Cesare Tinelli]({{ site.baseurl }}{% link _lessons/02-smt/cesare-th-arith.pdf %})
  - [A Fast Linear-Arithmetic Solver for DPLL(T)]({{ site.baseurl }}{% link _lessons/02-smt/Dutertre2006.pdf %})

- Theory solvers (Bit-vectors)
  - DP: 6.1, 6.2

## Toy bit-blaster

We can build a toy bit-blaster (for a few of the BV operators) on top of cvc5's QF_UF solver as in the example below, using its [pythonic API](https://cvc5.github.io/docs/cvc5-1.2.0/api/python/pythonic/pythonic.html):

```python
#! /usr/bin/python

from cvc5.pythonic import *

size = 2

bbvars = set({})

## Termos tem o formato

# - bvadd
#   ["bvadd", t1, t2]
# - var
#   ["var", "x"]
# - const
#   ["const", "01"]
# - bbt (para termos já "bit-blasted")
#   ["bbt", t1, t2]


# A função de bitblasting para um termo recebe um termo (limitado a um
# dos formatos acima) e gera uma lista de variáveis correspondendo a
# cada bit do bit-vector
def bitblastTerm(solver, t):
    op = t[0]
    res = ["bbt"]
    match op:
        case "const":
            for i in range(0, size):
                res += [False if t[1][i] == "0" else True]
        case "var":
            for i in range(0, size):
                var = Bool("{}[{}]".format(t[1], i))
                bbvars.add(var)
                res += [var]
            print(res)
        case "bvadd":
            bbt1 = bitblastTerm(solver, t[1])
            assert bbt1[0] == "bbt"
            bbt2 = bitblastTerm(solver, t[2])
            assert bbt2[0] == "bbt"
            carry = False
            res += [None for i in range(0, size) ]
            for i in range(0, size):
                j = size - i
                res[j] = Xor(Xor(bbt1[j], bbt2[j]), carry)
                carry = Or(
                    And(bbt1[j], bbt2[j]),
                    And(Xor(bbt1[j], bbt2[j]), carry))

    return res


## Fórmulas tem o formato

# - bveq
#   ["bveq", t1, t2]
# - bvult
#   ["bvult", t1, t2]

# A função de bitblasting para uma fórmula recebe uma fórmula
# (limitada a um dos formatos acima) e gera fórmula SMT cuja
# satisfatibilidade corresponde à formula passada
def bitblastFormula(solver, f):
    op = f[0]
    match op:
        case "bveq":
            print("eq")
            bbt1 = bitblastTerm(solver, f[1])
            assert bbt1[0] == "bbt"
            bbt2 = bitblastTerm(solver, f[2])
            assert bbt2[0] == "bbt"
            bitEqs = []
            for i in range(1, size + 1):
                bitEqs += [ bbt1[i] == bbt2[i] ]
            solver.add(And(bitEqs))
        case "bvult":
            print("ult")
            bbt1 = bitblastTerm(solver, f[1])
            assert bbt1[0] == "bbt"
            bbt2 = bitblastTerm(solver, f[2])
            assert bbt2[0] == "bbt"
            res = And(Not(bbt1[size]), bbt2[size])
            for i in range(0, size):
                j = size - i
                res = \
                  Or(And(bbt1[j] == bbt2[j], res),
                     And(Not(bbt1[j]), bbt2[j]))
            print(res)
            solver.add(res)

if __name__ == '__main__':
    t1 = ["var", "x"]
    t2 = ["const", "01"]
    t3 = ["bvadd", t1, t2]
    t4 = ["const", "11"]

    f = ["bvult", t3, t4]

    solver = SolverFor('QF_UF')

    bitblastFormula(solver, f)
    while (solver.check() == sat):
        print("sat")
        m = solver.model()
        blocking = []
        for v in bbvars:
            print("{}: {}".format(v, m[v]))
            blocking += [v != m[v]]
        solver.add(Or(blocking))
    print(unsat)
```
