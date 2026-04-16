# Xuleta d'Examen — Intel·ligència Artificial
**UdG · Curs 2025/26 · Temes 1, 2 i 3**

---

## 1. CERCA

### 1.1 Formulació d'un problema de cerca
Cinc elements: **estat inicial**, **accions**, **model de transició**, **cost**, **test d'objectiu**.

### 1.2 Tipus de cerca

| Tipus | $f(e)$ | Complet? | Òptim? | Notes |
|---|---|---|---|---|
| Cost uniforme | $g(e)$ | Sí | Sí | Cerca a cegues; entorns concèntrics |
| BFS voraç | $h(e)$ | No garantit | No | Ràpid però no fiable |
| **A*** | $g(e) + h(e)$ | Sí | Sí* | *Si $h$ admissible/consistent |

### 1.3 Algoritme A* — Pas a pas

**Procediment:**
1. Inicialitza FRONTERA = $\{(s_0,\; h(s_0))\}$, TANCATS = $\emptyset$.
2. Extreu de FRONTERA el node $e$ amb menor $f(e)$.
3. Si $e$ és objectiu → retorna solució i cost.
4. Afegeix $e$ a TANCATS.
5. Per cada successor $e_s$ de $e$ (que no sigui a TANCATS):
   - Si $e_s$ no és a FRONTERA → afegeix $(e_s,\; g(e) + c(e, e_s) + h(e_s))$.
   - Si $e_s$ ja hi és amb cost pitjor → actualitza'l.
6. Torna al pas 2.

**Exemple guiat:** Graf amb A → E.

```
A ──4── B ──2── C ──3── E
│                       │
└────────7──── D ──2────┘
h: A=6, B=3, C=1, D=2, E=0
```
Arcs: A-B(4), B-C(2), C-E(3), A-D(7), D-E(2).

| It. | Expandeix | FRONTERA (node: f=g+h) | TANCATS |
|---|---|---|---|
| 0 | — | A:0+6=6 | ∅ |
| 1 | A (f=6) | B:4+3=7, D:7+2=9 | {A} |
| 2 | B (f=7) | C:6+1=7, D:9 | {A,B} |
| 3 | C (f=7) | E:9+0=9, D:9 | {A,B,C} |
| 4 | D (f=9) | E:9 (no millora, g=9 des de D també) | {A,B,C,D} |
| 5 | E (f=9) | **Objectiu!** Cost = 9. Camí: A→B→C→E | |

> **Clau**: el cost d'un node a FRONTERA es pot **actualitzar** si es troba un camí més barat. Aquí D→E dóna g=9 igual que C→E, no s'actualitza.

### 1.4 Propietats de l'heurística

| Propietat | Definició | Importància |
|---|---|---|
| Admissible | $0 \leq h(e) \leq c^*(e)$ | A* és òptim |
| Consistent | $h(A) \leq c(A,B) + h(B)$ | $f$ no decreix; TANCATS segurs |

**Com verificar admissibilitat**: per cada node, comprova que $h(e)$ no supera el cost real del camí més curt a l'objectiu.

**Com verificar consistència**: per cada arc $(A, B)$ del graf, comprova $h(A) - h(B) \leq c(A, B)$.

**Regla pràctica**: Consistència $\Rightarrow$ admissibilitat (però no al revés).

### 1.5 Disseny d'heurístiques: relaxació del problema

Eliminar restriccions del problema original → el cost del problema relaxat és una heurística admissible.

**8-puzle:**
- Eliminar "adjacent" i "free" → $h_1$ = fitxes mal col·locades.
- Eliminar "free" → $h_2$ = suma distàncies Manhattan.
- Sempre: $h_1 \leq h_2 \leq h^*$ (Manhattan és més informativa).

**Exercici tipus**: "Proposa una heurística admissible per al TSP"
- Mínim cost de sortida entre les ciutats no visitades.
- Suma dels arcs mínims restants / n.

---

