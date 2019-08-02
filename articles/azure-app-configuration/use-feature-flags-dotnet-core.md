---
title: Tutorial para uso de sinalizadores de recurso em um aplicativo .NET Core | Microsoft Docs
description: Neste tutorial, você aprenderá a implementar sinalizadores de recursos em aplicativos .NET Core.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 99559c0c77c3e4b29badec1c0be2d741df1f0621
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798371"
---
# <a name="tutorial-use-feature-flags-in-an-aspnet-core-app"></a>Tutorial: Usar sinalizadores de recursos em um aplicativo ASP.NET Core

As bibliotecas de Gerenciamento de Recursos do .NET Core fornecem suporte idiomático para implementação de sinalizadores de recursos em um aplicativo .NET ou ASP.NET Core. Essas bibliotecas permitem que você adicione sinalizadores de recursos ao código de forma declarativa, de modo que você não precise escrever todas as instruções `if` para elas manualmente.

As bibliotecas do Gerenciamento de Recursos também gerenciam os ciclos de vida dos sinalizadores de recursos nos bastidores. Por exemplo, as bibliotecas atualizam e armazenam em cache os estados do sinalizador ou garantem um estado de sinalizador imutável durante uma chamada de solicitação. Além disso, a biblioteca ASP.NET Core oferece integrações prontas para uso, incluindo ações do controlador MVC, exibições, rotas e middleware.

O [Início Rápido Adicionar sinalizadores de recursos a um aplicativo ASP.NET Core](./quickstart-feature-flag-aspnet-core.md) mostra várias maneiras de adicionar sinalizadores de recursos a um aplicativo ASP.NET Core. Este tutorial explica esses métodos mais detalhadamente. Para obter uma referência completa, confira a [documentação do gerenciamento de recursos do ASP.NET Core](https://go.microsoft.com/fwlink/?linkid=2091410).

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Adicionar sinalizadores de recursos aos componentes principais de seu aplicativo para controlar a disponibilidade de recursos.
> * Integre-os à Configuração de Aplicativos ao usá-la para gerenciar sinalizadores de recursos.

## <a name="set-up-feature-management"></a>Configurar o gerenciamento de recursos

O gerenciador de recursos `IFeatureManager` do .NET Core obtém os sinalizadores de recursos do sistema de configuração nativa da estrutura. Como resultado, você pode definir os sinalizadores de recursos do aplicativo usando qualquer fonte de configuração compatível com o .NET Core, incluindo o arquivo *appsettings.json* local ou variáveis de ambiente. O `IFeatureManager` se baseia na injeção de dependência do .NET Core. Você pode registrar os serviços de gerenciamento de recursos usando convenções padrão:

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement();
    }
}
```

Por padrão, o gerenciador de recursos recupera os sinalizadores de recursos da seção `"FeatureManagement"` dos dados de configuração do .NET Core. O seguinte exemplo instrui o gerenciador de recursos a fazer a leitura de outra seção chamada `"MyFeatureFlags"`:

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement(options =>
        {
                options.UseConfiguration(Configuration.GetSection("MyFeatureFlags"));
        });
    }
}
```

Se você usar filtros nos sinalizadores de recursos, precisará incluir uma biblioteca adicional e registrá-la. O seguinte exemplo mostra como usar um filtro de recurso interno chamado `PercentageFilter`:

```csharp
using Microsoft.FeatureManagement;
using Microsoft.FeatureManagement.FeatureFilters;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement()
                .AddFeatureFilter<PercentageFilter>();
    }
}
```

Recomendamos que você mantenha os sinalizadores de recursos fora do aplicativo e gerencie-os separadamente. Isso permite que você modifique os estados do sinalizador a qualquer momento e que essas alterações entrem em vigor no aplicativo imediatamente. A Configuração de Aplicativos fornece um local centralizado para organizar e controlar todos os sinalizadores de recursos por meio de uma interface do usuário dedicada do portal. A Configuração de Aplicativos também fornece os sinalizadores ao aplicativo diretamente por meio de suas bibliotecas de clientes do .NET Core.

A maneira mais fácil de conectar seu aplicativo ASP.NET Core à Configuração de Aplicativos é por meio do provedor de configuração `Microsoft.Azure.AppConfiguration.AspNetCore`. Siga estas etapas para usar esse pacote NuGet.

1. Abra o arquivo *Program.cs* e adicione o seguinte código.

   ```csharp
   using Microsoft.Extensions.Configuration.AzureAppConfiguration;

   public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
       WebHost.CreateDefaultBuilder(args)
              .ConfigureAppConfiguration((hostingContext, config) => {
                  var settings = config.Build();
                  config.AddAzureAppConfiguration(options => {
                      options.Connect(settings["ConnectionStrings:AppConfig"])
                             .UseFeatureFlags();
                   });
              })
              .UseStartup<Startup>();
   ```

2. Abra *Startup.cs* e atualize o método `Configure` para adicionar um middleware para permitir que os valores de sinalizador de recurso sejam atualizados a um intervalo recorrente enquanto o aplicativo Web ASP.NET Core continua recebendo solicitações.

   ```csharp
   public void Configure(IApplicationBuilder app, IHostingEnvironment env)
   {
       app.UseAzureAppConfiguration();
       app.UseMvc();
   }
   ```

