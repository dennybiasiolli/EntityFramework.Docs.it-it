---
title: Impostazioni del file di configurazione-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 000044c6-1d32-4cf7-ae1f-ea21d86ebf8f
ms.openlocfilehash: 86389e4a3a3bac46e2a4cf2da648a4b19e29f3c3
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78417973"
---
# <a name="configuration-file-settings"></a><span data-ttu-id="c92a0-102">Impostazioni del file di configurazione</span><span class="sxs-lookup"><span data-stu-id="c92a0-102">Configuration File Settings</span></span>
<span data-ttu-id="c92a0-103">Entity Framework consente di specificare alcune impostazioni dal file di configurazione.</span><span class="sxs-lookup"><span data-stu-id="c92a0-103">Entity Framework allows a number of settings to be specified from the configuration file.</span></span> <span data-ttu-id="c92a0-104">In generale EF segue un principio di "Convenzione sulla configurazione": tutte le impostazioni descritte in questo post hanno un comportamento predefinito, è necessario preoccuparsi solo di modificare l'impostazione quando il valore predefinito non soddisfa più i requisiti.</span><span class="sxs-lookup"><span data-stu-id="c92a0-104">In general EF follows a ‘convention over configuration’ principle: all the settings discussed in this post have a default behavior, you only need to worry about changing the setting when the default no longer satisfies your requirements.</span></span>  

## <a name="a-code-based-alternative"></a><span data-ttu-id="c92a0-105">Un'alternativa basata sul codice</span><span class="sxs-lookup"><span data-stu-id="c92a0-105">A Code-Based Alternative</span></span>  

<span data-ttu-id="c92a0-106">Tutte queste impostazioni possono essere applicate anche usando il codice.</span><span class="sxs-lookup"><span data-stu-id="c92a0-106">All of these settings can also be applied using code.</span></span> <span data-ttu-id="c92a0-107">A partire da EF6 è stata introdotta la [configurazione basata sul codice](code-based.md), che fornisce un metodo centrale per applicare la configurazione dal codice.</span><span class="sxs-lookup"><span data-stu-id="c92a0-107">Starting in EF6 we introduced [code-based configuration](code-based.md), which provides a central way of applying configuration from code.</span></span> <span data-ttu-id="c92a0-108">Prima di EF6, la configurazione può comunque essere applicata dal codice, ma è necessario usare varie API per configurare aree diverse.</span><span class="sxs-lookup"><span data-stu-id="c92a0-108">Prior to EF6, configuration can still be applied from code but you need to use various APIs to configure different areas.</span></span> <span data-ttu-id="c92a0-109">L'opzione file di configurazione consente di modificare facilmente queste impostazioni durante la distribuzione senza aggiornare il codice.</span><span class="sxs-lookup"><span data-stu-id="c92a0-109">The configuration file option allows these settings to be easily changed during deployment without updating your code.</span></span>

## <a name="the-entity-framework-configuration-section"></a><span data-ttu-id="c92a0-110">Sezione di configurazione Entity Framework</span><span class="sxs-lookup"><span data-stu-id="c92a0-110">The Entity Framework Configuration Section</span></span>  

<span data-ttu-id="c92a0-111">A partire da EF 4.1 è possibile impostare l'inizializzatore di database per un contesto usando la sezione **appSettings** del file di configurazione.</span><span class="sxs-lookup"><span data-stu-id="c92a0-111">Starting with EF4.1 you could set the database initializer for a context using the **appSettings** section of the configuration file.</span></span> <span data-ttu-id="c92a0-112">In EF 4,3 è stata introdotta la sezione **EntityFramework** personalizzata per gestire le nuove impostazioni.</span><span class="sxs-lookup"><span data-stu-id="c92a0-112">In EF 4.3 we introduced the custom **entityFramework** section to handle the new settings.</span></span> <span data-ttu-id="c92a0-113">Entity Framework rileverà comunque gli inizializzatori di database impostati utilizzando il formato precedente, ma è consigliabile procedere al nuovo formato, laddove possibile.</span><span class="sxs-lookup"><span data-stu-id="c92a0-113">Entity Framework will still recognize database initializers set using the old format, but we recommend moving to the new format where possible.</span></span>

