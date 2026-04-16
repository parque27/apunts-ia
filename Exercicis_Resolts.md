# Exercicis resolts pas a pas — Intel·ligència Artificial
**UdG · Curs 2025/26**

---

## A. MINIMAX

---

### Exercici A1 — Minimax bàsic (4 nivells)

**Enunciat:** Calcula el valor minimax del node arrel. L'arrel és un node MAX.

```
                            MAX (arrel)
                         /              \
                   MIN (A)              MIN (B)
                  /      \            /       \
              MAX (C)   MAX (D)   MAX (E)    MAX (F)
              / \        / \       / \        / \
             3   5      6   2     1   8      4   7
```

**Resolució:**

Recorrem l'arbre de baix a dalt. Els valors de les fulles estan donats; comencem pels nodes MAX del nivell 3.

**Pas 1: Nivell 3 (MAX) — cada node tria el MÀXIM dels seus fills.**

- Node C: fills = {3, 5} → max(3, 5) = **5**
- Node D: fills = {6, 2} → max(6, 2) = **6**
- Node E: fills = {1, 8} → max(1, 8) = **8**
- Node F: fills = {4, 7} → max(4, 7) = **7**

**Pas 2: Nivell 2 (MIN) — cada node tria el MÍNIM dels seus fills.**

- Node A: fills = {C=5, D=6} → min(5, 6) = **5**
- Node B: fills = {E=8, F=7} → min(8, 7) = **7**

**Pas 3: Nivell 1 (MAX) — l'arrel tria el MÀXIM.**

- Arrel: fills = {A=5, B=7} → max(5, 7) = **7**

**Resultat:** El valor minimax és **7**. L'arrel tria la branca dreta (B).

```
                            MAX → 7
                         /              \
                   MIN → 5              MIN → 7     ← triada
                  /      \            /       \
              MAX→5    MAX→6      MAX→8     MAX→7
              / \        / \       / \        / \
             3   5      6   2     1   8      4   7
```

---

### Exercici A2 — Minimax amb 3 fills per node

**Enunciat:** Calcula el valor minimax. L'arrel = MAX.

```
              MAX (arrel)
           /      |       \
      MIN (A)   MIN (B)   MIN (C)
      / | \     / | \     / | \
     7  3  9   5  4  6   8  2  1
```

**Pas 1: Nivell 2 (MIN):** cada node MIN tria el menor dels seus 3 fills.

- A: min(7, 3, 9) = **3**
- B: min(5, 4, 6) = **4**
- C: min(8, 2, 1) = **1**

**Pas 2: Nivell 1 (MAX):** l'arrel tria el major.

- Arrel: max(3, 4, 1) = **4**

**Resultat:** Valor minimax = **4**. L'arrel tria la branca B.

---

## B. PODA ALFA-BETA

---

### Exercici B1 — Poda alfa-beta bàsica

**Enunciat:** Aplica poda alfa-beta a l'arbre següent (processant d'esquerra a dreta). Identifica els talls i els nodes podats.

```
                            MAX (arrel)
                         /              \
                   MIN (A)              MIN (B)
                  /      \            /       \
              MAX (C)   MAX (D)   MAX (E)    MAX (F)
              / \        / \       / \        / \
             3  12      8   2     6  14      5   9
```

**Resolució:** L'arrel comença amb α = −∞, β = +∞.

---

**Pas 1: Explorar MIN(A) ← hereta α=−∞, β=+∞ del pare MAX(arrel).**

**Pas 1a: Explorar MAX(C) ← hereta α=−∞, β=+∞ del pare MIN(A).**

- Avalua fulla 3 → v = 3. Actualitza α = max(−∞, 3) = 3.
- Avalua fulla 12 → v = max(3, 12) = 12. Actualitza α = 12.
- No hi ha tall (α=12 < β=+∞).
- **MAX(C) retorna 12.**

**Pas 1b: MIN(A) rep el valor 12 del primer fill.**

