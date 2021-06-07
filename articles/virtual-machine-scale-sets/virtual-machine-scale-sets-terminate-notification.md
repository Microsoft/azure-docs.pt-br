---
title: Notificação de encerramento para instâncias de conjuntos de dimensionamento de máquinas virtuais do Azure
description: Saiba como habilitar a notificação de término para instâncias do conjunto de dimensionamento de máquinas virtuais do Azure
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 02/26/2020
ms.reviewer: jushiman
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: c4d6de1b3406e6d82bdac5ff9b5c72a2286da988
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92747743"
---
# <a name="terminate-notification-for-azure-virtual-machine-scale-set-instances"></a>Notificação de encerramento para instâncias de conjuntos de dimensionamento de máquinas virtuais do Azure
As instâncias do conjunto de dimensionamento podem optar por receber notificações de encerramento de instância e definir um tempo limite de atraso predefinido para a operação de encerramento. A notificação de encerramento é enviada por meio do serviço de metadados do Azure – [eventos agendados](../virtual-machines/windows/scheduled-events.md), que fornece notificações e atraso de operações de impacto, como reinicializações e reimplantação. A solução adiciona outro evento – Terminate – à lista de Eventos Agendados, e o atraso associado do evento Terminate dependerá do limite de atraso conforme especificado pelos usuários em suas configurações de modelo de conjunto de dimensionamento.

Depois de registrado no recurso, as instâncias do conjunto de dimensionamento não precisam aguardar até que o tempo limite especificado expire antes que a instância seja excluída. Depois de receber uma notificação de encerramento, a instância pode optar por ser excluída a qualquer momento antes de expirar o tempo limite de encerramento.

## <a name="enable-terminate-notifications"></a>Habilitar notificações de término
Há várias maneiras de habilitar notificações de encerramento em suas instâncias do conjunto de dimensionamento, conforme detalhado nos exemplos abaixo.

### <a name="azure-portal"></a>Portal do Azure

As etapas a seguir habilitam a notificação de término ao criar um novo conjunto de dimensionamento. 

1. Vá para **conjuntos de dimensionamento de máquinas virtuais**.
1. Selecione **+ Adicionar** para criar um novo conjunto de dimensionamento.
1. Vá para a guia **Gerenciamento** . 
1. Localize a seção **término da instância** .
1. Para **notificação de encerramento de instância**, selecione **ativado**.
1. Para o **atraso de encerramento (minutos)**, defina o tempo limite padrão desejado.
1. Quando terminar de criar o novo conjunto de dimensionamento, selecione o botão **revisar + criar** . 

> [!NOTE]
> Você não pode definir notificações de término em conjuntos de dimensionamento existentes no portal do Azure

### <a name="rest-api"></a>API REST

O exemplo a seguir habilita a notificação de encerramento no modelo do conjunto de dimensionamento.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "virtualMachineProfile": {
            "scheduledEventsProfile": {
                "terminateNotificationProfile": {
                    "notBeforeTimeout":"PT5M",
                    "enable":true
                }
            }
        }
    }        
}

