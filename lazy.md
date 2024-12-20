class: center, middle

## Conceptes Avançats de Programació

# Seqüències _Lazy_

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

- .cyan[Avaluació mandrosa]

- Seqüències mandroses

- Exercicis

---

# Avaluació mandrosa

- L'avaluació mandrosa (lazy) només avalua el que cal.

- Un *thunk* representa un valor que encara no ha estat avaluat.

- L'avaluació mandrosa no avalua els *thunks* fins que no ho necessita.

- Les expressions es tradueixen en un graf que és recorregut per obtenir els elements necessaris.

- El mecanisme clojure més important per treballar amb avaluació mandrosa són les `lazy-seq`.

---

# Visualització dels *thunks* 

.cols5050[
.col1[
**Haskell**:

```haskell
λ> xs = [x + 1 | x <- [1..10]] :: [Int]

λ> :sprint xs
xs = _

λ> null xs
False

λ> :sprint xs
xs = _ : _

λ> head xs
2

λ> :sprint xs
xs = 2 : _

λ> length xs
10

λ> :sprint xs
xs = [2,_,_,_,_,_,_,_,_,_]
```
]
.col2[
**Clojure**:

```clojure
(def xs (for [x (range 1 11)] (inc x)))

xs
👉  thunk

(empty? xs)
👉  false

xs
👉  (cons thunk thunk)

(first xs)
👉  2

xs
👉  (cons 2 thunk)

(count xs)
👉  10

xs
👉  (2 thunk thunk thunk ...)
```
]]


---

# Visualització dels *thunks* 

.cols5050[
.col1[
**Haskell**:

```haskell
λ> y = head $ tail $ tail xs

λ> :sprint y
y = _

λ> :sprint xs
xs = [2,_,_,_,_,_,_,_,_,_]

λ> y
4

λ> :sprint y
y = 4

λ> :sprint xs
xs = [2,_,4,_,_,_,_,_,_,_]

λ> xs
[2,3,4,5,6,7,8,9,10,11]

λ> :sprint xs
xs = [2,3,4,5,6,7,8,9,10,11]
```
]
.col2[
**Clojure**:

```clojure
(def y (first (rest (rest xs))))

y
👉  thunk

xs
👉  (2 thunk thunk thunk ...)

y
👉  4

y
👉  4

xs
👉  (2 thunk 4 thunk ...)

xs
👉  (2 3 4 5 6 7 8 9 10 11)

xs
👉  (2 3 4 5 6 7 8 9 10 11)
```
]]

---
class: left, middle, inverse

## Sumari

- .brown[Avaluació mandrosa]

- .cyan[Seqüències mandroses]

- Exercicis

---

## Zeros

```clojure
(take 10 (repeat 0))
👉  (0 0 0 0 0 0 0 0 0 0)

(take 10 (cycle '(0)))
👉  (0 0 0 0 0 0 0 0 0 0)

(take 10 (iterate identity 0))
👉  (0 0 0 0 0 0 0 0 0 0)

(def zeros (lazy-seq (cons 0 zeros)))
(take 10 zeros)
👉  (0 0 0 0 0 0 0 0 0 0)
```

--

## Naturals

```clojure
(take 10 (range))
👉  (0 1 2 3 4 5 6 7 8 9)

(take 10 (iterate inc 0))
👉  (0 1 2 3 4 5 6 7 8 9)

(def naturals (lazy-seq (cons 0 (map inc naturals))))
(take 10 naturals)
👉  (0 1 2 3 4 5 6 7 8 9)

```

---

## Factorials

```clojure
(def factorials (lazy-seq (cons 1N (map * factorials (iterate inc 1N)))))
(take 10 factorials)
👉  (1N 1N 2N 6N 24N 120N 720N 5040N 40320N 362880N)

(take 10 (reductions * 1N (iterate inc 1N)))
👉  (1N 1N 2N 6N 24N 120N 720N 5040N 40320N 362880N)
```

--

## Fibonacci

```clojure
(def fibs (cons 0 (lazy-seq (cons 1 (map + fibs (rest fibs))))))
(take 10 fibs)
👉  (0 1 1 2 3 5 8 13 21 34)

(defn fibs
    ([]
        (fibs 1 1))
    ([a b]
        (lazy-seq (cons a (fibs b (+ a b))))))
(take 10 (fibs))
👉  (0 1 1 2 3 5 8 13 21 34)
```

---

## Primes

```clojure
(def primes (letfn [(sieve [s]
    (cons (first s)
        (lazy-seq (sieve 
            (filter 
                #(not= 0 (mod % (first s)))
                (rest s))))))]
    (sieve (iterate inc 2))))    

(take 10 primes)
👉  (2 3 5 7 11 13 17 19 23 29)
```

---
class: left, middle, inverse

## Sumari

- .brown[Avaluació mandrosa]

- .brown[Seqüències mandroses]

- .cyan[Exercicis]

---

# Exercicis

1. [U47698 Clojure - Llistes infinites](https://jutge.org/problems/U47698_ca)

