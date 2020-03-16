---
title: Relazioni-EF Core
description: Come configurare le relazioni tra i tipi di entità quando si usa Entity Framework Core
author: AndriySvyryd
ms.date: 11/21/2019
uid: core/modeling/relationships
ms.openlocfilehash: 6d68e813cec6c989e8e4cb848f8740489645c65c
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/15/2020
ms.locfileid: "79402166"
---
# <a name="relationships"></a><span data-ttu-id="85d88-103">Relazioni</span><span class="sxs-lookup"><span data-stu-id="85d88-103">Relationships</span></span>

<span data-ttu-id="85d88-104">Una relazione definisce il modo in cui due entità sono correlate tra loro.</span><span class="sxs-lookup"><span data-stu-id="85d88-104">A relationship defines how two entities relate to each other.</span></span> <span data-ttu-id="85d88-105">In un database relazionale, questo è rappresentato da un vincolo FOREIGN KEY.</span><span class="sxs-lookup"><span data-stu-id="85d88-105">In a relational database, this is represented by a foreign key constraint.</span></span>

> [!NOTE]  
> <span data-ttu-id="85d88-106">La maggior parte degli esempi in questo articolo usa una relazione uno-a-molti per illustrare i concetti.</span><span class="sxs-lookup"><span data-stu-id="85d88-106">Most of the samples in this article use a one-to-many relationship to demonstrate concepts.</span></span> <span data-ttu-id="85d88-107">Per esempi di relazioni uno-a-uno e molti-a-molti, vedere la sezione [altri modelli di relazione](#other-relationship-patterns) alla fine dell'articolo.</span><span class="sxs-lookup"><span data-stu-id="85d88-107">For examples of one-to-one and many-to-many relationships see the [Other Relationship Patterns](#other-relationship-patterns) section at the end of the article.</span></span>

## <a name="definition-of-terms"></a><span data-ttu-id="85d88-108">Definizione dei termini</span><span class="sxs-lookup"><span data-stu-id="85d88-108">Definition of terms</span></span>

<span data-ttu-id="85d88-109">Esistono diversi termini usati per descrivere le relazioni</span><span class="sxs-lookup"><span data-stu-id="85d88-109">There are a number of terms used to describe relationships</span></span>

* <span data-ttu-id="85d88-110">**Entità dipendente:** Si tratta dell'entità che contiene le proprietà di chiave esterna.</span><span class="sxs-lookup"><span data-stu-id="85d88-110">**Dependent entity:** This is the entity that contains the foreign key properties.</span></span> <span data-ttu-id="85d88-111">Noto anche come ' Child ' della relazione.</span><span class="sxs-lookup"><span data-stu-id="85d88-111">Sometimes referred to as the 'child' of the relationship.</span></span>

* <span data-ttu-id="85d88-112">**Entità principale:** Si tratta dell'entità che contiene le proprietà chiave primaria/alternativa.</span><span class="sxs-lookup"><span data-stu-id="85d88-112">**Principal entity:** This is the entity that contains the primary/alternate key properties.</span></span> <span data-ttu-id="85d88-113">Noto anche come ' Parent ' della relazione.</span><span class="sxs-lookup"><span data-stu-id="85d88-113">Sometimes referred to as the 'parent' of the relationship.</span></span>

* <span data-ttu-id="85d88-114">**Chiave principale:** Proprietà che identificano in modo univoco l'entità principale.</span><span class="sxs-lookup"><span data-stu-id="85d88-114">**Principal key:** The properties that uniquely identify the principal entity.</span></span> <span data-ttu-id="85d88-115">Può trattarsi della chiave primaria o di una chiave alternativa.</span><span class="sxs-lookup"><span data-stu-id="85d88-115">This may be the primary key or an alternate key.</span></span>

* <span data-ttu-id="85d88-116">**Chiave esterna:** Proprietà nell'entità dipendente utilizzate per archiviare i valori della chiave principale per l'entità correlata.</span><span class="sxs-lookup"><span data-stu-id="85d88-116">**Foreign key:** The properties in the dependent entity that are used to store the principal key values for the related entity.</span></span>

* <span data-ttu-id="85d88-117">**Proprietà di navigazione:** Proprietà definita nell'entità dipendente e/o dipendente che fa riferimento all'entità correlata.</span><span class="sxs-lookup"><span data-stu-id="85d88-117">**Navigation property:** A property defined on the principal and/or dependent entity that references the related entity.</span></span>

  * <span data-ttu-id="85d88-118">**Proprietà di navigazione della raccolta:** Proprietà di navigazione che contiene riferimenti a molte entità correlate.</span><span class="sxs-lookup"><span data-stu-id="85d88-118">**Collection navigation property:** A navigation property that contains references to many related entities.</span></span>

  * <span data-ttu-id="85d88-119">**Proprietà di navigazione riferimento:** Proprietà di navigazione che include un riferimento a una singola entità correlata.</span><span class="sxs-lookup"><span data-stu-id="85d88-119">**Reference navigation property:** A navigation property that holds a reference to a single related entity.</span></span>

  * <span data-ttu-id="85d88-120">**Proprietà di navigazione inversa:** Quando si discute una particolare proprietà di navigazione, questo termine fa riferimento alla proprietà di navigazione nell'altra entità finale della relazione.</span><span class="sxs-lookup"><span data-stu-id="85d88-120">**Inverse navigation property:** When discussing a particular navigation property, this term refers to the navigation property on the other end of the relationship.</span></span>
  
* <span data-ttu-id="85d88-121">**Relazione autoreferenziale:** Relazione in cui i tipi di entità dipendenti e entità sono uguali.</span><span class="sxs-lookup"><span data-stu-id="85d88-121">**Self-referencing relationship:** A relationship in which the dependent and the principal entity types are the same.</span></span>

<span data-ttu-id="85d88-122">Nel codice seguente viene illustrata una relazione uno-a-molti tra `Blog` e `Post`</span><span class="sxs-lookup"><span data-stu-id="85d88-122">The following code shows a one-to-many relationship between `Blog` and `Post`</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs#Full)]

* <span data-ttu-id="85d88-123">`Post` è l'entità dipendente</span><span class="sxs-lookup"><span data-stu-id="85d88-123">`Post` is the dependent entity</span></span>

* <span data-ttu-id="85d88-124">`Blog` è l'entità principale</span><span class="sxs-lookup"><span data-stu-id="85d88-124">`Blog` is the principal entity</span></span>

* <span data-ttu-id="85d88-125">`Blog.BlogId` è la chiave principale (in questo caso è una chiave primaria anziché una chiave alternativa)</span><span class="sxs-lookup"><span data-stu-id="85d88-125">`Blog.BlogId` is the principal key (in this case it is a primary key rather than an alternate key)</span></span>

* <span data-ttu-id="85d88-126">`Post.BlogId` è la chiave esterna</span><span class="sxs-lookup"><span data-stu-id="85d88-126">`Post.BlogId` is the foreign key</span></span>

* <span data-ttu-id="85d88-127">`Post.Blog` è una proprietà di navigazione di riferimento</span><span class="sxs-lookup"><span data-stu-id="85d88-127">`Post.Blog` is a reference navigation property</span></span>

* <span data-ttu-id="85d88-128">`Blog.Posts` è una proprietà di navigazione della raccolta</span><span class="sxs-lookup"><span data-stu-id="85d88-128">`Blog.Posts` is a collection navigation property</span></span>

* <span data-ttu-id="85d88-129">`Post.Blog` è la proprietà di navigazione inversa di `Blog.Posts` (e viceversa)</span><span class="sxs-lookup"><span data-stu-id="85d88-129">`Post.Blog` is the inverse navigation property of `Blog.Posts` (and vice versa)</span></span>

## <a name="conventions"></a><span data-ttu-id="85d88-130">Convenzioni</span><span class="sxs-lookup"><span data-stu-id="85d88-130">Conventions</span></span>

<span data-ttu-id="85d88-131">Per impostazione predefinita, viene creata una relazione quando viene individuata una proprietà di navigazione in un tipo.</span><span class="sxs-lookup"><span data-stu-id="85d88-131">By default, a relationship will be created when there is a navigation property discovered on a type.</span></span> <span data-ttu-id="85d88-132">Una proprietà viene considerata una proprietà di navigazione se il tipo a cui punta non può essere mappato come tipo scalare dal provider di database corrente.</span><span class="sxs-lookup"><span data-stu-id="85d88-132">A property is considered a navigation property if the type it points to can not be mapped as a scalar type by the current database provider.</span></span>

> [!NOTE]  
> <span data-ttu-id="85d88-133">Le relazioni individuate per convenzione saranno sempre destinate alla chiave primaria dell'entità principale.</span><span class="sxs-lookup"><span data-stu-id="85d88-133">Relationships that are discovered by convention will always target the primary key of the principal entity.</span></span> <span data-ttu-id="85d88-134">Per fare riferimento a una chiave alternativa, è necessario eseguire una configurazione aggiuntiva usando l'API Fluent.</span><span class="sxs-lookup"><span data-stu-id="85d88-134">To target an alternate key, additional configuration must be performed using the Fluent API.</span></span>

### <a name="fully-defined-relationships"></a><span data-ttu-id="85d88-135">Relazioni completamente definite</span><span class="sxs-lookup"><span data-stu-id="85d88-135">Fully defined relationships</span></span>

<span data-ttu-id="85d88-136">Il modello più comune per le relazioni prevede che le proprietà di navigazione siano definite in entrambe le estremità della relazione e in una proprietà di chiave esterna definita nella classe di entità dipendente.</span><span class="sxs-lookup"><span data-stu-id="85d88-136">The most common pattern for relationships is to have navigation properties defined on both ends of the relationship and a foreign key property defined in the dependent entity class.</span></span>

* <span data-ttu-id="85d88-137">Se tra due tipi viene trovata una coppia di proprietà di navigazione, queste verranno configurate come proprietà di navigazione inversa della stessa relazione.</span><span class="sxs-lookup"><span data-stu-id="85d88-137">If a pair of navigation properties is found between two types, then they will be configured as inverse navigation properties of the same relationship.</span></span>

* <span data-ttu-id="85d88-138">Se l'entità dipendente contiene una proprietà con un nome corrispondente a uno di questi modelli, sarà configurata come chiave esterna:</span><span class="sxs-lookup"><span data-stu-id="85d88-138">If the dependent entity contains a property with a name matching one of these patterns then it will be configured as the foreign key:</span></span>
  * `<navigation property name><principal key property name>`
  * `<navigation property name>Id`
  * `<principal entity name><principal key property name>`
  * `<principal entity name>Id`

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs?name=Full&highlight=6,15-16)]

