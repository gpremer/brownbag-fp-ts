# functional programming met fp-ts


---

## Functioneel programmeren

----

### Het fundament

* Werken met functies net als met andere types

* Als input van fucties en returnwaarde van functies (higher order functions)

* Referential transparancy en geen side-effects

* Patronen om functies te combineren

----

### Functioneel programmeren in Javascript

* De normaalste zaak van de wereld: call-backs

* Bibliotheek als Rambda 

* Specificate in Fantasyland

---

### Functioneel programmeren in Typescript

* Ontbrekende stuk van de puzzel: sterke typering

```
function double(n: number): number {
  return 2 * n;
}

const double2: (n: number) => number = n => 2 * n
```

---

=== Spelen met argumenten: currying

```
function sum(x: number, y: number): number {
  return x + y;
}

const s1 = sum(1, 2);

function sum2(x: number): (y: number => number) {
  return function(y: number) {
    return x + y;
  }
}

const s2 = sum2(1)(2);

const sum3: (x: number) => (y: number) => number = x => y => x + y

const s3 = sum3(1)(2);
```

---

### Voorbeeld van higher order function

```
const swapper: <A, B, C>(f: ((a: A, b: B) => C)) => ((b: B, a: A) => C) = f => (b, a) => f(a, b);

const format = (n: number, s: string) => `${n} ${s}`;

const revFormat = swapper(format);

format(5, "EUR");
revFormat("EUR", 5);
```
----

## Fp-ts

---

### Intro

* Implementatie van concepten uit theorie rond FP

---
