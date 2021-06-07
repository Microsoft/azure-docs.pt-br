---
title: Criar e configurar um cofre de chaves para Azure Disk Encryption
description: Este artigo fornece etapas para criar e configurar um cofre de chaves para uso com Azure Disk Encryption em uma VM do Linux.
ms.service: virtual-machines
ms.collection: linux
ms.subservice: disks
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: d163ee552d95322a45acd571966a22e99aa1512e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102562598"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Criar e configurar um cofre de chaves para Azure Disk Encryption

O Azure Disk Encryption usa o Azure Key Vault para ajudar você a controlar e gerenciar os segredos e chaves de criptografia de disco.  Para obter mais informações sobre cofres-chave, consulte [Introdução ao Cofre de Chaves do Azure](../../key-vault/general/overview.md) e [Proteja seu cofre de chaves](../../key-vault/general/secure-your-key-vault.md). 

> [!WARNING]
> - Se você já tiver usado o Azure Disk Encryption com o Azure AD anteriormente para criptografar uma VM, deverá continuar usando essa opção para criptografar a VM. Veja [Criação e configuração de um cofre de chaves para Azure Disk Encryption com o Azure AD (versão anterior)](disk-encryption-key-vault-aad.md) para saber detalhes.

Criar e configurar um cofre de chaves para usar com Azure Disk Encryption envolve três etapas:

1. Criar um grupo de recursos, se necessário.
2. Criando um cofre de chaves. 
3. Definir as políticas de acesso avançado do cofre de chaves.

Esses passos estão ilustrados nos seguintes guias de início rápido:

- [Criar e criptografar uma VM do Linux com a CLI do Azure](disk-encryption-cli-quickstart.md)
- [Criar e criptografar uma VM do Linux com o Azure PowerShell](disk-encryption-powershell-quickstart.md)

Caso queira, também é possível gerar ou importar uma KEK (chave de criptografia de chave).

> [!Note]
> As etapas neste artigo são automatizadas no [Script de CLI de pré-requisitos do Azure Disk Encryption](https://github.com/ejarvi/ade-cli-getting-started) e no [Script do PowerShell de pré-requisitos do Azure Disk Encryption](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).

## <a name="install-tools-and-connect-to-azure"></a>Instalar ferramentas e conectar-se ao Azure

As etapas neste artigo podem ser concluídas com a [CLI do Azure](/cli/azure/), o [módulo AZ do Azure PowerShell](/powershell/azure/) ou o [portal do Azure](https://portal.azure.com). 

Embora o portal possa ser acessado através do navegador, a CLI do Azure e o Azure PowerShell exigem a instalação local; veja [Azure Disk Encryption para Linux: Instalação de ferramentas](disk-encryption-linux.md#install-tools-and-connect-to-azure) para saber detalhes.

### <a name="connect-to-your-azure-account"></a>Conectar-se à sua conta do Azure

Antes de usar a CLI do Azure ou o Azure PowerShell, é necessário se conectar a sua assinatura do Azure. Você faz isso [Entrando com a CLI do Azure](/cli/azure/authenticate-azure-cli), [Entrando com Azure Powershell](/powershell/azure/authenticate-azureps) ou fornecendo suas credenciais para o portal do Azure quando solicitado.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 
 
## <a name="next-steps"></a>Próximas etapas

- [Script da CLI dos pré-requisitos do Azure Disk Encryption](https://github.com/ejarvi/ade-cli-getting-started)
- [Script do PowerShell dos pré-requisitos do Azure Disk Encryption](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- Aprenda sobre [Cenários do Azure Disk Encryption em VMs do Linux](disk-encryption-linux.md)
- Saiba como [solucionar problemas do Azure Disk Encryption](disk-encryption-troubleshooting.md)
- Leia os [Scripts de exemplo do Azure Disk Encryption](disk-encryption-sample-scripts.md)
