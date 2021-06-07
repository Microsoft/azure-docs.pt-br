---
title: Tutorial para uso de sinalizadores de recurso em um aplicativo .NET Core | Microsoft Docs
description: Neste tutorial, você aprenderá a implementar sinalizadores de recursos em aplicativos .NET Core.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/17/2020
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 4d54e1ff07b250b5595d2f8aee5f022bd2359721
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729500"
---
# <a name="tutorial-use-feature-flags-in-an-aspnet-core-app"></a>Tutorial: Usar sinalizadores de recursos em um aplicativo ASP.NET Core

As bibliotecas de Gerenciamento de Recursos do .NET Core fornecem suporte idiomático para implementação de sinalizadores de recursos em um aplicativo .NET ou ASP.NET Core. Essas bibliotecas permitem que você adicione sinalizadores de recursos ao código de maneira declarativa, de modo que não precise escrever código para habilitar ou desabilitar recursos com instruções `if`.

As bibliotecas do Gerenciamento de Recursos também gerenciam os ciclos de vida dos sinalizadores de recursos nos bastidores. Por exemplo, as bibliotecas atualizam e armazenam em cache os estados do sinalizador ou garantem um estado de sinalizador imutável durante uma chamada de solicitação. Além disso, a biblioteca ASP.NET Core oferece integrações prontas para uso, incluindo ações do controlador MVC, exibições, rotas e middleware.

O [Início Rápido Adicionar sinalizadores de recurso a um aplicativo ASP.NET Core](./quickstart-feature-flag-aspnet-core.md) mostra um exemplo simples de como usar sinalizadores de recurso em um aplicativo ASP.NET Core. Este tutorial mostra opções de configuração adicionais e os recursos das bibliotecas de Gerenciamento de Recursos. Você pode usar o aplicativo de exemplo criado no início rápido para experimentar o código de exemplo mostrado neste tutorial. 

Para ver a documentação de referência da API de gerenciamento de recursos do ASP.NET Core, confira [Namespace Microsoft.FeatureManagement](/dotnet/api/microsoft.featuremanagement).

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Adicionar sinalizadores de recursos aos componentes principais de seu aplicativo para controlar a disponibilidade de recursos.
> * Integre-os à Configuração de Aplicativos ao usá-la para gerenciar sinalizadores de recursos.

## <a name="set-up-feature-management"></a>Configurar o gerenciamento de recursos

Para acessar o gerenciador de recursos do .NET Core, seu aplicativo precisa ter referências ao pacote NuGet `Microsoft.FeatureManagement.AspNetCore`.

O gerenciador de recursos do .NET Core é configurado no sistema nativo de configuração da estrutura. Como resultado, você pode definir as configurações do sinalizador de recursos do aplicativo usando qualquer fonte de configuração compatível com o .NET Core, incluindo o arquivo *appsettings.json* local ou variáveis de ambiente.

Por padrão, o gerenciador de recursos recupera a configuração do sinalizador de recursos da seção `"FeatureManagement"` dos dados de configuração do .NET Core. Para usar a localização de configuração padrão, chame o método [AddFeatureManagement](/dotnet/api/microsoft.featuremanagement.servicecollectionextensions.addfeaturemanagement) do **IServiceCollection** passado para o método **ConfigureServices** da classe **Startup**.


```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement();
    }
}
```

