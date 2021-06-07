---
title: 'Tutorial: Usar configuração dinâmica da Configuração de Aplicativos no ASP.NET Core'
titleSuffix: Azure App Configuration
description: Neste tutorial, você aprenderá a atualizar dinamicamente os dados de configuração de aplicativos ASP.NET Core
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/1/2020
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 083bd56b2b211d11206a277bf31eea797b37cdb9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99979922"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Tutorial: Usar a configuração dinâmica em um aplicativo ASP.NET Core

O ASP.NET Core tem um sistema de configuração conectável que pode ler dados de configuração de uma variedade de fontes. Ele pode lidar com alterações de modo dinâmico sem fazer com que um aplicativo reinicie. ASP.NET Core dá suporte a associação de parâmetros de configuração para classes .NET fortemente tipadas. Isso injeta-lo em seu código usando diversos `IOptions<T>` padrões. Um desses padrões, especificamente `IOptionsSnapshot<T>`, fornece o recarregamento automático da configuração do aplicativo quando os dados subjacentes são alterados. Você pode injetar `IOptionsSnapshot<T>` nos controladores do aplicativo para acessar a configuração mais recente armazenada na Configuração de Aplicativo do Azure.

Você também pode configurar a biblioteca de cliente do ASP.NET Core de Configuração de Aplicativos para atualizar um conjunto de definições de configuração dinamicamente usando um middleware. As definições de configuração são atualizadas com o repositório de configurações a cada vez, desde que o aplicativo Web receba solicitações.

A Configuração de Aplicativos armazena em cache automaticamente cada configuração para evitar um excesso de chamadas ao repositório de configurações. A operação de atualização aguarda até o valor em cache de uma configuração expirar para atualizar essa configuração, mesmo quando o valor é alterado no repositório de configurações. O tempo de término de cache padrão é de 30 segundos. Você pode substituir esse tempo de término, se necessário.

Este tutorial mostra como você pode implementar atualizações de configuração dinâmica no código. Ele se baseia no aplicativo Web introduzido nos Inícios Rápidos. Antes de continuar, conclua [Criar um aplicativo ASP.NET Core com a Configuração de Aplicativo](./quickstart-aspnet-core-app.md) primeiro.

Você pode usar qualquer editor de código para executar as etapas deste tutorial. [Visual Studio Code](https://code.visualstudio.com/) é uma opção excelente que está disponível nas plataformas Windows, macOS e Linux.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar seu aplicativo para atualizar a configuração em resposta a alterações em um repositório de Configuração de Aplicativos.
> * Injetar a configuração mais recente nos controladores do aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

Para realizar este tutorial, instale o [SDK do .NET Core](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Antes de continuar, conclua [Criar um aplicativo ASP.NET Core com a Configuração de Aplicativo](./quickstart-aspnet-core-app.md) primeiro.

## <a name="add-a-sentinel-key"></a>Adicionar uma chave sentinela

Uma *chave sentinela* é uma chave especial usada para sinalizar quando a configuração foi alterada. Seu aplicativo monitora a chave sentinela em busca de alterações. Quando uma alteração é detectada, você atualiza todos os valores de configuração. Essa abordagem reduz o número total de solicitações feitas pelo aplicativo à Configuração de Aplicativos em comparação ao monitoramento de todas as chaves quanto a alterações.

1. No portal do Azure, selecione **Explorador de Configuração > Criar > Valor de chave**.
1. Para **Chave**, insira *TestApp:Settings:Sentinel*. Para **Valor**, insira "1". Deixe **Rótulo** e **Tipo de conteúdo** em branco.
1. Escolha **Aplicar**.

> [!NOTE]
> Se você não estiver usando uma chave Sentinel, será necessário registrar manualmente todas as chaves que quiser inspecionar.

## <a name="reload-data-from-app-configuration"></a>Recarregar os dados da Configuração de Aplicativo

1. Adicione uma referência ao pacote NuGet `Microsoft.Azure.AppConfiguration.AspNetCore`, executando o seguinte comando:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. Abra *Program.cs* e atualize o método `CreateWebHostBuilder` para adicionar o método `config.AddAzureAppConfiguration()`.

   #### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                    {
                        options.Connect(settings["ConnectionStrings:AppConfig"])
                               .ConfigureRefresh(refresh =>
                                    {
                                        refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                               .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                    });
                    });
                })
            .UseStartup<Startup>());
    ```   
    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                    {
                        options.Connect(settings["ConnectionStrings:AppConfig"])
                               .ConfigureRefresh(refresh =>
                                    {
                                        refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                               .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                    });
                    });
                })
            .UseStartup<Startup>());
    ```
    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .ConfigureRefresh(refresh =>
                                {
                                    refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                           .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                });
                });
            })
            .UseStartup<Startup>();
    ```
    ---

    O método `ConfigureRefresh` é usado para especificar as configurações usadas para atualizar os dados de configuração com o repositório de Configuração de Aplicativos quando uma operação de atualização é disparada. O parâmetro `refreshAll` para o método `Register` indica que todos os valores de configuração deverão ser atualizados se a chave sentinela for alterada.

    Além disso, o método `SetCacheExpiration` substitui o tempo de término de cache padrão de 30 segundos especificando um tempo de 5 minutos. Isso reduz o número de solicitações feitas à Configuração de Aplicativos.

    > [!NOTE]
    > Para fins de teste, talvez você queira diminuir o tempo de término do cache.

    Para disparar de fato uma operação de atualização, configure um middleware de atualização para o aplicativo atualizar os dados de configuração quando ocorrer qualquer alteração. Você verá como fazer isso em uma etapa posterior.

2. Adicione um arquivo *Settings.cs* no diretório Controllers que define e implementa uma nova classe `Settings`. Substitua o namespace pelo nome do seu projeto. 

    ```csharp
    namespace TestAppConfig
    {
        public class Settings
        {
            public string BackgroundColor { get; set; }
            public long FontSize { get; set; }
            public string FontColor { get; set; }
            public string Message { get; set; }
        }
    }
    ```

3. Abra *Startup.cs* e use `IServiceCollection.Configure<T>` no método `ConfigureServices` para associar os dados de configuração à classe `Settings`.

    #### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddControllersWithViews();
        services.AddAzureAppConfiguration();
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddControllersWithViews();
        services.AddAzureAppConfiguration();
    }
    ```
    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
        services.AddAzureAppConfiguration();
    }
    ```
    ---
    > [!Tip]
    > Para saber mais sobre o padrão de opções ao ler valores de configuração, confira [Padrões de opções no ASP.NET Core](/aspnet/core/fundamentals/configuration/options).

4. Atualize o método `Configure`, adicionando o middleware `UseAzureAppConfiguration` para permitir que as definições de configuração registradas para atualização sejam atualizadas enquanto o aplicativo Web ASP.NET Core continua a receber solicitações.


    #### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
                // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
                app.UseHsts();
            }

            // Add the following line:
            app.UseAzureAppConfiguration();

            app.UseHttpsRedirection();
            
            app.UseStaticFiles();

            app.UseRouting();

            app.UseAuthorization();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
                // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
                app.UseHsts();
            }

            // Add the following line:
            app.UseAzureAppConfiguration();

            app.UseHttpsRedirection();
            
            app.UseStaticFiles();

            app.UseRouting();

            app.UseAuthorization();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();

        services.Configure<CookiePolicyOptions>(options =>
        {
            options.CheckConsentNeeded = context => true;
            options.MinimumSameSitePolicy = SameSiteMode.None;
        });

        app.UseMvc();
    }
    ```
    ---
    
    O middleware usa a configuração de atualização especificada no método `AddAzureAppConfiguration` em `Program.cs` para disparar uma atualização para cada solicitação recebida pelo aplicativo Web ASP.NET Core. Para cada solicitação, uma operação de atualização é disparada e a biblioteca de clientes verifica se o valor armazenado em cache para a definição de configuração registrada expirou. Se tiver ele expirado, será atualizado.

    > [!NOTE]
    > A fim de garantir que a configuração seja atualizada, adicione o middleware assim que for apropriado ao pipeline de solicitação, de modo que ele não entre em curto-circuito devido a outro middleware no aplicativo.

