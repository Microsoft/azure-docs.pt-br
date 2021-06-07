---
title: Criar e recuperar atributos de uma chave no Azure Key Vault – CLI do Azure
description: Início Rápido que mostra como definir e recuperar uma chave do Azure Key Vault usando a CLI do Azure
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4141e60370b397e799664b7d42384bbeb096bd05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99071164"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-azure-cli"></a>Início Rápido: Definir e recuperar uma chave do Azure Key Vault usando a CLI do Azure

Neste guia de início rápido, você cria um cofre de chaves no Azure Key Vault com CLI do Azure. O Azure Key Vault é um serviço de nuvem que funciona como um repositório de segredos seguro. Você pode armazenar chaves, senhas, certificados e outros segredos com segurança. Para saber mais sobre o Key Vault, reveja a [Visão geral](../general/overview.md). A CLI do Azure é usada para criar e gerenciar recursos do Azure usando comandos ou scripts. Depois de concluir isso, você armazenará uma chave.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Este guia de início rápido exige a versão 2.0.4 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [Create a resource group](../../../includes/key-vault-cli-rg-creation.md)]

## <a name="create-a-key-vault"></a>Criar um cofre de chaves

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-kv-creation.md)]

## <a name="add-a-key-to-key-vault"></a>Adicionar uma chave ao Key Vault

Para adicionar uma chave ao cofre, basta executar algumas etapas adicionais. Essa chave pode ser usada por um aplicativo. 

Digite os comandos abaixo para criar uma chave chamada **ExampleKey**:

```azurecli
az keyvault key create --vault-name "<your-unique-keyvault-name>" -n ExampleKey --protection software
```

Agora, você pode referenciar essa chave que foi adicionada ao Azure Key Vault usando o respectivo URI. Use **'https://<nome-exclusivo-do-cofre-de-chaves>.vault.azure.net/keys/ExampleKey'** para obter a versão atual. 

Para ver a chave armazenada anteriormente:

```azurecli

az keyvault key show --name "ExampleKey" --vault-name "<your-unique-keyvault-name>"
```

Agora, você criou um Key Vault, armazenou uma chave e a recuperou.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-delete-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um Key Vault e armazenou uma chave nele. Para saber mais sobre o Key Vault e como integrá-lo a seus aplicativos, confira os artigos abaixo.

- Leia uma [Visão geral do Azure Key Vault](../general/overview.md)
- Confira a referência dos [comandos az keyvault da CLI do Azure](/cli/azure/keyvault)
- Examine a [Visão geral de segurança do Key Vault](../general/security-overview.md)
