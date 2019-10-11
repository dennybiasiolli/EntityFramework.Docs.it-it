---
title: Uso delle entità disconnesse - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 12138003-a373-4817-b1b7-724130202f5f
ms.openlocfilehash: f1ce44e7b00ec4c60a81ed850ce5c9d866495e1b
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181763"
---
# <a name="working-with-disconnected-entities"></a>Uso delle entità disconnesse
In un'applicazione basata su Entity Framework il rilevamento delle modifiche applicate alle entità rilevate viene eseguito da una classe contesto. Se si chiama il metodo SaveChanges le modifiche rilevate dal contesto vengono rese persistenti per il database. Quando si usano le applicazioni a più livelli, gli oggetti entità vengono modificati generalmente durante la disconnessione dal contesto ed è necessario decidere come tenere traccia delle modifiche e come segnalare le modifiche al contesto. Questo argomento descrive diverse opzioni disponibili quando si usa Entity Framework con entità disconnesse.   

## <a name="web-service-frameworks"></a>Framework di servizi Web

Le tecnologie dei servizi Web supportano in genere criteri che possono essere usati per rendere persistenti le modifiche in oggetti singoli disconnessi. Ad esempio, l'API Web ASP.NET consente di codificare le azioni del controller che possono includere chiamate a Entity Framework per rendere persistenti le modifiche apportate a un oggetto in un database. In effetti, gli strumenti per l'API Web in Visual Studio semplificano lo scaffolding di un controller dell'API Web dal modello di Entity Framework 6. Per altre informazioni, vedere [Uso di API Web con Entity Framework 6](https://docs.microsoft.com/aspnet/web-api/overview/data/using-web-api-with-entity-framework/).   

In passato, erano disponibili altre tecnologie di servizi Web che offrivano l'integrazione con Entity Framework, come ad esempio [WCF Data Services](https://docs.microsoft.com/dotnet/framework/data/wcf/create-a-data-service-using-an-adonet-ef-data-wcf) e [Servizi RIA](https://docs.microsoft.com/previous-versions/dotnet/wcf-ria/ee707344(v=vs.91)).

## <a name="low-level-ef-apis"></a>API Entity Framework di basso livello

Se non si vuole usare una soluzione a più livelli esistente, o se si vuole personalizzare il comportamento all'interno di un'azione del controller in servizi API Web, Entity Framework offre API che consentono di applicare le modifiche apportate a un livello disconnesso. Per altre informazioni, vedere [Add, Attach, and entity state](~/ef6/saving/change-tracking/entity-state.md) (Aggiungere e allegare stato entità).  

## <a name="self-tracking-entities"></a>Entità con rilevamento automatico  

Rilevare le modifiche nei grafici arbitrari delle entità durante la disconnessione dal contesto di Entity Framework è un problema complesso. Uno dei tentativi per risolverlo è stato il modello di generazione di codice di entità con rilevamento automatico. Questo modello genera classi di entità che contengono la logica per tenere traccia delle modifiche apportate in un livello disconnesso come stato nelle entità stesse. Viene generato anche un set di metodi di estensione per applicare le modifiche a un contesto.

Questo modello può essere usato con i modelli creati usando la finestra di progettazione di Entity Framework, ma non può essere usato con i modelli Code First. Per altre informazioni, vedere [Self-Tracking Entities](self-tracking-entities/index.md) (Entità con rilevamento automatico).  

> [!IMPORTANT]
> L'uso del modello di entità con rilevamento automatico non è più consigliabile. Continuerà a essere disponibile solo per supportare le applicazioni esistenti. Se l'applicazione richiede l'uso con grafici di entità disconnesse, prendere in considerazione altre alternative, come ad esempio [Trackable Entities](https://trackableentities.github.io/), che è una tecnologia simile alle entità con rilevamento automatico ma viene sviluppata in modo più attivo dalla community, oppure la scrittura di codice personalizzato usando le API di rilevamento delle modifiche di basso livello.
