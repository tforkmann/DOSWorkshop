# 18.11.2022 DevOpenSpace - Workshop - Farmer und AzureTackle - Azure Cloud Data Operations

# Farmer Erweiterungen, Ausblick Container Apps

## 1. Storage Account erstellen

- bitte ergänzt die `Program.fs` um eine neue Computational Expression (CE) für die Erstellung eines neuen Storage Accounts.
- bitte fügt in die StorageAccountConfig ein Storage Type eurer Wahl hinzu.
- bitte fügt 2 Tabellen und 2 Queues hinzu.
- bitte fügt die Storage Account Ressource in die `arm` CE hinzu.
- veröffentlicht die neue RessourceGroupConfig mit:

```bash
dotnet run
```

## 2. SQL Server hinzufügen

Nun wollen wir unser App mit einem SQL Server ausstatten.
WOLLEN WIR DAS WIRKLICH?
:scream:
:moneybag:

- bitte ergänzt die `Program.fs` um eine neue Computational Expression (CE) für die Erstellung eine neuen Sql Servers.
- bitte fügt einen Admin hinzu
- bitte fügt eine Firewall hinzu
- dann können wir unsere Datenbanken anlegen:

```fs
add_databases [
    sqlDb { name "db1" }
    sqlDb { name "db2" }
    sqlDb { name "db3basic"; sku Basic }
    sqlDb { name "db4businessCritical"; sku (BusinessCritical Gen5_2) }
    sqlDb { name "db5Hyperscale"; sku (Hyperscale Gen5_2) }
]
```

Ich an Eurer Stelle würde das jetzt nicht veröffentlichen, weil die SQLServer auf Azure doch etwas Geld kosten.

## 3. EventHub hinzufügen

- bitte ergänzt die `Program.fs` um eine neue Computational Expression (CE) für die Erstellung eine neuen EventHubs.
- der EventHub soll 2 Partitionen haben
- bitte fügt einen Consumer hinzu
- bitte fügt einen `namespace_name` hinzu
- bitte fügt die Storage Account Ressource in die `arm` CE hinzu.

- veröffentlicht die neue RessourceGroupConfig mit:

```bash
dotnet run
```

## 4. Ausblick Farmer ContainerApps

* [ContainerApps](http://www.navision-blog.de/blog/2021/12/06/santa-and-the-container-apps-in-the-cloud/) Santa und die ContainerApps in der Cloud

### Hintergrund
- Entkopplung von Background Prozessen mit ContainerApps
- Ausflug Miro....
- Vorteil ist das Container Apps gut skalieren, aber auch komplett komplett auf null runter gefahren werden
- Steffen führt das in seinen F#Advent genauer aus
- Kombination von ContainerApps mit SAFE Stack Apps wird kommen