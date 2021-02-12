---
title: Notificações de manutenção para conjuntos de dimensionamento de máquinas virtuais no Azure
description: Exiba notificações de manutenção e inicie a manutenção de autoatendimento para conjuntos de dimensionamento de máquinas virtuais no Azure.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 11/12/2020
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 2aa589d237a8cfeb8e0dc947896dba82e755631c
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94564762"
---
# <a name="planned-maintenance-notifications-for-virtual-machine-scale-sets"></a>Notificações de manutenção planejada para conjuntos de dimensionamento de máquinas virtuais


O Azure executa periodicamente atualizações para melhorar a confiabilidade, o desempenho e a segurança da infraestrutura do host para VMs (máquinas virtuais). As atualizações podem incluir aplicação de patch do ambiente de hospedagem ou atualização e descomissionamento de hardware. A maioria das atualizações não afeta as VMs hospedadas. No entanto, as atualizações afetam as VMs nesses cenários:

- Se a manutenção não exigir uma reinicialização, o Azure pausará a VM por alguns segundos enquanto o host for atualizado. Esses tipos de operações de manutenção são aplicados ao domínio de falha por domínio de falha. O andamento será interrompido se algum sinal de integridade de aviso for recebido.

- Se a manutenção requer uma reinicialização, você receberá um aviso informando para quando a manutenção está planejada. Nesses casos, você recebe uma janela de tempo que normalmente é de 35 dias em que você pode iniciar a manutenção por conta própria, quando ela funciona para você.


Uma manutenção planejada que requer uma reinicialização é agendada em ondas. Cada onda tem um escopo diferente (regiões):

- Uma onda começa com uma notificação para os clientes. Por padrão, a notificação é enviada ao proprietário e aos coproprietários da assinatura. É possível adicionar destinatários e opções de mensagens como email, SMS e webhooks às notificações, usando [alertas do Log de Atividades](../azure-monitor/platform/platform-logs-overview.md) do Azure.  
- Com a notificação, uma *janela de autoatendimento* é disponibilizada. Durante essa janela que normalmente é de 35 dias, você pode encontrar quais das suas VMs estão incluídas na onda. É possível iniciar a manutenção proativamente de acordo com suas próprias necessidades de agendamento.
- Após a janela de autoatendimento, *janela de manutenção agendada* inicia. Em algum ponto durante essa janela, o Azure agenda e aplica a manutenção necessária à VM. 

A meta de ter duas janelas é fornecer-lhe tempo suficiente para iniciar a manutenção e reiniciar a VM, sabendo quando o Azure iniciará a manutenção automaticamente.

É possível usar o portal do Azure, o PowerShell, a API REST e a CLI do Azure para consultar as janelas de manutenção das VMs do conjunto de dimensionamento de máquinas virtuais e iniciar a manutenção de autoatendimento.

## <a name="should-you-start-maintenance-during-the-self-service-window"></a>Você deve iniciar a manutenção durante a janela de autoatendimento?  

As diretrizes a seguir podem ajudá-lo a decidir se deseja iniciar a manutenção no horário que você escolher.

> [!NOTE] 
> A manutenção de autoatendimento pode não estar disponível para todas as suas VMs. Para determinar se a reimplantação proativa está disponível para a VM, procure **Iniciar agora** no status de manutenção. Atualmente, a manutenção de autoatendimento não está disponível para Serviços de Nuvem do Azure (Função de Trabalho/Web) e Microsoft Azure Service Fabric.


Não é recomendável a manutenção de autoatendimento para implantações que usam *conjuntos de disponibilidade*. Conjuntos de disponibilidade são configurações altamente disponíveis nas quais apenas um domínio de atualização é afetado a qualquer momento. Para conjuntos de disponibilidade:

- Permita que o Azure dispare a manutenção. Para manutenção que requer um reinício, a manutenção é feita no domínio de atualização pelo domínio de atualização. Os domínios de atualização não recebem necessariamente a manutenção sequencialmente. Há uma pausa de 30 minutos entre os domínios de atualização.
- Se uma perda temporária de parte da capacidade (1/contagem de domínio de atualização) for uma preocupação, você poderá compensar facilmente a perda, alocando instâncias adicionais durante o período de manutenção.
- Para manutenção que não requer reinício, as atualizações são aplicadas no nível do domínio de falha. 
    
**Não** use o autoatendimento de manutenção nos seguintes cenários: 