<span data-ttu-id="85d88-139">In questo esempio le proprietà evidenziate verranno usate per configurare la relazione.</span><span class="sxs-lookup"><span data-stu-id="85d88-139">In this example the highlighted properties will be used to configure the relationship.</span></span>

> [!NOTE]
> <span data-ttu-id="85d88-140">Se la proprietà è la chiave primaria o è di un tipo non compatibile con la chiave principale, non verrà configurata come chiave esterna.</span><span class="sxs-lookup"><span data-stu-id="85d88-140">If the property is the primary key or is of a type not compatible with the principal key then it won't be configured as the foreign key.</span></span>

> [!NOTE]
> <span data-ttu-id="85d88-141">Prima di EF Core 3,0, anche la proprietà denominata esattamente come la proprietà della chiave principale [è stata trovata come chiave esterna](https://github.com/aspnet/EntityFrameworkCore/issues/13274)</span><span class="sxs-lookup"><span data-stu-id="85d88-141">Before EF Core 3.0 the property named exactly the same as the principal key property [was also matched as the foreign key](https://github.com/aspnet/EntityFrameworkCore/issues/13274)</span></span>

### <a name="no-foreign-key-property"></a><span data-ttu-id="85d88-142">Nessuna proprietà di chiave esterna</span><span class="sxs-lookup"><span data-stu-id="85d88-142">No foreign key property</span></span>

<span data-ttu-id="85d88-143">Sebbene sia consigliabile disporre di una proprietà di chiave esterna definita nella classe di entità dipendente, non è obbligatorio.</span><span class="sxs-lookup"><span data-stu-id="85d88-143">While it is recommended to have a foreign key property defined in the dependent entity class, it is not required.</span></span> <span data-ttu-id="85d88-144">Se non viene trovata alcuna proprietà di chiave esterna, verrà introdotta una [proprietà di chiave esterna Shadow](shadow-properties.md) con il nome `<navigation property name><principal key property name>` o `<principal entity name><principal key property name>` se non è presente alcuna navigazione nel tipo dipendente.</span><span class="sxs-lookup"><span data-stu-id="85d88-144">If no foreign key property is found, a [shadow foreign key property](shadow-properties.md) will be introduced with the name `<navigation property name><principal key property name>` or `<principal entity name><principal key property name>` if no navigation is present on the dependent type.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/NoForeignKey.cs?name=NoForeignKey&highlight=6,15)]

