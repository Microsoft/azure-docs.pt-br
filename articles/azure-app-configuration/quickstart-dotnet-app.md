---
title: Início Rápido da Configuração de Aplicativo do Azure com o .NET Framework | Microsoft Docs
description: Neste artigo, crie um aplicativo .NET Framework com a Configuração de Aplicativos do Azure para centralizar o armazenamento e o gerenciamento de configurações de aplicativo separados do seu código.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 09/28/2020
ms.author: alkemper
ms.openlocfilehash: 62516218ed2c0249f829ad8d286e4ad8bbc471f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932083"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Início Rápido: Criar um aplicativo .NET Framework com a Configuração de Aplicativo do Azure

Neste guia de início rápido, você incorpora a Configuração de Aplicativos do Azure em um aplicativo com base em .NET Framework para centralizar o armazenamento e o gerenciamento de configurações de aplicativo separadas do seu código.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/dotnet)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.2](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Criar um repositório de Configuração de Aplicativos

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Selecione **Gerenciador de Configurações** > **Criar** > **Chave-valor** para adicionar os seguintes pares chave-valor:

    | Chave | Valor |
    |---|---|
    | TestApp:Settings:Message | Dados da Configuração de Aplicativo do Azure |

    Deixe **Rótulo** e **Tipo de Conteúdo** vazios por enquanto.

8. Escolha **Aplicar**.

## <a name="create-a-net-console-app"></a>Criar um aplicativo de console do .NET

1. Inicie o Visual Studio e selecione **Arquivo** > **Novo** > **Projeto**.

1. Em **Criar um projeto**, filtre o tipo de projeto **Console** e clique em **Aplicativo de Console (.NET Framework)** . Selecione **Avançar**.

1. Em **Configurar seu novo projeto**, insira um nome de projeto. Em **Framework**, selecione **.NET Framework 4.7.1** ou superior. Selecione **Criar**.

## <a name="connect-to-an-app-configuration-store"></a>Conectar um repositório de Configuração de Aplicativos

1. Clique com o botão direito do mouse no projeto e selecione **Gerenciar Pacotes do NuGet**. Na guia **Navegar**, pesquise e adicione os seguintes pacotes do NuGet ao projeto.

    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 or later
    System.Configuration.ConfigurationManager version 4.6.0 or later
    ```

1. Atualize o arquivo *App.config* do projeto, conforme a seguir:

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" connectionString="${ConnectionString}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="ConnectionString" value ="Set via an environment variable - for example, dev, test, staging, or production connection string." />
    </appSettings>
    ```

   A cadeia de conexão do repositório de Configuração de Aplicativos é lida na variável de ambiente `ConnectionString`. Adicione o `Environment` construtor de configuração antes de `MyConfigStore` na propriedade `configBuilders` da seção `appSettings`.

1. Abra *Program.cs* e atualize o método `Main` para usar a Configuração de Aplicativo, chamando `ConfigurationManager`.

    ```csharp
    static void Main(string[] args)
    {
        string message = System.Configuration.ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
        Console.ReadKey();
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Compilar e executar o aplicativo localmente

1. Atualize o arquivo **App.config** substituindo `${ConnectionString}` pela cadeia de conexão real da sua instância de Configuração de Aplicativo. Encontre-o na guia **Chaves de acesso** do recurso Configuração de Aplicativo no portal do Azure.

1. Pressione Ctrl + F5 para criar e executar o aplicativo do console.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um repositório de Configuração de Aplicativos e o utilizou com um aplicativo de console .NET Framework. O valor `AppSettings` de `ConfigurationManager` não será alterado depois que o aplicativo for iniciado. No entanto, a biblioteca do provedor de configuração do .NET Standard da Configuração de Aplicativos também pode ser usada em um aplicativo .NET Framework. Para saber como permitir que o aplicativo .NET Framework atualize dinamicamente configurações, passe para o próximo tutorial.

> [!div class="nextstepaction"]
> [Habilitar configuração dinâmica](./enable-dynamic-configuration-dotnet.md)
