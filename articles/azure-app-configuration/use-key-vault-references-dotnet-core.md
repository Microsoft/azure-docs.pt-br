---
title: Tutorial para o uso de referências do Key Vault da Configuração de Aplicativos do Azure em um aplicativo ASP.NET Core | Microsoft Docs
description: Neste tutorial, você aprenderá a usar as referências do Key Vault da Configuração de Aplicativos do Azure de um aplicativo ASP.NET Core
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/08/2020
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 57abbeefe8e3f2abe527f2b282d643db766b9dc9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775737"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>Tutorial: Usar referências do Key Vault em um aplicativo ASP.NET Core

Neste tutorial, você aprenderá a usar o serviço de Configuração de Aplicativos do Azure junto com o Azure Key Vault. A Configuração de Aplicativos e o Key Vault são serviços complementares usados lado a lado na maioria das implantações de aplicativo.

A Configuração de Aplicativos ajuda você a usar os serviços juntos por meio da criação de chaves que fazem referência a valores armazenados no Key Vault. Quando a Configuração de Aplicativos cria essas chaves, ela armazena os URIs de valores do Key Vault em vez dos valores propriamente ditos.

Seu aplicativo usa o provedor do cliente da Configuração de Aplicativos para recuperar referências do Key Vault, assim como faz para quaisquer outras chaves armazenadas na Configuração de Aplicativos. Nesse caso, os valores armazenados na Configuração de Aplicativos são URIs que fazem referência aos valores no Key Vault. Eles não são valores nem credenciais do Key Vault. Já que o provedor do cliente reconhece as chaves como referências do Key Vault, ele usa o Key Vault para recuperar os valores delas.

Seu aplicativo é responsável por autenticar corretamente, tanto na Configuração de Aplicativos quanto no Key Vault. Os dois serviços não se comunicam diretamente.

Este tutorial mostra a você como implementar referências do Key Vault em seu código. Ele se baseia no aplicativo Web introduzido nos Inícios Rápidos. Antes de continuar, conclua [Criar um aplicativo ASP.NET Core com a Configuração de Aplicativo](./quickstart-aspnet-core-app.md) primeiro.

