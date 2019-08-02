---
title: Tutorial - Usar o Azure Key Vault com um aplicativo Web do Azure no .NET | Microsoft Docs
description: Neste tutorial, você vai configurar um aplicativo do ASP.NET Core para ler um segredo no cofre de chaves.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 805893d6b5756c76ebc523187e0ed75c9fe24ed9
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67874754"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-web-app-in-net"></a>Tutorial: Usar o Azure Key Vault com um aplicativo Web do Azure no .NET

O Azure Key Vault ajuda a proteger segredos, como chaves de API e cadeias de conexão de banco de dados. Ele fornece acesso aos seus aplicativos, serviços e recursos de TI.

Neste tutorial, você aprenderá a criar um aplicativo Web do Azure que possa ler informações do Azure Key Vault. O processo usa identidades gerenciadas para recursos do Azure. Para obter mais informações sobre aplicativos Web do Azure, confira [Serviço de Aplicativo do Azure](../app-service/overview.md).

Este tutorial mostra como:

> [!div class="checklist"]
> * Crie um cofre da chave.
> * Adicionar um segredo ao cofre de chaves.
> * Recuperar um segredo do cofre de chaves.
> * Criar um aplicativo Web do Azure.
> * Habilitar uma identidade gerenciada para o aplicativo Web.
> * Atribuir permissão para o aplicativo Web.
> * Executar o aplicativo Web no Azure.