- Actualitza β = min(+∞, 12) = 12.
- α=−∞ < β=12 → no hi ha tall, continuem.

**Pas 1c: Explorar MAX(D) ← hereta α=−∞, β=12 de MIN(A).**

- Avalua fulla 8 → v = 8. Actualitza α = max(−∞, 8) = 8.
- Avalua fulla 2 → v = max(8, 2) = 8. α segueix 8.
- **MAX(D) retorna 8.**

**Pas 1d: MIN(A) rep 8.**

- v = min(12, 8) = 8. Actualitza β = min(12, 8) = 8.
- **MIN(A) retorna 8.**

**Pas 2: L'arrel MAX rep 8 de MIN(A). Actualitza α = max(−∞, 8) = 8.**

---

**Pas 3: Explorar MIN(B) ← hereta α=8, β=+∞ de l'arrel.**

**Pas 3a: Explorar MAX(E) ← hereta α=8, β=+∞ de MIN(B).**

- Avalua fulla 6 → v = 6. Actualitza α = max(8, 6) = 8.
   (Nota: α heretada = 8 > 6, no canvia.)
- Avalua fulla 14 → v = max(6, 14) = 14. Actualitza α = max(8, 14) = 14.
- **MAX(E) retorna 14.**

**Pas 3b: MIN(B) rep 14.**

- Actualitza β = min(+∞, 14) = 14.
- α=8 < β=14 → no hi ha tall, continuem.

**Pas 3c: Explorar MAX(F) ← hereta α=8, β=14 de MIN(B).**

- Avalua fulla 5 → v = 5. α = max(8, 5) = 8.
- Avalua fulla 9 → v = max(5, 9) = 9. α = max(8, 9) = 9.
- **MAX(F) retorna 9.**

**Pas 3d: MIN(B) rep 9.**

- v = min(14, 9) = 9. β = min(14, 9) = 9.
- **MIN(B) retorna 9.**

**Pas 4: L'arrel MAX rep 9. v = max(8, 9) = 9.**

**Resultat:** Valor minimax = **9**. Cap node s'ha pogut podar en aquest exemple — l'ordenació no era favorable. Es van explorar tots els 8 nodes fulla.

---

### Exercici B2 — Poda alfa-beta amb talls

**Enunciat:** Aplica alfa-beta. Indica on es produeixen els talls.

```
                       MAX (arrel)
                      /           \
                MIN (A)           MIN (B)
               /       \         /       \
              3         5       2         x
```
Fulles: 3, 5, 2, x (x és desconegut).

**Resolució:**

**Pas 1: Explorar MIN(A) ← α=−∞, β=+∞.**

- Avalua fulla 3 → v = 3. β = min(+∞, 3) = 3.
- Comprova: α=−∞ < β=3 → no hi ha tall.
- Avalua fulla 5 → v = min(3, 5) = 3. β segueix 3.
- **MIN(A) retorna 3.**

**Pas 2: L'arrel MAX rep 3. Actualitza α = max(−∞, 3) = 3.**

**Pas 3: Explorar MIN(B) ← α=3, β=+∞.**

- Avalua fulla 2 → v = 2. β = min(+∞, 2) = 2.
- **Comprova: β = 2 ≤ α = 3 → TALL ALFA!**
- Justificació: la branca B com a molt valdrà 2 (o menys, segons x). Com que MAX ja té assegurada l'opció 3 de la branca A, mai triarà B. No cal avaluar x.

**Resultat:** Valor minimax = **3**. L'arrel tria A. **Node x poddat** (tall alfa).

---

### Exercici B3 — Poda alfa-beta complet (arbre gran)

**Enunciat:** Arbre de 4 nivells. Aplica alfa-beta d'esquerra a dreta.

```
                                MAX (arrel)
                           /                    \
                     MIN (A)                    MIN (B)
                    /        \                /         \
               MAX (C)     MAX (D)      MAX (E)       MAX (F)
               / \          / \          / \            / \
              5   3        7   9        1   6          4   2
```

