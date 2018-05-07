# functioneel programmeren met fp-ts


---

## Functioneel programmeren

---

### De fundamenten

@ul

- Werken met functies als met andere types

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

* functie als waarde

```
function double1(n: number): number {
  return 2 * n;
}

const double2: (n: number) => number = n => 2 * n
```
@[1-3]
@[5]

+++

### Spelen met argumenten: currying

* Zonder currying

```
function sum(x: number, y: number): number {
  return x + y;
}

const s1 = sum(1, 2);
```
@[1-3]
@[5]

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
@[1-5]
@[7]

+++

### Spelen met argumenten: currying

* Met currying, variabelestijl

```
const sum3: (x: number) 
  => (y: number) => number = x => y => x + y

const s3 = sum3(1)(2);
```
@[1-2]
@[4]

+++

### Voorbeeld van higher order function

```
const format = (n: number, s: string) => `${n} ${s}`;

format(5, "EUR");    // 5,00€
revFormat("EUR", 5); // 5,00€

const swapper: <A, B, C>(f: (a: A, b: B) => C) => 
                         ((b: B, a: A) => C) 
                        = f => (b, a) => f(a, b);

const revFormat = swapper(format);
```
@[1-4]
@[10]
@[6-8]
@[6-10]

---

## Fp-ts

+++

### Intro

* Implementatie van concepten uit theorie rond FP

* Puur TypeScript

* Uitgebreid

+++

### Handige datatypes

+++

### Null & undefined

* T. Hoare: `null` = _1 billion dollar mistake_

* JavaScript -> `undefined`

* typisch gevolg => run time errors & omslachtige code

+++

#### Remediëring in TS

```
const unsafeQuote(x: string): string = `'${x}'`;

function quote(x: string): string {
  if ( x !== null && x !== undefined ) {
    return unsafeQuote(x);
  } else {
    return "";
  }
}

const quote: (x: string) = x => !!x ? unsafeQuote(x) : "";
```

@ul

* Wat met nesting?

* Wat met chaining?

@ulend

+++

#### `Option<A>` datatype

* `type Option<A> = None<A> | Some<A>`

* Kan ofwel een waarde bevatten ofwel niet

```
const m1: Option<number> = some(1);
const m2: Option<string> = some("Haskell");
const m3: Option<number> = none;
const m4: Option<string> = none;
```

@[1]
@[2]
@[3-4]

+++

```
const quote: (ms: Option<string>) => Option<string> = 
  ms => ms.map(unsafeQuote);

const c1 = quote(some("Curry"));
const c2 = quote(none);
```

@[1-2]
@[4-5]

+++

#### Een rijke set aan operaties

* transformeer: `map`

```
none.map(unsafeQuote); // none
some("Frege").map(unsafeQuote); // some("'Frege'")

```

+++

#### Een rijke set aan operaties

* selecteer: `filter`

```
const nonEmpty = (s: string) => s.length > 0;

none.filter(nonEmpty); // none
some("AWV").filter(nonEmpty); // some("AWV")
some("").filter(nonEmpty); // none
```
 
+++

#### Een rijke set aan operaties

* combineer: `chain`

```
const asNonEmpty = (s: string) => s.length > 0 ? some(s) : none;
const lookUp: (id: string) => Option<Laag> = ...;
nonEmpty("").chain(lookUp);
```

+++

#### Interoperabiliteit

* Van JS/TS naar Option: `fromNullable`, `fromPredicate`

```
const nonEmpty 
  = (s: string) => s.length > 0;
const asNonEmpty2 
  = (s: string) => fromNullable(s).filter(nonEmpty);
const asNonEmpty3 
  = (s: string) => fromPredicate(nonEmpty)(s);
const asNonEmpty4 
  = fromPredicate(nonEmpty);
const asNonEmpty5 
  = pipe(fromNullable, filter(nonEmpty));
```

+++

#### Interoperabiliteit

* Van Option naar JS/TS: `toNullable`, `toUndefined`, `getOrElse`, `getOrElseValue`, `fold`

```
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

Zie [kaart-reducer.ts](https://github.com/WegenenVerkeer/ng-kaart/blob/develop/src/lib/kaart/kaart-reducer.ts)

---


