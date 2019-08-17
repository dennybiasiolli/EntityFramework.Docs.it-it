---
title: Caricamento di entità correlate-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: c8417e18-a2ee-499c-9ce9-2a48cc5b468a
ms.openlocfilehash: f40034475ed6659b60ab4317605fd1d802218d69
ms.sourcegitcommit: 7b7f774a5966b20d2aed5435a672a1edbe73b6fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/17/2019
ms.locfileid: "69565304"
---
# <a name="loading-related-entities"></a><span data-ttu-id="2b1f0-102">Caricamento di entità correlate</span><span class="sxs-lookup"><span data-stu-id="2b1f0-102">Loading Related Entities</span></span>
<span data-ttu-id="2b1f0-103">Entity Framework supporta tre modi per caricare dati correlati, caricamento lazy e caricamento esplicito.</span><span class="sxs-lookup"><span data-stu-id="2b1f0-103">Entity Framework supports three ways to load related data - eager loading, lazy loading and explicit loading.</span></span> <span data-ttu-id="2b1f0-104">Le tecniche illustrate in questo argomento si applicano in modo analogo ai modelli creati con Code First ed EF Designer.</span><span class="sxs-lookup"><span data-stu-id="2b1f0-104">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="eagerly-loading"></a><span data-ttu-id="2b1f0-105">Caricamento eager</span><span class="sxs-lookup"><span data-stu-id="2b1f0-105">Eagerly Loading</span></span>  

<span data-ttu-id="2b1f0-106">Il caricamento eager è il processo in base al quale una query per un tipo di entità carica anche le entità correlate come parte della query.</span><span class="sxs-lookup"><span data-stu-id="2b1f0-106">Eager loading is the process whereby a query for one type of entity also loads related entities as part of the query.</span></span> <span data-ttu-id="2b1f0-107">Il caricamento eager viene effettuato tramite il metodo include.</span><span class="sxs-lookup"><span data-stu-id="2b1f0-107">Eager loading is achieved by use of the Include method.</span></span> <span data-ttu-id="2b1f0-108">Ad esempio, le query seguenti caricherà i Blog e tutti i post correlati a ogni blog.</span><span class="sxs-lookup"><span data-stu-id="2b1f0-108">For example, the queries below will load blogs and all the posts related to each blog.</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs and related posts
    var blogs1 = context.Blogs
                        .Include(b => b.Posts)
                        .ToList();

    // Load one blog and its related posts
    var blog1 = context.Blogs
                       .Where(b => b.Name == "ADO.NET Blog")
                       .Include(b => b.Posts)
                       .FirstOrDefault();

    // Load all blogs and related posts  
    // using a string to specify the relationship
    var blogs2 = context.Blogs
                        .Include("Posts")
                        .ToList();

    // Load one blog and its related posts  
    // using a string to specify the relationship
    var blog2 = context.Blogs
                       .Where(b => b.Name == "ADO.NET Blog")
                       .Include("Posts")
                       .FirstOrDefault();
}
```  

<span data-ttu-id="2b1f0-109">Si noti che include è un metodo di estensione nello spazio dei nomi System. Data. Entity, quindi assicurarsi di usare tale spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="2b1f0-109">Note that Include is an extension method in the System.Data.Entity namespace so make sure you are using that namespace.</span></span>  

### <a name="eagerly-loading-multiple-levels"></a><span data-ttu-id="2b1f0-110">Caricamento eager di più livelli</span><span class="sxs-lookup"><span data-stu-id="2b1f0-110">Eagerly loading multiple levels</span></span>  

<span data-ttu-id="2b1f0-111">È anche possibile caricare con entusiasmo più livelli di entità correlate.</span><span class="sxs-lookup"><span data-stu-id="2b1f0-111">It is also possible to eagerly load multiple levels of related entities.</span></span> <span data-ttu-id="2b1f0-112">Nelle query riportate di seguito vengono illustrati esempi di come eseguire questa operazione sia per le proprietà di navigazione di raccolta che di riferimento</span><span class="sxs-lookup"><span data-stu-id="2b1f0-112">The queries below show examples of how to do this for both collection and reference navigation properties.</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs, all related posts, and all related comments
    var blogs1 = context.Blogs
                        .Include(b => b.Posts.Select(p => p.Comments))
                        .ToList();

    // Load all users, their related profiles, and related avatar
    var users1 = context.Users
                        .Include(u => u.Profile.Avatar)
                        .ToList();

    // Load all blogs, all related posts, and all related comments  
    // using a string to specify the relationships
    var blogs2 = context.Blogs
                        .Include("Posts.Comments")
                        .ToList();

    // Load all users, their related profiles, and related avatar  
    // using a string to specify the relationships
    var users2 = context.Users
                        .Include("Profile.Avatar")
                        .ToList();
}
```  

