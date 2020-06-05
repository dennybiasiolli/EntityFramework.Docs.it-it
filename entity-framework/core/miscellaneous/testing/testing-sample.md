---
title: ''
description: ''
author: ''
ms.date: ''
uid: ''
no-loc:
- Item
- Tag
- Items
- Tags
ms.openlocfilehash: ae073fc0b3a99fb9de07a3e0a42c638fe0838a5a
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672821"
---
# <a name="ef-core-testing-sample"></a><span data-ttu-id="f4534-101">Esempio di test di EF Core</span><span class="sxs-lookup"><span data-stu-id="f4534-101">EF Core testing sample</span></span>

> [!TIP]
> <span data-ttu-id="f4534-102">Il codice in questo documento è disponibile in GitHub come [esempio eseguibile](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/).</span><span class="sxs-lookup"><span data-stu-id="f4534-102">The code in this document can be found on GitHub as a [runnable sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/).</span></span>
> <span data-ttu-id="f4534-103">Si noti che alcuni di questi test **dovrebbero avere esito negativo**.</span><span class="sxs-lookup"><span data-stu-id="f4534-103">Note that some of these tests **are expected to fail**.</span></span> <span data-ttu-id="f4534-104">I motivi di questo problema sono descritti di seguito.</span><span class="sxs-lookup"><span data-stu-id="f4534-104">The reasons for this are explained below.</span></span> 

<span data-ttu-id="f4534-105">Questo documento illustra un esempio per il test del codice che usa EF Core.</span><span class="sxs-lookup"><span data-stu-id="f4534-105">This doc walks through a sample for testing code that uses EF Core.</span></span>

## <a name="the-application"></a><span data-ttu-id="f4534-106">Applicazione</span><span class="sxs-lookup"><span data-stu-id="f4534-106">The application</span></span>