- Se você desligar as VMs com frequência, seja manualmente, usando DevTest Labs, usando o desligamento automático ou seguindo um agendamento. A manutenção de autoatendimento nesses cenários pode reverter o status de manutenção e causar tempo de inatividade adicional.
- Em VMs de curta duração que você sabe que serão excluídas antes do final da onda de manutenção. 
- Para cargas de trabalho com um estado grande armazenado no disco local (efêmero) que você quer manter após a atualização. 
- Se você redimensionar a VM com frequência. Esse cenário pode reverter o status de manutenção. 
- Se você adotou eventos agendados que permitem failover proativo ou desligamento normal da carga de trabalho 15 minutos antes do início do desligamento da manutenção.

**Use** a manutenção de autoatendimento se você planeja executar a VM ininterruptamente durante a fase de manutenção agendada e nenhuma das contra-indicações anteriores será aplicável. 

É melhor usar a manutenção de autoatendimento nos seguintes casos:

- É necessário comunicar uma janela de manutenção exata ao gerenciamento ou cliente. 
- É necessário concluir a manutenção até uma data específica. 
- É necessário controlar a sequência de manutenção, por exemplo, em um aplicativo multicamadas, para garantir uma recuperação segura.
- Você precisa de mais de 30 minutos de tempo de recuperação de VM entre dois domínios de atualização. Para controlar o tempo entre os domínios de atualização, será necessário disparar a manutenção em um domínio de atualização de uma VM por vez.

 
## <a name="view-virtual-machine-scale-sets-that-are-affected-by-maintenance-in-the-portal"></a>Exibir conjuntos de dimensionamento de máquinas virtuais que são afetados pela manutenção no portal

Quando uma onda de manutenção planejada é agendada, é possível exibir a lista de conjuntos de dimensionamento de máquinas virtuais que serão afetados pela próxima onda de manutenção usando o portal do Azure. 

