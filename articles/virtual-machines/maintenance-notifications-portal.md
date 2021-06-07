---
title: Usar o portal para notificações de manutenção
description: Exibir notificações de manutenção para máquinas virtuais em execução no Azure e iniciar a manutenção de autoatendimento, usando o Portal.
author: shants123
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: a13d79a28ac07d736b1eaf0d0e6b7f7b1ba4a4ec
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102557702"
---
# <a name="handling-planned-maintenance-notifications-using-the-portal"></a>Lidando com notificações de manutenção planejada usando o portal

**Este artigo se aplica a máquinas virtuais que executam o Linux e o Windows.**

Depois que uma onda de [manutenção planejada](maintenance-notifications.md) for agendada, você poderá verificar se há uma lista de máquinas virtuais afetadas. 

É possível usar o portal do Azure e procurar as VMs agendadas para manutenção.

1. Entre no [portal do Azure](https://portal.azure.com).

2. No painel de navegação esquerdo, clique em **máquinas virtuais**.

3. No painel máquinas virtuais, selecione o botão **Editar colunas** para abrir a lista de colunas disponíveis.

4. Selecione e adicione as seguintes colunas:

   **Status de manutenção**: mostra o status de manutenção para a VM. Estes são os valores possíveis:
      
    | Valor | Descrição |
    |-------|-------------|
    | Comece agora | A VM está na janela de manutenção de autoatendimento que permite que você inicie a manutenção por conta própria. Veja abaixo como iniciar a manutenção em sua VM. | 
    | Agendado | A VM está agendada para manutenção sem a opção de iniciar a manutenção. Você pode aprender sobre a janela de manutenção selecionando a janela de manutenção agendada neste modo de exibição ou clicando na VM. | 
    | Já atualizado | Sua VM já está atualizada e nenhuma ação adicional é necessária no momento. | 
    | Tente novamente mais tarde | Você iniciou a manutenção, mas ela apresentou falha. Você poderá usar a opção de manutenção de autoatendimento em um momento posterior. | 
    | Tente agora | Você pode tentar novamente uma manutenção autoiniciada anteriormente malsucedida. | 
    | - | Sua VM não faz parte de uma onda de manutenção planejada. |

   **Janela de manutenção de autoatendimento**: mostra a janela de tempo, quando é possível iniciar automaticamente a manutenção nas VMs.
   
   **Janela de manutenção agendada**: mostra a janela de tempo, quando o Azure fará a manutenção da VM. 



## <a name="notification-and-alerts-in-the-portal"></a>Notificação e alertas no portal

O Azure comunica uma agenda para manutenção planejada, enviando um email para o grupo de proprietário e os coadministradores de assinatura. Você pode adicionar outros destinatários e canais para essa comunicação com a criação de alertas de log de atividades do Azure. Para obter mais informações, consulte [Criar alertas do log de atividades em notificações de serviço](../service-health/alerts-activity-log-service-notifications-portal.md).

Certifique-se de definir o **tipo de evento** como **manutenção planejada** e **Serviços** como conjuntos de **dimensionamento** de máquinas virtuais e/ou **máquinas virtuais**.

## <a name="start-maintenance-on-your-vm-from-the-portal"></a>Iniciar Manutenção na sua VM do portal

Ao examinar os detalhes da VM, você poderá ver mais detalhes relacionados à manutenção.  
Na parte superior do modo de exibição de detalhes VM, uma nova faixa de opções de notificação será adicionada se sua VM for incluída em uma onda de manutenção planejada. Além disso, uma nova opção é adicionada ao iniciar manutenção quando possível. 


Clique na notificação de manutenção para ver a página de manutenção com mais detalhes sobre a manutenção planejada. Nesse local, você pode **iniciar a manutenção** da VM.

Quando você inicia a manutenção, a máquina virtual passa pelo processo de manutenção e o respectivo status é atualizado para refletir o resultado dentro de alguns minutos.

Caso perca a janela de autoatendimento, você ainda poderá exibi-la quando a VM passar pela manutenção do Microsoft Azure. 


## <a name="next-steps"></a>Próximas etapas

Você também pode manipular a manutenção planejada usando o [CLI do Azure](maintenance-notifications-cli.md) ou o [PowerShell](maintenance-notifications-powershell.md).