### 1.6 Cerca local — Resum ràpid

| Mètode | Idea | Avantatge | Desavantatge |
|---|---|---|---|
| Random walk | Veí aleatori | Complet | Lentíssim |
| Hill-climbing | Millor veí | Ràpid | Màxims locals |
| Simulated Ann. | Veí aleatori + accepta pitjors | Escapa locals | Cal ajustar $T$ |
| Beam search | K millors | Diversitat parcial | Convergeix |
| Genètic | Selec+encreu+mutació | Diversitat real | Complex |

**Simulated Annealing** — Decisió d'acceptació:
$$p(\text{acceptar}) = \begin{cases} 1 & \text{si } \Delta F \geq 0 \\ e^{\Delta F / T} & \text{si } \Delta F < 0 \end{cases}$$

**Cas especial**: SA amb $T \to 0$ ≡ hill-climbing; SA amb $T = \infty$ ≡ random walk.

---

### 1.7 Minimax i poda alfa-beta

**Minimax**: els nivells del teu torn fan MAX, els de l'oponent fan MIN. Recorre l'arbre en profunditat.

**Pas a pas per resoldre un arbre minimax:**
1. Identifica els nivells MAX i MIN (alternant des de l'arrel = MAX).
2. Als nodes fulla, anota la utilitat.
3. Propaga cap amunt: MAX tria el major fill, MIN tria el menor.

**Poda alfa-beta:**
- $\alpha$ = millor opció trobada fins ara per MAX (comença a $-\infty$).
- $\beta$ = millor opció trobada fins ara per MIN (comença a $+\infty$).
- **Tall alfa** (en node MIN): si $\beta \leq \alpha$ → poda (MAX ja té opció millor).
- **Tall beta** (en node MAX): si $\alpha \geq \beta$ → poda (MIN ja té opció millor).

**Exemple:**
```
         MAX
       /     \
     MIN      MIN
    / \      / \
   3   8    2   x
```
- Esquerra: MIN avalua fill 3, després 8 → min(3,8) = 3. Ara α (del MAX arrel) s'actualitza a 3.
- Dreta: MIN avalua fill 2 → β = 2. Com que β=2 ≤ α=3, es produeix **tall alfa**: MAX ja té una opció que val 3, i la branca dreta com a molt valdrà 2 (o menys). El node `x` **es poda** (no cal avaluar-lo).
- Resultat: valor minimax de l'arrel = **3**.

**Complexitat amb poda**: amb ordenació perfecta, s'aconsegueix $O(b^{p/2})$ → **duplica la profunditat** explorable.

---

## 2. PLANIFICACIÓ (PLANNING)

### 2.1 Conceptes fonamentals

**Problema STRIPS**: $\Pi = (Atoms, s_0, G, A)$

- **Estat** = conjunt d'àtoms ground certs (món tancat: no present = fals).
- **Acció** $a = \langle Pre, \langle eff^+, eff^-\rangle\rangle$:
  - Aplicable si $Pre \subseteq s$.
  - Resultat: $a(s) = (s \cup eff^+) \setminus eff^-$.
- **Pla** = seqüència $[a_1, \ldots, a_n]$ tal que $G \subseteq a_n(\dots(a_1(s_0))\dots)$.

### 2.2 Resolució pas a pas (progressió)

**Recepta per aplicar una acció $a$ a un estat $s$:**
1. Verifica que $Pre \subseteq s$ (totes les precondicions certes a $s$).
2. Afegeix $eff^+$ a $s$.
3. Elimina $eff^-$ de $s$.
4. El resultat és el nou estat $s'$.

**Exemple: Hanoi move(d1, d2, p2)**
```
Pre:  {on(d1,d2), clear(d1), smaller(d1,p2), clear(p2)}
eff+: {on(d1,p2), clear(d2)}
eff-: {clear(p2), on(d1,d2)}

s₀ = {clear(d1), clear(p2), clear(p3), on(d1,d2), on(d2,d3), on(d3,p1), smaller(...)}

Pre ⊆ s₀? → Sí (tots presents).

s₁ = (s₀ ∪ {on(d1,p2), clear(d2)}) \ {clear(p2), on(d1,d2)}
   = {clear(d1), clear(d2), clear(p3), on(d1,p2), on(d2,d3), on(d3,p1), smaller(...)}
```

### 2.3 Resolució pas a pas (regressió)

**Recepta per regressar un subobjectiu $G'$ a través d'una acció $a$:**
1. Comprova que cap àtom de $G'$ estigui a $eff^-$ (si sí → $\bot$, acció invàlida).
2. Elimina de $G'$ els àtoms que apareixen a $eff^+$.
3. Afegeix $Pre$ al resultat.

$$\text{sregr}(G', a) = (G' \setminus eff^+) \cup Pre$$

**Exemple: Hanoi, regressió de $G$ amb move(d1, p1, d2)**
```
G = {on(d1,d2), on(d2,d3), on(d3,p3)}
eff- = {clear(d2), on(d1,p1)} → cap a G → OK
eff+ = {on(d1,d2), clear(p1)} → eliminem on(d1,d2) de G
Pre  = {on(d1,p1), clear(d1), smaller(d1,d2), clear(d2)}

G' = ({on(d2,d3), on(d3,p3)}) ∪ {on(d1,p1), clear(d1), smaller(d1,d2), clear(d2)}
   = {on(d1,p1), clear(d1), smaller(d1,d2), clear(d2), on(d2,d3), on(d3,p3)}
```

### 2.4 Heurístiques de planning

$$h_{max} \leq h^+ \leq h^* \leq h_{add}$$

| Heurística | Com es calcula | Admissible? | Quan usar |
|---|---|---|---|
| $h_{max}$ | Màx cost d'un subobjectiu | Sí | Quan cal garantia d'optimalitat |
| $h^+$ | Resolent sense delete lists | Sí (òptim) | Teòricament ideal però NP-hard |
| $h_{add}$ | Suma de costos separats | No | Quan volem trobar qualsevol pla ràpid |

### 2.5 PDDL — Estructura

**Fitxer domini:**
```lisp
(define (domain NOM)
  (:requirements :strips)  ; o :adl, :fluents, etc.
  (:types ...)              ; tipatge d'objectes (opcional)
  (:predicates (pred1 ?x) (pred2 ?x ?y) ...)
  (:functions (func1 ?x) ...)  ; numeric fluents (PDDL 2.1)
  (:action NOM_ACCIÓ
    :parameters (?x - tipus ?y - tipus)
    :precondition (and ...)
    :effect (and ... (not ...) (increase ...) ...)))
```

**Fitxer problema:**
```lisp
(define (problem NOM)
  (:domain NOM_DOMINI)
  (:objects obj1 obj2 - tipus ...)
  (:init (pred1 obj1) (= (func1 obj1) 10) ...)
  (:goal (and (pred2 obj1 obj2) ...))
  (:metric minimize (total-cost)))   ; opcional
```

---

## 3. RAONAMENT PROBABILÍSTIC I XBs

### 3.1 Fórmules fonamentals (full resum)

| Fórmula | Expressió |
|---|---|
| Conjunta → marginal | $p(X) = \sum_y p(X, Y=y)$ |
| Condicional | $p(X \mid Y) = \frac{p(X,Y)}{p(Y)}$ |
| Producte (cadena) | $p(X,Y) = p(X \mid Y) \cdot p(Y)$ |
| Independència | $p(X,Y) = p(X) \cdot p(Y)$ |
| Indep. condicional | $p(X,Y \mid Z) = p(X \mid Z) \cdot p(Y \mid Z)$ |
| Bayes | $p(X \mid Y) = \frac{p(Y \mid X) \cdot p(X)}{p(Y)}$ |
| XB (producte) | $p(X_1,\ldots,X_V) = \prod_i p(X_i \mid \text{pares}(X_i))$ |
| Naïve Bayes | $p(C \mid \mathbf{x}) \propto p(C) \prod_i p(x_i \mid C)$ |
| Laplace | $\hat{p}(X=x) = \frac{c_x + 1}{N + |d(X)|}$ |

### 3.2 Inferència per enumeració — Pas a pas

**Consulta**: $P(Y \mid E_1 = e_1, \ldots, E_j = e_j)$

1. **Identifica** variables: consulta ($Y$), observades ($E$), ocultes ($H$).
2. **Filtra** les files de la taula conjunta consistents amb l'evidència.
3. **Marginalitza** (suma) sobre les variables ocultes.
4. **Normalitza** la taula resultant.

### 3.3 Eliminació de variables — Pas a pas

**Més eficient** que l'enumeració: no cal construir la taula conjunta sencera.

**Consulta**: $P(L)$ amb XB: $R \to T \to L$

1. **Reducció**: elimina files inconsistents amb l'evidència (si n'hi ha).
2. **Eliminar $R$**: producte de les taules que contenen $R$ → $p(R) \times p(T|R) = p(R,T)$ → marginalitzar $R$ → $p(T)$.
3. **Eliminar $T$**: producte de $p(T) \times p(L|T) = p(T,L)$ → marginalitzar $T$ → $p(L)$.
4. **Normalitzar** si cal.

