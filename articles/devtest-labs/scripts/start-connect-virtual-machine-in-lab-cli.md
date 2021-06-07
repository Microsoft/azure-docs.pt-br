---
title: Exemplo de Script de CLI do Azure - Iniciar uma máquina virtual em um laboratório | Microsoft Docs
description: Esse script da CLI do Azure inicia uma máquina virtual em um laboratório no Azure DevTest Labs.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: f82d43fc212da034d978ee3d65236d806daeb008
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764973"
---
# <a name="use-azure-cli-to-start-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Use a CLI do Azure para iniciar uma máquina virtual em um laboratório no Azure DevTest Labs

Esse script da CLI do Azure inicia uma máquina virtual (VM) em um laboratório. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemplo de script

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/start-connect-virtual-machine-in-lab/start-connect-virtual-machine-in-lab.sh "Start a VM")]


## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos:

| Comando | Observações |
|---|---|
| [az lab vm start](/cli/azure/lab/vm#az_lab_vm_start) | Inicia uma VM (máquina virtual) em um laboratório. Essa operação pode demorar um pouco para ser concluída. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Encontre exemplos adicionais de script da CLI do Azure Lab Services em [Exemplos da CLI do Azure Lab Services](../samples-cli.md).