<span data-ttu-id="f4534-107">L' [esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) contiene due progetti:</span><span class="sxs-lookup"><span data-stu-id="f4534-107">The [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) contains two projects:</span></span>
- <span data-ttu-id="f4534-108">ItemsWebApi: un' [API Web molto semplice supportata da ASP.NET Core](/aspnet/core/tutorials/first-web-api?view=aspnetcore-3.1&tabs=visual-studio) con un singolo controller</span><span class="sxs-lookup"><span data-stu-id="f4534-108">ItemsWebApi: A very simple [Web API backed by ASP.NET Core](/aspnet/core/tutorials/first-web-api?view=aspnetcore-3.1&tabs=visual-studio) with a single controller</span></span>
- <span data-ttu-id="f4534-109">Test: un progetto di test [xUnit](https://xunit.net/) per testare il controller</span><span class="sxs-lookup"><span data-stu-id="f4534-109">Tests: An [XUnit](https://xunit.net/) test project to test the controller</span></span>

### <a name="the-model-and-business-rules"></a><span data-ttu-id="f4534-110">Modello e regole business</span><span class="sxs-lookup"><span data-stu-id="f4534-110">The model and business rules</span></span>

<span data-ttu-id="f4534-111">Il modello che supporta questa API è costituito da due tipi di entità: Items e Tags .</span><span class="sxs-lookup"><span data-stu-id="f4534-111">The model backing this API has two entity types: Items and Tags.</span></span>

* Items<span data-ttu-id="f4534-112">hanno un nome con distinzione tra maiuscole e minuscole e una raccolta di Tags .</span><span class="sxs-lookup"><span data-stu-id="f4534-112"> have a case-sensitive name and a collection of Tags.</span></span>
* <span data-ttu-id="f4534-113">Ogni Tag ha un'etichetta e un conteggio che rappresenta il numero di volte in cui è stato applicato a Item .</span><span class="sxs-lookup"><span data-stu-id="f4534-113">Each Tag has a label and a count representing the number of times it has been applied to the Item.</span></span>
* <span data-ttu-id="f4534-114">Ogni Item deve avere un solo Tag oggetto con un'etichetta specificata.</span><span class="sxs-lookup"><span data-stu-id="f4534-114">Each Item should only have one Tag with a given label.</span></span>
  * <span data-ttu-id="f4534-115">Se un elemento viene contrassegnato con la stessa etichetta più di una volta, il conteggio del tag esistente con tale etichetta viene incrementato al posto di un nuovo tag creato.</span><span class="sxs-lookup"><span data-stu-id="f4534-115">If an item is tagged with the same label more than once, then the count on the existing tag with that label is incremented instead of a new tag being created.</span></span> 
* <span data-ttu-id="f4534-116">L'eliminazione di un oggetto Item deve eliminare tutti gli associati Tags .</span><span class="sxs-lookup"><span data-stu-id="f4534-116">Deleting an Item should delete all associated Tags.</span></span>

#### <a name="the-item-entity-type"></a><span data-ttu-id="f4534-117">ItemTipo di entità</span><span class="sxs-lookup"><span data-stu-id="f4534-117">The Item entity type</span></span>

<span data-ttu-id="f4534-118">`Item`Tipo di entità:</span><span class="sxs-lookup"><span data-stu-id="f4534-118">The `Item` entity type:</span></span>

[!code-csharp[ItemEntityType](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Item.cs?name=ItemEntityType)]

<span data-ttu-id="f4534-119">E la relativa configurazione in `DbContext.OnModelCreating` :</span><span class="sxs-lookup"><span data-stu-id="f4534-119">And its configuration in `DbContext.OnModelCreating`:</span></span>

[!code-csharp[ConfigureItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/ItemsContext.cs?name=ConfigureItem)]

<span data-ttu-id="f4534-120">Si noti che il tipo di entità vincola il modo in cui può essere usato per riflettere il modello di dominio e le regole business.</span><span class="sxs-lookup"><span data-stu-id="f4534-120">Notice that entity type constrains the way it can be used to reflect the domain model and business rules.</span></span> <span data-ttu-id="f4534-121">In particolare:</span><span class="sxs-lookup"><span data-stu-id="f4534-121">In particular:</span></span>
- <span data-ttu-id="f4534-122">La chiave primaria viene mappata direttamente al `_id` campo e non esposta pubblicamente</span><span class="sxs-lookup"><span data-stu-id="f4534-122">The primary key is mapped directly to the `_id` field and not exposed publicly</span></span>
  - <span data-ttu-id="f4534-123">EF rileva e usa il costruttore privato che accetta il nome e il valore della chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="f4534-123">EF detects and uses the private constructor accepting the primary key value and name.</span></span>
- <span data-ttu-id="f4534-124">La `Name` proprietà è di sola lettura e viene impostata solo nel costruttore.</span><span class="sxs-lookup"><span data-stu-id="f4534-124">The `Name` property is read-only and set only in the constructor.</span></span> 
- Tags<span data-ttu-id="f4534-125">vengono esposti come `IReadOnlyList<Tag>` per impedire la modifica arbitraria.</span><span class="sxs-lookup"><span data-stu-id="f4534-125"> are exposed as a `IReadOnlyList<Tag>` to prevent arbitrary modification.</span></span>
  - <span data-ttu-id="f4534-126">EF associa la `Tags` proprietà al `_tags` campo sottostante mediante la corrispondenza dei nomi.</span><span class="sxs-lookup"><span data-stu-id="f4534-126">EF associates the `Tags` property with the `_tags` backing field by matching their names.</span></span> 
  - <span data-ttu-id="f4534-127">Il `AddTag` metodo accetta un'etichetta tag e implementa la regola business descritta in precedenza.</span><span class="sxs-lookup"><span data-stu-id="f4534-127">The `AddTag` method takes a tag label and implements the business rule described above.</span></span>
    <span data-ttu-id="f4534-128">Ovvero, viene aggiunto un tag solo per le nuove etichette.</span><span class="sxs-lookup"><span data-stu-id="f4534-128">That is, a tag is only added for new labels.</span></span>
    <span data-ttu-id="f4534-129">In caso contrario, il conteggio su un'etichetta esistente viene incrementato.</span><span class="sxs-lookup"><span data-stu-id="f4534-129">Otherwise the count on an existing label is incremented.</span></span>
- <span data-ttu-id="f4534-130">La `Tags` proprietà di navigazione è configurata per una relazione molti-a-uno</span><span class="sxs-lookup"><span data-stu-id="f4534-130">The `Tags` navigation property is configured for a many-to-one relationship</span></span>
  - <span data-ttu-id="f4534-131">Non è necessario disporre di una proprietà di navigazione da Tag a Item , pertanto non è inclusa.</span><span class="sxs-lookup"><span data-stu-id="f4534-131">There is no need for a navigation property from Tag to Item, so it is not included.</span></span>
  - <span data-ttu-id="f4534-132">Inoltre, non Tag definisce una proprietà di chiave esterna.</span><span class="sxs-lookup"><span data-stu-id="f4534-132">Also, Tag does not define a foreign key property.</span></span>
    <span data-ttu-id="f4534-133">Al contrario, EF creerà e gestirà una proprietà in stato shadow.</span><span class="sxs-lookup"><span data-stu-id="f4534-133">Instead, EF will create and manage a property in shadow-state.</span></span>

#### <a name="the-tag-entity-type"></a><span data-ttu-id="f4534-134">TagTipo di entità</span><span class="sxs-lookup"><span data-stu-id="f4534-134">The Tag entity type</span></span>

<span data-ttu-id="f4534-135">`Tag`Tipo di entità:</span><span class="sxs-lookup"><span data-stu-id="f4534-135">The `Tag` entity type:</span></span>

[!code-csharp[TagEntityType](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Tag.cs?name=TagEntityType)]

<span data-ttu-id="f4534-136">E la relativa configurazione in `DbContext.OnModelCreating` :</span><span class="sxs-lookup"><span data-stu-id="f4534-136">And its configuration in `DbContext.OnModelCreating`:</span></span>

[!code-csharp[ConfigureTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/ItemsContext.cs?name=ConfigureTag)]

<span data-ttu-id="f4534-137">In modo analogo a Item , Tag nasconde la chiave primaria e rende la proprietà di sola `Label` lettura.</span><span class="sxs-lookup"><span data-stu-id="f4534-137">Similarly to Item, Tag hides its primary key and makes the `Label` property read-only.</span></span>

### <a name="the-itemscontroller"></a><span data-ttu-id="f4534-138">ItemsController</span><span class="sxs-lookup"><span data-stu-id="f4534-138">The ItemsController</span></span>

<span data-ttu-id="f4534-139">Il controller API Web è piuttosto semplice.</span><span class="sxs-lookup"><span data-stu-id="f4534-139">The Web API controller is pretty basic.</span></span>
<span data-ttu-id="f4534-140">Ottiene un oggetto `DbContext` dal contenitore di inserimento delle dipendenze tramite l'inserimento del costruttore:</span><span class="sxs-lookup"><span data-stu-id="f4534-140">It gets a `DbContext` from the dependency injection container through constructor injection:</span></span>

[!code-csharp[Constructor](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=Constructor)]

<span data-ttu-id="f4534-141">Dispone di metodi per ottenere tutti Items o un oggetto Item con un nome specificato:</span><span class="sxs-lookup"><span data-stu-id="f4534-141">It has methods to get all Items or an Item with a given name:</span></span>

[!code-csharp[Get](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=Get)]

<span data-ttu-id="f4534-142">Dispone di un metodo per aggiungere un nuovo Item :</span><span class="sxs-lookup"><span data-stu-id="f4534-142">It has a method to add a new Item:</span></span>

[!code-csharp[PostItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=PostItem)]

<span data-ttu-id="f4534-143">Metodo per contrassegnare un oggetto Item con un'etichetta:</span><span class="sxs-lookup"><span data-stu-id="f4534-143">A method to tag an Item with a label:</span></span>

[!code-csharp[PostTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=PostTag)]

<span data-ttu-id="f4534-144">E un metodo per eliminare un oggetto Item e tutti gli associati Tags :</span><span class="sxs-lookup"><span data-stu-id="f4534-144">And a method to delete an Item and all associated Tags:</span></span>

[!code-csharp[DeleteItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=DeleteItem)]

<span data-ttu-id="f4534-145">La maggior parte della convalida e della gestione degli errori sono state rimosse per ridurre il disordine.</span><span class="sxs-lookup"><span data-stu-id="f4534-145">Most validation and error handling have been removed to reduce clutter.</span></span>

## <a name="the-tests"></a><span data-ttu-id="f4534-146">Test</span><span class="sxs-lookup"><span data-stu-id="f4534-146">The Tests</span></span>

<span data-ttu-id="f4534-147">I test sono organizzati per l'esecuzione con più configurazioni del provider di database:</span><span class="sxs-lookup"><span data-stu-id="f4534-147">The tests are organized to run with multiple database provider configurations:</span></span>
* <span data-ttu-id="f4534-148">Provider di SQL Server, che è il provider utilizzato dall'applicazione</span><span class="sxs-lookup"><span data-stu-id="f4534-148">The SQL Server provider, which is the provider used by the application</span></span>
* <span data-ttu-id="f4534-149">Provider SQLite</span><span class="sxs-lookup"><span data-stu-id="f4534-149">The SQLite provider</span></span>
* <span data-ttu-id="f4534-150">Provider SQLite che usa i database SQLite in memoria</span><span class="sxs-lookup"><span data-stu-id="f4534-150">The SQLite provider using in-memory SQLite databases</span></span>
* <span data-ttu-id="f4534-151">Provider di database in memoria EF</span><span class="sxs-lookup"><span data-stu-id="f4534-151">The EF in-memory database provider</span></span>

<span data-ttu-id="f4534-152">Questa operazione viene eseguita inserendo tutti i test in una classe di base, quindi ereditando da questo oggetto per eseguire il test con ogni provider.</span><span class="sxs-lookup"><span data-stu-id="f4534-152">This is achieved by putting all the tests in a base class, then inheriting from this to test with each provider.</span></span>

> [!TIP]
> <span data-ttu-id="f4534-153">Se non si usa local DB, sarà necessario modificare la stringa di connessione SQL Server.</span><span class="sxs-lookup"><span data-stu-id="f4534-153">You will need to change the SQL Server connection string if you're not using LocalDB.</span></span>

> [!TIP]
> <span data-ttu-id="f4534-154">Vedere [test con SQLite](xref:core/miscellaneous/testing/sqlite) per istruzioni sull'uso di SQLite per i test in memoria.</span><span class="sxs-lookup"><span data-stu-id="f4534-154">See [Testing with SQLite](xref:core/miscellaneous/testing/sqlite) for guidance on using SQLite for in-memory testing.</span></span> 

<span data-ttu-id="f4534-155">I due test seguenti dovrebbero avere esito negativo:</span><span class="sxs-lookup"><span data-stu-id="f4534-155">The following two tests are expected to fail:</span></span>
* <span data-ttu-id="f4534-156">`Can_remove_item_and_all_associated_tags`Quando si esegue con il provider di database in memoria EF</span><span class="sxs-lookup"><span data-stu-id="f4534-156">`Can_remove_item_and_all_associated_tags` when running with the EF in-memory database provider</span></span>
* <span data-ttu-id="f4534-157">`Can_add_item_differing_only_by_case`Quando si esegue con il provider di SQL Server</span><span class="sxs-lookup"><span data-stu-id="f4534-157">`Can_add_item_differing_only_by_case` when running with the SQL Server provider</span></span>

<span data-ttu-id="f4534-158">Questo aspetto viene trattato in modo più dettagliato di seguito.</span><span class="sxs-lookup"><span data-stu-id="f4534-158">This is covered in more detail below.</span></span>

### <a name="setting-up-and-seeding-the-database"></a><span data-ttu-id="f4534-159">Impostazione e seeding del database</span><span class="sxs-lookup"><span data-stu-id="f4534-159">Setting up and seeding the database</span></span>

<span data-ttu-id="f4534-160">XUnit, come la maggior parte dei framework di test, creerà una nuova istanza della classe di test per ogni esecuzione dei test.</span><span class="sxs-lookup"><span data-stu-id="f4534-160">XUnit, like most testing frameworks, will create a new test class instance for each test run.</span></span>
<span data-ttu-id="f4534-161">Inoltre, XUnit non eseguirà i test in una determinata classe di test in parallelo.</span><span class="sxs-lookup"><span data-stu-id="f4534-161">Also, XUnit will not run tests within a given test class in parallel.</span></span> <span data-ttu-id="f4534-162">Ciò significa che è possibile impostare e configurare il database nel costruttore di test e che sarà in uno stato noto per ogni test.</span><span class="sxs-lookup"><span data-stu-id="f4534-162">This means that we can setup and configure the database in the test constructor and it will be in a well-known state for each test.</span></span>

> [!TIP]
> <span data-ttu-id="f4534-163">Questo esempio ricrea il database per ogni test.</span><span class="sxs-lookup"><span data-stu-id="f4534-163">This sample recreates the database for each test.</span></span>
> <span data-ttu-id="f4534-164">Questo approccio funziona bene per i test di database SQLite e EF in memoria, ma può comportare un sovraccarico significativo con altri sistemi di database, tra cui SQL Server.</span><span class="sxs-lookup"><span data-stu-id="f4534-164">This works well for SQLite and EF in-memory database testing but can involve significant overhead with other database systems, including SQL Server.</span></span>
> <span data-ttu-id="f4534-165">Gli approcci per la riduzione di questo overhead sono trattati nella [condivisione dei database tra i test](xref:core/miscellaneous/testing/sharing-databases).</span><span class="sxs-lookup"><span data-stu-id="f4534-165">Approaches for reducing this overhead are covered in [Sharing databases across tests](xref:core/miscellaneous/testing/sharing-databases).</span></span>

<span data-ttu-id="f4534-166">Quando ogni test viene eseguito:</span><span class="sxs-lookup"><span data-stu-id="f4534-166">When each test is run:</span></span>
* <span data-ttu-id="f4534-167">DbContextOptions sono configurati per il provider in uso e passati al costruttore della classe base</span><span class="sxs-lookup"><span data-stu-id="f4534-167">DbContextOptions are configured for the provider in use and passed to the base class constructor</span></span>
  * <span data-ttu-id="f4534-168">Queste opzioni vengono archiviate in una proprietà e utilizzate in tutti i test per la creazione di istanze di DbContext</span><span class="sxs-lookup"><span data-stu-id="f4534-168">These options are stored in a property and used throughout the tests for creating DbContext instances</span></span>
* <span data-ttu-id="f4534-169">Viene chiamato un metodo di inizializzazione per creare ed eseguire il seeding del database</span><span class="sxs-lookup"><span data-stu-id="f4534-169">A Seed method is called to create and seed the database</span></span>
  * <span data-ttu-id="f4534-170">Il metodo Seed garantisce che il database sia pulito e quindi ricreandolo</span><span class="sxs-lookup"><span data-stu-id="f4534-170">The Seed method ensures the database is clean by deleting it and then re-creating it</span></span>
  * <span data-ttu-id="f4534-171">Alcune entità di test note vengono create e salvate nel database</span><span class="sxs-lookup"><span data-stu-id="f4534-171">Some well-known test entities are created and saved to the database</span></span>

[!code-csharp[Seeding](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=Seeding)]

<span data-ttu-id="f4534-172">Ogni classe di test concreta eredita quindi da questa.</span><span class="sxs-lookup"><span data-stu-id="f4534-172">Each concrete test class then inherits from this.</span></span>
<span data-ttu-id="f4534-173">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="f4534-173">For example:</span></span>

[!code-csharp[SqliteItemsControllerTest](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/SqliteItemsControllerTest.cs?name=SqliteItemsControllerTest)]

### <a name="test-structure"></a><span data-ttu-id="f4534-174">Struttura di test</span><span class="sxs-lookup"><span data-stu-id="f4534-174">Test structure</span></span>

<span data-ttu-id="f4534-175">Anche se l'applicazione usa l'inserimento delle dipendenze, i test non lo eseguono.</span><span class="sxs-lookup"><span data-stu-id="f4534-175">Even though the application uses dependency injection, the tests do not.</span></span>
<span data-ttu-id="f4534-176">Per usare l'inserimento delle dipendenze, tuttavia, il codice aggiuntivo richiesto ha un valore minimo.</span><span class="sxs-lookup"><span data-stu-id="f4534-176">It would be fine to use dependency injection here, but the additional code it requires has little value.</span></span>
<span data-ttu-id="f4534-177">Viene invece creato un DbContext utilizzando `new` e quindi passato direttamente come dipendenza al controller.</span><span class="sxs-lookup"><span data-stu-id="f4534-177">Instead, a DbContext is created using `new` and then directly passed as the dependency to the controller.</span></span>

<span data-ttu-id="f4534-178">Ogni test esegue quindi il metodo sottoposto a test sul controller e dichiara i risultati come previsto.</span><span class="sxs-lookup"><span data-stu-id="f4534-178">Each test then executes the method under test on the controller and asserts the results are as expected.</span></span>
<span data-ttu-id="f4534-179">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="f4534-179">For example:</span></span>

[!code-csharp[CanGetItems](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanGetItems)]

<span data-ttu-id="f4534-180">Si noti che per eseguire il seeding del database e per eseguire i test vengono usate istanze DbContext diverse.</span><span class="sxs-lookup"><span data-stu-id="f4534-180">Notice that different DbContext instances are used to seed the database and run the tests.</span></span> <span data-ttu-id="f4534-181">In questo modo si garantisce che il test non stia usando (o inciampare) le entità rilevate dal contesto durante il seeding.</span><span class="sxs-lookup"><span data-stu-id="f4534-181">This ensures that the test is not using (or tripping over) entities tracked by the context when seeding.</span></span>
<span data-ttu-id="f4534-182">Corrisponde inoltre a ciò che accade in app Web e servizi.</span><span class="sxs-lookup"><span data-stu-id="f4534-182">It also better matches what happens in web apps and services.</span></span>

<span data-ttu-id="f4534-183">I test che mutano il database creano una seconda istanza di DbContext nel test per motivi analoghi.</span><span class="sxs-lookup"><span data-stu-id="f4534-183">Tests that mutate the database create a second DbContext instance in the test for similar reasons.</span></span>
<span data-ttu-id="f4534-184">Ovvero creando un nuovo contesto pulito e quindi leggendolo dal database per assicurarsi che le modifiche siano state salvate nel database.</span><span class="sxs-lookup"><span data-stu-id="f4534-184">That is, creating a new, clean, context and then reading into it from the database to ensure that the changes were saved to the database.</span></span> <span data-ttu-id="f4534-185">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="f4534-185">For example:</span></span>

[!code-csharp[CanAddItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddItem)]

<span data-ttu-id="f4534-186">Due test leggermente più necessari coprono la logica di business in merito all'aggiunta di tag.</span><span class="sxs-lookup"><span data-stu-id="f4534-186">Two slightly more involved tests cover the business logic around adding tags.</span></span>

[!code-csharp[CanAddTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddTag)]

[!code-csharp[CanUpTagCount](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanUpTagCount)]

## <a name="issues-using-different-database-providers"></a><span data-ttu-id="f4534-187">Problemi relativi all'utilizzo di provider di database diversi</span><span class="sxs-lookup"><span data-stu-id="f4534-187">Issues using different database providers</span></span>

<span data-ttu-id="f4534-188">Il test con un sistema di database diverso da quello usato nell'applicazione di produzione può causare problemi.</span><span class="sxs-lookup"><span data-stu-id="f4534-188">Testing with a different database system than is used in the production application can lead to problems.</span></span>
<span data-ttu-id="f4534-189">Questi sono trattati a livello concettuale nel [codice di test che usa EF Core](xref:core/miscellaneous/testing/index).</span><span class="sxs-lookup"><span data-stu-id="f4534-189">These are covered at the conceptual level in [Testing code that uses EF Core](xref:core/miscellaneous/testing/index).</span></span>  
<span data-ttu-id="f4534-190">Le sezioni seguenti illustrano due esempi di problemi che si verificano nei test di questo esempio.</span><span class="sxs-lookup"><span data-stu-id="f4534-190">The sections below cover two examples of such issues demonstrated by the tests in this sample.</span></span>

### <a name="test-passes-when-the-application-is-broken"></a><span data-ttu-id="f4534-191">Test superato quando l'applicazione è interruppe</span><span class="sxs-lookup"><span data-stu-id="f4534-191">Test passes when the application is broken</span></span>

<span data-ttu-id="f4534-192">Uno dei requisiti per l'applicazione è che " Items hanno un nome con distinzione tra maiuscole e minuscole e una raccolta di Tags ".</span><span class="sxs-lookup"><span data-stu-id="f4534-192">One of the requirements for our application is that "Items have a case-sensitive name and a collection of Tags."</span></span>
<span data-ttu-id="f4534-193">Questo è piuttosto semplice da testare:</span><span class="sxs-lookup"><span data-stu-id="f4534-193">This is pretty simple to test:</span></span>

[!code-csharp[CanAddItemCaseInsensitive](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddItemCaseInsensitive)]

<span data-ttu-id="f4534-194">L'esecuzione di questo test nel database in memoria EF indica che tutto funziona correttamente.</span><span class="sxs-lookup"><span data-stu-id="f4534-194">Running this test against the EF in-memory database indicates that everything is fine.</span></span>
<span data-ttu-id="f4534-195">Tutto sembra corretto quando si usa SQLite.</span><span class="sxs-lookup"><span data-stu-id="f4534-195">Everything still looks fine when using SQLite.</span></span>
<span data-ttu-id="f4534-196">Tuttavia, il test ha esito negativo quando viene eseguito su SQL Server.</span><span class="sxs-lookup"><span data-stu-id="f4534-196">But the test fails when run against SQL Server!</span></span>

```
System.InvalidOperationException : Sequence contains more than one element
   at System.Linq.ThrowHelper.ThrowMoreThanOneElementException()
   at System.Linq.Enumerable.Single[TSource](IEnumerable`1 source)
   at Microsoft.EntityFrameworkCore.Query.Internal.QueryCompiler.Execute[TResult](Expression query)
   at Microsoft.EntityFrameworkCore.Query.Internal.EntityQueryProvider.Execute[TResult](Expression expression)
   at System.Linq.Queryable.Single[TSource](IQueryable`1 source, Expression`1 predicate)
   at Tests.ItemsControllerTest.Can_add_item_differing_only_by_case()
