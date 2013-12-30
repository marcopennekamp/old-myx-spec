# myx core
<span style="font-size: 8pt;">myx is a language and platform by <a href="https://twitter.com/marcopennekamp">@marcopennekamp</a></span>

<div style="font-size: 8pt;">
<p style="height: 0.6em;"><b>Contributors</b></p>
<p>Marco Pennekamp<br />
Philipp Schröer
</p>

</div>

#### TODO

* Specify variable capturing semantics of *closures*.
* Type Binding: `protected` qualifier?
* A qualifier that signals the intended overshadowing of an element in a higher scope by a newly declared element (e.g. `hiding`).


--------------------------------


## Types

### Declaration

A type declaration in myx has the following form:

    Qualifiers Name = Type

Where `Type` is 

* An already existing type, addressed by its identifier (Types:Derivation).
* A type defined with a kind.

Type names have to start with an upper-case letter to distinguish types from variables.


### Derivation

A new type can derive from an already existing type.

    Id = Size

In the example above, `Id` is a new type, but derives from `Size`. This means it inherits all elements defined for Size, but new elements (for example methods) can be added to the type. The derived type can be converted to the deriving type implicitly (`Size` to `Id`), but not vice versa.


### Default Value

A type can have a default value, which is used when a value is expected, but none was explicitly supplied, such as it is the case with (mutable) variable declarations and return values.

A type's default value has to be declared explicitly, unless the kind specifies a procedure to generate a default value (e.g. tuples). You can add the default value to the type package, its name is always `default`.

    Number = { 0..1000 }
    package Number
        default = 0

Since default is a reserved variable name in type packages, the variable's type can be inferred. 


### Kinds

A type can be defined by using a kind. A kind is the type of a type and is defined outside the source code, but kinds can still be added by the programmer. Without going into the definition of kinds, the syntax to define a type with a kind is the following:

    KindName (Parameter, ...) Body

Where `KindName` is the name of the kind and `Body` consists of code that complies to rules defined by the kind. A type definition with a kind accepts parameters (in the example `Parameter`) separated by commas. Kinds must start with an upper-case letter. 

Sets, tuples and functions are the most common *kinds* and defined in the core specification, as they feature a special syntax.


### Sets

A myx set is a set of values that are optionally labeled with identifiers. Labeling a value is equivalent to adding it to the type package.

    StackSize = set
        min = 0, 1..19, max = 20

Should an identifier be present the value is optional. In that case the value is an increment of the value left of the current value or, if the element is the first one in the set, the default value of the element type. The default element type is `Int`. This allows to define "enumerations".

    Weekday = set
        mon, tue, wed, thu, fri, sat, sun

A set must have an element type, which can be specified with the following syntax:

    set (Type)

Where `Type` is a type identifier or type definition, as explained in Types:Declaration.
The set elements may also be delimited by newlines instead of commas. These two ways may be mixed.

A set can also be written between braces:

    Weekday = { mon, tue, wed, thu, fri, sat, sun }


### Tuples
    
Tuples combine types to form a new type. Each field in a tuple has an optional name and a type and always has the mutability properties of the variable that holds the tuple. 

    Vector = tuple
        Int x
        Int y
        Int z

A short notation is available:

	Vector = (Int x, Int y, Int z)

Accessing a named field can be done by using the dot/access operator.

    vec.x
    vec.y

To access an unnamed (or named) field the subscript operator can be used with an index (starting with 0).

    vec[0]

A tuple has default values for the types that have a specified default value. These can be changed:

    Answer = (String responder = "Deep Thought", String message = "42")

    Answer actual = default
    Answer alternate = (message: "I am a supercomputer!")

You can also specify a default value by changing the `default` variable bound to the tuple type.

0-tuple types can not be used as a variable or field type and have no default value.


### Functions

A function is a sequence of statements and declarations that are executed to map an input to an output.

A function has one tuple as a parameter and one tuple as a return type. The 0-tuple is the `void` type and declares that either no parameters are declared or that nothing is returned. If the parameter or return type is omitted, the 0-tuple is assumed. For convenience, we talk about parameters, although the elements of the parameter tuple are meant. Likewise, we refer to the fields of the return tuple as return values. All parameters are immutable while return values are mutable. The name of each tuple field is optional. Fields of the returned tuple can be addressed in the code, thus allowing to partially assign the returned values (or only setting a few while having the rest of the fields keep their default values).

    (Int a, Int b) -> (Int x, Int y)

Implicit conversions allow us to convert a simple value to a 1-tuple and vice versa, so a function may just take one non-tuple type or return one non-tuple type:

    Int x -> Int




## Variables

### Declaration

A variable declaration in myx has the following form:

    Qualifiers (Type Name | Name Type) = Expression

In order to make variable declarations distinguishable from variable assignments, at least one qualifier (`Qualifiers`) or `Type` have to be specified. If `Type` is not specified, the type of the variable is inferred.  `Type` may stand before or after the name. The name of the variable is specified by `Name`, it is mandatory and  the first letter must be lower-case. 


### Mutability

Variables are immutable by default and have to be qualified with the `mut` qualifier to be mutable. The mutability might affect a type in a different way than a simple mutability/immutability distinction, which is specified in each kind if necessary.

An immutable variable's value can not be changed. Mutable variables may be declared without a direct assignment, in which case the default value of the type will be assigned to the variable.




