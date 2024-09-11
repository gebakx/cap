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

- .brown[Clausures]

- .cyan[Exercicis]

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

# Més exercicis

- `my-partial`

