---
title: "Fruity Composable Design Patterns in Julia"
datePublished: Fri Jun 23 2023 12:18:08 GMT+0000 (Coordinated Universal Time)
cuid: clj8jfrsg000909l5ghan1rld
slug: fruity-composable-design-patterns-in-julia
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1687522411556/c781ad27-554c-48bd-b6fc-f42d5f629591.png
tags: design-patterns, software-development, coding, julia, factory-design-pattern

---

A design pattern is a repeatable solution to a common coding problem. Design patterns are not something beginner programmers typically think about a lot (that includes most scientists), they are probably focused on making their code work. At least that's what I did when I was a young programmer. At the other extreme such patterns can become a religion for people, where everything has to be a design pattern, or else the code is not considered good enough. However, people who make this mistake are not senior programmers either in my opinion. Senior programmers look for a balance between pure abstraction and simplicity (and many other requirements).

The Julia community has a special standing on design patterns: people don't really like them. In general the Julia community believes that design patterns expose a mistake in the language, because we should be able to automate any pattern away. I like that philosophy and I prefer not to focus on design patterns too much, but it's inevitable to encounter them while coding. Even if you do not consciously write design patterns, you may accidentally use them. For example I've used the Factory Method design pattern multiple times, specifically one that takes strings as input and outputs types/classes. This is quite a typical pattern to find in Python as well.

Therefore it's still valuable to think about design patterns. You can see them as best practices that you can learn from. Or you can see them as fun little puzzles, where you take some code out of context and ask "what is the best way to code X?".

## Composable Factory Method

Let's write a short example with fruits. Don't ask me why, but sometimes you get strings on the input from a user, or another data source, and you want to turn those into specific (factory) types for your internal code. These "factory types" can later be used to create something else. To be honest, I'm not focusing this article on the entire factory pattern, but only on a composable way to retrieve the type from a string. This also relates to a question about [enums as types](https://discourse.julialang.org/t/style-recommendation-for-enum-as-type/46078/4). Maybe this part of the pattern actually has another name? Who cares, I want to do the following:

```julia
module NaiveFruitFactory
    abstract type Fruit end
    struct Apple <: Fruit end
    struct Orange <: Fruit end

    function fruit(str::String)
        if str == "apple"
            result = Apple()
        elseif str == "orange"
            result = Orange()
        else
            error("Unknown fruit $str")
        end
        return result
    end
end
```

This works fine, right. I can turn a fruity string into a fruit type now.

```julia
julia> NaiveFruitFactory.fruit("apple")
Main.NaiveFruitFactory.Apple()
```

In this naïve example the value of the string makes the pattern especially difficult to extend by an outside user, you have to go into the module and add another `ifelse` statement. By the way there is a reason to avoid this factory pattern at all, because the code is type unstable, the output type cannot be predicted by the compiler from the input type. There are many reasons to avoid this factory pattern, but as I said, sometimes it's unavoidable. However, I am looking for a better alternative that is still readable and performant, yet also easily extendable. I know, software engineering always involves the most insane requirements.

I've read the book [Hands-On Design Patterns and Best Practices with Julia](https://www.google.com/search?rlz=1C1GCEU_enDE853DE853&q=Hands-On+Design+Patterns+and+Best+Practices+with+Julia:+Proven+Solutions+to+Common+Problems+in+Software+Design+for+Julia+1.x&si=AMnBZoEZ8aFftZu792frFYrnK9KQYGXRL3UTeDeHB9-uc0sfFeepDAVw_FxX4OtyqI1BQ8YRZRbli_Bwn0DaOA9TunvXQVZADtby617YcRQUdTbvWm-huT4HDHGkx6_eBkRdjtYN44bIvizA7J6wc7xex-HzqalhDEQCmQA6WtA4Wj_fzpnT2B9bMrGNq4mFuF2bZOJhajW_dAZ7gsn_q1oaG3JOw8Pi65BQuCMHx2UNnQpB0rsm-c9xgB0FyEyycCzoi2-CZco41_KXppcywjbX7puLwFCq-jdwADM3csPsF7xEQtANRS8f6hHav7e7vKHfZuf79NIIx52-t6nBMa9J_3KNgc0g_W6XGp44j5RrIoVS-qh7plg%3D&sa=X&ved=2ahUKEwiggciJgbb-AhVaxAIHHZrICI0QmxMoAHoECA8QAg) from Tom Kwong again for reference. The factory pattern in his Creational Patterns chapter is not exactly what I am looking for, as it doesn't use strings as input. His output factory depends on the input type (not the value), which is more preferable. His example is a formatter used for printing certain types in different ways:

```julia
abstract type Formatter end
struct IntegerFormatter <: Formatter end
struct FloatFormatter <: Formatter end

formatter(::Type{T}) where {T <: Integer} = IntegerFormatter()
formatter(::Type{T}) where {T <: AbstractFloat} = FloatFormatter()
formatter(::Type{T}) where T = error("No formatter defined for type $T")
```

So maybe we should have a separate name for a "type-based factory method" and a "value-based factory method"?

I have three options for a composable "value-based factory method" (please leave a comment if you see a better option):

