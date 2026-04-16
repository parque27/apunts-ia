# Terminologia — Intel·ligència Artificial
**UdG · Curs 2025/26**

---

## Tema 1 · Cerca

### A* (Algoritme A*)
Algoritme de cerca informada que expandeix el node amb menor $f(e) = g(e) + h(e)$. Combina el cost real acumulat amb l'estimació heurística futura. Complet i òptim si la heurística és admissible/consistent. Usa cua de prioritat (FRONTERA) i conjunt TANCATS.
- **$g(e)$**: cost real del camí des de l'estat inicial fins a $e$.
- **$h(e)$**: estimació heurística del cost des de $e$ fins a l'objectiu.
- **$f(e)$**: funció d'avaluació total; determina la prioritat d'exploració.

### Acció
Operació que l'agent pot executar per transitar d'un estat a un altre. Definida pel model de transició: `estat_f = TRANSICIÓ(estat_i, acció)`. El cost associat s'acumula al llarg del camí.

### Admissibilitat (d'una heurística)
Propietat d'una heurística que **mai sobreestima** el cost real:
$$0 \leq h(e) \leq c^*(e)$$
- **$c^*(e)$**: cost real (desconegut) del camí òptim des de $e$ fins a l'objectiu.
Garanteix que A* trobi la solució òptima.

### Algoritme genètic
Mètode de cerca local que manté $K$ estats (individus) i els combina mitjançant selecció, encreuament i mutació. Inspirat en l'evolució biològica. Resol el problema de diversitat de Beam Search.
- **Selecció**: probabilitat proporcional a la funció objectiu.
- **Encreuament**: combina dues solucions en un punt aleatori.
- **Mutació**: canvi aleatori amb baixa probabilitat.

### Arbre de cerca
Estructura que es construeix progressivament durant l'exploració. L'arrel és l'estat inicial; els fills d'un node són els successors. Un mateix **estat** pot aparèixer en múltiples **nodes**. Pot contenir redundàncies i cicles si no s'usen TANCATS.

### Beam Search (cerca per feixos)
Cerca local que guarda $K$ estats actuals simultàniament. Cada iteració genera tots els successors i en tria els $K$ millors. Problema: pèrdua de diversitat (convergència prematura).

### BFS voraç (Best-First Search voraç)
Cerca informada que expandeix el node amb menor $h(e)$. No considera el cost acumulat ($f(e) = h(e)$). No garanteix optimalitat; es comporta com una cerca en profunditat guiada.

### Cerca informada
Estratègia de cerca que disposa d'informació addicional (heurística) per guiar l'exploració cap a l'objectiu. Exemple: A*, BFS voraç.

### Cerca local
Família de mètodes on cada estat ja és una solució completa. No manté frontera ni camí; només estat actual + funció objectiu. Útil en espais molt grans, continus, o quan el camí no importa.

### Cerca no informada (a cegues)
Estratègia de cerca sense informació addicional sobre la proximitat a l'objectiu. Exemples: cerca en amplada, en profunditat, de cost uniforme.

### Consistència (d'una heurística)
Desigualtat triangular que requereix:
$$h(A) \leq c^*(A, B) + h(B)$$
- **$A$, $B$**: estats consecutius.
- **$c^*(A,B)$**: cost real de l'arc entre $A$ i $B$.
Garanteix que $f$ mai decreixi al llarg d'un camí. Consistència $\Rightarrow$ admissibilitat.

### Cost
Valor numèric associat a executar una acció. S'acumula al llarg de la seqüència (camí). `COST(estat_i, acció, estat_f)`.

### Efecte horitzó
Limitació de h-minimax: al tenir profunditat limitada, no detecta estratègies a mitjà/llarg termini. Un estat "bo" a $m$ nivells pot amagar un desastre al nivell $m+1$.

### Encreuament
Operador dels algoritmes genètics que combina dos individus $A$ i $B$ en un punt aleatori $x$: $C = [a_1,...,a_x, b_{x+1},...,b_L]$.

### Espai d'estats (graf)
Representació conceptual de tots els estats possibles. Nodes = estats; arcs = accions. No es construeix explícitament en memòria (massa gran).

### Estat
Configuració del problema en un instant donat. A la cerca local, cada estat és una solució completa.

### Expectimax
Variant de minimax que modela oponents no perfectes (estocàstics). Als nivells de l'oponent, en comptes de min, calcula el **valor esperat** de les utilitats dels fills.

### FRONTERA
Conjunt de nodes ja generats però encara no explorats. L'estratègia d'extracció defineix el tipus de cerca (cua FIFO = amplada, pila = profunditat, cua de prioritat = cost uniforme/A*).

