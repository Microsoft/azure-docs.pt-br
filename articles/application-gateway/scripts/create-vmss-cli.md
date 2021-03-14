---
title: Exemplo de Script da CLI do Azure - Gerenciar o tráfego de rede | Microsoft Docs
description: Exemplo de Script da CLI do Azure - Gerenciar o tráfego da web com um gateway de aplicativo e um conjunto de escala de máquinas virtuais.
services: application-gateway
documentationcenter: networking
author: vhorne
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 2e766bcdee2afa3ff4ed75476c619326c4c0e0c2
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99591641"
---
# <a name="manage-web-traffic-using-the-azure-cli"></a>Gerenciar o tráfego da web usando a CLI do Azure

Este script cria um gateway de aplicativo que usa um conjunto de dimensionamento de máquinas virtuais para servidores de back-end. O gateway de aplicativo pode ser configurado para gerenciar o tráfego da web. Depois de executar o script, você pode testar o gateway de aplicativo usando seu endereço IP público.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemplo de script

[!code-azurecli-interactive[main](../../../cli_scripts/application-gateway/create-vmss/create-vmss.sh "Create application gateway")]

## <a name="clean-up-deployment"></a>Limpar a implantação 

Execute o comando a seguir para remover o grupo de recurso, o gateway de aplicativo, e todos os recursos relacionados.

```azurecli-interactive 
az group delete --name myResourceGroupAG --yes
```

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos para criar a implantação. Cada item em que a tabela contém links para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az network vnet create](/cli/azure/network/vnet) | Cria uma rede virtual. |
| [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) | Cria uma rede virtual e uma sub-rede. |
| [az network public-ip create](/cli/azure/network/public-ip) | Cria o endereço IP público para o gateway do aplicativo. |
| [az network application-gateway create](/cli/azure/network/application-gateway) | Criar um gateway de aplicativo. |
| [az vmss create](/cli/azure/vmss) | Criar um conjunto de dimensionamento de máquinas virtuais. |
| [az network public-ip show](/cli/azure/network/public-ip) | Pega o endereço de IP público do gateway do aplicativo. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure/overview).

Exemplos de script CLI de máquinas virtuais adicionais podem ser encontrados na [documentação da VM do Windows do Azure](../cli-samples.md).