Você pode especificar que a configuração do gerenciamento de recursos deve ser recuperada de uma seção de configuração diferente chamando [Configuration.GetSection](/dotnet/api/microsoft.web.administration.configuration.getsection) e passando o nome da seção desejada. O seguinte exemplo instrui o gerenciador de recursos a fazer a leitura de outra seção chamada `"MyFeatureFlags"`:

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement(Configuration.GetSection("MyFeatureFlags"));
    }
}
```


Se usar filtros em seus sinalizadores de recursos, você deverá incluir o namespace [Microsoft.FeatureManagement.FeatureFilters](/dotnet/api/microsoft.featuremanagement.featurefilters) e adicionar uma chamada para [AddFeatureFilters](/dotnet/api/microsoft.featuremanagement.ifeaturemanagementbuilder.addfeaturefilter) especificando o nome do tipo do filtro que deseja usar como o tipo genérico do método. Para saber mais sobre como usar filtros de recursos para habilitar e desabilitar dinamicamente a funcionalidade, confira [Habilitar a distribuição em etapas de recursos para públicos-alvo](./howto-targetingfilter-aspnet-core.md).

O seguinte exemplo mostra como usar um filtro de recurso interno chamado `PercentageFilter`:



```csharp
using Microsoft.FeatureManagement;
using Microsoft.FeatureManagement.FeatureFilters;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement()
                .AddFeatureFilter<PercentageFilter>();
    }
}
```

Em vez de embutir os sinalizadores de recursos no código do aplicativo, recomendamos que você os mantenha fora do aplicativo e os gerencie separadamente. Isso permite que você modifique os estados do sinalizador a qualquer momento e que essas alterações entrem em vigor no aplicativo imediatamente. O serviço de Configuração de Aplicativos do Azure fornece uma interface do usuário do portal dedicada para gerenciar todos os seus sinalizadores de recursos. O serviço de Configuração de Aplicativos do Azure também fornece os sinalizadores de recursos ao aplicativo diretamente por meio de suas bibliotecas de clientes do .NET Core.

A maneira mais fácil de conectar seu aplicativo ASP.NET Core à Configuração de Aplicativos é por meio do provedor de configuração incluído o pacote NuGet `Microsoft.Azure.AppConfiguration.AspNetCore`. Depois de incluir uma referência ao pacote, siga estas etapas para usar este pacote NuGet.

1. Abra o arquivo *Program.cs* e adicione o seguinte código.
    > [!IMPORTANT]
    > `CreateHostBuilder` substitui `CreateWebHostBuilder` no .NET Core 3.x. Selecione a sintaxe correta com base em seu ambiente.

    ### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
                }).UseStartup<Startup>());
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration(config =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
            }).UseStartup<Startup>());
    ```
        
    ### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                    .ConfigureAppConfiguration(config =>
                    {
                        var settings = config.Build();
                        config.AddAzureAppConfiguration(options =>
                            options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
                    }).UseStartup<Startup>();
    ```
    ---

2. Abra *Startup.cs* e atualize o método `Configure` e `ConfigureServices` para adicionar o middleware interno chamado `UseAzureAppConfiguration`. Esse middleware permite que os valores de sinalizador de recurso sejam atualizados em um intervalo recorrente enquanto o aplicativo Web ASP.NET Core continua recebendo solicitações.



    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        app.UseAzureAppConfiguration();
    }
    ```

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       services.AddAzureAppConfiguration();
   }
   ```
   
Em um cenário típico, você atualizará os valores do sinalizador de recurso periodicamente à medida que implantar e habilitar e recursos diferentes do aplicativo. Por padrão, os valores de sinalizador de recurso são armazenados em cache por um período de 30 segundos, de modo que uma operação de atualização disparada quando o middleware recebe uma solicitação não atualizará o valor até que o valor armazenado em cache expire. O código a seguir mostra como alterar a hora de expiração do cache ou o intervalo de sondagem para 5 minutos definindo o [CacheExpirationInterval](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.featuremanagement.featureflagoptions.cacheexpirationinterval) na chamada como **UseFeatureFlags**.


    
```csharp
config.AddAzureAppConfiguration(options =>
    options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags(featureFlagOptions => {
        featureFlagOptions.CacheExpirationInterval = TimeSpan.FromMinutes(5);
    }));
});
```


## <a name="feature-flag-declaration"></a>Declaração do sinalizador de recurso

Cada declaração de sinalizador de recurso tem duas partes: um nome e uma lista de um ou mais filtros que são usados para avaliar se o estado de um recurso é *ativado* (ou seja, quando seu valor é `True`). Um filtro define um critério para os casos em que um recurso deve ser ligado.

Quando um sinalizador de recursos tem vários filtros, a lista de filtro é percorrida na ordem até um dos filtros determinar que o recurso deve ser habilitado. Neste ponto, o sinalizador de recursos é *ativado* e os resultados restantes do filtro são ignorados. Se nenhum filtro indicar que o recurso deve ser habilitado, o sinalizador de recursos será *desativado*.

O gerenciador de recursos dá suporte ao *appsettings.json* como uma fonte de configuração para sinalizadores de recursos. O seguinte exemplo mostra como configurar os sinalizadores de recursos em um arquivo JSON:

```JSON
"FeatureManagement": {
    "FeatureA": true, // Feature flag set to on
    "FeatureB": false, // Feature flag set to off
    "FeatureC": {
        "EnabledFor": [
            {
                "Name": "Percentage",
                "Parameters": {
                    "Value": 50
                }
            }
        ]
    }
}
```

Por convenção, a seção `FeatureManagement` deste documento JSON é usada para as configurações de sinalizadores de recursos. O exemplo anterior mostra três sinalizadores de recursos com seus filtros definidos na propriedade `EnabledFor`:

* `FeatureA` está *ativado*.
* `FeatureB` está *desativado*.
* `FeatureC` especifica um filtro chamado `Percentage` com uma propriedade `Parameters`. `Percentage` é um filtro configurável. Neste exemplo, `Percentage` especifica uma probabilidade de 50% de que o sinalizador `FeatureC` esteja *ativado*. Para ver um guia de instruções sobre como usar filtros de recursos, confira [Usar filtros de recursos para habilitar sinalizadores de recursos condicionais](./howto-feature-filters-aspnet-core.md).




## <a name="use-dependency-injection-to-access-ifeaturemanager"></a>Usar injeção de dependência para acessar o IFeatureManager 

Para algumas operações, como verificar manualmente os valores dos sinalizadores de recursos, você precisa obter uma instância do [IFeatureManager](/dotnet/api/microsoft.featuremanagement.ifeaturemanager?preserve-view=true&view=azure-dotnet-preview). No ASP.NET Core MVC, você pode acessar o gerenciador de recursos `IFeatureManager` por meio da injeção de dependência. No exemplo a seguir, um argumento do tipo `IFeatureManager` é adicionado à assinatura do construtor para um controlador. O runtime resolve automaticamente a referência e fornece a interface ao chamar o construtor. Se você estiver usando um modelo de aplicativo no qual o controlador já tem um ou mais argumentos de injeção de dependência no construtor, como `ILogger`, poderá simplesmente adicionar `IFeatureManager` como um argumento adicional:

### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)
    
```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(ILogger<HomeController> logger, IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(ILogger<HomeController> logger, IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```
    
### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

---

## <a name="feature-flag-references"></a>Referências de sinalizadores de recursos

Defina sinalizadores de recurso como variáveis de cadeia de caracteres para fazer referência a eles no código:

```csharp
public static class MyFeatureFlags
{
    public const string FeatureA = "FeatureA";
    public const string FeatureB = "FeatureB";
    public const string FeatureC = "FeatureC";
}
```

## <a name="feature-flag-checks"></a>Verificações de sinalizadores de recursos

Um padrão comum do gerenciamento de recursos é verificar se um sinalizador de recursos está definido como *ativado* e, se estiver, executar uma seção de código. Por exemplo:

```csharp
IFeatureManager featureManager;
...
if (await featureManager.IsEnabledAsync(MyFeatureFlags.FeatureA))
{
    // Run the following code
}
```

## <a name="controller-actions"></a>Ações do controlador

Com controladores MVC, você pode usar o atributo `FeatureGate` para controlar se uma classe de controlador inteira ou uma ação específica será habilitada. O seguinte controlador `HomeController` exige que `FeatureA` esteja *ativado* antes de executar qualquer ação contida pela classe do controlador:

```csharp
using Microsoft.FeatureManagement.Mvc;

[FeatureGate(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

A seguinte ação `Index` exige que `FeatureA` esteja *ativado* para ser executada:

```csharp
using Microsoft.FeatureManagement.Mvc;

[FeatureGate(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

Quando uma ação ou um controlador MVC está bloqueado devido ao sinalizador de recursos controlador estar *desativado*, uma interface [IDisabledFeaturesHandler](/dotnet/api/microsoft.featuremanagement.mvc.idisabledfeatureshandler?preserve-view=true&view=azure-dotnet-preview) registrada é chamada. A interface `IDisabledFeaturesHandler` padrão retorna um código de status 404 para o cliente sem nenhum corpo de resposta.

## <a name="mvc-views"></a>Exibições do MVC

Abra *_ViewImports.cshtml* no diretório *Views* e adicione o auxiliar de marca do gerenciador de recursos:

```html
@addTagHelper *, Microsoft.FeatureManagement.AspNetCore
```

Em exibições do MVC, você pode usar uma marca `<feature>` para renderizar o conteúdo com base em se um sinalizador de recursos está habilitado:

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

Para exibir o conteúdo alternativo quando os requisitos não forem atendidos, é possível usar o atributo `negate`.

```html
<feature name="FeatureA" negate="true">
    <p>This will be shown if 'FeatureA' is disabled.</p>
</feature>
```

A marcar do recurso `<feature>` também pode ser usada para mostrar o conteúdo se qualquer ou todos os recursos em uma lista estiverem habilitados.

```html
<feature name="FeatureA, FeatureB" requirement="All">
    <p>This can only be seen if 'FeatureA' and 'FeatureB' are enabled.</p>
</feature>
<feature name="FeatureA, FeatureB" requirement="Any">
    <p>This can be seen if 'FeatureA', 'FeatureB', or both are enabled.</p>
</feature>
```

## <a name="mvc-filters"></a>Filtros do MVC

Configure filtros do MVC de modo que eles sejam ativados com base no estado de um sinalizador de recursos. Essa funcionalidade é limitada a filtros que implementam [IAsyncActionFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncactionfilter). O exemplo a seguir adiciona um filtro do MVC chamado `ThirdPartyActionFilter`. Esse filtro só é disparado no pipeline do MVC se `FeatureA` está habilitado.  

```csharp
using Microsoft.FeatureManagement.FeatureFilters;

IConfiguration Configuration { get; set;}

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options => {
        options.Filters.AddForFeature<ThirdPartyActionFilter>(MyFeatureFlags.FeatureA);
    });
}
```

## <a name="middleware"></a>Middleware

Você também pode usar sinalizadores de recursos para adicionar middleware e branches do aplicativo condicionalmente. O seguinte exemplo só insere um componente de middleware no pipeline de solicitação quando `FeatureA` está habilitado:

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(MyFeatureFlags.FeatureA);
```

Este código cria a funcionalidade mais genérica para gerar um branch de todo o aplicativo com base em um sinalizador de recursos:

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a implementar sinalizadores de recursos no aplicativo ASP.NET Core usando as bibliotecas `Microsoft.FeatureManagement`. Para obter mais informações sobre o suporte de gerenciamento de recursos no ASP.NET Core e na Configuração de Aplicativos, confira os seguintes recursos:

* [Código de exemplo do sinalizador de recurso do ASP.NET Core](./quickstart-feature-flag-aspnet-core.md)
* [Documentação de Microsoft.FeatureManagement](/dotnet/api/microsoft.featuremanagement)
* [Gerenciar sinalizadores de recursos](./manage-feature-flags.md)