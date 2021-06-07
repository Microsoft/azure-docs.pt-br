---
title: Exemplo de Script da CLI do Azure - Restringir o tráfego de rede | Microsoft Docs
description: Exemplo de Script da CLI do Azure - Criar um gateway de aplicativo com um firewall de aplicativo de web e um conjunto de escala de máquina virtual que usa regras OWASP para restringir tráfego.
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
ms.openlocfilehash: 6e20a4324f46925b8f83d7519c481a1d5bfc06a9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789295"
---
# <a name="restrict-web-traffic-using-the-azure-cli"></a>Restringir o tráfego de web usando a CLI do Azure

Este script cria um gateway de aplicativo com um firewall do aplicativo Web que usa um conjunto de dimensionamento de máquina virtual para servidores back-end. O firewall do aplicativo web restringe o tráfego da web com base em regras OWASP. Depois de executar o script, você pode testar o gateway de aplicativo usando seu endereço IP público.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemplo de script

[!code-azurecli-interactive[main](../../../cli_scripts/application-gateway/create-vmss/create-vmss-waf.sh "Create application gateway")]

## <a name="clean-up-deployment"></a>Limpar a implantação 

Execute o comando a seguir para remover o grupo de recurso, o gateway de aplicativo, e todos os recursos relacionados.

```azurecli-interactive 
az group delete --name myResourceGroupAG --yes
```

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos para criar a implantação. Cada item em que a tabela contém links para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | Cria uma rede virtual. |
| [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Cria uma rede virtual e uma sub-rede. |
| [az network public-ip create](/cli/azure/network/public-ip) | Cria o endereço IP público para o gateway do aplicativo. |
| [az network application-gateway create](/cli/azure/network/application-gateway) | Criar um gateway de aplicativo. |
| [az vmss create](/cli/azure/vmss#az_vmss_create) | Criar um conjunto de dimensionamento de máquinas virtuais. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Cria uma conta de armazenamento. |
| [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) | Cria uma conta de armazenamento. |
| [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) | Pega o endereço de IP público do gateway do aplicativo. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure/overview).

Exemplos de script CLI de máquinas virtuais adicionais podem ser encontrados na [documentação de gateway de aplicativo do Azure](../cli-samples.md).
