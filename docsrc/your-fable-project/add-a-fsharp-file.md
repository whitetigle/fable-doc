---
title: Add a new F# file
---

# Add a new F# file to your project

Since a F# project takes its roots from the .NET ecosystem, we need to follow a few obligatory steps in order to add a file to F# a Project.

<ul class="textual-steps">
<li>

**Create the actual F# file**

Like JS files end with `.js`, F# files end with `.fs`. So adding a new F# file, is just a matter of creating a file ending with `.fs` like `MyAwesomeFeature.fs`
</li>

<li>

**Add the file to the project**

If your source files are located in the `src` folder, just open that folder. Here you will find a file ending with the `.fsproj`. This is the F# project file which describes the project using `XML` nodes.

For instance, if you take the Browser sample, in the src folder you will find an `App.fsproj` file:
```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <Compile Include="App.fs" />
    </ItemGroup>
    <ItemGroup>
        <PackageReference Include="Fable.Core" Version="2.1.0-alpha-002" />
        <PackageReference Include="Fable.Browser.Dom" Version="1.0.0-alpha-004" />
    </ItemGroup>
</Project>
```


**Please be aware that the in F#, file order is important.** For instance, if file `A.fs` calls `B.fs` then you should reference `B.fs` first. Some editor can do it for you though.

</li>
</ul>

## How does it work?

### Your files

Your files must be added following the rule:

```xml
<ItemGroup>
    <Compile Include="path/to/my/File.fs" />
<ItemGroup>
```

For instance, for an `App.fs` file located in the same folder with `App.fsproj`

```xml
<ItemGroup>
    <Compile Include="App.fs" />
<ItemGroup>
```

Let's add another file, `MyAwesomeFeature.fs`:

```xml
<ItemGroup>
    <Compile Include="MyAwesomeFeature.fs" />
    <Compile Include="App.fs" />
<ItemGroup>
```

Let's add another file, `Authentication.fs`, located in another folder `Shared` which is at the same depth than our `src` folder. Let's see the current state of our project tree:

```
myproject
    |_ src
        |_ App.fs
        |_ MyAwesomeFeature.fs
        |_ App.fsproj
    |_ Shared
        |_ Authentication.fs
```

This will result in:

```xml
<ItemGroup>
    <Compile Include="MyAwesomeFeature.fs" />
    <Compile Include="../Shared/Authentication.fs" />
    <Compile Include="App.fs" />
<ItemGroup>
```
