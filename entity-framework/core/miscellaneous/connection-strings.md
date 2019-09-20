---
title: Stringhe di connessione-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: aeb0f5f8-b212-4f89-ae83-c642a5190ba0
uid: core/miscellaneous/connection-strings
ms.openlocfilehash: ed89d6d09b15b0dea7fd8bc3ff3e3f631495ecb7
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71149112"
---
# <a name="connection-strings"></a>Stringhe di connessione

La maggior parte dei provider di database richiede una forma di stringa di connessione per la connessione al database. In alcuni casi la stringa di connessione contiene informazioni riservate che devono essere protette. Potrebbe anche essere necessario modificare la stringa di connessione mentre si sposta l'applicazione tra ambienti, ad esempio sviluppo, test e produzione.

## <a name="winforms--wpf-applications"></a>WinForms & applicazioni WPF

Le applicazioni WinForms, WPF e ASP.NET 4 hanno un modello di stringa di connessione provato e testato. La stringa di connessione deve essere aggiunta al file app. config dell'applicazione (Web. config se si usa ASP.NET). Se la stringa di connessione contiene informazioni riservate, ad esempio nome utente e password, è possibile proteggere il contenuto del file di configurazione utilizzando lo [strumento Gestione segreta](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager).

``` xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>

  <connectionStrings>
    <add name="BloggingDatabase"
         connectionString="Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" />
  </connectionStrings>
</configuration>
```

> [!TIP]  
> L' `providerName` impostazione non è necessaria per EF Core stringhe di connessione archiviate in app. config perché il provider di database è configurato tramite codice.

È quindi possibile leggere la stringa di connessione usando `ConfigurationManager` l'API nel `OnConfiguring` metodo del contesto. Per poter usare questa API, potrebbe essere necessario `System.Configuration` aggiungere un riferimento all'assembly del Framework.

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
      optionsBuilder.UseSqlServer(ConfigurationManager.ConnectionStrings["BloggingDatabase"].ConnectionString);
    }
}
```

## <a name="universal-windows-platform-uwp"></a>Piattaforma UWP (Universal Windows Platform)

Le stringhe di connessione in un'applicazione UWP sono in genere una connessione SQLite che specifica solo un nome file locale. In genere non contengono informazioni riservate e non devono essere modificate durante la distribuzione di un'applicazione. Di conseguenza, queste stringhe di connessione sono in genere più belle da rimanere nel codice, come illustrato di seguito. Se si vuole spostarli fuori dal codice, UWP supporta il concetto di impostazioni. per informazioni dettagliate, vedere la [sezione Impostazioni app della documentazione di UWP](https://docs.microsoft.com/windows/uwp/app-settings/store-and-retrieve-app-data) .

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
            optionsBuilder.UseSqlite("Data Source=blogging.db");
    }
}
```

## <a name="aspnet-core"></a>ASP.NET Core

In ASP.NET Core il sistema di configurazione è molto flessibile e la stringa di connessione può essere archiviata in `appsettings.json`, una variabile di ambiente, l'archivio del segreto utente o un'altra origine della configurazione. Per ulteriori informazioni, vedere la sezione relativa alla [configurazione della documentazione di ASP.NET Core](https://docs.asp.net/en/latest/fundamentals/configuration.html) . Nell'esempio seguente viene illustrata la stringa di `appsettings.json`connessione archiviata in.

``` json
{
  "ConnectionStrings": {
    "BloggingDatabase": "Server=(localdb)\\mssqllocaldb;Database=EFGetStarted.ConsoleApp.NewDb;Trusted_Connection=True;"
  },
}
```

Il contesto viene in genere configurato `Startup.cs` in con la stringa di connessione letta dalla configurazione. Si noti `GetConnectionString()` che il metodo cerca un valore di configurazione la `ConnectionStrings:<connection string name>`cui chiave è. È necessario importare lo spazio dei nomi [Microsoft. Extensions. Configuration](https://docs.microsoft.com/dotnet/api/microsoft.extensions.configuration) per usare questo metodo di estensione.

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("BloggingDatabase")));
}
```
