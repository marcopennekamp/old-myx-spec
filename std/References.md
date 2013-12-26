## References

A reference is a type that points to a mutable variable instead of copying its value. The type it refers to is called *content type*, its value is called *content value*.

    mut Int a
    
    /* The following lines do the same. */
    val ref = Ref (a)
    Ref ref = a
    Ref[Int] ref = a

It behaves exactly like its content type with the exception of assignment and passing. Whether the reference or its content value is passed or assigned is inferred:

* If an expression is expected to yield the content value of the reference, or multiple reference layers (e.g. `Ref[Ref[T]]`), the references are resolved until the expected type can be yielded.
* If a reference to a mutable variable or field is expected and a mutable variable or field is passed, that reference is created implicitly.
* Otherwise, if a reference is expected and the expression is a reference type, it is simply passed.

A reference's content is always mutable. The variable mutability determines whether a reference can be reassigned.
    
    mut Ref ref = a
	ref = Ref (b)