---
title: Salvataggio asincrono - EF Core
author: rowanmiller
ms.date: 01/24/2017
ms.assetid: b64a606e-ecd9-4807-829a-b6ec05ade33f
uid: core/saving/async
ms.openlocfilehash: 0823b86f0579dd3e42f6bd2aebfb433d3cbe00ab
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197815"
---
# <a name="asynchronous-saving"></a><span data-ttu-id="4fb7e-102">Salvataggio asincrono</span><span class="sxs-lookup"><span data-stu-id="4fb7e-102">Asynchronous Saving</span></span>

<span data-ttu-id="4fb7e-103">Il salvataggio asincrono consente di evitare il blocco di un thread mentre le modifiche vengono scritte nel database.</span><span class="sxs-lookup"><span data-stu-id="4fb7e-103">Asynchronous saving avoids blocking a thread while the changes are written to the database.</span></span> <span data-ttu-id="4fb7e-104">Questo può essere utile per evitare di bloccare l'interfaccia utente di un'applicazione thick-client.</span><span class="sxs-lookup"><span data-stu-id="4fb7e-104">This can be useful to avoid freezing the UI of a thick-client application.</span></span> <span data-ttu-id="4fb7e-105">Le operazioni asincrone possono anche aumentare la velocità effettiva in un'applicazione Web, in cui il thread può essere liberato per gestire altre richieste mentre viene completata l'operazione di database.</span><span class="sxs-lookup"><span data-stu-id="4fb7e-105">Asynchronous operations can also increase throughput in a web application, where the thread can be freed up to service other requests while the database operation completes.</span></span> <span data-ttu-id="4fb7e-106">Per altre informazioni, vedere [Programmazione asincrona in C#](https://docs.microsoft.com/dotnet/csharp/async).</span><span class="sxs-lookup"><span data-stu-id="4fb7e-106">For more information, see [Asynchronous Programming in C#](https://docs.microsoft.com/dotnet/csharp/async).</span></span>

> [!WARNING]  
> <span data-ttu-id="4fb7e-107">EF Core non supporta più operazioni parallele in esecuzione nella stessa istanza di contesto.</span><span class="sxs-lookup"><span data-stu-id="4fb7e-107">EF Core does not support multiple parallel operations being run on the same context instance.</span></span> <span data-ttu-id="4fb7e-108">È sempre necessario attendere il completamento di un'operazione prima di iniziare l'operazione successiva.</span><span class="sxs-lookup"><span data-stu-id="4fb7e-108">You should always wait for an operation to complete before beginning the next operation.</span></span> <span data-ttu-id="4fb7e-109">Questa operazione viene in genere eseguita tramite la parola chiave `await` per ogni operazione asincrona.</span><span class="sxs-lookup"><span data-stu-id="4fb7e-109">This is typically done by using the `await` keyword on each asynchronous operation.</span></span>

<span data-ttu-id="4fb7e-110">Entity Framework Core offre `DbContext.SaveChangesAsync()` come alternativa asincrona a `DbContext.SaveChanges()`.</span><span class="sxs-lookup"><span data-stu-id="4fb7e-110">Entity Framework Core provides `DbContext.SaveChangesAsync()` as an asynchronous alternative to `DbContext.SaveChanges()`.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Async/Sample.cs#Sample)]
