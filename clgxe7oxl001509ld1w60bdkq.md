---
title: "Automate Your Code Quality In Julia"
datePublished: Wed Apr 26 2023 07:47:00 GMT+0000 (Coordinated Universal Time)
cuid: clgxe7oxl001509ld1w60bdkq
slug: automate-your-code-quality-in-julia
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1682430557530/69f045a8-635c-4ec6-b836-dbdcf7f0dd23.png
tags: tools, software-engineering, code-quality, julia

---

Code quality is a topic in Julia that I believe deserves more attention from both users and developers. The Julia language originated in academia and focused heavily on helping scientists write better code, which is going great and deserves much praise! However, to onboard more software engineers and professional organizations we're going to have to invest even further into code quality and automated code quality tools and other methods such as used in the field of "quality assurance". In this article I'll explore the current state in the Julia ecosystem.

At our workplace we have investigated the following tools and practices. I'll start from generic practices and then move on to more advanced tools.

* Package structure
    
* Unit testing with Pkg.jl
    
* Automated testing and Continuous Integration (CI)
    
* Code Coverage with Pkg.jl
    
* Documentation testing with Documenter.jl
    
* Style guides and JuliaFormatter.jl
    
* Static Code Analysis with StaticLint.jl
    
* Quality Assurance with Aqua.jl
    
* Type stability with JET.jl
    

Let's have a look at all of them.

## Packages

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1682494969545/cff83dbe-327f-419f-ae55-d3441e2115e2.png align="center")

