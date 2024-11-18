class: center, middle

## Conceptes Avançats de Programació

# Macros

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

- .cyan[Macros]

- Exercicis

---

# Definició

Les macros són la descripció d'un patró que permet reemplaçar una part de codi per una altra:

- La seva sintaxi similar a la de les funcions

- S'apliquen en temps de compilació

- La seva aplicació s'anomena expansió

- El seu ús queda restringit a construccions sintàctiques

**Exemple**:

- Ús: `(when test & body)`

- Avalua `test`. Si `true`, avalua `body` en un `do`.

.cols5050[
.col1[
```clojure
(when (not= 0 3) (/ 2 3))  👉  2/3

(when (not= 0 0) (/ 2 3))  👉  nil
```
]
.col2[
```clojure
(macroexpand '(when (not= 0 3) (/ 2 3)))
👉
(if (not= 0 3) (do (/ 2 3)))
```
]]

---

# Característiques

- Les macros no són *first-class*

- No pots accedir a elles en *runtime*

- No poden ser passades com a paràmetres ni retornades per una funció

- No són programació funcional.

### Utilitat

- El codi s'ha d'executar en temps de compilació

- Treballar amb argument no avaluats (ex: macro when)

- Molt a veure amb noves estructures sintàctiques

---

# Expansió

- `(macroexpand form)`: eina per expandir les macros.

- Permet veure la implementació de les macros del sistema.

- Molt útil per depurar el procés de creació de macros.

**Exemple**:

```clojure
(-> {} (assoc :a 1) (assoc :b 2))
{:b 2, :a 1}

👉

(macroexpand '(-> {} (assoc :a 1) (assoc :b 2)))
(assoc (assoc {} :a 1) :b 2)
```

---

# Sintaxi


```clojure
(defmacro when
  "Evaluates test. If logical true, evaluates body in an implicit do."
  {:added "1.0"}
  [test & body]
  (list 'if test (cons 'do body)))
```

```clojure
(defmacro or
  "Evaluates exprs one at a time, from left to right. If a form
  returns a logical true value, or returns that value and doesn't
  evaluate any of the other expressions, otherwise it returns the
  value of the last expression. (or) returns nil."
  {:added "1.0"}
  ([] nil)
  ([x] x)
  ([x & next]
      `(let [or# ~x]
         (if or# or# (or ~@next)))))
```

---

# Gensym

---

# Exemple amb metas??

**thread-last**:

```clojure
(defmacro ->
  "Threads the expr through the forms. Inserts x as the
  second item in the first form, making a list of it if it is not a
  list already. If there are more forms, inserts the first form as the
  second item in second form, etc."
  {:added "1.0"}
  [x & forms]
  (loop [x x, forms forms]
    (if forms
      (let [form (first forms)
            threaded (if (seq? form)
                       (with-meta `(~(first form) ~x ~@(next form)) (meta form))
                       (list form x))]
        (recur threaded (next forms)))
      x)))
```

---
class: left, middle, inverse

## Sumari

- .brown[Macros]

- .cyan[Exercicis]

---

# Exercicis

Definiu les macros que tinguin el comportament següent:

- **unless**:

    ```clojure
    (unless false
      (println "Aquest missatge es mostra perquè la condició és falsa."))
    👉
    Aquest missatge es mostra perquè la condició és falsa.
    nil

    (unless true
      (println "Aquest missatge no es mostra perquè la condició és certa."))
    👉
    nil
    ```

---

# Exercicis

Definiu les macros que tinguin el comportament següent:

- **Bucle for**:

    ```clojure
    (for-loop [i 0 (< i 5) (inc i)]
      (println "Valor de i:" i))
    ```

**Bucle foreach**:


---

# Exercicis

Definiu les macros que tinguin el comportament següent:


**Point-free**:

- Canvi d'associativitat

- Consumir només un element

- mirar thread-first thread-last


