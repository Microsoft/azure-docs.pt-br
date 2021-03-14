---
title: Obter notificações de manutenção usando a CLI
description: Exibir notificações de manutenção para máquinas virtuais em execução no Azure e iniciar a manutenção de autoatendimento, usando o CLI do Azure.
author: shants123
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: cd042ce09533cbefe37cb2e4d311a3857e3dfdec
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102552398"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>Manipulando notificações de manutenção planejada usando o CLI do Azure

**Este artigo se aplica a máquinas virtuais que executam o Linux e o Windows.**

Você pode usar a CLI para ver quando as VMs estão agendadas para [manutenção](maintenance-notifications.md). As informações de manutenção planejada estão disponíveis em [AZ VM Get-Instance-View](/cli/azure/vm#az-vm-get-instance-view).
 
As informações de manutenção só serão retornadas se houver manutenção planejada. 

```azurecli-interactive
az vm get-instance-view -n myVM -g myResourceGroup --query instanceView.maintenanceRedeployStatus
```

Saída
```
      "maintenanceRedeployStatus": {
      "additionalProperties": {},
      "isCustomerInitiatedMaintenanceAllowed": true,
      "lastOperationMessage": null,
      "lastOperationResultCode": "None",
      "maintenanceWindowEndTime": "2018-06-04T16:30:00+00:00",
      "maintenanceWindowStartTime": "2018-05-21T16:30:00+00:00",
      "preMaintenanceWindowEndTime": "2018-05-19T12:30:00+00:00",
      "preMaintenanceWindowStartTime": "2018-05-14T12:30:00+00:00"
```

## <a name="start-maintenance"></a>Iniciar manutenção

A chamada a seguir iniciará a manutenção em uma VM se `IsCustomerInitiatedMaintenanceAllowed` for definido como true.

```azurecli-interactive
az vm perform-maintenance -g myResourceGroup -n myVM 
```

## <a name="classic-deployments"></a>Implantações clássicas

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

Caso você ainda tenha VMs herdadas que foram implantadas usando o modelo de implantação clássico, use a CLI Clássica do Azure para consultar VMs e iniciar a manutenção.

Verifique se você está no modo correto para trabalhar com a VM clássica digitando:

```
azure config mode asm
```

Para obter o status de manutenção de uma VM chamada *myVM*, digite:

```
azure vm show myVM 
``` 

Para iniciar a manutenção na VM clássica chamada *myVM* no serviço *myService* e na implantação *myDeployment*, digite:

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```

## <a name="next-steps"></a>Próximas etapas

Você também pode manipular a manutenção planejada usando o [Azure PowerShell](maintenance-notifications-powershell.md) ou o [portal](maintenance-notifications-portal.md).
