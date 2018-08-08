---
title: Introduzione a Entity Framework 6 - EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 66ce9113-81d2-480f-8c16-d00ec405b2f7
caps.latest.revision: 3
ms.openlocfilehash: 36857650bc546acd769e629a1e92948a63bfb786
ms.sourcegitcommit: 00cb52625b57c1ea339ded1454179fe89b6bcfea
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/16/2018
ms.locfileid: "39119640"
---
# <a name="get-started-with-entity-framework-6"></a>Introduzione a Entity Framework 6

Questa guida contiene una raccolta di collegamenti ad articoli della documentazione, procedure dettagliate e video selezionati per iniziare a usare rapidamente il prodotto:

## <a name="get-entity-frameworkef6fundamentalsinstallmd"></a>[Ottenere Entity Framework](~/ef6/fundamentals/install.md)
Viene descritto come aggiungere Entity Framework alle applicazioni e, se si vuole usare EF Designer, assicurarsi che venga installato in Visual Studio.

## <a name="creating-a-model-code-first-the-ef-designer-and-the-ef-workflowsef6modelingindexmd"></a>[Creazione di un modello: flussi di lavoro di Code First, EF Designer ed EF](~/ef6/modeling/index.md)
Si preferisce specificare il modello EF scrivendo codice o tracciando caselle e linee?
Si userà EF per mappare gli oggetti in un database esistente o si vuole che EF crei un database specifico per gli oggetti?
Di seguito sono descritti due approcci diversi per usare EF6: EF Designer e Code First.
Assicurarsi di seguire la discussione e di guardare il video che illustra le differenze.

## <a name="working-with-dbcontextef6fundamentalsworking-with-dbcontextmd"></a>[Utilizzo di DbContext](~/ef6/fundamentals/working-with-dbcontext.md)
DbContext è il principale tipo di Entity Framework che è necessario imparare a usare. Svolge la funzione di launchpad delle query del database e tiene traccia delle modifiche apportate agli oggetti in modo che possano essere salvate in modo permanente nel database.

## <a name="ask-a-questionef6resourcesget-helpmd"></a>[Poni una domanda](~/ef6/resources/get-help.md)
È possibile ricevere assistenza dagli esperti e contribuire con le proprie risposte alla community.

## <a name="contributehttpgithubcomaspnetentityframework6"></a>[Collaborare](http://github.com/aspnet/EntityFramework6/)
Entity Framework 6 usa un modello di sviluppo aperto. Nel repository GitHub sono disponibili informazioni su come migliorare ulteriormente Entity Framework.

## <a name="index-of-walkthroughs"></a>Indice delle procedure dettagliate

- Code First
  - [Code First nel flusso di lavoro di un database esistente](~/ef6/modeling/code-first/workflows/existing-database.md)
  - [Code First nel flusso di lavoro di un nuovo database](~/ef6/modeling/code-first/workflows/new-database.md)
  - [Mapping delle enumerazioni usando Code First](~/ef6/modeling/code-first/data-types/enums.md)
  - [Mapping dei tipi spaziali usando Code First](~/ef6/modeling/code-first/data-types/spatial.md)
  - [Creazione di convenzioni Code First personalizzate](~/ef6/modeling/code-first/conventions/custom.md)
  - [Uso della configurazione Fluent di Code First con Visual Basic](~/ef6/modeling/code-first/fluent/vb.md)
  - [Migrazioni Code First](~/ef6/modeling/code-first/migrations/index.md)
  - [Migrazioni Code First in ambienti team](~/ef6/modeling/code-first/migrations/teams.md)
  - [Migrazioni Code First automatiche](~/ef6/modeling/code-first/migrations/automatic.md) (non consigliato)

- EF Designer
  - [Flusso di lavoro di Database First](~/ef6/modeling/designer/workflows/database-first.md)
  - [Flusso di lavoro di Model First](~/ef6/modeling/designer/workflows/model-first.md)
  - [Mapping delle enumerazioni](~/ef6/modeling/designer/data-types/enums.md)
  - [Mapping dei tipi spaziali](~/ef6/modeling/designer/data-types/spatial.md)
  - [Mapping dell'ereditarietà della tabella per gerarchia](~/ef6/modeling/designer/inheritance/tph.md)
  - [Mapping dell'ereditarietà della tabella per tipo](~/ef6/modeling/designer/inheritance/tpt.md)
  - [Mapping di stored procedure per gli aggiornamenti](~/ef6/modeling/designer/stored-procedures/cud.md)
  - [Mapping di stored procedure per le query](~/ef6/modeling/designer/stored-procedures/query.md)
  - [Suddivisione di entità](~/ef6/modeling/designer/entity-splitting.md)
  - [Suddivisione di tabelle](~/ef6/modeling/designer/table-splitting.md)
  - [Query di definizione](~/ef6/modeling/designer/advanced/defining-query.md) (informazioni avanzate)
  - [Funzioni con valori di tabella](~/ef6/modeling/designer/advanced/tvfs.md) (informazioni avanzate)

- Concetti fondamentali
  - [Query e salvataggio asincroni](~/ef6/fundamentals/async.md)
  - [Data binding con WinForms](~/ef6/fundamentals/databinding/winforms.md)
  - [Data binding con WPF](~/ef6/fundamentals/databinding/wpf.md)
  - [Scenari disconnessi con entità con rilevamento automatico](~/ef6/fundamentals/disconnected-entities/self-tracking-entities/walkthrough.md) (non consigliato)