<span data-ttu-id="85d88-145">In questo esempio la chiave esterna Shadow è `BlogId`, perché il nome di navigazione anteposto è ridondante.</span><span class="sxs-lookup"><span data-stu-id="85d88-145">In this example the shadow foreign key is `BlogId` because prepending the navigation name would be redundant.</span></span>

> [!NOTE]
> <span data-ttu-id="85d88-146">Se una proprietà con lo stesso nome esiste già, al nome della proprietà shadow verrà associato un suffisso Number.</span><span class="sxs-lookup"><span data-stu-id="85d88-146">If a property with the same name already exists then the shadow property name will be suffixed with a number.</span></span>

### <a name="single-navigation-property"></a><span data-ttu-id="85d88-147">Proprietà di navigazione singola</span><span class="sxs-lookup"><span data-stu-id="85d88-147">Single navigation property</span></span>

<span data-ttu-id="85d88-148">Per includere una relazione definita per convenzione, è sufficiente includere una sola proprietà di navigazione (nessuna navigazione inversa e nessuna proprietà di chiave esterna).</span><span class="sxs-lookup"><span data-stu-id="85d88-148">Including just one navigation property (no inverse navigation, and no foreign key property) is enough to have a relationship defined by convention.</span></span> <span data-ttu-id="85d88-149">È inoltre possibile disporre di una sola proprietà di navigazione e di una proprietà di chiave esterna.</span><span class="sxs-lookup"><span data-stu-id="85d88-149">You can also have a single navigation property and a foreign key property.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneNavigation.cs?name=OneNavigation&highlight=6)]

