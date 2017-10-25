---
title: "Administrando notificações de manutenção para VMs do Linux no Azure | Microsoft Docs"
description: "Exiba notificações de manutenção para máquinas virtuais do Linux em execução no Azure e inicie a manutenção de autoatendimento."
services: virtual-machines-linux
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: zivr
ms.openlocfilehash: 2fc96898c9b7ebefc7648b5819975d9015e76c0c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="handling-planned-maintenance-notifications-for-linux-virtual-machines"></a>Administrando notificações de manutenção planejada para máquinas virtuais do Linux

O Azure realiza atualizações periodicamente para aumentar a confiabilidade, o desempenho e a segurança da infraestrutura de host para máquinas virtuais. As atualizações são as alterações como, por exemplo, aplicação de patches no ambiente de hospedagem ou atualização e desativação de hardware. A maioria dessas atualizações é realizada sem nenhum impacto nas máquinas virtuais hospedadas. No entanto, há casos em que as atualizações possuem um impacto:

- Se a manutenção não exigir uma reinicialização, o Azure usa migração in-loco para pausar a máquina virtual enquanto o host está atualizado.

- Se a manutenção requer uma reinicialização, você receberá um aviso informando para quando a manutenção está planejada. Nesses casos, você tem uma janela de tempo, na qual você pode iniciar a manutenção, quando for mais oportuno para você.


A manutenção planejada que requer um reinício é agendada em ondas. Cada onda tem um escopo diferente (regiões).

- Uma onda começa com uma notificação para os clientes. Por padrão, a notificação é enviada para o proprietário e os coproprietários da assinatura. Você pode adicionar mais destinatários e opções de mensagem como email, SMS e Webhooks, às notificações. 
- Logo após a notificação, uma janela de autoatendimento é definida. Durante essa janela, você pode ver qual das suas máquinas virtuais está incluída nessa onda e iniciar a manutenção usando reimplantação pró-ativa. 
- Após a janela de autoatendimento, uma janela de manutenção agendada começa. Neste momento, o Azure agenda e aplica a manutenção necessária à sua máquina virtual.  

O objetivo de ter duas janelas é dar a você tempo suficiente para iniciar a manutenção e reiniciar sua máquina virtual sabendo quando o Azure iniciará automaticamente a manutenção.

Você pode usar a CLI do Azure, PowerShell, API REST e o portal do Azure para consultar as janelas de manutenção para suas VMs e iniciar a manutenção de autoatendimento.

 > [!NOTE]
 > Se você tentar iniciar a manutenção e falhar, o Azure marcará sua VM como **ignorada** e ela não é reinicializada durante a janela de manutenção agendada. Em vez disso, você é contatado em um momento posterior com uma nova agenda. 

## <a name="find-vms-scheduled-for-maintenance-using-cli"></a>Encontrar VMs agendadas para manutenção usando a CLI

Informações de manutenção planejada podem ser vistas usando [azure vm get-instance-view](/cli/azure/vm?view=azure-cli-latest#az_vm_get_instance_view).
 
Informações de manutenção são retornadas somente se houver manutenção planejada. Se não houver que nenhuma manutenção agendada que afete a VM, o comando não retornará nenhuma informação de manutenção. 

```azure-cli
az vm get-instance-view  - g rgName  -n vmName 
```

Os seguintes valores são retornados em MaintenanceRedeployStatus: 

| Valor | Descrição   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Indica se você pode iniciar a manutenção na máquina virtual neste momento ||
| PreMaintenanceWindowStartTime         | O início da janela de autoatendimento de manutenção quando você pode iniciar a manutenção na sua VM ||
| PreMaintenanceWindowEndTime           | O fim da janela de autoatendimento de manutenção quando você pode iniciar manutenção na sua VM ||
| MaintenanceWindowStartTime            | O início da janela de manutenção agendada quando você pode iniciar manutenção na sua VM ||
| MaintenanceWindowEndTime              | O fim da manutenção agendada janela quando você pode iniciar manutenção na sua VM ||
| LastOperationResultCode               | O resultado da última tentativa de iniciar a manutenção na VM ||


## <a name="start-maintenance-on-your-vm-using-cli"></a>Iniciar manutenção na sua VM usando a CLI

A chamada a seguir iniciará a manutenção em uma VM se `IsCustomerInitiatedMaintenanceAllowed` for definido como true.

```azure-cli
az vm perform-maintenance rgName vmName 
```

[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]


## <a name="next-steps"></a>Próximas etapas

Saiba como você pode registrar-se para eventos de manutenção dentro da VM usando [Eventos agendados](scheduled-events.md).