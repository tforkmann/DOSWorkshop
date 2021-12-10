# 9.12.2021 DevOpenSpace - Workshop - Farmer und AzureTackle - Azure Cloud Data Operations

# Einleitung AzureTackle

## 1. Warum AzureTackle

* Azure Tabelle sind verdammt billig, aber nicht so leicht zu handhaben
* WindowsAzure SKD ist relative umständlich
* inspiriert von [DustyTables](https://github.com/Zaid-Ajaj/DustyTables)
* vereinfachter Azure Table Zugang
* bei meinen alten Arbeitgeber bei fast allen Produktiven Systemen verwendet

## 2. Funktionalitäten

### AzureProps

```fs
type AzureTableConfig =
    {   AzureTable: CloudTable option
        TableName: string option
        AzureAccount: AzureAccount option }

type StorageOption =
    {   Normal: AzureTableConfig
            Backup: AzureTableConfig option }
type TableProps =
    {   Filters: AzureFilter list
        FilterReceive: (string * string) option
        StorageOption: StorageOption option }
```

### Filter

```fs
type Operator =
| LessThan
| LessThanOrEqual
| GreaterThan
| GreaterThanOrEqual
| Equal
| NotEqual

type AzureFilter =
| Flt of string * Operator * float
| Txt of string * Operator * string
| Dtm of string * Operator * DateTime
| DtmO of string * Operator * DateTimeOffset
| PaKey of Operator * string 
| RoKey of Operator * string
| TStmp of Operator * DateTimeOffset
```

### Erstellung / Verbindung mit einer Tabelle

```fs
    connectionString()
    |> AzureTable.connect
    |> AzureTable.table "TestTable"
```

### Tabellen Entitäten einfügen

* wichtig immer PartitionKey und RowKey

```fs
let testData =
    {   PartKey = "PartKey"
        RowKey = DateTime.UtcNow |> RowKey.toRowKey
        Date = DateTime.UtcNow |> System.DateTimeOffset
        Value = 0.2
        Exists = true
        Text = "isWorking" }
do!
    connectionString()
    |> AzureTable.connect
    |> AzureTable.table TestTable
    |> AzureTable.insert
    (testData.PartKey, testData.RowKey)
        (fun set ->
            set.dateTimeOffset ("Date",testData.Date) |> ignore
            set.float ("Value",testData.Value) |> ignore
            set.bool ("Exists",testData.Exists) |> ignore
            set.string ("Text",testData.Text)
            )
```

### Tabellen Entitäten löschen

```fs
do!
    connectionString()
    |> AzureTable.connect
    |> AzureTable.table TestTable
    |> AzureTable.delete ("partKey", "rowKey")
```

### Tabellen Abfragen

* Filter und Mapping in einem Befehl

```fs
open AzureTackle

// get the connection from the environment
let connectionString() = Env.getVar "app_db"

type TestData =
    { PartKey: string
    RowKey: RowKey
    Date: DateTimeOffset
    Exists: bool
    Value: float
    Text: string }

let! values =
    connectionString()
    |> AzureTable.connect
    |> AzureTable.table testTable
    |> AzureTable.filter [DtmO ("Date",GreaterThenOrEqual, timeModel.DateStart);DtmO ("Date",LessThen, timeModel.DateEnd)]
    |> AzureTable.executeDirect (fun read ->
    {   PartKey = read.partKey
        RowKey = read.rowKey
        Date = read.dateTimeOffset "Date"
        Value = read.float "Value"
        Text = read.string "Text" })
```

### 3. Ausblick AzureTackle.Analyzer

[AzureTackleAnalyzer](https://twitter.com/tforkmann/status/1343317937478893569/photo/1)
* Verwendung um die Azure abfragen in CompileTime zu überprüfen
* Ziel: Reduzierung der Fehler vor dem Publish
* leider keine Zeit gehabt das weiterzuentwickeln
