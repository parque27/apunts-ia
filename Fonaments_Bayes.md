# Fonaments del Teorema de Bayes
**Intel·ligència Artificial — UdG 2025/26**

---

## 1. Intuïció: per què Bayes?

Imagina que un metge et diu: "El resultat del test ha donat positiu, i el test encerta el 99% de les vegades." Quina és la probabilitat que realment tinguis la malaltia?

La majoria de gent diria 99%. Però la resposta correcta depèn de **quanta gent té la malaltia** (prevalença). Si la malaltia és rara (1 de cada 10.000), fins i tot amb un test del 99%, la majoria de positius seran **falsos positius**.

El teorema de Bayes ens dóna la maquinària per combinar:
1. El que sabíem **abans** d'observar res (probabilitat a priori),
2. El que les dades ens diuen (evidència / versemblança),
3. Per obtenir el que sabem **ara** (probabilitat a posteriori).

---

## 2. La fórmula

$$p(H \mid E) = \frac{p(E \mid H) \cdot p(H)}{p(E)}$$

On:
| Símbol | Nom | Significat |
|---|---|---|
| $p(H)$ | **Prior** (a priori) | Creença inicial sobre la hipòtesi H, abans de veure l'evidència |
| $p(E \mid H)$ | **Likelihood** (versemblança) | Probabilitat d'observar l'evidència E si H fos certa |
| $p(E)$ | **Evidence** (evidència marginal) | Probabilitat total d'observar E, sota qualsevol hipòtesi |
| $p(H \mid E)$ | **Posterior** (a posteriori) | Creença actualitzada sobre H, després de veure E |

### Forma alternativa (amb normalització)

$$p(H \mid E) = \alpha \cdot p(E \mid H) \cdot p(H)$$

on $\alpha = 1/p(E)$ és la constant de normalització. Quan hi ha poques hipòtesis, és més pràctic calcular els numeradors per a cada hipòtesi i normalitzar al final.

---

## 3. Analogia: El detectiu

Pensa en un detectiu que investiga un crim:

- **Prior $p(H)$**: Li arriben 3 sospitosos. Sense cap pista, assigna probabilitats inicials: $p(\text{A}) = 0.5$, $p(\text{B}) = 0.3$, $p(\text{C}) = 0.2$ basant-se en antecedents.
- **Evidence $E$**: Troba un cabell ros a l'escena del crim.
- **Likelihood $p(E \mid H)$**: Sap que:
  - A és ros → $p(\text{cabell ros} \mid A) = 0.9$
  - B és moreno → $p(\text{cabell ros} \mid B) = 0.05$
  - C és pèl-roig → $p(\text{cabell ros} \mid C) = 0.1$
- **Posterior**: Actualitza les sospites:
  - Numerador A: $0.9 \times 0.5 = 0.45$
  - Numerador B: $0.05 \times 0.3 = 0.015$
  - Numerador C: $0.1 \times 0.2 = 0.02$
  - Suma = 0.485
  - $p(A \mid E) = 0.45/0.485 = 0.928$

El cabell ros ha fet que la sospita sobre A pugi del 50% al 92.8%.

---

## 4. Conceptes fonamentals relacionats

### 4.1. Probabilitat condicionada

$$p(A \mid B) = \frac{p(A \cap B)}{p(B)}$$

"La probabilitat d'A donat que ja sabem B." Aquesta és la base de tot: Bayes és una manipulació algebraica d'aquesta definició.

### 4.2. Regla del producte

$$p(A \cap B) = p(A \mid B) \cdot p(B) = p(B \mid A) \cdot p(A)$$

Igualant les dues expressions i dividint per $p(B)$, obtenim Bayes.

### 4.3. Llei de probabilitats totals

$$p(E) = \sum_{i} p(E \mid H_i) \cdot p(H_i)$$

Serveix per calcular el denominador de Bayes: expandeix $p(E)$ sumant sobre totes les hipòtesis possibles (que han de ser exhaustives i mútuament excloents).

### 4.4. Independència

Dues variables A i B són **independents** si:
$$p(A \mid B) = p(A) \quad \Leftrightarrow \quad p(A, B) = p(A) \cdot p(B)$$

Saber B no canvia la creença sobre A.

### 4.5. Independència condicional

A i B són **condicionalment independents** donat C si:
$$p(A, B \mid C) = p(A \mid C) \cdot p(B \mid C)$$

Encara que A i B estiguin correlacionades, un cop coneixes C, deixen d'aportar informació l'una sobre l'altra. Això és la base de les **Xarxes Bayesianes**.

---

## 5. Bayes encadenat: actualització seqüencial

Si arriben múltiples evidències $E_1, E_2, \ldots$, podem aplicar Bayes repetidament:

$$p(H \mid E_1, E_2) = \frac{p(E_2 \mid H, E_1) \cdot p(H \mid E_1)}{p(E_2 \mid E_1)}$$

El **posterior** d'ahir es converteix en el **prior** d'avui.

Si les evidències són **condicionalment independents** donada $H$:

$$p(H \mid E_1, E_2) \propto p(E_1 \mid H) \cdot p(E_2 \mid H) \cdot p(H)$$

Això és exactament el que fa **Naïve Bayes**.

---

## 6. Naïve Bayes

### Assumpció central

Donada la classe $C$, totes les variables observables $X_i$ són condicionalment independents entre elles:

$$p(X_1, X_2, \ldots, X_n \mid C) = \prod_{i=1}^{n} p(X_i \mid C)$$

### Per què funciona malgrat l'assumpció?