```

<span data-ttu-id="f4534-197">Ciò è dovuto al fatto che il database in memoria EF e il database SQLite fanno distinzione tra maiuscole e minuscole per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="f4534-197">This is because both the EF in-memory database and the SQLite database are case-sensitive by default.</span></span>
<span data-ttu-id="f4534-198">SQL Server, d'altra parte, non fa distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="f4534-198">SQL Server, on the other hand, is case-insensitive!</span></span> 

<span data-ttu-id="f4534-199">EF Core, da progettazione, non modifica questi comportamenti perché la forzatura di una modifica con distinzione maiuscole/minuscole può avere un notevole effetto sulle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="f4534-199">EF Core, by design, does not change these behaviors because forcing a change in case-sensitivity can have a big performance impact.</span></span>

<span data-ttu-id="f4534-200">Quando sappiamo che questo è un problema, possiamo correggere l'applicazione e compensare i test.</span><span class="sxs-lookup"><span data-stu-id="f4534-200">Once we know this is a problem we can fix the application and compensate in tests.</span></span>
<span data-ttu-id="f4534-201">Tuttavia, il punto in questo caso è che questo bug può essere ignorato se si verifica solo con il database in memoria EF o con i provider SQLite.</span><span class="sxs-lookup"><span data-stu-id="f4534-201">However, the point here is that this bug could be missed if only testing with the EF in-memory database or SQLite providers.</span></span>

### <a name="test-fails-when-the-application-is-correct"></a><span data-ttu-id="f4534-202">Il test ha esito negativo quando l'applicazione è corretta</span><span class="sxs-lookup"><span data-stu-id="f4534-202">Test fails when the application is correct</span></span> 

<span data-ttu-id="f4534-203">Un altro requisito per l'applicazione è che "l'eliminazione di un Item dovrebbe eliminare tutti gli associati Tags ".</span><span class="sxs-lookup"><span data-stu-id="f4534-203">Another of the requirements for our application is that "deleting an Item should delete all associated Tags."</span></span>
<span data-ttu-id="f4534-204">Anche in questo caso, è facile da testare:</span><span class="sxs-lookup"><span data-stu-id="f4534-204">Again, easy to test:</span></span>

[!code-csharp[DeleteItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=DeleteItem)]

<span data-ttu-id="f4534-205">Questo test viene superato SQL Server e SQLite, ma ha esito negativo con il database in memoria EF.</span><span class="sxs-lookup"><span data-stu-id="f4534-205">This test passes on SQL Server and SQLite, but fails with the EF in-memory database!</span></span>

```
Assert.False() Failure
Expected: False
Actual:   True
   at Tests.ItemsControllerTest.Can_remove_item_and_all_associated_tags()