<span data-ttu-id="2b1f0-113">Si noti che non è attualmente possibile filtrare le entità correlate caricate.</span><span class="sxs-lookup"><span data-stu-id="2b1f0-113">Note that it is not currently possible to filter which related entities are loaded.</span></span> <span data-ttu-id="2b1f0-114">Includi porta sempre in tutte le entità correlate.</span><span class="sxs-lookup"><span data-stu-id="2b1f0-114">Include will always bring in all related entities.</span></span>  

## <a name="lazy-loading"></a><span data-ttu-id="2b1f0-115">Caricamento lazy</span><span class="sxs-lookup"><span data-stu-id="2b1f0-115">Lazy Loading</span></span>  

<span data-ttu-id="2b1f0-116">Il caricamento lazy è il processo in base al quale un'entità o una raccolta di entità viene caricata automaticamente dal database la prima volta che si accede a una proprietà che fa riferimento all'entità o alle entità.</span><span class="sxs-lookup"><span data-stu-id="2b1f0-116">Lazy loading is the process whereby an entity or collection of entities is automatically loaded from the database the first time that a property referring to the entity/entities is accessed.</span></span> <span data-ttu-id="2b1f0-117">Quando si usano i tipi di entità POCO, il caricamento lazy viene ottenuto creando istanze di tipi proxy derivati e quindi eseguendo l'override delle proprietà virtuali per aggiungere l'hook di caricamento.</span><span class="sxs-lookup"><span data-stu-id="2b1f0-117">When using POCO entity types, lazy loading is achieved by creating instances of derived proxy types and then overriding virtual properties to add the loading hook.</span></span> <span data-ttu-id="2b1f0-118">Ad esempio, quando si usa la classe di entità Blog definita di seguito, i post correlati verranno caricati la prima volta che si accede alla proprietà di navigazione post:</span><span class="sxs-lookup"><span data-stu-id="2b1f0-118">For example, when using the Blog entity class defined below, the related Posts will be loaded the first time the Posts navigation property is accessed:</span></span>  

``` csharp
public class Blog
{  
    public int BlogId { get; set; }  
    public string Name { get; set; }  
    public string Url { get; set; }  
    public string Tags { get; set; }  

    public virtual ICollection<Post> Posts { get; set; }  
}
```  

### <a name="turn-lazy-loading-off-for-serialization"></a><span data-ttu-id="2b1f0-119">Disabilitare il caricamento lazy per la serializzazione</span><span class="sxs-lookup"><span data-stu-id="2b1f0-119">Turn lazy loading off for serialization</span></span>  

<span data-ttu-id="2b1f0-120">Il caricamento lazy e la serializzazione non sono combinati correttamente e, se non si presta attenzione, è possibile eseguire query per l'intero database solo perché il caricamento lazy è abilitato.</span><span class="sxs-lookup"><span data-stu-id="2b1f0-120">Lazy loading and serialization don’t mix well, and if you aren’t careful you can end up querying for your entire database just because lazy loading is enabled.</span></span> <span data-ttu-id="2b1f0-121">La maggior parte dei serializzatori funziona accedendo a ogni proprietà in un'istanza di un tipo.</span><span class="sxs-lookup"><span data-stu-id="2b1f0-121">Most serializers work by accessing each property on an instance of a type.</span></span> <span data-ttu-id="2b1f0-122">L'accesso alle proprietà attiva il caricamento lazy, in modo che vengano serializzate più entità.</span><span class="sxs-lookup"><span data-stu-id="2b1f0-122">Property access triggers lazy loading, so more entities get serialized.</span></span> <span data-ttu-id="2b1f0-123">È possibile accedere alle proprietà delle entità e vengono caricate anche altre entità.</span><span class="sxs-lookup"><span data-stu-id="2b1f0-123">On those entities properties are accessed, and even more entities are loaded.</span></span> <span data-ttu-id="2b1f0-124">È consigliabile disattivare il caricamento lazy prima di serializzare un'entità.</span><span class="sxs-lookup"><span data-stu-id="2b1f0-124">It’s a good practice to turn lazy loading off before you serialize an entity.</span></span> <span data-ttu-id="2b1f0-125">Le sezioni seguenti illustrano come eseguire questa operazione.</span><span class="sxs-lookup"><span data-stu-id="2b1f0-125">The following sections show how to do this.</span></span>  

