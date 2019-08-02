---
title: Manipulação de notificações de manutenção para VMs do Windows no Azure | Microsoft Docs
description: Exiba notificações de manutenção para máquinas virtuais do Windows em execução no Azure e inicie a manutenção de autoatendimento.
services: virtual-machines-windows
documentationcenter: ''
author: shants123
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/30/2019
ms.author: shants
ms.openlocfilehash: b388c600e5eb9331fe22d1a8e9aac8f47edb3ef2
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849595"
---
# <a name="handling-planned-maintenance-notifications-for-windows-virtual-machines"></a>Administração de notificações de manutenção planejada para máquinas virtuais do Windows

O Azure realiza atualizações periodicamente para aumentar a confiabilidade, o desempenho e a segurança da infraestrutura de host para máquinas virtuais. As atualizações são as alterações como, por exemplo, aplicação de patches no ambiente de hospedagem ou atualização e desativação de hardware. A maioria dessas atualizações é realizada sem nenhum impacto nas máquinas virtuais hospedadas. No entanto, há casos em que as atualizações possuem um impacto:

- Se a manutenção não exigir uma reinicialização, o Azure usa migração in-loco para pausar a máquina virtual enquanto o host está atualizado. Essas operações de manutenção não reiniciáveis são o domínio de falha aplicado pelo domínio de falha e o andamento é interrompido quando algum sinal de aviso de integridade é recebido. 

- Se a manutenção requer uma reinicialização, você receberá um aviso informando para quando a manutenção está planejada. Nesses casos, você recebe uma janela de tempo que normalmente é de 30 dias, em que você pode iniciar a manutenção por conta própria, quando ela funciona para você.


A manutenção planejada que requer um reinício é agendada em ondas. Cada onda tem um escopo diferente (regiões).

- Uma onda começa com uma notificação para os clientes. Por padrão, a notificação é enviada para o proprietário e os coproprietários da assinatura. Você pode adicionar mais destinatários e opções de mensagem como email, SMS e Webhooks, às notificações usando os [alertas de log de atividades](../../azure-monitor/platform/activity-logs-overview.md) do Azure.  
- No momento da notificação, uma *janela de autoatendimento* é disponibilizada. Durante essa janela que normalmente é de 30 dias, você pode encontrar quais máquinas virtuais estão incluídas nessa onda e iniciar de forma proativa a manutenção de acordo com suas necessidades de agendamento.
- Após a janela de autoatendimento, *janela de manutenção agendada* inicia. Em algum momento durante esta janela, o Azure agenda e aplica a manutenção necessária à sua máquina virtual. 

O objetivo de ter duas janelas é dar a você tempo suficiente para iniciar a manutenção e reiniciar sua máquina virtual sabendo quando o Azure iniciará automaticamente a manutenção.


Você pode usar o portal do Azure, o PowerShell, a API REST e a CLI para consultar as janelas de manutenção para suas VMs e iniciar a manutenção de autoatendimento.

  
## <a name="should-you-start-maintenance-during-the-self-service-window"></a>Você deve iniciar a manutenção durante a janela de autoatendimento?  

As diretrizes a seguir devem ajudá-lo a decidir se você deseja usar essa funcionalidade e a iniciar a manutenção no seu próprio tempo.

> [!NOTE] 
> A manutenção de autoatendimento pode não estar disponível para todas as suas VMs. Para determinar se a reimplantação pró-ativa está disponível para sua VM, procure o status de manutenção **Iniciar agora**. No momento, a manutenção de autoatendimento não está disponível para os serviços de nuvem (função Web/de trabalho) nem para o Service Fabric.


A manutenção de autoatendimento não é recomendada para implantações usando **conjuntos de disponibilidade**, pois elas são configurações altamente disponíveis, em que apenas um domínio de atualização é afetado a qualquer momento. 
- Permita que o Azure dispare a manutenção. Para a manutenção que exige reinício, lembre-se de que a manutenção será feita de domínio de atualização em domínio de atualização, que os domínios de atualização não necessariamente recebem a manutenção em sequência e que há uma pausa de 30 minutos entre os domínios de atualização. 
- Se a perda temporária de parte da capacidade (1/contagem de domínios de atualização) é uma preocupação, é possível compensar isso facilmente alocando instâncias de adição durante o período de manutenção.
- Para a manutenção que não exige reinicialização, as atualizações são aplicadas no nível do domínio de falha.
    
**Não** use o autoatendimento de manutenção nos seguintes cenários: 
- Se você desliga as VMs com frequência, seja manualmente, usando o DevTest Labs, usando o desligamento automático ou seguindo um agendamento, é possível reverter o status de manutenção e, portanto, aumentar o tempo de inatividade.
- Em VMs de curta duração que você sabe que serão excluídas antes do final da onda de manutenção. 
- Para cargas de trabalho com um estado grande armazenado no disco local (efêmero) e que se deseja manter após a atualização. 
- Para casos em que você redimensiona a VM com frequência, pois isso pode reverter o status de manutenção.
- Se você adotou eventos agendados que permitem um failover proativo ou o desligamento normal da carga de trabalho, 15 minutos antes do início do desligamento da manutenção

