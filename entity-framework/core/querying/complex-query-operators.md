---
title: Operatori di query complessi-EF Core
author: smitpatel
ms.date: 10/03/2019
ms.assetid: 2e187a2a-4072-4198-9040-aaad68e424fd
uid: core/querying/complex-query-operators
ms.openlocfilehash: 350a7fa6a3ee1de16bad4b63e10842f9356a1b60
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72186260"
---
# <a name="complex-query-operators"></a><span data-ttu-id="1e5eb-102">Operatori di query complessi</span><span class="sxs-lookup"><span data-stu-id="1e5eb-102">Complex Query Operators</span></span>

<span data-ttu-id="1e5eb-103">LINQ (Language Integrated Query) contiene molti operatori complessi, che combinano più origini dati o eseguono elaborazioni complesse.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-103">Language Integrated Query (LINQ) contains many complex operators, which combine multiple data sources or does complex processing.</span></span> <span data-ttu-id="1e5eb-104">Non tutti gli operatori LINQ hanno traduzioni appropriate sul lato server.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-104">Not all LINQ operators have suitable translations on the server side.</span></span> <span data-ttu-id="1e5eb-105">In alcuni casi, una query in un modulo viene convertita nel server, ma se scritta in un formato diverso non viene convertita anche se il risultato è lo stesso.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-105">Sometimes, a query in one form translates to the server but if written in a different form doesn't translate even if the result is the same.</span></span> <span data-ttu-id="1e5eb-106">In questa pagina vengono descritti alcuni degli operatori complessi e le relative varianti supportate.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-106">This page describes some of the complex operators and their supported variations.</span></span> <span data-ttu-id="1e5eb-107">Nelle versioni future, è possibile riconoscere più modelli e aggiungere le relative traduzioni.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-107">In future releases, we may recognize more patterns and add their corresponding translations.</span></span> <span data-ttu-id="1e5eb-108">È anche importante tenere presente che il supporto della traduzione varia tra i provider.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-108">It's also important to keep in mind that translation support varies between providers.</span></span> <span data-ttu-id="1e5eb-109">Una query specifica, che viene convertita in SqlServer, potrebbe non funzionare per i database SQLite.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-109">A particular query, which is translated in SqlServer, may not work for SQLite databases.</span></span>

> [!TIP]
> <span data-ttu-id="1e5eb-110">È possibile visualizzare l'[esempio](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) di questo articolo in GitHub.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-110">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="join"></a><span data-ttu-id="1e5eb-111">Join</span><span class="sxs-lookup"><span data-stu-id="1e5eb-111">Join</span></span>

<span data-ttu-id="1e5eb-112">L'operatore LINQ join consente di connettere due origini dati in base al selettore di chiave per ogni origine, generando una tupla di valori quando la chiave corrisponde a.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-112">The LINQ Join operator allows you to connect two data sources based on the key selector for each source, generating a tuple of values when the key matches.</span></span> <span data-ttu-id="1e5eb-113">Si traduce naturalmente in `INNER JOIN` nei database relazionali.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-113">It naturally translates to `INNER JOIN` on relational databases.</span></span> <span data-ttu-id="1e5eb-114">Mentre il join LINQ dispone di selettori di chiave esterni e interni, il database richiede una singola condizione di join.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-114">While the LINQ Join has outer and inner key selectors, the database requires a single join condition.</span></span> <span data-ttu-id="1e5eb-115">Quindi EF Core genera una condizione di join confrontando il selettore di chiave esterna con il selettore di chiave interna per verificarne l'uguaglianza.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-115">So EF Core generates a join condition by comparing the outer key selector to the inner key selector for equality.</span></span> <span data-ttu-id="1e5eb-116">Inoltre, se i selettori di chiave sono tipi anonimi, EF Core genera una condizione di join per confrontare il componente di uguaglianza Wise.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-116">Further, if the key selectors are anonymous types, EF Core generates a join condition to compare equality component wise.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#Join)]

```SQL
SELECT [p].[PersonId], [p].[Name], [p].[PhotoId], [p0].[PersonPhotoId], [p0].[Caption], [p0].[Photo]
FROM [PersonPhoto] AS [p0]
INNER JOIN [Person] AS [p] ON [p0].[PersonPhotoId] = [p].[PhotoId]
```