<span data-ttu-id="c92a0-114">La sezione **EntityFramework** è stata aggiunta automaticamente al file di configurazione del progetto quando è stato installato il pacchetto NuGet EntityFramework.</span><span class="sxs-lookup"><span data-stu-id="c92a0-114">The **entityFramework** section was automatically added to the configuration file of your project when you installed the EntityFramework NuGet package.</span></span>  

``` xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <configSections>
    <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
    <section name="entityFramework"
       type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=4.3.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
  </configSections>
</configuration>
```  

## <a name="connection-strings"></a><span data-ttu-id="c92a0-115">Stringhe di connessione</span><span class="sxs-lookup"><span data-stu-id="c92a0-115">Connection Strings</span></span>  

<span data-ttu-id="c92a0-116">In [Questa pagina](~/ef6/fundamentals/configuring/connection-strings.md) vengono forniti ulteriori dettagli sul modo in cui Entity Framework determina il database da utilizzare, incluse le stringhe di connessione nel file di configurazione.</span><span class="sxs-lookup"><span data-stu-id="c92a0-116">[This page](~/ef6/fundamentals/configuring/connection-strings.md) provides more details on how Entity Framework determines the database to be used, including connection strings in the configuration file.</span></span>  

<span data-ttu-id="c92a0-117">Le stringhe di connessione vengono inserite nell'elemento standard **connectionStrings** e non richiedono la sezione **EntityFramework** .</span><span class="sxs-lookup"><span data-stu-id="c92a0-117">Connection strings go in the standard **connectionStrings** element and do not require the **entityFramework** section.</span></span>  

<span data-ttu-id="c92a0-118">I modelli basati su Code First usano le normali stringhe di connessione di ADO.NET.</span><span class="sxs-lookup"><span data-stu-id="c92a0-118">Code First based models use normal ADO.NET connection strings.</span></span> <span data-ttu-id="c92a0-119">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="c92a0-119">For example:</span></span>  

``` xml
<connectionStrings>
  <add name="BlogContext"  
        providerName="System.Data.SqlClient"  
        connectionString="Server=.\SQLEXPRESS;Database=Blogging;Integrated Security=True;"/>
</connectionStrings>
```  

<span data-ttu-id="c92a0-120">I modelli basati su EF designer usano stringhe di connessione EF speciali.</span><span class="sxs-lookup"><span data-stu-id="c92a0-120">EF Designer based models use special EF connection strings.</span></span> <span data-ttu-id="c92a0-121">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="c92a0-121">For example:</span></span>  

``` xml  
<connectionStrings>
  <add name="BlogContext"  
    connectionString=
      "metadata=
        res://*/BloggingModel.csdl|
        res://*/BloggingModel.ssdl|
        res://*/BloggingModel.msl;
      provider=System.Data.SqlClient;
      provider connection string=
        &quot;data source=(localdb)\mssqllocaldb;
        initial catalog=Blogging;
        integrated security=True;
        multipleactiveresultsets=True;&quot;"
     providerName="System.Data.EntityClient" />
</connectionStrings>
```

## <a name="code-based-configuration-type-ef6-onwards"></a><span data-ttu-id="c92a0-122">Tipo di configurazione basata su codice (EF6 e versioni successive)</span><span class="sxs-lookup"><span data-stu-id="c92a0-122">Code-Based Configuration Type (EF6 Onwards)</span></span>  

