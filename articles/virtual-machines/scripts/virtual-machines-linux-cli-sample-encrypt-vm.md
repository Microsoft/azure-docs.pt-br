---
title: Amostra de script da CLI do Azure – Criptografar uma VM Linux | Microsoft Docs
description: Amostra de script da CLI do Azure – Criptografar uma VM Linux
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 16bbd4031c851a950af0f3c0fe98ebdd24b183df
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709430"
---
# <a name="encrypt-a-linux-virtual-machine-in-azure"></a>Criptografar uma máquina virtual Linux no Azure

Esse script cria um Azure Key Vault seguro, chaves de criptografia, uma entidade de serviço do Azure Active Directory e uma VM (máquina virtual) Linux. A VM é então criptografada usando a chave de criptografia do Key Vault e as credenciais da entidade de serviço.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/encrypt-disks/encrypt_vm.sh "Encrypt VM disks")]

## <a name="clean-up-deployment"></a>Limpar a implantação 

Execute o comando a seguir para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os comandos a seguir para criar um grupo de recursos, um Azure Key Vault, uma entidade de serviço, uma máquina virtual e todos os recursos relacionados. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az keyvault create](https://docs.microsoft.com/cli/azure/keyvault) | Cria um Azure Key Vault para armazenar dados seguros, como chaves de criptografia. |
| [az keyvault key create](https://docs.microsoft.com/cli/azure/keyvault/key) | Cria uma chave de criptografia no Key Vault. |
| [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp) | Cria uma entidade de serviço do Azure Active Directory para autenticar com segurança e controlar o acesso às chaves de criptografia. |
| [az keyvault set-policy](https://docs.microsoft.com/cli/azure/keyvault) | Define as permissões no Key Vault para conceder à entidade de serviço o acesso às chaves de criptografia. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm) | Cria a máquina virtual e a conecta a placa de rede, a rede virtual, a sub-rede e o NSG. Este comando também especifica a imagem de máquina virtual a ser usada e as credenciais administrativas.  |
| [az vm encryption enable](https://docs.microsoft.com/cli/azure/vm/encryption) | Habilita a criptografia em uma VM usando as credenciais da entidade de serviço e a chave de criptografia. |
| [az vm encryption show](https://docs.microsoft.com/cli/azure/vm/encryption) | Mostra o status do processo de criptografia da VM. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Os exemplos de script da CLI de máquina virtual adicionais podem ser encontrados na [documentação da VM Linux do Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
