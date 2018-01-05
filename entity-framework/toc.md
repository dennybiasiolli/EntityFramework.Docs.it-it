# [Entity Framework](index.md)

## [Confronto tra EF Core e EF6](efcore-and-ef6/index.md)

### [L'opzione più adatta alle proprie esigenze](efcore-and-ef6/choosing.md)
### [Confronto tra funzionalità](efcore-and-ef6/features.md)
### [EF6 ed EF Core nella stessa applicazione](efcore-and-ef6/side-by-side.md)
### [Conversione da EF6 a EF Core](efcore-and-ef6/porting/index.md)
#### [Requisiti convalida](efcore-and-ef6/porting/ensure-requirements.md)
#### [Conversione di un modello basato su EDMX](efcore-and-ef6/porting/port-edmx.md)
#### [Conversione di un modello basato su codice](efcore-and-ef6/porting/port-code.md)

## [Entity Framework Core](core/index.md)

### [Novità di EF Core 2.0](core/what-is-new/index.md)
#### [EF Core 1.0 (versione precedente)](core/what-is-new/ef-core-1.0.md)
#### [EF Core 1.1 (versione precedente)](core/what-is-new/ef-core-1.1.md)

### [Introduzione](core/get-started/index.md)
#### [Installazione di EF Core](core/get-started/install/index.md)
#### [.NET Framework (Console, Windows Form, WPF e così via)](core/get-started/full-dotnet/index.md)
##### [.NET framework - Nuovo database](core/get-started/full-dotnet/new-db.md)
##### [.NET framework - Database esistente](core/get-started/full-dotnet/existing-db.md)
#### [.NET Core (Windows, OSX, Linux e così via)](core/get-started/netcore/index.md)
##### [.NET Core - Nuovo database](core/get-started/netcore/new-db-sqlite.md)
#### [ASP.NET Core](core/get-started/aspnetcore/index.md)
##### [ASP.NET Core - Nuovo database](core/get-started/aspnetcore/new-db.md)
##### [ASP.NET Core - Database esistente](core/get-started/aspnetcore/existing-db.md)
##### [Esercitazione di EF Core sul sito di ASP.NET Core](https://docs.asp.net/en/latest/data/ef-mvc/intro.html)
#### [Piattaforma UWP (Universal Windows Platform)](core/get-started/uwp/index.md)
##### [UWP - Nuovo database](core/get-started/uwp/getting-started.md)

### [Creazione di un modello](core/modeling/index.md)
#### [Inclusione ed esclusione di tipi](core/modeling/included-types.md)
#### [Inclusione ed esclusione di proprietà](core/modeling/included-properties.md)
#### [Chiavi (primarie)](core/modeling/keys.md)
#### [Valori generati](core/modeling/generated-properties.md)
#### [Proprietà obbligatorie o facoltative](core/modeling/required-optional.md)
#### [Lunghezza massima](core/modeling/max-length.md)
#### [Token di concorrenza](core/modeling/concurrency.md)
#### [Nascondere le proprietà](core/modeling/shadow-properties.md)
#### [Relazioni](core/modeling/relationships.md)
#### [Indici](core/modeling/indexes.md)
#### [Chiavi alternative](core/modeling/alternate-keys.md)
#### [Ereditarietà](core/modeling/inheritance.md)
#### [Campi sottostanti](core/modeling/backing-field.md)
#### [Alternanza di modelli con lo stesso DbContext](core/modeling/dynamic-model.md)
#### [Modellazione di database relazionali](core/modeling/relational/index.md)
##### [Mapping tabella](core/modeling/relational/tables.md)
##### [Mapping colonne](core/modeling/relational/columns.md)
##### [Tipi di dati](core/modeling/relational/data-types.md)
##### [Chiavi primarie](core/modeling/relational/primary-keys.md)
##### [Schema predefinito](core/modeling/relational/default-schema.md)
##### [Colonne calcolate](core/modeling/relational/computed-columns.md)
##### [Sequenze](core/modeling/relational/sequences.md)
##### [Valori predefiniti](core/modeling/relational/default-values.md)
##### [Indici](core/modeling/relational/indexes.md)
##### [Foreign Key Constraints](core/modeling/relational/fk-constraints.md)
##### [Chiavi alternative (vincoli univoci)](core/modeling/relational/unique-constraints.md)
##### [Ereditarietà (database relazionale)](core/modeling/relational/inheritance.md)

### [Esecuzione di query su dati](core/querying/index.md)
#### [Query di base](core/querying/basic.md)
#### [Caricamento di dati correlati](core/querying/related-data.md)
#### [Valutazione client rispetto a server](core/querying/client-eval.md)
#### [Esecuzione e non esecuzione di verifiche](core/querying/tracking.md)
#### [Query SQL non elaborate](core/querying/raw-sql.md)
#### [Query asincrone](core/querying/async.md)
#### [Funzionamento della query](core/querying/overview.md)
#### [Filtri di query globali](core/querying/filters.md)

### [Salvataggio di dati](core/saving/index.md)
#### [Salvataggio di base](core/saving/basic.md)
#### [Dati correlati](core/saving/related-data.md)
#### [Eliminazione a catena](core/saving/cascade-delete.md)
#### [Conflitti di concorrenza](core/saving/concurrency.md)
#### [Transazioni](core/saving/transactions.md)
#### [Salvataggio asincrono](core/saving/async.md)
#### [Entità disconnesse](core/saving/disconnected-entities.md)
#### [Valori espliciti per le proprietà generate](core/saving/explicit-values-generated-properties.md)

