---
title: Guia de Início Rápido – Criar um Azure Key Vault com a CLI do Azure
description: Guia de início rápido que mostra como criar um Azure Key Vault usando a CLI do Azure
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: f7f6f5d82c5fda7101e80ddcb8b17dc6bdef6532
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99070238"
---
# <a name="quickstart-create-a-key-vault-using-the-azure-cli"></a>Início Rápido: Criar um cofre de chaves usando a CLI do Azure

O Azure Key Vault é um serviço de nuvem que fornece um repositório seguro para [chaves](../keys/index.yml), [segredos](../secrets/index.yml) e [certificados](../certificates/index.yml). Para obter mais informações sobre o Key Vault, confira [Sobre o Azure Key Vault](overview.md); para obter mais informações sobre o que pode ser armazenado em um cofre de chaves, confira [Sobre chaves, segredos e certificados](about-keys-secrets-certificates.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Este guia de início rápido exige a versão 2.0.4 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [Create a resource group](../../../includes/key-vault-cli-rg-creation.md)]

## <a name="create-a-key-vault"></a>Criar um cofre de chaves

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-kv-creation.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-delete-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou um Key Vault e o excluiu. Para saber mais sobre o Key Vault e como integrá-lo a seus aplicativos, confira os artigos abaixo.

- Leia uma [Visão geral do Azure Key Vault](overview.md)
- Examine a [Visão geral de segurança do Azure Key Vault](security-overview.md)
- Confira a referência dos [comandos az keyvault da CLI do Azure](/cli/azure/keyvault)

