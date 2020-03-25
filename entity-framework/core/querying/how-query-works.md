---
title: Funzionamento delle query - EF Core
author: ajcvickers
ms.date: 03/17/2020
ms.assetid: de2e34cd-659b-4cab-b5ed-7a979c6bf120
uid: core/querying/how-query-works
ms.openlocfilehash: e8a50efe31468ea8df211602636dd474550bc0ef
ms.sourcegitcommit: c3b8386071d64953ee68788ef9d951144881a6ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "80136246"
---
# <a name="how-queries-work"></a>Funzionamento delle query

Entity Framework Core usa query LINQ (Language Integrated Query) per eseguire query sui dati dal database. Le query LINQ consentono di usare C#, o il linguaggio .NET che si preferisce, per generare query fortemente tipizzate in base alle classi di contesto ed entità derivate.

## <a name="the-life-of-a-query"></a>Ciclo di vita di una query

Di seguito è disponibile una panoramica generale del processo di elaborazione di ogni query.

1. La query LINQ viene elaborata da Entity Framework Core per creare una rappresentazione pronta per essere elaborata dal provider di database
   1. Il risultato viene memorizzato nella cache in modo che tale elaborazione non debba essere ripetuta per ogni esecuzione della query
2. Il risultato viene passato al provider di database
   1. Il provider di database identifica le parti della query che possono essere valutate nel database
   2. Queste parti della query vengono convertite nel linguaggio di query specifico del database, ad esempio SQL per un database relazionale
   3. Viene inviata una query al database e il set di risultati restituito (i risultati sono valori del database, non istanze di entità)
3. Per ogni elemento nel set di risultati
   1. Se si tratta di una query con rilevamento delle modifiche, EF controlla se i dati rappresentano un'entità già presente nello strumento di rilevamento delle modifiche per l'istanza di contesto
      * In caso affermativo, viene restituita l'entità esistente
      * In caso contrario, viene creata una nuova entità, viene configurato il rilevamento delle modifiche e viene restituita la nuova entità
   2. Se si tratta di una query senza rilevamento, viene sempre creata e restituita una nuova entità

## <a name="when-queries-are-executed"></a>Quando vengono eseguite le query

Quando si chiamano operatori LINQ, si crea semplicemente una rappresentazione in memoria della query. La query viene inviata al database solo al momento dell'utilizzo dei risultati.

Le operazioni più comuni che causano l'invio della query al database sono:

* Iterazione dei risultati in un ciclo `for`
* Uso di un operatore, ad esempio `ToList`, `ToArray`, `Single`, `Count` o gli overload asincroni equivalenti

> [!WARNING]  
> **Convalidare sempre l'input dell'utente**: anche se EF Core offre protezione da attacchi SQL injection tramite l'uso di parametri e valori letterali di escape nelle query, non convalida gli input. La convalida appropriata, in base ai requisiti dell'applicazione, deve essere eseguita prima che i valori provenienti da origini non attendibili vengano usati nelle query LINQ, assegnati alle proprietà dell'entità o passati ad altre API EF Core. Ciò include qualsiasi input dell'utente usato per costruire query in modo dinamico. Anche quando si usa LINQ, se si accetta l'input dell'utente per la creazione delle espressioni, è necessario assicurarsi che possano essere costruite solo le espressioni previste.
