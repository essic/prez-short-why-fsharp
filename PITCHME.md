### C`#` developers here's 6 reasons to have a serious look at F`#` 

---

> "Point of view is worth 80 IQ points" <br> Alan Kay

---

### What's F# ?

- Strongly, statically typed, open sourced and cross platform language with deep inference from Microsoft |
- Multi-paradigm language : functional, imperative and object-oriented |
- Heavily influenced by ML, OCaml, Haskell, C# & others |
- 1.0 appeared on 2005 ( older than GO, TypeScript, Rust ...) |

---

### Reason 1
#### No compatibility risk !

+++
Anything written in C# can be used in F# 

+++
The reverse is true for all F# features not depending on the compiler

+++
In short, same constraints as VB.NET

---

### Reason 2
#### F# has better typing 

##### Sum types & Product types 

+++
#### Product type
```csharp
public class Something {
  public int SomeNumber { get; set; }
  public string SomeSentence {get; set;}
}
```
@[2](Any value of 'int' type can be assigned here)
@[3](All value of 'string' type can be assigned here)
@[2-3](The 'Something' class, can have any combination of 'int' AND 'string' value)
@[2-3](In short 'Something' is a product type of int and string)

+++
#### Product type 

```fsharp
type Something = Ctr of int * string

type Something2 = { SomeNumber : Int ; SomeSentence : string }
```
@[1](Something is a product type, of int AND string with 'Ctr' being the constructor)
@[3](Something2 is a record - another kind of product type - )

+++
```fsharp
type Something() =
  member val SomeNumber = 0 with get, set
  member val SomeSentence = "" with get, set
```
Of course you can also make a class in F# !

+++
#### Sum type

##### A type with multiple representations 

+++
There's no Sum Type in C# however, there's polymorphism ...

+++

```csharp
public abstract class Credentials {
//...
}

public class ClassicCredentials : Credentials {
  public string User { get; set; }
  public string Password { get; set; }
}

public class TokenCredentials : Credentials {
  public string Token {get;set;}
}

public interface HasCredentials {
  Credentials GetCredentials();
} 

public class LoginSystem {
  public bool LogIn(HasCredentials subject) {
    bool hasSignedIn;
    //Imagine some code here ...
    return hasSignedIn;
  }
}

public class UserWithLoginAndPassword : HasCredentials {
  //...
}

public class UserWithToken : HasCredentials {
  //...
}
```
@[1-3](We want a base Credentials class)
@[5-8](We define a type of Credentials)
@[10-12](We define another type of Credentials)
@[14-16](We define an interface to use later on ...)
@[18-24](We have some class which can deal with anything that implements 'HasCredentials')
@[26-28,30-32](We have two kind of Users, you get the idea ...)

+++
We got a lot of code for something we do almost everyday ... <br /> Let's see what we could do with F# 

+++

```fsharp
type UserAndPassword = { Login : string ; Password : string }

type Credentials =
| Token of string 
| Classic of UserAndPassword

type User =
  { 
    Cred : Credentials
    //other stuff there
  }
  
let logInToSystem(Credentials cred) =
  let hasSignedIn = false
  //imagination time ...
  hasSignedIn
```

