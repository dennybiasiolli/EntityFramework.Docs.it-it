---
title: Test con InMemory - Core a Entity Framework
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 0d0590f1-1ea3-4d5c-8f44-db17395cd3f3
ms.technology: entity-framework-core
uid: core/miscellaneous/testing/in-memory
ms.openlocfilehash: 33690e3424d0777930d3cb8167575fb0f4ddd8f7
ms.sourcegitcommit: d096484dcf9eff73d9943fa60db7a418b10ca0b3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2018
ms.locfileid: "27995587"
---
# <a name="testing-with-inmemory"></a><span data-ttu-id="35806-102">Test con InMemory</span><span class="sxs-lookup"><span data-stu-id="35806-102">Testing with InMemory</span></span>

<span data-ttu-id="35806-103">Il provider InMemory è utile quando si desidera testare i componenti dell'utilizzo di un elemento che si avvicinano a connettersi al database reale, senza l'overhead delle operazioni di database effettivo.</span><span class="sxs-lookup"><span data-stu-id="35806-103">The InMemory provider is useful when you want to test components using something that approximates connecting to the real database, without the overhead of actual database operations.</span></span>

> [!TIP]  
> <span data-ttu-id="35806-104">È possibile visualizzare l'[esempio](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing) di questo articolo in GitHub.</span><span class="sxs-lookup"><span data-stu-id="35806-104">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing) on GitHub.</span></span>

## <a name="inmemory-is-not-a-relational-database"></a><span data-ttu-id="35806-105">InMemory non è un database relazionale</span><span class="sxs-lookup"><span data-stu-id="35806-105">InMemory is not a relational database</span></span>

<span data-ttu-id="35806-106">Provider di database di sistema di EF non debbano essere database relazionali.</span><span class="sxs-lookup"><span data-stu-id="35806-106">EF Core database providers do not have to be relational databases.</span></span> <span data-ttu-id="35806-107">InMemory è progettato per essere un database generico per il testing e non è progettato per simulare un database relazionale.</span><span class="sxs-lookup"><span data-stu-id="35806-107">InMemory is designed to be a general purpose database for testing, and is not designed to mimic a relational database.</span></span>

<span data-ttu-id="35806-108">Alcuni esempi sono:</span><span class="sxs-lookup"><span data-stu-id="35806-108">Some examples of this include:</span></span>
* <span data-ttu-id="35806-109">InMemory consente di salvare i dati che violano i vincoli di integrità referenziale in un database relazionale.</span><span class="sxs-lookup"><span data-stu-id="35806-109">InMemory will allow you to save data that would violate referential integrity constraints in a relational database.</span></span>

* <span data-ttu-id="35806-110">Se si utilizza DefaultValueSql(string) per una proprietà nel modello, questa è un'API di database relazionale e si avrà alcun effetto durante l'esecuzione InMemory.</span><span class="sxs-lookup"><span data-stu-id="35806-110">If you use DefaultValueSql(string) for a property in your model, this is a relational database API and will have no effect when running against InMemory.</span></span>

> [!TIP]  
> <span data-ttu-id="35806-111">Queste differenze verranno è importante per molti scopi di test.</span><span class="sxs-lookup"><span data-stu-id="35806-111">For many test purposes these differences will not matter.</span></span> <span data-ttu-id="35806-112">Tuttavia, se si desidera confrontare con un elemento che è più simile a un database relazionale true, è consigliabile utilizzare [modalità in-memory SQLite](sqlite.md).</span><span class="sxs-lookup"><span data-stu-id="35806-112">However, if you want to test against something that behaves more like a true relational database, then consider using [SQLite in-memory mode](sqlite.md).</span></span>

## <a name="example-testing-scenario"></a><span data-ttu-id="35806-113">Scenario di test di esempio</span><span class="sxs-lookup"><span data-stu-id="35806-113">Example testing scenario</span></span>

<span data-ttu-id="35806-114">Si consideri il seguente servizio che consente al codice dell'applicazione eseguire alcune operazioni correlate ai blog.</span><span class="sxs-lookup"><span data-stu-id="35806-114">Consider the following service that allows application code to perform some operations related to blogs.</span></span> <span data-ttu-id="35806-115">Utilizza internamente un `DbContext` che si connette a un database di SQL Server.</span><span class="sxs-lookup"><span data-stu-id="35806-115">Internally it uses a `DbContext` that connects to a SQL Server database.</span></span> <span data-ttu-id="35806-116">Potrebbe essere utile scambiare il contesto per la connessione a un database InMemory in modo che è possibile scrivere test efficiente per questo servizio senza dover modificare il codice o eseguire molte operazioni per creare un test double del contesto.</span><span class="sxs-lookup"><span data-stu-id="35806-116">It would be useful to swap this context to connect to an InMemory database so that we can write efficient tests for this service without having to modify the code, or do a lot of work to create a test double of the context.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BlogService.cs)]

