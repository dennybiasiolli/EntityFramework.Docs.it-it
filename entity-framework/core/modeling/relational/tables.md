---
title: Mapping tabella-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c807aa4c-7845-443d-b8d0-bfc9b42691a3
uid: core/modeling/relational/tables
ms.openlocfilehash: 474c49aca4c65cd5d58b184b1f3c2d30e7abff84
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73656096"
---
# <a name="table-mapping"></a><span data-ttu-id="d7f69-102">Mapping di tabella</span><span class="sxs-lookup"><span data-stu-id="d7f69-102">Table Mapping</span></span>

> [!NOTE]  
> <span data-ttu-id="d7f69-103">La configurazione di questa sezione è applicabile in generale ai database relazionali.</span><span class="sxs-lookup"><span data-stu-id="d7f69-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="d7f69-104">I metodi di estensione descritti diventano disponibili quando si installa un provider di database relazionali (a causa del pacchetto *Microsoft.EntityFrameworkCore.Relational* condiviso).</span><span class="sxs-lookup"><span data-stu-id="d7f69-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="d7f69-105">Il mapping delle tabelle identifica i dati della tabella da cui devono essere eseguite query e salvati nel database.</span><span class="sxs-lookup"><span data-stu-id="d7f69-105">Table mapping identifies which table data should be queried from and saved to in the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="d7f69-106">Convenzioni</span><span class="sxs-lookup"><span data-stu-id="d7f69-106">Conventions</span></span>

<span data-ttu-id="d7f69-107">Per convenzione, ogni entità viene configurata in modo da eseguire il mapping a una tabella con lo stesso nome della proprietà `DbSet<TEntity>` che espone l'entità nel contesto derivato.</span><span class="sxs-lookup"><span data-stu-id="d7f69-107">By convention, each entity will be set up to map to a table with the same name as the `DbSet<TEntity>` property that exposes the entity on the derived context.</span></span> <span data-ttu-id="d7f69-108">Se non è incluso alcun `DbSet<TEntity>` per l'entità specificata, viene usato il nome della classe.</span><span class="sxs-lookup"><span data-stu-id="d7f69-108">If no `DbSet<TEntity>` is included for the given entity, the class name is used.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="d7f69-109">Annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="d7f69-109">Data Annotations</span></span>

<span data-ttu-id="d7f69-110">È possibile utilizzare le annotazioni dei dati per configurare la tabella a cui viene eseguito il mapping di un tipo.</span><span class="sxs-lookup"><span data-stu-id="d7f69-110">You can use Data Annotations to configure the table that a type maps to.</span></span>

``` csharp
using System.ComponentModel.DataAnnotations.Schema;

[Table("blogs")]
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

<span data-ttu-id="d7f69-111">È inoltre possibile specificare uno schema a cui appartiene la tabella.</span><span class="sxs-lookup"><span data-stu-id="d7f69-111">You can also specify a schema that the table belongs to.</span></span>

``` csharp
[Table("blogs", Schema = "blogging")]
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

## <a name="fluent-api"></a><span data-ttu-id="d7f69-112">API Fluent</span><span class="sxs-lookup"><span data-stu-id="d7f69-112">Fluent API</span></span>

<span data-ttu-id="d7f69-113">È possibile usare l'API Fluent per configurare la tabella a cui viene eseguito il mapping di un tipo.</span><span class="sxs-lookup"><span data-stu-id="d7f69-113">You can use the Fluent API to configure the table that a type maps to.</span></span>

``` csharp
using Microsoft.EntityFrameworkCore;

class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .ToTable("blogs");
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

<span data-ttu-id="d7f69-114">È inoltre possibile specificare uno schema a cui appartiene la tabella.</span><span class="sxs-lookup"><span data-stu-id="d7f69-114">You can also specify a schema that the table belongs to.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/TableAndSchema.cs?name=Table&highlight=2)]
