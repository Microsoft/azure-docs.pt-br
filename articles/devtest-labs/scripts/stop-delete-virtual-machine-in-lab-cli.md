---
title: CLI do Azure – parar e excluir uma máquina virtual em um laboratório
description: Este artigo fornece um script da CLI do Azure que interrompe e exclui uma máquina virtual em um laboratório do Azure DevTest Labs.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 3f3802837685281339f0ca355c677e1a0ceac067
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198193"
---
# <a name="use-azure-cli-to-stop-and-delete-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Usar a CLI do Azure para interromper e excluir uma máquina virtual em um laboratório no Azure DevTest Labs

Esse script da CLI do Azure interrompe e exclui uma VM (máquina virtual) em um laboratório. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemplo de script

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/stop-delete-virtual-machine-in-lab/stop-delete-virtual-machine-in-lab.sh "Stop and delete a VM in a lab")]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos:

| Comando | Observações |
|---|---|
| [az lab vm stop](/cli/azure/lab/vm#az-lab-vm-stop) | Interrompe uma máquina virtual (VM) em um laboratório. Essa operação pode demorar um pouco para ser concluída. |
| [az lab vm delete](/cli/azure/lab/vm#az-lab-vm-delete) | Exclui uma VM (máquina virtual) em um laboratório. Essa operação pode demorar um pouco para ser concluída. |


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Encontre exemplos adicionais de script da CLI do Azure Lab Services em [Exemplos da CLI do Azure Lab Services](../samples-cli.md).
