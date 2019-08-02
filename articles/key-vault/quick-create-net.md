---
title: 'Início Rápido: Definir e recuperar um segredo do Azure Key Vault usando um aplicativo Web do .NET – Azure Key Vault | Microsoft Docs'
description: Neste início rápido, você define e recupera um segredo do Azure Key Vault usando um aplicativo Web do .NET
services: key-vault
author: msmbaldwin
manager: sumedhb
ms.service: key-vault
ms.topic: quickstart
ms.date: 01/02/2019
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: 4f9fff41e4b9043c271d656583fb8b9a11ff3a7a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052792"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-by-using-a-net-web-app"></a>Início Rápido: Definir e recuperar um segredo do Azure Key Vault usando um aplicativo Web do .NET

Neste tutorial, siga as etapas para obter um aplicativo Web do Azure para ler informações do Azure Key Vault usando identidades gerenciadas dos recursos do Azure. Usar o Key Vault ajuda a proteger as informações. Você aprenderá como:

* Crie um cofre da chave.
* Armazenar um segredo no cofre de chaves.
* Recuperar um segredo do cofre de chaves.
* Criar um aplicativo Web do Azure.
* Habilite uma [identidade de serviço gerenciada](../active-directory/managed-identities-azure-resources/overview.md) para o aplicativo Web.
* Conceder as permissões necessárias para o aplicativo Web ler dados do cofre de chaves.

