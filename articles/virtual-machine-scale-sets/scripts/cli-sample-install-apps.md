---
title: Exemplos da CLI do Azure – Instalar aplicativos
description: Esse script cria um conjunto de dimensionamento de máquinas virtuais executando o Ubuntu e usa a Extensão de Script Personalizado para instalar um aplicativo Web básico.
author: mimckitt
ms.author: mimckitt
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.date: 03/27/2018
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 40a5b4005e55e507ffff5bf8e7dbf740a1e50cf3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932277"
---
# <a name="install-applications-into-a-virtual-machine-scale-set-with-the-azure-cli"></a>Instalar aplicativos em um conjunto de dimensionamento de máquinas virtuais com a CLI do Azure
Esse script cria um conjunto de dimensionamento de máquinas virtuais executando o Ubuntu e usa a Extensão de Script Personalizado para instalar um aplicativo Web básico. Depois de executar o script, é possível acessar o aplicativo Web por meio de navegador da Web.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemplo de script
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/install-apps/install-apps.sh "Install apps into a scale set")]

## <a name="clean-up-deployment"></a>Limpar a implantação
Execute o comando a seguir para remover o grupo de recursos, o conjunto de dimensionamento e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script
Este script usa os comandos a seguir para criar um grupo de recursos, um conjunto de dimensionamento de máquinas virtuais e todos os recursos relacionados. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/ad/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az vmss create](/cli/azure/vmss) | Cria um conjunto de dimensionamento de máquinas virtuais e a conecta à rede virtual, à sub-rede e ao grupo de segurança de rede. Um balanceador de carga também é criado para distribuir o tráfego para as diversas instâncias de VM. Esse comando também especifica a imagem da VM a ser usada e as credenciais administrativas.  |
| [az vmss extension set](/cli/azure/vmss/extension) | Instala a Extensão de Script Personalizado do Azure para executar um script que prepara os discos de dados em cada instância de VM. |
| [az network lb rule create](/cli/azure/network/lb/rule) | Cria uma regra do balanceador de carga para distribuir o tráfego na porta TCP 80 para as instâncias de VM no conjunto de dimensionamento. |
| [az network public-ip show](/cli/azure/network/public-ip) | Obtém informações sobre o endereço IP público atribuído usado pelo balanceador de carga. |
| [az group delete](/cli/azure/ad/group) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure/overview).