```

O bloco acima Especifica um atraso de tempo limite de 5 minutos (conforme indicado por *PT5M*) para qualquer operação de término em todas as instâncias em seu conjunto de dimensionamento. O campo *notBeforeTimeout* pode usar qualquer valor entre 5 e 15 minutos no formato ISO 8601. Você pode alterar o tempo limite padrão para a operação de término modificando a propriedade *notBeforeTimeout* em *terminateNotificationProfile* descrito acima.

Depois de habilitar o *scheduledEventsProfile* no modelo do conjunto de dimensionamento e definir o *notBeforeTimeout*, atualize as instâncias individuais para o [modelo mais recente](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) para refletir as alterações.

> [!NOTE]
>As notificações de término em instâncias do conjunto de dimensionamento só podem ser habilitadas com a versão de API 2019-03-01 e superior

### <a name="azure-powershell"></a>Azure PowerShell
Ao criar um novo conjunto de dimensionamento, você pode habilitar notificações de encerramento no conjunto de dimensionamento usando o cmdlet [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) .

Este script de exemplo percorre a criação de um conjunto de dimensionamento e os recursos associados usando o arquivo de configuração: [criar um conjunto de dimensionamento de máquinas virtuais completo](./scripts/powershell-sample-create-complete-scale-set.md). Você pode fornecer configurar a notificação de término adicionando os parâmetros *TerminateScheduledEvents* e *TerminateScheduledEventNotBeforeTimeoutInMinutes* ao objeto de configuração para criar um conjunto de dimensionamento. O exemplo a seguir habilita o recurso com um tempo limite de atraso de 10 minutos.

```azurepowershell-interactive
New-AzVmssConfig `
  -Location "VMSSLocation" `
  -SkuCapacity 2 `
  -SkuName "Standard_DS2" `
  -UpgradePolicyMode "Automatic" `
  -TerminateScheduledEvents $true `
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 10
```

Use o cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss) para habilitar notificações de encerramento em um conjunto de dimensionamento existente.

```azurepowershell-interactive
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -TerminateScheduledEvents $true `
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 15
```
O exemplo acima habilita as notificações de término em um conjunto de dimensionamento existente e define um tempo limite de 15 minutos para o evento Terminate.

Depois de habilitar os eventos agendados no modelo do conjunto de dimensionamento e definir o tempo limite, atualize as instâncias individuais para o [modelo mais recente](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) para refletir as alterações.

### <a name="azure-cli-20"></a>CLI do Azure 2.0

O exemplo a seguir é para habilitar a notificação de encerramento ao criar um novo conjunto de dimensionamento.

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --terminate-notification-time 10
```

O exemplo acima primeiro cria um grupo de recursos e, em seguida, cria um novo conjunto de dimensionamento com notificações de término habilitadas para um tempo limite padrão de 10 minutos.

O exemplo a seguir é para habilitar a notificação de encerramento em um conjunto de dimensionamento existente.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-terminate-notification true \
  --terminate-notification-time 10
```

## <a name="get-terminate-notifications"></a>Obter notificações de término

As notificações de término são entregues por meio de [eventos agendados](../virtual-machines/windows/scheduled-events.md), que é um serviço de metadados do Azure. O Serviço de Metadados do Azure expõe informações sobre a execução de Máquinas Virtuais usando um Ponto de Extremidade REST acessível de dentro da VM. As informações estão disponíveis por meio de um IP não roteável para que não seja exposta fora da VM.

Eventos Agendados está habilitado para seu conjunto de dimensionamento na primeira vez que você fizer uma solicitação de eventos. Você pode esperar uma resposta atrasada em sua primeira chamada de até dois minutos. Consulte o ponto de extremidade periodicamente para detectar futuros eventos de manutenção e o status das atividades de manutenção em andamento.

Eventos Agendados está desabilitado para seu conjunto de dimensionamento se as instâncias do conjunto de dimensionamento não fizerem uma solicitação por 24 horas.

### <a name="endpoint-discovery"></a>Descoberta de ponto de extremidade
Para VMs habilitadas para VNET, o serviço de metadados está disponível de um IP não roteável estático, 169.254.169.254.

O ponto de extremidade completo para a versão mais recente dos eventos agendados é:
> 'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="query-response"></a>Resposta de consulta
Uma resposta contém uma matriz de eventos agendados. Uma matriz vazia significa que não há eventos agendados no momento.

