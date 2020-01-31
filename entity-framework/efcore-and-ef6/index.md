---
title: Confronto tra EF6 e EF Core
description: Indicazioni su come scegliere tra EF6 e EF Core.
author: ajcvickers
ms.date: 01/23/2019
ms.assetid: a6b9cd22-6803-4c6c-a4d4-21147c0a81cb
uid: efcore-and-ef6/index
ms.openlocfilehash: e28c7d0299e5089f56fb0795d00917cfc30f5cf1
ms.sourcegitcommit: b3cf5d2e3cb170b9916795d1d8c88678269639b1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2020
ms.locfileid: "76888148"
---
# <a name="compare-ef-core--ef6"></a>Confronto tra EF Core e EF6

## <a name="ef-core"></a>EF Core

Entity Framework Core ([EF Core](../core/index.md)) è un mapper di database a oggetti moderno per .NET. Supporta le query LINQ, il rilevamento delle modifiche, gli aggiornamenti e le migrazioni dello schema.

EF Core funziona con SQL Server/SQL Azure, SQLite, Azure Cosmos DB, MySQL, PostgreSQL e molti altri database tramite un [modello di plug-in del provider di database](../core/providers/index.md).

## <a name="ef6"></a>EF6

Entity Framework 6 ([EF6](../ef6/index.md)) è un mapper relazionale a oggetti progettato per .NET Framework ma con supporto per .NET Core. EF6 è un prodotto stabile e supportato, ma non è più in sviluppo attivo.

## <a name="feature-comparison"></a>Confronto tra funzionalità

EF Core offre nuove funzionalità che non verranno implementate in EF6. Tuttavia, non tutte le funzionalità di EF6 sono attualmente implementate in EF Core.

Le tabelle seguenti confrontano le funzionalità disponibili in EF Core e in EF6. Questo è un confronto generale in cui non vengono elencate tutte le funzionalità o illustrate le differenze tra la stessa funzionalità nelle diverse versioni di Entity Framework.

La colonna EF Core contiene la versione del prodotto in cui la funzionalità è apparsa per la prima volta.

### <a name="creating-a-model"></a>Creazione di un modello

