# Resum Tema 3 — Intel·ligència Artificial
**Universitat de Girona · Curs 2025/26**  
**Prof. Jerónimo Hernández González**

---

## T3.1 · Representació del coneixement i raonament probabilístic

### Incertesa i variables aleatòries
- El món conté **incertesa** per impossibilitat d'observar-ho tot o per entorns estocàstics.
- Una **variable aleatòria (v.a.)** pren valors amb certa probabilitat.
  - V.a. discreta: domini finit `d(X) = {x₁, x₂, ..., xD}`.
  - La **distribució de probabilitat** és una taula: `p(xᵢ) ≥ 0`, `Σ p(xᵢ) = 1`.

### Probabilitat conjunta i marginal
- La **distribució conjunta** `p(X, Y, Z)` descriu la probabilitat de totes les combinacions de valors.
- **Marginalització**: obtenir `p(X)` sumant sobre totes les combinacions de les altres variables (llei de probabilitats totals):
$$p(Y = y_j) = \sum_{x_i \in d(X)} p(Y = y_j, X = x_i)$$

### Independència probabilística
- Dues variables són **independents** si:
$$p(X, Y) = p(X) \cdot p(Y)$$

### Probabilitat condicionada
- La distribució de `X` condicionada a `Y = yⱼ`:
$$p(X | Y) = \frac{p(X, Y)}{p(Y)}$$
- Si `X` i `Y` són independents: `p(X | Y) = p(X)`.

### Regla de la cadena
$$p(X_1, \ldots, X_V) = \prod_{i=1}^{V-1} p(X_i \mid X_{i+1}, \ldots, X_V) \cdot p(X_V)$$

### Inferència probabilística per enumeració
Donada la consulta `P(Y | E₁=e₁, ..., Eⱼ=eⱼ)`:
1. Ens quedem amb les files consistents amb les observacions.
2. Sumem sobre els valors de les variables ocultes.
3. Normalitzem.

> **Limitació:** complexitat espacial i temporal **exponencial** `O(dⁿ)`.

### Regla de Bayes
$$p(X | Y) = \frac{p(Y | X) \cdot p(X)}{p(Y)}$$
- Permet construir una condicional a partir del seu **revers**.
- Aplicació clàssica: detecció de spam.

---

## T3.2 · Representació amb Xarxes Bayesianes (XBs)

### Motivació
- La distribució conjunta creix **exponencialment** amb el nombre de variables.
- Calen simplificacions: explotar relacions d'independència.

### Xarxa Bayesiana
- **Model probabilístic** que explota la independència condicional per simplificar la representació.
- Representada com un **graf dirigit acíclic (DAG)**:
  - Cada node = variable aleatòria.
  - Els pares d'un node = variables que el condicionen.
- La distribució conjunta es factoritza com:
$$p(X_1, \ldots, X_V) = \prod_{i=1}^{V} p(X_i \mid \text{pares}(X_i))$$

### Independència condicional
- `X` i `Y` són **condicionalment independents** donada `Z` si:
$$p(X, Y \mid Z) = p(X \mid Z) \cdot p(Y \mid Z)$$
- Exemple clàssic (jardí moll): pluja (P) i rec obert (R) expliquen el jardí moll (M).  
  `I ⊥ M | P` (impermeable és independent del jardí moll si saps que plou).  
  `P ⊬ R | M` (pluja i rec **no** són independents si saps que el jardí és moll — *explaining away*).

### Ordre ancestral
- Ordenació de les variables tal que, en arribar a `Xᵢ`, ja s'han processat tots els seus pares.

### Inferència: eliminació de variables
- **Millora sobre enumeració**: intercala producte i marginalització.
  1. Reduir variables observades.
  2. Per cada variable oculta `H`: producte de les taules que la contenen, després marginalitzar.
  3. Producte de les taules restants + normalitzar.
- El **cost** depèn de l'ordre d'eliminació (en el pitjor cas, segueix sent exponencial).

### Resum XBs
- Representació **compacta** del coneixement probabilístic.
- El graf facilita la comprensió de les relacions entre variables.
- Associació estadística (no necessàriament causal).
- Poden resultar intractables per a xarxes grans.

---

## T3.3 · Xarxes Bayesianes: Inferència aproximada

### Motivació
- L'eliminació de variables pot ser **exponencial** en el pitjor cas.
- Alternativa: **inferència aproximada per mostreig**.

### Mostreig d'una distribució categòrica
- Generar una mostra:
  1. Obtenir un nombre aleatori `u ∈ [0,1]`.
  2. Seleccionar el valor `c ∈ d(C)` el subinterval del qual conté `u`.

### Forward Sampling (mostreig d'una XB)
- Seguint un **ordre ancestral**, per cada variable `Xᵢ` es mostreja:
$$x_i \sim p(X_i \mid \text{pares}(X_i))$$
- Permet generar mostres de la distribució conjunta eficientment (cost lineal).

