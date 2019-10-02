---
title: Nuove funzionalità in Entity Framework Core 3.0
author: divega
ms.date: 02/19/2019
ms.assetid: 2EBE2CCC-E52D-483F-834C-8877F5EB0C0C
uid: core/what-is-new/ef-core-3.0/index
ms.openlocfilehash: ce53d0fa21acfd52dc5e8b37911202cab02f00c8
ms.sourcegitcommit: 6c28926a1e35e392b198a8729fc13c1c1968a27b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71813466"
---
# <a name="new-features-in-entity-framework-core-30"></a><span data-ttu-id="6764c-102">Nuove funzionalità in Entity Framework Core 3.0</span><span class="sxs-lookup"><span data-stu-id="6764c-102">New features in Entity Framework Core 3.0</span></span>

<span data-ttu-id="6764c-103">L'elenco seguente include le nuove principali funzionalità di EF Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="6764c-103">The following list includes the major new features in EF Core 3.0.</span></span>

<span data-ttu-id="6764c-104">Essendo una versione principale, EF Core 3.0 contiene anche alcune [modifiche di rilevo](xref:core/what-is-new/ef-core-3.0/breaking-changes), vale a dire miglioramenti delle API che possono avere un impatto negativo sulle applicazioni esistenti.</span><span class="sxs-lookup"><span data-stu-id="6764c-104">As a major release, EF Core 3.0 also contains several [breaking changes](xref:core/what-is-new/ef-core-3.0/breaking-changes), which are API improvements that may have negative impact on existing applications.</span></span>  

## <a name="linq-overhaul"></a><span data-ttu-id="6764c-105">Revisione di LINQ</span><span class="sxs-lookup"><span data-stu-id="6764c-105">LINQ overhaul</span></span> 

<span data-ttu-id="6764c-106">LINQ consente di scrivere query di database usando il linguaggio .NET preferito, sfruttando i vantaggi di informazioni complete per offrire IntelliSense e il controllo del tipo in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="6764c-106">LINQ enables you to write database queries using your .NET language of choice, taking advantage of rich type information to offer IntelliSense and compile-time type checking.</span></span>
<span data-ttu-id="6764c-107">LINQ consente anche di scrivere un numero illimitato di query complesse contenenti espressioni arbitrarie (chiamate al metodo oppure operazioni).</span><span class="sxs-lookup"><span data-stu-id="6764c-107">But LINQ also enables you to write an unlimited number of complicated queries containing arbitrary expressions (method calls or operations).</span></span>
<span data-ttu-id="6764c-108">La difficoltà principale per i provider LINQ è gestire tutte queste combinazioni.</span><span class="sxs-lookup"><span data-stu-id="6764c-108">How to handle all those combinations is the main challenge for LINQ providers.</span></span>

<span data-ttu-id="6764c-109">In EF Core 3.0 il provider LINQ è stato riprogettato per consentire la conversione di più modelli di query in SQL, in modo da generare query efficienti in più casi e impedire che query inefficienti non siano rilevate.</span><span class="sxs-lookup"><span data-stu-id="6764c-109">In EF Core 3.0, we rearchitected our LINQ provider to enable translating more query patterns into SQL, generating efficient queries in more cases, and preventing inefficient queries from going undetected.</span></span> <span data-ttu-id="6764c-110">Il nuovo provider LINQ è la base che consentirà di offrire nuove funzionalità di query e miglioramenti delle prestazioni nelle versioni future, senza compromettere le applicazioni e i provider di dati esistenti.</span><span class="sxs-lookup"><span data-stu-id="6764c-110">The new LINQ provider is the foundation over which we'll be able to offer new query capabilities and performance improvements in future releases, without breaking existing applications and data providers.</span></span>

### <a name="restricted-client-evaluation"></a><span data-ttu-id="6764c-111">Valutazione client limitata</span><span class="sxs-lookup"><span data-stu-id="6764c-111">Restricted client evaluation</span></span>
<span data-ttu-id="6764c-112">La modifica più importante in termini di progettazione ha a che fare con la gestione delle espressioni LINQ che non possono essere convertite in parametri o in SQL.</span><span class="sxs-lookup"><span data-stu-id="6764c-112">The most important design change has to do with how we handle LINQ expressions that cannot be converted to parameters or translated to SQL.</span></span>

