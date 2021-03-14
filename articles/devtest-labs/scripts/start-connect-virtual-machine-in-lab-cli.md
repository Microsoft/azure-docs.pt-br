---
title: Exemplo de Script de CLI do Azure - Iniciar uma máquina virtual em um laboratório | Microsoft Docs
description: Esse script da CLI do Azure inicia uma máquina virtual em um laboratório no Azure DevTest Labs.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 8a3308a4e13b82cd90e00b6c25edadf4cc8aa4ee
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198159"
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
| [az lab vm start](/cli/azure/lab/vm#az-lab-vm-start) | Inicia uma VM (máquina virtual) em um laboratório. Essa operação pode demorar um pouco para ser concluída. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Encontre exemplos adicionais de script da CLI do Azure Lab Services em [Exemplos da CLI do Azure Lab Services](../samples-cli.md).
