---
title: Integrar a Configuração de Aplicativos do Azure usando um pipeline de integração e entrega contínuas
description: Saiba como implementar a integração e a entrega contínuas usando a Configuração de Aplicativos do Azure
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.date: 04/19/2020
ms.author: alkemper
ms.openlocfilehash: 3a4d171f0e3225db195c5c2b71ca99a3386e3a36
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99979837"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integrar com um pipeline de CI/CD

Este artigo explica como usar dados da Configuração de Aplicativos do Azure em um sistema de integração e implantação contínuas.

## <a name="use-app-configuration-in-your-azure-devops-pipeline"></a>Usar a Configuração de Aplicativos no pipeline do Azure DevOps

Se você tiver um pipeline do Azure DevOps, efetue fetch de valores de chave na Configuração de Aplicativos e defina-as como variáveis de tarefa. A [extensão de DevOps do Configuração de Aplicativos do Azure](https://go.microsoft.com/fwlink/?linkid=2091063) é um módulo complementar que fornece essa funcionalidade. Siga as instruções para usar a extensão em uma sequência de tarefas de versão ou build.

## <a name="deploy-app-configuration-data-with-your-application"></a>Implantar dados da Configuração de Aplicativos com seu aplicativo

Seu aplicativo poderá falhar ao executar se depender da Configuração de Aplicativos do Azure e não puder acessá-la. Aprimore a resiliência do seu aplicativo empacotando os dados de configuração em um arquivo implantado com o aplicativo e carregado localmente durante a inicialização do aplicativo. Essa abordagem garante que o seu aplicativo tenha valores de configuração padrão na inicialização. Esses valores são substituídos por alterações mais recentes em um repositório de Configuração de Aplicativos quando ele está disponível.

Usando a função [Exportar](./howto-import-export-data.md#export-data) da Configuração de Aplicativos do Azure, você pode automatizar o processo de recuperação de dados de configuração atuais como um único arquivo. Em seguida, insira esse arquivo em uma etapa de build ou implantação no pipeline de CI/CD (integração e implantação contínuas).

O exemplo a seguir mostra como incluir dados de Configuração de Aplicativos como uma etapa de build para o aplicativo Web introduzido nos guias de início rápido. Antes de continuar, conclua [Criar um aplicativo ASP.NET Core com a Configuração de Aplicativo](./quickstart-aspnet-core-app.md) primeiro.

Você pode usar qualquer editor de código para executar as etapas deste tutorial. O [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção disponível nas plataformas Windows, macOS e Linux.

### <a name="prerequisites"></a>Pré-requisitos

Se você compilar localmente, baixe e instale a [CLI do Azure](/cli/azure/install-azure-cli) se ainda não tiver feito isso.

Para fazer um build de nuvem com o Azure DevOps, por exemplo, assegure que a [CLI do Azure](/cli/azure/install-azure-cli) esteja instalada em seu sistema de build.

### <a name="export-an-app-configuration-store"></a>Exportar um repositório de Configuração de Aplicativos

1. Abra seu arquivo *.csproj* e adicione o seguinte script:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -d file --path $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```
1. Abra *Program.cs* e atualize o método `CreateWebHostBuilder` para usar o arquivo JSON exportado chamando o método `config.AddJsonFile()`.  Adicione o namespace `System.Reflection` também.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var directory = System.IO.Path.GetDirectoryName(Assembly.GetExecutingAssembly().Location);
                var settings = config.Build();

                config.AddJsonFile(Path.Combine(directory, "azureappconfig.json"));
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
            })
            .UseStartup<Startup>();
    ```

### <a name="build-and-run-the-app-locally"></a>Compilar e executar o aplicativo localmente

1. Defina uma variável de ambiente chamada **ConnectionString** e defina-a como a chave de acesso ao repositório de Configuração de Aplicativos. 
    Se você usar o prompt de comando do Windows, execute o comando a seguir e reinicie o prompt de comando para permitir que a alteração entre em vigor:

    ```console
     setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Se você usa o Windows PowerShell, execute o comando a seguir:

    ```powershell
     $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Se você usa macOS ou Linux, execute o comando a seguir:

    ```console
     export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. Para criar o aplicativo usando a CLI do .NET Core, execute o seguinte comando no shell de comando:

    ```console
     dotnet build
    ```

3. Depois que a construção for concluída com êxito, execute o seguinte comando para executar o aplicativo Web localmente:

    ```console
     dotnet run
    ```

4. Abra uma janela do navegador e vá para `http://localhost:5000`, que é a URL padrão do aplicativo Web hospedado localmente.

    ![Inicialização local do aplicativo do Início Rápido](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você exportou os dados de Configuração de Aplicativos do Azure a serem usados em um pipeline de implantação. Para saber mais sobre como usar a Configuração de Aplicativo, continue para ver as amostras da CLI do Azure.

> [!div class="nextstepaction"]
> [CLI do Azure](/cli/azure/appconfig)
