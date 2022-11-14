# 18.11.2022 DevOpenSpace - Workshop - Farmer und AzureTackle - Azure Cloud Data Operations

# Implementierung AzureTackle und Feliz.Daisy

## appsettings.json Datei anlegen

Bitte zuerst im Verzeichnis `src/Server` eine `appsettings.json` Datei anlegen und den Azure ConnectionString zum Azure StorageAccount hinzufügen.

```json
{
    "ConnectionString": "Connectionstring"
}
```
## Infrastructure.fs Datei anlegen

Eine weitere Datei `Infrastructure.fs` in `src/Sever` hinzufügen. Und folgende Inhalt hinzufügen:

```fs
module Infrastructure

open Microsoft.Extensions.Configuration
open System.IO
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

Im Anschluss müssen noch die  `Infrastructure.fs` und `appsettings.json` ins `Server.fsproj` Projekt hinzugefügt werden.

Die Projektdatei sollte dann wie folgt aussehen:
```fs
    <?xml version="1.0" encoding="utf-8"?>
    <Project Sdk="Microsoft.NET.Sdk.Web">
        <PropertyGroup>
            <OutputType>Exe</OutputType>
            <TargetFramework>net6.0</TargetFramework>
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

weitere Schritte:

1. Neuen Typen `ContactInfo` in der `Shared.fs` hinzufügen
```fs
type ContactInfo =
    { ContactDate : DateTimeOffset
      FullName: string
      PhoneNumber: string
      Message: string
      EmailAddress: string }

```
2. Fable.Remote Api in `Shared.fs` anpassen:
```fs
type IDOSAzureTackleApi =
    { GetContactInfos: unit -> Async<ContactInfo []>
      AddContactInfo: ContactInfo -> Async<unit> }
```
3. module TablesNames in `Infrastructure.fs` hinzufügen
```fs
module TableNames =
    [<Literal>]
    let ContactInfo = "ContactInfo"
```
4. `ContactInfo.fs` Datei zum `Server.fsproj` hinzufügen
```fs
module ContactInfo

open Infrastructure
open AzureTackle
open Shared
open FSharp.Control.Tasks.ContextInsensitive

/// Table Structure
/// PartKey FullName RowKey ContactDate //SortableRowKey -- increase performance of query

module Add =
    let saveContractInfo (contractInfo: ContactInfo) =
        try
            // printfn "contractInfo %A" contractInfo

            let rowKey =
                contractInfo.ContactDate.DateTime
                |> RowKey.toRowKey

            azureConn
            |> AzureTable.table TableNames.ContactInfo
            |> AzureTable.insertCustomRowKey (contractInfo.FullName, rowKey.GetValue) (fun set ->
                set.string "EmailAddress" contractInfo.EmailAddress
                set.string "PhoneNumber" contractInfo.PhoneNumber
                set.string "Message" contractInfo.Message
                set.returnEntity)
        with
        | exn ->
            printfn "failed to saveContractInfo: %A" exn
            failwithf "failed to savecontractInfo : %A" exn

    let addContractInfo contractInfo =
        task {
            let! result = saveContractInfo contractInfo

            return
                result
                |> function
                    | Ok _ -> ()
                    | Error exn ->
                        printfn "failed to saveContractInfo: %A" exn
                        failwithf "failed to savecontractInfo : %A" exn
        }
        |> Async.AwaitTask

module Get =
    let contacts () =
        task {
            let! contacts =
                azureConn
                |> AzureTable.table TableNames.ContactInfo
                |> AzureTable.execute (fun read ->
                    { EmailAddress = read.string "EmailAddress"
                      FullName = read.partKey
                      Message = read.string "Message"
                      PhoneNumber = read.string "PhoneNumber"
                      ContactDate =
                        read.rowKey
                        |> RowKey.toDate
                        |> System.DateTimeOffset })

            return
                contacts
                |> function
                    | Ok contacts -> contacts
                    | Error exn ->
                        printfn "failed to getContacts: %A" exn
                        failwithf "failed to getContacts : %A" exn
        }
        |> Async.AwaitTask

    let contactsUnsafe () =
        task {
            return!
                azureConn
                |> AzureTable.table TableNames.ContactInfo
                |> AzureTable.executeDirect (fun read ->
                    { EmailAddress = read.string "EmailAddress"
                      FullName = read.partKey
                      Message = read.string "Message"
                      PhoneNumber = read.string "PhoneNumber"
                      ContactDate =
                        read.rowKey
                        |> RowKey.toDate
                        |> System.DateTimeOffset })
        }
        |> Async.AwaitTask
```


5. ServerApi in `Server.fs` anpassen
```fs
let dosAzureTackleApi =
    { AddContactInfo = ContactInfo.Add.addContractInfo
      GetContactInfos = ContactInfo.Get.contacts}
```

Server Seite check! :party:

6. Jetzt in `src/Client` muss die Client Seite angepasst werden.
Anpassung der Index.fs wie folgt:

7. Anpassung Model:
```fs
type Model = { ContactInfos: ContactInfo [];ContactInfo: ContactInfo ; Error : exn option }
```

8. Anpassung der Msg:
```fs
type Msg =
    | GotContactInfos of ContactInfo []
    | AddContactInfo of ContactInfo
    | FailedContactInfo of exn
    | UpdateFullName of string
    | UpdatePhoneNumber of string
    | UpdateMessage of string
    | UpdateEmailAddress of string
    | SavedContactInfo of unit
    | SavedContactInfoFailed of exn
```

9. Anpassung der Remoting Api
```fs
let dosAzureTackleApi =
    Remoting.createApi ()
    |> Remoting.withRouteBuilder Route.builder
    |> Remoting.withBinarySerialization
    |> Remoting.buildProxy<IDOSAzureTackleApi>
```

10. den Rest zeig ich Euch live...