### Funció heurística $h(e)$
Funció que estima el cost d'arribar des de l'estat $e$ fins a un objectiu. No negativa; $h(e^*)=0$ si $e^*$ és objectiu. Específica del problema.
- Exemples al 8-puzle: $h_1$ = fitxes mal col·locades; $h_2$ = suma de distàncies Manhattan.

### Funció objectiu $f: \mathcal{S} \to \mathbb{R}$
A la cerca local, avalua la qualitat d'un estat. L'objectiu és trobar $\arg\max_{e \in \mathcal{S}} f(e)$. Dona topologia a l'espai (màxims locals, globals, plateaus).

### Hill-climbing (cerca local voraç)
Tria el millor veí com a següent estat. S'atura si cap veí millora l'actual. Ràpid però queda atrapat en màxims locals i plateaus.

### Minimax
Algoritme per a jocs de dos jugadors amb suma nul·la. Explora l'arbre en profunditat alternant MAX (tu) i MIN (oponent). Assumeix adversari racional. Temps i espai $O(b^m)$.
- **Valor minimax**: $U(e) = \max_{e' \in S(e)} U(e')$ als nivells MAX; $U(e) = \min_{e' \in S(e)} U(e')$ als nivells MIN.

### Poda alfa-beta
Optimització de minimax que evita explorar branques irrellevants.
- **$\alpha$**: valor mínim assegurat pel jugador MAX.
- **$\beta$**: valor màxim assegurat per l'oponent MIN.
- **Tall alfa**: $\beta \leq \alpha$ en un node MIN → s'atura.
- **Tall beta**: $\alpha \geq \beta$ en un node MAX → s'atura.
Complexitat amb ordenació òptima: $O(b^{p/2})$ (duplica la profunditat explorable).

### Scheduler (programa de refredament)
En simulated annealing, funció $T(k)$ que controla com decreix la temperatura al llarg de les iteracions. Quan $T$ alt → exploració; quan $T$ baix → explotació.

### Simulated Annealing
Cerca local que accepta veïns pitjors amb probabilitat $p = e^{\Delta F / T}$.
- **$\Delta F = f(\text{candidat}) - f(\text{actual})$**: diferència de qualitat.
- **$T$**: temperatura (paràmetre decreixent).
Si $\Delta F \geq 0$: sempre s'accepta. Si $\Delta F < 0$: menys probabilitat com pitjor sigui el candidat o menor sigui $T$.

### TANCATS
Conjunt d'estats ja expandits (explorats). S'usa per evitar cicles i camins redundants al graf.

### Utilitat $U: E \times P \to \mathbb{R}$
Funció que assigna un valor numèric a cada estat terminal per a cada jugador. A jocs de suma nul·la: tu la vols maximitzar, l'oponent minimitzar-la.

### Veïnat
Conjunt d'estats accessibles des de l'estat actual amb un únic canvi elemental.

---

## Tema 2 · Planificació (Planning)

### Acció (STRIPS)
Tupla $a = \langle Pre, \langle eff^+, eff^-\rangle\rangle$ que modifica l'estat. Aplicable si $Pre \subseteq s$. Resultat: $a(s) = (s \cup eff^+) \setminus eff^-$.
- **$Pre$**: precondicions (àtoms que han de ser certs).
- **$eff^+$**: efectes d'addició (àtoms que es fan certs).
- **$eff^-$**: efectes d'eliminació (àtoms que es fan falsos).
$eff^+$ i $eff^-$ són disjunts.

### ADL (Action Description Language)
Extensió de STRIPS que afegeix: tipus jeràrquics, negació, disjuncions, efectes condicionals (`when`) i quantificadors ($\forall$, $\exists$). $\text{STRIPS} \subset \text{ADL} \subset \text{PDDL}$.

### Assumpció de món tancat
En un estat, els àtoms ground que **no** hi apareixen es consideren **falsos**. No s'aplica a fluents numèrics.

### Àtom
Instanciació d'un predicat amb objectes. Exemple: `on(d1, d2)`. Un àtom **ground** no conté variables.

### Cerca explícita
Mètode de resolució de planning que explora estats individualment. Opcions de direcció: progressió (endavant), regressió (enrere), bidireccional.

### Domini (PDDL)
Fitxer que defineix els predicats i els esquemes d'acció d'un problema de planificació. Separat del fitxer de problema.

### Esquema d'acció
Representació "lifted" (abstracta) d'una acció usant variables en comptes d'objectes concrets. La instanciació ground genera totes les accions concretes.
- Exemple a Hanoi: `move(x, y, z)` amb `Pre: {on(x,y), clear(x), smaller(x,z), clear(z)}`.

### Estat (Planning)
Conjunt d'àtoms ground certs en un moment donat. L'estat inicial ($s_0$) i l'objectiu ($G$) defineixen el problema.

