---
title: Obter um grupo de recursos gerenciados e redimensionar VMs – CLI do Azure
description: Fornece um script de exemplo da CLI do Azure que obtém um grupo de recursos gerenciados em um Aplicativo Gerenciado do Azure. O script redimensiona as VMs.
author: tfitzmac
ms.devlang: azurecli
ms.topic: sample
ms.date: 10/25/2017
ms.author: tomfitz
ms.custom: devx-track-azurecli
ms.openlocfilehash: 711b516d1ba1154e574b0d8bbd8d86a02d7df018
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87497810"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-azure-cli"></a>Obter recursos em um grupo de recursos gerenciado e redimensionar VMs com a CLI do Azure

Este script recupera os recursos de um grupo de recursos gerenciado e redimensiona as VMs nesse grupo de recursos.


[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemplo de script

[!code-azurecli[main](../../../../cli_scripts/managed-applications/get-application/get-application.sh "Get application")]


## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir para implantar o aplicativo gerenciado. Cada comando na tabela redireciona para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [az managedapp list](/cli/azure/managedapp#az-managedapp-list) | Liste os aplicativos gerenciados. Forneça os valores da consulta para destacar os resultados. |
| [az resource list](/cli/azure/resource#az-resource-list) | Liste os recursos. Forneça um grupo de recursos e os valores da consulta para destacar os resultados. |
| [az vm resize](/cli/azure/vm#az-vm-resize) | Atualize o tamanho de uma máquina virtual. |


## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução aos aplicativos gerenciados, consulte [Visão geral de aplicativos gerenciados do Azure](../overview.md).
* Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).