### <a name="limitations"></a><span data-ttu-id="85d88-150">Limitazioni</span><span class="sxs-lookup"><span data-stu-id="85d88-150">Limitations</span></span>

<span data-ttu-id="85d88-151">Quando sono presenti più proprietà di navigazione definite tra due tipi, ovvero più di una sola coppia di spostamenti che puntano tra loro, le relazioni rappresentate dalle proprietà di navigazione sono ambigue.</span><span class="sxs-lookup"><span data-stu-id="85d88-151">When there are multiple navigation properties defined between two types (that is, more than just one pair of navigations that point to each other) the relationships represented by the navigation properties are ambiguous.</span></span> <span data-ttu-id="85d88-152">Sarà necessario configurarli manualmente per risolvere l'ambiguità.</span><span class="sxs-lookup"><span data-stu-id="85d88-152">You will need to manually configure them to resolve the ambiguity.</span></span>

### <a name="cascade-delete"></a><span data-ttu-id="85d88-153">Eliminazione a catena</span><span class="sxs-lookup"><span data-stu-id="85d88-153">Cascade delete</span></span>

<span data-ttu-id="85d88-154">Per convenzione, CASCADE DELETE verrà impostato su *Cascade* per le relazioni obbligatorie e *ClientSetNull* per le relazioni facoltative.</span><span class="sxs-lookup"><span data-stu-id="85d88-154">By convention, cascade delete will be set to *Cascade* for required relationships and *ClientSetNull* for optional relationships.</span></span> <span data-ttu-id="85d88-155">*Cascade* significa che vengono eliminate anche le entità dipendenti.</span><span class="sxs-lookup"><span data-stu-id="85d88-155">*Cascade* means dependent entities are also deleted.</span></span> <span data-ttu-id="85d88-156">*ClientSetNull* indica che le entità dipendenti che non vengono caricate in memoria rimarranno invariate e dovranno essere eliminate manualmente o aggiornate in modo da puntare a un'entità principale valida.</span><span class="sxs-lookup"><span data-stu-id="85d88-156">*ClientSetNull* means that dependent entities that are not loaded into memory will remain unchanged and must be manually deleted, or updated to point to a valid principal entity.</span></span> <span data-ttu-id="85d88-157">Per le entità caricate in memoria, EF Core tenterà di impostare le proprietà di chiave esterna su null.</span><span class="sxs-lookup"><span data-stu-id="85d88-157">For entities that are loaded into memory, EF Core will attempt to set the foreign key properties to null.</span></span>

<span data-ttu-id="85d88-158">Per la differenza tra le relazioni obbligatorie e facoltative, vedere la sezione [relazioni obbligatorie e facoltative](#required-and-optional-relationships) .</span><span class="sxs-lookup"><span data-stu-id="85d88-158">See the [Required and Optional Relationships](#required-and-optional-relationships) section for the difference between required and optional relationships.</span></span>

<span data-ttu-id="85d88-159">Per ulteriori informazioni sui diversi comportamenti di eliminazione e sulle impostazioni predefinite utilizzate dalla convenzione, vedere [CASCADE DELETE](../saving/cascade-delete.md) .</span><span class="sxs-lookup"><span data-stu-id="85d88-159">See [Cascade Delete](../saving/cascade-delete.md) for more details about the different delete behaviors and the defaults used by convention.</span></span>

## <a name="manual-configuration"></a><span data-ttu-id="85d88-160">Configurazione manuale</span><span class="sxs-lookup"><span data-stu-id="85d88-160">Manual configuration</span></span>

### <a name="fluent-api"></a>[<span data-ttu-id="85d88-161">API Fluent</span><span class="sxs-lookup"><span data-stu-id="85d88-161">Fluent API</span></span>](#tab/fluent-api)

<span data-ttu-id="85d88-162">Per configurare una relazione nell'API Fluent, è necessario innanzitutto identificare le proprietà di navigazione che compongono la relazione.</span><span class="sxs-lookup"><span data-stu-id="85d88-162">To configure a relationship in the Fluent API, you start by identifying the navigation properties that make up the relationship.</span></span> <span data-ttu-id="85d88-163">`HasOne` o `HasMany` identifica la proprietà di navigazione nel tipo di entità in cui si sta iniziando la configurazione.</span><span class="sxs-lookup"><span data-stu-id="85d88-163">`HasOne` or `HasMany` identifies the navigation property on the entity type you are beginning the configuration on.</span></span> <span data-ttu-id="85d88-164">È quindi possibile concatenare una chiamata a `WithOne` o `WithMany` per identificare la navigazione inversa.</span><span class="sxs-lookup"><span data-stu-id="85d88-164">You then chain a call to `WithOne` or `WithMany` to identify the inverse navigation.</span></span> <span data-ttu-id="85d88-165">`HasOne`/`WithOne` vengono utilizzate per le proprietà di navigazione di riferimento e `HasMany`/`WithMany` vengono utilizzate per le proprietà di navigazione della raccolta.</span><span class="sxs-lookup"><span data-stu-id="85d88-165">`HasOne`/`WithOne` are used for reference navigation properties and `HasMany`/`WithMany` are used for collection navigation properties.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoForeignKey.cs?name=NoForeignKey&highlight=8-10)]

### <a name="data-annotations"></a>[<span data-ttu-id="85d88-166">Annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="85d88-166">Data annotations</span></span>](#tab/data-annotations)

<span data-ttu-id="85d88-167">È possibile utilizzare le annotazioni dei dati per configurare la modalità di associazione delle proprietà di navigazione nelle entità dipendenti e entità.</span><span class="sxs-lookup"><span data-stu-id="85d88-167">You can use the Data Annotations to configure how navigation properties on the dependent and principal entities pair up.</span></span> <span data-ttu-id="85d88-168">Questa operazione viene in genere eseguita quando è presente più di una coppia di proprietà di navigazione tra due tipi di entità.</span><span class="sxs-lookup"><span data-stu-id="85d88-168">This is typically done when there is more than one pair of navigation properties between two entity types.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/InverseProperty.cs?name=InverseProperty&highlight=20,23)]