## <a name="get-your-context-ready"></a><span data-ttu-id="35806-117">Preparare il contesto</span><span class="sxs-lookup"><span data-stu-id="35806-117">Get your context ready</span></span>

### <a name="avoid-configuring-two-database-providers"></a><span data-ttu-id="35806-118">Evitare di configurare due provider di database</span><span class="sxs-lookup"><span data-stu-id="35806-118">Avoid configuring two database providers</span></span>

<span data-ttu-id="35806-119">I test si intende configurare esternamente il contesto per utilizzare il provider InMemory.</span><span class="sxs-lookup"><span data-stu-id="35806-119">In your tests you are going to externally configure the context to use the InMemory provider.</span></span> <span data-ttu-id="35806-120">Se si sta configurando un provider di database eseguendo l'override `OnConfiguring` nel contesto, è necessario aggiungere il codice condizionale per assicurarsi di configurare il provider del database solo se non ne è stato configurato.</span><span class="sxs-lookup"><span data-stu-id="35806-120">If you are configuring a database provider by overriding `OnConfiguring` in your context, then you need to add some conditional code to ensure that you only configure the database provider if one has not already been configured.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#OnConfiguring)]

> [!TIP]  
> <span data-ttu-id="35806-121">Se si utilizza ASP.NET Core, quindi non è necessario il codice perché il provider di database è già configurato all'esterno del contesto (in Startup.cs).</span><span class="sxs-lookup"><span data-stu-id="35806-121">If you are using ASP.NET Core, then you should not need this code since your database provider is already configured outside of the context (in Startup.cs).</span></span>

### <a name="add-a-constructor-for-testing"></a><span data-ttu-id="35806-122">Aggiungere un costruttore per il test</span><span class="sxs-lookup"><span data-stu-id="35806-122">Add a constructor for testing</span></span>

<span data-ttu-id="35806-123">Il modo più semplice per consentire i test in un database diverso consiste nel modificare il contesto per esporre un costruttore che accetta un `DbContextOptions<TContext>`.</span><span class="sxs-lookup"><span data-stu-id="35806-123">The simplest way to enable testing against a different database is to modify your context to expose a constructor that accepts a `DbContextOptions<TContext>`.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#Constructors)]

> [!TIP]  
> <span data-ttu-id="35806-124">`DbContextOptions<TContext>`indica il contesto di tutte le relative impostazioni, ad esempio il database a cui connettersi.</span><span class="sxs-lookup"><span data-stu-id="35806-124">`DbContextOptions<TContext>` tells the context all of its settings, such as which database to connect to.</span></span> <span data-ttu-id="35806-125">Questo è lo stesso oggetto che viene compilato mediante l'esecuzione del metodo OnConfiguring nel contesto.</span><span class="sxs-lookup"><span data-stu-id="35806-125">This is the same object that is built by running the OnConfiguring method in your context.</span></span>

## <a name="writing-tests"></a><span data-ttu-id="35806-126">Scrittura di test</span><span class="sxs-lookup"><span data-stu-id="35806-126">Writing tests</span></span>

<span data-ttu-id="35806-127">La chiave per il test con questo provider è la possibilità di stabilire il contesto da utilizzare il provider InMemory e controllare l'ambito del database in memoria.</span><span class="sxs-lookup"><span data-stu-id="35806-127">The key to testing with this provider is the ability to tell the context to use the InMemory provider, and control the scope of the in-memory database.</span></span> <span data-ttu-id="35806-128">In genere si desidera il database originale per ogni metodo di test.</span><span class="sxs-lookup"><span data-stu-id="35806-128">Typically you want a clean database for each test method.</span></span>

<span data-ttu-id="35806-129">Di seguito è riportato un esempio di una classe di test che utilizza il database InMemory.</span><span class="sxs-lookup"><span data-stu-id="35806-129">Here is an example of a test class that uses the InMemory database.</span></span> <span data-ttu-id="35806-130">Ogni metodo di test specifica un nome di database univoco, vale a dire che ogni metodo presenta un proprio database InMemory.</span><span class="sxs-lookup"><span data-stu-id="35806-130">Each test method specifies a unique database name, meaning each method has its own InMemory database.</span></span>

>[!TIP]
> <span data-ttu-id="35806-131">Utilizzare il `.UseInMemoryDatabase()` metodo di estensione, il pacchetto NuGet di riferimento `Microsoft.EntityFrameworkCore.InMemory`.</span><span class="sxs-lookup"><span data-stu-id="35806-131">To use the `.UseInMemoryDatabase()` extension method, reference the NuGet package `Microsoft.EntityFrameworkCore.InMemory`.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/TestProject/InMemory/BlogServiceTests.cs)]