The Julia community uses a standardized package structure and has a single package manager called [Pkg.jl](https://pkgdocs.julialang.org/v1/). There's also a single documentation system and a single testing system. This consensus alone helps tremendously with automating any workflows in your projects and organizations.

Please make sure you share professional code with others via packages. It's straightforward to adhere to the package structure. To get you started with creating well-defined (open source) packages, you can look at [PkgTemplates.jl](https://github.com/JuliaCI/PkgTemplates.jl).

When setting up my first open source Julia package, I enjoyed the documentation of the [JuliaTemplateRepo.jl](https://bjack205.github.io/JuliaTemplateRepo.jl/dev/index.html), which goes through all the basic steps and configurations for a Julia package.

## Unit Testing

Nowadays unit testing is a common practice in professional software engineering. Developers in Julia should be no exception. Fortunately, according to [Viral Instruction](https://viralinstruction.com/posts/goodjulia/#strong_ecosystem_tooling_consensus), 89% of all open source Julia packages have tests, including a lot of beginner packages. It's safe to say that the Julia community puts a lot of emphasis on testing, which I think is remarkable for a language that originated in academia. This really sets a good example.

All unit testing use the [Test.jl](https://docs.julialang.org/en/v1/stdlib/Test/) package, which is shipped with the base language. There are some extensions like [TestSetExtensions.jl](https://github.com/ssfrr/TestSetExtensions.jl) and [ReTest.jl](https://github.com/JuliaTesting/ReTest.jl), but I believe you can do most of your work with Test.jl.

Getting started with testing is trivial in Julia, just add a `test/runtests.jl` file to your package and add code like this:

```julia
using Test
using MyPackage

@testset "MyPackage tests" begin
    @test 1==2
end
```

You can then run the tests with `Pkg.test("MyPackage")` which starts an isolated sandbox environment for the tests.

If you are creating a reproducible package for others, including your future self, then there is absolutely no reason to not write tests. However, writing good descriptive tests that cover all your bases is a more advanced art. Many books are written on this topic. I would advise to just get started and improve your testing strategy as you grow your codebase.

## Automated Testing & Continuous Integration

Once you have unit tests defined, this aspect is a low hanging fruit for automation. It's very easy to forget to run the unit tests before committing. Automatically testing the code will save you from simple mistakes.

You can [setup Github Actions](https://bjack205.github.io/JuliaTemplateRepo.jl/dev/CI.html) or use a tool like Jenkins, to automatically run the tests upon every commit and block developers from merging if the unit tests do not pass. [PkgTemplates.jl](https://github.com/JuliaCI/PkgTemplates.jl) will typically already generate this Github Action for your open source package.

The tools and practice of frequently and automatically checking your code development is called Continuous Integration (CI). Inside software organization this is often combined with Continuous Deployment (CI/CD). All continuous integration (CI) tools that the Julia community uses can be found in [JuliaCI · GitHub](https://github.com/JuliaCI), I will address a few of those. If you want to configure your own Github actions you be inspired by the examples in [Julia Actions · GitHub](https://github.com/julia-actions).

## Code Coverage

A straightforward metric for monitoring your code quality is to check the fraction of code covered by your tests. Similar to automating your tests, this is a very low hanging fruit in the Julia community.

The code coverage generation itself is embedded inside the package manager via `Pkg.test("MyPackage", coverage=true)` . This will generate `.jl.cov` files with information about how often a line of code is touched by the tests.

Analyzing the code coverage visually line-by-line, for example inside VS Code, can help you identify where you are lacking tests, or help you find out where you can delete un-used code that is never called by your functions. You can automatically send the files to a service, like [Coveralls.io](http://Coveralls.io) or [Codecov.io](http://Codecov.io), and analyze in the browser. Here's an example in [Flux's functions.jl file](https://app.codecov.io/gh/FluxML/Flux.jl/blob/master/src/losses/functions.jl#L155), that's has one uncovered line (note that it's very common to forget to test our errors or other corner cases):

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1682335565744/6849d64f-4ab0-409f-980b-336c4c82dc97.png align="center")

You can calculate statistics on these coverage files, for example with [Coverage.jl](https://github.com/JuliaCI/Coverage.jl) or with one of the services above. That way you can monitor coverage statistics over time. And if you use such a service for your open source package you can add a shiny badge to your readme to show-off your coverage.

Other commercial tools are busy adopting Julia's code coverage, check with your preferred supplier if they already support Julia. If not, please request them to do so.

## Documentation Testing

Good documentation is incredible important for the users of your package, both in the open source community as well as inside organizations. Unfortunately, documentation that includes code examples can run out of sync with your code if you forget to test those. But it's very easy to automatically test those code examples with [Documenter.jl doctesting](https://documenter.juliadocs.org/stable/man/doctests/index.html).

Whenever you write a docstring or write code snippets in your docs folder, just add `jldoctest` and the expected output.

````julia
```jldoctest
a = 1
b = 2
a + b

# output

3
```
````

Now just add `Documenter.doctest(MyPackage)` to your automated tests, and you know immediately when your examples no longer work. Easy!

## Style Guides & Code Formatting

One of the challenges when working with many people on a single codebase is to adhere to a consistent coding style that is pleasant and unambiguous for everyone. This is where style guides help, together with formatting tools that make it easy to adhere to such a style guide.

The primary open source Julia style guides are at the moment are:

* [YAS](https://github.com/jrevels/YASGuide) (yet another style)
    
* [Blue](https://github.com/invenia/BlueStyle) style
    
* [SciML](https://github.com/SciML/SciMLStyle) style
    

You can [automatically format](https://www.julia-vscode.org/docs/stable/userguide/formatter/) your Julia files in VS Code with the click of a button. If you are a command line user, or want to automate the formatting in your CI, you can use the underlying [JuliaFormatter.jl](https://github.com/domluna/JuliaFormatter.jl) package.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1682337461140/08742d45-0db9-4a1f-92fc-3dc1778f1f94.png align="center")

This should get you started with code formatting in no time. Discuss with your colleagues which style guide you prefer. Personally I use the [BlueStyle](https://github.com/invenia/BlueStyle) so far, but the SciML style is relatively new, so looking into that one as well.

## Static Code Analysis

Static code analysis will look at the code without executing it. One package we found is [StaticLint.jl](https://github.com/julia-vscode/StaticLint.jl), which is used primarily by the Julia VS Code plugin [LanguageServer.jl](https://github.com/julia-vscode/LanguageServer.jl) to report on potential problems in your code. These are normally reported under the "Problems" tab. Here I found a few potential problems inside the [DataFrames.jl](https://github.com/JuliaData/DataFrames.jl) package:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1682407779863/4faa1e3e-25f2-4e9f-afc7-471f77de467a.png align="center")

The VS Code plugin also reports on potential problems when hovering over code, such as a warning about this unused function argument. These are not reported in the "Problems" tab.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1682416576798/b916156a-a8d4-4738-81a1-bafdb5a94b53.png align="center")

StaticLint still misses some documentation for users, but you can use the following [script](https://gist.github.com/pfitzseb/22493b0214276d3b65833232aa94bf11) and read my discussion in an [issue here](https://github.com/julia-vscode/StaticLint.jl/issues/14). After some fiddling with the code and environments I am able to obtain the same "diagnostics" on my REPL for a given file:

```julia
julia> docs[3]
Document: file:///c%3A/Users/matcox/Documents/Julia/static_lint/src/abstractdataframe/selection.jl

julia> docs[3].diagnostics[1]
LanguageServer.Diagnostic(LanguageServer.Range(LanguageServer.Position(223, 15), LanguageServer.Position(223, 36)), 4, "UnusedFunctionArgument", missing, "Julia", "An argument is included in a function signature but 
not used within its body.", [1], missing)
```

So StaticLint.jl can be used, but it's not yet user friendly for integration into any command line interfaces or automated tooling.

## Quality Assurance with Aqua.jl

The package [Aqua.jl](https://github.com/JuliaTesting/Aqua.jl) is developed to automate quality assurance for Julia. The readme is clear on what it checks:

* There are no method ambiguities.
    
* There are no undefined `export`s.
    
* There are no unbound type parameters.
    
* There are no stale dependencies listed in `Project.toml`.
    
* Check that test target of the root project `Project.toml` and test project (`test/Project.toml`) are consistent.
    
* Check that all external packages listed in `deps` have corresponding `compat` entry.
    
* `Project.toml` formatting is compatible with Pkg.jl output.
    
* There are no "obvious" type piracies
    

Aqua provides a function to use in your testing environment, which will report issues and throws an error so your tests cannot pass unless all Aqua checks pass. Here's a snippet of what we get for DataFrames:

```julia
julia> using Aqua, DataFrames

julia> Aqua.test_all(DataFrames)
17 ambiguities found
Ambiguity #1
<=(a::Integer, b::SentinelArrays.ChainedVectorIndex) in SentinelArrays at C:\Users\matcox\.julia\packages\SentinelArrays\BcfVF\src\chainedvector.jl:208
<=(x::BigInt, i::Integer) in Base.GMP at gmp.jl:696

Possible fix, define
  <=(::BigInt, ::SentinelArrays.ChainedVectorIndex)

... 

Test Summary:    | Fail  Total  Time
Method ambiguity |    1      1  8.3s
ERROR: Some tests did not pass: 0 passed, 1 failed, 0 errored, 0 broken.
```

Unfortunately I only find ambiguities for DataFrames, maybe I should find a package with more problems. You can also run all the underlying checks independently if you read the [Aqua documentation](https://juliatesting.github.io/Aqua.jl/stable/#Aqua.test_all-Tuple%7BModule%7D). Note that if you only want to check for ambiguities, you can also choose to call `Test.detect_ambiguities` directly from the standard Julia Test package.

A nice addition to Aqua would be a way to store the found issues in a standardized file format instead of printing them on the REPL. Similar to code coverage reporting, this can help to make overviews in automated systems. Now we would have to capture the printed output and parse that somehow.

## Finding Type Instability with JET.jl

There is non-stop activity in the Julia community to analyze our own code for improvements. An advanced package is [JET.jl](https://github.com/aviatesk/JET.jl), which uses the Julia compiler itself to detect potential bugs and type instabilities.

What is type instability? This happens when the type of a variable changes unexpectedly. Here's a simple example that outputs either an integer or a floating point variable:

```julia
function foo(value)
    if value < 1
        return 1
    else
        return 1.0
    end
end
```

Type instability is bad for performance because the compiler cannot infer the types and generate optimal native code. It may also point at bugs in your code, if you did not intend to have such instability. Julia does not enforce type stability like certain languages, because it wants to remain an easy language to use. Sometimes you don't care about performance and don't want to worry about types, in which cases it's easy if you are not forced to worry.

If you just want to check whether the output value can be inferred, you can use `Test.@inferred` in your tests:

```julia
julia> using Test

julia> Test.@inferred foo(0.5)
ERROR: return type Int64 does not match inferred return type Union{Float64, Int64}

julia> Test.@inferred foo(1.5)
ERROR: return type Float64 does not match inferred return type Union{Float64, Int64}
```

However, when you want more certainty about the internals of your code then you can turn to JET. Most of JET is doing specific method analysis with `@report_opt` and `@report_call` . JET can do some limited static analysis of your entire package with the `report_package` function. Unlike `@report_call` , this means JET doesn't know what types you want to input into your methods, so it has to make some assumptions.

I do warn that the output of JET can be rather intimidating. Here's what you get when executing the example `@report_call sum("julia")` :

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1682424595139/69f43545-9fa5-4ee5-bd98-afa928c11a55.png align="center")

And that's just the example from the [Quick Start](https://aviatesk.github.io/JET.jl/stable/jetanalysis/#jetanalysis-quick-start) page of JET.

We're still investigating how to use JET, because it is pretty advanced tooling. If you just started with types and Julia, I wouldn't dive right into this. Take your time to think about what [type inference](https://en.wikipedia.org/wiki/Type_inference) really means, and read the [documentation of JET](https://aviatesk.github.io/JET.jl/stable/tutorial/) if you want to know more.

## Reducing Compile Time with SnoopCompile.jl

Optimizing your code such that the compilation times are reduced is maybe not the first thing that comes to mind when thinking about "code quality", but it can improve the user experience of your package. Nobody likes to wait long to import your package. The [SnoopCompile.jl](https://github.com/timholy/SnoopCompile.jl) package helps you with analyzing your code for such improvements. It "snoops" on the compiler and reports on it's findings.

There is a lengthy blog post from the SciML community on how they improved their compilation times with SnoopCompile and other tools, called [How Julia ODE Solve Compile Time Was Reduced From 30 Seconds to 0.1](https://sciml.ai/news/2022/09/21/compile_time/). Definitely read that one for more information, I will not go into details here, but I do think SnoopCompile is worth a mention.

## Conclusion

You have plenty of options to check the code quality of your Julia packages and improve the quality over time. If this feels like a daunting task as a beginning (Julia) developer, don't worry, you can slowly add these tools to your workflow over time. The most important thing is to start with a good package structure and basic testing. The fact that the Julia ecosystem is so focused on making quality easy for beginners is truly praise-worthy and will help us all in the long run.

For senior developers and managers looking into these tools, one thing to remember is that lot's of code quality tooling in Julia is written with the human developer in mind. This currently limits some of the integration in automated CI tools. I believe this topic deserves some more attention in the Julia community and more support from commercial code quality tooling vendors. The good thing is that due to the standardization of the Julia package management it is very easy to get started with a uniform automation system in your organization. As the tools improve for these systems, it will be easy to incrementally add such tools to any open source or internal CI workflows.

Thanks to my colleague Matthijs den Otter for helping with the investigation. If we find better ways to monitor your Julia code quality, I intend to share that here, so don't forget to subscribe to the blog.