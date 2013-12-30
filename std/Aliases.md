## Aliases

A temporal variable that is qualified with `alias` can act as an alias for another variable, which means that it accesses the same memory location as the original variable. The type of the alias must either be the type of the referenced variable or a reinterpretation of it. All qualifiers are retained, including mutability.

    Vehicle = /* ... */
    Car = Vehicle

    Car car = /* ... */
    alias vehicle = car as Vehicle

An alias can only be initialized with a variable, not a simple value.