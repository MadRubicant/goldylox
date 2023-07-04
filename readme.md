# Rusty Lock

Personal implementation of the book [Crafting Interpreters](https://craftinginterpreters.com/), except I'm taking a few liberties with the language for my personal sensibilities.

# Language Definition
Current as of 2023-07-03 (aka when I started this)

## Typing
Dynamic typing, but if I stick with this to the point of having a real language I'll statically type it. After that I get to write generics!

## Fundamental Types
* Booleans. You kinda need these. `true` and `false` and all that
* Integers. 64-bit signed ints for now, if I ever get around to static typing I'll add the other arities and signedness
* Floating point. Double precision/64-bit floats. 
* Strings. As amusing as writing IO without strings would be they're also kinda necessary. Format strings are a stretch goal
* `never`. Writing a null-free language on my first try sounds extraordinarily difficult, so I'm just gonna name it `never` and promote it to bottom type once I switch to static typing
* Arrays. Making this work like lisp and forcing myself to use cons cells for everything would be amusing but not terribly practical. Also it'll probably make string implementations easier

## Expressions
I'll mostly be following the book for these, except that I'm personally a fan of Everything Is An Expression so I'll be attempting that
### Operators
Basic arithmetic operators, of course
- Addition, in the form of `left + right`
- Subtraction, in the form of `left - right` 
  - Unary negation, in the form `-number`
- Multiplication, in the form `left * right`
- Division, in the form `left / right`
- Bitwise operators as stretch goals

Comparison and logical operators
- `<`, `>`, `<=`, `>=`, `==`, `!=`
  - They work how you'd expect
- `&&` and `||`
  - short-circuiting
- Unary not with `!`

Grouping operators: `(` and `)`. Obviously necessary for any kind of interesting math/logic

Subscript operators: `[` and `]` - I'm not sold on these yet!

The dot operator `.`
Used for field lookup on objects

### Operator precedence
From the highest to lowest
- Parens bind tighter than anything
- Next are `.`, and `[]` if I implement it
- Unary `!` and `-`. These need to bind above logical operators (so that they're finished evaluating once we eval those) but below parens and lookup operators
- Binary logical operators
- Comparison operators
- Multiplication and division
- Addition and subtraction. I'd like to make these all the same for elegance reasons but that'd be a massive footgun so I won't

### Never
Since I'll be attempting to have every expression but sometimes there's nothing meaningful to return (`void`), `never` will be pulling double-duty until I can make this null-free and statically typed. Until then, `never` will be both `null` and `void`. I'm sure this will have no long-term repercussions whatsoever for this language when it takes off in my thirties

## Statements, or, Expressions with Effects
### Variables
Variables come in two flavors, mutable and immutable. 

Immutable variables are declared with `const` and it's an error to fail to assign to one or reassign to one
```
const someNumber = 13
const str = "Wow the first string written in rusty-lock!"
const needsAssignment // Error!
someNumber = 99 // Error!
```
I'd like to allow immutable variable *re*declaration, but I'm not sure whether that's a good idea
```
const number = 13
const added = number + 5
const number = added / 2
```

Mutable variables are declared with `var`, can be reassigned, but otherwise work identically to mutable variables
```
var number = 14
number = 15 // Completely fine
number = number / 2
```

### Control Flow
We've got `if`, `else if` and `else`. 
We've got `while`
And finally we've got `for (const name in sequence)`. None of that 3-statement `for(;;)` from C here. It can be emulated with while loops until I properly implement for-in loops.

### Functions
Function calls are the same as lox: `functionCall(arg1, arg2, arg3)`
