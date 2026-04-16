# Resum — Planificació amb IA
**Universitat de Girona · Curs 2025/26**  
**Prof. Joan Espasa Arxer & Mateu Villaret Ausellé**  
*(University of St Andrews & Universitat de Girona)*

---

## Part I · Modelització (Planning_Slides_1)

### Què és planificar?
> Utilitzant el coneixement sobre l'estat del món i les accions disponibles, determinar **a priori** una seqüència d'accions apropiades que transformin un estat inicial en un estat objectiu.

- Neix el 1958 amb el **General Problem Solver** (Simon, Shaw, Newell).
- El robot **Shakey** (1969) va marcar l'inici de la planificació automàtica aplicada i va impulsar el desenvolupament d'A* i del llenguatge **STRIPS**.

### Aplicacions reals
- Rovers de la NASA a Mart.
- Gestió logística d'hivernacles.
- Control del trànsit urbà.
- Planificació de medicació per a pacients.
- Gestió de magatzems d'Amazon.
- Resposta automatitzada a ciberatacs.

### Planificació Clàssica
Assumpcions que simplifiquen el problema general:
- Món **completament observat**.
- Canvis produïts **únicament per accions** (no per esdeveniments externs).
- Accions **deterministes**.
- Accions **instantànies**.
- Nombre **finit** de fets.

---

### Representació d'estat (STRIPS)

#### Definicions bàsiques
- **Objectes** (`O`): elements del domini (p.ex., discos, pals).
- **Predicats** (`P`): relacions entre objectes (p.ex., `on`, `clear`, `smaller`).
- **Àtom**: instanciació concreta d'un predicat amb objectes, p.ex., `on(d1, d2)`.
- **Àtom ground**: àtom sense variables.
- **Estat**: conjunt d'àtoms ground que són **certs** (assumpció de món tancat — el que no apareix és fals).
- **Predicats estàtics**: no canvien per l'efecte d'accions (p.ex., `smaller`).
- **Predicats dinàmics**: poden canviar (p.ex., `on`, `clear`).

#### Exemple: Torres de Hanoi (3 discos)
```
O = {p1, p2, p3, d1, d2, d3}
P = {smaller(·,·), clear(·), on(·,·)}

s₀ = { clear(d1), clear(p2), clear(p3),
       on(d1,d2), on(d2,d3), on(d3,p1),
       smaller(d1,d2), smaller(d1,d3), ... }

G  = { on(d1,d2), on(d2,d3), on(d3,p3) }
```

---

### Accions

#### Estructura d'una acció
- **Precondicions** (`Pre`): conjunt d'àtoms ground que han de ser certs per aplicar l'acció.
- **Efectes d'addició** (`eff⁺`): àtoms que es fan certs en aplicar l'acció.
- **Efectes d'eliminació** (`eff⁻`): àtoms que es fan falsos.

> `eff⁺` i `eff⁻` han de ser **disjunts**.

#### Aplicabilitat i resultat
- Acció `a = ⟨Pre, ⟨eff⁺, eff⁻⟩⟩` **és aplicable** en `s` si `Pre ⊆ s`.
- Resultat: `a(s) = (s ∪ eff⁺) \ eff⁻`.

---

### Problema de planificació STRIPS
$$\Pi = (Atoms, s_0, G, A)$$

