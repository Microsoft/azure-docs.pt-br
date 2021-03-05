---
title: CLI do Azure – criar e verificar uma máquina virtual em um laboratório
description: Esse script de CLI do Azure cria uma máquina virtual em um laboratório e verifica se ele está disponível.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: c7625f62d7897d61903f864b216ccf9aa13648ea
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198414"
---
# <a name="use-azure-cli-to-create-and-verify-availability-of-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Use a CLI do Azure para criar e verificar a disponibilidade de uma máquina virtual em um laboratório no Azure DevTest Labs

Esse script da CLI do Azure cria uma máquina virtual (VM) em um laboratório. A VM é criada com base em uma imagem do marketplace com autenticação ssh. O script, em seguida, verifica se a VM está disponível para uso. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemplo de script

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/create-verify-virtual-machine-in-lab/create-verify-virtual-machine-in-lab.sh "Create and verify availability of a VM")]

## <a name="clean-up-deployment"></a>Limpar a implantação 

Execute o comando a seguir para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos:

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az lab vm create](/cli/azure/lab/vm#az-lab-vm-create) | Cria uma VM (máquina virtual) em um laboratório. |
| [az lab vm show](/cli/azure/lab/vm#az-lab-vm-show) | Exibe o status da VM em um laboratório. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Encontre exemplos adicionais de script da CLI do Azure Lab Services em [Exemplos da CLI do Azure Lab Services](../samples-cli.md).