Antes de começar, leia [Conceitos básicos do Key Vault](key-vault-whatis.md#basic-concepts). 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Pré-requisitos

* Para Windows: [SDK do .NET Core 2.1 ou posterior](https://www.microsoft.com/net/download/windows)
* Para Mac: [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/)
* Para Windows, Mac e Linux:
  * [Git](https://git-scm.com/downloads)
  * Este tutorial requer que você execute a CLI do Azure localmente. Você deve ter a CLI do Azure versão 2.0.4 ou posterior instalada. Execute `az --version` para encontrar a versão. Caso precise instalar ou atualizar a CLI, confira [Instalar a CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="about-managed-service-identity"></a>Sobre a Identidade de Serviço Gerenciada

O Azure Key Vault armazena credenciais com segurança para que elas não sejam exibidas no seu código. No entanto, você precisa se autenticar no Azure Key Vault para recuperar as chaves. Para fazer a autenticação no Key Vault, você precisa de uma credencial. Esse é um dilema clássico de inicialização. A MSI (Identidade de Serviço Gerenciada) resolve esse problema fornecendo uma _identidade de inicialização_ que simplifica o processo.

Ao habilitar a MSI para um serviço do Azure, como Máquinas Virtuais do Azure, Serviço de Aplicativo do Azure ou Azure Functions, o Azure cria um [serviço principal](key-vault-whatis.md#basic-concepts). A MSI faz isso para a instância do serviço no Azure AD (Azure Active Directory) e injeta as credenciais da entidade de serviço nessa instância.

![Diagrama da MSI](media/MSI.png)

Em seguida, para obter um token de acesso, o código chama um serviço de metadados local disponível no recurso do Azure. O código usa o token de acesso obtido do ponto de extremidade da MSI local para se autenticar em um serviço do Azure Key Vault.

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Para fazer logon no Azure usando a CLI do Azure, digite:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos usando o comando [az group create](/cli/azure/group#az-group-create).

Em seguida, selecione um nome para o grupo de recursos e preencha o espaço reservado. O exemplo a seguir cria um grupo de recursos no local Oeste dos EUA:

   ```azurecli
   # To list locations: az account list-locations --output table
   az group create --name "<YourResourceGroupName>" --location "West US"
   ```

Você usará esse grupo de recursos durante este tutorial.

## <a name="create-a-key-vault"></a>Criar um cofre de chave

Para criar um cofre de chaves em seu grupo de recursos, forneça as seguintes informações:

* Nome do cofre de chaves: uma cadeia de caracteres de 3 a 24 caracteres contendo somente números (0 a 9), letras (a a z, A a Z) e hifens (-)
* Nome do grupo de recursos
* Localização: **Oeste dos EUA**

Na CLI do Azure, digite o seguinte comando:

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

Nesse ponto, sua conta do Azure é a única autorizada a executar qualquer operação nesse novo cofre.

## <a name="add-a-secret-to-the-key-vault"></a>Adicionar um segredo ao cofre de chaves

Você já pode adicionar um segredo. O segredo pode ser uma cadeia de conexão SQL ou qualquer outra informação que você precise manter em segurança e disponível para o aplicativo.

Para criar um segredo no cofre de chaves chamado **AppSecret**, insira o seguinte comando: 

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Esse segredo armazena o valor **MySecret**.

Para exibir o valor contido no segredo como texto sem formatação, insira o seguinte comando:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Esse comando mostra as informações secretas, incluindo o URI. 

Após concluir essas etapas, você deverá ter um URI para um segredo em um cofre de chaves. Anote essas informações para usar posteriormente neste tutorial. 

## <a name="create-a-net-core-web-app"></a>Criar um aplicativo Web do .NET Core

Para criar um aplicativo Web .NET Core e publicá-lo no Azure, siga as instruções em [Criar um aplicativo Web ASP.NET Core no Azure](../app-service/app-service-web-get-started-dotnet.md). 

Você também pode assistir a esse vídeo:

>[!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>Abrir e editar a solução

1. Navegue até o arquivo **Pages** > **About.cshtml.cs**.

1. Instale estes pacotes do NuGet:
   - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
   - [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)

1. Importe o código a seguir no arquivo *About.cshtml.cs*:

   ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
   ```

   O código na classe AboutModel deve ter esta aparência:

   ```csharp
    public class AboutModel : PageModel
    {
        public string Message { get; set; }

        public async Task OnGetAsync()
        {
            Message = "Your application description page.";
            int retries = 0;
            bool retry = false;
            try
            {
                /* The next four lines of code show you how to use AppAuthentication library to fetch secrets from your key vault */
                AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                KeyVaultClient keyVaultClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                var secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                        .ConfigureAwait(false);
                Message = secret.Value;
            }
            /* If you have throttling errors see this tutorial https://docs.microsoft.com/azure/key-vault/tutorial-net-create-vault-azure-web-app */
            /// <exception cref="KeyVaultErrorException">
            /// Thrown when the operation returned an invalid status code
            /// </exception>
            catch (KeyVaultErrorException keyVaultException)
            {
                Message = keyVaultException.Message;
            }
        }

        // This method implements exponential backoff if there are 429 errors from Azure Key Vault
        private static long getWaitTime(int retryCount)
        {
            long waitTime = ((long)Math.Pow(2, retryCount) * 100L);
            return waitTime;
        }

        // This method fetches a token from Azure Active Directory, which can then be provided to Azure Key Vault to authenticate
        public async Task<string> GetAccessTokenAsync()
        {
            var azureServiceTokenProvider = new AzureServiceTokenProvider();
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
            return accessToken;
        }
    }
    ```

## <a name="run-the-web-app"></a>Executar o aplicativo Web

1. No menu principal do Visual Studio 2019, escolha **Depurar** > **Iniciar** com ou sem depuração. 
1. No navegador, vá até a página **Sobre**.  
    O valor para o **AppSecret** é exibido.

## <a name="enable-a-managed-identity"></a>Habilitar uma identidade gerenciada

O Azure Key Vault fornece uma maneira de armazenar as credenciais e outros segredos com segurança, mas seu código precisa ser autenticado no Key Vault para recuperá-los. A [Visão geral das identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) ajuda a solucionar esse problema fornecendo aos serviços do Azure uma identidade gerenciada automaticamente no Azure AD. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter que todas as credenciais no seu código.

Na CLI do Azure, execute o comando de atribuir identidade para criar a identidade para esse aplicativo:

```azurecli
az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"
```

Substitua \<NomeDoSeuAplicativo> pelo nome do aplicativo publicado no Azure.  
    Por exemplo, se o nome do aplicativo publicado era **MyAwesomeapp.azurewebsites.net**, substitua \<NomeDoSeuAplicativo> por **MyAwesomeapp**.

Anote `PrincipalId` ao publicar o aplicativo no Azure. A saída do comando na etapa 1 deve estar no formato a seguir:

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

>[!NOTE]
>O comando neste procedimento é o equivalente a acessar o [portal do Azure](https://portal.azure.com) e mudar a configuração **Identidade / Atribuída pelo sistema** para **Ativada** nas propriedades do aplicativo Web.

## <a name="assign-permissions-to-your-app"></a>Conceder permissões ao aplicativo

Substitua o \<NomeDoSeucofredechaves> pelo nome do seu cofre de chaves e a \<IDPrincipal> pelo valor da **IDPrincipal** no comando a seguir:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list
```

Esse comando dá permissões para a identidade (MSI) do serviço de aplicativo para executar operações **get** e **list** no cofre de chaves.

## <a name="publish-the-web-app-to-azure"></a>Publicar aplicativo Web no Azure

Publique seu aplicativo Web no Azure mais uma vez para verificar se ele consegue buscar o valor do segredo.

1. No Visual Studio, selecione o projeto **key-vault-dotnet-core-quickstart**.
2. Selecione **Publicar** > **Iniciar**.
3. Selecione **Criar**.

Ao executar o aplicativo, você verá que ele é capaz de recuperar o valor do segredo.

Você acabou de criar com êxito um aplicativo Web no .NET, o qual armazena e agrupa seus segredos de seu cofre de chaves.

## <a name="clean-up-resources"></a>Limpar recursos
Quando eles não forem mais necessários, exclua a máquina virtual e o cofre de chaves.

## <a name="next-steps"></a>Próximas etapas

>[!div class="nextstepaction"]
>[Guia do Desenvolvedor do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-developers-guide)
