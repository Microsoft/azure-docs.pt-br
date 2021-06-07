---
title: Início Rápido da Configuração de Aplicativo do Azure com o Azure Functions | Microsoft Docs
description: Neste início rápido, crie um aplicativo do Azure Functions com a Configuração de Aplicativos do Azure e C#. Criar e conectar-se a um repositório da Configuração de Aplicativos. Testar a função localmente.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 09/28/2020
ms.author: alkemper
ms.openlocfilehash: 9d378b21132e6646329c459401255ef9a3ed9426
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724230"
---
# <a name="quickstart-create-an-azure-functions-app-with-azure-app-configuration"></a>Início Rápido: Criar um aplicativo do Azure Functions com a Configuração de Aplicativos do Azure

Neste guia de início rápido, você incorpora o serviço de Configuração de Aplicativos do Azure em uma aplicativo do Azure Functions para centralizar o armazenamento e o gerenciamento de configurações de aplicativo separadas do seu código.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/dotnet)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) com a carga de trabalho de **desenvolvimento do Azure**.
- [Ferramentas do Azure Functions](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>Criar um repositório de Configuração de Aplicativos

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Selecione **Gerenciador de Configurações** >  **+ Criar** > **Chave-valor** para adicionar os seguintes pares chave-valor:

    | Chave | Valor |
    |---|---|
    | TestApp:Settings:Message | Dados da Configuração de Aplicativo do Azure |

    Deixe **Rótulo** e **Tipo de Conteúdo** vazios por enquanto.

8. Escolha **Aplicar**.

## <a name="create-a-functions-app"></a>Criar um aplicativo do Functions

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>Conectar um repositório de Configuração de Aplicativos
Este projeto usará [injeção de dependência no Azure Functions .NET](../azure-functions/functions-dotnet-dependency-injection.md) e adicionará a Configuração de Aplicativos do Azure como uma fonte de configuração extra.

1. Clique com o botão direito do mouse no projeto e selecione **Gerenciar Pacotes do NuGet**. Na guia **Navegar**, pesquise e adicione os pacotes NuGet a seguir ao projeto.
   - [Microsoft.Extensions.Configuration.AzureAppConfiguration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureAppConfiguration/) versão 4.1.0 ou posterior
   - [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/) versão 1.1.0 ou posterior 

2. Adicione um novo arquivo, *Startup.cs*, com o código a seguir. Ele define uma classe chamada `Startup` que implementa a classe abstrata `FunctionsStartup`. Um atributo de assembly é usado para especificar o nome de tipo usado durante a inicialização do Azure Functions.

    O método `ConfigureAppConfiguration` é substituído e o provedor de Configuração de Aplicativos do Azure é adicionado como uma fonte de configuração extra chamando `AddAzureAppConfiguration()`. O método `Configure` é deixado vazio, pois você não precisa registrar nenhum serviço no momento.
    
    ```csharp
    using System;
    using Microsoft.Azure.Functions.Extensions.DependencyInjection;
    using Microsoft.Extensions.Configuration;

    [assembly: FunctionsStartup(typeof(FunctionApp.Startup))]

    namespace FunctionApp
    {
        class Startup : FunctionsStartup
        {
            public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
            {
                string cs = Environment.GetEnvironmentVariable("ConnectionString");
                builder.ConfigurationBuilder.AddAzureAppConfiguration(cs);
            }

            public override void Configure(IFunctionsHostBuilder builder)
            {
            }
        }
    }
    ```

3. Abra *Function1.cs* e adicione o namespace a seguir.

    ```csharp
    using Microsoft.Extensions.Configuration;
    ```

   Adicione um construtor usado para obter uma instância de `IConfiguration` por meio de injeção de dependência.

    ```csharp
    private readonly IConfiguration _configuration;

    public Function1(IConfiguration configuration)
    {
        _configuration = configuration;
    }
    ```

4. Atualize o método `Run` para ler valores da configuração.

    ```csharp
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        string keyName = "TestApp:Settings:Message";
        string message = _configuration[keyName];

        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

   A classe `Function1` e o método `Run` não devem ser estáticos. Remova o modificador `static` se ele tiver sido gerado automaticamente.

## <a name="test-the-function-locally"></a>Testar a função localmente

1. Defina uma variável de ambiente chamada **ConnectionString** e defina-a como a chave de acesso ao repositório de Configuração de Aplicativos. Se você usar o prompt de comando do Windows, execute o comando a seguir e reinicie o prompt de comando para permitir que a alteração entre em vigor:

    ```cmd
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Se você usa o Windows PowerShell, execute o comando a seguir:

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Se você usa macOS ou Linux, execute o comando a seguir:

    ```bash
        export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. Pressione F5 para testar sua função. Se solicitado, aceite a solicitação do Visual Studio para baixar e instalar as ferramentas **principais (CLI) do Azure Functions**. Além disso, talvez seja necessário habilitar uma exceção de firewall para que as ferramentas possam manipular solicitações HTTP.

3. Copie a URL da sua função da saída do Azure Functions runtime.

    ![Depuração de função do Início Rápido no VS](./media/quickstarts/function-visual-studio-debugging.png)

4. Cole a URL para a solicitação HTTP na barra de endereços do navegador. A imagem a seguir mostra a resposta no navegador para a solicitação GET local retornada pela função.

    ![Inicialização local da Função do Início Rápido](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um repositório de Configuração de Aplicativos e o usou com um aplicativo do Azure Functions por meio do [Provedor de Configuração de Aplicativos](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration). Para saber como atualizar seu aplicativo do Azure Functions para atualizar dinamicamente a configuração, passe para o próximo tutorial.

> [!div class="nextstepaction"]
> [Habilitar configuração dinâmica no Azure Functions](./enable-dynamic-configuration-azure-functions-csharp.md)