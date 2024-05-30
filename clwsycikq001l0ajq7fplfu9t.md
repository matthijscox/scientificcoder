---
title: "To Dict Or Not To Dict: Comparing Data Structure Sizes"
datePublished: Thu May 30 2024 07:47:04 GMT+0000 (Coordinated Universal Time)
cuid: clwsycikq001l0ajq7fplfu9t
slug: to-dict-or-not-to-dict-comparing-data-structure-sizes
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1716888816353/31878401-568f-4f95-b67e-dc8bf3df4240.png
tags: data-structures, julia

---

Searching for the best data structure for your problem can be a tricky business. The chosen data structure should be easy to understand for other developers, run fast in the algorithms where it's used and be memory efficient. You can't always optimize at the start, but you can learn from mistakes. I made such a mistake recently regarding the sizes in memory and on disk, so I'd like to walk you through a simple example.

Basically I was storing keys and values, so obviously a dictionary is the first choice of data structure. But it turned out that was not the most memory efficient solution to choose.

So let's look at 4 options that I tried, a `Dict` an `OrderedDict` a `Vector{Pair}` and an `AxisArray`. We'll start with a simple example with a 1000 strings and integers:

```julia
using OrderedCollections, AxisArrays

# our types
dict = Dict{String,Int}()
dict_ordered = OrderedDict{String,Int}()
vector_pair = Vector{Pair{String,Int}}()

# create some key-value objects
for value in 1:1000
    dict["key$value"] = value
    dict_ordered["key$value"] = value
    push!(vector_pair, "key$value" => value)
end

# and an axis array
axis_array = AxisArray(collect(values(dict)), key = collect(keys(dict)))
```

If we look at the data structure implementations, we see below that a `Dict` contains three arrays, `slots`, `keys`, `vals` . I assume the `slots` are used as a kind of hashes to quickly index the keys. But the `Dict` pre-allocates 4096 keys and values apparently.

```julia
julia> dump(dict)
Dict{String, Int64}
  slots: Array{UInt8}((4096,)) UInt8[0xd9, 0xef, 0xfb, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0xba  …  0x00, 0x00, 0x00, 0x00, 0x00, 0x87, 0x00, 0x94, 0x00, 0x00]      
  keys: Array{String}((4096,))
    1: String "key239"
    2: String "key390"
    ...
    4095: #undef
    4096: #undef
  vals: Array{Int64}((4096,)) [239, 390, 722, 0, 0, 0, 0, 0, 0, 480  …  0, 0, 0, 0, 0, 532, 0, 568, 0, 0]
  ndel: Int64 0
  count: Int64 1000
  age: UInt64 0x00000000000003f0
  idxfloor: Int64 1
  maxprobe: Int64 4
```

The `OrderedDict` is similar, but doesn't pre-allocate the keys and values.

```julia
julia> dump(dict_ordered)
OrderedDict{String, Int64}
  slots: Array{Int32}((4096,)) Int32[239, 390, 722, 0, 0, 0, 0, 0, 0, 480  …  0, 0, 0, 0, 0, 532, 0, 568, 0, 0]
  keys: Array{String}((1000,))
    1: String "key1"
    2: String "key2"
    ...
    999: String "key999"
    1000: String "key1000"
  vals: Array{Int64}((1000,)) [1, 2, 3, 4, 5, 6, 7, 8, 9, 10  …  991, 992, 993, 994, 995, 996, 997, 998, 999, 1000]
  ndel: Int64 0
  maxprobe: Int64 4
  dirty: Bool true
```

A `Pair` is just a key (`first`) and a value (`second`) in a struct. And then we can store an array of those.

```julia
julia> dump(vector_pair[1])
Pair{String, Int64}
  first: String "key1"
  second: Int64 1
```

An `AxisArray` is just an array with named axes, where each value in an axis contains our key.

```julia
julia> dump(axis_array)
AxisVector{Int64, Vector{Int64}, Tuple{Axis{:key, Vector{String}}}}
  data: Array{Int64}((1000,)) [239, 390, 722, 480, 679, 798, 841, 877, 21, 636  …  413, 667, 717, 697, 398, 256, 334, 823, 532, 568]
  axes: Tuple{Axis{:key, Vector{String}}}
    1: Axis{:key, Vector{String}}
      val: Array{String}((1000,))
        1: String "key239"
        2: String "key390"
        ...
        999: String "key532"
        1000: String "key568"
```

You can also index an AxisArray similar to a dictionary:

```julia
julia> axis_array["key5"]
5
```

## 1D sizes

Let's look at the memory size of these 4 beasts:

```julia
julia> varinfo(r"dict|vector|axis")
  name               size summary     
  –––––––––––– –––––––––– ––––––––––––––––––––––––––––––––––––––––––––
  axis_array   29.302 KiB 1-dimensional AxisArray{Int64,1,...} 
  dict         81.747 KiB Dict{String, Int64} with 1000 entries             
  dict_ordered 45.356 KiB OrderedDict{String, Int64} with 1000 entries    
  vector_pair  44.856 KiB 1000-element Vector{Pair{String, Int64}}
```

The `AxisArray` wins in terms of memory size. I'm also curious about size on disk with `JLD2`

```julia
using JLD2
for type in (:dict, :dict_ordered, :vector_pair, :axis_array)
    jldsave("$type.jld2", x=eval(type))
    println("$type.jld2 size: $(filesize("$type.jld2"))")
end
```

This gives us the following jld2 sizes:

```julia
dict.jld2 size: 105398
dict_ordered.jld2 size: 66303
vector_pair.jld2 size: 105162
axis_array.jld2 size: 50949
```

Again the `AxisArray` wins, though the difference shrunk a bit. Although a 2x difference doesn't feel very significant.

## 2D sizes

In my case I was actually storing a lot of dictionaries, which were actually sharing the same keys. This was my biggest mistake, because you can store such data as a matrix and share the keys of the rows and columns. Let's look at a small example again:

```julia
# let's very naively store a vector of dictionaries, to get a feel for the size
dict_vector = [deepcopy(dict) for _ in 1:100]

# let's also store an AxisMatrix with shared rows and columns
axis_matrix = AxisArray(
    rand(Int, 1000, 100),
    row = ["row$r" for r in 1:1000],
    col = ["col$c" for c in 1:100]
)
```

Now the sizes are significantly different:

```julia
julia> varinfo(r"dict_vector|axis_matrix")
  name         size           summary     
  –––––––––––– ––––––––––---- ––––––––––––––––––––––––––––––––––––––––––––
  axis_matrix  804.845 KiB    2-dimensional AxisArray{Int64,2,...} 
  dict_vector  7.984 MiB      100-element Vector{Dict{String, Int64}}
```

You see an almost 10x difference in memory size. The difference is more than 10x in the JLD2 files:

```julia
julia> for type in (:dict_vector, :axis_matrix)
    jldsave("$type.jld2", x=eval(type))
    println("$type.jld2 size: $(filesize("$type.jld2"))")
end

dict_vector.jld2 size: 10352637
axis_matrix.jld2 size:   849101
```

Differences are so big because most of the data is integers, which are small, and the "keys" of the matrix are strings which can occupy quite some memory.

In the end I opted to switch to AxisArrays for my 2D problem. Though I could only do this by assuming all columns had the same keys.

## Conclusion

There's a difference in memory usage between data structures that store keys and value. Dictionaries do not seem to be optimal, however for the simple case I looked at here the difference is not as large as I imagined. A bigger difference only appears when you have extra information, such as repeating keys, and you can use that information to choose a more optimal data structure, such as a matrix with the keys on the axes.