## <a name="groupjoin"></a><span data-ttu-id="1e5eb-117">GroupJoin</span><span class="sxs-lookup"><span data-stu-id="1e5eb-117">GroupJoin</span></span>

<span data-ttu-id="1e5eb-118">L'operatore LINQ GroupJoin consente di connettere due origini dati simili a join, ma crea un gruppo di valori interni per la corrispondenza di elementi esterni.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-118">The LINQ GroupJoin operator allows you to connect two data sources similar to Join, but it creates a group of inner values for matching outer elements.</span></span> <span data-ttu-id="1e5eb-119">L'esecuzione di una query come nell'esempio seguente genera il risultato `Blog` @ no__t-1 @ no__t-2.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-119">Executing a query like the following example generates a result of `Blog` & `IEnumerable<Post>`.</span></span> <span data-ttu-id="1e5eb-120">Poiché i database (in particolare i database relazionali) non hanno un modo per rappresentare una raccolta di oggetti lato client, GroupJoin non esegue la conversione nel server in molti casi.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-120">Since databases (especially relational databases) don't have a way to represent a collection of client-side objects, GroupJoin doesn't translate to the server in many cases.</span></span> <span data-ttu-id="1e5eb-121">È necessario ottenere tutti i dati dal server per eseguire GroupJoin senza un selettore speciale (prima query riportata di seguito).</span><span class="sxs-lookup"><span data-stu-id="1e5eb-121">It requires you to get all of the data from the server to do GroupJoin without a special selector (first query below).</span></span> <span data-ttu-id="1e5eb-122">Tuttavia, se il selettore limita i dati selezionati, il recupero di tutti i dati dal server può causare problemi di prestazioni (seconda query riportata di seguito).</span><span class="sxs-lookup"><span data-stu-id="1e5eb-122">But if the selector is limiting data being selected then fetching all of the data from the server may cause performance issues (second query below).</span></span> <span data-ttu-id="1e5eb-123">Per questo motivo EF Core non converte GroupJoin.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-123">That's why EF Core doesn't translate GroupJoin.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupJoin)]

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupJoinComposed)]

## <a name="selectmany"></a><span data-ttu-id="1e5eb-124">SelectMany</span><span class="sxs-lookup"><span data-stu-id="1e5eb-124">SelectMany</span></span>

<span data-ttu-id="1e5eb-125">L'operatore LINQ SelectMany consente di enumerare un selettore di raccolta per ogni elemento esterno e generare Tuple di valori da ogni origine dati.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-125">The LINQ SelectMany operator allows you to enumerate over a collection selector for each outer element and generate tuples of values from each data source.</span></span> <span data-ttu-id="1e5eb-126">In un modo, si tratta di un join ma senza alcuna condizione in modo che ogni elemento esterno sia connesso a un elemento dall'origine della raccolta.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-126">In a way, it's a join but without any condition so every outer element is connected with an element from the collection source.</span></span> <span data-ttu-id="1e5eb-127">A seconda del modo in cui il selettore di raccolta è correlato all'origine dati esterna, SelectMany può tradurre in diverse query sul lato server.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-127">Depending on how the collection selector is related to the outer data source, SelectMany can translate into various different queries on the server side.</span></span>

### <a name="collection-selector-doesnt-reference-outer"></a><span data-ttu-id="1e5eb-128">Il selettore di raccolta non fa riferimento all'esterno</span><span class="sxs-lookup"><span data-stu-id="1e5eb-128">Collection selector doesn't reference outer</span></span>

