---
title: Notificações de manutenção
description: Visão geral das notificações de manutenção para máquinas virtuais em execução no Azure.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 8/12/2020
ms.author: shants
ms.openlocfilehash: 53cde1178a4faae0fbd11222e4219f70be29145d
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94560801"
---
# <a name="handling-planned-maintenance-notifications"></a>Manipulando notificações de manutenção planejada

O Azure realiza atualizações periodicamente para aumentar a confiabilidade, o desempenho e a segurança da infraestrutura de host para máquinas virtuais. As atualizações são as alterações como, por exemplo, aplicação de patches no ambiente de hospedagem ou atualização e desativação de hardware. A maioria dessas atualizações é concluída sem nenhum impacto nas máquinas virtuais hospedadas. No entanto, há casos em que as atualizações possuem um impacto:

- Se a manutenção não exigir uma reinicialização, o Azure pausará a VM por alguns segundos enquanto o host for atualizado. Esses tipos de operações de manutenção são aplicados ao domínio de falha por domínio de falha. O andamento será interrompido se algum sinal de integridade de aviso for recebido.

- Se a manutenção requer uma reinicialização, você receberá um aviso informando para quando a manutenção está planejada. Você recebe uma janela de tempo de cerca de 35 dias, em que você pode iniciar a manutenção por conta própria, quando ela funciona para você.


Uma manutenção planejada que requer uma reinicialização é agendada em ondas. Cada onda tem um escopo diferente (regiões).

- Uma onda começa com uma notificação para os clientes. Por padrão, a notificação é enviada para o administrador da assinatura e os coadministradores. Você pode adicionar mais destinatários e opções de mensagens como email, SMS e WebHooks, usando [alertas do log de atividades](../service-health/alerts-activity-log-service-notifications-portal.md).  
- Quando uma notificação sai, uma *janela de autoatendimento* é disponibilizada. Durante essa janela, você pode consultar quais das suas máquinas virtuais são afetadas e iniciar a manutenção com base nas suas necessidades de agendamento. A janela de autoatendimento normalmente é de cerca de 35 dias.
- Após a janela de autoatendimento, *janela de manutenção agendada* inicia. Em algum momento durante esta janela, o Azure agenda e aplica a manutenção necessária à sua máquina virtual. 

O objetivo de ter duas janelas é dar a você tempo suficiente para iniciar a manutenção e reiniciar sua máquina virtual sabendo quando o Azure iniciará automaticamente a manutenção.


Você pode usar o portal do Azure, o PowerShell, a API REST e a CLI para consultar as janelas de manutenção para suas VMs e iniciar a manutenção de autoatendimento.

 
## <a name="should-you-start-maintenance-using-during-the-self-service-window"></a>Você deve começar a usar a manutenção durante a janela de autoatendimento?  

As diretrizes a seguir devem ajudá-lo a decidir se você deseja usar essa funcionalidade e a iniciar a manutenção no seu próprio tempo. 

> [!NOTE] 
> A manutenção de autoatendimento pode não estar disponível para todas as suas VMs. Para determinar se a reimplantação pró-ativa está disponível para sua VM, procure o status de manutenção **Iniciar agora**. No momento, a manutenção de autoatendimento não está disponível para os serviços de nuvem (função Web/de trabalho) nem para o Service Fabric.


A manutenção de autoatendimento não é recomendada para implantações usando **conjuntos de disponibilidade**. Os conjuntos de disponibilidade já estão atualizados apenas em um domínio de atualização por vez. 

- Permita que o Azure dispare a manutenção. Para manutenção que requer reinicialização, a manutenção será feita ao atualizar domínio pelo domínio de atualização. Os domínios de atualização não recebem necessariamente a manutenção sequencialmente e há uma pausa de 30 minutos entre os domínios de atualização. 
- Se uma perda temporária de alguma capacidade (1 domínio de atualização) for uma preocupação, você poderá adicionar instâncias durante o período de manutenção. 
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
- Mais de 30 minutos de tempo de recuperação de VM são necessários entre dois domínios de atualização (UDs). Para controlar o tempo entre domínios de atualização, você deve disparar a manutenção em suas VMs um domínio de atualização (UD) por vez.


## <a name="faq"></a>Perguntas frequentes


**P: por que você precisa reinicializar minhas máquinas virtuais agora?**

**R:** enquanto a maioria das atualizações para a plataforma do Azure não afete a disponibilidade da máquina virtual, há casos em que não podemos evitar o reinício de máquinas virtuais hospedadas no Azure. Nós acumulamos várias alterações que exigem que nossos servidores sejam reiniciados, o que resultará no reinício das máquinas virtuais.

**P: se eu seguir as recomendações para alta disponibilidade usando um conjunto de disponibilidade, estou seguro?**

**R:** as máquinas virtuais implantadas em um conjunto de disponibilidade ou conjuntos de dimensionamento de máquinas virtuais têm a noção de UD (domínios de atualização). Ao fazer a manutenção, o Azure respeita a restrição de UD e não reinicia máquinas virtuais de UDs diferentes (dentro do mesmo conjunto de disponibilidade).  O Azure também espera pelo menos 30 minutos antes de passar para o próximo grupo de máquinas virtuais. 

