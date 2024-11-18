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

- Thread-first

    ```clojure
    (-> {} (assoc :a 1) (assoc :b 2))
    👉
    {:b 2, :a 1}
    ```

- Expansió

    ```clojure
    (macroexpand '(-> {} (assoc :a 1) (assoc :b 2)))
    👉
    (assoc (assoc {} :a 1) :b 2)
    ```

---

# Sintaxi

**Funcions**:

`(defn name doc-string? attr-map? [params*] body)`

**Macros**:

`(defmacro name doc-string? attr-map? [params*] body)`

**Exemple**:

```clojure
(defmacro my-when
  [condicio & cos]
  (list 'if condicio (cons 'do cos)))
```

```clojure
(my-when (not= 0 3) (/ 2 3))  👉  2/3
```

```clojure
(macroexpand '(my-when (not= 0 3) (/ 2 3)))
👉
(if (not= 0 3) (do (/ 2 3)))
```

---

# Quoting per macros

- .blue[Quote]: no avalua

    ```clojure
    '(+ 1 2)  👉  (+ 1 2)
    ```

- .blue[Syntax-quote]: + *symbol auto-qualification*

    ```clojure
    `(+ 1 2)  👉  (clojure.core/+ 1 2)
    ```

- .blue[Unquote]: avalua

    ```clojure
    `(+ 1 ~(* 2 3))  👉  (clojure.core/+ 1 6)
    ```

    ```clojure
    (let [x '(2 3)] `(1 ~x))  👉  (1 (2 3))
    ```

- .blue[Unquote-splicing]: avalua i aplana

    ```clojure
    (let [x '(2 3)] `(1 ~@x))  👉  (1 2 3)
    ```

---

# Exemple amb símbols

.blue[Auto-gensym]: per generar noms "únics"

```clojure
 `or#  👉  or__153__auto__
```

**Exemple**:

```clojure
(defmacro my-or
  ([] nil)
  ([x] x)
  ([x & next]
      `(let [or# ~x]
         (if or# or# (or ~@next)))))
```

```clojure
(my-or true false)  👉  true
```

```clojure
(macroexpand '(my-or true false))
👉
(let* [or__199__auto__ true] 
    (if or__199__auto__ 
        or__199__auto__ 
        (clojure.core/or false)))
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

- **Bucle foreach**:

    ```clojure
    (foreach [x [1 2 3]] 
      (println (inc x)))
    👉
    2
    3
    4
    nil
    ```