## <a name="use-the-latest-configuration-data"></a>Usar os dados de configuração mais recentes

1. Abra *HomeController.cs* no diretório Controllers e adicione uma referência ao pacote `Microsoft.Extensions.Options`.

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. Atualize a classe `HomeController` para receber `Settings` por meio de injeção de dependência e faça uso de seus valores.

 #### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)

```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        private readonly ILogger<HomeController> _logger;

        public HomeController(ILogger<HomeController> logger, IOptionsSnapshot<Settings> settings)
        {
            _logger = logger;
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }

        // ...
    }
```
#### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        private readonly ILogger<HomeController> _logger;

        public HomeController(ILogger<HomeController> logger, IOptionsSnapshot<Settings> settings)
        {
            _logger = logger;
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }

        // ...
    }
```
#### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        public HomeController(IOptionsSnapshot<Settings> settings)
        {
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }
    }
```
---



3. Abra *Index.cshtml* no diretório Views > Página Inicial e substitua o conteúdo pelo seguinte script:

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @ViewData["BackgroundColor"]
        }
        h1 {
            color: @ViewData["FontColor"];
            font-size: @ViewData["FontSize"]px;
        }
    </style>
    <head>
        <title>Index View</title>
    </head>
    <body>
        <h1>@ViewData["Message"]</h1>
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>Compilar e executar o aplicativo localmente

1. Para criar o aplicativo usando a CLI do .NET Core, execute o seguinte comando no shell de comando:

    ```console
        dotnet build
    ```

1. Depois que a construção for concluída com êxito, execute o seguinte comando para executar o aplicativo Web localmente:

    ```console
        dotnet run
    ```

1. Abra uma janela do navegador e vá para a URL mostrada na saída `dotnet run`.

    ![Como iniciar o aplicativo de início rápido localmente](./media/quickstarts/aspnet-core-app-launch-local-before.png)

1. Entre no [portal do Azure](https://portal.azure.com). Escolha **Todos os recursos** e escolha a instância do repositório de Configuração de Aplicativos que você criou no início rápido.

1. Selecione **Gerenciador de Configurações** e atualize os valores das seguintes chaves:

    | Chave | Valor |
    |---|---|
    | TestApp:Settings:BackgroundColor | green |
    | TestApp:Settings:FontColor | lightGray |
    | TestApp:Settings:Message | Dados da Configuração de Aplicativo do Azure – agora com atualizações dinâmicas! |
    | TestApp:Settings:Sentinel | 2 |

1. Atualize a página do navegador para ver as novas definições de configuração. Talvez seja necessário atualizá-la mais de uma vez para que as alterações sejam refletidas ou alterar a taxa de atualização automática para menos de 5 minutos. 

    ![Como iniciar o aplicativo de início rápido atualizado localmente](./media/quickstarts/aspnet-core-app-launch-local-after.png)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você habilitou seu aplicativo Web ASP.NET Core para atualizar dinamicamente as configurações da Configuração de Aplicativos. Para saber como usar uma identidade gerenciada pelo Azure para simplificar o acesso à Configuração de Aplicativos, passe para o próximo tutorial.

> [!div class="nextstepaction"]
> [Integração de identidade gerenciada](./howto-integrate-azure-managed-service-identity.md)
