---
title: Adicionar suporte do Key Vault ao projeto do ASP.NET usando o Visual Studio - Azure Key Vault | Microsoft Docs
description: Use este tutorial para aprender a adicionar suporte do Key Vault para um aplicativo Web ASP.NET ou ASP.NET Core.
services: key-vault
author: ghogen
manager: jillfra
ms.service: key-vault
ms.custom: vs-azure, devx-track-csharp
ms.topic: how-to
ms.date: 08/07/2019
ms.author: ghogen
ms.openlocfilehash: 9c62534acdbfbff7fd4e718bad1f07a92c641626
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792388"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Adicione o Key Vault ao seu aplicativo Web usando os Serviços Conectados do Visual Studio

Neste tutorial, você aprenderá como adicionar facilmente tudo o que você precisa para começar a usar o Azure Key Vault para gerenciar seus segredos para projetos Web no Visual Studio, quer esteja usando o ASP.NET Core ou qualquer outro tipo de projeto do ASP.NET. Usando o recurso serviços conectados no Visual Studio, você pode fazer com que o Visual Studio adicione automaticamente todos os pacotes NuGet e definições de configuração que você precisa para se conectar ao Key Vault no Azure.

Para obter detalhes sobre as alterações que os Serviços Conectados realizam em seu projeto para habilitar o Key Vault, confira [Serviço Conectado do Key Vault — O que aconteceu com meu projeto do ASP.NET 4.7.1](#how-your-aspnet-framework-project-is-modified) ou [Serviço Conectado do Key Vault — O que aconteceu com meu projeto do ASP.NET Core](#how-your-aspnet-core-project-is-modified).

## <a name="prerequisites"></a>Pré-requisitos

- **Uma assinatura do Azure** . Se você não tiver uma assinatura, Inscreva-se para obter uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
- O **Visual Studio 2019 versão 16,3** ou posterior o [baixa agora](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).


## <a name="add-key-vault-support-to-your-project"></a>Como adicionar suporte do Key Vault ao seu projeto

Antes de começar, verifique se você está conectado ao Visual Studio. Entre com a mesma conta que você usa para sua assinatura do Azure. Em seguida, abra um ASP.NET 4.7.1 ou posterior, ou ASP.NET Core projeto Web 2,0, e execute as seguintes etapas:

1. No **Gerenciador de soluções** , clique com o botão direito do mouse no projeto ao qual você deseja adicionar o suporte de Key Vault e escolha **Adicionar**  >  **serviço conectado**  >  **Adicionar** .
   A página do Serviço Conectado aparece com os serviços que você pode adicionar ao seu projeto.
1. No menu de serviços disponíveis, escolha **Azure Key Vault** e clique em **Avançar** .

   ![Escolha "Azure Key Vault"](../media/vs-key-vault-add-connected-service/key-vault-connected-service.png)

1. Selecione a assinatura que você deseja usar e, em seguida, escolha uma Key Vault existente e clique em **concluir** . 

   ![Selecionar sua assinatura](../media/vs-key-vault-add-connected-service/key-vault-connected-service-select-vault.png)

Agora, a conexão com Key Vault é estabelecida e você pode acessar seus segredos no código. As próximas etapas são diferentes dependendo se você estiver usando o ASP.NET 4.7.1 ou ASP.NET Core.

## <a name="access-your-secrets-in-code-aspnet-core"></a>Acesse seus segredos no código (ASP.NET Core)

1. Abra um dos arquivos de paginação, como *index.cshtml.cs* e escreva o seguinte código:
   1. Inclua uma referência a `Microsoft.Extensions.Configuration` por isso usando a diretiva:

       ```csharp
       using Microsoft.Extensions.Configuration;
       ```

   1. Adicione a variável de configuração.

      ```csharp
      private static IConfiguration _configuration;
      ```

   1. Adicione este construtor ou substitua o Construtor existente por:

       ```csharp
       public IndexModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. Atualize o método `OnGet`. Atualize o valor do espaço reservado mostrado aqui com o nome do segredo que você criou nos comandos acima.

       ```csharp
       public void OnGet()
       {
           ViewData["Message"] = "My key val = " + _configuration["<YourSecretNameStoredInKeyVault>"];
       }
       ```

   1. Para confirmar o valor em tempo de execução, adicione o código para exibir o `ViewData["Message"]` arquivo *. cshtml* para exibir o segredo em uma mensagem.

      ```cshtml
          <p>@ViewData["Message"]</p>
      ```

Você pode executar o aplicativo localmente para verificar se o segredo foi obtido com êxito no Key Vault.

## <a name="access-your-secrets-aspnet"></a>Acesse seus segredos (ASP.NET)
Você pode configurar a configuração para que o arquivo de web.config tenha um valor fictício no `appSettings` elemento que é substituído pelo valor verdadeiro em tempo de execução. Você pode acessá-lo por meio da `ConfigurationManager.AppSettings` estrutura de dados.

1. Em Gerenciador de Soluções, clique com o botão direito do mouse em seu projeto e selecione gerenciar pacotes NuGet. Na guia procurar, localize e instale o [Microsoft.Configuration.ConfigurationBuilders. Azure](https://www.nuget.org/packages/Microsoft.Configuration.ConfigurationBuilders.Azure/)
 
1. Abra o arquivo web.config e escreva o código a seguir:
    1. Adicionar `configSections` e `configBuilders` :
        ```xml
         <configSections>
            <section
                name="configBuilders"
                type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a"
                restartOnExternalChanges="false"
                requirePermission="false" />
         </configSections>
         <configBuilders>
            <builders>
            <add
                    name="AzureKeyVault"
                    vaultName="vaultname"
                    type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Azure, Version=1.0.0.0, Culture=neutral"
                    vaultUri="https://vaultname.vault.azure.net" />
            </builders>
         </configBuilders>
        ```
    1. Localize a marca appSettings, adicione um atributo `configBuilders="AzureKeyVault"` e adicione uma linha:
        ```xml
         <add key="<secretNameInYourKeyVault>" value="dummy"/>
        ```

1. Edite o `About` método em *HomeController.cs* para exibir o valor de confirmação.

   ```csharp
   public ActionResult About()
   {
       ViewBag.Message = "Key vault value = " + ConfigurationManager.AppSettings["<secretNameInYourKeyVault>"];
   }
   ```
1. Execute o aplicativo localmente no depurador, alterne para a guia **sobre** e verifique se o valor da Key Vault é exibido.

## <a name="troubleshooting"></a>Solução de problemas

Se o Key Vault estiver sendo executado em um conta Microsoft diferente daquele em que você está conectado ao Visual Studio (por exemplo, o Key Vault está sendo executado em sua conta corporativa, mas o Visual Studio está usando sua conta privada), você recebe um erro no arquivo Program.cs, que o Visual Studio não pode obter acesso ao Key Vault. Para corrigir esse problema:

1. Vá para a [portal do Azure](https://portal.azure.com) e abra o Key Vault.

1. Escolha **políticas de acesso** e, em seguida, **Adicionar política de acesso** e escolha a conta com a qual você fez logon como principal.

1. No Visual Studio, escolha **arquivo**  >  **configurações de conta** .
Selecione **Adicionar uma conta** na seção **todas as contas** . Entre com a conta que você escolheu como entidade de segurança da sua política de acesso.

1. Escolha **ferramentas**  >  **Opções** e procure autenticação de **serviço do Azure** . Em seguida, selecione a conta que você acabou de adicionar ao Visual Studio.

Agora, quando você depura seu aplicativo, o Visual Studio se conecta à conta em que seu Key Vault está localizado.

## <a name="how-your-aspnet-core-project-is-modified"></a>Como seu projeto de ASP.NET Core é modificado

Esta seção identifica as alterações exatas feitas em um projeto ASP.NET ao adicionar o serviço conectado do Key Vault usando o Visual Studio.

### <a name="added-references-for-aspnet-core"></a>Referências adicionadas para ASP.NET Core

Afeta as referências de .NET do arquivo de projeto e as referências de pacote NuGet.

| Type | Referência |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

### <a name="added-files-for-aspnet-core"></a>Arquivos adicionados para ASP.NET Core

- `ConnectedService.json` adicionado, que registra algumas informações sobre o provedor de serviços conectado, a versão e um link da documentação.

### <a name="project-file-changes-for-aspnet-core"></a>Alterações de arquivo de projeto para ASP.NET Core

- Adicionado o grupo e o arquivo dos serviços conectados `ConnectedServices.json` .

### <a name="launchsettingsjson-changes-for-aspnet-core"></a>launchsettings.jssobre alterações para ASP.NET Core

- Adição das seguintes entradas de variável de ambiente ao perfil do IIS Express e ao perfil que coincide com o nome do seu projeto da Web:

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure-for-aspnet-core"></a>Alterações no Azure para ASP.NET Core

- Criação de um grupo de recursos (ou uso de um existente).
- Criação de um Key Vault no grupo de recursos especificado.

## <a name="how-your-aspnet-framework-project-is-modified"></a>Como seu projeto do ASP.NET Framework é modificado

Esta seção identifica as alterações exatas feitas em um projeto ASP.NET ao adicionar o serviço conectado do Key Vault usando o Visual Studio.

### <a name="added-references-for-aspnet-framework"></a>Referências adicionadas para a estrutura ASP.NET

Afeta o arquivo de projeto referências do .NET e `packages.config` (referências do NuGet).

| Type | Referência |
| --- | --- |
| .NET; NuGet | Azure. Identity |
| .NET; NuGet | Azure. Security. keyvault. Keys |
| .NET; NuGet | Azure. Security. keyvault. segredos |

> [!IMPORTANT] 
> Por padrão, o Azure. Identity 1.1.1 está instalado, o que não oferece suporte à credencial do Visual Studio. Você pode atualizar a referência do pacote manualmente para a 1.2 + usar a credencial do Visual Studio.

### <a name="added-files-for-aspnet-framework"></a>Arquivos adicionados para a estrutura ASP.NET

- `ConnectedService.json` adicionado, que registra algumas informações sobre o provedor de serviços conectado, a versão e um link para a documentação.

### <a name="project-file-changes-for-aspnet-framework"></a>Alterações de arquivo de projeto para a estrutura ASP.NET

- Adicione o ItemGroup dos Serviços Conectados e o arquivo ConnectedServices.json.
- Referências aos assemblies .NET descritos na seção [Referências adicionadas](#added-references-for-aspnet-framework).

## <a name="next-steps"></a>Próximas etapas

Se você seguiu este tutorial, suas permissões de Key Vault são configuradas para serem executadas com sua própria assinatura do Azure, mas isso pode não ser desejável para um cenário de produção. Você pode criar uma identidade gerenciada para gerenciar o acesso de Key Vault para seu aplicativo. Consulte [como autenticar para Key Vault](./authentication.md) e [atribuir uma política de acesso de Key Vault](./assign-access-policy-portal.md).

Saiba mais sobre o desenvolvimento de Key Vault lendo o [Guia do desenvolvedor do Key Vault](developers-guide.md).