> **Important**: l'ordre d'eliminació afecta la mida de les taules intermèdies. Sempre eliminar primer les variables amb menys connexions.

### 3.4 Mostreig — Tècniques

#### Rejection Sampling

```
Per cada mostra s (de S totals):
  Genera mostra amb forward sampling (ordre ancestral)
  Si mostra és consistent amb evidència E=e:
    comptes[valor_X] += 1
Retorna normalitza(comptes)
```
**Problema**: si $p(E=e)$ és petit → cal generar $S = S'/p(E=e)$ mostres.

#### Likelihood Weighting

```
Per cada mostra s:
  w = 1.0
  Per cada variable Xi (ordre ancestral):
    Si Xi és observada (Xi = ei):
      valor[Xi] = ei
      w *= p(ei | pares(Xi))
    Sinó:
      valor[Xi] = mostra de p(Xi | pares(Xi))
  pes_acum[valor_X] += w
Retorna normalitza(pes_acum)
```

**Exemple guiat:** XB: Nuvós → {Rec, Pluja} → Moll.  
Evidència: $R = +r$. Consulta: $P(N \mid R = +r)$.

| Mostra | N (mostr.) | R (fixat) | P (mostr.) | M (mostr.) | w |
|---|---|---|---|---|---|
| 1 | +n | +r | +p | +m | $p(+r \mid +n) = 0.1$ |
| 2 | -n | +r | -p | -m | $p(+r \mid -n) = 0.5$ |
| 3 | +n | +r | +p | +m | $0.1$ |