<span data-ttu-id="c92a0-123">A partire da EF6, è possibile specificare DbConfiguration per EF da usare per la [configurazione basata su codice](code-based.md) nell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="c92a0-123">Starting with EF6, you can specify the DbConfiguration for EF to use for [code-based configuration](code-based.md) in your application.</span></span> <span data-ttu-id="c92a0-124">Nella maggior parte dei casi non è necessario specificare questa impostazione perché EF individuerà automaticamente i DbConfiguration.</span><span class="sxs-lookup"><span data-stu-id="c92a0-124">In most cases you don't need to specify this setting as EF will automatically discover your DbConfiguration.</span></span> <span data-ttu-id="c92a0-125">Per informazioni dettagliate su quando potrebbe essere necessario specificare DbConfiguration nel file di configurazione, vedere la sezione relativa allo stato di **DbConfiguration** in [fase di configurazione basata su codice](code-based.md).</span><span class="sxs-lookup"><span data-stu-id="c92a0-125">For details of when you may need to specify DbConfiguration in your config file see the **Moving DbConfiguration** section of [Code-Based Configuration](code-based.md).</span></span>  

<span data-ttu-id="c92a0-126">Per impostare un tipo DbConfiguration, è necessario specificare il nome del tipo completo dell'assembly nell'elemento **codeConfigurationType** .</span><span class="sxs-lookup"><span data-stu-id="c92a0-126">To set a DbConfiguration type, you specify the assembly qualified type name in the **codeConfigurationType** element.</span></span>  

> [!NOTE]
> <span data-ttu-id="c92a0-127">Il nome completo di un assembly è il nome completo dello spazio dei nomi, seguito da una virgola, quindi dall'assembly in cui risiede il tipo.</span><span class="sxs-lookup"><span data-stu-id="c92a0-127">An assembly qualified name is the namespace qualified name, followed by a comma, then the assembly that the type resides in.</span></span> <span data-ttu-id="c92a0-128">Facoltativamente, è anche possibile specificare la versione dell'assembly, le impostazioni cultura e il token di chiave pubblica.</span><span class="sxs-lookup"><span data-stu-id="c92a0-128">You can optionally also specify the assembly version, culture and public key token.</span></span>  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyConfiguration, MyAssembly">
</entityFramework>
```  

## <a name="ef-database-providers-ef6-onwards"></a><span data-ttu-id="c92a0-129">Provider di database EF (EF6 e versioni successive)</span><span class="sxs-lookup"><span data-stu-id="c92a0-129">EF Database Providers (EF6 Onwards)</span></span>  

<span data-ttu-id="c92a0-130">Nelle versioni precedenti a EF6, le parti specifiche di un provider di database di Entity Framework devono essere incluse come parte del provider ADO.NET di base.</span><span class="sxs-lookup"><span data-stu-id="c92a0-130">Prior to EF6, Entity Framework-specific parts of a database provider had to be included as part of the core ADO.NET provider.</span></span> <span data-ttu-id="c92a0-131">A partire da EF6, le parti specifiche di EF sono ora gestite e registrate separatamente.</span><span class="sxs-lookup"><span data-stu-id="c92a0-131">Starting with EF6, the EF specific parts are now managed and registered separately.</span></span>  

<span data-ttu-id="c92a0-132">In genere non è necessario registrare manualmente i provider.</span><span class="sxs-lookup"><span data-stu-id="c92a0-132">Normally you won't need to register providers yourself.</span></span> <span data-ttu-id="c92a0-133">Questa operazione viene in genere eseguita dal provider durante l'installazione.</span><span class="sxs-lookup"><span data-stu-id="c92a0-133">This will typically be done by the provider when you install it.</span></span>  

<span data-ttu-id="c92a0-134">I provider vengono registrati includendo un elemento **provider** nella sezione figlio **provider** della sezione **EntityFramework** .</span><span class="sxs-lookup"><span data-stu-id="c92a0-134">Providers are registered by including a **provider** element under the **providers** child section of the **entityFramework** section.</span></span> <span data-ttu-id="c92a0-135">Sono disponibili due attributi obbligatori per una voce del provider:</span><span class="sxs-lookup"><span data-stu-id="c92a0-135">There are two required attributes for a provider entry:</span></span>  

- <span data-ttu-id="c92a0-136">**invariname** identifica il provider ADO.NET di base a cui è destinato questo provider EF</span><span class="sxs-lookup"><span data-stu-id="c92a0-136">**invariantName** identifies the core ADO.NET provider that this EF provider targets</span></span>  
- <span data-ttu-id="c92a0-137">**Type** è il nome del tipo completo di assembly dell'implementazione del provider EF</span><span class="sxs-lookup"><span data-stu-id="c92a0-137">**type** is the assembly qualified type name of the EF provider implementation</span></span>  

> [!NOTE]
> <span data-ttu-id="c92a0-138">Il nome completo di un assembly è il nome completo dello spazio dei nomi, seguito da una virgola, quindi dall'assembly in cui risiede il tipo.</span><span class="sxs-lookup"><span data-stu-id="c92a0-138">An assembly qualified name is the namespace qualified name, followed by a comma, then the assembly that the type resides in.</span></span> <span data-ttu-id="c92a0-139">Facoltativamente, è anche possibile specificare la versione dell'assembly, le impostazioni cultura e il token di chiave pubblica.</span><span class="sxs-lookup"><span data-stu-id="c92a0-139">You can optionally also specify the assembly version, culture and public key token.</span></span>  

<span data-ttu-id="c92a0-140">Ad esempio, ecco la voce creata per registrare il provider di SQL Server predefinito quando si installa Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="c92a0-140">As an example here is the entry created to register the default SQL Server provider when you install Entity Framework.</span></span>  

``` xml  
<providers>
  <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
