# Resum Tema 1 — Cerca
**Universitat de Girona · Curs 2025/26**  
**Prof. Jerónimo Hernández González**

---

## T1.1 · Problemes de cerca

### Concepte
- La cerca en IA estudia com un **agent** troba una seqüència d'accions per assolir un objectiu.
- Cap acció aïllada és suficient; cal planificar una **seqüència**.

### Agent solucionador de problemes
- Construeix plans per resoldre problemes.
- Fa servir una **representació atòmica** (simplificació del món real).
- Exemples: trobar ruta de Romania a Bucarest, mesurar 4 litres amb gerros de 5 i 3 L, PAC-MAN.

### Formulació d'un problema
| Component | Descripció |
|---|---|
| **Estat inicial** | Punt de partida de l'agent |
| **Conjunt d'accions** | Accions possibles en cada estat |
| **Model de transició** | `estat_f = TRANSICIÓ(estat_i, acció)` |
| **Cost** | `COST(estat_i, acció, estat_f)` (s'acumula) |
| **Test d'objectiu** | Comprova si l'estat actual és l'objectiu |

### Espai d'estats (graf)
- **Node** = estat (cada estat apareix una sola vegada).
- **Arc** = acció que connecta un estat amb el seu successor.
- No es construeix en memòria; és un concepte auxiliar.
- Cal evitar: factor de ramificació elevat, cicles, ambigüitats.

### Arbre de cerca
- Es construeix a mesura que s'explora la solució.
- Un **node** correspon a un estat dins d'un pla; un **estat** pot aparèixer a molts nodes.
- La **frontera** és el conjunt de nodes arribar però no explorats.

#### Mètode bàsic
```
funció cerca(problema):
  FRONTERA := [estat_inicial]
  mentre FRONTERA != []:
    e := treure un node de FRONTERA
    si comprova_objectiu(e): retorna e
    FRONTERA := FRONTERA + successors(e)
  retorna Null
```

#### Mètode estès (evita cicles i redundàncies)
```
funció cerca(problema):
  FRONTERA := [estat_inicial], TANCATS := []
  mentre FRONTERA != []:
    e := treure un node de FRONTERA
    si comprova_objectiu(e): retorna e
    TANCATS := TANCATS + [e]
    FRONTERA := FRONTERA + successors(e) \ TANCATS \ FRONTERA
  retorna Null
```

### Cerca informada vs. no informada
- **No informada (a cegues)**: només sap si s'ha arribat a l'objectiu o no.
- **Informada (heurística)**: sap, addicionalment, quin estat s'apropa més a l'objectiu.

> La cerca opera sempre sobre **models** del món. La qualitat de la cerca depèn de la qualitat del model.

---

## T1.3 · Cerca informada

### Motivació
- La cerca **no informada** (cost uniforme) explora entorns concèntrics al voltant de l'estat inicial, sense dirigir-se cap a l'objectiu.
- La cerca **informada** usa una funció heurística per guiar-se.

### Funció heurística
- `h(e)`: estimació del cost per arribar des de `e` fins a un estat objectiu.
- No negativa; `h(e*) = 0` si `e*` és un estat objectiu.
- **Exemples**:
  - Encaminament: distància en línia recta fins a Bucarest.
  - 8-puzle: nombre de fitxes mal col·locades (`h₁`) o suma de distàncies Manhattan (`h₂`).

### Cerca Best-First Search (BFS) voraç
- Expandeix sempre el node amb **menor** `h(e)`.
- `f(e) = h(e)` — no té en compte el cost acumulat.
- Usa una **cua de prioritat** ordenada per `f`.
- **Propietats**:
  - Pot no trobar la solució òptima (no garantit).
  - Complexitat temporal `O(bᵐ)`.
  - Es comporta com una cerca en profunditat mal guiada.

### Algoritme A*
- Combina cost passat i estimació futura:
$$f(e) = g(e) + h(e)$$
  - `g(e)`: cost real del camí fins a `e` (conegut).
  - `h(e)`: estimació heurística del cost fins a l'objectiu.
- Expandeix sempre el node de **menor `f(e)`**.

#### Pseudocodi
```
funció algoritme_a_star(problema, h):
  FRONTERA := [(estat_ini, 0+h(estat_ini))]
  TANCATS := []
  mentre FRONTERA != []:
    e, c := treure primer element de FRONTERA
    si comprova_objectiu(e): retorna e, c
    TANCATS := TANCATS + [e]
    SUCCESSORS := successors(e) \ TANCATS
    per cada e_s, c_s en SUCCESSORS:
      si e_s no en FRONTERA:
        FRONTERA += [(e_s, c + c_s + h(e_s))]
      si (e_s, c') en FRONTERA i c' > c + c_s + h(e_s):
        FRONTERA := FRONTERA - [(e_s,c')] + [(e_s, c+c_s+h(e_s))]
  retorna Null
```

### Propietats de l'heurística per a A*

#### Admissibilitat
$$0 \leq h(e) \leq c^*(e)$$
- L'heurística **mai no sobreestima** el cost real.
- Garanteix que A* trobi la solució òptima.

#### Consistència (desigualtat triangular)
$$h(A) \leq c^*(A, B) + h(B)$$
- El valor de `f` mai no decreix al llarg d'un camí.
- Necessari quan l'espai és un graf (per fer servir `TANCATS`).
- Consistència → admissibilitat.

### Comparació: A* vs. Cost uniforme (8-puzle)

| Mètode | Passos òptims = 4 | Passos òptims = 8 | Passos òptims = 12 |
|---|---|---|---|
| `h(e) = 0` (cost uniforme) | 112 nodes | 6.300 | 3,6 × 10⁶ |
| `h₁` (fitxes mal col·locades) | 13 | 39 | 227 |
| `h₂` (distàncies Manhattan) | 12 | 25 | 73 |

> Com dissenyar heurístiques? **Relaxant el problema** (eliminant restriccions).

---

## T1.4 · Cerca local

### Concepte
- Cada **estat és una solució** (total o parcial) del problema.
- No manté la frontera ni el camí; només guarda l'**estat actual**.
- S'hi mou cap a un **veí** guiat per una funció objectiu.
- Adequada quan: el problema és molt gran, l'espai és continu, o el camí no importa.

### Funció objectiu
$$f: \mathcal{S} \to \mathbb{R}; \quad \text{Objectiu: } \arg\max_{e \in \mathcal{S}} f(e)$$
- Dona **topologia** a l'espai d'estats: màxims globals, màxims locals, plateaus.

### Veïnat d'un estat
- Conjunt d'estats als que es pot arribar aplicant un únic canvi petit.
- La tria del veí és clau; cada estratègia diferent dona un mètode diferent.

### Cerca aleatòria (Random Walk)
- Tria **aleatòriament** un veí.
- Completa (trobarà la solució), però molt ineficient.
- Millora: llençar diverses cerques en paral·lel.

### Cerca Hill-Climbing (voraç)
- Tria el **millor veí** (el que maximitza `f`).
- Acaba quan cap veí millora l'estat actual.
- **Inconvenients**: queda atrapat en **màxims locals** i **plateaus**.
- **Variants**: reinici aleatori, hill-climbing aleatori, primera elecció, moviments laterals.

```
funció cerca_hill_climbing(problema, estat_inicial):
  estat_actual := estat_inicial
  mentre True:
    candidat := agafa_millor(successors(estat_actual))
    si f(candidat) > f(estat_actual):
      estat_actual := candidat
    altrament:
      retorna estat_actual
```

> Exemple 8-reines: amb HC simple, solució vàlida el 14% dels casos. Amb moviments laterals, el 94%.

### Simulated Annealing
- Accepta veïns **pitjors** amb certa probabilitat decreixent.
$$\Delta F = f(\text{candidat}) - f(\text{estat actual}); \quad p = e^{\Delta F / T}$$
- Quan `T` és alt → comportament com random walk.
- Quan `T` és baix → comportament com hill-climbing.
- Amb un bon **scheduler** (programa de refredament), la probabilitat de trobar l'òptim global tendeix a 1.
- No determinista; eficient en memòria.

### Cerca per feixos (Beam Search)
- Guarda **K estats actuals** en lloc d'1.
- En cada iteració: genera tots els successors dels K estats, tria els K millors.
- Problema: poca **diversitat** (els K estats es concentren en una zona).

### Algoritmes genètics
- Manté K estats (individus) i generes nous combinant-los:
  1. **Selecció**: individus amb millor `f` tenen més probabilitat de ser triats.
  2. **Encreuament**: combina dos individus en un punt aleatori.
  3. **Mutació**: amb petita probabilitat, aplica un canvi aleatori.
- Resol el problema de diversitat de la cerca per feixos.
- Representació: cada estat s'ha de codificar (p.ex., com un vector).

### Resum comparatiu

| Mètode | Memòria | Completesa | Optimalitat |
|---|---|---|---|
| Random walk | O(1) | Sí | Sí (eventual) |
| Hill-climbing | O(1) | No | No (subòptim) |
| Simulated annealing | O(1) | Probabilística | Probabilística |
| Beam search | O(K) | No | No |
| Algoritmes genètics | O(K) | No | No |

---

## T1.5 · Cerca amb adversari

### Context
- Entorn **competitiu** amb dos agents amb objectius oposats (jocs).
- Assumpcions: determinista, 2 jugadors alternats, estat observable, suma nul·la.
- Exemples: escacs, dames, go, 3 en ratlla.

### Formalització
| Element | Descripció |
|---|---|
| `E` | Conjunt d'estats (estat inicial `e₀`) |
| `P` | Jugadors `{1, ..., Nⱼ}` |
| `A` | Accions disponibles |
| `T: E × A → E` | Funció de transició |
| `F: E → {True, False}` | Test final (l'estat és terminal?) |
| `U: E × P → ℝ` | Funció d'utilitat (valora l'estat per a cada jugador) |

### Valor Minimax
- **Valor minimax** d'un node: millor valor d'utilitat que es pot aconseguir des d'aquell node assumint que l'oponent juga òptimament.
- Propagació des dels nodes finals cap amunt:
  - Nivells **MAX** (el teu torn): tria el valor **màxim** dels fills.
  - Nivells **MIN** (oponent): tria el valor **mínim** dels fills.

### Algoritme Minimax
```
funció valor-max(problema, estat):
  si estat_final(estat): retorna utilitat(estat)
  v := -Inf
  per cada s en successors(estat):
    v := max(v, valor-min(problema, s))
  retorna v

funció valor-min(problema, estat):
  si estat_final(estat): retorna utilitat(estat)
  v := Inf
  per cada s en successors(estat):
    v := min(v, valor-max(problema, s))
  retorna v
```
- **Propietats**: equivalent a DFS; temps i espai `O(bᵐ)`. Òptim contra un adversari racional.
- **Limitació**: explorar l'arbre complet és inviable per a jocs complexos.

### H-Minimax (amb límit de profunditat)
- S'explora fins a un màxim de `m` nivells.
- Als nodes de tall: si no és estat final, s'estima la utilitat amb una **funció heurística**.
- La heurística es calcula ràpidament i usa coneixement del domini (p.ex., `h(e) = w₁·f₁(e) + ... + wₕ·fₕ(e)`).

### Poda Alfa-Beta
- **Redueix** el nombre de nodes que cal explorar sense canviar el resultat.
- Manté dos valors durant la cerca:
  - **α (alfa)**: valor màxim assegurat per al jugador (MAX).
  - **β (beta)**: valor mínim assegurat per a l'oponent (MIN).
- **Tall alfa**: si en un node MIN, `β ≤ α`, s'atura (el jugador no triarà aquesta branca).
- **Tall beta**: si en un node MAX, `α ≥ β`, s'atura (l'oponent no triarà aquesta branca).