Pes acumulat de $+n$: $0.1 + 0.1 = 0.2$; de $-n$: $0.5$. Normalitzant: $P(+n \mid +r) \approx 0.29$.

### 3.5 Naïve Bayes — Classificació pas a pas

**Consulta**: Donat un cas $\mathbf{x} = (x_1, \ldots, x_d)$, quina classe $C$ és més probable?

1. Per cada valor $c$ de $C$, calcula:
$$\text{score}(c) = p(C = c) \cdot \prod_{i=1}^{d} p(x_i \mid C = c)$$
2. La classe predita és $\hat{c} = \arg\max_c \text{score}(c)$.
3. Si cal la probabilitat: normalitza dividint per $\sum_c \text{score}(c)$.

**Exemple: detecció de spam**
```
Variables: X1=remitent, X2=majúscules, X3=€/$, X4="free"
Cas nou: desconegut, SÍ majúscules, SÍ $, NO "free"

score(spam) = p(spam) · p(desc|spam) · p(CAP|spam) · p($|spam) · p(no_free|spam)
            = 3/8 · 1 · 1/3 · 1/3 · 1/3 = 0.0139

score(no)   = p(no) · p(desc|no) · p(CAP|no) · p($|no) · p(no_free|no)
            = 5/8 · 3/5 · 0 · 1/5 · 4/5 = 0  ← Problema! Zero per CAP|no!
```

