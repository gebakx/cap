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

- .cyan[Clausures]

- Exercicis

---

# Clausures

`fn` crea clausures (*closure*):
- tanca l'abast (*scope*) lèxic del voltant i captura els sus valors.

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

# Models d'entorns

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

- .brown[Clausures]

- .cyan[Exercicis]

---

# Exercicis

- Dir que mostra un exemple.

- Punts amb clausures i distància euclidea.

- `my-partial`

