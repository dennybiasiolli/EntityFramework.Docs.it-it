---
title: Provider di database SQLite-limitazioni-EF Core
description: Limitazioni del provider di database Entity Framework Core SQLite rispetto ad altri provider
author: bricelam
ms.date: 07/16/2020
uid: core/providers/sqlite/limitations
ms.openlocfilehash: 546910afb9c97a93a7cc471bb813be0b9874a4bd
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071225"
---
# <a name="sqlite-ef-core-database-provider-limitations"></a>Limitazioni del provider di database SQLite per EF Core

Il provider SQLite presenta alcune limitazioni relative alle migrazioni. La maggior parte di queste limitazioni è il risultato di limitazioni nel motore di database SQLite sottostante e non sono specifiche di EF.

## <a name="modeling-limitations"></a>Limitazioni di modellazione

La libreria relazionale comune (condivisa da Entity Framework provider di database relazionali) definisce le API per i concetti di modellazione comuni alla maggior parte dei motori di database relazionali. Alcuni di questi concetti non sono supportati dal provider SQLite.

* Schemi
* Sequenze

## <a name="query-limitations"></a>Limitazioni delle query

SQLite non supporta in modo nativo i tipi di dati seguenti. EF Core possibile leggere e scrivere valori di questi tipi ed è supportata anche l'esecuzione di query per verificarne l'uguaglianza ( `where e.Property == value` ). Altre operazioni, tuttavia, come il confronto e l'ordinamento richiederanno una valutazione sul client.

* DateTimeOffset
* Decimal
* TimeSpan
* UInt64

Invece di `DateTimeOffset` , è consigliabile usare i valori DateTime. Quando si gestiscono più fusi orari, è consigliabile convertire i valori in formato UTC prima di salvare e quindi tornare al fuso orario appropriato.

Il `Decimal` tipo fornisce un livello elevato di precisione. Se questo livello di precisione non è necessario, tuttavia, è consigliabile usare invece Double. È possibile usare un [convertitore di valori](xref:core/modeling/value-conversions) per continuare a usare Decimal nelle classi.

``` csharp
modelBuilder.Entity<MyEntity>()
    .Property(e => e.DecimalProperty)
    .HasConversion<double>();
```

## <a name="migrations-limitations"></a>Limitazioni delle migrazioni

Il motore di database SQLite non supporta una serie di operazioni dello schema supportate dalla maggior parte degli altri database relazionali. Se si tenta di applicare una delle operazioni non supportate a un database SQLite `NotSupportedException` , verrà generata un'eccezione.

Per eseguire determinate operazioni verrà tentata una ricompilazione. Le ricompilazioni sono possibili solo per gli elementi del database che fanno parte del modello di EF Core. Se un elemento del database non fa parte del modello, ad esempio se è stato creato manualmente all'interno di una migrazione, `NotSupportedException` viene comunque generata un'eccezione.

| Operazione            | Supportato?  | Richiede la versione |
|:---------------------|:------------|:-----------------|
| AddCheckConstraint   | ✔ (ricompila) | 5,0              |
| AddColumn            | ✔           | 1,0              |
| AddForeignKey        | ✔ (ricompila) | 5,0              |
| AddPrimaryKey        | ✔ (ricompila) | 5,0              |
| AddUniqueConstraint  | ✔ (ricompila) | 5,0              |
| AlterColumn          | ✔ (ricompila) | 5,0              |
| CreateIndex          | ✔           | 1,0              |
| CreateTable          | ✔           | 1,0              |
| DropCheckConstraint  | ✔ (ricompila) | 5,0              |
| DropColumn           | ✔ (ricompila) | 5,0              |
| DropForeignKey       | ✔ (ricompila) | 5,0              |
| DropIndex            | ✔           | 1,0              |
| DropPrimaryKey       | ✔ (ricompila) | 5,0              |
| DropTable            | ✔           | 1,0              |
| DropUniqueConstraint | ✔ (ricompila) | 5,0              |
| RenameColumn         | ✔           | 2.2.2            |
| RenameIndex          | ✔ (ricompila) | 2.1              |
| RenameTable          | ✔           | 1,0              |
| EnsureSchema         | ✔ (no-op)   | 2.0              |
| DropSchema           | ✔ (no-op)   | 2.0              |
| Insert               | ✔           | 2.0              |
| Aggiorna               | ✔           | 2.0              |
| Delete               | ✔           | 2.0              |

## <a name="migrations-limitations-workaround"></a>Soluzione alternativa alle migrazioni

È possibile aggirare alcune di queste limitazioni scrivendo manualmente il codice nelle migrazioni per eseguire una ricompilazione. Le ricompilazioni delle tabelle comportano la creazione di una nuova tabella, la copia dei dati nella nuova tabella, l'eliminazione della tabella precedente, la ridenominazione della nuova tabella. `Sql(string)`Per eseguire alcuni di questi passaggi, sarà necessario usare il metodo.

Per altri dettagli, vedere la documentazione di SQLite per [altri tipi di modifiche dello schema della tabella](https://sqlite.org/lang_altertable.html#otheralter) .
