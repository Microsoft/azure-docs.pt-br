---
title: Configurar identidades gerenciadas com a configuração Azure App
description: Saiba como as identidades gerenciadas funcionam na configuração do Azure App e como configurar uma identidade gerenciada
author: barbkess
ms.topic: article
ms.date: 02/25/2020
ms.author: barbkess
ms.reviewer: lcozzens
ms.service: azure-app-configuration
ms.openlocfilehash: 5424b776b977d8a6939bbb28c5d4c9c428cac444
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179605"
---
# <a name="how-to-use-managed-identities-for-azure-app-configuration"></a>Como usar identidades gerenciadas para configuração de Azure App

Este tópico mostra como criar uma identidade gerenciada para Azure App configuração. Uma identidade gerenciada do Azure Active Directory (AAD) permite que Azure App configuração acesse facilmente outros recursos protegidos por AAD, como Azure Key Vault. A identidade é gerenciada pela plataforma do Azure. Ele não exige que você provisione ou gire segredos. Para obter mais informações sobre identidades gerenciadas no AAD, veja [Identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md).

Seu aplicativo pode receber dois tipos de identidades:

- Uma **identidade atribuída pelo sistema** é vinculada ao seu repositório de configuração. Ele será excluído se o repositório de configuração for excluído. Um repositório de configurações só pode ter uma identidade atribuída pelo sistema.
- Uma **identidade atribuída pelo usuário** é um recurso autônomo do Azure que pode ser atribuído ao seu repositório de configuração. Um repositório de configurações pode ter várias identidades atribuídas pelo usuário.

## <a name="adding-a-system-assigned-identity"></a>Adicionando uma identidade designada pelo sistema

A criação de um repositório de configurações de aplicativo com uma identidade atribuída pelo sistema requer que uma propriedade adicional seja definida no repositório.

### <a name="using-the-azure-cli"></a>Usando a CLI do Azure

Para configurar uma identidade gerenciada usando o CLI do Azure, use o comando [AZ AppConfig Identity Assign] para um repositório de configuração existente. Você tem três opções para executar os exemplos nesta seção:

- Usar o [Azure Cloud Shell](../cloud-shell/overview.md) do portal do Azure.
- Use o Azure Cloud Shell inserido usando o botão “Experimentar”, localizado no canto superior direito de cada bloco de código abaixo.
- [Instale a versão mais recente do CLI do Azure](/cli/azure/install-azure-cli) (2,1 ou posterior) se preferir usar um console local da CLI.

As etapas a seguir mostrarão como criar um repositório de configuração de aplicativo e atribuir a ele uma identidade usando a CLI:

1. Se você estiver usando a CLI do Azure em um console local, primeiro entre no Azure usando o [logon az]. Use uma conta que esteja associada à sua assinatura do Azure:

    ```azurecli-interactive
    az login
    ```

1. Crie um repositório de configuração de aplicativo usando a CLI. Para obter mais exemplos de como usar a CLI com Azure App configuração, consulte [exemplos de CLI de configuração de aplicativo](scripts/cli-create-service.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Execute o comando [AZ AppConfig Identity Assign] para criar a identidade atribuída pelo sistema para este repositório de configuração:

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup
    ```

## <a name="adding-a-user-assigned-identity"></a>Adicionando uma identidade atribuída pelo usuário

A criação de um repositório de configurações de aplicativo com uma identidade atribuída pelo usuário requer que você crie a identidade e, em seguida, atribua seu identificador de recurso à sua loja.

### <a name="using-the-azure-cli"></a>Usando a CLI do Azure

Para configurar uma identidade gerenciada usando o CLI do Azure, use o comando [AZ AppConfig Identity Assign] para um repositório de configuração existente. Você tem três opções para executar os exemplos nesta seção:

- Usar o [Azure Cloud Shell](../cloud-shell/overview.md) do portal do Azure.
- Use o Azure Cloud Shell inserido usando o botão “Experimentar”, localizado no canto superior direito de cada bloco de código abaixo.
- [Instale a versão mais recente da CLI do Azure](/cli/azure/install-azure-cli) (2.0.31 ou mais recente) se você preferir usar um console da CLI local.

As etapas a seguir mostrarão como criar uma identidade atribuída pelo usuário e um repositório de configuração de aplicativo e, em seguida, atribuir a identidade à loja usando a CLI:

1. Se você estiver usando a CLI do Azure em um console local, primeiro entre no Azure usando o [logon az]. Use uma conta que esteja associada à sua assinatura do Azure:

    ```azurecli-interactive
    az login
    ```

1. Crie um repositório de configuração de aplicativo usando a CLI. Para obter mais exemplos de como usar a CLI com Azure App configuração, consulte [exemplos de CLI de configuração de aplicativo](scripts/cli-create-service.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Crie uma identidade atribuída pelo usuário chamada `myUserAssignedIdentity` usando a CLI.

    ```azurecli-interactive
    az identity create -resource-group myResourceGroup --name myUserAssignedIdentity
    ```

    Na saída desse comando, observe o valor da `id` propriedade.

1. Execute o comando [AZ AppConfig Identity Assign] para atribuir a nova identidade atribuída pelo usuário a esse repositório de configuração. Use o valor da `id` propriedade que você anotou na etapa anterior.

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup --identities /subscriptions/[subscription id]/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity
    ```

## <a name="removing-an-identity"></a>Removendo uma identidade

Uma identidade atribuída pelo sistema pode ser removida desabilitando o recurso usando o comando [AZ AppConfig Identity remove](/cli/azure/appconfig/identity#az-appconfig-identity-remove) no CLI do Azure. As identidades atribuídas pelo usuário podem ser removidas individualmente. Remover uma identidade atribuída pelo sistema dessa maneira também a excluirá do AAD. As identidades atribuídas pelo sistema também são automaticamente removidas do AAD quando o recurso do aplicativo é excluído.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um aplicativo ASP.NET Core com a configuração Azure App](quickstart-aspnet-core-app.md)

[atribuir identidade do AZ appconfig]: /cli/azure/appconfig/identity#az-appconfig-identity-assign
[az login]: /cli/azure/reference-index#az-login
