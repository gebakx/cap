class: center, middle

## Conceptes Avançats de Programació

# Immutabilitat

<br>

**Jordi Delgado i Gerard Escudero**

<br>

Departament de Ciències de la Computació

Universitat Politècnica de Catalunya

<br>

![:scale 12%](figures/clojure_logo.png) ![:scale 75%](figures/fib.png)

---
class: left, middle, inverse

## Sumari

- .cyan[Immutabilitat]

- Exercicis

---

# Immutabilitat

**Concepte**:

- Objecte **immutable**: una vegada creat, no es pot canviar el seu estat.

- **Immutabilitat**: el valor d'una variable no es pot canviar un cop assignat en la creació.

**Exemple**:

- Per modificar una llista hem de crear-ne una de nova:

  ```clojure
  (defn inserir [ll val pos]
      (concat (take pos ll) (conj (drop pos ll) val)))

  (inserir '(1 2 4 5) 3 2)  👉  (1 2 3 4 5)
  ```

---

# Programació funcional

Molta importància a la creació de **funcions pures** per crear codi més segur.

- S'intenta separar o aïllar la noció d'estat (funcions impures).

- L'estat és el responsable més gran dels efectes laterals.

- Una funció és **pura** quan sempre retorna el mateix resultat per una mateixa entrada, sense efectes laterals.

  - Són més fàcils de testejar, depurar i mantenir.

  - No modifiquen dades de fora el seu àmbit (*scope*).

<br>

La **immutabilitat** ajuda en la definició de **funcions pures**.

---

.cols5050[
.col1[
## Avantatges 

- Codi més predecible;
  - evita canvis de dades, i
  - raonament sobre el codi.

- Compartició de dades.

.center[![:scale 70%](figures/shared-trees.png)]

.center[.small[.blue[Font]: The Joy of Clojure.]]

- Concurrència i paral·lelisme:
  - Estr. de dades compartides
  - Menys conflictes
]
.col2[
## Reptes

- **Problemes d'espai**: <br>
  Es minimitza l'impacte mitjançant optimització o compartició de dades.

- **Eficiència**: <br>
  La creació de noves instàncies pot arribar a ser un problema. <br>
  Avaluació mandrosa, cachés o memoització poden ajudar a minimitzar aquest impacte.
]]

---
class: left, middle, inverse

## Sumari

- .brown[Immutabilitat]

- .cyan[Exercicis]

---

# Exercicis

1. [Arbre binari](problemes/arbre-binari.pdf)

2. Expressions
