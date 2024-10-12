class: center, middle

## Conceptes Avançats de Programació

# Clausures

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

- .cyan[Sessió 5]

  - .cyan[Clausures]

  - Exercicis

- Sessió 6

---

# Clausures

`fn` crea clausures (*closure*):
- tanca l'abast (*scope*) lèxic del voltant i captura els seus valors.

.cols5050[
.col1[
```clojure
(defn interna [x]
  (fn [y] (println x y "!"))) 
  ; torna una clausura que conté x 
  ; i la funció anònima

(def externa (interna "Hola"))

(externa "món")  👉  Hola món !
```

Fixeu-vos en que si la funció interna tornés només una funció, no funcionaria.
]
.col2[
![:scale 105%](figures/clausura.png)

.small[diagrama: [Python Tutor](https://pythontutor.com)]
]]

---

# Python Tutor

Podeu veure com funciona pas a pas utilitzant l'eina:

[Python Tutor](https://pythontutor.com/visualize.html#mode=edit)

i inserint l'equivalent python de l'_script_ anterior:

```python
def interna(x):
    return lambda y: print(x, y, "!")
    
externa = interna("Hola")

externa("món")
```

i clicant a **Visualize Execution**.

---
class: left, middle, inverse

## Sumari

- Sessió 5

  - .brown[Clausures]

  - .cyan[Exercicis]

- Sessió 6

---

# Exercici 1

- Executa aquest codi i justifica el resultat que mostra.

    ```clojure
    (ns misteri)

    (defn misteri [n]
      (let [secret 4
            n (+ n 2)]
        (fn [mult]
          (* secret (* mult n)))))

    (defn misteri3 [param]
      (fn [bonus]
        (+ (param 6) bonus)))

    (let [h (misteri 3)
          j (misteri3 h)
          result (j 2)]
      (println result))
    ```

---

# Exercici 2

Es poden utilitzar clausures per definir objectes sense classe. En aquest problema definirem l'"objecte" per representar punts en el pla amb clausures. Així fent `(punt 2 2)` ens tornarà una instància amb les coordenades cartesianes {2, 2}.

- Afegiu la funcionalitat tal que a l'aplicar les *keywords* (a l'estil dels mètodes) `:crt` o `:plr` per obtenir les coordenades cartesianes o polars respectivament.

    ```clojure
    ((punt 2 0) :crt)  👉  (2 0)

    ((punt 2 0) :plr)  👉  (2.0 0.0)

    ((punt 2 2) :crt)  👉  (2 2)

    ((punt 2 2) :plr)  👉  (2.8284271247461903 45.0)
    ```

---

# Exercici 2bis

- Afegiu la possibilitat de calcular distàncies euclidianes amb la *keyword* `:dst`.

    ```clojure
    ((punt 2 2) :dst (punt 2 0))  👉  2.0

    ((punt 2 0) :dst (punt 2 0))  👉  0.0
    ```

- Doneu una funció `mes-propera` que, donada una llista de punts i un punt, ens torni el punt de la llista més proper al donat com a paràmetre.

    ```clojure
    (mes-propera (punt 2 0) (list (punt 1 1) (punt 2 1) (punt 3 2)))
    👉  (2 1)
    ```

---

# Exercici 3

- Implementeu una funció `my-partial` que faci el mateix que la funció clojure `partial`:

    ```clojure
    ((my-partial * 2) 4)  👉  8

    ((my-partial * 2) 4 3)  👉  24
    ```

---
class: left, middle, inverse

## Sumari

- .brown[Sessió 5]

- .cyan[Sessió 6]

  - .cyan[Atoms]

  - Exercicis

---

# Atoms

Els atoms són objectes que contenen referències a valors. Ens permetran afegir mutabilitat a les clausures.

.cols5050[
.col1[
**Inicialització**:

```clojure
(atom 0)
```

```clojure
(def comptador (atom 0))      ; global

(let [comptador (atom 0)]     ; local
  ...)
```

**Accés**:

```clojure
(deref comptador)  👉  0

@comptador  👉  0
```
]
.col2[
**Modificació amb funció**:

```clojure
(swap! comptador #(+ % 1)) 👉 1

(swap! comptador inc)  👉  2
```

<br>

**Modificació amb valor**:

```clojure
(reset! comptador 3)  👉  3
```

]]


---
class: left, middle, inverse

## Sumari

- .brown[Sessió 5]

- .cyan[Sessió 6]

  - .brown[Atoms]

  - .cyan[Exercicis]

---

# Exercici 1

Estudieu aquest codi i justifiqueu perquè funciona:

```clojure
(defn memoize [f]
  (let [mem (atom {})]
    (fn [& args]
      (if-let [e (find @mem args)]
        (val e)
        (let [ret (apply f args)]
          (swap! mem assoc args ret)
          ret)))))

(defn fib [n]
  (if (<= n 1)
    n
    (+ (fib (dec n)) (fib (- n 2)))))

(time (fib 35))
user=> "Elapsed time: 941.445 msecs"

(def fib (memoize fib))

(time (fib 35))
user=> "Elapsed time: 0.044 msecs"
```

.footnote[font: [Clojure - Atoms](https://clojure.org/reference/atoms)]

---

# Exercici 2

- Feu una versió mutable de l'exercici 2 (punt) de la sessió 5.

- Han de continuar funcionant els "mètodes": `:crt`, `:plr` i `:dst`. 

- Afegiu tres "mètodes" nous:

  - `:rst`: que inicialitzi el punt a (0, 0).

  - `:inx`: que incrementi la coordenada x en 1.

  - `:iny`: que incrementi la coordenada y en 1.

```clojure
(use 'punts2 :reload-all)
(def p (punt))
(p :inx)  👉  [1 0]
(def q (punt))
(q :iny)  👉  [0 1]
(p :dst q)  👉  1.4142135623730951
```
