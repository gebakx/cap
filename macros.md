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

# Macros vs Functions

Les macros no són *first-class*; es tracten com qualsevol altre valor.
- No pots accedir a elles en runtime. No poden ser passades com a paràmetres ni retornades per una funció. No són programació funcional.

Són útils quan:
- El codi s'ha d'executar en temps de compilació.
- Treballar amb argument no avaluats (ex: macro when).  Evasió de l'avaluació anticipada
- Molt a veure amb noves estructures sintàctiques.
- inline code (ex: log amb num de línia)


---

# Exercicis

**unless**:

```clojure
(defmacro unless [condition & body]
  `(if (not ~condition)
     (do ~@body)))

;; Exemple d'ús:
(unless false
  (println "Aquest missatge es mostrarà perquè la condició és falsa."))

(unless true
  (println "Aquest missatge no es mostrarà perquè la condició és certa."))
```

**Decorador memoize**:


**Bucle foreach**:


**Bucle for**:

```clojure
(defmacro for-loop [[init condition step] & body]
  `(loop [~@(take 1 init)]
     (when ~condition
       ~@body
       (recur ~step))))

;; Exemple d'ús:
(for-loop [i 0 (< i 5) (inc i)]
  (println "Valor de i:" i))
```

