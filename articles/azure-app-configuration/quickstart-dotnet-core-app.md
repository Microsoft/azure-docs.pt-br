---
title: Início Rápido da Configuração de Aplicativo do Azure com o .NET Core | Microsoft Docs
description: Neste guia de início rápido, crie um aplicativo .NET Core com a Configuração de Aplicativos do Azure para centralizar o armazenamento e o gerenciamento de configurações de aplicativo separadas do seu código.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 09/28/2020
ms.author: alkemper
ms.openlocfilehash: 0ff80287971365b1477be319dc7a04760687f6a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98663393"
---
# <a name="quickstart-create-a-net-core-app-with-app-configuration"></a>Início Rápido: Criar um aplicativo .NET Core com a Configuração de Aplicativo

Neste guia de início rápido, você incorpora a Configuração de Aplicativos do Azure em um aplicativo .NET Core para centralizar o armazenamento e o gerenciamento de configurações de aplicativo separadas do seu código.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/dotnet)
- [SDK do .NET Core](https://dotnet.microsoft.com/download) – também disponível no [Azure Cloud Shell](https://shell.azure.com).

## <a name="create-an-app-configuration-store"></a>Criar um repositório de Configuração de Aplicativos

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Selecione **Gerenciador de Configurações** > **Criar** > **Chave-valor** para adicionar os seguintes pares chave-valor:

    | Chave | Valor |
    |---|---|
    | TestApp:Settings:Message | Dados da Configuração de Aplicativo do Azure |

    Deixe **Rótulo** e **Tipo de Conteúdo** vazios por enquanto.

8. Escolha **Aplicar**.

## <a name="create-a-net-core-console-app"></a>Criar um aplicativo de console .NET Core

Você usará a [CLI (interface de linha de comando) do .NET Core](/dotnet/core/tools/) para criar um projeto de aplicativo de console .NET Core. A vantagem de usar a CLI do .NET Core em relação ao Visual Studio é que ela está disponível nas plataformas Windows, macOS e Linux.  Como alternativa, use as ferramentas pré-instaladas disponíveis no [Azure Cloud Shell](https://shell.azure.com).

1. Crie uma nova pasta para o seu projeto.

2. Na nova pasta, execute o seguinte comando para criar um projeto de aplicativo de console .NET Core:

    ```dotnetcli
    dotnet new console
    ```

## <a name="connect-to-an-app-configuration-store"></a>Conectar um repositório de Configuração de Aplicativos

1. Adicione uma referência ao pacote NuGet `Microsoft.Extensions.Configuration.AzureAppConfiguration`, executando o seguinte comando:

    ```dotnetcli
    dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration
    ```

2. Execute o seguinte comando para restaurar pacotes do projeto:

    ```dotnetcli
    dotnet restore
    ```

3. Abra *Program.cs* e adicione uma referência ao provedor da Configuração de Aplicativos do .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

4. Atualize o método `Main` para usar a Configuração de Aplicativos chamando o método `builder.AddAzureAppConfiguration()`.

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));

        var config = builder.Build();
        Console.WriteLine(config["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Compilar e executar o aplicativo localmente

1. Defina uma variável de ambiente chamada **ConnectionString** e defina-a como a chave de acesso ao repositório de Configuração de Aplicativos. Na linha de comando, execute o seguinte comando:

    ```cmd
    setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Se você usa o Windows PowerShell, execute o comando a seguir:

    ```azurepowershell
    $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Se você usa macOS ou Linux, execute o comando a seguir:

    ```console
    export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

    Reinicie o prompt de comando para permitir que a alteração entre em vigor. Imprima o valor da variável de ambiente para confirmar que ele está definido corretamente.

2. Execute o seguinte comando para compilar o aplicativo de console:

    ```dotnetcli
    dotnet build
    ```

3. Depois que a construção for concluída com êxito, execute o seguinte comando para executar o aplicativo localmente:

    ```dotnetcli
    dotnet run
    ```

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um repositório de Configuração de Aplicativos e o usou com um aplicativo de console .NET Core por meio do [provedor da Configuração de Aplicativos](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration). Para saber como configurar seu aplicativo .NET Core para atualizar dinamicamente as configurações, passe para o próximo tutorial.

> [!div class="nextstepaction"]
> [Habilitar configuração dinâmica](./enable-dynamic-configuration-dotnet-core.md)