### $h_{add}$ (heurística additiva)
$$h_{add}(s) = \sum_{g \in G} \text{cost mínim de satisfer } g \text{ per separat}$$
No admissible (sobreestima quan una acció beneficia múltiples objectius). Molt informativa per trobar un pla ràpidament sense garantia d'optimalitat.

### $h_{max}$ (heurística del subobjectiu més costós)
$$h_{max}(s) = \max_{g \in G} \text{cost mínim de satisfer } g$$
Admissible i consistent. Calculable en temps polinòmic. Poc informativa amb múltiples objectius.

### $h^+$ (heurística de borrat relaxat)
Relaxació que elimina les delete lists (efectes negatius) de les accions. Admissible i consistent si es calcula òptimament, però trobar-la és NP-hard. S'aproxima amb hill-climbing.
$$h_{max} \leq h^+ \leq h^* \leq h_{add}$$

### Heurística de relaxació
S'obté resolent una versió simplificada del problema. Dues maneres de relaxar: afegir arestes (camins extres) o agrupar nodes. El cost exacte de la solució relaxada és admissible.

### Numeric fluent (variable d'estat numèrica)
Variable numèrica $\in \mathbb{Q}$ en PDDL 2.1. Exemple: `(current_fuel ?sh)`. Poden ser modificades amb `increase`, `decrease`, `assign`.

### Pla
Seqüència d'accions $[a_1, \ldots, a_n]$ tal que $G \subseteq a_n(\ldots(a_1(s_0))\ldots)$. La **qualitat** es pot mesurar amb una mètrica numèrica (p.ex., minimitzar combustible).

### PDDL (Planning Domain Definition Language)
Llenguatge estàndard per especificar problemes de planificació. Dos fitxers: **domini** (predicats + accions) i **problema** (objectes + $s_0$ + $G$). Evoluciona amb les International Planning Competitions.

### Planificació clàssica
Formulació que assumeix: món completament observat, accions deterministes i instantànies, sense events exògens, fets finits.

