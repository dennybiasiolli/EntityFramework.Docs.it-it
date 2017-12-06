---
title: Chiavi alternative (vincoli univoci) - Core a Entity Framework
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 3d419dcf-2b5d-467c-b408-ea03d830721a
ms.technology: entity-framework-core
uid: core/modeling/relational/unique-constraints
ms.openlocfilehash: 1b7e2bef6ede95f8c27211ba00dcc6b97cccde9b
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2017
---
# <a name="alternate-keys-unique-constraints"></a><span data-ttu-id="c7229-102">Chiavi alternative (vincoli univoci)</span><span class="sxs-lookup"><span data-stu-id="c7229-102">Alternate Keys (Unique Constraints)</span></span>

> [!NOTE]  
> <span data-ttu-id="c7229-103">La configurazione di questa sezione è applicabile a database relazionali in generale.</span><span class="sxs-lookup"><span data-stu-id="c7229-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="c7229-104">I metodi di estensione qui verranno rese disponibili quando si installa un provider di database relazionali (a causa di condiviso *Microsoft.EntityFrameworkCore.Relational* pacchetto).</span><span class="sxs-lookup"><span data-stu-id="c7229-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="c7229-105">Un vincolo unique è stato introdotto per ogni chiave alternativa nel modello.</span><span class="sxs-lookup"><span data-stu-id="c7229-105">A unique constraint is introduced for each alternate key in the model.</span></span>

## <a name="conventions"></a><span data-ttu-id="c7229-106">Convenzioni</span><span class="sxs-lookup"><span data-stu-id="c7229-106">Conventions</span></span>

<span data-ttu-id="c7229-107">Per convenzione, l'indice e vincolo introdotte per una chiave alternativa verrà denominati `AK_<type name>_<property name>`.</span><span class="sxs-lookup"><span data-stu-id="c7229-107">By convention, the index and constraint that are introduced for an alternate key will be named `AK_<type name>_<property name>`.</span></span> <span data-ttu-id="c7229-108">Per le chiavi alternative composte `<property name>` diventa un elenco separato da un carattere di sottolineatura di nomi di proprietà.</span><span class="sxs-lookup"><span data-stu-id="c7229-108">For composite alternate keys `<property name>` becomes an underscore separated list of property names.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="c7229-109">Annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="c7229-109">Data Annotations</span></span>

<span data-ttu-id="c7229-110">I vincoli UNIQUE non possono essere configurati utilizzando le annotazioni dei dati.</span><span class="sxs-lookup"><span data-stu-id="c7229-110">Unique constraints can not be configured using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="c7229-111">Microsoft Office Fluent API</span><span class="sxs-lookup"><span data-stu-id="c7229-111">Fluent API</span></span>

<span data-ttu-id="c7229-112">Per configurare il nome di indice e vincolo per una chiave alternativa, è possibile utilizzare l'API Fluent.</span><span class="sxs-lookup"><span data-stu-id="c7229-112">You can use the Fluent API to configure the index and constraint name for an alternate key.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/AlternateKeyName.cs?name=Model&highlight=9)]
