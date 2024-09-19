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

- .brown[Funcions d'ordre superior habituals]

- .cyan[*Destructuring*]

- Llistes per comprensió

- Exercicis

---

# Destructuring

És una forma de crear *bindings* a valors a partir d'estructures.

**Sequential destructuring**:

```clojure
(defn distancia [va vb]
    (let [[ax ay] va
          [bx by] vb]
        (Math/sqrt (+ (Math/pow (- ax bx) 2) (Math/pow (- ay by) 2)))))

(distancia '(2 2) '(1 2))  👉  1.0
```

**Associative destructuring**:

```clojure
(defn distancia [va vb]
    (let [{ax :x ay :y} va
          {bx :x by :y} vb]
        (Math/sqrt (+ (Math/pow (- ax bx) 2) (Math/pow (- ay by) 2)))))

(distancia '{:x 2 :y 2} '{:x 1 :y 2})  👉  1.0
```

.small[[Documentació Destructuring](https://clojure.org/guides/destructuring)]

---
class: left, middle, inverse

## Contingut

- .brown[Funcions *First-Class*]

- .brown[Seqüències i col·leccions]

- .brown[*Destructuring*]

- .cyan[Funcions d'ordre superior habituals]

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

### `comp` 

Composició de funcions.

**Exemple**:

```clojure
((comp reverse sort) '(3 1 5))  👉  (5 3 1)
```

```clojure
(def tres-mes-grans (comp (partial take 3) reverse sort))

(tres-mes-grans '(3 1 2 6 7))  👉  (7 6 3)
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
(for [x (range 1 6) :when even?] (* x x))  👉  (1 4 9 16 25)    ; tipus filter
```

```clojure
(for [x (range 3) y (range 3)] x y)                             ; cartesià
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

- .brown[Llistes per comprensió]

- .cyan[Exercicis]

---

# Exercicis

1. [Funcions d'ordre superior](problemes/first-class.pdf)

2. [Llistes per comprensió](problemes/llistes-comprensio.pdf)