No caso de haver eventos agendados, a resposta contém uma matriz de eventos. Para um evento "Terminate", a resposta terá a seguinte aparência:
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Terminate",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled",
            "NotBefore": {timeInUTC},
        }
    ]
}
```
O *DocumentIncarnation* é uma ETag e fornece uma maneira fácil de inspecionar se a carga de eventos foi alterada desde a última consulta.

Para obter mais informações sobre cada um dos campos acima, consulte a documentação do Eventos Agendados para [Windows](../virtual-machines/windows/scheduled-events.md#event-properties) e [Linux](../virtual-machines/linux/scheduled-events.md#event-properties).

### <a name="respond-to-events"></a>Responder a eventos
Depois de aprender sobre um evento futuro e concluir sua lógica para desligamento normal, você pode aprovar o evento pendente fazendo uma chamada POST para o serviço de metadados com EventId. A chamada POST indica ao Azure que ele pode continuar com a exclusão da VM.

Abaixo está o JSON esperado no corpo da solicitação POST. A solicitação deve conter uma lista de StartRequests. Cada StartRequest contém o EventId para o evento que você deseja acelerar:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

Verifique se cada VM no conjunto de dimensionamento está apenas aprovando o EventID relevante somente para essa VM. Uma VM pode obter seu próprio nome de VM [por meio de metadados de instância](virtual-machine-scale-sets-instance-ids.md#instance-metadata-vm-name). Esse nome usa a forma "{scale-Set-name} _ {Instance-ID}" e será exibido na seção "recursos" da resposta de consulta descrita acima.

Você também pode consultar scripts de exemplos para consultar e responder a eventos [Python](../virtual-machines/linux/scheduled-events.md#python-sample).

## <a name="tips-and-best-practices"></a>Dicas e práticas recomendadas
-   Encerrar notificações somente em operações ' excluir ' – todas as operações de exclusão (exclusão manual ou redução iniciada pelo dimensionamento automático) gerarão eventos de término se o conjunto de dimensionamento tiver *scheduledEventsProfile* habilitado. Outras operações, como reinicializar, refazer imagem, reimplantar e parar/desalocar, não geram eventos Terminate. As notificações de término não podem ser habilitadas para VMs de baixa prioridade.
-   Nenhuma espera obrigatória para tempo limite – você pode iniciar a operação de término a qualquer momento depois que o evento tiver sido recebido e antes de o tempo de falta *antes* do evento ser expirado.
-   Exclusão obrigatória no tempo limite – não há nenhum recurso de estender o valor de tempo limite após a geração de um evento. Quando o tempo limite expirar, o evento de encerramento pendente será processado e a VM será excluída.
-   Valor de tempo limite modificável – você pode modificar o valor de tempo limite a qualquer momento antes que uma instância seja excluída, modificando a propriedade *notBeforeTimeout* no modelo do conjunto de dimensionamento e atualizando as instâncias de VM para o modelo mais recente.
-   Aprovar todas as exclusões pendentes – se houver uma exclusão pendente em VM_1 que não está aprovada e você tiver aprovado outro evento de encerramento em VM_2, VM_2 não será excluído até que o evento de encerramento para VM_1 seja aprovado ou seu tempo limite tenha decorrido. Depois de aprovar o evento Terminate para VM_1, os VM_1 e VM_2 são excluídos.
-   Aprovar todas as exclusões simultâneas – estendendo o exemplo acima, se VM_1 e VM_2 tiverem o mesmo tempo *antes* , então ambos os eventos de término deverão ser aprovados ou nenhuma VM será excluída antes que o tempo limite expire.

## <a name="troubleshoot"></a>Solucionar problemas
### <a name="failure-to-enable-scheduledeventsprofile"></a>Falha ao habilitar scheduledEventsProfile
Se você receber um erro ' BadRequest ' com uma mensagem de erro informando "não foi possível encontrar o membro ' scheduledEventsProfile ' no objeto do tipo ' VirtualMachineProfile '", verifique a versão da API usada para as operações do conjunto de dimensionamento. A versão de API de computação **2019-03-01** ou superior é necessária. 

### <a name="failure-to-get-terminate-events"></a>Falha ao obter eventos de encerramento
Se você não estiver obtendo eventos de **término** por meio de eventos agendados, verifique a versão da API usada para obter os eventos. A API do serviço de metadados versão **2019-01-01** ou superior é necessária para eventos de término.
>'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="getting-terminate-event-with-incorrect-notbefore-time"></a>Obtendo evento de encerramento com hora incorreta  
Depois de habilitar o *scheduledEventsProfile* no modelo do conjunto de dimensionamento e definir o *notBeforeTimeout*, atualize as instâncias individuais para o [modelo mais recente](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) para refletir as alterações.

## <a name="next-steps"></a>Próximas etapas
Aprenda como [Implantar o aplicativo](virtual-machine-scale-sets-deploy-app.md) em conjuntos de dimensionamento de máquinas virtuais
