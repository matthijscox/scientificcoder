---
title: "Julia Type Annotations"
datePublished: Tue Jul 16 2024 08:19:04 GMT+0000 (Coordinated Universal Time)
cuid: clyo56pap00030alceq1ndbya
slug: julia-type-annotations
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1721115946719/caea6f6c-497a-410a-816e-1624c9174180.png
tags: julia, types, type-annotation

---

The Julia language allows type annotation in multiple ways, with different behaviors, in order to improve performance and readability of the code. Types annotations always use the `::` syntax, for example in function declarations such as `f(variable::Integer)` . When you are new to Julia, it might not be clear what the possible type annotations are, nor what their expected behavior is.

The behavior of type annotation is well-documented, but a little scattered across the Julia manual. So I decided to write an overview here. In short, these are the kinds of type annotation I know of:

1. Field declarations in composite types:
    
    ```julia
    struct MyType
        field::String
    end
    ```
    
2. Method definitions may contain type annotation:
    
    ```julia
    my_function(input::Integer) = input + 1
    ```
    
3. Type assertion of variables
    
    ```julia
    x = my_function(5)::String
    ```
    
4. Automatic type conversion
    
    ```julia
    x::Int8 = 5
    # or as function output type
    function(input)::String = input + 1
    ```
    

Additionally, we could add a fifth, overarching function of type annotation:

5. Documentation and code clarity
    

While this is not strictly a technical behavior, code clarity can be a crucial reason to annotate types in any programming language.

This blog post is inspired by:

