---
title: Criar e configurar um cofre de chaves para Azure Disk Encryption
description: Este artigo fornece etapas para criar e configurar um cofre de chaves para usar com o Azure Disk Encryption
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: acd2ae54d81fb508d5f8c02262cf8c2f0f071fb5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016787"
---
# <a name="create-and-configure-a-key-vault-for-azure-disk-encryption"></a>Criar e configurar um cofre de chaves para o Azure Disk Encryption

O Azure Disk Encryption usa o Azure Key Vault para ajudar você a controlar e gerenciar os segredos e chaves de criptografia de disco.  Para obter mais informações sobre cofres-chave, consulte [Introdução ao Cofre de Chaves do Azure](../key-vault/general/overview.md) e [Proteja seu cofre de chaves](../key-vault/general/secure-your-key-vault.md).

Criar e configurar um cofre de chaves para usar com Azure Disk Encryption envolve três etapas:

1. Criar um grupo de recursos, se necessário.
2. Criando um cofre de chaves. 
3. Definir as políticas de acesso avançado do cofre de chaves.

Esses passos estão ilustrados nos seguintes guias de início rápido:

Caso queira, também é possível gerar ou importar uma KEK (chave de criptografia de chave).

## <a name="install-tools-and-connect-to-azure"></a>Instalar ferramentas e conectar-se ao Azure

As etapas neste artigo podem ser concluídas com a [CLI do Azure](/cli/azure/), o [módulo AZ do Azure PowerShell](/powershell/azure/) ou o [portal do Azure](https://portal.azure.com).

### <a name="connect-to-your-azure-account"></a>Conectar-se à sua conta do Azure

Antes de usar a CLI do Azure ou o Azure PowerShell, é necessário se conectar a sua assinatura do Azure. Você pode fazer isso ao [entrar com a CLI do Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), [ao entrar com Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0) ou ao fornecer suas credenciais para o portal do Azure quando solicitado.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>Próximas etapas

- [Visão geral do Azure Disk Encryption](disk-encryption-overview.md)
- [Criptografar um conjunto de dimensionamento de máquinas virtuais usando a CLI do Azure](disk-encryption-cli.md)
- [Criptografar um conjunto de dimensionamento de máquinas virtuais usando o Azure PowerShell](disk-encryption-powershell.md)