Antes de continuarmos, leia os [conceitos básicos para o Key Vault](key-vault-whatis.md#basic-concepts).

>[!NOTE]
>O Key Vault é um repositório central para armazenar segredos de forma programática. Mas, para fazer isso, os aplicativos e usuários precisam autenticar primeiro o Key Vault, ou seja, introduzir um segredo. Seguindo as melhores práticas de segurança, esse primeiro segredo deve ser girado periodicamente. 
>
>Com as [identidades de serviço gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md), os aplicativos executados no Azure obtêm uma identidade gerenciada automaticamente pelo Azure. Isso ajuda a resolver o *problema de introdução do segredo* para que os usuários e aplicativos possam seguir as melhores práticas e não precisem se preocupar em girar o primeiro segredo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

* No Windows:
  * [Visual Studio 2019](https://www.microsoft.com/net/download/windows) com as cargas de trabalho a seguir:
    * Desenvolvimento Web e ASP.NET
    * Desenvolvimento multiplataforma com o .NET Core
  * [SDK do .NET Core 2.1 ou posterior](https://www.microsoft.com/net/download/windows)

* No Mac:
  * Confira as [Novidades do Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).

* Todas as plataformas:
  * Git ([download](https://git-scm.com/downloads)).
  * Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
  * [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) versão 2.0.4 ou posterior. Disponível para Windows, Mac e Linux.

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Para fazer logon no Azure usando a CLI do Azure, digite:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos usando o comando [az group create](/cli/azure/group#az-group-create). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

Selecione um nome para o grupo de recursos e preencha o espaço reservado.
O exemplo a seguir cria um grupo de recursos no local Leste dos EUA:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

O grupo de recursos que você acabou de criar é usado ao longo deste artigo.

## <a name="create-a-key-vault"></a>Criar um cofre de chave

Em seguida, você cria um cofre de chaves no grupo de recursos criado na etapa anterior. Forneça as seguintes informações:

* Nome do cofre de chaves: O nome precisa ser uma cadeia de 3 a 24 caracteres e conter apenas 0-9, a-z, A-Z e um hífen (-).
* Nome do grupo de recursos.
* Localização: **Leste dos EUA**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

Nesse ponto, sua conta do Azure é a única autorizada a executar qualquer operação nesse novo cofre.

## <a name="add-a-secret-to-the-key-vault"></a>Adicionar um segredo ao cofre de chaves

Estamos adicionando um segredo para ajudar a ilustrar como isso funciona. Você pode estar armazenando uma cadeia de conexão SQL ou qualquer outra informação que precise manter com segurança, mas disponibilizar para o aplicativo.

Digite os comandos a seguir para criar um segredo no cofre de chaves chamado **AppSecret**. Esse segredo armazenará o valor **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Para exibir o valor contido no segredo como texto sem formatação:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Esse comando mostra as informações secretas, incluindo o URI. Após concluir essas etapas, você deverá ter um URI para um segredo em um cofre de chaves. Anote essa informação. Ela será necessária em uma etapa posterior.

## <a name="clone-the-repo"></a>Clonar o repositório

Clone o repositório para fazer uma cópia local em que você possa editar o código-fonte. Execute o comando a seguir:

```
git clone https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart.git
```

## <a name="open-and-edit-the-solution"></a>Abrir e editar a solução

Edite o arquivo program.cs para executar o exemplo com o nome específico de seu cofre de chaves:

1. Navegue até a pasta key-vault-dotnet-core-quickstart.
2. Abra o arquivo key-vault-dotnet-core-quickstart.sln no Visual Studio 2019.
3. Abra o arquivo Program.cs e atualize o espaço reservado *YourKeyVaultName* com o nome do cofre de chaves criado anteriormente.

A solução usa as bibliotecas NuGet [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) e [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault).

## <a name="run-the-app"></a>Execute o aplicativo

No menu principal do Visual Studio 2019, escolha **Depurar** > **Iniciar sem depuração**. Quando o navegador aparecer, navegue até a página **Sobre**. O valor para o **AppSecret** é exibido.

## <a name="publish-the-web-application-to-azure"></a>Publicar o aplicativo Web no Azure

Publique esse aplicativo no Azure para vê-lo funcionando como um aplicativo Web e também verificar se é possível buscar o valor do segredo:

1. No Visual Studio, selecione o projeto **key-vault-dotnet-core-quickstart**.
2. Selecione **Publicar** > **Iniciar**.
3. Crie um novo **Serviço de Aplicativo** e selecione **Publicar**.
4. Altere o nome do aplicativo para **keyvaultdotnetcorequickstart**.
5. Selecione **Criar**.

>[!VIDEO https://sec.ch9.ms/ch9/e93d/a6ac417f-2e63-4125-a37a-8f34bf0fe93d/KeyVault_high.mp4]

## <a name="enable-a-managed-identity-for-the-web-app"></a>Habilitar uma identidade gerenciada para o aplicativo Web

O Azure Key Vault fornece uma maneira de armazenar com segurança as credenciais e outras chaves e segredos, mas seu código precisa autenticar para o Key Vault para recuperá-los. [Visão geral das identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) facilita a solução desse problema, fornecendo aos serviços do Azure uma identidade gerenciada automaticamente no Azure AD (Azure Active Directory). Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter que todas as credenciais no seu código.

Na CLI do Azure, execute o comando de atribuir identidade para criar a identidade para esse aplicativo:

   ```azurecli
   az webapp identity assign --name "keyvaultdotnetcorequickstart" --resource-group "<YourResourceGroupName>"
   ```

>[!NOTE]
>O comando neste procedimento é o equivalente a acessar o portal e mudar a configuração **Identidade/Atribuída pelo sistema** para **Ativada** nas propriedades do aplicativo Web.

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Atribuir permissões ao aplicativo para ler segredos do Key Vault

Anote a saída quando você publicar o aplicativo no Azure. Ela deve ter o seguinte formato:
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
Em seguida, execute o comando usando o nome do seu cofre de chaves e o valor de **PrincipalId** copiado acima:

```azurecli

az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list

```

Agora, ao executar o aplicativo, você verá o valor do segredo recuperado. No comando anterior, você está dando permissões para a identidade do serviço de aplicativo para executar operações **get** e **list** no cofre de chaves.

## <a name="clean-up-resources"></a>Limpar recursos
Exclua o grupo de recursos, a máquina virtual e todos os recursos relacionados quando não precisar mais deles. Para fazer isso, selecione o grupo de recursos do cofre de chaves e selecione **Excluir**.

Exclua o cofre de chaves usando o comando [az keyvault delete](https://docs.microsoft.com/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-delete):

```azurecli
az keyvault delete --name
                   [--resource-group]
                   [--subscription]
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre o Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)
