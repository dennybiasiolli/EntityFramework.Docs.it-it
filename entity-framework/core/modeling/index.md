---
title: Creazione e configurazione di un modello - EF Core
description: Panoramica della creazione e configurazione di un modello con Entity Framework Core
author: rowanmiller
ms.date: 11/05/2019
uid: core/modeling/index
ms.openlocfilehash: 1380ed0c1af0fdd3ae24de311bea455cb57a7dcd
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071420"
---
# <a name="creating-and-configuring-a-model"></a>Creazione e configurazione di un modello

Entity Framework usa un insieme di convenzioni per compilare un modello in base alla forma delle classi di entità. È possibile specificare una configurazione aggiuntiva per integrare e/o sostituire gli elementi individuati dalla convenzione.

Questo articolo descrive la configurazione che può essere applicata a un modello destinato a qualsiasi archivio dati e che può essere applicata per qualsiasi database relazionale. I provider possono anche abilitare una configurazione specifica di un archivio dati. Per informazioni sulla configurazione specifica del provider, vedere la sezione  [Provider di database](xref:core/providers/index) .

> [!TIP]  
> È possibile visualizzare l' [esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples)  di questo articolo in GitHub.

## <a name="use-fluent-api-to-configure-a-model"></a>Usare l'API Fluent per configurare un modello

È possibile eseguire l'override del metodo  `OnModelCreating`  nel contesto derivato e usare  `ModelBuilder API` per configurare il modello. Questo è il metodo di configurazione più efficace e consente di specificare la configurazione senza modificare le classi di entità. La configurazione dell'API Fluent ha la precedenza più elevata e sostituisce le convenzioni e le annotazioni dei dati.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?highlight=12-14)]

## <a name="use-data-annotations-to-configure-a-model"></a>Usare le annotazioni dei dati per configurare un modello

È anche possibile applicare attributi (chiamati annotazioni dei dati) alle classi e alle proprietà. Le annotazioni dei dati sostituiranno le convenzioni, ma saranno a loro volta ignorate dalla configurazione dell'API Fluent.

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?highlight=15)]