### <a name="turning-off-lazy-loading-for-specific-navigation-properties"></a><span data-ttu-id="2b1f0-126">Disattivazione del caricamento lazy per specifiche proprietà di navigazione</span><span class="sxs-lookup"><span data-stu-id="2b1f0-126">Turning off lazy loading for specific navigation properties</span></span>  

<span data-ttu-id="2b1f0-127">Il caricamento lazy della raccolta post può essere disattivato rendendo la proprietà post non virtuale:</span><span class="sxs-lookup"><span data-stu-id="2b1f0-127">Lazy loading of the Posts collection can be turned off by making the Posts property non-virtual:</span></span>  

``` csharp
public class Blog
{  
    public int BlogId { get; set; }  
    public string Name { get; set; }  
    public string Url { get; set; }  
    public string Tags { get; set; }  

    public ICollection<Post> Posts { get; set; }  
}
```  

<span data-ttu-id="2b1f0-128">Il caricamento della raccolta dei post può ancora essere eseguito usando il caricamento eager (vedere *caricamento eager* sopra) o il metodo Load (vedere *caricamento esplicito* di seguito).</span><span class="sxs-lookup"><span data-stu-id="2b1f0-128">Loading of the Posts collection can still be achieved using eager loading (see *Eagerly Loading* above) or the Load method (see *Explicitly Loading* below).</span></span>  

### <a name="turn-off-lazy-loading-for-all-entities"></a><span data-ttu-id="2b1f0-129">Disattiva caricamento lazy per tutte le entità</span><span class="sxs-lookup"><span data-stu-id="2b1f0-129">Turn off lazy loading for all entities</span></span>  

<span data-ttu-id="2b1f0-130">Il caricamento lazy può essere disattivato per tutte le entità nel contesto impostando un flag sulla proprietà di configurazione.</span><span class="sxs-lookup"><span data-stu-id="2b1f0-130">Lazy loading can be turned off for all entities in the context by setting a flag on the Configuration property.</span></span> <span data-ttu-id="2b1f0-131">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="2b1f0-131">For example:</span></span>  

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext()
    {
        this.Configuration.LazyLoadingEnabled = false;
    }
}
```  

<span data-ttu-id="2b1f0-132">Il caricamento di entità correlate può comunque essere eseguito usando il caricamento eager (vedere *caricamento eager* sopra) o il metodo Load (vedere *caricamento esplicito* di seguito).</span><span class="sxs-lookup"><span data-stu-id="2b1f0-132">Loading of related entities can still be achieved using eager loading (see *Eagerly Loading* above) or the Load method (see *Explicitly Loading* below).</span></span>  

## <a name="explicitly-loading"></a><span data-ttu-id="2b1f0-133">Caricamento esplicito</span><span class="sxs-lookup"><span data-stu-id="2b1f0-133">Explicitly Loading</span></span>  

<span data-ttu-id="2b1f0-134">Anche con il caricamento lazy disabilitato, è comunque possibile caricare in modo differito le entità correlate, ma è necessario eseguire una chiamata esplicita.</span><span class="sxs-lookup"><span data-stu-id="2b1f0-134">Even with lazy loading disabled it is still possible to lazily load related entities, but it must be done with an explicit call.</span></span> <span data-ttu-id="2b1f0-135">A tale scopo, utilizzare il metodo Load sulla voce dell'entità correlata.</span><span class="sxs-lookup"><span data-stu-id="2b1f0-135">To do so you use the Load method on the related entity’s entry.</span></span> <span data-ttu-id="2b1f0-136">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="2b1f0-136">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var post = context.Posts.Find(2);

    // Load the blog related to a given post
    context.Entry(post).Reference(p => p.Blog).Load();

    // Load the blog related to a given post using a string  
    context.Entry(post).Reference("Blog").Load();

    var blog = context.Blogs.Find(1);

    // Load the posts related to a given blog
    context.Entry(blog).Collection(p => p.Posts).Load();

    // Load the posts related to a given blog  
    // using a string to specify the relationship
    context.Entry(blog).Collection("Posts").Load();
}
```  

<span data-ttu-id="2b1f0-137">Si noti che il metodo di riferimento deve essere utilizzato quando un'entità dispone di una proprietà di navigazione per un'altra entità singola.</span><span class="sxs-lookup"><span data-stu-id="2b1f0-137">Note that the Reference method should be used when an entity has a navigation property to another single entity.</span></span> <span data-ttu-id="2b1f0-138">D'altra parte, il metodo di raccolta deve essere usato quando un'entità dispone di una proprietà di navigazione per una raccolta di altre entità.</span><span class="sxs-lookup"><span data-stu-id="2b1f0-138">On the other hand, the Collection method should be used when an entity has a navigation property to a collection of other entities.</span></span>  

