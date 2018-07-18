---
title: Ripristino di ObjectContext in Entity Framework Designer - Entity Framework 6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 36550569-a1de-47cb-ba6d-544794ffd500
caps.latest.revision: 3
ms.openlocfilehash: 17fa6538cf5e92f59ab72376af96ad65c640a085
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2018
ms.locfileid: "39121166"
---
# <a name="reverting-to-objectcontext-in-entity-framework-designer"></a>Ripristino di ObjectContext in Entity Framework Designer
Con la versione precedente di Entity Framework, un modello creato con la finestra di progettazione di Entity Framework genera classi di entità derivato da EntityObject e un contesto che deriva da ObjectContext.

È consigliabile partire EF4.1 dello swapping in un modello di generazione di codice che genera un contesto che deriva da DbContext e POCO classi di entità.

In Visual Studio 2012 si ottiene codice DbContext generato per impostazione predefinita per tutti i nuovi modelli creati con la finestra di progettazione di Entity Framework. I modelli esistenti continueranno a generare il codice di ObjectContext in base a meno che non si decide di scambiare al generatore di codice di base di DbContext.

## <a name="reverting-back-to-objectcontext-code-generation"></a>Eseguendo il ripristino di generazione del codice di ObjectContext

### <a name="1-disable-dbcontext-code-generation"></a>1. Disabilitare la generazione del codice DbContext

Generazione delle classi derivate DbContext e POCO è gestita da due file con estensione tt nel progetto, se si espande il file con estensione edmx in Esplora soluzioni verrà visualizzato questi file. Eliminare entrambi i file dal progetto.

![CodeGenFiles](~/ef6/media/codegenfiles.png)

Se si usa Visual Basic.NET è necessario selezionare la **Mostra tutti i file** pulsante per visualizzare i file annidati.

![ShowAllFiles](~/ef6/media/showallfiles.png)

### <a name="2-re-enable-objectcontext-code-generation"></a>2. Abilitare nuovamente la generazione del codice di ObjectContext

Open è modellare in Entity Framework Designer, fare clic su una sezione vuota dell'area di progettazione e seleziona **proprietà**.

La modifica della finestra delle proprietà di **Code Generation Strategy** da **None** a **predefinito**.

![CodeGenStrategy](~/ef6/media/codegenstrategy.png)