## Statements

Statements are used to calculate values and affect the control flow. They make up function bodies, but can also be used outside of functions, for example expressions that are used to initialize a variable declared outside a function. There are three types of statements: expressions, commands and blocks.

### Expressions

Expressions are statements that calculate a value. Loop expressions do not yield the value of whatever is calculated but a possibly empty list of values. Values and variables are also treated as expressions.


### Commands

Commands are statements that yield the 0-tuple. Some commands are only valid in blocks associated with other expressions and commands, such as `break` and `continue`, which are only valid in loops.


### Block

A (statement) block consists of one or more indented statements. A block might return a value, in which case it yields the value of its last statement, or simply the 0-tuple.




## Expressions

### If-Else

The if-else expression executes one of two blocks based on a boolean expression. 

    if flag then 1 else 0

    if flag
        1
    else
        0

If no expression yield is expected, the `else` branch can be omitted. Otherwise, it can be omitted as long as a default value is specified for the expected type.


### While

The `while` loop expression executes a block as long as a condition is true.

    while flag do something ()

    mut running = true
    while running
        step ()
        running = keepRunning ", Damien!"


### Operators

There are a few operators in myx that are predefined for some types and can be defined for other types (e.g. tuple types). 




## Commands

### Return

`return` immediately ends the function and optionally returns values. The return semantics in myx are discussed in the Types:Function section.

    func add (Int a, Int b) -> Int = 
        return a + b


### Assert

`assert` is a command that evaluates a boolean expression and exits the program when the expression is false, accompanied by an error message showing the file and line of the failed assertion. Basically, it can be used to assert specific states that always should be true. When producing release code, the compiler usually strips out assertions. Assertions are evaluated at compile-time if possible, otherwise at run-time. The intent of assertions is not to provide run-time error handling, but to aid the programmer in finding errors. 

    Int a = 0
    assert a == 0 




## Qualifiers




## Scopes

The scope of an element is the section of source code where exactly this element is declared. We can likewise define the scope of a section as the set of elements that are declared in this section of code.

A piece of code can exist in multiple scopes. For example, declaring an element in a package adds it to the global scope and to the file scope. A statement in a function exists in the global, the file and the local block scope(s). The lowest scope always overshadows the higher scopes. This means that if an identifier is used in multiple scopes, the element that can be found with that identifier in the lowest scope possible is used.

The following table lists the scopes from high to low.

<table>
<tr><th>Name</th><th>Element is visible in</th></tr>
<tr><td>Global scope</td><td>Whole program</td></tr>
<tr><td>Package/Type scope</td><td><i>internal:</i> Package and sub-packages</td></tr>
<tr><td>File scope</td><td>Current file</td></tr>
<tr><td>Local scopes</td><td>Current block and child blocks</td></tr>
</table>

Some statements and elements have special scoping rules for child members (for example functions with parameter and result tuples).




## Packages

Packages are used to divide elements, making it possible for two or more elements in different packages to share a common name. You can use the `package` declaration to create a package, which is only possible in the file scope or inside other package declarations.

    package collection
        visible List = /* ... */

The package name must start with a lowercase letter and may consist of any letter and number afterwards, but preferably lowercase letters. You can create sub-packages by separating the package names with dots or using a `package` declaration inside another one.

    package util.math
        visible func abs (Int value) -> Int = if value < 0 then -value else value

To use an element declared within a package, you have to address it by its full name (name and package prefixes).

    util.math.abs (-1)

Elements in the same package or sub-package(s) do not need to use their shared package prefix, unless there is an ambiguity, in which case a compile-time error is thrown.

    package test.a
        visible val constant = 12345
    
    package test.b
        visible val other = a.constant * 54321

You can use a `using` declaration to make package prefixes partially or fully obsolete, which only applies to the scope it is used in. If used in the middle of a scope, only the part **after** the using declaration is affected. 

    func one -> Int = 
        using util.math
        abs (-1)

Sub-packages may not share a name with any element declared in the package. If there is a naming conflict between a package and a variable that can not be resolved by the compiler (e.g. a package and a tuple type variable with the same element/field name), a compilation error is thrown.

To be visible outside the file it is declared in, an element must be a member of a package or sub-package. An element that is declared `visible` is added to the global scope, while an element declared with `internal` is placed in the package scope of the package it was declared in. This package scope can only be seen in the package or sub-packages.

Elements are internal by default.


### Types as Packages

Elements can be put in the namespace and possibly scope (in case of an `internal` declaration) of a type with the same semantics as packages. Only `using` can not be used with types. Elements in the type package can be seen by other elements of the type and sub-types, and methods bound to the type and sub-types.

    package Type
        visible val a = 12

Instead of using `package`, you may declare and add an element directly to the package, with the type standing before its name and separated by a dot.

    visible val Type.a = 12




## Methods

A method is a function that is bound to a type. 


### Binding

Methods may be *bound* to a type by using `bind`. Every function in its block becomes a method of the type.

    bind Type
        public func add (Int b) -> Int = a + b

`bind` can be used multiple times with the same type in possibly different files. 

Methods bound to a type may be declared `public` or `private`. They are `private` by default. A private method can only be seen by other methods bound to the type or methods bound to a type that derives the type. A public method is visible in all scopes.






