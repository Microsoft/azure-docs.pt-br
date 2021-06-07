---
title: Início Rápido da Configuração de Aplicativo do Azure com o ASP.NET Core | Microsoft Docs
description: Criar um aplicativo ASP.NET Core com a Configuração de Aplicativos do Azure para centralizar o armazenamento e o gerenciamento de configurações de aplicativo para um aplicativo ASP.NET Core.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-csharp, contperf-fy21q1
ms.topic: quickstart
ms.date: 09/25/2020
ms.author: alkemper
ms.openlocfilehash: 353a934842fd872d48d091f1df1b6d94ac4599ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98663427"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Início Rápido: Criar um aplicativo ASP.NET Core com a Configuração de Aplicativo do Azure

Neste início rápido, você deverá usar a Configuração de Aplicativos do Azure para centralizar o armazenamento e o gerenciamento de configurações de aplicativo para um aplicativo ASP.NET Core. O ASP.NET Core cria um objeto de configuração baseado em chave-valor, usando as configurações de uma ou mais fontes de dados especificadas por um aplicativo. Essas origens de dados são conhecidas como *provedores de configuração*. Como o cliente .NET Core da Configuração de Aplicativos é implementado como um provedor de configuração, o serviço aparece como outra fonte de dados.

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/dotnet)
* [SDK do .NET Core](https://dotnet.microsoft.com/download)

> [!TIP]
> O Azure Cloud Shell é um shell interativo gratuito que pode ser usado para executar as instruções de linha de comando contidas neste artigo. Ele tem ferramentas comuns do Azure pré-instaladas, incluindo o SDK do .NET Core. Se você fez logon na assinatura do Azure, inicie o [Azure Cloud Shell](https://shell.azure.com) em shell.azure.com. Você pode saber mais sobre o Azure Cloud Shell [lendo nossa documentação](../cloud-shell/overview.md)

## <a name="create-an-app-configuration-store"></a>Criar um repositório de Configuração de Aplicativos

[!INCLUDE[Azure App Configuration resource creation steps](../../includes/azure-app-configuration-create.md)]

7. Selecione **Operações** > **Gerenciador de Configurações** > **Criar** > **Chave-valor** para adicionar os seguintes pares chave-valor:

    | Chave                                | Valor                               |
    |------------------------------------|-------------------------------------|
    | `TestApp:Settings:BackgroundColor` | *#FFF*                              |
    | `TestApp:Settings:FontColor`       | *#000*                              |
    | `TestApp:Settings:FontSize`        | *24*                                |
    | `TestApp:Settings:Message`         | *Dados da Configuração de Aplicativo do Azure* |

    Deixe **Rótulo** e **Tipo de conteúdo** vazios por enquanto. Escolha **Aplicar**.

## <a name="create-an-aspnet-core-web-app"></a>Criar um aplicativo Web ASP.NET Core

Use a [CLI (interface de linha de comando) do .NET Core](/dotnet/core/tools) para criar um projeto MVC do ASP.NET Core. O [Azure Cloud Shell](https://shell.azure.com) fornece essas ferramentas para você. Elas também estão disponíveis nas plataformas Windows, macOS e Linux.

Execute o seguinte comando para criar um projeto MVC do ASP.NET Core em uma nova pasta *TestAppConfig*:

```dotnetcli
dotnet new mvc --no-https --output TestAppConfig
```

[!INCLUDE[Add Secret Manager support to an ASP.NET Core project](../../includes/azure-app-configuration-add-secret-manager.md)]

## <a name="connect-to-the-app-configuration-store"></a>Conectar-se a um repositório de Configuração de Aplicativos

1. Execute o seguinte comando para adicionar uma referência do pacote NuGet [Microsoft.Azure.AppConfiguration.AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore):

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. Execute o comando a seguir no mesmo diretório do arquivo *.csproj*. O comando usa o Gerenciador de Segredos para armazenar um segredo chamado `ConnectionStrings:AppConfig`, que armazena a cadeia de conexão do seu repositório de Configuração de Aplicativos. Substitua o espaço reservado `<your_connection_string>` pela cadeia de conexão do repositório de Configuração de Aplicativos. Encontre a cadeia de conexão em **Chaves de Acesso** no portal do Azure.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig "<your_connection_string>"
    ```

    > [!IMPORTANT]
    > Alguns shells truncarão a cadeia de conexão, a menos que ela seja colocada entre aspas. Verifique se a saída do comando `dotnet user-secrets list` mostra toda a cadeia de conexão. Se não, execute novamente o comando, colocando a cadeia de conexão entre aspas.

    O Gerenciador de Segredos é usado apenas para testar o aplicativo Web localmente. Quando o aplicativo for implantado no [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/web), use a configuração de aplicativo **Cadeias de Conexão** no Serviço de Aplicativo, em vez de armazenar a cadeia de conexão no Gerenciador de Segredos.

    Acesse esse segredo usando a API de Configuração do .NET Core. Um sinal de dois-pontos (`:`) funciona no nome da configuração com a API de Configuração em todas as plataformas com suporte. Para obter mais informações, confira [Chaves e valores de configuração](/aspnet/core/fundamentals/configuration#configuration-keys-and-values).

1. Em *Program.cs*, adicione uma referência ao namespace da API da Configuração do .NET Core:

    ```csharp
    using Microsoft.Extensions.Configuration;
    ```

1. Atualize o método `CreateWebHostBuilder` para usar a Configuração de Aplicativos chamando o método `AddAzureAppConfiguration`.

    > [!IMPORTANT]
    > `CreateHostBuilder` substitui `CreateWebHostBuilder` no .NET Core 3.x. Selecione a sintaxe correta com base em seu ambiente.

     #### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    var connection = settings.GetConnectionString("AppConfig");
                    config.AddAzureAppConfiguration(connection);
                }).UseStartup<Startup>());
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    var connection = settings.GetConnectionString("AppConfig");
                    config.AddAzureAppConfiguration(connection);
                }).UseStartup<Startup>());
    ```

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration(config =>
            {
                var settings = config.Build();
                var connection = settings.GetConnectionString("AppConfig");
                config.AddAzureAppConfiguration(connection);
            })
            .UseStartup<Startup>();
    ```

    ---

    Com a alteração anterior, o [provedor de configuração da Configuração de Aplicativos](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration) foi registrado com a API de Configuração do .NET Core.

## <a name="read-from-the-app-configuration-store"></a>Ler no repositório de Configuração de Aplicativos

Conclua as etapas a seguir para ler e exibir valores armazenados no repositório de Configuração de Aplicativos. A API de Configuração do .NET Core será usada para acessar o repositório. A sintaxe Razor será usada para exibir os valores das chaves.

Abra *\<app root>/Views/Home/Index.cshtml* e substitua o conteúdo pelo seguinte código:

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<style>
    body {
        background-color: @Configuration["TestApp:Settings:BackgroundColor"]
    }
    h1 {
        color: @Configuration["TestApp:Settings:FontColor"];
        font-size: @Configuration["TestApp:Settings:FontSize"]px;
    }
</style>

<h1>@Configuration["TestApp:Settings:Message"]</h1>
```

No código anterior, as chaves do repositório de Configuração de Aplicativos são usadas da seguinte maneira:

* O valor da chave `TestApp:Settings:BackgroundColor` é atribuído à propriedade CSS `background-color`.
* O valor da chave `TestApp:Settings:FontColor` é atribuído à propriedade CSS `color`.
* O valor da chave `TestApp:Settings:FontSize` é atribuído à propriedade CSS `font-size`.
* O valor da chave `TestApp:Settings:Message` é exibido como um título.

## <a name="build-and-run-the-app-locally"></a>Compilar e executar o aplicativo localmente

1. Para criar o aplicativo usando a CLI do .NET Core, navegue até o diretório raiz do seu projeto. Execute o seguinte comando no shell de comando:

    ```dotnetcli
    dotnet build
    ```

1. Depois que o build for concluído com êxito, execute o seguinte comando para executar o aplicativo Web localmente:

    ```dotnetcli
    dotnet run
    ```

1. Se você estiver trabalhando em seu computador local, use um navegador para navegar até `http://localhost:5000`. Este endereço é a URL padrão do aplicativo Web hospedado localmente. Se você estiver trabalhando no Azure Cloud Shell, selecione o botão **Visualização na Web**, seguido de **Configurar**.

    ![Localizar o botão Visualização na Web](./media/quickstarts/cloud-shell-web-preview.png)

    Quando for solicitado a configurar a porta para a versão prévia, insira *5000* e selecione **Abrir e procurar**. A página da Web lerá "Dados de Configuração de Aplicativos do Azure".

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE[Azure App Configuration cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você:

* Provisionou um novo repositório de Configuração de Aplicativos.
* Registrou o provedor de configuração do .NET Core do repositório de Configuração de Aplicativos.
* Leu as chaves do repositório de Configuração de Aplicativos com o provedor de configuração.
* Exibiu os valores de chave do repositório de Configuração de Aplicativos usando a sintaxe Razor.

Para saber como configurar seu aplicativo ASP.NET Core para atualizar dinamicamente as configurações, passe para o próximo tutorial.

> [!div class="nextstepaction"]
> [Habilitar configuração dinâmica](./enable-dynamic-configuration-aspnet-core.md)