class: center, middle

### Conceptes Avançats de Programació

# Programació funcional en Clojure

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

- .cyan[Presentació]

  - .cyan[L'eina]

  - Tipus bàsics i expressions

  - Funcions, condicionals i lets

  - Seqüències

  - Recursivitat (recur) & seqüències (vectors, strings)

- Funcions *first-class*

- Clausures i models d'entorns

- Funcions d'ordre superior

- Immutabilitat i estructures de dades

- Avaluació mandrosa

- Macros

---

# Clojure

Dialecte de LISP que compila a la JVM.

### Referències

- Plana principal: [https://clojure.org/](https://clojure.org/)

- Instal·lació: [https://clojure.org/guides/install_clojure](https://clojure.org/guides/install_clojure)

### Eina:

.cols5050[
.col1[

```clojure
clj
```

És un *REPL* (intèrpret) i compilador.
]
.col2[
- quit: *Ctrl + d*

- load: `(use 'nom :reload-all)`
]]

---

# Hello world!

**Estructura del projecte**:

```
deps.edn
src/
└── hola_mon.clj
```

```clojure
;; deps.edn
{:paths ["src"]}
```

```clojure
;; hola_mon.clj
(ns hola_mon)
(println "Hola, món!"))
```

**Execució**:

```shell
gerard@lopetit:~/docencia/cap/hola_mon$ clj
Clojure 1.11.3
(use 'hola_mon :reload-all)
Hola món!
nil
user=> 
```

---

# Linia de comandes

**Expressions**:

```shell
gerard@lopetit:~/docencia/cap/hola_mon$ clj -M -e '(+ 1 2)'
3
gerard@lopetit:~/docencia/cap/hola_mon$
```

**Scripts**:

```shell
gerard@lopetit:~/docencia/cap/hola_mon$ clj -M -e "(use 'hola_mon)"
Hola món!
gerard@lopetit:~/docencia/cap/hola_mon$
```

---

# Creació d'un arxiu *jar*

**deps.edn**:

```clojure
{
  :paths ["src"]
  :deps {clojure.java-time/clojure.java-time {:mvn/version "1.1.0"}}
  :main-opts ["-m" "core"]
  :aliases {
    ;; build an uberjar (application) with AOT compilation by default:
    :uberjar {:replace-deps {com.github.seancorfield/depstar {:mvn/version "2.1.303"}}
             :exec-fn hf.depstar/uberjar
             :exec-args {:aot true}}
    ;; build a jar (library):
    :jar {:replace-deps {com.github.seancorfield/depstar {:mvn/version "2.1.303"}}
          :exec-fn hf.depstar/jar
          :exec-args {}}
   }
}
```
.cols5050[
.col1[
**hola_mon.clj**:

```clojure
(ns hola_mon)
(defn -main []
  (println "Hola món!"))
```
]
.col2[
**ordres**:
```shell
clj -X:uberjar :jar hola_mon.jar
java -cp hola_mon.jar clojure.main 
     -m hola_mon 
```

]]
---
class: left, middle, inverse

## Sumari

- .cyan[Presentació]

  - .brown[L'eina]

  - .cyan[Tipus bàsics i expressions]

  - Funcions, condicionals i lets

  - Seqüències

  - Recursivitat (recur) & seqüències (vectors, strings)

- Funcions *first-class*

- Clausures i models d'entorns

- Funcions d'ordre superior

- Immutabilitat i estructures de dades

- Avaluació mandrosa

- Macros

---