Os valores de sinalizadores de recurso devem ser alterados ao longo do tempo. Por padrão, os valores de sinalizador de recurso são armazenados em cache por um período de 30 segundos, de modo que uma operação de atualização disparada quando o middleware recebe uma solicitação não atualizará o valor até que o valor armazenado em cache expire. O seguinte código mostra como alterar a hora de expiração do cache ou o intervalo de sondagem para 5 segundos na chamada `options.UseFeatureFlags()`.

```csharp
config.AddAzureAppConfiguration(options => {
    options.Connect(settings["ConnectionStrings:AppConfig"])
           .UseFeatureFlags(featureFlagOptions => {
                featureFlagOptions.CacheExpirationTime = TimeSpan.FromMinutes(5);
           });
});
```

## <a name="feature-flag-declaration"></a>Declaração do sinalizador de recurso

Cada sinalizador de recurso tem duas partes: um nome e uma lista de um ou mais filtros que são usados para avaliar se o estado de um recurso é *ativado* (ou seja, quando seu valor é `True`). Um filtro define um caso de uso para os casos em que um recurso deve ser ligado.

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
* `FeatureC` especifica um filtro chamado `Percentage` com uma propriedade `Parameters`. `Percentage` é um filtro configurável. Neste exemplo, `Percentage` especifica uma probabilidade de 50% de que o sinalizador `FeatureC` esteja *ativado*.

## <a name="feature-flag-references"></a>Referências de sinalizadores de recursos

Para que você possa referenciar sinalizadores de recursos no código com facilidade, defina-os como variáveis `enum`:

```csharp
public enum MyFeatureFlags
{
    FeatureA,
    FeatureB,
    FeatureC
}
```

## <a name="feature-flag-checks"></a>Verificações de sinalizadores de recursos

O padrão básico do gerenciamento de recursos é primeiro verificar se um sinalizador de recursos está definido como *ativado*. Nesse caso, em seguida, o gerenciador de recursos executa as ações contidas pelo recurso. Por exemplo:

```csharp
IFeatureManager featureManager;
...
if (featureManager.IsEnabled(nameof(MyFeatureFlags.FeatureA)))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Injeção de dependência

No ASP.NET Core MVC, você pode acessar o gerenciador de recursos `IFeatureManager` por meio da injeção de dependência:

```csharp
public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

## <a name="controller-actions"></a>Ações do controlador

Em controladores MVC, use o atributo `FeatureGate` para controlar se uma classe de controlador inteira ou uma ação específica será habilitada. O seguinte controlador `HomeController` exige que `FeatureA` esteja *ativado* antes de executar qualquer ação contida pela classe do controlador:

```csharp
[FeatureGate(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

A seguinte ação `Index` exige que `FeatureA` esteja *ativado* para ser executada:

```csharp
[FeatureGate(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

Quando uma ação ou um controlador MVC está bloqueado devido ao sinalizador de recursos controlador estar *desativado*, uma interface `IDisabledFeaturesHandler` registrada é chamada. A interface `IDisabledFeaturesHandler` padrão retorna um código de status 404 para o cliente sem nenhum corpo de resposta.

## <a name="mvc-views"></a>Exibições do MVC

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

Configure filtros do MVC de modo que eles sejam ativados com base no estado de um sinalizador de recursos. O exemplo a seguir adiciona um filtro do MVC chamado `SomeMvcFilter`. Esse filtro só é disparado no pipeline do MVC se `FeatureA` está habilitado.

```csharp
using Microsoft.FeatureManagement.FeatureFilters;

IConfiguration Configuration { get; set;}

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options => {
        options.Filters.AddForFeature<SomeMvcFilter>(nameof(MyFeatureFlags.FeatureA));
    });
}
```

## <a name="routes"></a>Rotas

Você pode usar sinalizadores de recursos para expor rotas dinamicamente. O seguinte exemplo adiciona uma rota, que só define `Beta` como o controlador padrão quando `FeatureA` está habilitado:

```csharp
app.UseMvc(routes => {
    routes.MapRouteForFeature(nameof(MyFeatureFlags.FeatureA), "betaDefault", "{controller=Beta}/{action=Index}/{id?}");
});
```

## <a name="middleware"></a>Middleware

Você também pode usar sinalizadores de recursos para adicionar middleware e branches do aplicativo condicionalmente. O seguinte exemplo só insere um componente de middleware no pipeline de solicitação quando `FeatureA` está habilitado:

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(nameof(MyFeatureFlags.FeatureA));
```

Este código cria a funcionalidade mais genérica para gerar um branch de todo o aplicativo com base em um sinalizador de recursos:

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a implementar sinalizadores de recursos no aplicativo ASP.NET Core usando as bibliotecas `Microsoft.FeatureManagement`. Para obter mais informações sobre o suporte de gerenciamento de recursos no ASP.NET Core e na Configuração de Aplicativos, confira os seguintes recursos:

* [Código de exemplo do sinalizador de recurso do ASP.NET Core](/azure/azure-app-configuration/quickstart-feature-flag-aspnet-core)
* [Documentação de Microsoft.FeatureManagement](https://docs.microsoft.com/dotnet/api/microsoft.featuremanagement)
* [Gerenciar sinalizadores de recursos](./manage-feature-flags.md)
