# 9.12.2021 DevOpenSpace - Workshop - Farmer und AzureTackle - Azure Cloud Data Operations

# SAFE-Stack, erste SAFE-Stack app, Fable-Remoting

## Wofür steh SAFE-Stack?

vier separate Komponenten
- Saturn, F# Webentwicklung Bibliothek geschrieben in F#
- Azure, umfangreiches Set von Cloud Services
- Fable, F#-to-JavaScript Compiler
- Elmish

## Warum SAFE-Stack?

Vorteile vom SAFE Stack:
- einfache und schnelle Webentwicklung
- baut auf Industrie Standards auf
- extrem Produktiv
- Server, Client typen sind miteinander gekoppelt
- Type-sicher
- leicht anpassungsfähig
- Cloud-ready

Mehr seht in der Dokumentation: [SAFE-Stack](https://safe-stack.github.io/)

## Erste SAFE-Stack app erstellen

Als erstes installiert ihr bitte das SAFE templates:

```bash
dotnet new -i SAFE.Template
```

Als nächstes erstellen wir ein SAFE-Stack Projekt mit dem Namen `DOSWorkshop`
```bash
dotnet new SAFE -o DOSWorkshop
```

Als nächste gehen wir in das soeben erstellte Verzeichnis:
```
cd DOSWorkshop
````

und führen die App aus

```bash
dotnet run
```

Ok cool...das war schon

## Erklärung der Komponenten im Quellcode

