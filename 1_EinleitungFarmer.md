# 18.11.2022 DevOpenSpace - Workshop - Farmer und AzureTackle - Azure Cloud Data Operations

# Einleitung Farmer

## Voraussetzungen
1. Visual Studio Code (Windows, Linux oder MacOS)
2. Ionide F# plugin (Ionide-fsharp) by Ionide
3. Grundlegendes Verständnis von F#
4. .NET Core version 6.0 oder später
5. Einfaches Azure Abonnement

## Wichtig: Tipps und Tricks mit Ionide

Ionide spinnt leider manchmal etwas rum. Daher hier die wichtigsten Tipps und Tricks.
1. Lösung 1 obj - Ordner löschen
* Um alle obj Ordner zu löschen installiert bitte folgenden dotnet tool: `dotnet tool install -g removeobj`
* Zum Löschen von .obj Dateien im Projektordner könnt Ihr folgenden Befehl ausführen: `removeob`
2. Lösung 2 bin - Ordner löschen
3. Lösung 3 Visual Studio Code neustarten
4. Lösung 4 `dotnet restore` ausführen

## 1. Aufbau der Grundlegen Azure Infrastruktur mit Farmer

Für die Einrichtung von Farmer benötigen wir ein Azure Subscription und eine F# Konsolenanwendung.
Für die nuget Paketbereitstellung nutzen wir Paket. Paket installieren wir als dotnet tool.
Bitte führt folgende Befehle aus:

```bash
mkdir infrastructure
dotnet new console -lang F#
dotnet new tool-manifest
dotnet tool install paket
dotnet paket init
dotnet paket add Farmer
```

Wir wollen nun mit Farmer die webApp veröffentlichen.
Bitte passt die `Program.fs` Datei wie folgt an:

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

Nun überprüfen wir ob die Konsolenanwendung korrekt kompiliert wurde.
Führt bitte folgenden Befehl aus:

```bash
dotnet watch build
```

Wenn die Konsolenanwendung korrekt kompiliert wurde, kann die WebApp mit folgendem Befehl veröffentlicht werden:

```bash
dotnet run
```

Bitte öffnet nun das Azure Portal und überprüft ob die WebApp korrekt angezeigt wird.

Herzlichen Glückwunsch Ihr habe Eure erste Azure Cloud Infrastruktur mit Farmer erstellt.
