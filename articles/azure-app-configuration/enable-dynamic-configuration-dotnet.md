---
title: 'Tutorial do .NET Framework: configuração dinâmica na Configuração de Aplicativos do Azure'
description: Neste tutorial, você aprenderá a atualizar dinamicamente os dados de configuração de aplicativos .NET Framework usando a Configuração de Aplicativos do Azure.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-csharp
ms.topic: tutorial
ms.date: 07/24/2020
ms.author: alkemper
ms.openlocfilehash: 03940a86176d0bc93c5066977fdc87de5c456060
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932754"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-framework-app"></a>Tutorial: Usar configuração dinâmica em um aplicativo .NET Framework

A biblioteca de clientes do .NET da Configuração de Aplicativos é compatível com a atualização de um conjunto de definições de configuração sob demanda sem fazer o aplicativo reiniciar. Isso pode ser implementado primeiro obtendo uma instância de `IConfigurationRefresher` entre as opções para o provedor de configuração e, em seguida, chamando `TryRefreshAsync` nessa instância em qualquer lugar em seu código.

Para manter as configurações atualizadas e evitar o excesso de chamadas para o repositório de configurações, um cache é usado para cada configuração. Até que o valor armazenado em cache de uma configuração tenha expirado, a operação de atualização não atualizará o valor, mesmo quando o valor tiver sido alterado no repositório de configurações. O tempo de expiração padrão para cada solicitação é de 30 segundos, mas pode ser substituído, se necessário.

Este tutorial mostra como você pode implementar atualizações de configuração dinâmica no código. Ele se baseia no aplicativo introduzido nos inícios rápidos. Antes de continuar, primeiro conclua [Criar um aplicativo .NET Framework com a Configuração de Aplicativos](./quickstart-dotnet-app.md).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configure seu aplicativo .NET Framework para atualizar a configuração em resposta a alterações em um repositório de Configuração de Aplicativos.
> * Injete a configuração mais recente em seu aplicativo.
## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.1 ou posterior](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Criar um repositório de Configuração de Aplicativos

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Selecione **Gerenciador de Configurações** >  **+ Criar** > **Chave-valor** para adicionar os seguintes pares chave-valor:

    | Chave | Valor |
    |---|---|
    | TestApp:Settings:Message | Dados da Configuração de Aplicativo do Azure |

    Deixe **Rótulo** e **Tipo de Conteúdo** vazios por enquanto.

8. Escolha **Aplicar**.

## <a name="create-a-net-framework-console-app"></a>Criar um aplicativo de console .NET Framework

1. Inicie o Visual Studio e selecione **Arquivo** > **Novo** > **Projeto**.

1. Em **Criar um projeto**, filtre o tipo de projeto **Console** e clique em **Aplicativo de Console (.NET Framework)** . Clique em **Próximo**.

1. Em **Configurar seu novo projeto**, insira um nome de projeto. Em **Framework**, selecione **.NET Framework 4.7.1** ou superior. Clique em **Criar**.

## <a name="reload-data-from-app-configuration"></a>Recarregar os dados da Configuração de Aplicativo
1. Clique com o botão direito do mouse no projeto e selecione **Gerenciar Pacotes do NuGet**. Na guia **Procurar**, pesquise e adicione o pacote NuGet *Microsoft.Extensions.Configuration.AzureAppConfiguration* ao seu projeto. Se você não conseguir localizá-lo, marque a caixa de seleção **Incluir pré-lançamento**.

1. Abra *Program.cs* e adicione uma referência ao provedor da Configuração de Aplicativos do .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Adicione duas variáveis para armazenar objetos relacionados à configuração.

    ```csharp
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;
    ```

1. Atualize o método `Main` para se conectar à Configuração de Aplicativos com as opções de atualização especificadas.

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                    .ConfigureRefresh(refresh =>
                    {
                        refresh.Register("TestApp:Settings:Message")
                            .SetCacheExpiration(TimeSpan.FromSeconds(10));
                    });

            _refresher = options.GetRefresher();
        });

        _configuration = builder.Build();
        PrintMessage().Wait();
    }
    ```
    O método `ConfigureRefresh` é usado para especificar as configurações usadas para atualizar os dados de configuração com o repositório de Configuração de Aplicativos quando uma operação de atualização é disparada. Uma instância do `IConfigurationRefresher` pode ser recuperada chamando o método `GetRefresher` nas opções fornecidas ao método `AddAzureAppConfiguration` e o método `TryRefreshAsync` nessa instância pode ser usado para disparar uma operação de atualização em qualquer lugar no seu código.

    > [!NOTE]
    > O tempo de expiração de cache padrão para um parâmetro de configuração é de 30 segundos, mas pode ser substituído chamando o método `SetCacheExpiration` no inicializador de opções passado como um argumento para o método `ConfigureRefresh`.

1. Adicione um método chamado `PrintMessage()` que dispara uma atualização manual dos dados de configuração da Configuração de Aplicativos.

    ```csharp
    private static async Task PrintMessage()
    {
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

        // Wait for the user to press Enter
        Console.ReadLine();

        await _refresher.TryRefreshAsync();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Compilar e executar o aplicativo localmente

1. Defina uma variável de ambiente chamada **ConnectionString** e defina-a como a chave de acesso ao repositório de Configuração de Aplicativos. Se você usar o prompt de comando do Windows, execute o comando a seguir e reinicie o prompt de comando para permitir que a alteração entre em vigor:

    ```console
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Se você usa o Windows PowerShell, execute o comando a seguir:

    ```powershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

1. Reinicie o Visual Studio para permitir que a alteração tenha efeito. 

1. Pressione Ctrl + F5 para criar e executar o aplicativo do console.

    ![Local de inicialização do aplicativo](./media/dotnet-app-run.png)

1. Entre no [portal do Azure](https://portal.azure.com). Escolha **Todos os recursos** e escolha a instância do repositório de Configuração de Aplicativos que você criou no início rápido.

1. Selecione **Gerenciador de Configurações** e atualize os valores das seguintes chaves:

    | Chave | Valor |
    |---|---|
    | TestApp:Settings:Message | Dados da Configuração de Aplicativos do Azure – Atualizados |

1. De volta ao aplicativo em execução, pressione a tecla Enter para disparar uma atualização e imprimir o valor atualizado na janela do Prompt de Comando ou do PowerShell.

    ![Local de atualização do aplicativo](./media/dotnet-app-run-refresh.png)
    
    > [!NOTE]
    > Uma vez que o tempo de expiração do cache foi definido como 10 segundos usando o método `SetCacheExpiration` ao especificar a configuração para a operação de atualização, o valor para a definição de configuração será atualizado apenas se pelo menos 10 segundos tiverem se passado desde a última atualização para essa configuração.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você permitiu que seu aplicativo .NET Framework atualizasse dinamicamente configurações da Configuração de Aplicativos. Para saber como usar uma identidade gerenciada pelo Azure para simplificar o acesso à Configuração de Aplicativos, passe para o próximo tutorial.

> [!div class="nextstepaction"]
> [Integração de identidade gerenciada](./howto-integrate-azure-managed-service-identity.md)
