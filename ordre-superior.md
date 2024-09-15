class: center, middle

## Conceptes Avançats de Programació

# Funcions d'ordre superior

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

- .cyan[Composició i *Pipelining*]

- *Tail Recursion*

- *Continuation-Passing Style*

- *Trampolining*

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
class: left, middle, inverse

## Sumari

- .brown[Composició i *Pipelining*]

- .cyan[*Tail Recursion*]

- *Continuation-Passing Style*

- *Trampolining*

- Exercicis

---

# Recursivitat

*Tail Recursion*: la crida recursiva es fa just abans de retornar el valor.

.cols5050[
.col1[
**Factorial recursiu**:

```clojure
(defn f_rec [n]
  (if (== n 0)
    1
    (*' n (f_rec (dec n)))))
```
]
.col2[
**Factorial tail recursion**:

```clojure
(defn f_tailrec
  ([n] (f_tailrec n 1))
  ([n resultat]
    (if (== n 0)
      resultat
      (f_tailrec (dec n) 
                 (*' n resultat)))))
```
]]

- **Tail Recursion Optimization**: optimització en que el compilador substitueix la crida per recursiva per un salt. 

- Clojure no ho suporta degut a l'arquitectura de la *JVM*

- Altra opció és passar-la a **iterativa**...

---

# Recur

Aproximació en Clojure:

```clojure
(defn f_iter
  ([n] (f_iter n 1))
  ([n resultat]
    (if (== n 0)
      resultat
      (recur (dec n) (*' n resultat)))))
```

---
class: left, middle, inverse

## Sumari

- .brown[Composició i *Pipelining*]

- .brown[*Tail Recursion*]

- .cyan[*Continuation-Passing Style*]

- *Trampolining*

- Exercicis

---

# *Continuation-Passing Style*

És una tècnica de la programació funcional en la que es retornen les funcions a aplicar al resultat, en lloc dels valors.

**Exemple**:

```clojure
(def expr #(+ (* (+ 1 2) 3) 4))
(expr)  👉  13
```

Fem una funció per cada operació.

```clojure
(def mes2 (fn [x cont] (cont (+ 2 x))))
(def per3 (fn [x cont] (cont (* 3 x))))
(def mes4 (fn [x cont] (cont (+ 4 x))))

(defn expr_cps [x cont]
  (mes2 x 
    (fn [y]
      (per3 y 
        (fn [z] 
          (mes4 z 
            (fn [res]
              (cont res))))))))

(expr_cps 1 println)  👉  13
```

---

# CPS amb funcions recursives

Amb una funció recursiva és més natural.

**Exemple**:

```clojure
(defn fact_cps [n cont]
  (if (== n 0)
    (cont 1)
    (fact_cps (dec n) #(cont (*' n %))))) 

(fact_cps 6 identity)  👉  720
```

Continuem tenint el problema de la pila.

---
class: left, middle, inverse

## Sumari

- .brown[Composició i *Pipelining*]

- .brown[*Tail Recursion*]

- .brown[*Continuation-Passing Style*]

- .cyan[*Trampolining*]

- Exercicis

---

# *Trampolining*

És una tècnica que evita el creixement de la pila.

### `trampoline`

`(trampoline f & args)`

Aplica `f` a `args` i continua aplicant el resultat fins que deixa de ser una funció.

**Factorial**:

Afegim a la funció factorial un parell de *lambdes*:

.cols5050[
.col1[
```clojure
(defn fact_cps2 [n cont]
  (if (== n 0)
    (cont 1)
    (fn [] 
      (fact_cps2 
        (dec n)
        (fn [valor] 
          (fn [] 
            (cont (*' n valor))))))))
```
]
.col2[

**Funciona!**

```python
(trampoline fact_cps2 6 identity)  
👉  720
```

```python
(trampoline fact_cps2 1000 identity)
👉  4023872600770.....0000000000000000N
```
]]

---
class: left, middle, inverse

## Sumari

- .brown[Composició i *Pipelining*]

- .brown[*Tail Recursion*]

- .brown[*Continuation-Passing Style*]

- .brown[*Trampolining*]

- .cyan[Exercicis]

---

# Exercici 1


---

# Exercici 2

**Fibonacci**:

- Feu una funció `slow_fib` que torni l'n-èssim element de la sèrie de Fibonacci.

- Feu una versió `quick_fib` amb *tail recursion*.

- Feu una versió `iter_fib` iterativa amb `recur`.

- Feu una versió `cps_fib` amb *continuation-passing style`.

- Feu una versió `cps2_fib` per utilitzar amb la funció `trampoline`.

- Comproveu el comportament de les diferents versions amb diferents paràmetres. La funció `time` us serà d'utilitat.