</providers>
```  

## <a name="interceptors-ef61-onwards"></a><span data-ttu-id="c92a0-141">Intercettori (EF 6.1 e versioni successive)</span><span class="sxs-lookup"><span data-stu-id="c92a0-141">Interceptors (EF6.1 Onwards)</span></span>  

<span data-ttu-id="c92a0-142">A partire da EF 6.1 è possibile registrare gli intercettori nel file di configurazione.</span><span class="sxs-lookup"><span data-stu-id="c92a0-142">Starting with EF6.1 you can register interceptors in the configuration file.</span></span> <span data-ttu-id="c92a0-143">Gli intercettori consentono di eseguire logica aggiuntiva quando EF esegue determinate operazioni, ad esempio l'esecuzione di query sul database, l'apertura di connessioni e così via.</span><span class="sxs-lookup"><span data-stu-id="c92a0-143">Interceptors allow you to run additional logic when EF performs certain operations, such as executing database queries, opening connections, etc.</span></span>  

<span data-ttu-id="c92a0-144">Gli intercettori vengono registrati includendo un elemento **Interceptor** nella sezione figlio **Interceptor** della sezione **EntityFramework** .</span><span class="sxs-lookup"><span data-stu-id="c92a0-144">Interceptors are registered by including an **interceptor** element under the **interceptors** child section of the **entityFramework** section.</span></span> <span data-ttu-id="c92a0-145">La configurazione seguente, ad esempio, registra l'intercettore **DatabaseLogger** incorporato che registrerà tutte le operazioni di database nella console.</span><span class="sxs-lookup"><span data-stu-id="c92a0-145">For example, the following configuration registers the built-in **DatabaseLogger** interceptor that will log all database operations to the Console.</span></span>  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework"/>
</interceptors>
```  

### <a name="logging-database-operations-to-a-file-ef61-onwards"></a><span data-ttu-id="c92a0-146">Registrazione delle operazioni di database in un file (EF 6.1 e versioni successive)</span><span class="sxs-lookup"><span data-stu-id="c92a0-146">Logging Database Operations to a File (EF6.1 Onwards)</span></span>  

<span data-ttu-id="c92a0-147">La registrazione degli intercettori tramite il file di configurazione è particolarmente utile quando si desidera aggiungere la registrazione a un'applicazione esistente per facilitare il debug di un problema.</span><span class="sxs-lookup"><span data-stu-id="c92a0-147">Registering interceptors via the config file is especially useful when you want to add logging to an existing application to help debug an issue.</span></span> <span data-ttu-id="c92a0-148">**DatabaseLogger** supporta la registrazione in un file fornendo il nome del file come parametro del costruttore.</span><span class="sxs-lookup"><span data-stu-id="c92a0-148">**DatabaseLogger** supports logging to a file by supplying the file name as a constructor parameter.</span></span>  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
    <parameters>
      <parameter value="C:\Temp\LogOutput.txt"/>
    </parameters>
  </interceptor>