> [!NOTE]
> <span data-ttu-id="85d88-169">Per influenzare la richiesta della relazione, è possibile utilizzare solo [Required] sulle proprietà dell'entità dipendente.</span><span class="sxs-lookup"><span data-stu-id="85d88-169">You can only use [Required] on properties on the dependent entity to impact the requiredness of the relationship.</span></span> <span data-ttu-id="85d88-170">[Required] la navigazione dall'entità principale viene in genere ignorata, ma può causare che l'entità diventi quella dipendente.</span><span class="sxs-lookup"><span data-stu-id="85d88-170">[Required] on the navigation from the principal entity is usually ignored, but it may cause the entity to become the dependent one.</span></span>

> [!NOTE]
> <span data-ttu-id="85d88-171">Le annotazioni dei dati `[ForeignKey]` e `[InverseProperty]` sono disponibili nello spazio dei nomi `System.ComponentModel.DataAnnotations.Schema`.</span><span class="sxs-lookup"><span data-stu-id="85d88-171">The data annotations `[ForeignKey]` and `[InverseProperty]` are available in the `System.ComponentModel.DataAnnotations.Schema` namespace.</span></span> <span data-ttu-id="85d88-172">`[Required]` è disponibile nello spazio dei nomi `System.ComponentModel.DataAnnotations`.</span><span class="sxs-lookup"><span data-stu-id="85d88-172">`[Required]` is available in the `System.ComponentModel.DataAnnotations` namespace.</span></span>

---

### <a name="single-navigation-property"></a><span data-ttu-id="85d88-173">Proprietà di navigazione singola</span><span class="sxs-lookup"><span data-stu-id="85d88-173">Single navigation property</span></span>

<span data-ttu-id="85d88-174">Se è presente una sola proprietà di navigazione, sono presenti overload senza parametri di `WithOne` e `WithMany`.</span><span class="sxs-lookup"><span data-stu-id="85d88-174">If you only have one navigation property then there are parameterless overloads of `WithOne` and `WithMany`.</span></span> <span data-ttu-id="85d88-175">Ciò indica che è concettualmente presente un riferimento o una raccolta nell'altra entità finale della relazione, ma nella classe di entità non è inclusa alcuna proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="85d88-175">This indicates that there is conceptually a reference or collection on the other end of the relationship, but there is no navigation property included in the entity class.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneNavigation.cs?name=OneNavigation&highlight=8-10)]

### <a name="foreign-key"></a><span data-ttu-id="85d88-176">Chiave esterna</span><span class="sxs-lookup"><span data-stu-id="85d88-176">Foreign key</span></span>

#### <a name="fluent-api-simple-key"></a>[<span data-ttu-id="85d88-177">API Fluent (chiave semplice)</span><span class="sxs-lookup"><span data-stu-id="85d88-177">Fluent API (simple key)</span></span>](#tab/fluent-api-simple-key)

<span data-ttu-id="85d88-178">È possibile usare l'API Fluent per configurare la proprietà da usare come proprietà di chiave esterna per una determinata relazione:</span><span class="sxs-lookup"><span data-stu-id="85d88-178">You can use the Fluent API to configure which property should be used as the foreign key property for a given relationship:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ForeignKey.cs?name=ForeignKey&highlight=11)]

#### <a name="fluent-api-composite-key"></a>[<span data-ttu-id="85d88-179">API Fluent (chiave composta)</span><span class="sxs-lookup"><span data-stu-id="85d88-179">Fluent API (composite key)</span></span>](#tab/fluent-api-composite-key)