@[1](Nothing new here)
@[3-5](This is a sum type.)
@[3-5](You create a valid value of type 'Credentials' with 'Token' OR 'Classic' constructor)
@[7-11](No need for several implementation of User or any variation, just give it a Credential field)
@[13-17](F# gives you ways to deal with both representation of Credentials)

+++
Sum types is supported by many languages already : OCaml, TypeScript, Rust, Scala, Haskell ...

---

### Reason 3
#### F# is immutable by default. 

##### How many times have you declared some fields / properties as 'readonly' ?

+++
Far less complicated to write concurrent code in F#

+++
Declaration is initialisation <br /> This does not compile in F#, you must give it a value

```fsharp
let someVariable : Int
```

+++
In F#, you never question if the value is present but only what the value is.

+++
Also 'null' is not the default for representing the abscence of value anymore <br /> You need a value by default ? Make it explicit !

+++
F# supports 'null' for compatibility <br /> reasons with .NET however ...
- no implicit 'null' assignation is done |
- pure F# types are not nullable (still usable in C#) | 

+++

Of course if mutability is what you want ...

```fsharp
let mutable myNameIs = "@essicf37"

myNameIs <- "Slim Shady"
```
+++
Why the 'buzz' on immutability ? Please watch [@KevlinHenney presentation on NCraft](http://videos.ncrafts.io/video/276832516) for some answers.

---

### Reason 4
#### Functional paradigm first

#### `F#` supports several paradigm but is functional first 

+++
```fsharp
let sayHello name =
  printfn "Hello %s" name
```
This is a function in F# and it's strongly typed !

+++
```fsharp
//int -> int -> int
let add number1 number2 =
  number1 + number2

//int -> int
let addTwo = add 2

addTwo 4
//results is 6
```
@[1-3](A simple addition function)
@[3](No need for return, F# function always returns the last value computed)
@[5-6](We use the automatic currying of F# to create a function which will add '2')
@[8-9](We call the addTwo function)

+++
```fsharp
let add n1 n2 =
  n1 + n2

let mul n1 n2 =
  n1 * n2
  
let mulTwo = mul 2
let addTen = add 10

let mulByTwoThenAddTen = mulTwo >> addTen

mulByTwoThenAddTen 5 
//result is : 20

```
@[1-2,4-5](We define some functions)
@[7-8](We use some currying, again)
@[10](We use 'function composition' to define a function which multiply input by 2 then add 10 to the result)
@[10](This is called function composition, `>>` is offered by F#)
@[12-13](We call function mulByTwoThenAddTen)
+++

Also functional paradigm has 3 core operations (amongst others), all present in F# of course 
- Transform |
- Filter |
- Reduce |

+++
In short, it's about writing LinQ all day long :
- Transform : Select() in Linq or map in F# | 
- Filter: Where() in Linq or filter in F#|
- Reduce: Aggregate() in Linq or fold / reduce in F# |

+++
Functional paradigm implies a strong focus on data and composable functions 

---

### Reason 5
#### Pattern matching

##### Just write it as it is ...

+++
```fsharp
type LoginAndPassword { Login: string; Password : string }
type Credentials =
| Token of string
| Classic of LoginAndPassword

let logInToSystem (subject:Credentials) =
  match subject with
    | Token t -> // We do something with it
    | Classic c -> // We do something else with it 
```
@[1-4](We take back our exemple from before ...)
@[6-9](We go a little further on the 'log in' function ...)
@[7-9](That's pattern matching !)
@[7,8,2,3](We deal with the Token representation of the sum type)
@[7,9,2,4](We deal with the Classic representation of the sum type)

+++
```fsharp
let someFunc a =
  let aBigTuple = ("@essiccf37","c# developer","f# deveoper")
  let handle, skill1, skill2 = aBigTuple
  //...
  ()
```
@[1-5](Some function)
@[2](A simple tuple, also a product type btw : `string * string * string`)
@[3](That's also pattern matching !)

+++
```fsharp
let someFunc (a: string list) =
  match a with
  | [] -> printfn "List is empty !" 
  | [_; _] -> printfn "List has only 2 elements"
  | _ -> printfn "List has more than 2 elements"
```
@[1-5](A function with pattern matching on list)
@[2-3](We do that if list is empty)
@[2,4](Or we do that if list has only two elements)
@[2,5](Or for any other case that's what we do !)

+++
Pattern matching in F# allows for much more, check out [Microsoft doc on this](https://docs.microsoft.com/en-us/dotnet/fsharp/language-reference/pattern-matching)

---
### Reason 6
#### Interactivity is key !

+++
F# has a REPL call FSI for F# Interactive

+++
FSI has full Visual Studio Support for a while now, VS2017 included.

+++
REPL is useful: ask Python and Javascript developers or just watch the CScript initiative from MS ...

+++
Some useful usage :
- You can create scripts |
- You can write code to test some cases and load production DLLs to see exactly what's happening  |
- You can run some code during developmenet, load it and try it directly |

---
### What we did not talk about 

+++ 
Support on multiple IDE <br /> (VS, Atom, VsCode, Emacs, Vim) 

+++ 
F# on .NET core

+++ 
Active patterns 

+++ 
Error handling with : `Option<T>` and `Result<TSuccess,TError>` 

+++ 
Type Providers

+++ 
Asynchronous programming

+++ 
Computation expression

+++ 
[Domain Driven Design with F#](https://www.amazon.fr/Domain-Modeling-Made-Functional-Domain-Driven/dp/1680502549/ref=sr_1_fkmr0_1?ie=UTF8&qid=1531145812&sr=8-1-fkmr0&keywords=DDD+in+F%23)

+++ 
Property based testing with [FSCheck](https://fscheck.github.io/FsCheck/) or [Hedgehog](https://github.com/hedgehogqa/fsharp-hedgehog)

+++ 
F# to Javascript with [Fable.io](http://fable.io)

+++ 
Much more features, libraries and frameworks

---
### Some useful links

- Learning F# for [Fun and Profit](https://fsharpforfunandprofit.com)
- [Interactive programming with F#](https://docs.microsoft.com/en-us/dotnet/fsharp/tutorials/fsharp-interactive/)
- [Weekly news on F#](https://sergeytihon.com/category/f-weekly/)
- [F# foundation](https://fsharp.org)

---

C# is absolutely not dead and continues to evolve <br /> However having another point of view in itself can only make you better at what you do. 

---
### Thank you !

Questions ?