```
funció valor-max(problema, estat, α, β):
  si estat_final(estat): retorna utilitat(estat)
  v := -Inf
  per cada s en successors(estat):
    v := max(v, valor-min(problema, s, α, β))
    α := max(α, v)
    si α ≥ β: break  # Tall beta
  retorna v
```

#### Complexitat amb poda alfa-beta

| Ordenació dels nodes | Temps | Espai |
|---|---|---|
| Pitjor cas (sense poda) | `O(bᵖ)` | `O(bᵖ)` |
| Ordenació òptima | `O(b^(p/2))` | `O(b^(p/2))` |
| Ordre aleatori | `O(b^(3p/4))` | `O(b^(3p/4))` |

> L'ordenació òptima **duplica la profunditat** assolible en el mateix temps.

### Limitacions de Minimax
- Massa **cautelós**: assumeix sempre que l'oponent juga perfectament.
- **Efecte horitzó**: h-minimax no veu estratègies a mitjà/llarg termini.
- **Expectimax**: variant que modela la incertesa de les jugades de l'oponent.

### Evolució històrica
- DeepBlue (1997): vencia a Kasparov amb cerca exhaustiva (~10⁸ nodes/s).
- AlphaZero (Google, 2010s): Minimax + mètodes estocàstics + deep learning + aprenentatge per reforç → rendiment sobrehumà.

---

## Resum global

| Tema | Contingut clau |
|---|---|
| T1.1 | Formulació de problemes, espai d'estats, arbre de cerca, mètode bàsic |
| T1.3 | Heurística, BFS voraç, A* (admissible + consistent) |
| T1.4 | Cerca local: Hill-climbing, Simulated Annealing, Beam Search, Algoritmes genètics |
| T1.5 | Minimax, poda alfa-beta, h-minimax |

> **Referència principal:** Russell & Norvig (2010). *Artificial Intelligence: A Modern Approach* (3a ed.).  
> Material addicional: Dan Klein & Pieter Abbeel (UC Berkeley) — [ai.berkeley.edu](http://ai.berkeley.edu)