L'assumpció gairebé mai és certa exactament (en un correu spam, "gratis" i "oferta" tendeixen a aparèixer juntes). Però tot i així:
- El classificador només necessita que la **classe amb score més alt** sigui correcta, no que les probabilitats exactes siguin correctes.
- Amb dades limitades, estimar $p(X_1, X_2, \ldots, X_n \mid C)$ de forma conjunta requeriria una quantitat exponencial de dades. Naïve Bayes descompon el problema en estimacions simples i robustes.

### Suavitzat de Laplace

Per evitar que una sola probabilitat zero anul·li tot el producte:

$$p(X_i = v \mid C) = \frac{\text{count}(X_i = v, C) + 1}{\text{count}(C) + |d(X_i)|}$$

On $|d(X_i)|$ és el nombre de valors possibles de $X_i$.

---

## 7. Xarxes Bayesianes (XB)

### Motivació

Una distribució conjunta sobre $n$ variables binàries necessita $2^n - 1$ paràmetres. Amb 30 variables: ~1.000 milions. Intractable.

Les XB exploten les **independències condicionals** per representar la distribució de forma compacta.

### Definició

Una XB és un **graf dirigit acíclic (DAG)** on:
- Cada node és una variable aleatòria.
- Cada arc indica una dependència directa.
- Cada node $X$ té una **taula de probabilitat condicionada (CPT)**: $p(X \mid \text{Pares}(X))$.

### Distribució conjunta

$$p(X_1, \ldots, X_n) = \prod_{i=1}^{n} p(X_i \mid \text{Pares}(X_i))$$

### Independències estructurals (d-separació)

La topologia del graf determina quines variables són independents:

| Patró | Estructura | Independència |
|---|---|---|
| **Cadena** | A → B → C | A⊥C ∣ B (bloqueig actiu) |
| **Causa comuna** | A ← B → C | A⊥C ∣ B (bloqueig actiu) |
| **V-estructura** | A → B ← C | A⊥C (marginal); **A⊥̸C ∣ B** (conèixer B activa!) |

---

## 8. Mètodes d'inferència en XB

### 8.1. Inferència per enumeració (exacta)

$$p(Q \mid E = e) = \alpha \sum_{\text{ocultes}} \prod_{i} p(X_i \mid \text{Pares}(X_i))$$

**Problema:** complexitat exponencial en el nombre de variables ocultes.

### 8.2. Eliminació de variables (exacta)

Estratègia més eficient: en lloc de sumar sobre totes les combinacions, elimina una variable oculta a la vegada:
1. Multiplica les taules que la contenen.
2. Marginalitza (suma sobre ella).
3. Repeteix amb la següent variable.

**Complexitat:** depèn de l'**ample d'arbre** (treewidth). Si el graf és un arbre, és lineal.

### 8.3. Mostreig (aproximada)

Quan la xarxa és massa gran per a inferència exacta:

| Mètode | Idea |
|---|---|
| **Forward Sampling** | Genera mostres seguint l'ordre ancestral. Rebuig si no coincideix amb l'evidència (ineficient). |
| **Likelihood Weighting** | Fixa l'evidència i pondera cada mostra pel producte de les probabilitats de les variables observades. |
| **Gibbs Sampling (MCMC)** | Itera: fixa totes les variables menys una, mostra aquella una de la seva distribució condicionada. |

---

## 9. Exemple complet: del prior al posterior

**Exemple del metge (base rate fallacy):**

- Malaltia rara: $p(M) = 0.001$ (1 de cada 1000).
- Test sensible: $p(+ \mid M) = 0.99$ (99% de detecció).
- Test específic: $p(+ \mid \neg M) = 0.02$ (2% de falsos positius).

**Pregunta:** El test dona positiu. Quina és la probabilitat de tenir la malaltia?

**Pas 1: Prior.**
$p(M) = 0.001$, $p(\neg M) = 0.999$.

**Pas 2: Likelihood.**
$p(+ \mid M) = 0.99$, $p(+ \mid \neg M) = 0.02$.

**Pas 3: Evidence.**
$p(+) = 0.99 \times 0.001 + 0.02 \times 0.999 = 0.00099 + 0.01998 = 0.02097$.

**Pas 4: Posterior.**
$$p(M \mid +) = \frac{0.99 \times 0.001}{0.02097} = \frac{0.00099}{0.02097} \approx \mathbf{0.047}$$

**Resultat:** Només un 4.7% de probabilitat de tenir la malaltia, malgrat un test del 99%!

**Per què?** Com que la malaltia és molt rara, la gran quantitat de sans que donen fals positiu (2% de 999 = ~20 persones) supera àmpliament els malalts que donen positiu veritable (~1 persona). De cada ~21 positius, només 1 està realment malalt.

**Lliçó:** El **prior** importa. Ignorar-lo porta a la *base rate fallacy*, un dels errors de raonament més comuns.

---

## 10. Resum visual

```
           PRIOR           ×      LIKELIHOOD         =    POSTERIOR
     (Què creia abans?)     (Quant d'acord estan        (Què crec ara?)
                              les dades amb H?)

        p(H)               ×      p(E | H)           ∝    p(H | E)
                                                      
                         ÷ EVIDÈNCIA TOTAL
                            p(E) = Σ p(E|H_i)·p(H_i)
```

**Les tres claus per recordar:**
1. **Bayes actualitza creences.** No és una fórmula estàtica; és un procés d'aprenentatge.
2. **El prior no és subjectiu per sempre.** Amb prou dades, el posterior convergeix independentment del prior.
3. **L'evidència normalitza.** $p(E)$ assegura que les probabilitats sumin 1, però no conté informació sobre quina hipòtesi és correcta.
