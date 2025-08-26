class: center, middle

## Conceptes Avançats de Programació

# Funcions *First-Class*

<br>

**Jordi Delgado i Gerard Escudero**

<br>

Departament de Ciències de la Computació

Universitat Politècnica de Catalunya

<br>

![:scale 12%](figures/clojure_logo.png) ![:scale 75%](figures/fib.png)

---
class: left, middle, inverse

## Contingut

- .cyan[Funcions *First-Class*]

- Seqüències i col·leccions

- *Destructuring*

- Funcions d'ordre superior habituals

- Composició i *Pipelining*

- Llistes per comprensió

- Exercicis

---

# Funcions *First-Class*

**Funcions first-class**: són un tipus; es tracten com qualsevol altre valor.

**Funcions d'ordre superior**: passen com a paràmetres o retornen funcions.

**Exemples**:

```clojure
(def apli2 (fn [f x] (f (f x))))   ; f paràmentre funció
```

```clojure
(apli2 inc 3)  👉  5
```

```clojure
(apli2 #(* %1 %1) 2)  👉  16
```

```clojure
(def m2
  ((fn [x]            ; retorna una funció
      #(* x %)) 
    2))

(m2 3)  👉  6
```

.small[Això últim és possible gràcies a les *clausures*; que veurem en en el proper tema.]

---
class: left, middle, inverse

## Contingut

- .brown[Funcions *First-Class*]

- .cyan[Seqüències i col·leccions]

- *Destructuring*

- Funcions d'ordre superior habituals

- Composició i *Pipelining*

- Llistes per comprensió

- Exercicis

---

# Seqüències i col·leccions

Les seqüències són una abstracció de les col·leccions.

- Operacions: `first`, `rest`, `next`, `seq`, `lazy-seq`


**Col·leccions**:

| col·lecció | exemple | operacions | documentació |
|:---|:---|:---|:---|
| list | '(1 2 3) | `first`, `rest` | [list ref](https://clojure.org/guides/learn/sequential_colls#_lists) |
| vector | [1 2 3] | `get`, `assoc` | [vector ref](https://clojure.org/guides/learn/sequential_colls#_vectors) |
| set | #{1 2 3} | `disj`, `contains?`, `union`, `intersection` | [set ref](https://clojure.org/guides/learn/hashed_colls#_sets), [ops](https://clojure.github.io/clojure/clojure.set-api.html) |
| map | {:a 1 :b 2} | `get`, `assoc`, `dissoc`, `contains?` | [map ref](https://clojure.org/guides/learn/hashed_colls#_maps) |

- Operacions: `=`, `count`, `conj`, `empty`, `seq` (obté una seqüència)


Abstracció que representa una vista sobre les col·leccions.


.footnote[El `conj` afegeix pel final als vectors.]

---
class: left, middle, inverse

## Contingut

- .brown[Funcions *First-Class*]

- .brown[Seqüències i col·leccions]

- .cyan[*Destructuring*]

- Funcions d'ordre superior habituals

- Composició i *Pipelining*

- Llistes per comprensió

- Exercicis

---

# Destructuring

És una forma de crear *bindings* a valors a partir d'estructures.

**Sequential destructuring**:

```clojure
(defn preu [v]
    (let [[quantitat preu-unitari] v]
        (* quantitat preu-unitari)))

(preu '(2 3.50))  👉  7.0
```

**Sequential amb cua**:

```clojure
(let [[x y & z] (range 5)]
    (list (list x y) z))  👉  ((0 1) (2 3 4))
```

**Associative destructuring**:

```clojure
(let [{quantitat :quantitat preu-unitari :preu-unitari} 
      {:quantitat 3 :preu-unitari 3.50}]
    (* quantitat preu-unitari))  👉  10.5
```

.small[[Documentació Destructuring](https://clojure.org/guides/destructuring)]

---
class: left, middle, inverse

## Contingut

- .brown[Funcions *First-Class*]

- .brown[Seqüències i col·leccions]

- .brown[*Destructuring*]

- .cyan[Funcions d'ordre superior habituals]

- Composició i *Pipelining*

- Llistes per comprensió

- Exercicis

---

# Funcions d'ordre superior habituals

### `map`

Aplica una funció a tots els elements d'una seqüència.

**Exemples**:

```clojure
(map inc '(1 2 3))  👉  (2 3 4)
```

```clojure
(map + '(1 2 3) '(4 5 6 7))  👉  (5 7 9)
```

### `filter`

Obté els elements d'una seqüència que satisfan un predicat.

**Exemple**:

```clojure
(filter even? '(2 1 4 6 7))  👉  (2 4 6)
```

---

# Funcions d'ordre superior habituals

### `drop-while`

Elimina els primers elements consecutius d'una seqüència que satisfan un predicat.

**Exemple**:

```clojure
(drop-while even? '(2 4 6 7 8))  👉  (7 8)
```

### `take-while`

Obté els primers elements consecutius d'una seqüència que satisfan un predicat.

**Exemple**:

```clojure
(take-while even? '(2 4 6 7 8))  👉  (2 4 6)
```
---

# Funcions d'ordre superior habituals

### `reduce`

Desplega un operador `⊕` a una llista $(x_1 x_2 ... x_n)$ donant el resultat $((x1 ⊕ x2) ⊕ ...) ⊕ xn$.

**Exemples**:

```clojure
(reduce + '(2 4 6))  👉  12
```

```clojure
(reduce * 1 '(2 3 4))  👉  24
```

### `iterate`

`(iterate f x)` retorna la llista infinita `'(x (f x) (f (f x))...)`.

**Exemple**:

```clojure
(take 5 (iterate inc 1))  👉  (1 2 3 4 5)

```

---

# Funcions d'ordre superior habituals

### `apply`

Aplica una funció a una llista.

**Exemple**:

```clojure
(apply + '(2 3))  👉  5`           ; equivalent a (+ 2 3)
```

### `partial` 

Torna una funció derivada de fixar paràmetres d'una altra funció que rep com a paràmetre. Està relacionada amb la *currificació*.

**Exemple**:

```clojure
((partial + 2) 4)  👉  6`
```

```clojure
(def f (partial #(str %1 %2 "!") "Hola ")) 
(f "Gerard")  👉  "Hola Gerard!"
```

---

# Funcions d'ordre superior habituals

### `juxt` 

Retorna la justaposició de l'aplicació d'una sèrie de funcions a valors.

```clojure
((juxt a b c) x) => [(a x) (b x) (c x)]
```

**Exemple**:

```clojure
((juxt (partial filter even?) (partial filter odd?)) (range 10))
👉  [(0 2 4 6 8) (1 3 5 7 9)]
```

### `every?`

Mira si un predicat es satisfà per tots els elements d'una seqüència.

**Exemple**:

```clojure
(every? even? '(2 4 6))  👉  true
```

---
class: left, middle, inverse

## Contingut

- .brown[Funcions *First-Class*]

- .brown[Seqüències i col·leccions]

- .brown[*Destructuring*]

- .brown[Funcions d'ordre superior habituals]

- .cyan[Composició i *Pipelining*]

- Llistes per comprensió

- Exercicis

---

# Composició i *Pipelining*

El *pipelining* és l'encadenament de funcions mitjançant la composició.

<br>

**Composició de funcions**

```clojure
((comp reverse sort) '(3 1 5))  👉  (5 3 1)
```
<br>

**Pipelining**:

```clojure
(def tres-mes-grans (comp (partial take 3) reverse sort))

(tres-mes-grans '(3 1 2 6 7))  👉  (7 6 3)
```

---

# Macros *thread-first* i *thread-last*

Tenim 2 macros que ens ajuden molt en el *pipelining*:

.cols5050[
.col1[
**Thread-first ->**:

```clojure
(-> a
    (f b)
    (g c))
```

que equival a:

```clojure
(g (f a b) c)
```
]
.col2[
**Thread-last ->>**:

```clojure
(->> a
    (f b)
    (g c))
```

que equival a:

```clojure
(g c (f b a))
```
]]

**Exemple**:

```clojure
(def tres-mes-grans #(->> % sort reverse (take 3)))

(tres-mes-grans '(3 1 2 6 7))  👉  (7 6 3)
```

---
class: left, middle, inverse

## Contingut

- .brown[Funcions *First-Class*]

- .brown[Seqüències i col·leccions]

- .brown[*Destructuring*]

- .brown[Funcions d'ordre superior habituals]

- .brown[Composició i *Pipelining*]

- .cyan[Llistes per comprensió]

- Exercicis

---

# Llistes per comprensió amb `for`

**Sintaxi**:

`(for seq-exprs body-expr)`

**Exemples**:

```clojure
(for [x (range 1 4)] (* x x))  👉  (1 4 9)                      ; tipus map
```

```clojure
(for [x (range 1 6) :when (even? x)] (* x x))  👉  (4 16)    ; tipus filter
```

```clojure
(for [x (range 3) y (range 3)] [x y])                             ; cartesià
👉
([0 0] [0 1] [0 2] 
 [1 0] [1 1] [1 2] 
 [2 0] [2 1] [2 2])
```

Es pot aplicar a altres col·leccions. 

.footnote[[Documentació](https://clojuredocs.org/clojure.core/for)]

---
class: left, middle, inverse

## Contingut

- .brown[Funcions *First-Class*]

- .brown[Seqüències i col·leccions]

- .brown[*Destructuring*]

- .brown[Funcions d'ordre superior habituals]

- .brown[Composició i *Pipelining*]

- .brown[Llistes per comprensió]

- .cyan[Exercicis]

---

# Exercicis

1. [W86002 Clojure - Ús de funcions d'ordre superior ](https://jutge.org/problems/W86002_ca)

2. [W54264 Clojure - Ús de llistes per comprensió ](https://jutge.org/problems/W54264_ca)

2. [Y93083 Clojure - Exercici de fold/reduce ](https://jutge.org/problems/Y93083_ca)

---

# Altres exercicis 

1. Escriu una funció que, donada una llista d'enters, filtri els nombres parells, multipliqui cada nombre per 2 i després sumi tots els elements.

2. Escriu una funció que, donat un map de noms i edats, retorni un vector dels noms de les persones majors de 18 anys, ordenades alfabèticament.

3. Escriu una funció que prengui un vector d'enters, elimini els negatius, prengui els tres primers nombres i multipliqui cadascun per 10.

4. Escriu una funció que, donat un vector de maps amb les claus `:preu` i `:quantitat`, calculi el total per cada element `(preu * quantitat)`, elimini els que tinguin un valor total inferior a 100 i sumi tots els totals.

---

# Altres exercicis 

5. Escriu una funció que rebi una llista de nombres, elimini els nombres més petits que 5, multipliqui els resultants per 3 i els ordeni en ordre descendent.

6. Escriu una funció que prengui una llista d'strings, calculi la longitud de cada cadena, elimini les longituds parells i sumi les longituds restants.

7. Escriu una funció que prengui una llista de nombres, elimini aquells que siguin múltiples de 3, prengui els primers 5 resultats i els elevi al quadrat.

8. Definiu una funció que converteixi un número romà al seu enter equivalent amb funcions d'ordre superior.
  Recordeu que els números romans s’escriuen amb els símbols I, V, X, L, C, D i M, amb valors 1, 5, 10, 50, 100, 500 i 1000 respectivament. En aquest sistema, per obtenir el nombre representat, se sumen els valors dels símbols, excepte els símbols situats a l’esquerra d’un símbol de valor més gran, que es resten.