| Element | Descripció |
|---|---|
| `Atoms` | Conjunt d'àtoms possibles |
| `s₀` | Estat inicial |
| `G ⊆ Atoms` | Objectiu (conjunt d'àtoms a satisfer) |
| `A` | Conjunt finit d'accions |

**Solució (pla)**: seqüència `[a₁, ..., aₙ]` tal que `G ⊆ aₙ(...(a₁(s₀))...)`.

> STRIPS és NP-hard i PSPACE-complet. Molts algorismes de la literatura es presenten per a tasques STRIPS.

#### Cerca genèrica per resoldre STRIPS
```
Inicialitza open amb l'estat inicial
mentre open no és buida:
  Tria el millor node de open
  si node conté el goal: retorna solució
  Genera tots els estats veïns (un pas)
  Afegeix els veïns a open
retorna error
```

---

### Esquemes d'acció
- Representen de forma **abstracta** (lifted) totes les accions disponibles, usant **variables** en lloc d'objectes concrets.
- Consten de: nom, paràmetres (variables), precondició, efectes d'addició i eliminació.
- La **instanciació ground** de l'esquema genera totes les accions concretes.

**Exemple: esquema `move` a Hanoi**
```
move(x, y, z) =
  Pre: {on(x,y), clear(x), smaller(x,z), clear(z)}
  eff⁺: {on(x,z), clear(y)}
  eff⁻: {clear(z), on(x,y)}
```

---

### Més enllà de STRIPS: ADL i PDDL

#### ADL (Action Description Language)
Afegeix a STRIPS:
- Estructuració jeràrquica de **tipus** d'objectes.
- **Negació** directa en precondicions i objectiu.
- Expressions **disjuntives** en precondicions i objectiu.
- **Efectes condicionals** (`when`).
- **Quantificadors** (`∀`, `∃`).

#### PDDL (Planning Domain Definition Language)
- Llenguatge estàndard per especificar problemes de planificació de forma **declarativa**.
- `STRIPS ⊂ ADL ⊂ PDDL`.
- S'especifica en **dos fitxers separats**:
  - **Domini**: predicats + esquemes d'acció.
  - **Problema**: objectes + estat inicial + objectiu.

**Exemple domini Hanoi en PDDL (STRIPS):**
```lisp
(define (domain hanoiv1)
  (:requirements :strips)
  (:predicates (clear ?x) (on ?x ?y) (smaller ?x ?y))
  (:action move
    :parameters (?disc ?from ?to)
    :precondition (and (smaller ?disc ?to) (on ?disc ?from)
                       (clear ?disc) (clear ?to))
    :effect (and (clear ?from) (on ?disc ?to)
                 (not (on ?disc ?from)) (not (clear ?to)))))
```

---

### Planning Numèric (PDDL 2.1)
- Afegeix **variables d'estat numèriques** (*numeric fluents*) ∈ ℚ.
- Precondicions i objectius poden incloure expressions aritmètiques: `(>= (current_fuel ?sh) (consumption_empty ?from ?to))`.
- Efectes poden modificar fluents: `(decrease (current_fuel ?sh) ...)`, `(assign ...)`.
- Permet **minimitzar o maximitzar** una expressió com a mètrica de qualitat del pla.
- **Inconvenient**: l'espai d'estats es torna **infinit** (semidecidibilitat).

---

## Part II · Resolució amb cerca (Planning_Slides_2)

### La planificació com a cerca
Tres mètodes principals per resoldre classical planning:

| Mètode | Idea | Decisions clau |
|---|---|---|
| **Cerca explícita** | Representa i explora estats individualment | Direcció, algorisme, heurístiques |
| **Satisfactibilitat (SAT/CP/SMT)** | Redueix el problema a queries lògiques | Codificació, solucionador, estratègia |
| **Cerca simbòlica** | Manipula conjunts d'estats compactament (BDDs) | Estructura de dades + cerca explícita |

> La cerca explícita és el mètode més extensament estudiat i usat.

---

### Cerca explícita: opcions de disseny

#### Direcció de la cerca
- **Progressió (endavant)**: de l'estat inicial cap a l'objectiu.
- **Regressió (enrere)**: dels estats objectiu cap a l'estat inicial.
- **Bidireccional**: les dues alhora.

#### Algorisme de cerca
- **No informada**: profunditat, amplada...
- **Heurística sistemàtica**: greedy best-first, A*, IDA*...
- **Heurística local**: hill-climbing, cerca per feixos...

#### Control de la cerca
- Heurístiques per a cerca informada.
- Tècniques de poda: invariants, eliminació de simetries, reducció d'ordre parcial.

---

### Cerca per regressió (enrere)

#### Concepte
- Parteix del conjunt d'estats objectiu `G` i aplica iterativament **regressió** d'accions per generar subobjectius predecessors.
- Troba una solució quan un subobjectiu generat conté l'estat inicial `s₀`.

#### Regressió STRIPS
Donada una acció `a = ⟨Pre, ⟨eff⁺, eff⁻⟩⟩` i un subobjectiu `G'`:
$$\text{sregr}(G', a) = \begin{cases} \bot & \text{si } \exists \, at_i \in eff^- \\ (G' \setminus eff^+) \cup Pre & \text{altrament} \end{cases}$$

- Només es consideren **accions rellevants**: les que afegeixen com a mínim un àtom de `G'`.

**Pros**: gestiona molts estats simultàniament.  
**Contres**: les operacions bàsiques (p.ex., detecció de duplicats) poden ser NP-completes.

---

### Cerca cap endavant informada amb A*

L'aproximació **més exitosa** per a classical planning és la cerca endavant amb A*.

#### Interfície abstracta necessària
```
init()      → retorna l'estat inicial
is_goal(s)  → comprova si s és un estat objectiu
succ(s)     → retorna tots els estats successors de s
cost(a)     → retorna el cost de l'acció a
h(s)        → retorna el valor heurístic per a l'estat s
```

#### A* per a planning
- Expandeix l'estat amb menor `g(s) + h(s)`.
- `g(s)`: cost real des de l'estat inicial fins a `s`.
- Cal una taula de cerca per **evitar repetir estats**.

---

### Heurístiques per a planning

#### Propietats de les heurístiques
| Propietat | Definició |
|---|---|
| **Segura** | `h(s) = ∞ ⟹ h*(s) = ∞` |
| **Conscient de l'objectiu** | `h(s) = 0` per a tots els estats objectiu |
| **Admissible** | `h(s) ≤ h*(s)` per a tots els estats |
| **Consistent** | `h(s) ≤ cost(a) + h(s')` per a totes les transicions `s → s'` |

> Amb heurístiques admissibles i consistents, A* garanteix **completesa** i **optimalitat** (sense revisitar nodes).

---

### Heurístiques de relaxació
- S'obté una versió **relaxada** del problema original (`Π'`) i es resol.
- El cost de la solució de `Π'` serveix com a heurística per a `Π`.
- El cost exacte d'una solució relaxada és una heurística **admissible**.

#### Heurística d'ignorar precondicions
- Elimina les precondicions de les accions → qualsevol acció és aplicable en qualsevol estat.
- Trobar el conjunt mínim d'accions que satisfaci l'objectiu és NP-hard (set-cover).
- Un algorisme voraç pot aproximar-ho sacrificant admissibilitat.

**Exemple: 8-puzle**
- Eliminant `(free ?s2)` i `(adjacent ?s1 ?s2)` → `hmisplaced`: nombre de peces mal col·locades.
- Eliminant `(free ?s2)` → `hmanhattan`: suma de distàncies Manhattan.

#### Heurística h⁺ (ignorar llistes de borrat)
- Elimina els efectes negatius (delete lists) de les accions.
- Les propietats d'estat creixen **monòtonament** → no hi ha backtracking.
- Admissible i consistent quan es calcula de forma òptima.
- Trobar h⁺ òptimament és NP-hard; s'aproxima amb hill-climbing en temps polinòmic.

#### Heurística hmax
$$h_{\max}(s) = \text{cost màxim per satisfer el subobjectiu més costós de } G$$
- Admissible i consistent; calculable en temps polinòmic.
- Pot ser poc informativa quan hi ha molts objectius interaccionant.

#### Heurística hadd
$$h_{\text{add}}(s) = \sum_{g \in G} \text{cost mínim de satisfer } g \text{ per separat}$$
- No admissible (sobreestima quan una acció beneficia múltiples objectius).
- Molt informativa per trobar qualsevol pla (sense garantia d'optimalitat).

#### Comparació d'heurístiques (8-puzle)
$$h_{\max} \leq h^+ \leq h^* \leq h_{\text{add}}$$

| Heurística | Admissible | Consistent | Informatività |
|---|---|---|---|
| `hmax` | Sí | Sí | Baixa |
| `h⁺` | Sí | Sí | Alta |
| `hadd` | No | No | Molt alta |
| `hFF` | No | No | Alta (combinació) |

---

### Exemple: Blocks World en PDDL
```lisp
(define (domain blocksworld)
  (:requirements :strips :typing :equality)
  (:types block)
  (:predicates (on ?x ?y - block) (ontable ?x - block)
               (clear ?x - block) (handempty) (holding ?x - block))
  
  (:action pick-up
    :parameters (?x - block)
    :precondition (and (clear ?x) (ontable ?x) (handempty))
    :effect (and (not (ontable ?x)) (not (clear ?x))
                 (not (handempty)) (holding ?x)))
  
  (:action stack
    :parameters (?x ?y - block)
    :precondition (and (holding ?x) (clear ?y) (not (= ?x ?y)))
    :effect (and (not (holding ?x)) (not (clear ?y))
                 (clear ?x) (handempty) (on ?x ?y))))
```

---

## Resum global

| Part | Contingut clau |
|---|---|
| Part I | Planificació clàssica, STRIPS, accions, esquemes, PDDL, planning numèric |
| Part II | Cerca explícita/regressió/endavant, A*, heurístiques de relaxació (hmax, h⁺, hadd) |

> **Eines pràctiques**: pyperplan (planner STRIPS simple en Python), ENHSP (planner numèric).  
> **Referència principal:** Russell & Norvig (2016). *Artificial Intelligence: A Modern Approach*. Pearson.  
> Haslum et al. (2019). *An Introduction to the Planning Domain Definition Language*. Morgan & Claypool.
