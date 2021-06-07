---
title: Início Rápido para adicionar sinalizadores de recursos a aplicativos .NET Framework | Microsoft Docs
description: Um início rápido para adicionar sinalizadores de recursos a aplicativos .NET Framework e gerenciá-los na Configuração de Aplicativos do Azure
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET
ms.workload: tbd
ms.date: 10/19/2020
ms.author: alkemper
ms.openlocfilehash: 513c826e11ff9dfe6ea94349c67620da9d1bba48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932040"
---
# <a name="quickstart-add-feature-flags-to-a-net-framework-app"></a>Início Rápido: Adicionar sinalizadores de recursos a um aplicativo .NET Framework

Neste início rápido, você incorporará a Configuração de Aplicativos do Azure a um aplicativo .NET Framework para criar uma implementação de ponta a ponta do gerenciamento de recursos. Você pode usar o serviço de Configuração de Aplicativos para armazenar de forma centralizada todos os sinalizadores de recurso e controlar seus estados. 

As bibliotecas do Gerenciamento de Recursos do .NET estendem a estrutura com suporte a sinalizadores de recursos. Essas bibliotecas se baseiam no sistema de configuração do .NET. Elas são integradas à Configuração de Aplicativos por meio do provedor de configuração do .NET.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/dotnet)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.8](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Criar um repositório de Configuração de Aplicativos

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Selecione **Gerenciador de Recursos** >  **+Adicionar** para adicionar um sinalizador de recurso chamado `Beta`.

    > [!div class="mx-imgBorder"]
    > ![Habilitar o sinalizador de recurso chamado Beta](media/add-beta-feature-flag.png)

    Mantenha `label` indefinido por enquanto.

## <a name="create-a-net-console-app"></a>Criar um aplicativo de console do .NET

1. Inicie o Visual Studio e selecione **Arquivo** > **Novo** > **Projeto**.

1. Em **Criar um projeto**, filtre o tipo de projeto **Console** e clique em **Aplicativo de Console (.NET Framework)** . Clique em **Próximo**.

1. Em **Configurar seu novo projeto**, insira um nome de projeto. Em **Framework**, selecione **.NET Framework 4.8** ou superior. Clique em **Criar**.

## <a name="connect-to-an-app-configuration-store"></a>Conectar um repositório de Configuração de Aplicativos

1. Clique com o botão direito do mouse no projeto e selecione **Gerenciar Pacotes do NuGet**. Na guia **Navegar**, pesquise e adicione os seguintes pacotes do NuGet ao projeto. Se você não conseguir localizá-los, marque a caixa de seleção **Incluir pré-lançamento**.

    ```
    Microsoft.Extensions.DependencyInjection
    Microsoft.Extensions.Configuration.AzureAppConfiguration
    Microsoft.FeatureManagement
    ```

1. Abra *Program.cs* e adicione as seguintes instruções:

    ```csharp
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    using Microsoft.FeatureManagement;
    using System.Threading.Tasks;
    ```

1. Atualize o método `Main` para se conectar à Configuração de Aplicativos, especificando a opção `UseFeatureFlags` para que os sinalizadores de recursos sejam recuperados. Em seguida, exiba uma mensagem se o sinalizador de recursos `Beta` estiver habilitado.

    ```csharp
        public static async Task Main(string[] args)
        {         
            IConfigurationRoot configuration = new ConfigurationBuilder()
                .AddAzureAppConfiguration(options =>
                {
                    options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                           .UseFeatureFlags();
                }).Build();

            IServiceCollection services = new ServiceCollection();

            services.AddSingleton<IConfiguration>(configuration).AddFeatureManagement();

            using (ServiceProvider serviceProvider = services.BuildServiceProvider())
            {
                IFeatureManager featureManager = serviceProvider.GetRequiredService<IFeatureManager>();

                if (await featureManager.IsEnabledAsync("Beta"))
                {
                    Console.WriteLine("Welcome to the beta!");
                }
            }

            Console.WriteLine("Hello World!");
            Console.WriteLine("Press any key to continue ...");
            Console.Read();
        }
    ```

## <a name="build-and-run-the-app-locally"></a>Compilar e executar o aplicativo localmente

1. Defina uma variável de ambiente chamada **ConnectionString** como a cadeia de conexão do repositório de Configuração de Aplicativos. Se você usar o prompt de comando do Windows, execute o seguinte comando:

    ```console
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Se você usa o Windows PowerShell, execute o comando a seguir:

    ```powershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

1. Reinicie o Visual Studio para permitir que a alteração tenha efeito. 

1. Pressione Ctrl + F5 para criar e executar o aplicativo do console.

    ![Aplicativo com sinalizador de recursos habilitado](./media/quickstarts/dotnet-app-feature-flag.png)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um sinalizador de recursos na Configuração de Aplicativos e o usou com um aplicativo de console do .NET Framework. Para saber como atualizar dinamicamente os sinalizadores de recursos e outros valores de configuração sem reiniciar o aplicativo, passe para o próximo tutorial.

> [!div class="nextstepaction"]
> [Habilitar configuração dinâmica](./enable-dynamic-configuration-dotnet.md)
