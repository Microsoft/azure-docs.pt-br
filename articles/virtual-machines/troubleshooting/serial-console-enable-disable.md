---
title: Habilitar e desabilitar o console serial do Azure | Microsoft Docs
description: Como habilitar e desabilitar o serviço de console serial do Azure
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/20/2019
ms.author: alsin
ms.openlocfilehash: e09e08f8ba36cf576bc27551254225adee3bb0fd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "75451295"
---
# <a name="enable-and-disable-the-azure-serial-console"></a>Habilitar e desabilitar o console serial do Azure

Assim como qualquer outro recurso, o console serial do Azure pode ser habilitado e desabilitado. O console serial está habilitado por padrão para todas as assinaturas no Azure global. Atualmente, desabilitar o console serial desabilitará o serviço para toda a sua assinatura. Desabilitar ou reabilitar o console serial para uma assinatura requer acesso no nível de colaborador ou acima na assinatura.

Você também pode desabilitar o console serial para uma VM individual ou instância do conjunto de dimensionamento de máquinas virtuais desabilitando o diagnóstico de inicialização. Você precisará de acesso no nível de colaborador ou acima no conjunto de dimensionamento de máquinas virtuais/VM e na sua conta de armazenamento de diagnóstico de inicialização.

## <a name="vm-level-disable"></a>Desabilitação no nível da VM
O console serial pode ser desabilitado para uma VM específica ou um conjunto de dimensionamento de máquinas virtuais desabilitando a configuração de diagnóstico de inicialização. Desative o diagnóstico de inicialização do portal do Azure para desabilitar o console serial para a VM ou o conjunto de dimensionamento de máquinas virtuais. Se você estiver usando o console serial em um conjunto de dimensionamento de máquinas virtuais, certifique-se de atualizar suas instâncias do conjunto de dimensionamento de máquinas virtuais para o modelo mais recente.


## <a name="subscription-level-enabledisable"></a>Habilitação/desabilitação no nível da assinatura

> [!NOTE]
> Verifique se você está na nuvem certa (nuvem pública do Azure, nuvem do governo dos EUA do Azure) antes de executar este comando. Você pode verificar `az cloud list` e definir sua nuvem com o `az cloud set -n <Name of cloud>` .

### <a name="azure-cli"></a>CLI do Azure

Console serial pode ser desabilitado e habilitado novamente para uma assinatura inteira usando os comandos a seguir na CLI do Azure (você pode usar o botão "Experimente" para iniciar uma instância do Azure Cloud Shell no qual você pode executar os comandos):

Para desabilitar o console serial para uma assinatura, use os seguintes comandos:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action disableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

Para habilitar o console serial para uma assinatura, use os seguintes comandos:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action enableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

Para obter o status atual habilitado/desabilitado do console serial para uma assinatura, use os seguintes comandos:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource show --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --output=json --api-version="2018-05-01" | jq .properties
```

### <a name="powershell"></a>PowerShell

Console serial também pode ser habilitado e desabilitado usando o PowerShell.

Para desabilitar o console serial para uma assinatura, use os seguintes comandos:
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action disableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

Para habilitar o console serial para uma assinatura, use os seguintes comandos:
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action enableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre o [console serial do Azure para VMs Linux](./serial-console-linux.md)
* Saiba mais sobre o [console serial do Azure para VMs do Windows](./serial-console-windows.md)
* Saiba mais sobre [as opções de gerenciamento de energia no console serial do Azure](./serial-console-power-options.md)