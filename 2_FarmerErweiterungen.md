# 9.12.2021 DevOpenSpace - Workshop - Farmer und AzureTackle - Azure Cloud Data Operations

# Farmer Erweiterungen, Ausblick Container Apps 

## 1. Storage Account erstellen

Bitte ergänzt die `Program.fs` um eine neue Computational Expression (CE) für die Erstellung eines neuen Storage Accounts. 
Bitte fügt in die StorageAccountConfig ein Storage Type eurer Wahl hinzu.
Bitte fügt 2 Tabellen und 2 Queues hinzu.

Bitte fügt die Ressource in die `arm` CE hinzu.

Veröffentlichung die neuen RessourceGroupConfig mit:

```bash
dotnet run
```

## 1. SQL Server hinzufügen

Nun wollen wir unser App mit einem SQL Server ausstatten. 
WOLLEN WIR DAS WIRKLICH? 

Wir wollen die mit Farmer die webApp veröffentlichen.
Bitte passt die `Program.fs` Datei wie folgt an:
:scream: :money:
```fs
open Farmer
open Farmer.Builders
open Farmer.WebApp
let web = webApp {
        name "DOSWorkshop"
        operating_system Linux
        sku Sku.B1
        https_only
        zip_deploy "deploy"
    }
let deployment = arm {
    location Location.CanadaEast
    add_resource web
}

deployment
|> Deploy.execute "DOSWorkshop" Deploy.NoParameters
|> ignore
```