<span data-ttu-id="6764c-113">Nelle versioni precedenti EF Core identificava le parti di query che potevano essere convertite in SQL ed eseguiva il resto della query nel client.</span><span class="sxs-lookup"><span data-stu-id="6764c-113">In previous versions, EF Core identified what portions of a query could be translated to SQL, and executed the rest of the query on the client.</span></span>
<span data-ttu-id="6764c-114">Questo tipo di esecuzione sul lato client è utile in alcune situazioni, ma in molti altri casi può generare query inefficienti.</span><span class="sxs-lookup"><span data-stu-id="6764c-114">This type of client-side execution is desirable in some situations, but in many other cases it can result in inefficient queries.</span></span>

<span data-ttu-id="6764c-115">Ad esempio, se in EF Core 2.2 non era possibile convertire un predicato in una chiamata `Where()`, veniva eseguita un'istruzione SQL senza filtro, tutte le righe venivano trasferite dal database e poi filtrate in memoria:</span><span class="sxs-lookup"><span data-stu-id="6764c-115">For example, if EF Core 2.2 couldn't translate a predicate in a `Where()` call, it executed an SQL statement without a filter, transferred all the rows from the database, and then filtered them in-memory:</span></span>

``` csharp
var specialCustomers = 
  context.Customers
    .Where(c => c.Name.StartsWith(n) && IsSpecialCustomer(c));
```

<span data-ttu-id="6764c-116">Questo approccio può essere accettabile se il database contiene un numero ridotto di righe, ma può causare problemi di prestazioni significativi o persino un errore dell'applicazione se il database contiene un numero elevato di righe.</span><span class="sxs-lookup"><span data-stu-id="6764c-116">That may be acceptable if the database contains a small number of rows but can result in significant performance issues or even application failure if the database contains a large number or rows.</span></span>

<span data-ttu-id="6764c-117">In EF Core 3.0 la valutazione client è stata limitata in modo che avvenga solo nella proiezione di primo livello, essenzialmente con l'ultima chiamata a `Select()`.</span><span class="sxs-lookup"><span data-stu-id="6764c-117">In EF Core 3.0, we've restricted client evaluation to only happen on the top-level projection (essentially, the last call to `Select()`).</span></span>
<span data-ttu-id="6764c-118">Quando EF Core 3.0 rileva espressioni che non possono essere convertite altrove nella query, viene generata un'eccezione in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="6764c-118">When EF Core 3.0 detects expressions that can't be translated anywhere else in the query, it throws a runtime exception.</span></span>

<span data-ttu-id="6764c-119">Per valutare una condizione del predicato nel client come nell'esempio precedente, gli sviluppatori devono ora cambiare in modo esplicito la valutazione della query in LINQ to Objects:</span><span class="sxs-lookup"><span data-stu-id="6764c-119">To evaluate a predicate condition on the client as in the previous example, developers now need to explicitly switch evaluation of the query to LINQ to Objects:</span></span> 

``` csharp
var specialCustomers =
  context.Customers
    .Where(c => c.Name.StartsWith(n)) 
    .AsEnumerable() // switches to LINQ to Objects
    .Where(c => IsSpecialCustomer(c));
```