### <a name="applying-filters-when-explicitly-loading-related-entities"></a><span data-ttu-id="2b1f0-139">Applicazione di filtri quando si caricano in modo esplicito le entità correlate</span><span class="sxs-lookup"><span data-stu-id="2b1f0-139">Applying filters when explicitly loading related entities</span></span>  

<span data-ttu-id="2b1f0-140">Il metodo di query consente di accedere alla query sottostante che Entity Framework utilizzerà per il caricamento di entità correlate.</span><span class="sxs-lookup"><span data-stu-id="2b1f0-140">The Query method provides access to the underlying query that Entity Framework will use when loading related entities.</span></span> <span data-ttu-id="2b1f0-141">È quindi possibile usare LINQ per applicare filtri alla query prima di eseguirla con una chiamata a un metodo di estensione LINQ, ad esempio ToList, Load e così via. Il metodo di query può essere utilizzato sia con proprietà di navigazione di riferimento che di raccolta, ma è particolarmente utile per le raccolte in cui può essere utilizzato per caricare solo parte della raccolta.</span><span class="sxs-lookup"><span data-stu-id="2b1f0-141">You can then use LINQ to apply filters to the query before executing it with a call to a LINQ extension method such as ToList, Load, etc. The Query method can be used with both reference and collection navigation properties but is most useful for collections where it can be used to load only part of the collection.</span></span> <span data-ttu-id="2b1f0-142">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="2b1f0-142">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Load the posts with the 'entity-framework' tag related to a given blog
    context.Entry(blog)
           .Collection(b => b.Posts)
           .Query()
           .Where(p => p.Tags.Contains("entity-framework"))
           .Load();

    // Load the posts with the 'entity-framework' tag related to a given blog  
    // using a string to specify the relationship  
    context.Entry(blog)
           .Collection("Posts")
           .Query()
           .Where(p => p.Tags.Contains("entity-framework"))
           .Load();
}
```  

<span data-ttu-id="2b1f0-143">Quando si usa il metodo di query, in genere è preferibile disattivare il caricamento lazy per la proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="2b1f0-143">When using the Query method it is usually best to turn off lazy loading for the navigation property.</span></span> <span data-ttu-id="2b1f0-144">Questo perché, in caso contrario, l'intera raccolta potrebbe essere caricata automaticamente dal meccanismo di caricamento lazy prima o dopo l'esecuzione della query filtrata.</span><span class="sxs-lookup"><span data-stu-id="2b1f0-144">This is because otherwise the entire collection may get loaded automatically by the lazy loading mechanism either before or after the filtered query has been executed.</span></span>  

<span data-ttu-id="2b1f0-145">Si noti che, mentre la relazione può essere specificata come stringa anziché come espressione lambda, l'oggetto IQueryable restituito non è generico quando viene utilizzata una stringa e quindi il metodo Cast è in genere necessario prima che qualsiasi operazione utile possa essere eseguita.</span><span class="sxs-lookup"><span data-stu-id="2b1f0-145">Note that while the relationship can be specified as a string instead of a lambda expression, the returned IQueryable is not generic when a string is used and so the Cast method is usually needed before anything useful can be done with it.</span></span>  

## <a name="using-query-to-count-related-entities-without-loading-them"></a><span data-ttu-id="2b1f0-146">Utilizzo di query per conteggiare entità correlate senza caricarle</span><span class="sxs-lookup"><span data-stu-id="2b1f0-146">Using Query to count related entities without loading them</span></span>  

<span data-ttu-id="2b1f0-147">A volte è utile sapere quante entità sono correlate a un'altra entità nel database senza dover effettivamente sostenere il costo del caricamento di tutte le entità.</span><span class="sxs-lookup"><span data-stu-id="2b1f0-147">Sometimes it is useful to know how many entities are related to another entity in the database without actually incurring the cost of loading all those entities.</span></span> <span data-ttu-id="2b1f0-148">Per eseguire questa operazione, è possibile usare il metodo di query con il metodo di conteggio LINQ.</span><span class="sxs-lookup"><span data-stu-id="2b1f0-148">The Query method with the LINQ Count method can be used to do this.</span></span> <span data-ttu-id="2b1f0-149">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="2b1f0-149">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Count how many posts the blog has  
    var postCount = context.Entry(blog)
                           .Collection(b => b.Posts)
                           .Query()
                           .Count();
}
```  