<span data-ttu-id="85d88-180">È possibile utilizzare l'API Fluent per configurare le proprietà che devono essere utilizzate come proprietà di chiave esterna composta per una determinata relazione:</span><span class="sxs-lookup"><span data-stu-id="85d88-180">You can use the Fluent API to configure which properties should be used as the composite foreign key properties for a given relationship:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositeForeignKey.cs?name=CompositeForeignKey&highlight=13)]

#### <a name="data-annotations-simple-key"></a>[<span data-ttu-id="85d88-181">Annotazioni dati (chiave semplice)</span><span class="sxs-lookup"><span data-stu-id="85d88-181">Data annotations (simple key)</span></span>](#tab/data-annotations-simple-key)

<span data-ttu-id="85d88-182">È possibile utilizzare le annotazioni dei dati per configurare quale proprietà deve essere utilizzata come proprietà di chiave esterna per una determinata relazione.</span><span class="sxs-lookup"><span data-stu-id="85d88-182">You can use the Data Annotations to configure which property should be used as the foreign key property for a given relationship.</span></span> <span data-ttu-id="85d88-183">Questa operazione viene in genere eseguita quando la proprietà di chiave esterna non viene individuata per convenzione:</span><span class="sxs-lookup"><span data-stu-id="85d88-183">This is typically done when the foreign key property is not discovered by convention:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/ForeignKey.cs?name=ForeignKey&highlight=17)]

> [!TIP]  
> <span data-ttu-id="85d88-184">L'annotazione `[ForeignKey]` può essere posizionata su una proprietà di navigazione nella relazione.</span><span class="sxs-lookup"><span data-stu-id="85d88-184">The `[ForeignKey]` annotation can be placed on either navigation property in the relationship.</span></span> <span data-ttu-id="85d88-185">Non è necessario che venga eseguita la proprietà di navigazione nella classe di entità dipendente.</span><span class="sxs-lookup"><span data-stu-id="85d88-185">It does not need to go on the navigation property in the dependent entity class.</span></span>

> [!NOTE]
> <span data-ttu-id="85d88-186">Non è necessario che la proprietà specificata con `[ForeignKey]` in una proprietà di navigazione esista il tipo dipendente.</span><span class="sxs-lookup"><span data-stu-id="85d88-186">The property specified using `[ForeignKey]` on a navigation property doesn't need to exist the the dependent type.</span></span> <span data-ttu-id="85d88-187">In questo caso, il nome specificato verrà utilizzato per creare una chiave esterna Shadow.</span><span class="sxs-lookup"><span data-stu-id="85d88-187">In this case the specified name will be used to create a shadow foreign key.</span></span>

---

#### <a name="shadow-foreign-key"></a><span data-ttu-id="85d88-188">Chiave esterna Shadow</span><span class="sxs-lookup"><span data-stu-id="85d88-188">Shadow foreign key</span></span>

<span data-ttu-id="85d88-189">È possibile utilizzare l'overload di stringa di `HasForeignKey(...)` per configurare una proprietà shadow come chiave esterna. per ulteriori informazioni, vedere [proprietà shadow](shadow-properties.md) .</span><span class="sxs-lookup"><span data-stu-id="85d88-189">You can use the string overload of `HasForeignKey(...)` to configure a shadow property as a foreign key (see [Shadow Properties](shadow-properties.md) for more information).</span></span> <span data-ttu-id="85d88-190">È consigliabile aggiungere esplicitamente la proprietà shadow al modello prima di utilizzarla come chiave esterna, come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="85d88-190">We recommend explicitly adding the shadow property to the model before using it as a foreign key (as shown below).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ShadowForeignKey.cs?name=ShadowForeignKey&highlight=10,16)]

#### <a name="foreign-key-constraint-name"></a><span data-ttu-id="85d88-191">Nome vincolo di chiave esterna</span><span class="sxs-lookup"><span data-stu-id="85d88-191">Foreign key constraint name</span></span>

<span data-ttu-id="85d88-192">Per convenzione, quando la destinazione è un database relazionale, i vincoli di chiave esterna vengono denominati FK_<dependent type name> _<principal type name>_ <foreign key property name>.</span><span class="sxs-lookup"><span data-stu-id="85d88-192">By convention, when targeting a relational database, foreign key constraints are named FK_<dependent type name>_<principal type name>_<foreign key property name>.</span></span> <span data-ttu-id="85d88-193">Per le chiavi esterne composite <foreign key property name> diventa un elenco delimitato da caratteri di sottolineatura dei nomi delle proprietà di chiave esterna.</span><span class="sxs-lookup"><span data-stu-id="85d88-193">For composite foreign keys <foreign key property name> becomes an underscore separated list of foreign key property names.</span></span>

<span data-ttu-id="85d88-194">È anche possibile configurare il nome del vincolo come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="85d88-194">You can also configure the constraint name as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ConstraintName.cs?name=ConstraintName&highlight=6-7)]

