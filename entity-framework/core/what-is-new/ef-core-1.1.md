---
title: Novità di EF Core 1.1 - EF Core
description: Modifiche e miglioramenti in Entity Framework Core 1,1
author: divega
ms.date: 10/27/2016
uid: core/what-is-new/ef-core-1.1
ms.openlocfilehash: 5f81e8b25feba5cdf5ae5e84b1d3362912ab5b26
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072408"
---
# <a name="new-features-in-ef-core-11"></a>Nuove funzionalità di EF Core 1.1

## <a name="modeling"></a>Modellazione

### <a name="field-mapping"></a>Mapping campi

Consente di configurare un campo sottostante per una proprietà. Può essere utile per le proprietà di sola lettura o per i dati che hanno metodi Get/Set invece di una proprietà.

### <a name="mapping-to-memory-optimized-tables-in-sql-server"></a>Mapping a tabelle ottimizzate per la memoria in SQL Server

È possibile specificare che la tabella a cui viene eseguito il mapping di un'entità è ottimizzata per la memoria. Quando si usa EF Core per creare e gestire un database in base al modello (con le migrazioni o `Database.EnsureCreated()`), per queste entità verrà creata una tabella ottimizzata per la memoria.

## <a name="change-tracking"></a>Change tracking

### <a name="additional-change-tracking-apis-from-ef6"></a>Altre API di rilevamento modifiche da EF6

Ad esempio, `Reload`, `GetModifiedProperties`, `GetDatabaseValues` e così via.

## <a name="query"></a>Query

### <a name="explicit-loading"></a>Caricamento esplicito

Consente di attivare il popolamento di una proprietà di navigazione in un'entità caricata in precedenza dal database.

### <a name="dbsetfind"></a>DbSet.Find

Consente di recuperare facilmente un'entità in base al valore della chiave primaria.

## <a name="other"></a>Altro

### <a name="connection-resiliency"></a>Resilienza della connessione

Ritenta automaticamente i comandi del database non riusciti. È particolarmente utile durante la connessione a SQL Azure, in cui sono comuni gli errori temporanei.

### <a name="simplified-service-replacement"></a>Sostituzione dei servizi semplificata

Facilita la sostituzione dei servizi interni usati da EF.
