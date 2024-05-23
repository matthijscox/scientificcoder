---
title: "Comparing Package Management in Python, R, Julia, and Rust"
datePublished: Thu May 23 2024 09:23:13 GMT+0000 (Coordinated Universal Time)
cuid: clwj1p6vb000108lbh1co0m5f
slug: comparing-package-management-in-python-r-julia-and-rust
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1716453066068/3cc0f5a8-83e5-455e-80a4-761753dea076.png
tags: python, r, rust, julia, package-manager

---

When switching between programming languages, people often start with comparing syntax differences, and many overview exist on this topic. However, a large part of programming revolves around package management, especially if you want to develop your own packages, and I have not encountered many overviews comparing programming languages on this topic. A package is essentially how code is shared between programmers. Understanding package management and package development is vital when you want to get good at a language.

To satisfy my curiosity, and help myself and others, I decided to write this package management overview myself. I have chosen to compare popular languages Python and R, my personal favorite language Julia, and the rising star Rust, which has notoriously good package management.

## Overview Table

I created an overview table below comparing various aspects of package management between the languages. I'll go into the details in the remainder of the blog post.

|  | Python | R | Julia | Rust |
| --- | --- | --- | --- | --- |
| **Package Manager** | `pip` or `conda` | `install.packages()` (base R) | `Pkg` | `cargo` |
| **Development Tools** | `setuptools`, `poetry` | `devtools` | `Pkg` | `cargo` |
| **Package Template Tools** | `cookiecutter`, `pyscaffold`, `flit` | `usethis`, `devtools` | `Pkg.generate()` , `PkgTemplates.jl` | `cargo new`, `cargo init`, `cargo-generate` |
| **Package Repository** | PyPI (Python Package Index) | CRAN (Comprehensive R Archive Network) | General registry | crates.io |
| **Virtual Environment** | `venv`, `virtualenv` | `renv` | Built-in in the `Pkg` module | Built-in with `cargo` |
| **Distribution Format** | `.whl` (wheel, incl binaries) or `tar.gz` (source) | `.tar.gz` (source and/or binary) | `Pkg` will git clone from source, and download (binary) artifacts | `.crate`(can be binary or source) |
| **Dependency Management** | `requirements.txt`, or `Pipfile`, or `pyproject.toml` (poetry) | `DESCRIPTION`, `NAMESPACE` | `Project.toml`, `Manifest.toml`, `Artifacts.toml` | `Cargo.toml`, `Cargo.lock` |
| **Tutorial** | [Python Packages book](https://py-pkgs.org/) (uses poetry) | [R packages book](https://r-pkgs.org/) | [Pkg docs](https://pkgdocs.julialang.org/v1/) and this [howto](https://julialang.org/contribute/developing_package/) | [Cargo Guide](https://doc.rust-lang.org/cargo/guide/index.html) |

## Package manager

In modern open source programming, package managers are vital to help you install the code you need, and all of it's dependencies, which includes other (source code) packages and compiled binary libraries.

* **Python**: `pip` is the standard tool for installing and managing Python packages. Use `pip install <package>` to install a package via the command line. A secondary package manager is `conda`, which tries to be language-agnostic, but is mostly used for Python.
    
* **R**: `install.packages("<package>")` is the base function for installing packages from CRAN.
    
* **Julia**: `Pkg.jl` is the built-in package manager. `Pkg.add("<package>")` will install a package.
    
* **Rust**: `cargo` is the build system and package manager for Rust. Packages are called "crates" and you can install them via `cargo install <crate>` on the command line.
    

An interesting difference is that some languages, like Python and Rust, have a package manager that is called from outside the language, so from your operating system's command line, while others like in Julia and R are called from inside the programming language itself.

## Package Development Tools

The package manager is often only targeted at helping users install packages. Developers of package may need additional tools, for example to handle dependencies.

* **Python**: `setuptools` can help you with building and distributing packages. `poetry` is a more recent, elegant way to help you with packaging and dependencies (but only for pure Python code, not for binary dependencies).
    
* **R**: `devtools` is the go-to tool for helping you development of R packages.
    
* **Julia**: `Pkg.jl` can help with most of your development.
    
* **Rust**: `cargo` can help with most of your development.
    

## Package Template Tools

Template are predefined formats of a package folder structure and files, typically including documentation, testing and automation (for example with Github actions). This helps you get up and running quickly with a professional package.

**Python**:

* `cookiecutter`: A popular tool to create project templates from cookiecutters (project templates).
    
* `pyscaffold`: A tool to set up the scaffolding for new Python projects with sensible defaults.
    
* `flit`: Simplifies the process of packaging simple Python projects, focusing on pyproject.toml.
    

**R:**

* `usethis`: Facilitates package development by setting up structure and common files.
    
* `devtools`: Provides functions like `create()`, `package.skeleton()`, and more to help create and manage R packages.
    

**Julia:**

* `Pkg.generate()`: Built-in function in Julia’s Pkg module to generate a new package with a very minimal template.
    
* `PkgTemplates.jl`: A Julia package that generates new Julia package projects with customizable templates.
    

**Rust:**

* `cargo new`: Initializes a new project with a basic template.
    
* `cargo init`: Initializes a new package in an existing directory.
    
* `cargo-generate`: A tool to generate new Rust projects based on existing templates.
    

## Package Repository

When you install a package, the source code and all of its dependencies need to be downloaded from somewhere. Most programming languages use a central location that stores copies of the source code and/or compiled binaries, for every version of a package. Julia is slightly different, using a registry that contains links to the source code.

* **Python**: Packages are hosted on [PyPI](https://pypi.org/), the Python Package Index.
    
* **R**: [CRAN](https://cran.r-project.org/) is the primary repository for R packages.
    
* **Julia**: Packages are registered in the [General registry](https://github.com/JuliaRegistries/General). Note these are only links to the (Github) source code. Binary artifacts are built with [Yggdrasil](https://github.com/JuliaPackaging/Yggdrasil) and [BinaryBuilder.jl](https://docs.binarybuilder.org/stable/).
    
* **Rust**: [crates.io](http://crates.io) is the official package registry.
    

## Virtual Environments

Virtual environments are crucial when you need to handle different versions of dependencies across your different projects. You could try to use one environment for all your projects, but that may quickly lead to conflicts in your dependencies.

Often virtual environments are just switching the folder location from which packages are installed and loaded, and use their own separate dependency management.

* **Python**: Tools like `venv` and `virtualenv` create isolated environments for projects. Create an environment with `python -m venv /path/to/environment`.
    
* **R**: `renv` manages project-specific environments. Create an environment with `renv::init(project = "path/to/environment")` .
    
* **Julia**: Environments are managed within the `Pkg` module. I have a [blog post about Julia environments](https://scientificcoder.com/clean-code-tips-for-scientists-1-reproducible-environments). Create an environment with `Pkg.activate("path/to/environment")`.
    
* **Rust**: Environments are handled within `cargo` projects, use `cargo new my_project`.
    

## Distribution Formats

When you release and distribute your package it's good to be aware of how it's handled by the package repository and package manager.

* **Python**: Uses `.whl` for pre-built packages with binary distributions and `.tar.gz` for source distributions (aka `sdist`). These distributions are stored in the PyPI.
    
* **R**: Packages are distributed as `.tar.gz` or `.zip`, stored in CRAN.
    
* **Julia**: Source distributions are downloaded directly from their repositories by `Pkg`
    
* **Rust**: Packages (crates) are distributed as `.crate` files, which are by default located at `crates.io`.
    

## Dependency Management

When a package is installed, the manager needs to know which dependencies to install, and which versions. Every package developer needs to write this down in some predefined format that the package manager can parse.

* **Python**: `requirements.txt` is used for listing dependencies, or a `Pipfile` for advanced dependency management. `pyproject.toml` is an alternative source code dependency management file, used by the `poetry` tool.
    
* **R**: `DESCRIPTION` and `NAMESPACE` files manage dependencies.
    
* **Julia**: Use `Project.toml` to handle source code dependencies. A `Manifest.toml` file can be generated to specify the exact versions used in a project. And `Artifacts.toml` is used to handle (binary) artifacts.
    
* **Rust**: `Cargo.toml` lists dependencies, and `Cargo.lock` locks them.
    

For example, a simple `Cargo.toml` may look like this to specify your package name, version and dependencies. Julia's `Project.toml` and Python poetry's `pyproject.toml` look similar.

```ini
[package]
name = "mypackage"
version = "0.1.0"

[dependencies]
time = "0.1.12"
```

## Binary Dependency Management

I'm curious how the different languages handle binary dependencies, for example libraries compiled from C code. This is a more advanced topic that most package developers don't need to worry about, but it may interest people (such as myself) who have encountered this topic in one or more programming languages. I've personally encountered this challenge when I contributed to the [BrainFlow](https://github.com/brainflow-dev/brainflow) project, which distributes a C++ library with bindings in many programming languages.

There's multiple aspects to binary dependencies:

1. **Write Binary Code**: Write or include existing C/C++/Fortran code within your package.
    
2. **Build Configuration**: Configure the build process to compile the binary code (e.g., using `setup.py`, `Cargo.toml`, `Makevars`).
    
3. **Build**: Run the build and compilation tool specific to your language (e.g., `python setup.py`).
    
4. **Use in Code**: Import and use the compiled binaries within your main language.
    

Each programming language handles this differently.

Let's say we have the following very simple C program, with a header:

```c
// myclib.h
#ifndef MYCLIB_H
#define MYCLIB_H

void hello_from_c();

#endif // MYCLIB_H
```

and the C code:

```c
// myclib.c
#include <stdio.h>

void hello_from_c() {
    printf("Hello from C!\n");
}
```

How would we start embedding that in each language?

### Python binaries

1. **Creating Binary Extensions**:
    
    * **C Extensions**: Python allows you to write C extensions, which can be compiled and used within Python code. This is typically done using the Python C API or using Cython.
        
    * **Tools**:
        
        * `setuptools`: Includes support for compiling C extensions. You can specify extensions in `setup.py`.
            
        * `Cython`: A superset of Python that additionally supports C language features. See their tutorial on [using C libraries](https://docs.cython.org/en/latest/src/tutorial/clibraries.html). Cython will act as a kind of glue between C and your regular Python code, in the form of a `.pxd` and/or `.pyx` file.
            
    * **Example with Cython**:
        
        ```python
        # myclib.pxd
        cdef extern from "myclib.h":
            void hello_from_c()
        ```
        
        ```python
        # myextension.pyx
        
        # Import the declarations from the .pxd file
        from myclib cimport hello_from_c
        
        # Create a Python wrapper function
        def py_hello_from_c():
            hello_from_c()
        ```
        
2. **Building**:
    
    * Use `setuptools` with extensions in the `setup.py` file:
        
        ```python
        from setuptools import Extension, setup
        from Cython.Build import cythonize
        
        setup(
            ext_modules = cythonize([Extension("myextension", ["myextension.pyx"])])
        )
        ```
        
    * Running `python setup.py build_ext --inplace` compiles the extension for you, if a C compiler is configured properly. Alternatively you can compile the C code yourself and [dynamically link to it](https://docs.cython.org/en/latest/src/tutorial/clibraries.html#dynamic-linking).
        
    * To automatically compile for every platform, look into [cibuildwheels](https://cibuildwheel.pypa.io/en/stable/).
        
3. **Using Binary Extensions**:
    
    * Once compiled, these extensions can be imported and used in Python code just like any other module.
        
    * **Installation**: Use `pip` to install binary packages (wheels) from PyPI or directly from a source distribution. Note that there should be a wheels file per platform, see for example the [Numpy built distributions](https://pypi.org/project/numpy/#files).
        

### **R binaries**

1. **Creating Binary Packages**:
    
    * R packages can include source code written in C, C++, or Fortran. These are compiled when the package is built.
        
    * **Tools**:
        
        * `R CMD INSTALL`: The command-line tool to install packages and compile their binary components. Alternatively you can use `devtools`.
            
        * `Rcpp`: A package that makes it easier to integrate R with C or C++ code.
            
    * **Example**:
        
        ```cpp
        // myextension.cpp
        #include <Rcpp.h>
        extern "C" {
            #include "myclib.h"
        }
        
        // [[Rcpp::export]]
        void hello_from_c_wrapper() {
            hello_from_c();
        }
        ```
        
        ```r
        # hello.R
        
        # Import the Rcpp function
        Rcpp::sourceCpp("src/myextension.cpp")
        
        hello_from_c <- function() {
          hello_from_c_wrapper()
        }
        ```
        
2. **Building**:
    
    * Update the `DESCRIPTION` and `NAMESPACE` files to add Rcpp and your C function. Here's an example `NAMESPACE` :
        
        ```r
        useDynLib(MyPackage)
        importFrom(Rcpp, evalCpp)
        export(hello_from_c)
        ```
        
    * Then use `R CMD build` to create a package tarball and `R CMD INSTALL` to install it, which compiles the code. Alternatively you can use `devtools` to build and install inside your R session:
        
        ```r
        setwd("path/to/MyPackage")
        devtools::document()
        devtools::build()
        devtools::install()
        ```
        
3. **Using Binary Packages**:
    
    * After installation, functions from the binary components can be called from R scripts or the console.
        
    * **Installation**: Binary packages can be installed from CRAN or other repositories using `install.packages()`.
        

### **Julia binaries**

1. **Creating Binary Dependencies**:
    
    * Julia allows direct calling of C functions using its `ccall` interface. No wrapper code is needed.
        
    * **Tools**:
        
        * `BinaryBuilder.jl`: A tool for building binaries that can be used across different platforms.
            
    * **Example**:
        
        ```julia
        # Calling a C function
        function my_c_function()
            ccall((:hello_from_c, "libmyclib"), Cvoid, ())
        end
        ```
        
2. **Building**:
    
    * Use [`BinaryBuilder.jl`](https://docs.binarybuilder.org/stable/) to create binaries for every platform and distribute them automatically, including the Julia wrapper code. This way you do not have to compile anything yourself. You will have to put the C code into a separate repository and then provide a build script to [Yggdrasil](https://github.com/JuliaPackaging/Yggdrasil).
        
    * Alternatively you can compile the code yourself and dynamically open the library in Julia with `Libdl.dlopen()`.
        
3. **Using Binary Dependencies**:
    
    * **Artifacts**: Julia uses a system of artifacts to handle binary dependencies, which can be declared in a package's `Artifacts.toml` file. The wrapper package generated by `BinaryBuilder.jl` will already have this `Artifacts.toml` file. The wrapper package will also have regular Julia functions automatically generated for all the `ccall` functions, which you can use in your Julia code.
        
    * If you manually compiled the C library, you'll have to upload it somewhere and add the link to the `Artifacts.toml` file. `ArtifactUtils.jl` is a package that can help with that. (Note: I used this approach for [brainflow](https://github.com/brainflow-dev/brainflow/tree/master/julia_package/brainflow).)
        
    * **Installation**: Julia's package manager `Pkg` downloads and installs the required binaries automatically.
        

### **Rust binaries**

1. **Creating Binary Dependencies**:
    
    * Rust can interface with C libraries using the `extern` keyword and FFI (Foreign Function Interface). See this [tutorial](https://docs.rust-embedded.org/book/interoperability/c-with-rust.html) for example.
        
    * **Tools**:
        
        * `cargo`: Manages dependencies and builds projects.
            
        * `bindgen`: (Optional) generates Rust FFI bindings to C libraries.
            
    * **Example**:
        
        ```rust
        // src/extension.rs
        
        extern "C" {
            fn hello_from_c();
        }
        
        pub fn call_hello_from_c() {
            unsafe {
                hello_from_c();
            }
        }
        ```
        
2. **Building**:
    
    * Add the `cc` crate to `Cargo.toml` to help with compiling the C code:
        
        ```ini
        [package]
        name = "myextension"
        version = "0.1.0"
        edition = "2018"
        build = "build.rs"
        
        [build-dependencies]
        cc = "1.0"
        ```
        
    * Specify the paths to the C code in a `build.rs` file of a Rust project.
        
        ```rust
        fn main() {
            cc::Build::new()
                .file("src/myclib.c")
                .compile("myextension");
        }
        ```
        
    * Then build the project using `cargo build --release`
        
3. **Using Binary Dependencies**:
    
    * The example above compiles the C code for you. If your C code is already available as a static library, it's also possible to link against that.
        
    * **Installation**: Rust's `cargo` handles fetching and compiling the necessary binary dependencies.
        

Note that these are very simple, and incomplete, examples of embedding C in the respective languages, yet they give you a highlight of what's involved when working with binary dependencies.

### **Conclusion**

Mastering package management and binary dependencies in Python, R, Julia, and Rust varies by language, but it's essential if you want to be a proficient developer in any of these languages. I hope this overview helps you with your package development whenever you need to switch between these programming languages.