### [Implementazioni di .NET supportate](core/platforms/index.md)

### [Provider di database](core/providers/index.md)
#### [Microsoft SQL Server](core/providers/sql-server/index.md)
##### [Tabelle con ottimizzazione per la memoria](core/providers/sql-server/memory-optimized-tables.md)
#### [SQLite](core/providers/sqlite/index.md)
##### [Limitazioni di SQLite](core/providers/sqlite/limitations.md)
#### [PostgreSQL (Npgsql)](core/providers/npgsql/index.md)
#### [IBM Data Server (DB2)](core/providers/ibm/index.md)
#### [MySQL (ufficiale)](core/providers/mysql/index.md)
#### [MySQL (Pomelo)](core/providers/pomelo/index.md)
#### [Microsoft SQL Server Compact Edition](core/providers/sql-compact/index.md)
#### [InMemory (per i test)](core/providers/in-memory/index.md)
#### [Devart (MySQL, Oracle, PostgreSQL, SQLite, DB2 e altri)](core/providers/devart/index.md)
#### [Oracle (non ancora disponibile)](core/providers/oracle/index.md)
#### [MyCat](core/providers/my-cat/index.md)
#### [Firebird-Community](core/providers/firebird-community/index.md)
#### [Scrittura di un provider di database](core/providers/writing-a-provider.md)

### [Gestione di schemi di database](core/managing-schemas/index.md)
#### [Migrazioni](core/managing-schemas/migrations/index.md)
##### [Ambienti di team](core/managing-schemas/migrations/teams.md)
##### [Operazioni personalizzate](core/managing-schemas/migrations/operations.md)
##### [Uso di un progetto separato](core/managing-schemas/migrations/projects.md)
##### [Più provider](core/managing-schemas/migrations/providers.md)
##### [Tabella di cronologia personalizzata](core/managing-schemas/migrations/history-table.md)
#### [🔧 API di creazione ed eliminazione](core/managing-schemas/ensure-created.md)
#### [🔧 Reverse Engineering](core/managing-schemas/scaffolding.md)

### [Riferimenti alla riga di comando](core/miscellaneous/cli/index.md)
#### [Console di Gestione pacchetti (Visual Studio)](core/miscellaneous/cli/powershell.md)
#### [Interfaccia della riga di comando di .NET Core](core/miscellaneous/cli/dotnet.md)
#### [Creazione DbContext in fase di progettazione](core/miscellaneous/cli/dbcontext-creation.md)
#### [Servizi in fase di progettazione](core/miscellaneous/cli/services.md)

### [Strumenti ed estensioni](core/extensions/index.md)
#### [LLBLGen Pro](core/extensions/llbl-gen-pro.md)
#### [Devart Entity Developer](core/extensions/devart-entity-developer.md)
#### [EFSecondLevelCache.Core](core/extensions/efsecondlevelcache-core.md)
#### [Geco (generatore modelli di reverse engineering)](core/extensions/geco.md)
#### [EntityFrameworkCore.Detached](core/extensions/entityframeworkcore-detached.md)
#### [EntityFrameworkCore.Triggers](core/extensions/entityframeworkcore-triggers.md)
#### [EntityFrameworkCore.Rx](core/extensions/entityframeworkcore-rx.md)
#### [EntityFrameworkCore.PrimaryKey](core/extensions/entityframeworkcore-primarykey.md)
#### [EntityFrameworkCore.TypedOriginalValues](core/extensions/entityframeworkcore-typedoriginalvalues.md)
#### [EFCore.Practices](core/extensions/efcore-practices.md)
#### [LinqKit.Microsoft.EntityFrameworkCore](core/extensions/linqkit.md)
#### [Microsoft.EntityFrameworkCore.AutoHistory](core/extensions/autohistory.md)
#### [Microsoft.EntityFrameworkCore.DynamicLinq](core/extensions/dynamiclinq.md)
#### [Microsoft.EntityFrameworkCore.UnitOfWork](core/extensions/unitofwork.md)

### Varie
#### [Stringhe di connessione](core/miscellaneous/connection-strings.md)
#### [Registrazione](core/miscellaneous/logging.md)
#### [Resilienza della connessione](core/miscellaneous/connection-resiliency.md)
#### [Test](core/miscellaneous/testing/index.md)
##### [Test con SQLite](core/miscellaneous/testing/sqlite.md)
##### [Test con InMemory](core/miscellaneous/testing/in-memory.md)
#### [Configurazione di un DbContext](core/miscellaneous/configuring-dbcontext.md)
#### [Aggiornamento da 1.0 RC1 a RC2](core/miscellaneous/rc1-rc2-upgrade.md)
#### [Aggiornamento da 1.0 RC2 a RTM](core/miscellaneous/rc2-rtm-upgrade.md)
#### [Aggiornamento a EF Core 2.0](core/miscellaneous/1x-2x-upgrade.md)

### [⤤ Riferimento API](https://docs.microsoft.com/dotnet/api/?view=efcore-2.0)

## [Entity Framework 6](ef6/index.md)
### [⤤ Documentazione](http://msdn.com/data/ef)
### [⤤ Riferimento API](https://msdn.microsoft.com/library/dn223258.aspx)