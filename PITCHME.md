# functional programming met fp-ts


---

## Functioneel programmeren

---

### Het fundament

@ul

- Werken met functies net als met andere types
- Als input van fucties en returnwaarde van functies (higher order functions)
- Referential transparency (geen side-effects)
- Patronen om functies te combineren

@ulend

---

### Functioneel programmeren in JavaScript

* De normaalste zaak van de wereld: call-backs

* Bibliotheek als [Rambda](http://ramdajs.com/) 

* Specificate in [Fantasyland](https://github.com/fantasyland/fantasy-land)

---

### Functioneel programmeren in TypeScript

* Ontbrekende stuk van de puzzel: sterke typering

```
function double1(n: number): number {
  return 2 * n;
}

const double2: (n: number) => number = n => 2 * n
```

+++

### Spelen met argumenten: currying

* Zonder currying

```
function sum(x: number, y: number): number {
  return x + y;
}

const s1 = sum(1, 2);
```

+++

### Spelen met argumenten: currying

* Met currying, functiestijl

```
function sum2(x: number): (y: number => number) {
  return function(y: number) {
    return x + y;
  }
}

const s2 = sum2(1)(2);
```

+++

### Spelen met argumenten: currying

* Met currying, variabelestijl

```
const sum3: (x: number) 
  => (y: number) => number = x => y => x + y

const s3 = sum3(1)(2);
```

+++

### Voorbeeld van higher order function

```
format(5, "EUR");    // 5,00€
revFormat("EUR", 5); // 5,00€

const format = (n: number, s: string) => `${n} ${s}`;

const revFormat = swapper(format);

const swapper: <A, B, C>(f: (a: A, b: B) => C) => 
                         ((b: B, a: A) => C) 
                        = f => (b, a) => f(a, b);


```

---

## Fp-ts

+++

### Intro

* Implementatie van concepten uit theorie rond FP
* Puur TypeScript
* Uitgebreid

+++

### Typische datastructuren

+++

### Null & undefined

* T. Hoare: `null` = _1 billion dollar mistake_

* typescript -> `undefined`

* typisch gevolg => run time errors

+++

#### Remediëring in JS

```
const unsafeQuote(x: string): string = `'${x}'`;

function quote(x: string): string {
  if ( x !== null && x !== undefined ) {
    return unsafeQuote(x);
  } else {
    return "";
  }
}

const quote: (x: string) = x => (!!x && unsafeQuote(x)) || ""; // :-(
```

* Wat met nesting?

* Wat met chaining?

+++

#### `Option<A>` datatype (`Maybe`)

* `type Option<A> = None<A> | Some<A>`

* Kan ofwel een waarde bevatten ofwel niet

```
  const m1: Option<number> = some(1);
  const m2: Option<string> = some("Haskell");
  const m3: Option<number> = none;
  const m4: Option<string> = none;
```

```
  const quote: (ms: Option<string>) => Option<string> = ms => ms.map(unsafeQuote);

  const c1 = quote(some("Curry"));
  const c2 = quote(none);
```

+++

#### Een rijke set aan operaties

* transformeer: `map`

```
none.map(unsafeQuote); // none
some("Frege").map(unsafeQuote); // some("'Frege'")

```

* selecteer: `filter`

```
const nonEmpty = (s: string) => s.length > 0;
none.filter(nonEmpty); // none
some("AWV").filter(nonEmpty); // some("AWV")
some("").filter(nonEmpty); // none
```
  
* combineer: `chain`

```
const asNonEmpty = (s: string) => s.length > 0 ? some(s) : none;
const lookUp: (id: string) => Option<Laag> = ...;
nonEmpty("").chain(lookUp);
```

+++

#### Interoperabiliteit

* Van JS/TS naar Option: `fromNullable`, `fromPredicate`

* Van Option naar JS/TS: `toNullable`, `toUndefined`, `getOrElse`, `getOrElseValue`, `fold`

```
const nonEmpty = (s: string) => s.length > 0;
const asNonEmpty2 = (s: string) => fromNullable(s).filter(nonEmpty);
const asNonEmpty3 = (s: string) => fromPredicate(nonEmpty)(s);
const asNonEmpty4 = fromPredicate(nonEmpty);
const asNonEmpty5 = pipe(fromNullable, filter(nonEmpty));

const s1: string = anOption.getOrElseValue("<null>"); // niet-lazy
const s2: string = anOption.getOrElse(() => throw new Error("Yikes")); // lazy
const s3: string = anOption.fold(
  () => "<null>",
  s => s
);
```

+++


### `Either<L, R>`

* `type Either<L, A> = Left<L, A> | Right<L, A>`

* Ofwel waarde van `L` ofwel van `R` type

* Uitbreiding van `Some<R>`

* Bijv. een berekening die foutwaarden propageert
  * Links = foutwaarde
  * Rechts = succes
  
+++

```
const parse = (errorMessage: string) => (input: string): Either<string, number> => {
  const n = parseInt(input, 10)
  return isNaN(n) ? left(errorMessage) : right(n)
}

parse("Geen getal")("5"); // right(5)
parse("Geen getal")("$5"); // left("Geen getal")
```

* Rechts preferentieel

```
right(12).map(double) // right(24)
left(23).map(double) // left(23)
```

### `Validation<L, A>`

* `type Validation<L, A> = Failure<L, A> | Success<L, A>`

* Zoals Either, maar accumuleert de inhoud van `L`

* Meerdere inputwaarden controleren 

* Meerdere regels toepassen op één waarde

* Heeft regels nodig omtrent accumuleren van `L`

---

## Voorbeelden uit ng-kaart

Zie (kaart-reducer.ts)[https://github.com/WegenenVerkeer/ng-kaart/blob/develop/src/lib/kaart/kaart-reducer.ts]

---


