---
title: Tutorial para o uso da configuração dinâmica da Configuração de Aplicativo do Azure em um aplicativo ASP.NET Core | Microsoft Docs
description: Neste tutorial, você aprenderá a atualizar dinamicamente os dados de configuração de aplicativos ASP.NET Core
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 9eccb4ca505dac312dd22123a3585863c67f3ad7
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359864"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Tutorial: Usar a configuração dinâmica em um aplicativo ASP.NET Core

O ASP.NET Core tem um sistema de configuração conectável que pode ler dados de configuração de uma variedade de fontes. Ele pode lidar com alterações em tempo real sem fazer com que um aplicativo reinicie. ASP.NET Core dá suporte a associação de parâmetros de configuração para classes .NET fortemente tipadas. Isso injeta-lo em seu código usando diversos `IOptions<T>` padrões. Um desses padrões, especificamente `IOptionsSnapshot<T>`, fornece o recarregamento automático da configuração do aplicativo quando os dados subjacentes são alterados. Você pode injetar `IOptionsSnapshot<T>` nos controladores do aplicativo para acessar a configuração mais recente armazenada na Configuração de Aplicativo do Azure.

Você também pode configurar a biblioteca de cliente do ASP.NET Core de Configuração de Aplicativos para atualizar um conjunto de definições de configuração dinamicamente usando um middleware. Desde que o aplicativo Web continue a receber solicitações, as definições de configuração continuarão sendo atualizado com o repositório de configurações.

Para manter as configurações atualizadas e evitar o excesso de chamadas para o repositório de configurações, um cache é usado para cada configuração. Até que o valor armazenado em cache de uma configuração tenha expirado, a operação de atualização não atualizará o valor, mesmo quando o valor tiver sido alterado no repositório de configurações. O tempo de expiração padrão para cada solicitação é de 30 segundos, mas pode ser substituído, se necessário.

Este tutorial mostra como você pode implementar atualizações de configuração dinâmica no código. Ele se baseia no aplicativo Web introduzido nos Inícios Rápidos. Antes de continuar, conclua [Criar um aplicativo ASP.NET Core com a Configuração de Aplicativo](./quickstart-aspnet-core-app.md) primeiro.

Você pode usar qualquer editor de código para executar as etapas deste tutorial. [Visual Studio Code](https://code.visualstudio.com/) é uma opção excelente que está disponível nas plataformas Windows, macOS e Linux.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configure seu aplicativo para atualizar a configuração em resposta a alterações em um repositório de configurações de aplicativo.
> * Injetar a configuração mais recente nos controladores do aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

Para realizar este tutorial, instale o [SDK do .NET Core](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Recarregar os dados da Configuração de Aplicativo

1. Abra *Program.cs* e atualize o método `CreateWebHostBuilder` para adicionar o método `config.AddAzureAppConfiguration()`.

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
                               refresh.Register("TestApp:Settings:BackgroundColor")
                                      .Register("TestApp:Settings:FontColor")
                                      .Register("TestApp:Settings:Message")
                           });
                }
            })
            .UseStartup<Startup>();
    ```

    O método `ConfigureRefresh` é usado para especificar as configurações usadas para atualizar os dados de configuração com o repositório de configurações de aplicativos quando uma operação de atualização é disparada. Para disparar de fato uma operação de atualização, um middleware de atualização precisa ser configurado para o aplicativo atualizar os dados de configuração quando ocorrer qualquer alteração.

2. Adicione um arquivo *Settings.cs* que define e implementa uma nova classe `Settings`.

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

3. Abra *Startup.cs* e atualize o método `ConfigureServices` para associar os dados de configuração à classe `Settings`.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));

        services.Configure<CookiePolicyOptions>(options =>
        {
            options.CheckConsentNeeded = context => true;
            options.MinimumSameSitePolicy = SameSiteMode.None;
        });

        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

4. Atualize o método `Configure` para adicionar um middleware para permitir que as definições de configuração registradas para atualização sejam atualizadas enquanto o aplicativo Web ASP.NET Core continua a receber solicitações.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();
        app.UseMvc();
    }
    ```
    
    O middleware usa a configuração de atualização especificada no método `AddAzureAppConfiguration` em `Program.cs` para disparar uma atualização para cada solicitação recebida pelo aplicativo Web ASP.NET Core. Para cada solicitação, uma operação de atualização é disparada e a biblioteca de clientes verifica se o valor armazenado em cache para as definições de configuração registradas expirou. Para os valores armazenados em cache que expiraram, os valores para as configurações são atualizados com o repositório de configurações de aplicativos e os valores restantes permanecem inalterados.
    
    > [!NOTE]
    > O tempo de expiração de cache padrão para um parâmetro de configuração é de 30 segundos, mas pode ser substituído chamando o método `SetCacheExpiration` no inicializador de opções passado como um argumento para o método `ConfigureRefresh`.

## <a name="use-the-latest-configuration-data"></a>Usar os dados de configuração mais recentes

1. Abra *HomeController.cs* no diretório Controllers e adicione uma referência ao pacote `Microsoft.Extensions.Options`.

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. Atualize a classe `HomeController` para receber `Settings` por meio de injeção de dependência e faça uso de seus valores.

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
            font-size: @ViewData["FontSize"];
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

        dotnet build

2. Depois que a construção for concluída com êxito, execute o seguinte comando para executar o aplicativo Web localmente:

        dotnet run

3. Abra uma janela do navegador e vá para `http://localhost:5000`, que é a URL padrão do aplicativo Web hospedado localmente.

    ![Inicialização local do aplicativo do Início Rápido](./media/quickstarts/aspnet-core-app-launch-local-before.png)

4. Entre no [Portal do Azure](https://portal.azure.com). Selecione **Todos os recursos** e selecione a instância do repositório de configurações do aplicativo que você criou no início rápido.

5. Selecione **Gerenciador de Configurações** e atualize os valores das seguintes chaves:

    | Chave | Valor |
    |---|---|
    | TestApp:Settings:BackgroundColor | green |
    | TestApp:Settings:FontColor | lightGray |
    | TestApp:Settings:Message | Dados da Configuração de Aplicativo do Azure – agora com atualizações dinâmicas! |

6. Atualize a página do navegador para ver as novas definições de configuração. Mais de uma atualização de página do navegador pode ser necessária para que as alterações sejam refletidas.

    ![Atualização local do aplicativo do Início Rápido](./media/quickstarts/aspnet-core-app-launch-local-after.png)
    
    > [!NOTE]
    > Uma vez que as definições de configuração são armazenadas em cache com um tempo de expiração padrão de 30 segundos, todas as alterações feitas às configurações no repositório de configurações de aplicativos só serão refletidas no aplicativo Web quando o cache tiver expirado.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você adicionou uma identidade de serviço gerenciada do Azure para simplificar o acesso à Configuração de Aplicativo e melhorar o gerenciamento de credenciais de seu aplicativo. Para saber mais sobre como usar a Configuração de Aplicativo, continue para ver as amostras da CLI do Azure.

> [!div class="nextstepaction"]
> [Exemplos de CLI](./cli-samples.md)
