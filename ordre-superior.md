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

- .cyan[Composició i *pipelining*]

- *Tail Call Optimisation*

- *Continuation-Passing Style*

- *Trampolining*

- Exercicis

---

# Composició i *pipelining*

**Composició de funcions**:

```clojure
((comp reverse sort) '(3 1 5))  👉  (5 3 1)
```

**Pipelining**:

```clojure
(def tres-mes-grans (comp (partial take 3) reverse sort))

(tres-mes-grans '(3 1 2 6 7))  👉  (7 6 3)
```

---
class: left, middle, inverse

## Sumari

- .brown[Composició i *pipelining*]

- .cyan[*Tail Call Optimisation*]

- *Continuation-Passing Style*

- *Trampolining*

- Exercicis

---

# *Tail Call Optimisation*

En Clojure s'aconsegueix amb l'ús del `recur`.

**Exemple**:

```clojure
(defn taula [n]
  (loop [i 1]
    (if (= i 11)
      nil
      (do
        (println i "x" n "=" (* i n))
        (recur (inc i))))))
```

```clojure
(taula 2)
👉
1 x 2 = 2
2 x 2 = 4
3 x 2 = 6
4 x 2 = 8
5 x 2 = 10
6 x 2 = 12
7 x 2 = 14
8 x 2 = 16
9 x 2 = 18
10 x 2 = 20
nil
```

---
class: left, middle, inverse

## Sumari

- .brown[Composició i *pipelining*]

- .brown[*Tail Call Optimisation*]

- .cyan[*Continuation-Passing Style*]

- *Trampolining*

- Exercicis