### <a name="without-navigation-property"></a><span data-ttu-id="85d88-195">Senza proprietà di navigazione</span><span class="sxs-lookup"><span data-stu-id="85d88-195">Without navigation property</span></span>

<span data-ttu-id="85d88-196">Non è necessario necessariamente fornire una proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="85d88-196">You don't necessarily need to provide a navigation property.</span></span> <span data-ttu-id="85d88-197">È possibile specificare semplicemente una chiave esterna su un lato della relazione.</span><span class="sxs-lookup"><span data-stu-id="85d88-197">You can simply provide a foreign key on one side of the relationship.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoNavigation.cs?name=NoNavigation&highlight=8-11)]

### <a name="principal-key"></a><span data-ttu-id="85d88-198">Chiave principale</span><span class="sxs-lookup"><span data-stu-id="85d88-198">Principal key</span></span>

<span data-ttu-id="85d88-199">Se si desidera che la chiave esterna faccia riferimento a una proprietà diversa dalla chiave primaria, è possibile utilizzare l'API Fluent per configurare la proprietà chiave principale per la relazione.</span><span class="sxs-lookup"><span data-stu-id="85d88-199">If you want the foreign key to reference a property other than the primary key, you can use the Fluent API to configure the principal key property for the relationship.</span></span> <span data-ttu-id="85d88-200">La proprietà configurata come chiave principale verrà automaticamente impostata come [chiave alternativa](alternate-keys.md).</span><span class="sxs-lookup"><span data-stu-id="85d88-200">The property that you configure as the principal key will automatically be setup as an [alternate key](alternate-keys.md).</span></span>

#### <a name="simple-key"></a>[<span data-ttu-id="85d88-201">Chiave semplice</span><span class="sxs-lookup"><span data-stu-id="85d88-201">Simple key</span></span>](#tab/simple-key)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/PrincipalKey.cs?name=PrincipalKey&highlight=11)]

#### <a name="composite-key"></a>[<span data-ttu-id="85d88-202">Chiave composta</span><span class="sxs-lookup"><span data-stu-id="85d88-202">Composite key</span></span>](#tab/composite-key)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositePrincipalKey.cs?name=CompositePrincipalKey&highlight=11)]

> [!WARNING]  
> <span data-ttu-id="85d88-203">L'ordine in cui si specificano le proprietà della chiave principale deve corrispondere all'ordine in cui sono specificate per la chiave esterna.</span><span class="sxs-lookup"><span data-stu-id="85d88-203">The order in which you specify principal key properties must match the order in which they are specified for the foreign key.</span></span>

---

### <a name="required-and-optional-relationships"></a><span data-ttu-id="85d88-204">Relazioni obbligatorie e facoltative</span><span class="sxs-lookup"><span data-stu-id="85d88-204">Required and optional relationships</span></span>

<span data-ttu-id="85d88-205">È possibile usare l'API Fluent per configurare se la relazione è obbligatoria o facoltativa.</span><span class="sxs-lookup"><span data-stu-id="85d88-205">You can use the Fluent API to configure whether the relationship is required or optional.</span></span> <span data-ttu-id="85d88-206">In definitiva, controlla se la proprietà della chiave esterna è obbligatoria o facoltativa.</span><span class="sxs-lookup"><span data-stu-id="85d88-206">Ultimately this controls whether the foreign key property is required or optional.</span></span> <span data-ttu-id="85d88-207">Questa operazione è particolarmente utile quando si usa una chiave esterna dello stato di ombreggiatura.</span><span class="sxs-lookup"><span data-stu-id="85d88-207">This is most useful when you are using a shadow state foreign key.</span></span> <span data-ttu-id="85d88-208">Se nella classe di entità è presente una proprietà di chiave esterna, la richiesta della relazione viene determinata a seconda che la proprietà della chiave esterna sia obbligatoria o facoltativa (per ulteriori informazioni, vedere [proprietà obbligatorie e facoltative](required-optional.md) ).</span><span class="sxs-lookup"><span data-stu-id="85d88-208">If you have a foreign key property in your entity class then the requiredness of the relationship is determined based on whether the foreign key property is required or optional (see [Required and Optional properties](required-optional.md) for more information).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/Required.cs?name=Required&highlight=6)]

> [!NOTE]
> <span data-ttu-id="85d88-209">La chiamata di `IsRequired(false)` rende anche la proprietà di chiave esterna facoltativa, a meno che non sia configurata in modo</span><span class="sxs-lookup"><span data-stu-id="85d88-209">Calling `IsRequired(false)` also makes the foreign key property optional unless it's configured otherwise.</span></span>

### <a name="cascade-delete"></a><span data-ttu-id="85d88-210">Eliminazione a catena</span><span class="sxs-lookup"><span data-stu-id="85d88-210">Cascade delete</span></span>

