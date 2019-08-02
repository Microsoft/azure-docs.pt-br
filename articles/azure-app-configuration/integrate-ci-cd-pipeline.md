---
title: Tutorial para a integração com um pipeline de integração contínua e entrega por meio da Configuração de Aplicativos do Azure | Microsoft Docs
description: Neste tutorial, você aprenderá como gerar um arquivo de configuração usando os dados na Configuração de Aplicativos do Azure durante a integração e entrega contínuas
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: e2f682a2782eb1a61dd44e02d665175e31c441f8
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68357023"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integrar com um pipeline de CI/CD

Este artigo descreve várias maneiras de usar dados da Configuração de Aplicativos do Azure em um sistema de integração e implantação contínuas.

## <a name="use-app-configuration-in-your-azure-devops-pipeline"></a>Usar a Configuração de Aplicativos no pipeline do Azure DevOps

Se você tiver um pipeline do Azure DevOps, efetue fetch de valores de chave na Configuração de Aplicativos e defina-as como variáveis de tarefa. A [extensão de DevOps do Configuração de Aplicativos do Azure](https://go.microsoft.com/fwlink/?linkid=2091063) é um módulo complementar que fornece essa funcionalidade. Basta seguir as instruções para usar a extensão em uma sequência de tarefas de versão ou build.

## <a name="deploy-app-configuration-data-with-your-application"></a>Implantar dados da Configuração de Aplicativos com seu aplicativo

Seu aplicativo poderá falhar ao executar se depender da Configuração de Aplicativos do Azure e não puder acessá-la. Você pode melhorar a resiliência de seu aplicativo para lidar com um evento desse tipo, embora seja improvável que isso aconteça. Para fazer isso, empacote os dados de configuração atuais em um arquivo que é implantado com o aplicativo e carregado localmente durante sua inicialização. Essa abordagem garante que seu aplicativo tenha, pelo menos, valores de configuração padrão. Esses valores são substituídos por alterações mais recentes em um repositório de configurações de aplicativos quando ele está disponível.

Usando a função [Exportar](./howto-import-export-data.md#export-data) da Configuração de Aplicativos do Azure, você pode automatizar o processo de recuperação de dados de configuração atuais como um único arquivo. Em seguida, insira esse arquivo em uma etapa de build ou implantação em seu pipeline de CI/CD (integração contínua e implantação contínua).

O exemplo a seguir mostra como incluir dados de Configuração de Aplicativos como uma etapa de build para o aplicativo Web introduzido nos guias de início rápido. Antes de continuar, conclua [Criar um aplicativo ASP.NET Core com a Configuração de Aplicativo](./quickstart-aspnet-core-app.md) primeiro.

Você pode usar qualquer editor de código para executar as etapas deste tutorial. O [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção disponível nas plataformas Windows, macOS e Linux.

### <a name="prerequisites"></a>Pré-requisitos

Se você compilar localmente, baixe e instale a [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) se ainda não tiver feito isso.

Para fazer um build de nuvem com o Azure DevOps, por exemplo, assegure que a [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) esteja instalada em seu sistema de build.

### <a name="export-an-app-configuration-store"></a>Exportar um repositório de configurações de aplicativos

1. Abra seu arquivo *.csproj* e adicione o seguinte script:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -d file --path $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```

    Adicione o *ConnectionString* associado a seu repositório de configurações de aplicativos como uma variável de ambiente.

2. Abra *Program.cs* e atualize o método `CreateWebHostBuilder` para usar o arquivo JSON exportado chamando o método `config.AddJsonFile()`.

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

1. Defina uma variável de ambiente chamada **ConnectionString** e defina-a como a chave de acesso ao repositório de configurações de aplicativo. Se você usar o prompt de comando do Windows, execute o comando a seguir e reinicie o prompt de comando para permitir que a alteração entre em vigor:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Se você usa o Windows PowerShell, execute o comando a seguir:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Se você usa macOS ou Linux, execute o comando a seguir:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Para criar o aplicativo usando a CLI do .NET Core, execute o seguinte comando no shell de comando:

        dotnet build

3. Depois que a construção for concluída com êxito, execute o seguinte comando para executar o aplicativo Web localmente:

        dotnet run

4. Abra uma janela do navegador e vá para `http://localhost:5000`, que é a URL padrão do aplicativo Web hospedado localmente.

    ![Inicialização local do aplicativo do Início Rápido](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você exportou os dados de Configuração de Aplicativos do Azure a serem usados em um pipeline de implantação. Para saber mais sobre como usar a Configuração de Aplicativo, continue para ver as amostras da CLI do Azure.

> [!div class="nextstepaction"]
> [Integração de identidade gerenciada](./howto-integrate-azure-managed-service-identity.md)