### Predicat
Relació amb nom entre objectes. Exemples: `on(?x, ?y)`, `clear(?x)`, `adjacent(?l1, ?l2)`. Pot ser **estàtic** (no canvia) o **dinàmic** (canvia per efectes d'accions).

### Problema de planificació STRIPS
$$\Pi = (Atoms, s_0, G, A)$$
- **$Atoms$**: conjunt d'àtoms possibles.
- **$s_0$**: estat inicial.
- **$G \subseteq Atoms$**: objectiu.
- **$A$**: conjunt finit d'accions.
Complexitat: NP-hard i PSPACE-complet.

### Progressió vs. Regressió
- **Progressió (endavant)**: des de $s_0$, aplica accions cap a $G$.
- **Regressió (enrere)**: des de $G$, inverteix accions fins trobar $s_0$.
  - $\text{sregr}(G', a) = (G' \setminus eff^+) \cup Pre$ si $\nexists\, at_i \in eff^-$ .

### STRIPS
Stanford Research Institute Problem Solver (1971). Formalisme fundacional de la planificació: defineix problemes com $\Pi = (Atoms, s_0, G, A)$.

---

## Tema 3 · Raonament probabilístic i Xarxes Bayesianes

### Classificació
Tasca d'aprenentatge automàtic que assigna una **classe** (etiqueta) a un cas descrit per variables descriptores. Exemples: spam/no, dígits manuscrits, diagnòstic mèdic.

### Distribució conjunta $p(X, Y, Z)$
Taula que assigna probabilitats a **totes** les combinacions de valors. Creix exponencialment: $O(d^n)$ on $d$ = mida del domini, $n$ = nombre de variables.

### Distribució condicional $p(X | Y)$
Probabilitat de $X$ donat que $Y$ pren un valor concret:
$$p(X | Y) = \frac{p(X, Y)}{p(Y)}$$
- **$p(X, Y)$**: distribució conjunta.
- **$p(Y)$**: distribució marginal de $Y$ (normalitzador).

### Distribució marginal $p(X)$
S'obté sumant (marginalitzant) la conjunta sobre totes les altres variables:
$$p(Y = y_j) = \sum_{x_i \in d(X)} p(Y = y_j, X = x_i)$$

### Eliminació de variables
Algorisme d'inferència exacta en XBs que intercala producte i marginalització:
1. Reduir variables observades.
2. Per cada variable oculta $H$: producte de taules que la contenen → marginalitzar $H$.
3. Producte de taules restants → normalitzar.
L'ordre d'eliminació afecta el cost (pitjor cas: exponencial).

### Forward Sampling
Mostreig d'una XB: per cada variable $X_i$ en ordre ancestral, mostreja $x_i \sim p(X_i | \text{pares}(X_i))$. Cost lineal en $V$ variables.

### Graf dirigit acíclic (DAG)
Representació gràfica d'una XB. Nodes = variables; arcs = relacions de dependència condicional. Pares d'un node = variables que el condicionen.

### Independència condicional
$X$ i $Y$ són condicionalment independents donada $Z$ si:
$$p(X, Y | Z) = p(X | Z) \cdot p(Y | Z)$$
Equivalent: $p(X | Y, Z) = p(X | Z)$. Fonamental per simplificar XBs.

### Independència probabilística
Dues variables són independents si:
$$p(X, Y) = p(X) \cdot p(Y)$$
Saber el valor d'una no dona informació sobre l'altra.

### Inferència probabilística
Càlcul de $P(Y | E_1 = e_1, \ldots, E_j = e_j)$ a partir del model. Mètodes: enumeració (exponencial), eliminació de variables, mostreig (aproximat).

### Likelihood Weighting (mostreig amb pesos)
Mostreig d'una XB fixant l'evidència en lloc de rebutjar mostres. Cada mostra té un pes $w$:
$$w = \prod_{i: E_i \text{ obs.}} p(e_i | \text{pares}(E_i))$$
Resposta: $p(X = x | E = e) = \frac{\sum_{s: s_X = x} w_s}{\sum_s w_s}$
No rebutja cap mostra → eficient per a evidències poc probables.

### Marginalització
Procés d'obtenir $p(X)$ a partir de $p(X, Y)$ sumant sobre $Y$. Base de la llei de probabilitats totals.

### Màxima versemblança (MLE)
Estimador que escull els paràmetres que maximitzen la probabilitat de les dades observades:
$$\hat{\theta} = \arg\max_\theta \sum_{x \in D} \log p_\theta(x) \quad \Rightarrow \quad \hat{p}(X = x) = \frac{c_x}{N}$$
- **$D$**: conjunt de dades observades.
- **$c_x$**: nombre de mostres amb valor $x$.
- **$N$**: total de mostres.

### Naïve Bayes
XB amb estructura fixada per a classificació: la variable classe $C$ és pare de totes les descriptores $X_1, \ldots, X_d$, sense arcs entre descriptores.
$$p(C | x_1, \ldots, x_d) \propto p(C) \cdot \prod_{i=1}^{d} p(x_i | C)$$
Assumpció forta: $X_i \perp X_j | C$ per a tots $i, j$. Nombre de paràmetres lineal en $d$.

### Ordre ancestral
Ordenació de les variables d'una XB on, en arribar a $X_i$, tots els seus pares ja han estat processats. Necessari per forward sampling i per la regla del producte.

### Regla de Bayes
$$p(X | Y) = \frac{p(Y | X) \cdot p(X)}{p(Y)} = \frac{p(Y | X) \cdot p(X)}{\sum_{x_i} p(Y | X = x_i) \cdot p(X = x_i)}$$
- **$p(X | Y)$**: posterior (el que volem).
- **$p(Y | X)$**: versemblança (likelihood).
- **$p(X)$**: prior (coneixement previ).
- **$p(Y)$**: evidència (normalitzador).

### Regla de la cadena
$$p(X_1, \ldots, X_V) = \prod_{i=1}^{V-1} p(X_i | X_{i+1}, \ldots, X_V) \cdot p(X_V)$$
A XBs, simplificada amb independències condicionals:
$$p(X_1, \ldots, X_V) = \prod_{i=1}^{V} p(X_i | \text{pares}(X_i))$$

### Rejection Sampling (mostreig amb rebuig)
Genera mostres amb forward sampling; descarta les que no coincideixen amb l'evidència. Ineficient si $p(E = e)$ és petit (moltes mostres rebutjades).

### Suavitzat de Laplace
Afegeix una mostra virtual per cada valor per evitar probabilitats zero:
$$\hat{p}(X = x) = \frac{c_x + 1}{N + |d(X)|} \qquad \hat{p}(T = t | R = r) = \frac{c_{t,r} + 1}{c_r + |d(T)|}$$
- **$|d(X)|$**: nombre de valors possibles de $X$.

### Variable aleatòria
Representa un aspecte del món amb incertesa. V.a. discreta: domini finit $d(X) = \{x_1, \ldots, x_D\}$. La seva distribució de probabilitat és una taula que suma 1.

### Xarxa Bayesiana (XB)
Model probabilístic representat com un DAG que explota independències condicionals. El producte de les distribucions locals (una per node) reconstrueix la distribució conjunta.
$$p(X_1, \ldots, X_V) = \prod_{i=1}^{V} p(X_i | \text{pares}(X_i))$$
Avantatge: complexitat espacial molt menor que la taula conjunta completa.