**Use** a manutenção de autoatendimento se você estiver planejando executar sua VM ininterrupta durante a fase de manutenção agendada e nenhuma das indicações de contadores mencionadas acima são aplicáveis. 

É melhor usar a manutenção de autoatendimento nos seguintes casos:

- Você precisa comunicar uma janela de manutenção exata para o gerenciamento ou o cliente final. 
- Você precisa concluir a manutenção em uma determinada data. 
- Você precisa controlar a sequência de manutenção, por exemplo, o aplicativo de várias camadas para garantir uma recuperação segura.
- Você precisa de mais de 30 minutos de tempo de recuperação de máquina virtual entre dois domínios de atualização (UDs). Para controlar o tempo entre domínios de atualização, você deve disparar a manutenção em suas VMs um domínio de atualização (UD) por vez.

 

[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]

## <a name="check-maintenance-status-using-powershell"></a>Verificar o status de manutenção usando o PowerShell

Você também pode usar o Azure Powershell para ver quando as VMs serão agendadas para manutenção. As informações de manutenção planejadas estão disponíveis no cmdlet [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) quando você usa o parâmetro `-status`.
 
As informações de manutenção só serão retornadas se houver manutenção planejada. Se não houver nenhuma manutenção agendada que afete a VM, o cmdlet não retornará nenhuma informação de manutenção. 

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

```powershell
Get-AzVM -ResourceGroupName rgName -Name vmName -Status
```

As propriedades a seguir são retornadas em MaintenanceRedeployStatus: 

| Valor | DESCRIÇÃO   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Indica se você pode iniciar a manutenção na máquina virtual neste momento |
| PreMaintenanceWindowStartTime         | O início da janela de autoatendimento de manutenção quando você pode iniciar a manutenção na sua VM |
| PreMaintenanceWindowEndTime           | O fim da janela de autoatendimento de manutenção quando você pode iniciar manutenção na sua VM |
| MaintenanceWindowStartTime            | O início da manutenção agendada na qual o Azure inicia a manutenção na sua VM |
| MaintenanceWindowEndTime              | O término da janela de manutenção agendada na qual o Azure inicia a manutenção na sua VM |
| LastOperationResultCode               | O resultado da última tentativa de iniciar a manutenção na VM |



Você também pode obter o status de manutenção para todas as VMs em um grupo de recursos usando [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm), sem especificar a VM.
 
```powershell
Get-AzVM -ResourceGroupName rgName -Status
```

A função do PowerShell a seguir usa a sua ID de assinatura e imprime uma lista de máquinas virtuais que estão agendados para manutenção.

```powershell

function MaintenanceIterator
{
    Select-AzSubscription -SubscriptionId $args[0]

    $rgList= Get-AzResourceGroup 

    for ($rgIdx=0; $rgIdx -lt $rgList.Length ; $rgIdx++)
    {
        $rg = $rgList[$rgIdx]        
    $vmList = Get-AzVM -ResourceGroupName $rg.ResourceGroupName 
        for ($vmIdx=0; $vmIdx -lt $vmList.Length ; $vmIdx++)
        {
            $vm = $vmList[$vmIdx]
            $vmDetails = Get-AzVM -ResourceGroupName $rg.ResourceGroupName -Name $vm.Name -Status
              if ($vmDetails.MaintenanceRedeployStatus )
            {
                Write-Output "VM: $($vmDetails.Name)  IsCustomerInitiatedMaintenanceAllowed: $($vmDetails.MaintenanceRedeployStatus.IsCustomerInitiatedMaintenanceAllowed) $($vmDetails.MaintenanceRedeployStatus.LastOperationMessage)"               
            }
          }
    }
}

```

### <a name="start-maintenance-on-your-vm-using-powershell"></a>Iniciar manutenção na sua VM usando o PowerShell

Usando as informações da função na seção anterior, o seguinte iniciará a manutenção em uma VM se **IsCustomerInitiatedMaintenanceAllowed** for definido como true.

```powershell
Restart-AzVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```

## <a name="classic-deployments"></a>Implantações clássicas

Caso você ainda tenha VMs herdadas que foram implantadas usando o modelo de implantação clássico, use o PowerShell para consultar VMs e iniciar a manutenção.

Para obter o status de manutenção de uma VM, digite:

```
Get-AzureVM -ServiceName <Service name> -Name <VM name>
```

Para iniciar a manutenção na VM clássica, digite:

```
Restart-AzureVM -InitiateMaintenance -ServiceName <service name> -Name <VM name>
```


## <a name="faq"></a>Perguntas Frequentes


**P: Por que você precisa reiniciar minhas máquinas virtuais agora?**

**R:** Embora a maioria das atualizações e upgrades para a plataforma do Azure não afetem a disponibilidade da máquina virtual, há casos em que não podemos evitar o reinício de máquinas virtuais hospedadas no Azure. Nós acumulamos várias alterações que exigem que nossos servidores sejam reiniciados, o que resultará no reinício das máquinas virtuais.

**P: Se eu seguir as recomendações de Alta Disponibilidade usando um Conjunto de Disponibilidade, estarei seguro?**