Você pode usar qualquer editor de código para executar as etapas deste tutorial. Por exemplo, o [Visual Studio Code](https://code.visualstudio.com/) é um editor de código multiplataforma disponível para os sistemas operacionais Windows, macOS e Linux.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma chave da Configuração de Aplicativos que referencia um valor armazenado no Key Vault.
> * Acessar o valor dessa chave por meio de um aplicativo Web ASP.NET Core.

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, instale o [SDK do .NET Core](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>Criar um cofre

1. Selecione a opção **Criar um recurso** no canto superior esquerdo do portal do Azure:

    ![A captura de tela mostra a opção Criar um recurso no portal do Azure.](./media/quickstarts/search-services.png)
1. Digite **Key Vault** na caixa de pesquisa.
1. Na lista de resultados, selecione **Cofres de chaves** à esquerda.
1. Em **Cofres de chaves**, selecione **Adicionar**.
1. À direita, na seção **Criar cofre de chaves**, forneça as seguintes informações:
    - Selecione **Assinatura** para escolher uma assinatura.
    - Em **Grupo de Recursos**, selecione **Criar novo** e digite um nome para o grupo de recursos.
    - Em **Nome do cofre de chaves**, é necessário um nome exclusivo. Para este tutorial, insira **Contoso-vault2**.
    - Na lista suspensa **Região**, escolha uma localização.
1. Deixe as outras opções de **Criar cofre de chaves** com os valores padrão.
1. Selecione **Criar**.

Nesse ponto, sua conta do Azure é a única autorizada a acessar esse novo cofre.

![A captura de tela mostra o cofre de chaves.](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Adicionar um segredo ao Key Vault

Para adicionar um segredo ao cofre, basta executar algumas etapas adicionais. Nesse caso, adicione uma mensagem que você possa usar para testar a recuperação do Key Vault. A mensagem é chamada de **Mensagem** e você armazena nela o valor de "Olá do Key Vault".

1. Na página de propriedades do Key Vault, selecione **Segredos**.
1. Selecione **Gerar/Importar**.
1. No painel **Criar um segredo**, insira os seguintes valores:
    - **Opções de upload**: insira **Manual**.
    - **Name**: insira **Mensagem**.
    - **Valor**: insira **Olá do Key Vault**.
1. Deixe as outras propriedades de **Criar um segredo** com os valores padrão.
1. Selecione **Criar**.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Adicionar uma referência do Key Vault à Configuração de Aplicativos

1. Entre no [portal do Azure](https://portal.azure.com). Escolha **Todos os recursos** e depois escolha a instância do repositório de Configurações de Aplicativos que você criou no início rápido.

1. Selecione **Gerenciador de Configurações**.

1. Clique em **+ Criar** > **Referência do Key Vault** e especifique os seguintes valores:
    - **Chave**: selecione **TestApp:Settings:KeyVaultMessage**.
    - **Rótulo**: deixe esse valor em branco.
    - **Assinatura**, **Grupo de recursos** e **Cofre de chaves**: Insira os valores correspondentes àqueles no cofre de chaves que você criou na seção anterior.
    - **Segredo**: selecione o segredo chamado **Mensagem** criado na seção anterior.

## <a name="connect-to-key-vault"></a>Conectar-se ao Key Vault

1. Neste tutorial, você usará uma entidade de serviço para autenticação no Key Vault. Para criar uma entidade de serviço, use o comando [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) da CLI do Azure:

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Essa operação retorna uma série de pares de chave/valor:

    ```console
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. Execute o comando a seguir para permitir que a entidade de serviço acesse o cofre de chaves:

    ```cmd
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
    ```

1. Adicione variáveis de ambiente para armazenar os valores de *clientId*, *clientSecret* e *tenantId*.

    #### <a name="windows-command-prompt"></a>[Prompt de comando do Windows](#tab/cmd)

    ```cmd
    setx AZURE_CLIENT_ID <clientId-of-your-service-principal>
    setx AZURE_CLIENT_SECRET <clientSecret-of-your-service-principal>
    setx AZURE_TENANT_ID <tenantId-of-your-service-principal>
    ```

    #### <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```PowerShell
    $Env:AZURE_CLIENT_ID = <clientId-of-your-service-principal>
    $Env:AZURE_CLIENT_SECRET = <clientSecret-of-your-service-principal>
    $Env:AZURE_TENANT_ID = <tenantId-of-your-service-principal>
    ```

    #### <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    export AZURE_CLIENT_ID = <clientId-of-your-service-principal>
    export AZURE_CLIENT_SECRET = <clientSecret-of-your-service-principal>
    export AZURE_TENANT_ID = <tenantId-of-your-service-principal>
    ```

    ---

    > [!NOTE]
    > Essas credenciais do Key Vault são usadas somente dentro do seu aplicativo. Seu aplicativo é autenticado diretamente para o Key Vault com essas credenciais. Elas nunca são passadas para o serviço de Configuração de Aplicativos.

1. Reinicie o terminal para carregar essas novas variáveis de ambiente.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Atualizar o código para usar uma referência do Key Vault

1. Adicione uma referência aos pacotes NuGet necessários executando o seguinte comando:

    ```dotnetcli
    dotnet add package Azure.Identity
    ```

1. Abra *Program.cs* e adicione referências aos seguintes pacotes necessários:

    ```csharp
    using Azure.Identity;
    ```

1. Atualize o método `CreateWebHostBuilder` para usar a Configuração de Aplicativos chamando o método `config.AddAzureAppConfiguration`. Inclua a opção `ConfigureKeyVault` e transmita as credenciais corretas para o Key Vault.

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
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>();
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
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>());
    ```

1. Ao inicializar a conexão com a Configuração de Aplicativos, você configura a conexão com o Key Vault chamando o método `ConfigureKeyVault`. Após a inicialização, você pode acessar os valores de referências do Key Vault da mesma maneira que acessa os valores de chaves comuns da Configuração de Aplicativos.

    Para ver esse processo em ação, abra *Index.cshtml* no diretório **Exibições** > **Página Inicial**. Substitua o conteúdo pelo código a seguir:

    ```html
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

    <h1>@Configuration["TestApp:Settings:Message"]
        and @Configuration["TestApp:Settings:KeyVaultMessage"]</h1>
    ```

    Você acessa o valor da referência do Key Vault **TestApp:Settings:KeyVaultMessage** da mesma maneira que acessa o valor de configuração **TestApp:Settings:Message**.

## <a name="build-and-run-the-app-locally"></a>Compilar e executar o aplicativo localmente

1. Para criar o aplicativo usando a CLI do .NET Core, execute o seguinte comando no shell de comando:

    ```dotnetcli
    dotnet build
    ```

1. Depois que o build é concluído, use o comando a seguir para executar o aplicativo Web localmente:

    ```dotnetcli
    dotnet run
    ```

1. Abra uma janela do navegador e vá para `http://localhost:5000`, que é a URL padrão do aplicativo Web hospedado localmente.

    ![Inicialização de aplicativo local de início rápido](./media/key-vault-reference-launch-local.png)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou uma chave da Configuração de Aplicativos que referencia um valor armazenado no Key Vault. Para saber como adicionar uma identidade de serviço gerenciada pelo Azure que simplifica o acesso à Configuração de Aplicativos e ao Key Vault, prossiga para o próximo tutorial.

> [!div class="nextstepaction"]
> [Integração de identidade gerenciada](./howto-integrate-azure-managed-service-identity.md)
