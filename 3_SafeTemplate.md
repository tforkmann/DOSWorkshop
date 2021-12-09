# 9.12.2021 DevOpenSpace - Workshop - Farmer und AzureTackle - Azure Cloud Data Operations

# SafeTemplate

## 2. Scaffolding a project with SAFE template
Now that we verified out tools, let's create an empty Fable project that we will incrementally enhance with features we need. The easiest is to use one of available scaffolding templates, and we will use SAFE template. Read more about SAFE Stack [here](https://safe-stack.github.io/). SAFE stands for Saturn-Azure-Fable-Elmish, but we will only be using SFE from its st        ack.

First install SAFE templates:
```
dotnet new -i SAFE.Template
```
Now generate a new Saturn/Fable/Elmish project with minimal number of features (option "-m"):
```
dotnet new SAFE -m -o DOSWorkshop
```
This will create a new folder DOSWorkshop with project files. Checkout its README.md file and the `src` folder. The source folder contains three subfolders: `Client`, `Server` and `Shared`.

Let's build the server first. Open the project folder in Visual Studio Code, Start the Terminal (you can start two Terminal instances: for server and client), and do the following:
```
cd src/Server
dotnet run
```

## Add new fs file infrastructure in src/Server

Add

```fs
open AzureTackle

// get the connection from the environment
let config =
    ConfigurationBuilder()
        .SetBasePath(Directory.GetCurrentDirectory())
        .AddJsonFile("appsettings.json")
        .Build()

let connectionString = config.["ConnectionString"]
let azureProps = AzureTable.connect connectionString()
```

And following to your Server.fsproj file

```fs
    <None Include="appsettings.json">
      <CopyToOutputDirectory>Always</CopyToOutputDirectory>
    </None>
```

and add the fs file to fsproj
```fs
    <Compile Include="Infrastructure.fs" />
````

the Server.fsproj file then should look like this:

```fs
    <?xml version="1.0" encoding="utf-8"?>
    <Project Sdk="Microsoft.NET.Sdk.Web">
        <PropertyGroup>
            <OutputType>Exe</OutputType>
            <TargetFramework>net5.0</TargetFramework>
        </PropertyGroup>
        <ItemGroup>
            <None Include="paket.references" />
            <Compile Include="Infrastructure.fs" />
            <Compile Include="Server.fs" />
            <None Include="appsettings.json">
                <CopyToOutputDirectory>Always</CopyToOutputDirectory>
            </None>
        </ItemGroup>
        <ItemGroup>
            <ProjectReference Include="..\Shared\Shared.fsproj" />
        </ItemGroup>
        <Import Project="..\..\.paket\Paket.Restore.targets" />
    </Project>
```


Add new module to Infrastructure.fs

```fs
module TableNames =
    [<Literal>]
    let Counter = "Counter"
```

Add new Counter file to Server.fs

