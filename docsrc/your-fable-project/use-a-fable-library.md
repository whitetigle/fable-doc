---
title: Use a Fable library
---

[[toc]]

## Use a Fable library

In order to maximize the performances of the F# compiler, we often create libraries using [NuGet](https://www.nuget.org/), which is the defacto .NET package manager. Creating library most often implies creating a cross-platform ".dll" which then, offers the F# compiler the convenience of having to read symbols and not actual code, thus leading to faster compilation times.

So we do need libraries. And Fable proposes a great variety of libraries ready for you to use like:
- [Fable.Core](https://www.nuget.org/packages/Fable.Core/), which is required for every Fable project
- [Fable.Browser.Dom](https://www.nuget.org/packages/Fable.Browser.Dom/1.0.0-alpha-004), for all your Dom need (window, documents)
- [*Other Fable packages...*](https://www.nuget.org/packages?q=fable)

There are 2 ways to call Fable libraries:
1 - Reference them directly in your project file
2 - Use paket manager.

### Option 1: Reference a library in your project file

Just like `.fs` files, we reference libraries directly in the `.fsproj` file.

We need to tell what library we need and what version we'd like to use. For instance for `Fable.Browser.Dom` version `1.0.0-alpha-004` we'll add the following node in the `.fsproj` file:

```xml
<PackageReference Include="Fable.Browser.Dom" Version="1.0.0-alpha-004" />
```

Hence the standard format for a library:

```xml
<PackageReference Include="packageName" Version="versionNumberString" />
```

For instance here is a an `ItemGroup` with references for two libraries:

```xml
<ItemGroup>
    <PackageReference Include="Fable.Core" Version="2.1.0-alpha-002" />
    <PackageReference Include="Fable.Browser.Dom" Version="1.0.0-alpha-004" />
</ItemGroup>
```

That's basically all you need to do because our build process will then automatically download the libraries for you and compile your code against them.


### Option 2: Use Paket!

The second way of adding libraries and often the most safe one in order to make sure libraries are compatible, is to use  the [paket](https://fsprojects.github.io/Paket/) library manager. While it's not compulsory, it's in most cases a good choice for large projects.

Using paket is clearly straightforward if you follow the [official documentation](https://fsprojects.github.io/Paket/getting-started.html)

But in order to make things easier for you, we created a [sample](https://github.com/fable-compiler/fable2-samples/tree/master/withpaket). This is a good companion while you read the paket doc.

Usually getting started with paket takes only a few minutes.

### Important considerations

You can only use .NET libraries that comply to the following rules:
1. Fable libraries
2. Pure F# libraries with no .NET bindings

The reason is simple: Fable targets JavaScript while .NET targets the .NET environment. But libraries maintainers will indicate that for you and you won't get lost.