**Resolució detallada:**

**Fase 1: Branca esquerra (A)**

**MAX(C)** ← α=−∞, β=+∞
- Fulla 5: v=5, α = max(−∞, 5) = 5.
- Fulla 3: v = max(5, 3) = 5. α = 5.
- **Retorna 5.**

**MIN(A)** rep 5 → β = min(+∞, 5) = 5.
- Comprova: α=−∞ < β=5 → no talla. Continua amb D.

**MAX(D)** ← α=−∞, β=5
- Fulla 7: v=7, α = max(−∞, 7) = 7.
- **Comprova: α=7 ≥ β=5 → TALL BETA!**
- Justificació: MAX(D) ja val ≥7, però el pare MIN(A) ja té l'opció de triar 5 via C. MIN mai triarà D (que és ≥7 > 5). No cal avaluar la fulla 9.
- **Retorna 7** (valor parcial; MIN l'ignorarà igualment).

**MIN(A)**: v = min(5, 7) = 5. β = 5.
- **Retorna 5.**

**Arrel MAX** rep 5 → α = max(−∞, 5) = 5.

---

**Fase 2: Branca dreta (B)**

**MAX(E)** ← α=5, β=+∞
- Fulla 1: v=1. α = max(5, 1) = 5 (heretat, no canvia).
- Fulla 6: v = max(1, 6) = 6. α = max(5, 6) = 6.
- **Retorna 6.**

**MIN(B)** rep 6 → β = min(+∞, 6) = 6.
- Comprova: α=5 < β=6 → no talla. Continua amb F.

**MAX(F)** ← α=5, β=6
- Fulla 4: v=4. α = max(5, 4) = 5.
- Fulla 2: v = max(4, 2) = 4. α = 5.
- **Retorna 4.**

**MIN(B)**: v = min(6, 4) = 4. β = 4.
- **Retorna 4.**

**Arrel MAX**: v = max(5, 4) = **5**.

**Resum de l'exercici:**

| Resultat | Detall |
|---|---|
| Valor minimax | **5** |
| Talls | 1 tall beta (fulla 9, dins MAX(D)) |
| Nodes fulla avaluats | 7 de 8 |
| Decisió de l'arrel | Tria branca A |

---

### Exercici B4 — Simula minimax pas a pas (exercici de slides)

**Enunciat:** Del material de classe. Arbre de 3 nivells. Recorregut de S a G.

```
             MAX
          /    |    \
       MIN    MIN    MIN
       / \   / \    / \
      5  3  7  9   6  4
```

**Pas 1 (MIN esquerra):** min(5, 3) = **3**
**Pas 2 (MIN centre):** min(7, 9) = **7**
**Pas 3 (MIN dreta):** min(6, 4) = **4**
**Pas 4 (MAX arrel):** max(3, 7, 4) = **7**

**Resultat:** Valor minimax = **7**. L'arrel tria la branca central.

---

## C. TEOREMA DE BAYES I PROBABILITAT

---

### Exercici C1 — Regla de Bayes directa (Spam)

**Enunciat:** La paraula "oferta" apareix en el 80% dels correus spam i en el 10% dels correus no-spam. El 30% dels correus rebuts són spam. Donat un correu amb "oferta", quina és la probabilitat que sigui spam?

**Dades:**
$$p(\text{oferta} \mid \text{spam}) = 0.80, \quad p(\text{oferta} \mid \neg\text{spam}) = 0.10, \quad p(\text{spam}) = 0.30$$

**Pas 1: Identifica què volem.**

$$p(\text{spam} \mid \text{oferta}) = \frac{p(\text{oferta} \mid \text{spam}) \cdot p(\text{spam})}{p(\text{oferta})}$$

**Pas 2: Calcula el denominador $p(\text{oferta})$ amb la llei de probabilitats totals.**

$$p(\text{oferta}) = p(\text{oferta} \mid \text{spam}) \cdot p(\text{spam}) + p(\text{oferta} \mid \neg\text{spam}) \cdot p(\neg\text{spam})$$
$$= 0.80 \times 0.30 + 0.10 \times 0.70 = 0.24 + 0.07 = 0.31$$

**Pas 3: Aplica Bayes.**

$$p(\text{spam} \mid \text{oferta}) = \frac{0.80 \times 0.30}{0.31} = \frac{0.24}{0.31} \approx \mathbf{0.774}$$

**Interpretació:** Tot i que només el 30% dels correus són spam, si un correu conté "oferta", la probabilitat puja al 77.4%.

---

### Exercici C2 — Regla de Bayes amb urnes

**Enunciat:** Tenim dues urnes. Urna 1: 3 boles verdes, 2 vermelles. Urna 2: 1 verda, 4 vermelles. Triem una urna a l'atzar (50/50) i en traiem una bola verda. Quina és la probabilitat d'haver triat la urna 1?

**Dades:**
$$p(V \mid U_1) = 3/5 = 0.6, \quad p(V \mid U_2) = 1/5 = 0.2, \quad p(U_1) = p(U_2) = 0.5$$

**Pas 1:** Volem $p(U_1 \mid V)$.

**Pas 2:** Denominador:
$$p(V) = p(V \mid U_1) \cdot p(U_1) + p(V \mid U_2) \cdot p(U_2) = 0.6 \times 0.5 + 0.2 \times 0.5 = 0.30 + 0.10 = 0.40$$

**Pas 3:** Bayes:
$$p(U_1 \mid V) = \frac{0.6 \times 0.5}{0.40} = \frac{0.30}{0.40} = \mathbf{0.75}$$

**Verificació:** $p(U_2 \mid V) = 0.10 / 0.40 = 0.25$. Suma: $0.75 + 0.25 = 1$ ✓

---

### Exercici C3 — Inferència per enumeració

**Enunciat:** Donada la taula conjunta, calcula $p(T \mid S = \text{fred})$.

| E | S | T | p(E,S,T) |
|---|---|---|---|
| estiu | calor | sol | 0.29 |
| estiu | calor | pluja | 0.12 |
| estiu | fred | sol | 0.02 |
| estiu | fred | pluja | 0.04 |
| hivern | calor | sol | 0.08 |
| hivern | calor | pluja | 0.06 |
| hivern | fred | sol | 0.24 |
| hivern | fred | pluja | 0.15 |

**Pas 1: Identifica variables.**
- Consulta: $T$
- Evidència: $S = \text{fred}$
- Oculta: $E$

**Pas 2: Filtra files consistents amb l'evidència $S = \text{fred}$.**

| E | T | p(E, S=fred, T) |
|---|---|---|
| estiu | sol | 0.02 |
| estiu | pluja | 0.04 |
| hivern | sol | 0.24 |
| hivern | pluja | 0.15 |

**Pas 3: Marginalitza $E$ (suma sobre estiu/hivern per cada valor de $T$).**

| T | p(T, S=fred) |
|---|---|
| sol | 0.02 + 0.24 = **0.26** |
| pluja | 0.04 + 0.15 = **0.19** |

**Pas 4: Normalitza.** Suma = 0.26 + 0.19 = 0.45.

| T | p(T \| S=fred) |
|---|---|
| sol | 0.26 / 0.45 = **0.578** |
| pluja | 0.19 / 0.45 = **0.422** |

**Verificació:** 0.578 + 0.422 = 1.00 ✓

---

### Exercici C4 — Eliminació de variables

**Enunciat:** XB amb 3 variables: $R \to T \to L$. Calcula $p(L)$.

**Taules:**

| R | p(R) |
|---|---|
| +r | 0.1 |
| -r | 0.9 |

| R | T | p(T\|R) |
|---|---|---|
| +r | +t | 0.8 |
| +r | -t | 0.2 |
| -r | +t | 0.1 |
| -r | -t | 0.9 |

| T | L | p(L\|T) |
|---|---|---|
| +t | +l | 0.3 |
| +t | -l | 0.7 |
| -t | +l | 0.1 |
| -t | -l | 0.9 |

**Variables:** consulta = L; ocultes = R, T; evidència = cap.

**Pas 1: Eliminar $R$.** Identifica les taules que contenen $R$: $p(R)$ i $p(T|R)$.

Producte: $p(R) \cdot p(T|R) = p(R, T)$:

| R | T | p(R,T) |
|---|---|---|
| +r | +t | 0.1 × 0.8 = 0.08 |
| +r | -t | 0.1 × 0.2 = 0.02 |
| -r | +t | 0.9 × 0.1 = 0.09 |
| -r | -t | 0.9 × 0.9 = 0.81 |

Marginalitza $R$ (suma per cada valor de $T$):

| T | p(T) |
|---|---|
| +t | 0.08 + 0.09 = **0.17** |
| -t | 0.02 + 0.81 = **0.83** |

**Verificació parcial:** 0.17 + 0.83 = 1.00 ✓

**Pas 2: Eliminar $T$.** Taules amb $T$: $p(T)$ i $p(L|T)$.

Producte: $p(T) \cdot p(L|T) = p(T, L)$:

| T | L | p(T,L) |
|---|---|---|
| +t | +l | 0.17 × 0.3 = 0.051 |
| +t | -l | 0.17 × 0.7 = 0.119 |
| -t | +l | 0.83 × 0.1 = 0.083 |
| -t | -l | 0.83 × 0.9 = 0.747 |

Marginalitza $T$:

| L | p(L) |
|---|---|
| +l | 0.051 + 0.083 = **0.134** |
| -l | 0.119 + 0.747 = **0.866** |

**Verificació:** 0.134 + 0.866 = 1.000 ✓

**Resultat:** $p(L = +l) = 0.134$, $p(L = -l) = 0.866$.

---

### Exercici C5 — Eliminació de variables amb evidència

**Enunciat:** Amb la mateixa XB $R \to T \to L$, calcula $p(L \mid R = +r)$.

**Pas 1: Reducció.** Fixa $R = +r$ a les taules que contenen $R$.

$p(R=+r)$ ja no és una taula; és una constant = 0.1 (la guardem per normalitzar al final si cal).

$p(T | R=+r)$:

| T | p(T\|R=+r) |
|---|---|
| +t | 0.8 |
| -t | 0.2 |

**Pas 2: Eliminar $T$.** Producte de $p(T|R=+r)$ i $p(L|T)$:

| T | L | producte |
|---|---|---|
| +t | +l | 0.8 × 0.3 = 0.24 |
| +t | -l | 0.8 × 0.7 = 0.56 |
| -t | +l | 0.2 × 0.1 = 0.02 |
| -t | -l | 0.2 × 0.9 = 0.18 |

Marginalitza $T$:

| L | p(L, R=+r) (no normalitzat) |
|---|---|
| +l | 0.24 + 0.02 = **0.26** |
| -l | 0.56 + 0.18 = **0.74** |

**Pas 3: Normalitza.** Suma = 0.26 + 0.74 = 1.00 (ja normalitzat perquè $p(T|R=+r)$ ja era una distribució completa).

**Resultat:** $p(L = +l \mid R = +r) = \mathbf{0.26}$, $p(L = -l \mid R = +r) = \mathbf{0.74}$.

**Comparació:** Sense evidència, $p(+l) = 0.134$. Amb $R = +r$, puja a $0.26$. Saber que plou ($R$) quasi duplica la probabilitat de $L$, perquè la pluja causa el terreny moll.

---

### Exercici C6 — Naïve Bayes complet (detecció de spam)

**Enunciat:** Tenim 8 emails etiquetats:

| Email | Remitent ($X_1$) | Majúscules ($X_2$) | €/$ ($X_3$) | "free" ($X_4$) | Classe |
|---|---|---|---|---|---|
| 1 | desconegut | sí | sí | sí | spam |
| 2 | desconegut | no | no | sí | spam |
| 3 | desconegut | sí | sí | no | spam |
| 4 | conegut | no | no | no | no |
| 5 | desconegut | no | no | no | no |
| 6 | conegut | no | no | sí | no |
| 7 | desconegut | no | sí | no | no |
| 8 | conegut | no | no | no | no |

**Pas 1: Estima paràmetres (amb suavitzat de Laplace, $|d(X_i)| = 2$ per a totes).**

$p(\text{spam}) = (3+1)/(8+2) = 4/10 = 0.4$, $p(\text{no}) = (5+1)/(8+2) = 6/10 = 0.6$

Per cada variable condicionada a la classe:

**$X_1$ (remitent):**
- $p(\text{desc} \mid \text{spam}) = (3+1)/(3+2) = 4/5$
- $p(\text{con} \mid \text{spam}) = (0+1)/(3+2) = 1/5$
- $p(\text{desc} \mid \text{no}) = (2+1)/(5+2) = 3/7$
- $p(\text{con} \mid \text{no}) = (3+1)/(5+2) = 4/7$

**$X_2$ (majúscules):**
- $p(\text{sí} \mid \text{spam}) = (2+1)/(3+2) = 3/5$
- $p(\text{no} \mid \text{spam}) = (1+1)/(3+2) = 2/5$
- $p(\text{sí} \mid \text{no}) = (0+1)/(5+2) = 1/7$
- $p(\text{no} \mid \text{no}) = (5+1)/(5+2) = 6/7$

**$X_3$ (€/$):**
- $p(\text{sí} \mid \text{spam}) = (2+1)/(3+2) = 3/5$
- $p(\text{no} \mid \text{spam}) = (1+1)/(3+2) = 2/5$
- $p(\text{sí} \mid \text{no}) = (1+1)/(5+2) = 2/7$
- $p(\text{no} \mid \text{no}) = (4+1)/(5+2) = 5/7$

**$X_4$ ("free"):**
- $p(\text{sí} \mid \text{spam}) = (2+1)/(3+2) = 3/5$
- $p(\text{no} \mid \text{spam}) = (1+1)/(3+2) = 2/5$
- $p(\text{sí} \mid \text{no}) = (1+1)/(5+2) = 2/7$
- $p(\text{no} \mid \text{no}) = (4+1)/(5+2) = 5/7$

**Pas 2: Classifica un nou email: desconegut, sí majúscules, sí €, no "free".**

$$\text{score(spam)} = p(\text{spam}) \cdot p(\text{desc}|\text{sp}) \cdot p(\text{sí}|\text{sp}) \cdot p(\text{sí}|\text{sp}) \cdot p(\text{no}|\text{sp})$$
$$= 0.4 \times \frac{4}{5} \times \frac{3}{5} \times \frac{3}{5} \times \frac{2}{5} = 0.4 \times 0.8 \times 0.6 \times 0.6 \times 0.4 = \mathbf{0.04608}$$

$$\text{score(no)} = p(\text{no}) \cdot p(\text{desc}|\text{no}) \cdot p(\text{sí}|\text{no}) \cdot p(\text{sí}|\text{no}) \cdot p(\text{no}|\text{no})$$
$$= 0.6 \times \frac{3}{7} \times \frac{1}{7} \times \frac{2}{7} \times \frac{5}{7} = 0.6 \times 0.4286 \times 0.1429 \times 0.2857 \times 0.7143 = \mathbf{0.00750}$$

**Pas 3: Normalitza.**

$$p(\text{spam} \mid \mathbf{x}) = \frac{0.04608}{0.04608 + 0.00750} = \frac{0.04608}{0.05358} \approx \mathbf{0.860}$$

$$p(\text{no} \mid \mathbf{x}) \approx 0.140$$

**Classificació:** **SPAM** (amb 86% de confiança).

**Nota:** Sense suavitzat de Laplace, $p(\text{sí\_majúscules} \mid \text{no}) = 0/5 = 0$, fent score(no) = 0 i donant 100% spam — un resultat massa radical i poc fiable.

---

### Exercici C7 — Likelihood Weighting

**Enunciat:** XB amb: Nuvós (N) → Pluja (P), Nuvós (N) → Rec (R), {Pluja, Rec} → Moll (M). Consulta: $p(M \mid R = +r)$. Nombres aleatoris: U = {0.37, 0.61, 0.04}.

**Taules:**

| N | p(N) | | N | P | p(P\|N) | | N | R | p(R\|N) |
|---|---|---|---|---|---|---|---|---|---|
| +n | 0.5 | | +n | +p | 0.8 | | +n | +r | 0.1 |
| -n | 0.5 | | +n | -p | 0.2 | | +n | -r | 0.9 |
| | | | -n | +p | 0.2 | | -n | +r | 0.5 |
| | | | -n | -p | 0.8 | | -n | -r | 0.5 |

| R | P | M | p(M\|R,P) |
|---|---|---|---|
| +r | +p | +m | 0.99 |
| +r | +p | -m | 0.01 |
| +r | -p | +m | 0.90 |
| +r | -p | -m | 0.10 |
| -r | +p | +m | 0.90 |
| -r | +p | -m | 0.10 |
| -r | -p | +m | 0.01 |
| -r | -p | -m | 0.99 |

**Ordre ancestral:** N, R, P, M. Evidència: R = +r.

**Mostra 1:** U = {0.37, 0.61, 0.04}

| Variable | Tipus | Acció | Resultat |
|---|---|---|---|
| N | No observada | u₁ = 0.37 < 0.5 → +n | N = +n |
| R | **Observada** | Fixem R = +r. w = 1.0 × p(+r\|+n) = 1.0 × 0.1 = **0.1** | R = +r, w = 0.1 |
| P | No observada | u₂ = 0.61. p(P\|N=+n): +p si u < 0.8 → 0.61 < 0.8 → +p | P = +p |
| M | No observada | u₃ = 0.04. p(M\|R=+r, P=+p): +m si u < 0.99 → 0.04 < 0.99 → +m | M = +m |

**Mostra 1 resultat:** (N=+n, R=+r, P=+p, M=+m), w = 0.1. Acumulem: pes_acum[+m] += 0.1.

*Per obtenir un resultat fiable, caldrien centenars de mostres. Amb 3 mostres, el resultat és una aproximació molt grollera.*

---

### Exercici C8 — Forward Sampling (exercici de les slides)

**Enunciat:** Amb l'XB d'Olimpíades (G, E, P, O, F), ordena ancestralment i genera una mostra amb $U = \{0.93, 0.87, 0.56, 0.13, 0.76\}$.

**Taules resumides:**

| G | p(G) | | E | p(E) |
|---|---|---|---|---|
| no | 0.55 | | poc | 0.10 |
| bona | 0.35 | | norm | 0.60 |
| excel | 0.10 | | molt | 0.30 |

**Ordre ancestral:** G, E, F, P, O.

**Pas 1: G.** u₁ = 0.93. Acumulats: no=0.55, bona=0.90, excel=1.0. 0.93 > 0.90 → **G = excel**.

**Pas 2: E.** u₂ = 0.87. Acumulats: poc=0.10, norm=0.70, molt=1.0. 0.87 > 0.70 → **E = molt**.

**Pas 3: F.** u₃ = 0.56. p(F|E=molt): sí=0.1, no=0.9. 0.56 > 0.1 → **F = no**.

**Pas 4: P.** u₄ = 0.13. p(P|G=excel, E=molt): norm=0.1, notable=0.3, destaca=1.0. 0.13 > 0.1 → **P = notable**.

**Pas 5: O.** u₅ = 0.76. p(O|P=notable): sí=0.2, no=1.0. 0.76 > 0.2 → **O = no**.

**Mostra:** (G=excel, E=molt, F=no, P=notable, O=no).