### Mostreig amb rebuig (*Rejection Sampling*)
- Per respondre `p(X | E=e)`:
  1. Obtenir `S` mostres amb *forward sampling*.
  2. **Rebutjar** les mostres que no compleixen `E=e`.
  3. Normalitzar els comptes de les mostres restants.
- **Limitació**: si `p(E=e)` és molt petita, es necessiten **moltes mostres** per obtenir-ne suficients de vàlides (molt ineficient).

### Mostreig amb pesos (*Likelihood Weighting*)
- **Idea**: no rebutjar mostres; en canvi, **fixar l'evidència** i ponderar cada mostra.
- Per cada mostra:
  1. Seguir un ordre ancestral.
  2. Per cada variable observada (`Eᵢ = eᵢ`): fixar el valor i **multiplicar el pes** per `p(eᵢ | pares(Eᵢ))`.
  3. Per les variables no observades: mostrar normalment.
- Resposta: pes acumulat per cada valor de `X`, normalitzat:
$$p(X=+x \mid E=e) = \frac{\sum_{s: s_X=+x} w_s}{\sum_s w_s}$$
- **Avantatge**: no es rebutja cap mostra → molt més eficient per a evidències poc probables.

### Comparació
| Mètode | Consultes marginals `p(X)` | Consultes condicionals `p(X|e)` |
|---|---|---|
| Rejection sampling | Ràpid i adequat | Ineficient si `p(e)` és petit |
| Likelihood weighting | Adequat | Eficient (no rebutja mostres) |

---

## T3.4 · Naïve Bayes

### Aprenentatge de Xarxes Bayesianes
- Les XBs es poden **aprendre de dades** (aprenentatge automàtic).
- **Estimació dels paràmetres** (valors de les taules) per **màxima versemblança**:
$$\hat{p}(X = x) = \frac{c_x}{N}$$
  - `cₓ` = nombre de mostres amb valor `x`, `N` = total de mostres.
  - Per distribucions condicionals: es restringeix als casos amb el valor fixat pels pares.
- **Aprendre l'estructura (graf)** és un problema NP-complet → solució habitual: cerca local.

### Suavitzat de Laplace
- Evita probabilitats zero (mai observades ≠ impossibles).
$$\hat{p}(X = x) = \frac{c_x + 1}{N + |d(X)|}$$

### Classificació
- **Classificació**: donat un cas (descrit per variables), predir la seva classe/etiqueta.
- Exemples: detecció de spam, reconeixement de dígits, diagnòstic mèdic.

### Naïve Bayes
- XB amb **estructura fixada** per a problemes de classificació:
  - Una variable **classe** `C` és la pare de totes les variables **descriptores** `X₁, ..., Xd`.
  - No hi ha arcs entre descriptores.
- **Assumpció**: independència condicional entre descriptores donada la classe:
$$X_i \perp X_j \mid C, \quad \forall i, j$$
- Distribució conjunta:
$$p(X_1, \ldots, X_d, C) = p(C) \cdot \prod_{i=1}^{d} p(X_i \mid C)$$

### Classificació amb Naïve Bayes
- Per classificar un nou cas `x = (x₁, ..., xd)`:
$$p(C \mid x_1, \ldots, x_d) \propto p(C) \cdot \prod_{i=1}^{d} p(x_i \mid C)$$
- Si `C` és binària, es comparen els numeradors directament (el denominador és el mateix).
- Si `C` té més de dos valors, es normalitza.

### Propietats de Naïve Bayes
| Característica | Detall |
|---|---|
| Estructura | Fixada (no cal aprendre-la) |
| Paràmetres | Estimats de les dades (màx. versemblança + Laplace) |
| Nombre de paràmetres | **Lineal** en `d` (molt eficient) |
| Assumpció | Independència condicional (forta i sovint irreal) |
| Ús típic | Spam, classificació de text, diagnòstic |

### Exemple: identificació de dígits
- Cada píxel (0/1) és una variable descriptora.
- La classe `C ∈ {0, 1, ..., 9}`.
- Es prediu: `argmax_C p(C) · ∏ᵢ,ⱼ p(lᵢⱼ | C)`.

---

## Resum global

| Tema | Contingut clau |
|---|---|
| T3.1 | Probabilitat bàsica, inferència per enumeració, Regla de Bayes |
| T3.2 | Xarxes Bayesianes: estructura, factorització, eliminació de variables |
| T3.3 | Inferència aproximada: rejection sampling, likelihood weighting |
| T3.4 | Aprenentatge de XBs, Naïve Bayes per classificació |

> **Referència principal:** Russell & Norvig (2010). *Artificial Intelligence: A Modern Approach* (3a ed.).  
> Material addicional: Dan Klein & Pieter Abbeel (UC Berkeley) — [ai.berkeley.edu](http://ai.berkeley.edu)