1. Entre no [portal do Azure](https://portal.azure.com).
2. No menu esquerdo, selecione **Todos os serviços** e, em seguida, selecione **Conjuntos de dimensionamento de máquinas virtuais**.
3. Em **Conjuntos de dimensionamento de máquinas virtuais** , selecione **Editar colunas** para abrir a lista de colunas disponíveis.
4. Na seção **Colunas disponíveis** , selecione **Manutenção de autoatendimento** e, em seguida, mova-a para a lista **Colunas selecionadas**. Escolha **Aplicar**.  

    Para facilitar a localização do item de **Manutenção de autoatendimento** , é possível alterar a opção suspensa na seção **Colunas disponíveis** de **Todos** para **Propriedades**.

A **Manutenção de autoatendimento** agora aparece na lista de conjuntos de dimensionamento de máquinas virtuais. Cada conjunto de dimensionamento de máquina virtual pode ter um dos seguintes valores para a coluna de manutenção de autoatendimento:

| Valor | Descrição |
|-------|-------------|
| Sim | Pelo menos uma VM no conjunto de dimensionamento de máquinas virtuais está em uma janela de autoatendimento. Você pode iniciar a manutenção a qualquer momento durante essa janela de autoatendimento. | 
| Não | Nenhuma VM está em uma janela de autoatendimento no conjunto de dimensionamento de máquinas virtuais afetado. | 
| - | Os conjuntos de dimensionamento de máquinas virtuais não fazem parte de uma onda de manutenção planejada.| 

## <a name="notification-and-alerts-in-the-portal"></a>Notificação e alertas no portal

O Azure comunica uma agenda para manutenção planejada, enviando um email para o grupo de proprietário e os coadministradores de assinatura. É possível adicionar destinatários e canais a essa comunicação, criando alertas do Log de Atividades. Para obter mais informações, consulte [Monitorar a atividade da assinatura com o Log de Atividades do Azure](../azure-monitor/platform/platform-logs-overview.md).

1. Entre no [portal do Azure](https://portal.azure.com).
2. No menu esquerdo, selecione **Monitorar**. 
3. No painel **Monitorar - Alertas (clássico)** , selecione **+Adicionar alerta do log de atividades**.
4. Na página **Adicionar alerta do log de atividades** , selecione ou insira as informações solicitadas. Em **Critérios** , certifique-se de definir os valores a seguir:
   - **Categoria de eventos** : selecione **Integridade do Serviço**.
   - **Serviços** : selecione **Conjuntos de Dimensionamento de Máquinas Virtuais e Máquinas Virtuais**.
   - **Tipo** : selecione **Manutenção planejada**. 
    
Para saber mais sobre como configurar os alertas do Log de Atividades, consulte [Criar alertas do Log de Atividades](../azure-monitor/platform/activity-log-alerts.md)
    
    
## <a name="start-maintenance-on-your-virtual-machine-scale-set-from-the-portal"></a>Iniciar a manutenção em seu conjunto de dimensionamento de máquina virtual no portal

É possível ver mais detalhes relacionados à manutenção na visão geral dos conjuntos de dimensionamento de máquinas virtuais. Se pelo menos uma VM no conjunto de dimensionamento de máquinas virtuais estiver incluída na onda de manutenção planejada, uma nova faixa de notificação será adicionada na parte superior da página. Selecione a faixa de notificação para acessar a página **Manutenção**. 

Na página **Manutenção** , é possível ver qual instância da VM é afetada pela manutenção planejada. Para iniciar a manutenção, selecione a caixa de seleção que corresponde à VM afetada. Em seguida, selecione **Iniciar manutenção**.

Após iniciar a manutenção, as VMs afetadas no conjunto de dimensionamento de máquinas virtuais passarão por manutenção e ficarão temporariamente indisponíveis. Se você perdeu a janela de autoatendimento, ainda será possível ver a janela de tempo quando o conjunto de dimensionamento de máquinas virtuais passar pela manutenção do Azure.
 
## <a name="check-maintenance-status-by-using-powershell"></a>Verificar o status de manutenção usando PowerShell

É possível usar o Microsoft Azure PowerShell para ver quando as VMs nos conjuntos de dimensionamento de máquinas virtuais estão agendadas para manutenção. As informações de manutenção planejada estarão disponíveis usando o cmdlet [Get-AzVmss](/powershell/module/az.compute/get-azvmss) quando você utilizar o parâmetro `-InstanceView`.
 
As informações de manutenção serão retornadas somente se a manutenção for planejada. Se não houver manutenção agendada que afete a instância da VM, o cmdlet não retornará nenhuma informação de manutenção. 

```powershell
Get-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -InstanceView
```

As seguintes propriedades são retornadas em **MaintenanceRedeployStatus** : 

| Valor | Descrição   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Indica se é possível iniciar a manutenção na VM neste momento. |
| PreMaintenanceWindowStartTime         | O início da janela de autoatendimento de manutenção quando você pode iniciar a manutenção na VM. |
| PreMaintenanceWindowEndTime           | O fim da janela de autoatendimento de manutenção quando você pode iniciar manutenção na VM. |
| MaintenanceWindowStartTime            | O início da manutenção agendada na qual o Azure inicia a manutenção na VM. |
| MaintenanceWindowEndTime              | O término da janela de manutenção agendada na qual o Azure inicia a manutenção na VM. |
| LastOperationResultCode               | O resultado da última tentativa de iniciar a manutenção na VM. |



### <a name="start-maintenance-on-your-vm-instance-by-using-powershell"></a>Iniciar manutenção na instância de VM usando PowerShell

É possível iniciar a manutenção em uma VM se **IsCustomerInitiatedMaintenanceAllowed** estiver definido como **true**. Use o cmdlet [Set-AzVmss](/powershell/module/az.compute/set-azvmss) com o parâmetro `-PerformMaintenance`.

```powershell
Set-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -PerformMaintenance 
```

## <a name="check-maintenance-status-by-using-the-cli"></a>Verificar status de manutenção usando a CLI

É possível exibir as informações de manutenção planejada usando [az vmss list-instances](/cli/azure/vmss?view=azure-cli-latest#az-vmss-list-instances).
 
As informações de manutenção serão retornadas somente se a manutenção for planejada. Se nenhuma manutenção que afete a instância de VM estiver agendada, o comando não retornará nenhuma informação de manutenção. 

```azurecli
az vmss list-instances -g rgName -n vmssName --expand instanceView
```

As propriedades a seguir são retornadas em **MaintenanceRedeployStatus** para cada instância de VM: 

| Valor | Descrição   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Indica se é possível iniciar a manutenção na VM neste momento. |
| PreMaintenanceWindowStartTime         | O início da janela de autoatendimento de manutenção quando você pode iniciar a manutenção na VM. |
| PreMaintenanceWindowEndTime           | O fim da janela de autoatendimento de manutenção quando você pode iniciar manutenção na VM. |
| MaintenanceWindowStartTime            | O início da manutenção agendada na qual o Azure inicia a manutenção na VM. |
| MaintenanceWindowEndTime              | O término da janela de manutenção agendada na qual o Azure inicia a manutenção na VM. |
| LastOperationResultCode               | O resultado da última tentativa de iniciar a manutenção na VM. |


### <a name="start-maintenance-on-your-vm-instance-by-using-the-cli"></a>Iniciar manutenção na instância de VM usando a CLI

A chamada a seguir iniciará a manutenção em uma instância de VM se `IsCustomerInitiatedMaintenanceAllowed` estiver definido como **true** :

```azurecli
az vmss perform-maintenance -g rgName -n vmssName --instance-ids id
```

## <a name="faq"></a>Perguntas frequentes

**P: Por que é necessário reiniciar as VMs agora?**

**R:** Embora a maioria das atualizações e upgrades para a plataforma do Azure não afetem a disponibilidade da VM, em alguns casos não é possível evitar o reinício de VMs hospedadas no Azure. Há várias alterações acumuladas que exigem reiniciar os servidores, o que resultará no reinício da VM.

**P: Se eu seguir as recomendações de alta disponibilidade usando um conjunto de disponibilidade, estarei seguro?**

**R:** Máquinas virtuais implantadas em um conjunto de disponibilidade ou em conjuntos de dimensionamento de máquinas virtuais usam domínios de atualização. Ao executar a manutenção, o Azure respeita a restrição do domínio de atualização e não reinicia as VMs de um domínio de atualização diferente (dentro do mesmo conjunto de disponibilidade). O Azure também aguarda pelo menos 30 minutos antes de passar para o próximo grupo de VMs. 

Para obter mais informações sobre alta disponibilidade, consulte [Regiões e disponibilidade para máquinas virtuais no Azure](../virtual-machines/availability.md).

**P: Como posso ser notificado sobre manutenção planejada?**

**R:** uma onda manutenção planejada começa pela definição de uma agenda para uma ou mais regiões do Azure. Logo após, uma notificação por email é enviada para os administradores de assinatura, os coadministradores, os proprietários e os colaboradores (um email por assinatura). Os canais adicionais e os destinatários dessa notificação podem ser configurados usando Alertas de Log de Atividades. Caso você implante uma máquina virtual em uma região em que a manutenção planejada já esteja agendada, você não receberá a notificação. Em vez disso, verifique o estado de manutenção da VM.

**P: não vejo nenhuma indicação de manutenção planejada no portal, no PowerShell ou na CLI. Qual é o problema?**

**R:** As informações relacionadas à manutenção planejada estarão disponíveis durante uma onda de manutenção planejada apenas para as VMs afetadas pela manutenção planejada. Se não for possível visualizar os dados, a onda de manutenção já poderá ter sido concluída (ou não iniciada) ou a VM já poderá estar hospedada em um servidor atualizado.

**P: Existe uma maneira de saber exatamente quando a VM será afetada?**

**R:** Quando definimos o agendamento, definimos uma janela de tempo de vários dias. A sequência exata dos servidores (e de VMs) nesse período é desconhecida. Se você quiser saber o tempo exato em que as VMs serão atualizadas, poderá usar [eventos agendados](../virtual-machines/windows/scheduled-events.md). Ao usar eventos agendados, é possível consultar a partir da VM e receber uma notificação de 15 minutos antes do reinício da VM.

**P: Quanto tempo irá demorar para reiniciar a VM?**

**R:**  Dependendo do tamanho da VM, o reinício poderá demorar vários minutos durante a janela de manutenção de autoatendimento. Durante os reinícios iniciados pelo Azure na janela de manutenção agendada, o reinício normalmente demora cerca de 25 minutos. Se usar Serviços de Nuvem (Função de Trabalho/Web), conjuntos de dimensionamento de máquinas virtuais, ou conjuntos de disponibilidade, terá 30 minutos entre cada grupo de VMs (atualização de domínio) durante a janela de manutenção programada. 

**P: não vejo nenhuma informação de manutenção em minhas VMs. O que deu errado?**

**R:** Há vários motivos pelos quais não é possível ver as informações de manutenção nas VMs:
   - Você está usando uma assinatura marcada como *Interno da Microsoft*.
   - As VMs não estão agendadas para manutenção. Pode ser que a onda de manutenção tenha sido encerrada, cancelada ou modificada para que as VMs não sejam mais afetadas pela manutenção.
   - Você não tem a coluna **Manutenção** adicionada ao modo de exibição de lista da VM. Embora tenhamos adicionado essa coluna à exibição padrão, se você configurar a exibição para ver colunas não padrão, deverá adicionar manualmente a coluna **Manutenção** à exibição de lista da VM.

**P: minha VM está agendada para manutenção pela segunda vez. Por?**

**R:** Em vários casos de uso, a VM estará agendada para manutenção após ter concluído a manutenção e reimplantado:
   - Nós cancelamos a fase de manutenção e a reiniciamos com uma carga diferente. Pode ser que tenhamos detectado uma carga com falha e seja necessário simplesmente implantar uma carga adicional.
   - Sua máquina virtual teve o *serviço autorrestabelecido* para outro nó devido a uma falha de hardware.
   - Você optou por parar (desalocar) e reiniciar a VM.
   - O **desligamento automático** está ativado para a VM.

## <a name="next-steps"></a>Próximas etapas

Saiba como registrar eventos de manutenção na VM usando [eventos agendados](../virtual-machines/windows/scheduled-events.md).
