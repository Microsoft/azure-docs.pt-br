---
title: Atualizações do Azure Site Recovery | Microsoft Docs
description: Fornece uma visão geral das atualizações de serviço e de como atualizar os componentes usados no Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/05/2019
ms.author: rajanaki
ms.openlocfilehash: e27dee213baf8365c3ad4efc69602f66e2081abe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61035950"
---
# <a name="service-updates-in-azure-site-recovery"></a>Atualizações de serviço no Azure Site Recovery
Como uma organização, você precisa descobrir como pretende manter seus dados seguros e os aplicativos/as cargas de trabalho em execução durante interrupções planejadas e não planejadas. O Azure Site Recovery contribui para sua estratégia de BCDR mantendo seus aplicativos em execução nas VMs e nos servidores físicos disponíveis caso um site fique inativo. O Site Recovery replica as cargas de trabalho em execução em VMs e servidores físicos para que eles permaneçam disponíveis em um local secundário se o site primário não estiver disponível. Ele recupera as cargas de trabalho para o site primário quando ele está ativo e em execução novamente.

O Site Recovery pode gerenciar a replicação para:

- [VMs do Azure replicadas entre regiões do Azure](azure-to-azure-tutorial-dr-drill.md).
- As máquinas virtuais e servidores físicos locais que replicam no Azure ou em um site secundário.
Para saber mais, consulte a documentação [aqui](https://docs.microsoft.com/azure/site-recovery).

O Azure Site Recovery publica atualizações de serviço regularmente – incluindo a adição de novos recursos, melhorias na matriz de suporte e correções de bug, se houver. Para ficar em dia, aproveite todos os recursos, todas as melhorias e todas as correções de bug mais recentes se houver. Os usuários são aconselhados a sempre atualizar para as últimas versões dos componentes do Azure Site Recovery. 
 
## <a name="support-statement-for-azure-site-recovery"></a>Declaração de suporte do Azure Site Recovery 

> [!IMPORTANT]
> **A cada nova versão 'N' liberada de um componente do Azure Site Recovery, todas as versões abaixo de 'N-4' são consideradas sem suporte**. Portanto, é sempre aconselhável atualizar para as últimas versões disponíveis.

> [!IMPORTANT]
> O suporte oficial para atualizações abrange a versão > N-4 à N (N sendo a última versão). Se você estiver usando a N-6, precisará primeiro atualizar para a N-4 e, em seguida, atualizar para a N.

### <a name="upgrading-when-the-difference-between-current-version-and-latest-released-version-is-greater-than-4"></a>Atualização quando a diferença entre a versão atual e a última versão liberada é maior que 4

1. Como primeira etapa, atualize o componente atualmente instalado, digamos, da versão N para a N+4 e, em seguida, migre para a próxima versão compatível. Digamos que a versão atual seja 9.24 e você esteja usando a 9.16. Primeiro atualize para 9.20 e, em seguida, para 9.24.
2. Siga o mesmo processo para todos os componentes, dependendo do cenário.

### <a name="support-for-latest-oskernel-versions"></a>Suporte para as últimas versões do kernel/do sistema operacional

> [!NOTE]
> Caso você tenha uma janela de manutenção agendada e uma reinicialização faça parte dela, recomendamos que você primeiro atualize os componentes do Site Recovery e continue com o restante das atividades agendadas.

1. Antes de atualizar suas versões do Kernel/do sistema operacional, verifique se a versão de destino é compatível com o Azure Site Recovery. Encontre as informações em nossa documentação para VMs do Azure, [VMs do VMware](vmware-physical-azure-support-matrix.md) e VMs do Hyper-V em
2. Veja nossas [Atualizações de serviço](https://azure.microsoft.com/updates/?product=site-recovery) para descobrir qual versão dos componentes do Site Recovery dá suporte à versão específica para a qual você deseja atualizar.
3. Primeiro, atualize para a última versão do Site Recovery.
4. Agora, atualize o Kernel/o sistema operacional para as versões desejadas.
5. Execute uma reinicialização.
6. Isso garantirá que a versão do Kernel/do sistema operacional nos computadores seja atualizada para a última versão e que as últimas alterações do Site Recovery, necessárias para dar suporte à nova versão, também sejam carregadas no computador de origem.



## <a name="azure-vm-disaster-recovery-to-azure"></a>Recuperação de desastre da VM do Azure para o Azure
Nesse cenário, é altamente recomendável que você [habilite](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-autoupdate) as atualizações automáticas. Você pode optar por permitir que o Site Recovery gerencie as atualizações das seguintes maneiras:

- Como parte da etapa de habilitação da replicação
- Ativar/desativar as configurações de atualização de dentro do cofre

Caso tenha escolhido gerenciar as atualizações manualmente, siga estas etapas:

1. Acesse o portal do Azure e, em seguida, navegue para seu "cofre dos Serviços de Recuperação".
2. Acesse o painel "Itens Replicados" no portal do Azure do "cofre dos Serviços de Recuperação".
3. Clique na seguinte notificação na parte superior da tela:
    
    *Nova atualização do agente de replicação do Site Recovery disponível*
    
    *Clique aqui para instalar ->*

4. Selecione as VMs às quais deseja aplicar a atualização e, em seguida, clique em **OK**.

## <a name="between-two-on-premises-vmm-sites"></a>Entre dois sites do VMM locais
1. Baixe o último pacote cumulativo de atualizações para o Provedor do Microsoft Azure Site Recovery.
2. Instale o pacote cumulativo de atualizações primeiro no servidor VMM local que gerencia o site de recuperação.
3. Após a atualização do site de recuperação, instale o pacote cumulativo de atualizações no servidor VMM que gerencia o site primário.

> [!NOTE]
> Se o VMM for um VMM Altamente Disponível (VMM Clusterizado), instale a atualização em todos os nós do cluster nos quais o serviço VMM está instalado.

## <a name="between-an-on-premises-vmm-site-and-azure"></a>Entre um site do VMM local e o Azure
1. Baixe o pacote cumulativo de atualizações para o Provedor do Microsoft Azure Site Recovery.
2. Instale o pacote cumulativo de atualizações no servidor VMM local.
3. Instale o último agente do MARS em todos os hosts do Hyper-V.

> [!NOTE]
> Se o VMM for um VMM Altamente Disponível (VMM Clusterizado), instale a atualização em todos os nós do cluster nos quais o serviço VMM está instalado.

## <a name="between-an-on-premises-hyper-v-site-and-azure"></a>Entre um site do Hyper-V local e o Azure

1. Baixe o pacote cumulativo de atualizações para o Provedor do Microsoft Azure Site Recovery.
2. Instale o provedor em cada nó dos servidores Hyper-V registrados no Azure Site Recovery.

> [!NOTE]
> Se o Hyper-V for um servidor Hyper-V Clusterizado de Host, instale a atualização em todos os nós do cluster

## <a name="between-an-on-premises-vmware-or-physical-site-to-azure"></a>Entre um site físico ou do VMware local para o Azure

Antes de prosseguir com as atualizações, consulte [Instrução de suporte do Site Recovery](#support-statement-for-azure-site-recovery) para entender o caminho de atualização.

1. Com base em sua instrução de suporte e a versão atual fornecida acima, instale a atualização primeiro em seu servidor de gerenciamento local, seguindo as diretrizes fornecidas [aqui](vmware-azure-deploy-configuration-server.md#upgrade-the-configuration-server). Esse é o servidor que tem as funções de servidor de Configuração e servidor de Processo.
2. Caso tenha servidores de processo de expansão, atualize-os em seguida conforme as diretrizes fornecidas [aqui](vmware-azure-manage-process-server.md#upgrade-a-process-server).
3. Em seguida, para atualizar o agente de mobilidade em cada item protegido, vá até o portal do Azure e, em seguida, vá até a página **Itens protegidos** > **Itens replicados**. Selecione uma VM nessa página. Selecione o botão **Atualizar Agente** exibido na parte inferior da página de cada VM. Isso atualizará o Agente do Serviço de Mobilidade em todas as VMs protegidas.

### <a name="reboot-of-source-machine-after-mobility-agent-upgrade"></a>Reinicialização do computador de origem após a atualização do agente de mobilidade

Uma reinicialização é recomendada após cada atualização do agente de Mobilidade para garantir que todas as últimas alterações sejam carregadas no computador de origem. No entanto, ela **não é obrigatória**. Se a diferença entre a versão do agente durante a última reinicialização e a versão atual for maior que 4, uma reinicialização será obrigatória. Consulte a tabela a seguir para obter uma explicação detalhada.

|**Versão do agente durante a última reinicialização** | **Atualizando para** | **A reinicialização é obrigatória?**|
|---------|---------|---------|
|9.16 |  9.18 | Não obrigatório|
|9.16 | 9.19 | Não obrigatório|
| 9.16 | 9.20 | Não obrigatório
 | 9.16 | 9.21 | Sim, primeiro atualize para 9.20 e, em seguida, reinicialize o computador antes de atualizar para 9.21, pois a diferença entre as versões (9.16, em que a última reinicialização foi executada e a versão de destino é 9.21) é > 4.

## <a name="links-to-currently-supported-update-rollups"></a>Links para pacotes cumulativos de atualizações atualmente compatíveis

|Pacote cumulativo de atualizações  |Provedor  |Instalação unificada| OVF  |MARS|
|---------|---------|---------|---------|--------|
|[Pacote cumulativo de atualizações 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery)     |   5.1.4000.0  |  9.23.5163.1   |  5.1.4000.0  | 2.0.9156.0
|[Pacote cumulativo de atualizações 34](https://support.microsoft.com/en-us/help/4490016/update-rollup-34-for-azure-site-recovery) - Hot fix     |   5.1.3950.0  |  9.22.5142.1   |  5.1.3950.0  | 2.0.9155.0
|[Pacote cumulativo de atualizações 33](https://support.microsoft.com/en-us/help/4489582/update-rollup-33-for-azure-site-recovery)     |   5.1.3900.0  |  9.22.5109.1   |  5.1.3900.0  | 2.0.9155.0
|[Pacote cumulativo de atualizações 32](https://support.microsoft.com/en-us/help/4485985/update-rollup-32-for-azure-site-recovery)     |   5.1.3800.0  |  9.21.5091.1   |  5.1.3800.0  |2.0.9144.0
|[Pacote cumulativo de atualizações 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)     |     5.1.3700.0      |   9.20.5051.1      |     5.1.3700.0    |2.0.9144.0
|[Pacote cumulativo de atualizações 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30)     |    5.1.3650.0   |   9.19.5007.1    |     5.1.3650.0    |2.0.9139.0

## <a name="previous-update-rollups"></a>Pacotes cumulativos de atualizações anteriores

- [Pacote cumulativo de atualizações 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery)
- [Pacote cumulativo de atualizações 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery)
- [Pacote cumulativo de atualizações 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery)
- [Pacote cumulativo de atualizações 26](https://support.microsoft.com/help/4344054/update-rollup-26-for-azure-site-recovery)  
- [Pacote cumulativo de atualizações 25](https://support.microsoft.com/help/4278275/update-rollup-25-for-azure-site-recovery) 
- [Pacote cumulativo de atualizações 23](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) 
- [Pacote cumulativo de atualizações 22](https://support.microsoft.com/help/4072852/update-rollup-22-for-azure-site-recovery) 
- [Pacote cumulativo de atualizações 21](https://support.microsoft.com/help/4051380/update-rollup-21-for-azure-site-recovery) 
- [Pacote cumulativo de atualizações 20](https://support.microsoft.com/help/4041105/update-rollup-20-for-azure-site-recovery) 
- [Pacote cumulativo de atualizações 19](https://support.microsoft.com/help/4034599/update-rollup-19-for-azure-site-recovery) 
