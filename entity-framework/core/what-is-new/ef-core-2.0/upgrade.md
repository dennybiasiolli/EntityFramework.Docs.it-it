---
title: Aggiornamento da versioni precedenti a EF Core 2-EF Core
description: Istruzioni e note per l'aggiornamento a Entity Framework Core 2,0
author: divega
ms.date: 08/13/2017
uid: core/what-is-new/ef-core-2.0/upgrade
ms.openlocfilehash: bdc0cfe8c0be4a83f8c78ba2ac66bb1e18cea0f7
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072343"
---
# <a name="upgrading-applications-from-previous-versions-to-ef-core-20"></a><span data-ttu-id="b71bd-103">Aggiornamento di applicazioni da versioni precedenti a EF Core 2,0</span><span class="sxs-lookup"><span data-stu-id="b71bd-103">Upgrading applications from previous versions to EF Core 2.0</span></span>

<span data-ttu-id="b71bd-104">Abbiamo avuto la possibilità di affinare in modo significativo le API e i comportamenti esistenti in 2,0.</span><span class="sxs-lookup"><span data-stu-id="b71bd-104">We have taken the opportunity to significantly refine our existing APIs and behaviors in 2.0.</span></span> <span data-ttu-id="b71bd-105">Ci sono alcuni miglioramenti che possono richiedere la modifica del codice dell'applicazione esistente, sebbene ritengano che per la maggior parte delle applicazioni l'effetto sarà basso, nella maggior parte dei casi la necessità di ricompilare solo e modifiche guidate minime per sostituire le API obsolete.</span><span class="sxs-lookup"><span data-stu-id="b71bd-105">There are a few improvements that can require modifying existing application code, although we believe that for the majority of applications the impact will be low, in most cases requiring just recompilation and minimal guided changes to replace obsolete APIs.</span></span>

<span data-ttu-id="b71bd-106">L'aggiornamento di un'applicazione esistente a EF Core 2,0 potrebbe richiedere:</span><span class="sxs-lookup"><span data-stu-id="b71bd-106">Updating an existing application to EF Core 2.0 may require:</span></span>

1. <span data-ttu-id="b71bd-107">Aggiornamento dell'implementazione .NET di destinazione dell'applicazione a una che supporta .NET Standard 2,0.</span><span class="sxs-lookup"><span data-stu-id="b71bd-107">Upgrading the target .NET implementation of the application to one that supports .NET Standard 2.0.</span></span> <span data-ttu-id="b71bd-108">Per informazioni dettagliate, vedere [implementazioni di .NET supportate](xref:core/platforms/index) .</span><span class="sxs-lookup"><span data-stu-id="b71bd-108">See [Supported .NET Implementations](xref:core/platforms/index) for more details.</span></span>