* [Julia Discourse On type annotations](https://discourse.julialang.org/t/on-type-annotations/116305)
    
* [Julia Discourse on Annotating Types: Best practice for beginners](https://discourse.julialang.org/t/annotating-types-best-practice-for-beginners/50521/2)
    

## Composite types

You can define your own types in Julia very easily with the `struct` declaration. This is well documented in the [manual](https://docs.julialang.org/en/v1/manual/types/#Composite-Types). You could do this without any type annotation if you want to:

```julia
struct MyType
    x
    y
end
```

However, this means the fields `x` and `y` can be any type, and the Julia compiler cannot optimize the memory layout for your type. Ideally the memory size of your type is known at compile time, and it's continuous in memory (all bytes are subsequently behind each other in memory). A better memory layout will in turn lead to faster data accessing of your type's fields.

The better practice is therefore to add the types of the fields (if you know them), and it's best if these field types are also *concrete*. That means they are not abstract types or unions. Then the compiler can better optimize your code. Here's an example:

```julia
struct MyConcreteType
    x::Int64
    y::String
end
```

You can also create [*parametric* types](https://docs.julialang.org/en/v1/manual/types/#Parametric-Types), which allows flexibility in the used type definition, yet still allows you to create a concrete parametric object when it's constructed.

## Method definitions

A method in Julia is simply a specific definition of a function, where every method has a different set of input types. Type annotations are used to define a method:

```julia
# a method of `f` with floats
f(x::Float64, y::Float64) = 2x + y
# a method of `f` with strings
f(x::String, y::String) = x * y
```

The Julia manual extensively discusses [methods and their behavior](https://docs.julialang.org/en/v1/manual/methods/#Methods).

You could use the method definition as some kind of type assertion, because if a method does not exist, it will throw an error. But this is not guaranteed, because there might be a generic method defined, for example for `Any` input type, and then that function will be called. For the example above, where we have only 2 methods defined for `f`, we can throw an error for integer input:

```julia
julia> f
f (generic function with 2 methods)

julia> f(1,2)
ERROR: MethodError: no method matching f(::Int64, ::Int64)
```

So this might be considered a kind of type assertion. But we can also define a generic function and then everything will work:

```julia
julia> f(x, y) = "f will always work now"
f (generic function with 3 methods)

julia> f(1,2)
"f will always work now"
```

If you want to use the method definitions themselves as a kind of type assertion for your own functions, you'll have to be careful to not declare a method that's very generic. Yet you will probably want your methods to work for a variety of input types, and not be too specific. Finding this balance is an art in Julia.

## Type assertion

Type assertion means that your code will fail if you encounter the wrong type. This helps you check that code works as expected and/or helps inform other developers what type is expected in that piece of code.

In Julia you can trigger such assertion by annotation at the right hand side of an annotation, on the REPL or inside a function.

```julia
function my_assertion(x, y)
    z = f(x, y)::Float64
    return z
end
```

Using the function `f` from the previous section on method definitions we have the following behavior:

```julia
julia> my_assertion(1.0,2.0)
4.0

julia> my_assertion("a", "b")
ERROR: TypeError: in typeassert, expected Float64, got a value of type String
```

You can also annotate directly on the REPL in recent Julia versions:

```julia
julia> x = 5::Int64
5

julia> x = 5::String
ERROR: TypeError: in typeassert, expected String, got a value of type Int64
```

## Type conversion

There's a tricky difference in Julia whether you place your type annotation on the left or right hand side of the assignment. As explained in the previous section, we get type assertion on the right hand side. But automatic type conversion happens when it's on the left hand side:

```julia
julia> x = 5::Int8 # type assertion
ERROR: TypeError: in typeassert, expected Int8, got a value of type Int64

julia> x::Int8 = 5 # type conversion that succeeds
5

julia> x = "a" # note: the type of x is remembered now
ERROR: MethodError: Cannot `convert` an object of type String to an object of type Int8

julia> foo::String = 5 # type conversion that fails
ERROR: MethodError: Cannot `convert` an object of type Int64 to an object of type String
```

This annotation will simply call `convert` , so `x::Int8 = 5` is equivalent to `x = convert(Int8, 5)` , except that it also seems to remember the type of `x`.

Type conversion is very handy behavior, but may be less expected by new Julia developers. So be careful with these annotations.

Type conversion also happens automatically when you declare the output type of a function:

```julia
function convert_to_int8(x)::Int8
    return x
end
```

Similar to the previous behavior, this will convert anything to `Int8` unless it cannot find a `convert` function:

```julia
julia> convert_to_int8(5) |> typeof
Int8

julia> convert_to_int8("a")
ERROR: MethodError: Cannot `convert` an object of type String to an object of type Int8
```

This means that if you want your code to truly assert your output variable, instead of convert, you need to do this in the return statement:

```julia
function assert_int8(x)
    return x::Int8
end
```

Automatic type conversion also happens on the default struct constructor. So for the type we defined previously this would work, even though we specified `Int64` as type of the first field:

```julia
julia> obj = MyConcreteType(Int8(5), "a") # this will convert the Int8
MyConcreteType(5, "a")

julia> typeof(obj.x) # see it's an Int64 now, not Int8
Int64

julia> MyConcreteType("a", "a") # only fail if conversion is not possible
ERROR: MethodError: Cannot `convert` an object of type String to an object of type Int64
```

I've heard the type assertion might add some runtime overhead, while type conversion can often be compiled away. I haven't personally investigated this yet.

## Assertion vs Conversion

A quick overview between assertion and conversion of types, because I often forget about these differences.

```julia
# assertion, in right-hand definitions:
x = 5::Int
f(x) = 5x::Int
x = f(5::Int)
# conversion, in left-hand definitions:
x::Int = 5
f(x)::Int = 5x

# not to be confused with type dispatching in method definitions:
f(x::Int) = 5x
```

Note that I also added the assertion example that may happen inside a function call like `x = f(5::Int)`, which I didn't discuss yet. And it's good to remember this is distinct from type annotation in the actual method definition.

## Type annotation done wrong?

I think there are two cases of "overengineered type annotation":

* too many annotations
    
* too strict annotations
    

You can go overboard and annotate everything in your code, like for example:

```julia
function f(x::Int64, y::T)::Int64 where T<:Real
    z::Int64 = Int64(y::T)::Int64
    result::Int64 = x::Int64 + z::Int64
    return result::Int64
end
```

This is a bit extreme and redundant, it doesn't even help readability, and probably just adds overhead for the compiler to remove all these type annotations.

You can also make your types too strict, especially in method definitions, for example:

```julia
f(x::Int64, y::Int64) = x + y
# while you can be more generic:
f(x::Real, y::Real) = x + y
```

A general heuristic some people follow is that it's good to keep your composite types as concrete as possible, while keeping your methods as abstract as possible.

## Conclusion

There seem to be at least 4 different behaviors of type annotation, with documentation as a 5th reason to add type annotation. I have given a short overview of all of these in this blog post, which may help you compare all different type annotations side by side.