> **Solució**: aplicar **suavitzat de Laplace** per evitar zeros:
> $\hat{p}(X=x \mid C=c) = \frac{c_{x,c} + 1}{c_c + |d(X)|}$

### 3.6 Estimació de paràmetres

**Sense suavitzat:**
$$\hat{p}(X = x) = \frac{c_x}{N} \qquad \hat{p}(T = t \mid R = r) = \frac{c_{t,r}}{c_r}$$

**Amb suavitzat de Laplace:**
$$\hat{p}(X = x) = \frac{c_x + 1}{N + |d(X)|} \qquad \hat{p}(T = t \mid R = r) = \frac{c_{t,r} + 1}{c_r + |d(T)|}$$

---

## TÈCNIQUES PER A EXERCICIS

### Exercici tipus: A* sobre un graf
1. Dibuixa taula amb columnes: **Iteració**, **FRONTERA** (node: $f = g+h$), **TANCATS**.
2. Sempre expandeix el node amb menor $f$.
3. Quan un node ja és a TANCATS, ignora'l.
4. Si un node ja és a FRONTERA amb cost pitjor, actualitza'l.
5. Quan expandeixes un objectiu, para.

### Exercici tipus: Minimax amb alfa-beta
1. Dibuixa l'arbre. Etiqueta nivells MAX/MIN alternant.
2. Processa en **DFS** (esquerra a dreta).
3. Manté $\alpha$, $\beta$ globals propagats des de l'arrel.
4. **Tall alfa**: en node MIN, si trobes un valor $\leq \alpha$ del pare MAX → poda germani dreta.
5. **Tall beta**: en node MAX, si trobes un valor $\geq \beta$ del pare MIN → poda germans dreta.
6. Marca clarament quins nodes es **podem** i els que **s'exploren**.

### Exercici tipus: Aplicar accions STRIPS
1. Escriu l'estat actual $s$ com a conjunt d'àtoms.
2. Verifica $Pre \subseteq s$.
3. Calcula $s' = (s \cup eff^+) \setminus eff^-$.
4. Repeteix per cada acció del pla.

### Exercici tipus: Regressió
1. Parteix de $G$.
2. Tria una acció rellevant (que tingui algun àtom de $G$ a $eff^+$).
3. Verifica que cap àtom de $G$ estigui a $eff^-$.
4. $G' = (G \setminus eff^+) \cup Pre$.
5. Repeteix fins que $G' \subseteq s_0$.

### Exercici tipus: Eliminació de variables
1. Identifica variables consulta, observades i ocultes.
2. Redueix variables observades (fixa el seu valor a les taules).
3. Per cada oculta (una a una):
   a. Identifica les taules que la contenen.
   b. Fes el producte d'aquestes taules.
   c. Marginalitza (suma) sobre la variable oculta.
4. Producte de les taules restants.
5. Normalitza.

### Exercici tipus: Likelihood Weighting
1. Fixa l'evidència.
2. Per cada variable en ordre ancestral:
   - Si observada: fixa valor, multiplica $w$ per la probabilitat condicionada.
   - Si no: mostra normalment (usa el nombre aleatori $u$).
3. Acumula $w$ per a cada valor de la variable consulta.
4. Normalitza els pesos acumulats.

### Exercici tipus: Naïve Bayes
1. Per cada classe $c$: multiplica $p(c)$ per tots els $p(x_i | c)$.
2. Compara els resultats (o normalitza si cal probabilitats).
3. Recorda aplicar Laplace si hi ha zeros a les taules.
