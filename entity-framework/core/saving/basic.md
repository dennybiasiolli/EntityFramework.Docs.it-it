---
title: Salvataggio di base - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 850d842e-3fad-4ef2-be17-053768e97b9e
uid: core/saving/basic
ms.openlocfilehash: 6f72458504a9dbe99038af7cfd23b6991258f6b8
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197783"
---
# <a name="basic-save"></a>Salvataggio di base

Informazioni su come aggiungere, modificare e rimuovere i dati usando le classi di contesto e di entità.

> [!TIP]  
> È possibile visualizzare l'[esempio](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Basics/) di questo articolo in GitHub.

## <a name="adding-data"></a>Aggiunta di dati

Usare il metodo *DbSet.Add* per aggiungere nuove istanze delle classi di entità. I dati verranno inseriti nel database quando si chiama *SaveChanges*.

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#Add)]

> [!TIP]  
> I metodi Add, Attach e Update operano tutti sul grafo completo delle entità passato, come descritto nella sezione [Dati correlati](related-data.md). In alternativa, è possibile usare la proprietà EntityEntry.State per impostare lo stato di una singola entità. Ad esempio `context.Entry(blog).State = EntityState.Modified`.

## <a name="updating-data"></a>Aggiornamento di dati

EF rileverà automaticamente le modifiche apportate a un'entità esistente con rilevamento delle modifiche dal contesto. Sono incluse le entità caricate/sottoposte a query dal database e le entità aggiunte e salvate in precedenza nel database.

È sufficiente modificare i valori assegnati alle proprietà e quindi chiamare *SaveChanges*.

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#Update)]

## <a name="deleting-data"></a>Eliminazione di dati

Usare il metodo *DbSet.Remove* per eliminare le istanze delle classi di entità.

Se l'entità esiste già nel database, verrà eliminata durante *SaveChanges*. Se l'entità non è ancora stata salvata nel database (ad esempio è contrassegnata come aggiunta), verrà rimossa dal contesto e non verrà più inserita quando viene chiamato *SaveChanges*.

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#Remove)]

## <a name="multiple-operations-in-a-single-savechanges"></a>Più operazioni in una singola chiamata a SaveChanges

È possibile combinare più operazioni Add/Update/Remove in una singola chiamata a *SaveChanges*.

> [!NOTE]  
> Per la maggior parte dei provider di database, *SaveChanges* è transazionale. Questo significa che tutte le operazioni avranno esito positivo o negativo e le operazioni non verranno mai lasciate in condizione di applicazione parziale.

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#MultipleOperations)]