Para obter mais informações sobre alta disponibilidade, consulte [disponibilidade para máquinas virtuais no Azure](availability.md).

**P: como fazer para ser notificado sobre manutenção planejada?**

**R:** uma onda manutenção planejada começa pela definição de uma agenda para uma ou mais regiões do Azure. Logo após, uma notificação por email é enviada para os administradores de assinatura, os coadministradores, os proprietários e os colaboradores (um email por assinatura). Os canais adicionais e os destinatários dessa notificação podem ser configurados usando Alertas de Log de Atividades. Caso você implante uma máquina virtual em uma região em que a manutenção planejada já foi agendada, não receberá a notificação e precisará verificar o estado de manutenção da VM.

**P: não vejo nenhuma indicação de manutenção planejada no portal, no PowerShell ou na CLI. Qual é o problema?**

**R:** As informações relacionadas à manutenção planejada ficam disponíveis durante uma onda de manutenção planejada apenas para as VMs que serão afetadas por ela. Em outras palavras, se você não vir os dados, pode ser que a fase de manutenção já tenha sido concluída (ou não iniciada) ou que sua máquina virtual já esteja hospedada em um servidor atualizado.

**P: há uma maneira de saber exatamente quando minha máquina virtual será afetada?**

**R:** ao definir a agenda, definiremos um período de tempo de vários dias. No entanto, a sequência exata dos servidores (e de VMs) nesse período é desconhecido. Os clientes que querem saber a hora exata para suas VMs podem usar [eventos agendados](./linux/scheduled-events.md) e consultar na máquina virtual para receber uma notificação de 15 minutos antes do reinício de uma VM.

**P: quanto tempo levará o reinício da minha máquina virtual?**

**R:** dependendo do tamanho da VM, o reinício poderá levar vários minutos durante a janela de manutenção por autoatendimento. Durante as reinicializações iniciadas pelo Azure na janela de manutenção agendada, a reinicialização geralmente levará cerca de 25 minutos. Observe que, caso você use Serviços de Nuvem (função Web/de trabalho), Conjuntos de Dimensionamento de Máquinas Virtuais ou conjuntos de disponibilidade, você terá 30 minutos entre cada grupo de VMs (UD) durante a janela de manutenção agendada.

**P: Qual é a experiência no caso de conjuntos de dimensionamento de máquinas virtuais?**

**R:** A manutenção planejada agora está disponível para conjuntos de dimensionamento de máquinas virtuais. Para obter instruções sobre como iniciar a manutenção de autoatendimento, consulte documento [manutenção planejada para conjuntos de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/virtual-machine-scale-sets-maintenance-notifications.md) .

**P: Qual é a experiência no caso de serviços de nuvem (função Web/de trabalho) e do Service Fabric?**

**R:** embora essas plataformas sejam afetadas pela manutenção planejada, considera-se que os clientes que usam essas plataformas estejam seguros, já que somente as VMs em um UD (domínio de atualização) simples serão afetadas em determinado momento. No momento, a manutenção de autoatendimento não está disponível para os serviços de nuvem (função Web/de trabalho) nem para o Service Fabric.

**P: não vejo nenhuma informação de manutenção em minhas VMs. O que deu errado?**

**R:** existem várias razões para não se ver informações de manutenção em suas VMs:
1.  Você está usando uma assinatura marcada como Microsoft interna.
2.  Suas VMs não estão agendadas para manutenção. É possível que a onda de manutenção tenha sido concluída, cancelada ou modificada de modo que suas VMs não são afetadas por ela.
3. Você tem a VM desalocada e a iniciou. Isso pode fazer com que a VM seja movida para um local que não tenha a onda de manutenção planejada agendada. Portanto, a VM não mostrará mais informações de manutenção. 
4.  Você não tem a coluna **Manutenção** adicionada ao modo de exibição de lista da VM. Embora tenhamos adicionado essa coluna à exibição padrão, os clientes que configuraram para ver colunas não padrão devem adicionar manualmente a coluna **Manutenção** ao modo de exibição de lista da VM.

**P: minha VM está agendada para manutenção pela segunda vez. Por?**

**R:** há diversos casos de uso em que você verá sua VM agendada para manutenção depois de ter concluído a reimplantação da manutenção:
1.  Nós cancelamos a fase de manutenção e a reiniciamos com uma carga diferente. É possível que tenhamos detectado uma carga com falha e seja necessário simplesmente implantar uma carga adicional.
2.  Sua máquina virtual teve o *serviço autorrestabelecido* para outro nó devido a uma falha de hardware.
3.  Você optou por parar (desalocar) e reiniciar a VM.
4.  O **desligamento automático** está ativado para a VM.



## <a name="next-steps"></a>Próximas etapas

Você pode lidar com a manutenção planejada usando o [CLI do Azure](maintenance-notifications-cli.md), [Azure PowerShell](maintenance-notifications-powershell.md) ou [portal](maintenance-notifications-portal.md).