```

<span data-ttu-id="f4534-206">In questo caso, l'applicazione funziona correttamente perché SQL Server supporta le [eliminazioni a catena](xref:core/saving/cascade-delete).</span><span class="sxs-lookup"><span data-stu-id="f4534-206">In this case, the application is working correctly because SQL Server supports [cascade deletes](xref:core/saving/cascade-delete).</span></span> <span data-ttu-id="f4534-207">SQLite supporta inoltre le eliminazioni a catena, così come la maggior parte dei database relazionali, quindi il test di questa operazione su SQLite funziona.</span><span class="sxs-lookup"><span data-stu-id="f4534-207">SQLite also supports cascade deletes, as do most relational databases, so testing this on SQLite works.</span></span>
<span data-ttu-id="f4534-208">D'altra parte, il database in memoria EF [non supporta le eliminazioni a catena](https://github.com/dotnet/efcore/issues/3924).</span><span class="sxs-lookup"><span data-stu-id="f4534-208">On the other hand, the EF in-memory database [does not support cascade deletes](https://github.com/dotnet/efcore/issues/3924).</span></span>
<span data-ttu-id="f4534-209">Questo significa che questa parte dell'applicazione non può essere testata con il provider di database in memoria EF.</span><span class="sxs-lookup"><span data-stu-id="f4534-209">This means that this part of the application cannot be tested with the EF in-memory database provider.</span></span>