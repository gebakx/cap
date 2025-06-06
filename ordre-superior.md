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

- Exercicis

---

# Exercicis

- Definiu una funció `freqs` que, donada una llista, torni un diccionari que tingui com a claus els elements de la llista i com a valors el nombre de vegades que apareix a la llista.

  ```clojure
  (freqs '(1 2 3 2 1 3))  👉  {1 2, 2 2, 3 2}
  ```

- Definiu una funció `paraules-inici-fi` que, donats un string amb paraules separades per espais i dos caràcters, torni les paraules de l'string que comencen pel 1er caràcter i acaben pel segon.

  ```clojure
  (paraules-inici-fi "el gos menja galetes" \g \s)  👉  ("gos" "galetes")
  ```

- Definiu una funció `ordre-diferents` que, donat un string amb paraules separades per espais, torni una llista ordenada de paraules no repetides.

  ```clojure
  (ordre-diferents "aa bb ab bb ab ab")  👉  ("aa" "ab" "bb")
  ```

---

# Exercicis

- Definiu una funció `ordre-mida` que, donada una llista de ciutats, torni la llista de les que tenen més de 5 lletres ordenades pel nombre de lletres.

  ```clojure
  (def ciutats '("Roma" "París" "Tòquio" "Nairobi" "Barcelona"))
  (ordre-mida ciutats)  👉  ("Tòquio" "Nairobi" "Barcelona")
  ```

- Definiu una funció `agrupa` que, donada una llista de ciutats, les agrupi de 3 en 3.

  ```clojure
  (def ciutats '("Roma" "París" "Tòquio" "Londres" "Nairobi" "Barcelona" "Nova Delhi"))
  (agrupa ciutats)  👉  (("Tòquio" "París" "Roma") ("Barcelona" "Nairobi" "Londres") ("Nova Delhi"))
  ```

- Definiu una funció `cartesia` que, donats 3 conjunts, torni una llista de llistes amb el producte cartesià dels 3 conjunts.

  ```clojure
  (cartesia #{"x" "y" "z"} #{1 2 3 4} #{:blanc :negre})
  👉
  (("z" 1 :blanc) ("z" 1 :negre) ("z" 4 :blanc) ("z" 4 :negre) ("z" 3 :blanc) ("z" 3 :negre) ("z" 2 :blanc) ("z" 2 :negre) ("x" 1 :blanc) ("x" 1 :negre) ("x" 4 :blanc) ("x" 4 :negre) ("x" 3 :blanc) ("x" 3 :negre) ("x" 2 :blanc) ("x" 2 :negre) ("y" 1 :blanc) ("y" 1 :negre) ("y" 4 :blanc) ("y" 4 :negre) ("y" 3 :blanc) ("y" 3 :negre) ("y" 2 :blanc) ("y" 2 :negre))
  ```

---

# Exercici

- Definiu una funció `inner-join` que funcioni tal i com mostra l'exemple:

  ```clojure
  (def regions 
    '({:regId 1 :regName "Europe"}
      {:regId 2 :regName "Asia"}))

  (def countries
    '({:regId 1 :country "Belgium" :countryID :BE}
      {:regId 1 :country "Switzerland" :countryID :CH}
      {:regId 2 :country "India" :countryID :IN}
      {:regId 2 :country "Japan" :countryID :JP}
      {:regId 1 :country "Denmark" :countryID :DK}))
  ```

  ```clojure
  (inner-join regions countries :regId)
  👉
  ({:regId 1, :regName "Europe", :country "Belgium", :countryID :BE} 
   {:regId 1, :regName "Europe", :country "Switzerland", :countryID :CH} 
   {:regId 1, :regName "Europe", :country "Denmark", :countryID :DK} 
   {:regId 2, :regName "Asia", :country "India", :countryID :IN} 
   {:regId 2, :regName "Asia", :country "Japan", :countryID :JP})
  ```

---
class: left, middle, inverse

## Sumari

- .brown[Sessió 7]

- .cyan[Sessió 8]

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

- .brown[Sessió 7]

- .cyan[Sessió 8]

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

- .brown[Sessió 7]

- .cyan[Sessió 8]

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

- .brown[Sessió 7]

- .cyan[Sessió 8]

  - .brown[*Tail Recursion*]

  - .brown[*Continuation-Passing Style*]

  - .brown[*Trampolining*]

  - .cyan[Exercicis]

---

# Exercici 

**Fibonacci**:

- Definiu una funció `slow_fib` que torni l'n-èssim element de la sèrie de Fibonacci.

- Definiu una versió `quick_fib` amb *tail recursion*.

- Definiu una versió `iter_fib` iterativa amb `recur`.

- Definiu una versió `cps_fib` amb *continuation-passing style*.

- Executeu una versió `cps_fib` amb la funció `trampoline`.

- Comproveu el comportament de les diferents versions amb diferents paràmetres. La funció `time` us serà d'utilitat.