<span data-ttu-id="85d88-211">È possibile usare l'API Fluent per configurare in modo esplicito il comportamento di eliminazione a catena per una determinata relazione.</span><span class="sxs-lookup"><span data-stu-id="85d88-211">You can use the Fluent API to configure the cascade delete behavior for a given relationship explicitly.</span></span>

<span data-ttu-id="85d88-212">Per una descrizione dettagliata di ogni opzione, vedere [CASCADE DELETE](../saving/cascade-delete.md) .</span><span class="sxs-lookup"><span data-stu-id="85d88-212">See [Cascade Delete](../saving/cascade-delete.md) for a detailed discussion of each option.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CascadeDelete.cs?name=CascadeDelete&highlight=6)]

## <a name="other-relationship-patterns"></a><span data-ttu-id="85d88-213">Altri modelli di relazione</span><span class="sxs-lookup"><span data-stu-id="85d88-213">Other relationship patterns</span></span>

### <a name="one-to-one"></a><span data-ttu-id="85d88-214">Uno-a-uno</span><span class="sxs-lookup"><span data-stu-id="85d88-214">One-to-one</span></span>

<span data-ttu-id="85d88-215">Le relazioni uno-a-uno hanno una proprietà di navigazione di riferimento su entrambi i lati.</span><span class="sxs-lookup"><span data-stu-id="85d88-215">One to one relationships have a reference navigation property on both sides.</span></span> <span data-ttu-id="85d88-216">Seguono le stesse convenzioni delle relazioni uno-a-molti, ma un indice univoco viene introdotto sulla proprietà della chiave esterna per garantire che solo un dipendente sia correlato a ogni entità.</span><span class="sxs-lookup"><span data-stu-id="85d88-216">They follow the same conventions as one-to-many relationships, but a unique index is introduced on the foreign key property to ensure only one dependent is related to each principal.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneToOne.cs?name=OneToOne&highlight=6,15-16)]

> [!NOTE]  
> <span data-ttu-id="85d88-217">EF sceglierà una delle entità come dipendente in base alla capacità di rilevare una proprietà di chiave esterna.</span><span class="sxs-lookup"><span data-stu-id="85d88-217">EF will choose one of the entities to be the dependent based on its ability to detect a foreign key property.</span></span> <span data-ttu-id="85d88-218">Se l'entità sbagliata viene scelta come dipendente, è possibile usare l'API Fluent per correggere questa operazione.</span><span class="sxs-lookup"><span data-stu-id="85d88-218">If the wrong entity is chosen as the dependent, you can use the Fluent API to correct this.</span></span>

<span data-ttu-id="85d88-219">Quando si configura la relazione con l'API Fluent, si usano i metodi `HasOne` e `WithOne`.</span><span class="sxs-lookup"><span data-stu-id="85d88-219">When configuring the relationship with the Fluent API, you use the `HasOne` and `WithOne` methods.</span></span>

<span data-ttu-id="85d88-220">Quando si configura la chiave esterna è necessario specificare il tipo di entità dipendente. si noti il parametro generico fornito a `HasForeignKey` nell'elenco seguente.</span><span class="sxs-lookup"><span data-stu-id="85d88-220">When configuring the foreign key you need to specify the dependent entity type - notice the generic parameter provided to `HasForeignKey` in the listing below.</span></span> <span data-ttu-id="85d88-221">In una relazione uno-a-molti è evidente che l'entità con l'esplorazione dei riferimenti è il dipendente e quello con la raccolta è l'entità.</span><span class="sxs-lookup"><span data-stu-id="85d88-221">In a one-to-many relationship it is clear that the entity with the reference navigation is the dependent and the one with the collection is the principal.</span></span> <span data-ttu-id="85d88-222">Ma non si tratta di una relazione uno-a-uno, di conseguenza è necessario definirla in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="85d88-222">But this is not so in a one-to-one relationship - hence the need to explicitly define it.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneToOne.cs?name=OneToOne&highlight=11)]

### <a name="many-to-many"></a><span data-ttu-id="85d88-223">Molti-a-molti</span><span class="sxs-lookup"><span data-stu-id="85d88-223">Many-to-many</span></span>

<span data-ttu-id="85d88-224">Le relazioni many-to-many senza una classe di entità per rappresentare la tabella di join non sono ancora supportate.</span><span class="sxs-lookup"><span data-stu-id="85d88-224">Many-to-many relationships without an entity class to represent the join table are not yet supported.</span></span> <span data-ttu-id="85d88-225">È tuttavia possibile rappresentare una relazione molti-a-molti includendo una classe di entità per la tabella di join ed eseguendo il mapping di due relazioni uno-a-molti separate.</span><span class="sxs-lookup"><span data-stu-id="85d88-225">However, you can represent a many-to-many relationship by including an entity class for the join table and mapping two separate one-to-many relationships.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToMany.cs?name=ManyToMany&highlight=11-14,16-19,39-46)]