<span data-ttu-id="1e5eb-129">Quando il selettore di raccolta non fa riferimento ad alcun elemento dall'origine esterna, il risultato è un prodotto cartesiano di entrambe le origini dati.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-129">When the collection selector isn't referencing anything from the outer source, the result is a cartesian product of both data sources.</span></span> <span data-ttu-id="1e5eb-130">Viene convertito in `CROSS JOIN` nei database relazionali.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-130">It translates to `CROSS JOIN` in relational databases.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#SelectManyConvertedToCrossJoin)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
CROSS JOIN [Posts] AS [p]
```

### <a name="collection-selector-references-outer-in-a-where-clause"></a><span data-ttu-id="1e5eb-131">Riferimenti al selettore di raccolta esterni in una clausola WHERE</span><span class="sxs-lookup"><span data-stu-id="1e5eb-131">Collection selector references outer in a where clause</span></span>

<span data-ttu-id="1e5eb-132">Quando il selettore di raccolta include una clausola WHERE, che fa riferimento all'elemento esterno, EF Core la converte in un join del database e usa il predicato come condizione di join.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-132">When the collection selector has a where clause, which references the outer element, then EF Core translates it to a database join and uses the predicate as the join condition.</span></span> <span data-ttu-id="1e5eb-133">Questa situazione si verifica in genere quando si utilizza la navigazione della raccolta sull'elemento esterno come selettore della raccolta.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-133">Normally this case arises when using collection navigation on the outer element as the collection selector.</span></span> <span data-ttu-id="1e5eb-134">Se la raccolta è vuota per un elemento esterno, non verrà generato alcun risultato per quell'elemento esterno.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-134">If the collection is empty for an outer element, then no results would be generated for that outer element.</span></span> <span data-ttu-id="1e5eb-135">Tuttavia, se `DefaultIfEmpty` viene applicato al selettore della raccolta, l'elemento esterno sarà connesso con un valore predefinito dell'elemento interno.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-135">But if `DefaultIfEmpty` is applied on the collection selector then the outer element will be connected with a default value of the inner element.</span></span> <span data-ttu-id="1e5eb-136">A causa di questa distinzione, questo tipo di query viene convertito in `INNER JOIN` in assenza di `DefaultIfEmpty` e `LEFT JOIN` quando viene applicato `DefaultIfEmpty`.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-136">Because of this distinction, this kind of queries translates to `INNER JOIN` in the absence of `DefaultIfEmpty` and `LEFT JOIN` when `DefaultIfEmpty` is applied.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#SelectManyConvertedToJoin)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
INNER JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]

SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]
```

### <a name="collection-selector-references-outer-in-a-non-where-case"></a><span data-ttu-id="1e5eb-137">Riferimenti a selettori di raccolta esterni in un caso non-where</span><span class="sxs-lookup"><span data-stu-id="1e5eb-137">Collection selector references outer in a non-where case</span></span>

<span data-ttu-id="1e5eb-138">Quando il selettore della raccolta fa riferimento all'elemento esterno, che non si trova in una clausola WHERE (come nel caso precedente), non viene convertito in un join del database.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-138">When the collection selector references the outer element, which isn't in a where clause (as the case above), it doesn't translate to a database join.</span></span> <span data-ttu-id="1e5eb-139">Per questo motivo è necessario valutare il selettore di raccolta per ogni elemento esterno.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-139">That's why we need to evaluate the collection selector for each outer element.</span></span> <span data-ttu-id="1e5eb-140">Si traduce in operazioni `APPLY` in molti database relazionali.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-140">It translates to `APPLY` operations in many relational databases.</span></span> <span data-ttu-id="1e5eb-141">Se la raccolta è vuota per un elemento esterno, non verrà generato alcun risultato per quell'elemento esterno.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-141">If the collection is empty for an outer element, then no results would be generated for that outer element.</span></span> <span data-ttu-id="1e5eb-142">Tuttavia, se `DefaultIfEmpty` viene applicato al selettore della raccolta, l'elemento esterno sarà connesso con un valore predefinito dell'elemento interno.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-142">But if `DefaultIfEmpty` is applied on the collection selector then the outer element will be connected with a default value of the inner element.</span></span> <span data-ttu-id="1e5eb-143">A causa di questa distinzione, questo tipo di query viene convertito in `CROSS APPLY` in assenza di `DefaultIfEmpty` e `OUTER APPLY` quando viene applicato `DefaultIfEmpty`.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-143">Because of this distinction, this kind of queries translates to `CROSS APPLY` in the absence of `DefaultIfEmpty` and `OUTER APPLY` when `DefaultIfEmpty` is applied.</span></span> <span data-ttu-id="1e5eb-144">Alcuni database come SQLite non supportano gli operatori `APPLY`, pertanto questo tipo di query non può essere convertito.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-144">Certain databases like SQLite don't support `APPLY` operators so this kind of query may not be translated.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#SelectManyConvertedToApply)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], ([b].[Url] + N'=>') + [p].[Title] AS [p]
FROM [Blogs] AS [b]
CROSS APPLY [Posts] AS [p]

SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], ([b].[Url] + N'=>') + [p].[Title] AS [p]
FROM [Blogs] AS [b]
OUTER APPLY [Posts] AS [p]
```

## <a name="groupby"></a><span data-ttu-id="1e5eb-145">GroupBy</span><span class="sxs-lookup"><span data-stu-id="1e5eb-145">GroupBy</span></span>

<span data-ttu-id="1e5eb-146">Gli operatori di GroupBy LINQ creano un risultato di tipo `IGrouping<TKey, TElement>` in cui `TKey` e `TElement` possono essere di qualsiasi tipo arbitrario.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-146">LINQ GroupBy operators create a result of type `IGrouping<TKey, TElement>` where `TKey` and `TElement` could be any arbitrary type.</span></span> <span data-ttu-id="1e5eb-147">Inoltre, `IGrouping` implementa `IEnumerable<TElement>`, il che significa che è possibile comporre su di esso utilizzando qualsiasi operatore LINQ dopo il raggruppamento.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-147">Furthermore, `IGrouping` implements `IEnumerable<TElement>`, which means you can compose over it using any LINQ operator after the grouping.</span></span> <span data-ttu-id="1e5eb-148">Poiché non è possibile che la struttura di database rappresenti un `IGrouping`, nella maggior parte dei casi gli operatori GroupBy non hanno alcuna traduzione.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-148">Since no database structure can represent an `IGrouping`, GroupBy operators have no translation in most cases.</span></span> <span data-ttu-id="1e5eb-149">Quando viene applicato un operatore di aggregazione a ogni gruppo, che restituisce un valore scalare, può essere convertito in SQL `GROUP BY` nei database relazionali.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-149">When an aggregate operator is applied to each group, which returns a scalar, it can be translated to SQL `GROUP BY` in relational databases.</span></span> <span data-ttu-id="1e5eb-150">Anche SQL `GROUP BY` è restrittivo.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-150">The SQL `GROUP BY` is restrictive too.</span></span> <span data-ttu-id="1e5eb-151">È necessario raggruppare solo i valori scalari.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-151">It requires you to group only by scalar values.</span></span> <span data-ttu-id="1e5eb-152">La proiezione può contenere solo colonne chiave di raggruppamento o qualsiasi aggregazione applicata a una colonna.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-152">The projection can only contain grouping key columns or any aggregate applied over a column.</span></span> <span data-ttu-id="1e5eb-153">EF Core identifica questo modello e lo converte nel server, come nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="1e5eb-153">EF Core identifies this pattern and translates it to the server, as in the following example:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupBy)]

```SQL
SELECT [p].[AuthorId] AS [Key], COUNT(*) AS [Count]
FROM [Posts] AS [p]
GROUP BY [p].[AuthorId]
```

<span data-ttu-id="1e5eb-154">EF Core inoltre converte le query in cui viene visualizzato un operatore di aggregazione nel raggruppamento in un operatore LINQ WHERE o OrderBy (o un altro ordinamento).</span><span class="sxs-lookup"><span data-stu-id="1e5eb-154">EF Core also translates queries where an aggregate operator on the grouping appears in a Where or OrderBy (or other ordering) LINQ operator.</span></span> <span data-ttu-id="1e5eb-155">Usa la clausola `HAVING` in SQL per la clausola WHERE.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-155">It uses `HAVING` clause in SQL for the where clause.</span></span> <span data-ttu-id="1e5eb-156">La parte della query prima di applicare l'operatore GroupBy può essere qualsiasi query complessa purché possa essere convertita nel server.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-156">The part of the query before applying the GroupBy operator can be any complex query as long as it can be translated to server.</span></span> <span data-ttu-id="1e5eb-157">Inoltre, dopo aver applicato gli operatori di aggregazione in una query di raggruppamento per rimuovere i raggruppamenti dall'origine risultante, è possibile comporre su di esso come qualsiasi altra query.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-157">Furthermore, once you apply aggregate operators on a grouping query to remove groupings from the resulting source, you can compose on top of it like any other query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupByFilter)]

```SQL
SELECT [p].[AuthorId] AS [Key], COUNT(*) AS [Count]
FROM [Posts] AS [p]
GROUP BY [p].[AuthorId]
HAVING COUNT(*) > 0
ORDER BY [p].[AuthorId]
```

<span data-ttu-id="1e5eb-158">Gli operatori di aggregazione EF Core supportati sono i seguenti:</span><span class="sxs-lookup"><span data-stu-id="1e5eb-158">The aggregate operators EF Core supports are as follows</span></span>

- <span data-ttu-id="1e5eb-159">Average</span><span class="sxs-lookup"><span data-stu-id="1e5eb-159">Average</span></span>
- <span data-ttu-id="1e5eb-160">Count</span><span class="sxs-lookup"><span data-stu-id="1e5eb-160">Count</span></span>
- <span data-ttu-id="1e5eb-161">LongCount</span><span class="sxs-lookup"><span data-stu-id="1e5eb-161">LongCount</span></span>
- <span data-ttu-id="1e5eb-162">Max</span><span class="sxs-lookup"><span data-stu-id="1e5eb-162">Max</span></span>
- <span data-ttu-id="1e5eb-163">Min</span><span class="sxs-lookup"><span data-stu-id="1e5eb-163">Min</span></span>
- <span data-ttu-id="1e5eb-164">Sum</span><span class="sxs-lookup"><span data-stu-id="1e5eb-164">Sum</span></span>

## <a name="left-join"></a><span data-ttu-id="1e5eb-165">Left join</span><span class="sxs-lookup"><span data-stu-id="1e5eb-165">Left Join</span></span>

<span data-ttu-id="1e5eb-166">Mentre left join non è un operatore LINQ, i database relazionali hanno il concetto di un left join usato spesso nelle query.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-166">While Left Join isn't a LINQ operator, relational databases have the concept of a Left Join which is frequently used in queries.</span></span> <span data-ttu-id="1e5eb-167">Un modello particolare nelle query LINQ restituisce lo stesso risultato di un `LEFT JOIN` nel server.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-167">A particular pattern in LINQ queries gives the same result as a `LEFT JOIN` on the server.</span></span> <span data-ttu-id="1e5eb-168">EF Core identifica tali modelli e genera l'equivalente `LEFT JOIN` sul lato server.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-168">EF Core identifies such patterns and generates the equivalent `LEFT JOIN` on the server side.</span></span> <span data-ttu-id="1e5eb-169">Il modello prevede la creazione di un GroupJoin tra le origini dati e quindi la Flating del raggruppamento usando l'operatore SelectMany con DefaultIfEmpty nell'origine di raggruppamento in modo che corrisponda a null quando l'oggetto interno non ha un elemento correlato.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-169">The pattern involves creating a GroupJoin between both the data sources and then flattening out the grouping by using the SelectMany operator with DefaultIfEmpty on the grouping source to match null when the inner doesn't have a related element.</span></span> <span data-ttu-id="1e5eb-170">Nell'esempio seguente viene illustrato l'aspetto del modello e l'elemento che genera.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-170">The following example shows what that pattern looks like and what it generates.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#LeftJoin)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]
```

<span data-ttu-id="1e5eb-171">Il modello precedente crea una struttura complessa nell'albero delle espressioni.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-171">The above pattern creates a complex structure in the expression tree.</span></span> <span data-ttu-id="1e5eb-172">Per questo motivo, EF Core necessario rendere flat i risultati del raggruppamento dell'operatore GroupJoin in un passaggio immediatamente successivo all'operatore.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-172">Because of that, EF Core requires you to flatten out the grouping results of the GroupJoin operator in a step immediately following the operator.</span></span> <span data-ttu-id="1e5eb-173">Anche se GroupJoin-DefaultIfEmpty-SelectMany viene usato ma con un modello diverso, è possibile che non venga identificato come left join.</span><span class="sxs-lookup"><span data-stu-id="1e5eb-173">Even if the GroupJoin-DefaultIfEmpty-SelectMany is used but in a different pattern, we may not identify it as a Left Join.</span></span>