</interceptors>
```  

<span data-ttu-id="c92a0-149">Per impostazione predefinita, il file di log verrà sovrascritto con un nuovo file ogni volta che l'app viene avviata.</span><span class="sxs-lookup"><span data-stu-id="c92a0-149">By default this will cause the log file to be overwritten with a new file each time the app starts.</span></span> <span data-ttu-id="c92a0-150">Per aggiungere invece al file di log se esiste già, usare un valore simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="c92a0-150">To instead append to the log file if it already exists use something like:</span></span>  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
    <parameters>
      <parameter value="C:\Temp\LogOutput.txt"/>
      <parameter value="true" type="System.Boolean"/>
    </parameters>
  </interceptor>
</interceptors>
```  

<span data-ttu-id="c92a0-151">Per altre informazioni su **DatabaseLogger** e sulla registrazione degli intercettori, vedere il post di blog [Ef 6,1: attivazione della registrazione senza ricompilazione](https://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/).</span><span class="sxs-lookup"><span data-stu-id="c92a0-151">For additional information on **DatabaseLogger** and registering interceptors, see the blog post [EF 6.1: Turning on logging without recompiling](https://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/).</span></span>  

## <a name="code-first-default-connection-factory"></a><span data-ttu-id="c92a0-152">Code First Factory di connessione predefinita</span><span class="sxs-lookup"><span data-stu-id="c92a0-152">Code First Default Connection Factory</span></span>  

<span data-ttu-id="c92a0-153">La sezione di configurazione consente di specificare una factory di connessione predefinita che Code First deve usare per individuare un database da usare per un contesto.</span><span class="sxs-lookup"><span data-stu-id="c92a0-153">The configuration section allows you to specify a default connection factory that Code First should use to locate a database to use for a context.</span></span> <span data-ttu-id="c92a0-154">La factory di connessione predefinita viene utilizzata solo quando non è stata aggiunta alcuna stringa di connessione al file di configurazione per un contesto.</span><span class="sxs-lookup"><span data-stu-id="c92a0-154">The default connection factory is only used when no connection string has been added to the configuration file for a context.</span></span>  

<span data-ttu-id="c92a0-155">Quando è stato installato il pacchetto NuGet EF è stata registrata una factory di connessione predefinita che punta a SQL Express o al database locale, a seconda di quale installato.</span><span class="sxs-lookup"><span data-stu-id="c92a0-155">When you installed the EF NuGet package a default connection factory was registered that points to either SQL Express or LocalDB, depending on which one you have installed.</span></span>  

<span data-ttu-id="c92a0-156">Per impostare una factory di connessione, specificare il nome del tipo completo dell'assembly nell'elemento **defaultConnectionFactory** .</span><span class="sxs-lookup"><span data-stu-id="c92a0-156">To set a connection factory, you specify the assembly qualified type name in the **defaultConnectionFactory** element.</span></span>  

> [!NOTE]
> <span data-ttu-id="c92a0-157">Il nome completo di un assembly è il nome completo dello spazio dei nomi, seguito da una virgola, quindi dall'assembly in cui risiede il tipo.</span><span class="sxs-lookup"><span data-stu-id="c92a0-157">An assembly qualified name is the namespace qualified name, followed by a comma, then the assembly that the type resides in.</span></span> <span data-ttu-id="c92a0-158">Facoltativamente, è anche possibile specificare la versione dell'assembly, le impostazioni cultura e il token di chiave pubblica.</span><span class="sxs-lookup"><span data-stu-id="c92a0-158">You can optionally also specify the assembly version, culture and public key token.</span></span>  

<span data-ttu-id="c92a0-159">Di seguito è riportato un esempio di impostazione della factory di connessione predefinita:</span><span class="sxs-lookup"><span data-stu-id="c92a0-159">Here is an example of setting your own default connection factory:</span></span>  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="MyNamespace.MyCustomFactory, MyAssembly"/>
</entityFramework>
```  

<span data-ttu-id="c92a0-160">L'esempio precedente richiede che la factory personalizzata disponga di un costruttore senza parametri.</span><span class="sxs-lookup"><span data-stu-id="c92a0-160">The above example requires the custom factory to have a parameterless constructor.</span></span> <span data-ttu-id="c92a0-161">Se necessario, è possibile specificare i parametri del costruttore usando l'elemento **Parameters** .</span><span class="sxs-lookup"><span data-stu-id="c92a0-161">If needed, you can specify constructor parameters using the **parameters** element.</span></span>  

<span data-ttu-id="c92a0-162">Ad esempio, SqlCeConnectionFactory, incluso in Entity Framework, richiede di specificare un nome invariante del provider per il costruttore.</span><span class="sxs-lookup"><span data-stu-id="c92a0-162">For example, the SqlCeConnectionFactory, that is included in Entity Framework, requires you to supply a provider invariant name to the constructor.</span></span> <span data-ttu-id="c92a0-163">Il nome invariante del provider identifica la versione di SQL Compact che si desidera utilizzare.</span><span class="sxs-lookup"><span data-stu-id="c92a0-163">The provider invariant name identifies the version of SQL Compact you want to use.</span></span> <span data-ttu-id="c92a0-164">Con la seguente configurazione, i contesti utilizzeranno SQL Compact versione 4,0 per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="c92a0-164">The following configuration will cause contexts to use SQL Compact version 4.0 by default.</span></span>  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlCeConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="System.Data.SqlServerCe.4.0" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

<span data-ttu-id="c92a0-165">Se non si imposta una factory di connessione predefinita, Code First USA SqlConnectionFactory, puntando a `.\SQLEXPRESS`.</span><span class="sxs-lookup"><span data-stu-id="c92a0-165">If you don’t set a default connection factory, Code First uses the SqlConnectionFactory, pointing to `.\SQLEXPRESS`.</span></span> <span data-ttu-id="c92a0-166">SqlConnectionFactory dispone inoltre di un costruttore che consente di eseguire l'override di parti della stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="c92a0-166">SqlConnectionFactory also has a constructor that allows you to override parts of the connection string.</span></span> <span data-ttu-id="c92a0-167">Se si desidera utilizzare un'istanza di SQL Server diversa da `.\SQLEXPRESS` è possibile utilizzare questo costruttore per impostare il server.</span><span class="sxs-lookup"><span data-stu-id="c92a0-167">If you want to use a SQL Server instance other than `.\SQLEXPRESS` you can use this constructor to set the server.</span></span>  

<span data-ttu-id="c92a0-168">La Code First configurazione seguente provocherà l'uso di **ServerDatabase** per i contesti che non dispongono di una stringa di connessione esplicita impostata.</span><span class="sxs-lookup"><span data-stu-id="c92a0-168">The following configuration will cause Code First to use **MyDatabaseServer** for contexts that don’t have an explicit connection string set.</span></span>  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="Data Source=MyDatabaseServer; Integrated Security=True; MultipleActiveResultSets=True" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

<span data-ttu-id="c92a0-169">Per impostazione predefinita, si presuppone che gli argomenti del costruttore siano di tipo String.</span><span class="sxs-lookup"><span data-stu-id="c92a0-169">By default, it’s assumed that constructor arguments are of type string.</span></span> <span data-ttu-id="c92a0-170">Per modificare questa operazione, è possibile usare l'attributo Type.</span><span class="sxs-lookup"><span data-stu-id="c92a0-170">You can use the type attribute to change this.</span></span>  

``` xml
<parameter value="2" type="System.Int32" />
```  

## <a name="database-initializers"></a><span data-ttu-id="c92a0-171">Inizializzatori di database</span><span class="sxs-lookup"><span data-stu-id="c92a0-171">Database Initializers</span></span>  

<span data-ttu-id="c92a0-172">Gli inizializzatori di database sono configurati in base al contesto.</span><span class="sxs-lookup"><span data-stu-id="c92a0-172">Database initializers are configured on a per-context basis.</span></span> <span data-ttu-id="c92a0-173">Possono essere impostate nel file di configurazione usando l'elemento **context** .</span><span class="sxs-lookup"><span data-stu-id="c92a0-173">They can be set in the configuration file using the **context** element.</span></span> <span data-ttu-id="c92a0-174">Questo elemento usa il nome completo dell'assembly per identificare il contesto da configurare.</span><span class="sxs-lookup"><span data-stu-id="c92a0-174">This element uses the assembly qualified name to identify the context being configured.</span></span>  

<span data-ttu-id="c92a0-175">Per impostazione predefinita, i contesti di Code First sono configurati per l'utilizzo dell'inizializzatore CreateDatabaseIfNotExists.</span><span class="sxs-lookup"><span data-stu-id="c92a0-175">By default, Code First contexts are configured to use the CreateDatabaseIfNotExists initializer.</span></span> <span data-ttu-id="c92a0-176">Nell'elemento **context** è disponibile un attributo **disableDatabaseInitialization** che può essere usato per disabilitare l'inizializzazione del database.</span><span class="sxs-lookup"><span data-stu-id="c92a0-176">There is a **disableDatabaseInitialization** attribute on the **context** element that can be used to disable database initialization.</span></span>  

<span data-ttu-id="c92a0-177">La configurazione seguente, ad esempio, Disabilita l'inizializzazione del database per il contesto blogging. BlogContext definito in MyAssembly. dll.</span><span class="sxs-lookup"><span data-stu-id="c92a0-177">For example, the following configuration disables database initialization for the Blogging.BlogContext context defined in MyAssembly.dll.</span></span>  

``` xml  
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly" disableDatabaseInitialization="true" />
</contexts>
```  

<span data-ttu-id="c92a0-178">Per impostare un inizializzatore personalizzato, è possibile usare l'elemento **databaseInitializer** .</span><span class="sxs-lookup"><span data-stu-id="c92a0-178">You can use the **databaseInitializer** element to set a custom initializer.</span></span>  

``` xml
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="Blogging.MyCustomBlogInitializer, MyAssembly" />
  </context>
</contexts>
```  

<span data-ttu-id="c92a0-179">I parametri del costruttore utilizzano la stessa sintassi delle factory di connessione predefinite.</span><span class="sxs-lookup"><span data-stu-id="c92a0-179">Constructor parameters use the same syntax as default connection factories.</span></span>  

``` xml  
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="Blogging.MyCustomBlogInitializer, MyAssembly">
      <parameters>
        <parameter value="MyConstructorParameter" />
      </parameters>
    </databaseInitializer>
  </context>
</contexts>
```  

<span data-ttu-id="c92a0-180">È possibile configurare uno degli inizializzatori di database generici inclusi in Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="c92a0-180">You can configure one of the generic database initializers that are included in Entity Framework.</span></span> <span data-ttu-id="c92a0-181">L'attributo **Type** usa il formato .NET Framework per i tipi generici.</span><span class="sxs-lookup"><span data-stu-id="c92a0-181">The **type** attribute uses the .NET Framework format for generic types.</span></span>  

<span data-ttu-id="c92a0-182">Se ad esempio si utilizza Migrazioni Code First, è possibile configurare il database di cui eseguire la migrazione automaticamente utilizzando l'inizializzatore di `MigrateDatabaseToLatestVersion<TContext, TMigrationsConfiguration>`.</span><span class="sxs-lookup"><span data-stu-id="c92a0-182">For example, if you are using Code First Migrations, you can configure the database to be migrated automatically using the `MigrateDatabaseToLatestVersion<TContext, TMigrationsConfiguration>` initializer.</span></span>  

``` xml
<contexts>
  <context type="Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="System.Data.Entity.MigrateDatabaseToLatestVersion`2[[Blogging.BlogContext, MyAssembly], [Blogging.Migrations.Configuration, MyAssembly]], EntityFramework" />
  </context>
</contexts>
```