**R:** As máquinas virtuais implantadas em um conjunto de disponibilidade ou conjuntos de dimensionamento de máquinas virtuais têm a noção de UD (domínios de atualização). Ao fazer a manutenção, o Azure respeita a restrição de UD e não reinicia máquinas virtuais de UDs diferentes (dentro do mesmo conjunto de disponibilidade).  O Azure também espera pelo menos 30 minutos antes de passar para o próximo grupo de máquinas virtuais. 

Para obter mais informações sobre alta disponibilidade, consulte [disponibilidade para máquinas virtuais no Azure](availability.MD).

**P: Como fazer para ser notificado sobre manutenção planejada?**

**R:** uma onda manutenção planejada começa pela configuração de uma agenda para uma ou mais regiões do Azure. Assim, uma notificação por email é enviada para proprietários de assinaturas (um email por assinatura). Os canais adicionais e os destinatários dessa notificação podem ser configurados usando Alertas de Log de Atividades. Caso você implante uma máquina virtual em uma região em que a manutenção planejada já foi agendada, não receberá a notificação e precisará verificar o estado de manutenção da VM.

**P: Não vejo nenhuma indicação de manutenção planejada no portal, no Powershell ou na CLI. Qual é o problema?**

**R:** As informações relacionadas à manutenção planejada ficam disponíveis durante uma onda de manutenção planejada apenas para as VMs que serão afetadas por ela. Em outras palavras, se você não vir os dados, pode ser que a fase de manutenção já tenha sido concluída (ou não iniciada) ou que sua máquina virtual já esteja hospedada em um servidor atualizado.

**P: Há uma maneira de saber exatamente quando minha máquina virtual será afetada?**

**R:** Ao definir a agenda, definiremos um período de tempo de vários dias. No entanto, a sequência exata dos servidores (e de VMs) nesse período é desconhecido. Os clientes que querem saber a hora exata para suas VMs podem usar [eventos agendados](scheduled-events.md) e consultar na máquina virtual para receber uma notificação de 15 minutos antes do reinício de uma VM.

**P: Quanto tempo você levará para reiniciar minha máquina virtual?**

**R:**  Dependendo do tamanho da VM, o reinício poderá levar vários minutos durante a janela de manutenção por autoatendimento. Durante as reinicializações iniciadas pelo Azure na janela de manutenção agendada, a reinicialização geralmente levará cerca de 25 minutos. Observe que, caso você use Serviços de Nuvem (função Web/de trabalho), Conjuntos de Dimensionamento de Máquinas Virtuais ou conjuntos de disponibilidade, você terá 30 minutos entre cada grupo de VMs (UD) durante a janela de manutenção agendada. 

**P: Qual é a experiência no caso de Conjuntos de Dimensionamento de Máquinas Virtuais?**

**R:** A manutenção planejada agora está disponível para Conjuntos de Dimensionamento de Máquinas Virtuais. Para obter instruções sobre como iniciar a manutenção de autoatendimento, consulte o documento [Manutenção planejada para VMSS](../../virtual-machine-scale-sets/virtual-machine-scale-sets-maintenance-notifications.md).

**P: Qual é a experiência no caso do Serviço de Nuvem (Função de trabalho/Web) e Service Fabric?**

**R:** Embora essas plataformas sejam afetadas pela manutenção planejada, considera-se que os clientes que usam essas plataformas estejam seguros, já que somente as VMs em um UD (domínio de atualização) simples serão afetadas em determinado momento. No momento, a manutenção de autoatendimento não está disponível para os serviços de nuvem (função Web/de trabalho) nem para o Service Fabric.

**P: Não vejo informações sobre manutenção nas minhas VMs. O que deu errado?**

**R:** Há vários motivos pelos quais você não está vendo informações de manutenção em suas VMs:
1.  Você está usando uma assinatura marcada como Microsoft interna.
2.  Suas VMs não estão agendadas para manutenção. É possível que a onda de manutenção tenha sido concluída, cancelada ou modificada de modo que suas VMs não são afetadas por ela.
3.  Você não tem a coluna **Manutenção** adicionada ao modo de exibição de lista da VM. Embora tenhamos adicionado essa coluna à exibição padrão, os clientes que configuraram para ver colunas não padrão devem adicionar manualmente a coluna **Manutenção** ao modo de exibição de lista da VM.

**P: Minha VM está agendada para manutenção pela segunda vez. Por quê?**

**R:** Há diversos casos de uso em que você verá sua VM agendada para manutenção depois de ter concluído a reimplantação da manutenção:
1.  Nós cancelamos a fase de manutenção e a reiniciamos com uma carga diferente. É possível que tenhamos detectado uma carga com falha e seja necessário simplesmente implantar uma carga adicional.
2.  A VM foi *recuperada* em outro nó devido a uma falha de hardware
3.  Você optou por parar (desalocar) e reiniciar a máquina virtual
4.  Você tem o **desligamento automático** ligado para a VM


## <a name="next-steps"></a>Próximas etapas

Saiba como você pode registrar-se para eventos de manutenção dentro da VM usando [Eventos agendados](scheduled-events.md).
