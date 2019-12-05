---
title: Reverse Engineering-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/13/2018
ms.assetid: 6263EF7D-4989-42E6-BDEE-45DA770342FB
uid: core/managing-schemas/scaffolding
ms.openlocfilehash: 1ba9352d261f1c131b0d70f8cdad2128d9afaefe
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824463"
---
# <a name="reverse-engineering"></a>Reverse Engineering

Il Reverse Engineering è il processo di impalcature di classi di tipi di entità e una classe DbContext basata su uno schema di database. Può essere eseguita usando il comando `Scaffold-DbContext` degli strumenti di EF Core Console di gestione pacchetti (PMC) o il comando `dotnet ef dbcontext scaffold` degli strumenti dell'interfaccia della riga di comando di .NET (CLI).

## <a name="installing"></a>Installazione del

Prima di reverse engineering, è necessario installare gli strumenti di [PMC](xref:core/miscellaneous/cli/powershell) (solo Visual Studio) o gli [strumenti dell'interfaccia](xref:core/miscellaneous/cli/dotnet)della riga di comando. Per informazioni dettagliate, vedere i collegamenti.

Sarà inoltre necessario installare un [provider di database](xref:core/providers/index) appropriato per lo schema del database che si desidera decodificare.

## <a name="connection-string"></a>Stringa di connessione

Il primo argomento del comando è una stringa di connessione al database. Questa stringa di connessione verrà utilizzata dagli strumenti per leggere lo schema del database.

La modalità di citazione e di escape della stringa di connessione dipende dalla shell utilizzata per eseguire il comando. Per informazioni dettagliate, vedere la documentazione della shell. PowerShell, ad esempio, richiede l'escape del carattere di `$`, ma non `\`.

``` powershell
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer
```

```dotnetcli
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer
```

### <a name="configuration-and-user-secrets"></a>Configurazione e segreti utente

Se si dispone di un progetto di ASP.NET Core, è possibile utilizzare la sintassi `Name=<connection-string>` per leggere la stringa di connessione dalla configurazione.

Questo funziona bene con lo [strumento di gestione dei segreti](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager) per evitare che la password del database sia separata dalla codebase.

```dotnetcli
dotnet user-secrets set ConnectionStrings.Chinook "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook"
dotnet ef dbcontext scaffold Name=Chinook Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="provider-name"></a>Nome provider

Il secondo argomento è il nome del provider. Il nome del provider è in genere uguale al nome del pacchetto NuGet del provider.

## <a name="specifying-tables"></a>Specifica di tabelle

Per impostazione predefinita, tutte le tabelle nello schema del database sono decodificate in tipi di entità. È possibile limitare le tabelle decodificate specificando schemi e tabelle.

Il parametro `-Schemas` in PMC e l'opzione `--schema` nell'interfaccia della riga di comando possono essere utilizzati per includere ogni tabella all'interno di uno schema.

è possibile usare `-Tables` (PMC) e `--table` (CLI) per includere tabelle specifiche.

Per includere più tabelle in PMC, usare una matrice.

``` powershell
Scaffold-DbContext ... -Tables Artist, Album
```

Per includere più tabelle nell'interfaccia della riga di comando, specificare l'opzione più volte.

```dotnetcli
dotnet ef dbcontext scaffold ... --table Artist --table Album
```

## <a name="preserving-names"></a>Conservazione dei nomi

Per impostazione predefinita, i nomi delle tabelle e delle colonne sono corretti per una migliore corrispondenza con le convenzioni di denominazione .NET per i tipi e le proprietà. Se si specifica l'opzione `-UseDatabaseNames` nell'interfaccia della riga di comando o l'opzione `--use-database-names` nell'interfaccia della riga di comando, questo comportamento verrà disabilitato nel modo più possibile mantenendo i nomi del database originale. Gli identificatori .NET non validi verranno comunque corretti e i nomi sintetizzati, come le proprietà di navigazione, saranno comunque conformi alle convenzioni di denominazione .NET.

## <a name="fluent-api-or-data-annotations"></a>API Fluent o annotazioni dei dati

Per impostazione predefinita, i tipi di entità vengono configurati tramite l'API Fluent. Specificare `-DataAnnotations` (PMC) o `--data-annotations` (CLI) per usare invece le annotazioni dei dati, quando possibile.

Se ad esempio si usa l'API Fluent, l'impalcatura è la seguente:

``` csharp
entity.Property(e => e.Title)
    .IsRequired()
    .HasMaxLength(160);
```

Quando si usano le annotazioni dei dati, questo è l'impalcatura:

``` csharp
[Required]
[StringLength(160)]
public string Title { get; set; }
```

## <a name="dbcontext-name"></a>Nome DbContext

Il nome della classe DbContext con impalcature sarà il nome del database con suffisso *per impostazione predefinita* . Per specificare un valore diverso, usare `-Context` in PMC e `--context` nell'interfaccia della riga di comando.

## <a name="directories-and-namespaces"></a>Directory e spazi dei nomi

Le classi di entità e una classe DbContext sono impalcature nella directory radice del progetto e usano lo spazio dei nomi predefinito del progetto. È possibile specificare la directory in cui le classi sono sottoposto a impalcatura usando `-OutputDir` (PMC) o `--output-dir` (CLI). Lo spazio dei nomi sarà lo spazio dei nomi radice più i nomi delle sottodirectory nella directory radice del progetto.

È anche possibile usare `-ContextDir` (PMC) e `--context-dir` (CLI) per eseguire il patibolo della classe DbContext in una directory separata dalle classi del tipo di entità.

``` powershell
Scaffold-DbContext ... -ContextDir Data -OutputDir Models
```

```dotnetcli
dotnet ef dbcontext scaffold ... --context-dir Data --output-dir Models
```

## <a name="how-it-works"></a>Funzionamento

Il reverse engineering inizia con la lettura dello schema del database. Legge le informazioni su tabelle, colonne, vincoli e indici.

USA quindi le informazioni sullo schema per creare un modello di EF Core. Le tabelle vengono utilizzate per creare i tipi di entità. le colonne vengono utilizzate per creare le proprietà; e le chiavi esterne vengono utilizzate per creare relazioni.

Infine, il modello viene utilizzato per generare il codice. Le classi del tipo di entità, l'API Fluent e le annotazioni dei dati corrispondenti sono basate su impalcature per ricreare lo stesso modello dall'app.

## <a name="limitations"></a>Limitazioni

* Non tutti gli elementi di un modello possono essere rappresentati utilizzando uno schema di database. Ad esempio, le informazioni sulle [**gerarchie di ereditarietà**](../modeling/inheritance.md), i [**tipi di proprietà**](../modeling/owned-entities.md)e la suddivisione delle [**tabelle**](../modeling/table-splitting.md) non sono presenti nello schema del database. Per questo motivo, questi costrutti non verranno mai decodificati.
* Inoltre, **alcuni tipi di colonna** potrebbero non essere supportati dal provider EF core. Queste colonne non verranno incluse nel modello.
* È possibile definire i [**token di concorrenza**](../modeling/concurrency.md)in un modello di EF core per impedire a due utenti di aggiornare la stessa entità nello stesso momento. Alcuni database hanno un tipo speciale per rappresentare questo tipo di colonna (ad esempio, rowversion in SQL Server), nel qual caso è possibile decompilare queste informazioni; Tuttavia, altri token di concorrenza non verranno decodificati.
* [La C# funzionalità a 8 tipi di riferimento Nullable](/dotnet/csharp/tutorials/nullable-reference-types) non è attualmente supportata in Reverse Engineering: EF core genera C# sempre il codice che presuppone che la funzionalità sia disabilitata. Ad esempio, le colonne di testo Nullable verranno sottoposto a impalcatura come proprietà con tipo `string`, non `string?`, con l'API Fluent o le annotazioni dei dati utilizzate per configurare se una proprietà è obbligatoria o meno. È possibile modificare il codice con impalcature e sostituirle C# con annotazioni di valori null. Il supporto dell'impalcatura per i tipi di riferimento nullable viene rilevato da Issue [#15520](https://github.com/aspnet/EntityFrameworkCore/issues/15520).

## <a name="customizing-the-model"></a>Personalizzazione del modello

Il codice generato da EF Core è il codice. È possibile modificarlo. Verrà rigenerato solo se si esegue nuovamente la decompilazione dello stesso modello. Il codice con impalcatura rappresenta *un* modello che può essere utilizzato per accedere al database, ma non è certo l' *unico* modello che può essere utilizzato.

Personalizzare le classi del tipo di entità e la classe DbContext in base alle esigenze. È ad esempio possibile scegliere di rinominare tipi e proprietà, introdurre gerarchie di ereditarietà o suddividere una tabella in più entità. È inoltre possibile rimuovere indici non univoci, sequenze inutilizzate e proprietà di navigazione, proprietà scalari facoltative e nomi di vincoli dal modello.

È anche possibile aggiungere ulteriori costruttori, metodi, proprietà e così via. uso di un'altra classe parziale in un file separato. Questo approccio funziona anche quando si intende decodificare nuovamente il modello.

## <a name="updating-the-model"></a>Aggiornamento del modello

Dopo aver apportato modifiche al database, potrebbe essere necessario aggiornare il modello di EF Core per riflettere tali modifiche. Se le modifiche apportate al database sono semplici, potrebbe essere più semplice apportare manualmente le modifiche al modello di EF Core. Ad esempio, la ridenominazione di una tabella o di una colonna, la rimozione di una colonna o l'aggiornamento del tipo di una colonna sono semplici modifiche da apportare nel codice.

Le modifiche più significative, tuttavia, non sono semplici da creare manualmente. Un flusso di lavoro comune è quello di decompilare nuovamente il modello dal database usando `-Force` (PMC) o `--force` (CLI) per sovrascrivere il modello esistente con uno aggiornato.

Un'altra funzionalità comunemente richiesta è la possibilità di aggiornare il modello dal database mantenendo la personalizzazione, ad esempio le rinominazioni, le gerarchie dei tipi e così via. Utilizzare Issue [#831](https://github.com/aspnet/EntityFrameworkCore/issues/831) per tenere traccia dello stato di avanzamento di questa funzionalità.

> [!WARNING]
> Se si esegue di nuovo la decompilazione del modello dal database, tutte le modifiche apportate ai file andranno perse.