<span data-ttu-id="6764c-120">Vedere la [documentazione sulle modifiche di rilievo](xref:core/what-is-new/ef-core-3.0/breaking-changes#linq-queries-are-no-longer-evaluated-on-the-client) per altri dettagli su come vengono influenzate le applicazioni esistenti.</span><span class="sxs-lookup"><span data-stu-id="6764c-120">See the [breaking changes documentation](xref:core/what-is-new/ef-core-3.0/breaking-changes#linq-queries-are-no-longer-evaluated-on-the-client) for more details about how this can affect existing applications.</span></span>

### <a name="single-sql-statement-per-linq-query"></a><span data-ttu-id="6764c-121">Singola istruzione SQL per ogni query LINQ</span><span class="sxs-lookup"><span data-stu-id="6764c-121">Single SQL statement per LINQ query</span></span>

<span data-ttu-id="6764c-122">Esiste un altro aspetto della progettazione che è stato significativamente modificato nella versione 3.0. Ora viene generata sempre una singola istruzione SQL per ogni query LINQ.</span><span class="sxs-lookup"><span data-stu-id="6764c-122">Another aspect of the design that changed significantly in 3.0 is that we now always generate a single SQL statement per LINQ query.</span></span> <span data-ttu-id="6764c-123">Nelle versioni precedenti si generavano più istruzioni SQL in casi specifici, ad esempio per convertire chiamate `Include()` in proprietà di navigazione della raccolta e per convertire le query che seguivano determinati modelli con sottoquery.</span><span class="sxs-lookup"><span data-stu-id="6764c-123">In previous versions, we used to generate multiple SQL statements in certain cases, like to translate `Include()` calls on collection navigation properties and to translate queries that followed certain patterns with subqueries.</span></span> <span data-ttu-id="6764c-124">Sebbene in alcuni casi questo approccio fosse comodo e per `Include()` impediva persino di inviare dati ridondanti in rete, l'implementazione era complessa, alcuni comportamenti erano estremamente inefficienti (N + 1 query) e in talune situazioni poteva succedere che i dati restituiti tra più query non fossero coerenti.</span><span class="sxs-lookup"><span data-stu-id="6764c-124">Although this was in some cases convenient, and for `Include()` it even helped avoid sending redundant data over the wire, the implementation was complex, it resulted in some extremely inefficient behaviors (N+1 queries), and there was situations in which the data returned across multiple queries could be inconsistent.</span></span>

<span data-ttu-id="6764c-125">Analogamente alla valutazione client, se EF Core 3.0 non può convertire una query LINQ in una singola istruzione SQL, viene generata un'eccezione in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="6764c-125">Similarly to client evaluation, if EF Core 3.0 can't translate a LINQ query into a single SQL statement, it throws a runtime exception.</span></span> <span data-ttu-id="6764c-126">Con EF Core è comunque possibile convertire molti dei modelli comuni usati per generare più query in una singola query con JOIN.</span><span class="sxs-lookup"><span data-stu-id="6764c-126">But we made EF Core capable of translating many of the common patterns that used to generate multiple queries to a single query with JOINs.</span></span>

## <a name="cosmos-db-support"></a><span data-ttu-id="6764c-127">Supporto di Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="6764c-127">Cosmos DB support</span></span> 

<span data-ttu-id="6764c-128">Il provider Cosmos DB per EF Core consente agli sviluppatori che hanno familiarità con il modello di programmazione di EF di usare facilmente Azure Cosmos DB come database dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="6764c-128">The Cosmos DB provider for EF Core enables developers familiar with the EF programing model to easily target Azure Cosmos DB as an application database.</span></span> <span data-ttu-id="6764c-129">L'obiettivo è quello di rendere alcuni dei vantaggi di Cosmos DB, ad esempio la distribuzione globale, la disponibilità "AlwaysOn", la scalabilità elastica e la bassa latenza, ancora più accessibili per gli sviluppatori .NET.</span><span class="sxs-lookup"><span data-stu-id="6764c-129">The goal is to make some of the advantages of Cosmos DB, like global distribution, "always on" availability, elastic scalability, and low latency, even more accessible to .NET developers.</span></span> <span data-ttu-id="6764c-130">Nel provider è abilitata la maggior parte delle funzionalità di EF Core, ad esempio il rilevamento delle modifiche automatico, LINQ e le conversioni dei valori, con l'API SQL in Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="6764c-130">The provider enables most EF Core features, like automatic change tracking, LINQ, and value conversions, against the SQL API in Cosmos DB.</span></span>

<span data-ttu-id="6764c-131">Per altri dettagli, vedere la [documentazione sul provider di Cosmos DB](xref:core/providers/cosmos/index).</span><span class="sxs-lookup"><span data-stu-id="6764c-131">See the [Cosmos DB provider documentation](xref:core/providers/cosmos/index) for more details.</span></span>

## <a name="c-80-support"></a><span data-ttu-id="6764c-132">Supporto di C# 8.0</span><span class="sxs-lookup"><span data-stu-id="6764c-132">C# 8.0 support</span></span>

<span data-ttu-id="6764c-133">EF Core 3.0 sfrutta alcune delle [nuove funzionalità di C# 8.0](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-8):</span><span class="sxs-lookup"><span data-stu-id="6764c-133">EF Core 3.0 takes advantage of a couple of the [new features in C# 8.0](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-8):</span></span>

### <a name="asynchronous-streams"></a><span data-ttu-id="6764c-134">Flussi asincroni</span><span class="sxs-lookup"><span data-stu-id="6764c-134">Asynchronous streams</span></span>

<span data-ttu-id="6764c-135">I risultati delle query asincrone vengono ora esposti usando la nuova interfaccia standard `IAsyncEnumerable<T>` e possono essere usati con `await foreach`.</span><span class="sxs-lookup"><span data-stu-id="6764c-135">Asynchronous query results are now exposed using the new standard `IAsyncEnumerable<T>` interface and can be consumed using `await foreach`.</span></span>

``` csharp
var orders = 
  from o in context.Orders
  where o.Status == OrderStatus.Pending
  select o;

await foreach(var o in orders.AsAsyncEnumerable())
{
  Process(o);
} 
```

<span data-ttu-id="6764c-136">Per altri dettagli, vedere la [documentazione sui flussi asincroni in C#](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-8#asynchronous-streams).</span><span class="sxs-lookup"><span data-stu-id="6764c-136">See the [asynchronous streams in the C# documentation](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-8#asynchronous-streams) for more details.</span></span>

### <a name="nullable-reference-types"></a><span data-ttu-id="6764c-137">Tipi riferimento nullable</span><span class="sxs-lookup"><span data-stu-id="6764c-137">Nullable reference types</span></span> 

<span data-ttu-id="6764c-138">Quando questa nuova funzionalità è abilitata nel codice, EF Core esamina il supporto di valori Null delle proprietà del tipo di riferimento e lo applica alle colonne e alle relazioni corrispondenti nel database. Le proprietà di tipi di riferimento non nullable vengono considerate come se avessero l'attributo di annotazione dei dati `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="6764c-138">When this new feature is enabled in your code, EF Core examines the nullability of reference type properties and applies it to corresponding columns and relationships in the database: properties of non-nullable references types are treated as if they had the `[Required]` data annotation attribute.</span></span>

<span data-ttu-id="6764c-139">Ad esempio, nella classe seguente le proprietà contrassegnate come di tipo `string?` verranno configurate come facoltative, mentre quelle di tipo `string` verranno configurate come obbligatorie:</span><span class="sxs-lookup"><span data-stu-id="6764c-139">For example, in the following class, properties marked as of type `string?` will be configured as optional, whereas `string` will be configured as required:</span></span>

``` csharp
public class Customer
{
  public int Id { get; set; }
  public string FirstName { get; set; }
  public string LastName { get; set; }
  public string? MiddleName { get; set; }
}
```

<span data-ttu-id="6764c-140">Per altri dettagli, vedere [Uso dei tipi di riferimento nullable](xref:core/miscellaneous/nullable-reference-types) nella documentazione di EF Core.</span><span class="sxs-lookup"><span data-stu-id="6764c-140">See [Working with nullable reference types](xref:core/miscellaneous/nullable-reference-types) in the EF Core documentation for more details.</span></span>

## <a name="interception-of-database-operations"></a><span data-ttu-id="6764c-141">Intercettazione di operazioni di database</span><span class="sxs-lookup"><span data-stu-id="6764c-141">Interception of database operations</span></span>

<span data-ttu-id="6764c-142">La nuova API di intercettazione in EF Core 3.0 offre una logica personalizzata da richiamare automaticamente quando si verificano operazioni di database di basso livello come parte del normale funzionamento di EF Core,</span><span class="sxs-lookup"><span data-stu-id="6764c-142">The new interception API in EF Core 3.0 allows providing custom logic to be invoked automatically whenever low-level database operations occur as part of the normal operation of EF Core.</span></span> <span data-ttu-id="6764c-143">ad esempio durante l'apertura di connessioni, il commit di transazioni o l'esecuzione di comandi.</span><span class="sxs-lookup"><span data-stu-id="6764c-143">For example, when opening connections, committing transactions, or executing commands.</span></span> 

<span data-ttu-id="6764c-144">Analogamente alle funzionalità di intercettazione in EF 6, gli intercettori consentono di intercettare le operazioni prima o dopo che si verifichino.</span><span class="sxs-lookup"><span data-stu-id="6764c-144">Similarly to the interception features that existed in EF 6, interceptors allow you to intercept operations before or after they happen.</span></span> <span data-ttu-id="6764c-145">Quando vengono intercettate prima che si verifichino, è possibile ignorare l'esecuzione e offrire risultati alternativi dalla logica di intercettazione.</span><span class="sxs-lookup"><span data-stu-id="6764c-145">When you intercept them before they happen, you are allowed to by-pass execution and supply alternate results from the interception logic.</span></span> 

<span data-ttu-id="6764c-146">Per modificare il testo del comando, è ad esempio possibile creare un oggetto `IDbCommandInterceptor`:</span><span class="sxs-lookup"><span data-stu-id="6764c-146">For example, to manipulate command text, you can create an `IDbCommandInterceptor`:</span></span>

``` csharp 
public class HintCommandInterceptor : DbCommandInterceptor
{
  public override InterceptionResult ReaderExecuting(
    DbCommand command, 
    CommandEventData eventData, 
    InterceptionResult result)
  {
    // Manipulate the command text, etc. here...
    command.CommandText += " OPTION (OPTIMIZE FOR UNKNOWN)";
    return result;
  }
}
``` 

<span data-ttu-id="6764c-147">E registrarlo con  `DbContext`:</span><span class="sxs-lookup"><span data-stu-id="6764c-147">And register it with your `DbContext`:</span></span>

``` csharp
services.AddDbContext(b => b
  .UseSqlServer(connectionString)
  .AddInterceptors(new HintCommandInterceptor()));
```

## <a name="reverse-engineering-of-database-views"></a><span data-ttu-id="6764c-148">Decompilazione delle viste di database</span><span class="sxs-lookup"><span data-stu-id="6764c-148">Reverse engineering of database views</span></span>

<span data-ttu-id="6764c-149">I tipi di query che rappresentano dati che possono essere letti dal database, ma che non possono essere aggiornati, sono stati rinominati in [tipi di entità senza chiave](xref:core/modeling/keyless-entity-types).</span><span class="sxs-lookup"><span data-stu-id="6764c-149">Query types, which represent data that can be read from the database but not updated, have been renamed to [keyless entity types](xref:core/modeling/keyless-entity-types).</span></span> <span data-ttu-id="6764c-150">Essendo una soluzione ottimale per il mapping di viste di database nella maggior parte degli scenari, EF Core ora crea automaticamente tipi di entità senza chiave durante la decompilazione delle viste di database.</span><span class="sxs-lookup"><span data-stu-id="6764c-150">As they are an excellent fit for mapping database views in most scenarios, EF Core now automatically creates keyless entity types when reverse engineering database views.</span></span>

<span data-ttu-id="6764c-151">Ad esempio, usando lo [strumento da riga di comando dotnet ef](xref:core/miscellaneous/cli/dotnet) è possibile digitare quanto riportato di seguito:</span><span class="sxs-lookup"><span data-stu-id="6764c-151">For example, using the [dotnet ef command-line tool](xref:core/miscellaneous/cli/dotnet) you can type:</span></span>

``` console
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="6764c-152">Lo strumento ora creerà automaticamente tipi per viste e tabelle senza chiavi:</span><span class="sxs-lookup"><span data-stu-id="6764c-152">And the tool will now automatically scaffold types for views and tables without keys:</span></span>

``` csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
  modelBuilder.Entity<Names>(entity =>
  {
    entity.HasNoKey();
    entity.ToView("Names");
  });

  modelBuilder.Entity<Things>(entity =>
  {
    entity.HasNoKey();
  });
}
```

## <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a><span data-ttu-id="6764c-153">Le entità dipendenti che condividono la tabella con l'entità di sicurezza sono ora facoltative</span><span class="sxs-lookup"><span data-stu-id="6764c-153">Dependent entities sharing the table with the principal are now optional</span></span>

<span data-ttu-id="6764c-154">A partire da EF Core 3.0, se `OrderDetails` è di proprietà di `Order` o mappato in modo esplicito alla stessa tabella, sarà possibile aggiungere un `Order` senza `OrderDetails` e tutte le proprietà di `OrderDetails`, a eccezione della chiave primaria, verranno mappate a colonne che ammettono i valori Null.</span><span class="sxs-lookup"><span data-stu-id="6764c-154">Starting with EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table, it will be possible to add an `Order` without an `OrderDetails` and all of the `OrderDetails` properties, except the primary key will be mapped to nullable columns.</span></span>

<span data-ttu-id="6764c-155">In fase di query, EF Core imposterà `OrderDetails` su `null` se una delle relative proprietà obbligatorie non ha un valore o se non sono presenti proprietà obbligatorie oltre alla chiave primaria e tutte le proprietà sono `null`.</span><span class="sxs-lookup"><span data-stu-id="6764c-155">When querying, EF Core will set `OrderDetails` to `null` if any of its required properties doesn't have a value, or if it has no required properties besides the primary key and all properties are `null`.</span></span>

``` csharp
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public OrderDetails Details { get; set; }
}

[Owned]
public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}
```

## <a name="ef-63-on-net-core"></a><span data-ttu-id="6764c-156">EF 6.3 in .NET Core</span><span class="sxs-lookup"><span data-stu-id="6764c-156">EF 6.3 on .NET Core</span></span>

<span data-ttu-id="6764c-157">Non si tratta di una funzionalità di EF Core 3.0, ma è un aspetto importante per molti dei clienti attuali.</span><span class="sxs-lookup"><span data-stu-id="6764c-157">This isn't really an EF Core 3.0 feature, but we think it is important to many of our current customers.</span></span> 

<span data-ttu-id="6764c-158">Si sa che molte applicazioni esistenti usano versioni precedenti di EF e che la loro conversione in EF Core al solo scopo di sfruttare i vantaggi di .NET Core può richiedere notevoli sforzi.</span><span class="sxs-lookup"><span data-stu-id="6764c-158">We understand that many existing applications use previous versions of EF, and that porting them to EF Core only to take advantage of .NET Core can require a significant effort.</span></span> <span data-ttu-id="6764c-159">Per questo motivo, è stato deciso di convertire la prossima versione di EF 6 in modo che possa essere eseguita in .NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="6764c-159">For that reason, we decided to port the newest version of EF 6 to run on .NET Core 3.0.</span></span> 

<span data-ttu-id="6764c-160">Per altri dettagli, vedere [Novità di EF6](xref:ef6/what-is-new/index).</span><span class="sxs-lookup"><span data-stu-id="6764c-160">For more details, see [what's new in EF 6](xref:ef6/what-is-new/index).</span></span>

## <a name="postponed-features"></a><span data-ttu-id="6764c-161">Funzionalità rimandate</span><span class="sxs-lookup"><span data-stu-id="6764c-161">Postponed features</span></span>

<span data-ttu-id="6764c-162">Alcune funzionalità pianificate originariamente per EF Core 3.0 sono state rimandate alle versioni future:</span><span class="sxs-lookup"><span data-stu-id="6764c-162">Some features originally planned for EF Core 3.0 were postponed to future releases:</span></span>

- <span data-ttu-id="6764c-163">Possibilità di ignorare parti di un modello nelle migrazioni, registrata come [#2725](https://github.com/aspnet/EntityFrameworkCore/issues/2725).</span><span class="sxs-lookup"><span data-stu-id="6764c-163">Ability to ignore parts of a model in migrations, tracked as [#2725](https://github.com/aspnet/EntityFrameworkCore/issues/2725).</span></span>
- <span data-ttu-id="6764c-164">Entità contenitore delle proprietà, registrata come due problemi distinti: [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914) per le entità di tipo condiviso e [#13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610) per il supporto del mapping delle proprietà indicizzate.</span><span class="sxs-lookup"><span data-stu-id="6764c-164">Property bag entities, tracked as two separate issues: [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914) about shared-type entities and [#13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610) about indexed property mapping support.</span></span>