| **Funzionalità**                                           | **EF6.4**| **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| Mapping delle classi di base                                   | Sì      | 1.0                                   |
| Costruttori con parametri                          |          | 2.1                                   |
| Conversioni di valori di proprietà                            |          | 2.1                                   |
| Tipi con mapping senza chiavi                             |          | 2.1                                   |
| Convenzioni                                           | Sì      | 1.0                                   |
| Convenzioni personalizzate                                    | Sì      | 1.0 (parziale; [#214](https://github.com/dotnet/efcore/issues/214)) |
| Annotazioni dei dati                                      | Sì      | 1.0                                   |
| API Fluent                                            | Sì      | 1.0                                   |
| Ereditarietà: tabella per gerarchia                | Sì      | 1.0                                   |
| Ereditarietà: tabella per tipo                     | Sì      | Pianificata per la versione 5.0 ([#2266](https://github.com/dotnet/efcore/issues/2266)) |
| Ereditarietà: tabella per classe concreta           | Sì      | Obiettivo aggiuntivo per la versione 5.0 ([#3170](https://github.com/dotnet/efcore/issues/3170)) <sup>(1)</sup> |
| Proprietà con stato shadow                               |          | 1.0                                   |
| Chiavi alternative                                        |          | 1.0                                   |
| Navigazioni molti-a-molti                              | Sì      | Pianificata per la versione 5.0 ([#19003](https://github.com/dotnet/efcore/issues/19003)) |
| Molti-a-molti senza entità join                      | Sì      | Nel backlog ([#1368](https://github.com/dotnet/efcore/issues/1368)) |
| Generazione delle chiavi: Database                              | Sì      | 1.0                                   |
| Generazione delle chiavi: Client                                |          | 1.0                                   |
| Tipi complessi/di proprietà                                   | Sì      | 2.0                                   |
| Dati spaziali                                          | Sì      | 2.2                                   |
| Formato del modello: Codice                                    | Sì      | 1.0                                   |
| Creazione del modello dal database: Riga di comando              | Sì      | 1.0                                   |
| Aggiornamento del modello dal database                            | Partial  | Nel backlog ([#831](https://github.com/dotnet/efcore/issues/831)) |
| Filtri di query globali                                  |          | 2.0                                   |
| Suddivisione di tabelle                                       | Sì      | 2.0                                   |
| Suddivisione di entità                                      | Sì      | Obiettivo aggiuntivo per la versione 5.0 ([#620](https://github.com/dotnet/efcore/issues/620)) <sup>(1)</sup> |
| Mapping di funzione scalare del database                      | Poor     | 2.0                                   |
| Mapping campi                                         |          | 1.1                                   |
| Tipi riferimento nullable (C# 8.0)                     |          | 3.0                                   |
| Visualizzazione grafica del modello                      | Sì      | Nessun supporto pianificato <sup>(2)</sup>     |
| Editor di modello grafico                                | Sì      | Nessun supporto pianificato <sup>(2)</sup>     |
| Formato del modello: EDMX (XML)                              | Sì      | Nessun supporto pianificato <sup>(2)</sup>     |
| Creazione del modello dal database: procedura guidata di Visual Studio                 | Sì      | Nessun supporto pianificato <sup>(2)</sup>     |

### <a name="querying-data"></a>Esecuzione di query su dati

| **Funzionalità**                                           | **EF6.4**| **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| Query LINQ                                          | Sì      | 1.0                                   |
| Query SQL generate come leggibili                                | Poor     | 1.0                                   |
| Conversione di GroupBy                                   | Sì      | 2.1                                   |
| Caricamento dei dati correlati: eager                           | Sì      | 1.0                                   |
| Caricamento dei dati correlati: caricamento eager per i tipi derivati |          | 2.1                                   |
| Caricamento dei dati correlati: Lazy                            | Sì      | 2.1                                   |
| Caricamento dei dati correlati: Explicit                        | Sì      | 1.1                                   |
| Query SQL non elaborate: Tipi di entità                         | Sì      | 1.0                                   |
| Query SQL non elaborate: tipi di entità senza chiave                 | Sì      | 2.1                                   |
| Query SQL non elaborate: Composizione con LINQ                  |          | 1.0                                   |
| Query compilate in modo esplicito                           | Poor     | 2.0                                   |
| await foreach (C# 8.0)                                |          | 3.0                                   |
| Linguaggio di query basato su testo (Entity SQL)                | Sì      | Nessun supporto pianificato <sup>(2)</sup>     |

### <a name="saving-data"></a>Salvataggio di dati

| **Funzionalità**                                           | **EF6.4**| **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| Rilevamento modifiche: Snapshot                             | Sì      | 1.0                                   |
| Rilevamento modifiche: Notifica                         | Sì      | 1.0                                   |
| Rilevamento modifiche: Proxy                              | Sì      | Sottoposta a merge per la versione 5.0 ([#10949](https://github.com/dotnet/efcore/issues/10949)) |
| Accesso allo stato registrato                               | Sì      | 1.0                                   |
| Concorrenza ottimistica                                | Sì      | 1.0                                   |
| Transazioni                                          | Sì      | 1.0                                   |
| Invio in batch di istruzioni                                |          | 1.0                                   |
| Mapping di stored procedure                              | Sì      | Nel backlog ([#245](https://github.com/dotnet/efcore/issues/245)) |
| API di basso livello grafo disconnesso                     | Poor     | 1.0                                   |
| End-to-end grafo disconnesso                         |          | 1.0 (parziale; [#5536](https://github.com/dotnet/efcore/issues/5536)) |

### <a name="other-features"></a>Altre funzionalità

| **Funzionalità**                                           | **EF6.4**| **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| Migrazioni                                            | Sì      | 1.0                                   |
| API di creazione/eliminazione del database                       | Sì      | 1.0                                   |
| Dati del valore di inizializzazione                                             | Sì      | 2.1                                   |
| Resilienza della connessione                                 | Sì      | 1.1                                   |
| Intercettori                                          | Sì      | 3.0                                   |
| Events                                                | Sì      | 3.0 (parziale; [#626](https://github.com/dotnet/efcore/issues/626)) |
| Registrazione semplice (Database.Log)                         | Sì      | Sottoposta a merge per la versione 5.0 ([#1199](https://github.com/dotnet/efcore/issues/1199)) |
| Pooling DbContext                                     |          | 2.0                                   |

### <a name="database-providers-sup3sup"></a>Provider di database <sup>(3)</sup>

| **Funzionalità**                                           | **EF6.4**| **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| SQL Server                                            | Sì      | 1.0                                   |
| MySQL                                                 | Sì      | 1.0                                   |
| PostgreSQL                                            | Sì      | 1.0                                   |
| Oracle                                                | Sì      | 1.0                                   |
| SQLite                                                | Sì      | 1.0                                   |
| SQL Server Compact                                    | Sì      | 1.0 <sup>(4)</sup>                    |
| DB2                                                   | Sì      | 1.0                                   |
| Firebird                                              | Sì      | 2.0                                   |
| Jet (Microsoft Access)                                |          | 2.0 <sup>(4)</sup>                    |
| Azure Cosmos DB                                       |          | 3.0                                   |
| In memoria (per il test)                               |          | 1.0                                   |

<sup>1</sup> È improbabile che gli obiettivi aggiuntivi vengano raggiunti per una determinata versione. Tuttavia, se tutto procede per il meglio, si tenterà di includerli.

<sup>2</sup> Alcune funzionalità di EF6 non verranno implementate in EF Core. Queste funzionalità dipendono dal modello EDM (Entity Data Model) sottostante di EF6 e/o sono funzionalità complesse con ritorno sugli investimenti relativamente basso. I commenti e i suggerimenti dei clienti sono sempre benvenuti, ma mentre EF Core supporta molti aspetti non possibili in EF6, non è fattibile viceversa per EF Core supportare tutte le funzionalità di EF6.

<sup>3</sup> I provider di database EF Core implementati da terze parti potrebbero subire un ritardo per l'aggiornamento alle nuove versioni principali di EF Core. Vedere [Provider di database](../core/providers/index.md) per altre informazioni.

<sup>4</sup> I provider SQL Server Compact e Jet funzionano solo in .NET Framework (non in .NET Core).

### <a name="supported-platforms"></a>Piattaforme supportate

EF Core 3.1 viene eseguito in .NET Core e .NET Framework, tramite l'uso di .NET Standard 2.0. Tuttavia, EF Core 5.0 non viene eseguito in .NET Framework. Per altri dettagli, vedere [Piattaforme](../core/platforms/index.md).

EF 6.4 viene eseguito in .NET Core e .NET Framework, tramite multitargeting.

## <a name="guidance-for-new-applications"></a>Linee guida per le nuove applicazioni

Usare EF Core in .NET Core per tutte le nuove applicazioni, a meno che l'app non richieda caratteristiche [supportate solo in .NET Framework](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server).

## <a name="guidance-for-existing-ef6-applications"></a>Linee guida per le applicazioni EF6 esistenti

EF Core non è una sostituzione diretta per EF6. Il passaggio da EF6 a EF Core richiederà probabilmente modifiche all'applicazione.

Per il passaggio di un'app EF6 a .NET Core:
* Continuare a usare EF6 se il codice di accesso ai dati è stabile e non è probabile che si evolva o che siano necessarie nuove funzionalità.
* Eseguire la conversione a EF Core se il codice di accesso ai dati è in evoluzione o se l'app necessita di nuove funzionalità disponibili solo in EF Core.
* La conversione a EF Core viene spesso eseguita anche per le prestazioni. Tuttavia, non tutti gli scenari sono più veloci, quindi eseguire prima di tutto una profilatura.

Per altre informazioni, vedere [Porting from EF6 to EF Core](porting/index.md) (Conversione da EF6 a EF Core).
