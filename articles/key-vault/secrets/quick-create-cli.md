---
title: Guia de Início Rápido – Definir e recuperar um segredo do Azure Key Vault
description: Início Rápido que mostra como definir e recuperar um segredo do Azure Key Vault usando a CLI do Azure
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: 655ea1920fc80c8cd677281f09cfca21120e1d61
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726350"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-azure-cli"></a>Início Rápido: Definir e recuperar um segredo do Azure Key Vault usando a CLI do Azure

Neste guia de início rápido, você cria um cofre de chaves no Azure Key Vault com CLI do Azure. O Azure Key Vault é um serviço de nuvem que funciona como um repositório de segredos seguro. Você pode armazenar chaves, senhas, certificados e outros segredos com segurança. Para saber mais sobre o Key Vault, reveja a [Visão geral](../general/overview.md). A CLI do Azure é usada para criar e gerenciar recursos do Azure usando comandos ou scripts. Depois de concluído, você armazenará um segredo.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Este guia de início rápido exige a versão 2.0.4 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [Create a resource group](../../../includes/key-vault-cli-rg-creation.md)]

## <a name="create-a-key-vault"></a>Criar um cofre de chaves

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-kv-creation.md)]

## <a name="add-a-secret-to-key-vault"></a>Adicionar um segredo ao Key Vault

Para adicionar um segredo ao cofre, basta executar algumas etapas adicionais. Essa senha pode ser usada por um aplicativo. A senha será chamada **ExamplePassword** e armazenará o valor **hVFkk965BuUv** nela.

Use o comando [az keyvault secret set](/cli/azure/keyvault/secret#az_keyvault_secret_set) da CLI do Azure abaixo para criar um segredo no Key Vault chamado **ExamplePassword** que armazenará o valor **hVFkk965BuUv**:

```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "ExamplePassword" --value "hVFkk965BuUv"
```

## <a name="retrieve-a-secret-from-key-vault"></a>Recuperar um segredo do Key Vault

Agora, você pode fazer referência a essa senha que foi adicionada ao Azure Key Vault usando seu URI. Use **'https://<nome-exclusivo-do-cofre-de-chaves>.vault.azure.net/secrets/ExamplePassword'** para obter a versão atual.

Para exibir o valor contido no segredo como texto sem formatação:

```azurecli
az keyvault secret show --name "ExamplePassword" --vault-name "<your-unique-keyvault-name>" --query "value"
```

Agora, você criou um Key Vault, armazenou um segredo e o recuperou.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-delete-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um Key Vault e armazenou um segredo nele. Para saber mais sobre o Key Vault e como integrá-lo a seus aplicativos, confira os artigos abaixo.

- Leia uma [Visão geral do Azure Key Vault](../general/overview.md)
- Saiba como [armazenar segredos de várias linhas no Key Vault](multiline-secrets.md)
- Confira a referência dos [comandos az keyvault da CLI do Azure](/cli/azure/keyvault)
- Examine a [Visão geral de segurança do Key Vault](../general/security-overview.md)