* Interactive subtype looping (don't do this!)
    
* Registration mechanism
    
* Value-based dispatching
    

The first one I considered long ago, is simply to loop over the `subtypes` of the abstract type. I'll show this was a performance mistake. The fact that you need to import `InteractiveUtils.jl` in your code is always a big warning sign.

We can do one with a collection like a dictionary and a `register!` function, but I personally prefer one with automatic registration/subscription of the new type. This pattern is probably something you'd do in Python.

Finally, we can do a `Val` dispatch, it's a bit slower than the if-else/switch statement. This is what we can use if performance isn't a main issue, like on a public interface function. You may want to reconsider in a deep inner loop that is performance critical for your code.

Let's get into the details.

## Subtype Looping

I will show a very straightforward solution, that's very difficult for the compiler. I am showing this approach, because I made this mistake once. Here's the code. It's very similar to the naïve example, except now we ask every type of fruit to provide a `fruitname` function and we loop over `subtypes(Fruit)` until we find the string.

```julia
module SubtypeFruitFactory
    import InteractiveUtils: subtypes

    abstract type Fruit end
    struct Apple <: Fruit end
    struct Orange <: Fruit end

    fruitname(::Type{Apple}) = "apple"
    fruitname(::Type{Orange}) = "orange"

    function fruit(str::String)
        for type in subtypes(Fruit)
            if str == fruitname(type)
                return type()
            end
        end
        error("Unknown fruit $str")
    end
end
```

The benefit is that I can let anyone extend this module with their own fruit types with very little code:

```julia
module SubtypeFruitExtension
    import ..SubtypeFruitFactory
    struct Banana <: SubtypeFruitFactory.Fruit end
    SubtypeFruitFactory.fruitname(::Type{Banana}) = "banana"
end
```

It works fine, but the catch is that `subtypes` is an interpreted runtime function, it cannot be compiled at all, because at any moment a new Fruit subtype can be added. You can see the drastic difference in timing on my computer (I don't even have to do proper benchmarking):

```julia
julia> @time NaiveFruitFactory.fruit("orange");
  0.000002 seconds

julia> @time SubtypeFruitFactory.fruit("orange");
  0.014683 seconds (1.01 k allocations: 814.500 KiB)
```

So let's avoid this one, shall we?

## Registration Mechanism

Another straightforward approach. Instead of hardcoding the names of the types that we want to check, we store them in a mutable collection, like a dictionary.

```julia
module RegisterFruitFactory

    abstract type Fruit end

    const FRUIT_MAP = Dict{String, DataType}()

    function register!(fruit::Type{<:Fruit}, name::String)
        FRUIT_MAP[name] = fruit
    end

    struct Apple <: Fruit end
    register!(Apple, "apple")
    struct Orange <: Fruit end
    register!(Orange, "orange")

    function fruit(str::String)
        fruit_type = get(FRUIT_MAP, str, nothing)
        if isnothing(fruit_type)
            error("Unknown fruit $str")
        else
            return fruit_type()
        end
    end
end
```

Similar to the previous example, we can easily extend this one:

```julia
module RegisterFruitExtension
    import ..RegisterFruitFactory
    struct Banana <: RegisterFruitFactory.Fruit end
    RegisterFruitFactory.register!(Banana, "banana")
end
```

Performance is good in my opinion, though slower than the hardcoded if-else statement in the start, due to the dictionary. Let's check the minimum time with `BenchmarkTools.jl` . (And we always have to be careful that we are not looking at [compiler optimizations](https://juliaci.github.io/BenchmarkTools.jl/stable/manual/#Understanding-compiler-optimizations).)

```julia
julia> using BenchmarkTools

julia> @btime NaiveFruitFactory.fruit($"orange");
  10.911 ns (0 allocations: 0 bytes)

julia> @btime RegisterFruitFactory.fruit($"orange");
  146.007 ns (0 allocations: 0 bytes)
```

Looks okay. Downside is that we are using a global variable in a module to store the registered types. We may have to put locks around that for multi-threading purposes. That would be a topic for another blog post.

## Value-based Dispatching

Let's have a swing at another Julia solution. In Julia it is possible to dispatch on values, by wrapping them into parametric `Val{}` types. Note that this works only for plain data types, for example check `isbitstype(Int64)`. Strings are mutable arrays of characters, so they are not allowed as parametric values. However, we can first convert them to symbols and then dispatch on those. Let's have a look at the implementation.

```julia
module ValueFruitFactory
    abstract type Fruit end
    struct Apple <: Fruit end
    struct Orange <: Fruit end

    fruit(str::String) = fruit(Symbol(str))
    fruit(sym::Symbol) = fruit(Val(sym))
    fruit(::Val{:apple}) = Apple()
    fruit(::Val{:orange}) = Orange()

    # default error
    fruit(::Val{T}) where T = error("Unknown fruit $T")
end
```

The smallest implementation so far! And as always the extension package is 3 lines of code:

```julia
module ValueFruitExtension
    import ..ValueFruitFactory
    struct Banana <: ValueFruitFactory.Fruit end
    ValueFruitFactory.fruit(::Val{:banana}) = Banana()
end
```

How are we doing in performance?

```julia
julia> @btime ValueFruitFactory.fruit($"orange");
  236.941 ns (0 allocations: 0 bytes)
```

Slightly slower than the registration method with a dictionary, but significantly more pleasing to read in my opinion.

## Conclusion

In summary, I wanted this behavior in a simple, yet performing manner:

```julia
using ValueFruitFactory
fruit("apple") == Apple()
fruit("orange") == Orange()
fruit("banana") # throws error

using SomeFruitExtension
fruit("banana") == Banana()
```

(I am ignoring namespaces for a moment here, but we can always `export` those symbols in Julia.)

In the end, a simple switch statement (an if-elseif-...-elseif) is best for performance when you want to construct types from values, such as strings. But that means you cannot extend the constructor with another type, because it's hardcoded in the switch statement. If you want a decently performing, composable solution that is pleasant to read, then the value-based dispatching seems to be the way to go.

I should probably wrap up with a final conclusion about design patterns. First of all, solving little puzzles is fun and when you enjoy your work, you generally do better, so please tinker with design patterns if you find them fun. Next to that it's a matter of balancing the requirements of your code, look for what works best in your case, while keeping less obvious non-functional requirements in mind, such as readability, decent performance and composability. With that pragmatic mindset you can look at design patterns for inspiration.