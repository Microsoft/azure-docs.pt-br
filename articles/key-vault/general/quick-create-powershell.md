---
title: Guia de Início Rápido – Criar um Azure Key Vault usando o Azure PowerShell
description: Guia de início rápido que mostra como criar um Azure Key Vault usando o Azure PowerShell
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: e77493bc73bc2d6f590d9bdcf891171fbd71f74e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99070170"
---
# <a name="quickstart-create-a-key-vault-using-powershell"></a>Início Rápido: Criar um cofre de chaves usando o PowerShell

O Azure Key Vault é um serviço de nuvem que fornece um repositório seguro para [chaves](../keys/index.yml), [segredos](../secrets/index.yml) e [certificados](../certificates/index.yml). Para obter mais informações sobre o Key Vault, confira [Sobre o Azure Key Vault](overview.md); para obter mais informações sobre o que pode ser armazenado em um cofre de chaves, confira [Sobre chaves, segredos e certificados](about-keys-secrets-certificates.md).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Neste guia de início rápido, você criará um cofre de chaves com o [Azure PowerShell](/powershell/azure/). Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá o módulo do Azure PowerShell versão 1.0.0 ou posterior. Digite `$PSVersionTable.PSVersion` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Login-AzAccount` para criar uma conexão com o Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [Create a resource group](../../../includes/key-vault-powershell-rg-creation.md)]

## <a name="create-a-key-vault"></a>Criar um cofre de chaves

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-kv-creation.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um Key Vault usando o Azure PowerShell. Para saber mais sobre o Key Vault e como integrá-lo a seus aplicativos, confira os artigos abaixo.

- Leia uma [Visão geral do Azure Key Vault](overview.md)
- Confira a referência dos [cmdlets do Key Vault do Azure PowerShell](/powershell/module/az.keyvault/)
- Examine a [Visão geral de segurança do Azure Key Vault](security-overview.md)