2. <span data-ttu-id="b71bd-109">Identificare un provider per il database di destinazione compatibile con EF Core 2,0.</span><span class="sxs-lookup"><span data-stu-id="b71bd-109">Identify a provider for the target database which is compatible with EF Core 2.0.</span></span> <span data-ttu-id="b71bd-110">Vedere [EF Core 2,0 richiede un provider di database 2,0 di](#ef-core-20-requires-a-20-database-provider) seguito.</span><span class="sxs-lookup"><span data-stu-id="b71bd-110">See [EF Core 2.0 requires a 2.0 database provider](#ef-core-20-requires-a-20-database-provider) below.</span></span>

3. <span data-ttu-id="b71bd-111">Aggiornamento di tutti i pacchetti di EF Core (Runtime e strumenti) a 2,0.</span><span class="sxs-lookup"><span data-stu-id="b71bd-111">Upgrading all the EF Core packages (runtime and tooling) to 2.0.</span></span> <span data-ttu-id="b71bd-112">Per altri dettagli, vedere [installazione di EF Core](xref:core/get-started/install/index) .</span><span class="sxs-lookup"><span data-stu-id="b71bd-112">Refer to [Installing EF Core](xref:core/get-started/install/index) for more details.</span></span>

4. <span data-ttu-id="b71bd-113">Apportare le modifiche necessarie al codice per compensare le modifiche di rilievo descritte nel resto di questo documento.</span><span class="sxs-lookup"><span data-stu-id="b71bd-113">Make any necessary code changes to compensate for the breaking changes described in the rest of this document.</span></span>

## <a name="aspnet-core-now-includes-ef-core"></a><span data-ttu-id="b71bd-114">ASP.NET Core include ora EF Core</span><span class="sxs-lookup"><span data-stu-id="b71bd-114">ASP.NET Core now includes EF Core</span></span>

<span data-ttu-id="b71bd-115">Le applicazioni associate ad ASP.NET Core 2.0 possono usare EF Core 2.0 senza dipendenze aggiuntive oltre ai provider di database di terze parti.</span><span class="sxs-lookup"><span data-stu-id="b71bd-115">Applications targeting ASP.NET Core 2.0 can use EF Core 2.0 without additional dependencies besides third party database providers.</span></span> <span data-ttu-id="b71bd-116">Tuttavia, le applicazioni destinate alle versioni precedenti di ASP.NET Core necessario eseguire l'aggiornamento a ASP.NET Core 2,0 per poter utilizzare EF Core 2,0.</span><span class="sxs-lookup"><span data-stu-id="b71bd-116">However, applications targeting previous versions of ASP.NET Core need to upgrade to ASP.NET Core 2.0 in order to use EF Core 2.0.</span></span> <span data-ttu-id="b71bd-117">Per ulteriori informazioni sull'aggiornamento delle applicazioni ASP.NET Core a 2,0, vedere [la documentazione ASP.NET Core sull'argomento](/aspnet/core/migration/1x-to-2x/).</span><span class="sxs-lookup"><span data-stu-id="b71bd-117">For more details on upgrading ASP.NET Core applications to 2.0 see [the ASP.NET Core documentation on the subject](/aspnet/core/migration/1x-to-2x/).</span></span>

## <a name="new-way-of-getting-application-services-in-aspnet-core"></a><span data-ttu-id="b71bd-118">Un nuovo modo per ottenere i servizi delle applicazioni in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b71bd-118">New way of getting application services in ASP.NET Core</span></span>

<span data-ttu-id="b71bd-119">Il modello consigliato per le applicazioni Web ASP.NET Core è stato aggiornato per 2,0 in modo da comportare la logica della fase di progettazione EF Core utilizzata in 1. x.</span><span class="sxs-lookup"><span data-stu-id="b71bd-119">The recommended pattern for ASP.NET Core web applications has been updated for 2.0 in a way that broke the design-time logic EF Core used in 1.x.</span></span> <span data-ttu-id="b71bd-120">In precedenza, in fase di progettazione, EF Core tenterà di richiamare `Startup.ConfigureServices` direttamente per accedere al provider di servizi dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="b71bd-120">Previously at design-time, EF Core would try to invoke `Startup.ConfigureServices` directly in order to access the application's service provider.</span></span> <span data-ttu-id="b71bd-121">In ASP.NET Core 2,0, la configurazione viene inizializzata all'esterno della `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="b71bd-121">In ASP.NET Core 2.0, Configuration is initialized outside of the `Startup` class.</span></span> <span data-ttu-id="b71bd-122">Le applicazioni che usano EF Core in genere accedono alla relativa stringa di connessione dalla configurazione, quindi `Startup` da solo non è più sufficiente.</span><span class="sxs-lookup"><span data-stu-id="b71bd-122">Applications using EF Core typically access their connection string from Configuration, so `Startup` by itself is no longer sufficient.</span></span> <span data-ttu-id="b71bd-123">Se si aggiorna un'applicazione ASP.NET Core 1. x, è possibile che venga visualizzato l'errore seguente quando si usano gli strumenti di EF Core.</span><span class="sxs-lookup"><span data-stu-id="b71bd-123">If you upgrade an ASP.NET Core 1.x application, you may receive the following error when using the EF Core tools.</span></span>

> <span data-ttu-id="b71bd-124">Nessun costruttore senza parametri trovato in ' ApplicationContext '.</span><span class="sxs-lookup"><span data-stu-id="b71bd-124">No parameterless constructor was found on 'ApplicationContext'.</span></span> <span data-ttu-id="b71bd-125">Aggiungere un costruttore senza parametri a' ApplicationContext ' o aggiungere un'implementazione di ' IDesignTimeDbContextFactory &lt; ApplicationContext &gt; ' nello stesso assembly di ' ApplicationContext '</span><span class="sxs-lookup"><span data-stu-id="b71bd-125">Either add a parameterless constructor to 'ApplicationContext' or add an implementation of 'IDesignTimeDbContextFactory&lt;ApplicationContext&gt;' in the same assembly as 'ApplicationContext'</span></span>

<span data-ttu-id="b71bd-126">Nel modello predefinito di ASP.NET Core 2.0 è stato aggiunto un nuovo hook della fase di progettazione.</span><span class="sxs-lookup"><span data-stu-id="b71bd-126">A new design-time hook has been added in ASP.NET Core 2.0's default template.</span></span> <span data-ttu-id="b71bd-127">Il `Program.BuildWebHost` metodo statico consente EF Core di accedere al provider di servizi dell'applicazione in fase di progettazione.</span><span class="sxs-lookup"><span data-stu-id="b71bd-127">The static `Program.BuildWebHost` method enables EF Core to access the application's service provider at design time.</span></span> <span data-ttu-id="b71bd-128">Se si sta aggiornando un'applicazione ASP.NET Core 1. x, sarà necessario aggiornare la `Program` classe in modo che sia simile alla seguente.</span><span class="sxs-lookup"><span data-stu-id="b71bd-128">If you are upgrading an ASP.NET Core 1.x application, you will need to update the `Program` class to resemble the following.</span></span>

``` csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;

namespace AspNetCoreDotNetCore2._0App
{
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
}
```

<span data-ttu-id="b71bd-129">L'adozione di questo nuovo modello quando si aggiornano le applicazioni a 2,0 è altamente consigliata ed è necessaria per le funzionalità del prodotto, ad esempio Entity Framework Core le migrazioni funzionano.</span><span class="sxs-lookup"><span data-stu-id="b71bd-129">The adoption of this new pattern when updating applications to 2.0 is highly recommended and is required in order for product features like Entity Framework Core Migrations to work.</span></span> <span data-ttu-id="b71bd-130">L'altra alternativa comune consiste nell' [implementare \*IDesignTimeDbContextFactory \<TContext> \*](xref:core/miscellaneous/cli/dbcontext-creation#from-a-design-time-factory).</span><span class="sxs-lookup"><span data-stu-id="b71bd-130">The other common alternative is to [implement *IDesignTimeDbContextFactory\<TContext>*](xref:core/miscellaneous/cli/dbcontext-creation#from-a-design-time-factory).</span></span>

## <a name="idbcontextfactory-renamed"></a><span data-ttu-id="b71bd-131">IDbContextFactory rinominato</span><span class="sxs-lookup"><span data-stu-id="b71bd-131">IDbContextFactory renamed</span></span>

<span data-ttu-id="b71bd-132">Per supportare diversi modelli di applicazione e fornire agli utenti un maggiore controllo sulle modalità di utilizzo di tali modelli in `DbContext` fase di progettazione, in passato è stata fornita l' `IDbContextFactory<TContext>` interfaccia.</span><span class="sxs-lookup"><span data-stu-id="b71bd-132">In order to support diverse application patterns and give users more control over how their `DbContext` is used at design time, we have, in the past, provided the `IDbContextFactory<TContext>` interface.</span></span> <span data-ttu-id="b71bd-133">In fase di progettazione, gli strumenti di EF Core rileveranno le implementazioni di questa interfaccia nel progetto e la utilizzeranno per creare `DbContext` oggetti.</span><span class="sxs-lookup"><span data-stu-id="b71bd-133">At design-time, the EF Core tools will discover implementations of this interface in your project and use it to create `DbContext` objects.</span></span>

<span data-ttu-id="b71bd-134">Questa interfaccia ha un nome molto generale che induce in inganno alcuni utenti a provare a riutilizzarlo per altri `DbContext` scenari di creazione.</span><span class="sxs-lookup"><span data-stu-id="b71bd-134">This interface had a very general name which mislead some users to try re-using it for other `DbContext`-creating scenarios.</span></span> <span data-ttu-id="b71bd-135">Sono stati rilevati quando gli strumenti EF tentavano di usare la propria implementazione in fase di progettazione e causavano la mancata esecuzione di comandi come `Update-Database` o `dotnet ef database update` .</span><span class="sxs-lookup"><span data-stu-id="b71bd-135">They were caught off guard when the EF Tools then tried to use their implementation at design-time and caused commands like `Update-Database` or `dotnet ef database update` to fail.</span></span>

<span data-ttu-id="b71bd-136">Per comunicare la semantica avanzata della fase di progettazione di questa interfaccia, è stata rinominata in `IDesignTimeDbContextFactory<TContext>` .</span><span class="sxs-lookup"><span data-stu-id="b71bd-136">In order to communicate the strong design-time semantics of this interface, we have renamed it to `IDesignTimeDbContextFactory<TContext>`.</span></span>

<span data-ttu-id="b71bd-137">Per la versione 2,0 `IDbContextFactory<TContext>` , esiste ancora, ma è contrassegnato come obsoleto.</span><span class="sxs-lookup"><span data-stu-id="b71bd-137">For the 2.0 release the `IDbContextFactory<TContext>` still exists but is marked as obsolete.</span></span>

## <a name="dbcontextfactoryoptions-removed"></a><span data-ttu-id="b71bd-138">DbContextFactoryOptions rimosso</span><span class="sxs-lookup"><span data-stu-id="b71bd-138">DbContextFactoryOptions removed</span></span>

<span data-ttu-id="b71bd-139">A causa delle modifiche ASP.NET Core 2,0 descritte in precedenza, `DbContextFactoryOptions` è stato rilevato che non era più necessario sulla nuova `IDesignTimeDbContextFactory<TContext>` interfaccia.</span><span class="sxs-lookup"><span data-stu-id="b71bd-139">Because of the ASP.NET Core 2.0 changes described above, we found that `DbContextFactoryOptions` was no longer needed on the new `IDesignTimeDbContextFactory<TContext>` interface.</span></span> <span data-ttu-id="b71bd-140">Ecco le alternative da usare.</span><span class="sxs-lookup"><span data-stu-id="b71bd-140">Here are the alternatives you should be using instead.</span></span>

| <span data-ttu-id="b71bd-141">DbContextFactoryOptions</span><span class="sxs-lookup"><span data-stu-id="b71bd-141">DbContextFactoryOptions</span></span> | <span data-ttu-id="b71bd-142">Alternativa</span><span class="sxs-lookup"><span data-stu-id="b71bd-142">Alternative</span></span>                                                  |
|:------------------------|:-------------------------------------------------------------|
| <span data-ttu-id="b71bd-143">ApplicationBasePath</span><span class="sxs-lookup"><span data-stu-id="b71bd-143">ApplicationBasePath</span></span>     | <span data-ttu-id="b71bd-144">AppContext. BaseDirectory</span><span class="sxs-lookup"><span data-stu-id="b71bd-144">AppContext.BaseDirectory</span></span>                                     |
| <span data-ttu-id="b71bd-145">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="b71bd-145">ContentRootPath</span></span>         | <span data-ttu-id="b71bd-146">Directory. GetCurrentDirectory ()</span><span class="sxs-lookup"><span data-stu-id="b71bd-146">Directory.GetCurrentDirectory()</span></span>                              |
| <span data-ttu-id="b71bd-147">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="b71bd-147">EnvironmentName</span></span>         | <span data-ttu-id="b71bd-148">Environment. GetEnvironmentVariable ("ASPNETCORE_ENVIRONMENT")</span><span class="sxs-lookup"><span data-stu-id="b71bd-148">Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT")</span></span> |

## <a name="design-time-working-directory-changed"></a><span data-ttu-id="b71bd-149">Directory di lavoro della fase di progettazione modificata</span><span class="sxs-lookup"><span data-stu-id="b71bd-149">Design-time working directory changed</span></span>

<span data-ttu-id="b71bd-150">Le modifiche apportate ASP.NET Core 2,0 richiedono anche la directory di lavoro utilizzata da `dotnet ef` per allinearsi alla directory di lavoro utilizzata da Visual Studio durante l'esecuzione dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="b71bd-150">The ASP.NET Core 2.0 changes also required the working directory used by `dotnet ef` to align with the working directory used by Visual Studio when running your application.</span></span> <span data-ttu-id="b71bd-151">Un effetto collaterale osservabile è che i nomi dei file SQLite sono ora relativi alla directory del progetto e non alla directory di output come sono stati usati.</span><span class="sxs-lookup"><span data-stu-id="b71bd-151">One observable side effect of this is that SQLite filenames are now relative to the project directory and not the output directory like they used to be.</span></span>

## <a name="ef-core-20-requires-a-20-database-provider"></a><span data-ttu-id="b71bd-152">EF Core 2,0 richiede un provider di database 2,0</span><span class="sxs-lookup"><span data-stu-id="b71bd-152">EF Core 2.0 requires a 2.0 database provider</span></span>

<span data-ttu-id="b71bd-153">Per EF Core 2,0 sono state apportate numerose semplificazioni e miglioramenti nel modo in cui funzionano i provider di database.</span><span class="sxs-lookup"><span data-stu-id="b71bd-153">For EF Core 2.0 we have made many simplifications and improvements in the way database providers work.</span></span> <span data-ttu-id="b71bd-154">Ciò significa che i provider 1.0. x e 1.1. x non funzioneranno con EF Core 2,0.</span><span class="sxs-lookup"><span data-stu-id="b71bd-154">This means that 1.0.x and 1.1.x providers will not work with EF Core 2.0.</span></span>

<span data-ttu-id="b71bd-155">I provider SQL Server e SQLite vengono spediti dal team EF e le versioni 2,0 saranno disponibili come parte della versione 2,0.</span><span class="sxs-lookup"><span data-stu-id="b71bd-155">The SQL Server and SQLite providers are shipped by the EF team and 2.0 versions will be available as part of the 2.0 release.</span></span> <span data-ttu-id="b71bd-156">I provider di terze parti open source per [SQL Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact), [PostgreSQL](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL)e [MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql) vengono aggiornati per 2,0.</span><span class="sxs-lookup"><span data-stu-id="b71bd-156">The open-source third party providers for [SQL Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact), [PostgreSQL](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL), and [MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql) are being updated for 2.0.</span></span> <span data-ttu-id="b71bd-157">Per tutti gli altri provider, contattare il writer del provider.</span><span class="sxs-lookup"><span data-stu-id="b71bd-157">For all other providers, please contact the provider writer.</span></span>

## <a name="logging-and-diagnostics-events-have-changed"></a><span data-ttu-id="b71bd-158">Gli eventi di registrazione e diagnostica sono stati modificati</span><span class="sxs-lookup"><span data-stu-id="b71bd-158">Logging and Diagnostics events have changed</span></span>

<span data-ttu-id="b71bd-159">Nota: queste modifiche non dovrebbero avere un effetto sulla maggior parte del codice dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="b71bd-159">Note: these changes should not impact most application code.</span></span>

<span data-ttu-id="b71bd-160">Gli ID evento per i messaggi inviati a un [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) sono stati modificati in 2,0.</span><span class="sxs-lookup"><span data-stu-id="b71bd-160">The event IDs for messages sent to an [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) have changed in 2.0.</span></span> <span data-ttu-id="b71bd-161">Gli ID evento sono ora univoci nel codice EF Core.</span><span class="sxs-lookup"><span data-stu-id="b71bd-161">The event IDs are now unique across EF Core code.</span></span> <span data-ttu-id="b71bd-162">Questi messaggi ora seguono inoltre il modello standard per la registrazione strutturata usato, ad esempio, da MVC.</span><span class="sxs-lookup"><span data-stu-id="b71bd-162">These messages now also follow the standard pattern for structured logging used by, for example, MVC.</span></span>

<span data-ttu-id="b71bd-163">Anche le categorie del logger sono state modificate.</span><span class="sxs-lookup"><span data-stu-id="b71bd-163">Logger categories have also changed.</span></span> <span data-ttu-id="b71bd-164">È ora disponibile un set di categorie ben noto accessibile tramite [DbLoggerCategory](https://github.com/aspnet/EntityFrameworkCore/blob/rel/2.0.0/src/EFCore/DbLoggerCategory.cs).</span><span class="sxs-lookup"><span data-stu-id="b71bd-164">There is now a well-known set of categories accessed through [DbLoggerCategory](https://github.com/aspnet/EntityFrameworkCore/blob/rel/2.0.0/src/EFCore/DbLoggerCategory.cs).</span></span>

<span data-ttu-id="b71bd-165">Gli eventi [DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) ora utilizzano gli stessi nomi di ID evento dei `ILogger` messaggi corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="b71bd-165">[DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) events now use the same event ID names as the corresponding `ILogger` messages.</span></span> <span data-ttu-id="b71bd-166">I payload dell'evento sono tutti tipi nominali derivati da [EventData](/dotnet/api/microsoft.entityframeworkcore.diagnostics.eventdata).</span><span class="sxs-lookup"><span data-stu-id="b71bd-166">The event payloads are all nominal types derived from [EventData](/dotnet/api/microsoft.entityframeworkcore.diagnostics.eventdata).</span></span>

<span data-ttu-id="b71bd-167">Gli ID evento, i tipi di payload e le categorie sono documentati nelle classi [CoreEventId](/dotnet/api/microsoft.entityframeworkcore.diagnostics.coreeventid) e [RelationalEventId](/dotnet/api/microsoft.entityframeworkcore.diagnostics.relationaleventid) .</span><span class="sxs-lookup"><span data-stu-id="b71bd-167">Event IDs, payload types, and categories are documented in the [CoreEventId](/dotnet/api/microsoft.entityframeworkcore.diagnostics.coreeventid) and the [RelationalEventId](/dotnet/api/microsoft.entityframeworkcore.diagnostics.relationaleventid) classes.</span></span>

<span data-ttu-id="b71bd-168">Gli ID sono stati spostati anche da Microsoft. EntityFrameworkCore. Infrastructure al nuovo spazio dei nomi Microsoft. EntityFrameworkCore. Diagnostics.</span><span class="sxs-lookup"><span data-stu-id="b71bd-168">IDs have also moved from Microsoft.EntityFrameworkCore.Infrastructure to the new Microsoft.EntityFrameworkCore.Diagnostics namespace.</span></span>

## <a name="ef-core-relational-metadata-api-changes"></a><span data-ttu-id="b71bd-169">EF Core modifiche API dei metadati relazionali</span><span class="sxs-lookup"><span data-stu-id="b71bd-169">EF Core relational metadata API changes</span></span>

<span data-ttu-id="b71bd-170">EF Core 2.0 ora compila un elemento [IModel](/dotnet/api/microsoft.entityframeworkcore.metadata.imodel) diverso per ogni provider in uso.</span><span class="sxs-lookup"><span data-stu-id="b71bd-170">EF Core 2.0 will now build a different [IModel](/dotnet/api/microsoft.entityframeworkcore.metadata.imodel) for each different provider being used.</span></span> <span data-ttu-id="b71bd-171">L'operazione è in genere trasparente per l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="b71bd-171">This is usually transparent to the application.</span></span> <span data-ttu-id="b71bd-172">Questa operazione ha semplificato la semplificazione delle API dei metadati di basso livello, in modo che qualsiasi accesso ai _concetti comuni relativi ai metadati relazionali_ venga sempre effettuato tramite una chiamata a `.Relational` anziché `.SqlServer` , `.Sqlite` e così via. Ad esempio, il codice 1.1. x è simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="b71bd-172">This has facilitated a simplification of lower-level metadata APIs such that any access to _common relational metadata concepts_ is always made through a call to `.Relational` instead of `.SqlServer`, `.Sqlite`, etc. For example, 1.1.x code like this:</span></span>

``` csharp
var tableName = context.Model.FindEntityType(typeof(User)).SqlServer().TableName;
```

<span data-ttu-id="b71bd-173">Dovrebbe ora essere scritto come segue:</span><span class="sxs-lookup"><span data-stu-id="b71bd-173">Should now be written like this:</span></span>

``` csharp
var tableName = context.Model.FindEntityType(typeof(User)).Relational().TableName;
```

<span data-ttu-id="b71bd-174">Anziché usare metodi come `ForSqlServerToTable` , i metodi di estensione sono ora disponibili per scrivere codice condizionale basato sul provider corrente in uso.</span><span class="sxs-lookup"><span data-stu-id="b71bd-174">Instead of using methods like `ForSqlServerToTable`, extension methods are now available to write conditional code based on the current provider in use.</span></span> <span data-ttu-id="b71bd-175">Esempio:</span><span class="sxs-lookup"><span data-stu-id="b71bd-175">For example:</span></span>

```csharp
modelBuilder.Entity<User>().ToTable(
    Database.IsSqlServer() ? "SqlServerName" : "OtherName");
```

<span data-ttu-id="b71bd-176">Si noti che questa modifica si applica solo alle API/metadati definiti per _tutti i_ provider relazionali.</span><span class="sxs-lookup"><span data-stu-id="b71bd-176">Note that this change only applies to APIs/metadata that is defined for _all_ relational providers.</span></span> <span data-ttu-id="b71bd-177">L'API e i metadati rimangono invariati quando sono specifici di un singolo provider.</span><span class="sxs-lookup"><span data-stu-id="b71bd-177">The API and metadata remains the same when it is specific to only a single provider.</span></span> <span data-ttu-id="b71bd-178">Gli indici cluster, ad esempio, sono specifici del server SQL, quindi è `ForSqlServerIsClustered`  `.SqlServer().IsClustered()` necessario utilizzare e.</span><span class="sxs-lookup"><span data-stu-id="b71bd-178">For example, clustered indexes are specific to SQL Sever, so `ForSqlServerIsClustered` and  `.SqlServer().IsClustered()` must still be used.</span></span>

## <a name="dont-take-control-of-the-ef-service-provider"></a><span data-ttu-id="b71bd-179">Non assumere il controllo del provider di servizi EF</span><span class="sxs-lookup"><span data-stu-id="b71bd-179">Don’t take control of the EF service provider</span></span>

<span data-ttu-id="b71bd-180">EF Core usa un `IServiceProvider` contenitore interno (un contenitore di inserimento di dipendenze) per la relativa implementazione interna.</span><span class="sxs-lookup"><span data-stu-id="b71bd-180">EF Core uses an internal `IServiceProvider` (a dependency injection container) for its internal implementation.</span></span> <span data-ttu-id="b71bd-181">Le applicazioni devono consentire EF Core di creare e gestire questo provider, tranne nei casi particolari.</span><span class="sxs-lookup"><span data-stu-id="b71bd-181">Applications should allow EF Core to create and manage this provider except in special cases.</span></span> <span data-ttu-id="b71bd-182">È consigliabile rimuovere tutte le chiamate a `UseInternalServiceProvider` .</span><span class="sxs-lookup"><span data-stu-id="b71bd-182">Strongly consider removing any calls to `UseInternalServiceProvider`.</span></span> <span data-ttu-id="b71bd-183">Se un'applicazione deve chiamare `UseInternalServiceProvider` , provare a [presentare un problema](https://github.com/dotnet/efcore/Issues) per poter esaminare altri modi per gestire lo scenario.</span><span class="sxs-lookup"><span data-stu-id="b71bd-183">If an application does need to call `UseInternalServiceProvider`, then please consider [filing an issue](https://github.com/dotnet/efcore/Issues) so we can investigate other ways to handle your scenario.</span></span>

<span data-ttu-id="b71bd-184">`AddEntityFramework`La chiamata `AddEntityFrameworkSqlServer` di, e così via non è richiesta dal codice dell'applicazione a meno che non `UseInternalServiceProvider` venga chiamato anche.</span><span class="sxs-lookup"><span data-stu-id="b71bd-184">Calling `AddEntityFramework`, `AddEntityFrameworkSqlServer`, etc. is not required by application code unless `UseInternalServiceProvider` is also called.</span></span> <span data-ttu-id="b71bd-185">Rimuovere qualsiasi chiamata esistente a `AddEntityFramework` o `AddEntityFrameworkSqlServer` e così via. `AddDbContext` deve essere comunque utilizzata come prima.</span><span class="sxs-lookup"><span data-stu-id="b71bd-185">Remove any existing calls to `AddEntityFramework` or `AddEntityFrameworkSqlServer`, etc. `AddDbContext` should still be used in the same way as before.</span></span>

## <a name="in-memory-databases-must-be-named"></a><span data-ttu-id="b71bd-186">I database in memoria devono essere denominati</span><span class="sxs-lookup"><span data-stu-id="b71bd-186">In-memory databases must be named</span></span>

<span data-ttu-id="b71bd-187">Il database in memoria globale senza nome è stato rimosso, ma è necessario assegnare un nome a tutti i database in memoria.</span><span class="sxs-lookup"><span data-stu-id="b71bd-187">The global unnamed in-memory database has been removed and instead all in-memory databases must be named.</span></span> <span data-ttu-id="b71bd-188">Esempio:</span><span class="sxs-lookup"><span data-stu-id="b71bd-188">For example:</span></span>

``` csharp
optionsBuilder.UseInMemoryDatabase("MyDatabase");
```

<span data-ttu-id="b71bd-189">Viene creato un database con il nome "database".</span><span class="sxs-lookup"><span data-stu-id="b71bd-189">This creates/uses a database with the name “MyDatabase”.</span></span> <span data-ttu-id="b71bd-190">Se `UseInMemoryDatabase` viene chiamato di nuovo con lo stesso nome, verrà usato lo stesso database in memoria, in modo che possa essere condiviso da più istanze di contesto.</span><span class="sxs-lookup"><span data-stu-id="b71bd-190">If `UseInMemoryDatabase` is called again with the same name, then the same in-memory database will be used, allowing it to be shared by multiple context instances.</span></span>

## <a name="read-only-api-changes"></a><span data-ttu-id="b71bd-191">Modifiche all'API di sola lettura</span><span class="sxs-lookup"><span data-stu-id="b71bd-191">Read-only API changes</span></span>

<span data-ttu-id="b71bd-192">`IsReadOnlyBeforeSave`, `IsReadOnlyAfterSave` e `IsStoreGeneratedAlways` sono stati obsoleti e sostituiti con [BeforeSaveBehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.beforesavebehavior) e [AfterSaveBehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.aftersavebehavior).</span><span class="sxs-lookup"><span data-stu-id="b71bd-192">`IsReadOnlyBeforeSave`, `IsReadOnlyAfterSave`, and `IsStoreGeneratedAlways` have been obsoleted and replaced with [BeforeSaveBehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.beforesavebehavior) and [AfterSaveBehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.aftersavebehavior).</span></span> <span data-ttu-id="b71bd-193">Questi comportamenti si applicano a qualsiasi proprietà (non solo alle proprietà generate dall'archivio) e determinano il modo in cui il valore della proprietà deve essere utilizzato quando si inserisce in una riga di database ( `BeforeSaveBehavior` ) o quando si aggiorna una riga di database esistente ( `AfterSaveBehavior` ).</span><span class="sxs-lookup"><span data-stu-id="b71bd-193">These behaviors apply to any property (not only store-generated properties) and determine how the value of the property should be used when inserting into a database row (`BeforeSaveBehavior`) or when updating an existing database row (`AfterSaveBehavior`).</span></span>

<span data-ttu-id="b71bd-194">Per impostazione predefinita, le proprietà contrassegnate come [ValueGenerated. OnAddOrUpdate](/dotnet/api/microsoft.entityframeworkcore.metadata.valuegenerated) (ad esempio per le colonne calcolate) ignoreranno tutti i valori attualmente impostati nella proprietà.</span><span class="sxs-lookup"><span data-stu-id="b71bd-194">Properties marked as [ValueGenerated.OnAddOrUpdate](/dotnet/api/microsoft.entityframeworkcore.metadata.valuegenerated) (for example, for computed columns) will by default ignore any value currently set on the property.</span></span> <span data-ttu-id="b71bd-195">Ciò significa che un valore generato dall'archivio sarà sempre ottenuto indipendentemente dal fatto che un valore sia stato impostato o modificato sull'entità rilevata.</span><span class="sxs-lookup"><span data-stu-id="b71bd-195">This means that a store-generated value will always be obtained regardless of whether any value has been set or modified on the tracked entity.</span></span> <span data-ttu-id="b71bd-196">Questo può essere modificato impostando un diverso `Before\AfterSaveBehavior` .</span><span class="sxs-lookup"><span data-stu-id="b71bd-196">This can be changed by setting a different `Before\AfterSaveBehavior`.</span></span>

## <a name="new-clientsetnull-delete-behavior"></a><span data-ttu-id="b71bd-197">Nuovo comportamento di eliminazione ClientSetNull</span><span class="sxs-lookup"><span data-stu-id="b71bd-197">New ClientSetNull delete behavior</span></span>

<span data-ttu-id="b71bd-198">Nelle versioni precedenti, [DeleteBehavior. Restrict](/dotnet/api/microsoft.entityframeworkcore.deletebehavior) aveva un comportamento per le entità rilevate dal contesto che ha più chiuso la `SetNull` semantica corrispondente.</span><span class="sxs-lookup"><span data-stu-id="b71bd-198">In previous releases, [DeleteBehavior.Restrict](/dotnet/api/microsoft.entityframeworkcore.deletebehavior) had a behavior for entities tracked by the context that more closed matched `SetNull` semantics.</span></span> <span data-ttu-id="b71bd-199">In EF Core 2,0 `ClientSetNull` è stato introdotto un nuovo comportamento come impostazione predefinita per le relazioni facoltative.</span><span class="sxs-lookup"><span data-stu-id="b71bd-199">In EF Core 2.0, a new `ClientSetNull` behavior has been introduced as the default for optional relationships.</span></span> <span data-ttu-id="b71bd-200">Questo comportamento ha la `SetNull` semantica per le entità rilevate e il `Restrict` comportamento per i database creati con EF core.</span><span class="sxs-lookup"><span data-stu-id="b71bd-200">This behavior has `SetNull` semantics for tracked entities and `Restrict` behavior for databases created using EF Core.</span></span> <span data-ttu-id="b71bd-201">In questa esperienza, questi sono i comportamenti più attesi/utili per le entità rilevate e il database.</span><span class="sxs-lookup"><span data-stu-id="b71bd-201">In our experience, these are the most expected/useful behaviors for tracked entities and the database.</span></span> <span data-ttu-id="b71bd-202">`DeleteBehavior.Restrict` viene ora rispettato per le entità rilevate se impostate per le relazioni facoltative.</span><span class="sxs-lookup"><span data-stu-id="b71bd-202">`DeleteBehavior.Restrict` is now honored for tracked entities when set for optional relationships.</span></span>

## <a name="provider-design-time-packages-removed"></a><span data-ttu-id="b71bd-203">Pacchetti della fase di progettazione del provider rimossi</span><span class="sxs-lookup"><span data-stu-id="b71bd-203">Provider design-time packages removed</span></span>

<span data-ttu-id="b71bd-204">Il `Microsoft.EntityFrameworkCore.Relational.Design` pacchetto è stato rimosso.</span><span class="sxs-lookup"><span data-stu-id="b71bd-204">The `Microsoft.EntityFrameworkCore.Relational.Design` package has been removed.</span></span> <span data-ttu-id="b71bd-205">I contenuti sono stati consolidati in `Microsoft.EntityFrameworkCore.Relational` e `Microsoft.EntityFrameworkCore.Design` .</span><span class="sxs-lookup"><span data-stu-id="b71bd-205">It's contents were consolidated into `Microsoft.EntityFrameworkCore.Relational` and `Microsoft.EntityFrameworkCore.Design`.</span></span>

<span data-ttu-id="b71bd-206">Questa operazione viene propagata nei pacchetti della fase di progettazione del provider.</span><span class="sxs-lookup"><span data-stu-id="b71bd-206">This propagates into the provider design-time packages.</span></span> <span data-ttu-id="b71bd-207">Questi pacchetti ( `Microsoft.EntityFrameworkCore.Sqlite.Design` , `Microsoft.EntityFrameworkCore.SqlServer.Design` e così via) sono stati rimossi e il relativo contenuto è stato consolidato nei pacchetti del provider principali.</span><span class="sxs-lookup"><span data-stu-id="b71bd-207">Those packages (`Microsoft.EntityFrameworkCore.Sqlite.Design`, `Microsoft.EntityFrameworkCore.SqlServer.Design`, etc.) were removed and their contents consolidated into the main provider packages.</span></span>

<span data-ttu-id="b71bd-208">Per abilitare `Scaffold-DbContext` o `dotnet ef dbcontext scaffold` in EF Core 2,0, è sufficiente fare riferimento al pacchetto del singolo provider:</span><span class="sxs-lookup"><span data-stu-id="b71bd-208">To enable `Scaffold-DbContext` or `dotnet ef dbcontext scaffold` in EF Core 2.0, you only need to reference the single provider package:</span></span>

``` xml
<PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer"
    Version="2.0.0" />
<PackageReference Include="Microsoft.EntityFrameworkCore.Tools"
    Version="2.0.0"
    PrivateAssets="All" />
<DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet"
    Version="2.0.0" />
```