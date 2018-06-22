---
title: Provider di database Microsoft SQL Server - EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 2e007c82-c6e4-45bb-8129-851b79ec1a0a
ms.technology: entity-framework-core
uid: core/providers/sql-server/index
ms.openlocfilehash: 2ed7c0dd127db03d5e7340fde1ef83cf01b30135
ms.sourcegitcommit: b2d94cebdc32edad4fecb07e53fece66437d1b04
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
ms.locfileid: "29678650"
---
# <a name="microsoft-sql-server-ef-core-database-provider"></a><span data-ttu-id="00755-102">Provider di database Microsoft SQL Server per EF Core</span><span class="sxs-lookup"><span data-stu-id="00755-102">Microsoft SQL Server EF Core Database Provider</span></span>

<span data-ttu-id="00755-103">Questo provider di database consente l'uso di Entity Framework Core con Microsoft SQL Server (incluso SQL Azure).</span><span class="sxs-lookup"><span data-stu-id="00755-103">This database provider allows Entity Framework Core to be used with Microsoft SQL Server (including SQL Azure).</span></span> <span data-ttu-id="00755-104">Il provider viene gestito nell'ambito del [progetto Entity Framework Core](https://github.com/aspnet/EntityFrameworkCore).</span><span class="sxs-lookup"><span data-stu-id="00755-104">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="00755-105">Installazione di</span><span class="sxs-lookup"><span data-stu-id="00755-105">Install</span></span>

<span data-ttu-id="00755-106">Installare il [pacchetto NuGet Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="00755-106">Install the [Microsoft.EntityFrameworkCore.SqlServer NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="get-started"></a><span data-ttu-id="00755-107">Introduzione</span><span class="sxs-lookup"><span data-stu-id="00755-107">Get Started</span></span>

<span data-ttu-id="00755-108">Per acquisire familiarità con il provider, usare le risorse seguenti.</span><span class="sxs-lookup"><span data-stu-id="00755-108">The following resources will help you get started with this provider.</span></span>
* <span data-ttu-id="00755-109">[Getting Started on .NET Framework (Console, WinForms, WPF, etc.)](../../get-started/full-dotnet/index.md) (Introduzione a .NET Framework - Console, WinForms, WPF e così via)</span><span class="sxs-lookup"><span data-stu-id="00755-109">[Getting Started on .NET Framework (Console, WinForms, WPF, etc.)](../../get-started/full-dotnet/index.md)</span></span>

* <span data-ttu-id="00755-110">[Getting Started on ASP.NET Core](../../get-started/aspnetcore/index.md) (Introduzione ad ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="00755-110">[Getting Started on ASP.NET Core](../../get-started/aspnetcore/index.md)</span></span>

* <span data-ttu-id="00755-111">[UnicornStore Sample Application](https://github.com/rowanmiller/UnicornStore/tree/master/UnicornStore) (Applicazione di esempio UnicornStore)</span><span class="sxs-lookup"><span data-stu-id="00755-111">[UnicornStore Sample Application](https://github.com/rowanmiller/UnicornStore/tree/master/UnicornStore)</span></span>

## <a name="supported-database-engines"></a><span data-ttu-id="00755-112">Motori di database supportati</span><span class="sxs-lookup"><span data-stu-id="00755-112">Supported Database Engines</span></span>

* <span data-ttu-id="00755-113">Microsoft SQL Server (2008 e versioni successive)</span><span class="sxs-lookup"><span data-stu-id="00755-113">Microsoft SQL Server (2008 onwards)</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="00755-114">Piattaforme supportate</span><span class="sxs-lookup"><span data-stu-id="00755-114">Supported Platforms</span></span>

* <span data-ttu-id="00755-115">.NET Framework (4.5.1 e versioni successive)</span><span class="sxs-lookup"><span data-stu-id="00755-115">.NET Framework (4.5.1 onwards)</span></span>

* <span data-ttu-id="00755-116">.NET Core</span><span class="sxs-lookup"><span data-stu-id="00755-116">.NET Core</span></span>

* <span data-ttu-id="00755-117">Mono (4.2.0 e versioni successive)</span><span class="sxs-lookup"><span data-stu-id="00755-117">Mono (4.2.0 onwards)</span></span>

      Caution: Using this provider on Mono will make use of the Mono SQL Client implementation, which has a number of known issues. For example, it does not support secure connections (SSL).
