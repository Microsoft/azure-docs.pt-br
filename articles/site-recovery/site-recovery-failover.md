---
title: Failover durante a recuperação de desastre com o Azure Site Recovery | Microsoft Docs
description: Aprenda sobre o failover de VMs e servidores físicos durante a recuperação de desastre com o serviço Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 1/18/2019
ms.author: mayg
ms.openlocfilehash: 8f76d4e54133e4e899e707e666703a67310e8702
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61280447"
---
# <a name="fail-over-vms-and-physical-servers"></a>Fazer failover de VMs e servidores físicos 

Este artigo descreve como executar o failover de máquinas virtuais e servidores físicos protegidos pelo Site Recovery.

## <a name="prerequisites"></a>Pré-requisitos
1. Antes de realizar um failover, faça um [failover de teste](site-recovery-test-failover-to-azure.md) para verificar se tudo está funcionando conforme o esperado.
1. [Prepare a rede](site-recovery-network-design.md) no local de destino antes de realizar um failover.  

Use a tabela a seguir para saber sobre as opções de failover fornecidas pelo Azure Site Recovery. Essas opções também são listadas para diferentes cenários de failover.

| Cenário | Requisito de recuperação de aplicativo | Fluxo de trabalho para Hyper-V | Fluxo de trabalho para VMware
|---|--|--|--|
|Failover planejado devido a um futuro tempo de inatividade do datacenter| Perda de dados zero para o aplicativo quando uma atividade planejada é executada| Para o Hyper-V, o ASR replica os dados em uma frequência de cópia especificada pelo usuário. Failover planejado é usado para substituir a frequência e replicar as alterações finais antes de um failover ser iniciado. <br/> <br/> 1.    Planeje uma janela de manutenção de acordo com o processo de gerenciamento de alterações da sua empresa. <br/><br/> 2.Notifique os usuários sobre o tempo de inatividade que está por vir. <br/><br/> 3. Coloque offline o aplicativo voltado ao usuário.<br/><br/>4.Inicie o failover planejado usando o portal de ASR. A máquina virtual local é automaticamente desligada.<br/><br/>Perda de dados efetiva do aplicativo = 0 <br/><br/>Um diário de pontos de recuperação também é fornecido em uma janela de retenção para um usuário que deseje usar um ponto de recuperação mais antigo. (retenção de 24 horas para o Hyper-V). Se a replicação tiver sido interrompida além do período da janela de retenção, os clientes ainda poderão conseguir fazer failover usando os últimos pontos de recuperação disponíveis. | Para o VMware, o ASR replica dados continuamente usando CDP. O failover fornece ao usuário a opção de failover para os dados mais recentes (incluindo após o desligamento do aplicativo)<br/><br/> 1. Planeje uma janela de manutenção de acordo com o processo de gerenciamento de alterações <br/><br/>2.Notifique os usuários sobre o tempo de inatividade que está por vir <br/><br/>3.    Coloque offline o aplicativo voltado ao usuário. <br/><br/>4.  Inicie um Failover Planejado, usando o portal do ASR para o último momento depois o aplicativo ficar offline. Use a opção "Failover não planejado" no portal e selecione o ponto mais recente para realizar failover. A máquina virtual local é automaticamente desligada.<br/><br/>Perda de dados efetiva do aplicativo = 0 <br/><br/>Um diário de pontos de recuperação em uma janela de retenção é fornecido para um cliente que deseje usar um ponto de recuperação mais antigo. (72 horas de retenção para o VMware). Se a replicação tiver sido interrompida além do período da janela de retenção, os clientes ainda poderão conseguir fazer failover usando os últimos pontos de recuperação disponíveis.
|Failover devido a um tempo de inatividade não planejado do datacenter (desastre natural ou de TI) | Perda mínima de dados para o aplicativo | 1.Inicie o plano BCP da organização <br/><br/>2. Inicie o failover não planejado usando o portal do ASR para a versão mais recente ou um ponto na janela de retenção (diário).| 1. Inicie o plano BCP da organização. <br/><br/>2.  Inicie o failover não planejado usando o portal do ASR para a versão mais recente ou um ponto na janela de retenção (diário).


## <a name="run-a-failover"></a>Executar um failover
Este procedimento descreve como executar um failover para um [plano de recuperação](site-recovery-create-recovery-plans.md). Como alternativa, você pode executar o failover de uma única máquina virtual ou servidor físico na página **Itens replicados**, conforme descrito [aqui](vmware-azure-tutorial-failover-failback.md#run-a-failover-to-azure).


![Failover](./media/site-recovery-failover/Failover.png)

1. Selecione **Planos de Recuperação** > *recoveryplan_name*. Clique em **Failover**.
2. Na tela de **Failover**, selecione um **Ponto de Recuperação** para o qual fazer o failover. Você pode usar uma das seguintes opções:
   1. **Mais recente**: Essa opção inicia o trabalho primeiro processando todos os dados que foram enviados ao serviço Site Recovery. O processamento dos dados cria um ponto de recuperação para cada máquina virtual. Esse ponto de recuperação é usado pela máquina virtual durante o failover. Essa opção oferece o menor RPO (Objetivo de Ponto de Recuperação), pois a máquina virtual criada após o failover terá todos os dados que tiverem sido replicados para o serviço de Site Recovery quando o failover for disparado.
   1. **Mais recente processado**: Essa opção faz failover em todas as máquinas virtuais do plano de recuperação para o último ponto de recuperação que já foi processado pelo serviço de Site Recovery. Quando você estiver realizando o failover de teste de uma máquina virtual, o carimbo de data/hora do último ponto de recuperação processado também é mostrado. Se estiver fazendo o failover de um plano de recuperação, você pode ir para a máquina virtual individual e visualizar o bloco **Últimos Pontos de Recuperação** para obter essas informações. Como nenhum tempo é gasto para processar os dados não processados, esta opção fornece uma opção de failover de baixo RTO (objetivo de tempo de recuperação).
   1. **Consistente com o aplicativo mais recente**: Essa opção realiza failover em todas as máquinas virtuais do plano de recuperação para o último ponto de recuperação consistente de aplicativo que já foi processado pelo serviço do Site Recovery. Quando você estiver realizando failover de teste de uma máquina virtual, o carimbo de data/hora do ponto mais recente recuperação consistentes com o aplicativo também é mostrado. Se estiver fazendo o failover de um plano de recuperação, você pode ir para a máquina virtual individual e visualizar o bloco **Últimos Pontos de Recuperação** para obter essas informações.
   1. **Várias VMs processadas mais recentemente**: Essa opção só está disponível para os planos de recuperação que têm pelo menos uma máquina virtual com consistência de várias VMs ativada. As máquinas virtuais que fazem parte de um failover de grupo de replicação para o ponto de recuperação consistente de várias VMs comuns mais recentes. Outras máquinas virtuais fazem failover para seus últimos pontos de recuperação processados.  
   1. **Multi-VMs mais recentes consistentes com o aplicativo**: Essa opção só está disponível para os planos de recuperação que têm pelo menos uma máquina virtual com consistência de várias VMs ativada. Máquinas virtuais que fazem parte de um failover do grupo de replicação para o ponto de recuperação comum mais recente de várias VMs consistente com aplicativo. Outras máquinas virtuais fazem failover para seus últimos pontos de recuperação consistentes com aplicativo.
   1. **Personalizado**: Se você estiver realizando teste de failover de uma máquina virtual, você pode usar essa opção de failover para um determinado ponto de recuperação.

      > [!NOTE]
      > A opção de escolher um ponto de recuperação só está disponível ao fazer failover no Azure.
      >
      >


1. Se algumas das máquinas virtuais no plano de recuperação sofrerem failover em uma execução anterior e as máquinas virtuais estiverem ativas nos locais de origem e destino, você pode usar a opção **Alterar direção** para escolher a direção na qual o failover deve ocorrer.
1. Se estiver fazendo failover no Azure e a criptografia de dados estiver habilitada para a nuvem (só se aplica quando você tiver máquinas virtuais Hyper-v protegidas em um Servidor VMM), em **Chave de Criptografia**, selecione o certificado que foi emitido quando você habilitou a criptografia de dados durante a configuração no servidor VMM.
1. Selecione **Desligue o computador antes do início do failover** se quiser que o Site Recovery tente realizar um desligamento das máquinas virtuais de origem antes de disparar o failover. O failover continuará mesmo o desligamento falhar.  

    > [!NOTE]
    > Se máquinas virtuais Hyper-V estão protegidas, a opção de desligar também tenta sincronizar os dados locais que ainda não foram enviados para o serviço antes de disparar o failover.
    >
    >

1. Você pode acompanhar o progresso do failover na página **Trabalhos** . Mesmo que ocorram erros durante um failover não planejado, o plano de recuperação será executado até que seja concluído.
1. Após o failover, valide a máquina virtual, efetuando logon nela. Se desejar alternar outro ponto de recuperação da máquina virtual, você poderá usar a opção **Alterar ponto de recuperação**.
1. Quando estiver satisfeito com a máquina virtual que passou por failover, você pode **Confirmar** o failover. **A confirmação exclui todos os pontos de recuperação disponíveis no serviço** e a opção **Alterar ponto de recuperação** não está mais disponível.

## <a name="planned-failover"></a>Failover planejado
Máquinas virtuais/servidores físicos protegidos usando o Site Recovery também dão suporte ao **Failover planejado**. O failover planejado é uma opção de failover sem nenhuma perda de dados. Quando um failover planejado é acionado, primeiro as máquinas virtuais de origem são desligadas, depois os dados mais recentes são sincronizados e, em seguida, um failover é acionado.

> [!NOTE]
> Durante o failover de máquinas virtuais Hyper-v de um site local para outro site local, antes de voltar ao site local principal, você precisa fazer a **replicação inversa** da máquina virtual para o site principal e, depois, disparar um failover. Se a máquina virtual principal não estiver disponível, você vai precisar restaurar a máquina virtual de um backup antes de iniciar a **replicação inversa**.   
> 
> 
> ## <a name="failover-job"></a>Trabalho de failover

![Failover](./media/site-recovery-failover/FailoverJob.png)

Quando é disparado, um failover envolve as seguintes etapas:

1. Verificação de pré-requisitos: Essa etapa garante que todas as condições necessárias para o failover sejam atendidas
1. Failover: Essa etapa processa os dados, deixando-os prontos para que uma máquina virtual do Azure seja criada a partir deles. Se você tiver escolhido o ponto de recuperação **Mais recente**, essa etapa cria um ponto de recuperação dos dados que foram enviados para o serviço.
1. Iniciar: Essa etapa cria uma máquina virtual do Azure usando os dados processados na etapa anterior.

> [!WARNING]
> **Não cancele um failover em andamento**: Antes de iniciar o failover, a replicação para a máquina virtual é interrompida. Se você **Cancelar** um trabalho em andamento, o failover é interrompido, mas a máquina virtual não começa a replicação. A replicação não pode ser iniciada novamente.
>
>

## <a name="time-taken-for-failover-to-azure"></a>Tempo necessário para failover do Azure

Em certos casos, o failover de máquinas virtuais requer uma etapa intermediária extra que geralmente leva cerca de oito a 10 minutos para ser concluída. Nos casos a seguir, o tempo necessário para o failover será maior que o normal:

* Máquinas virtuais VMware usando o serviço de mobilidade da versão anterior a 9.8
* Servidores físicos
* Máquinas virtuais VMware Linux
* Máquinas virtuais Hyper-V protegidas como servidores físicos
* Máquinas virtuais VMware em que os drivers a seguir não estão presentes como drivers de inicialização
    * storvsc
    * vmbus
    * storflt
    * intelide
    * atapi
* Máquinas virtuais VMware que não têm o serviço DHCP habilitado, independentemente de estarem usando endereços DHCP ou IP estáticos

Em todos outros casos, essa etapa intermediária não é necessária, e o tempo necessário para o failover é menor.

## <a name="using-scripts-in-failover"></a>Utilizar scripts no Failover
Você talvez queira automatizar determinadas ações durante um failover. É possível usar scripts ou [runbooks de Automação do Azure](site-recovery-runbook-automation.md) nos [planos de recuperação](site-recovery-create-recovery-plans.md) para fazer isso.

## <a name="post-failover-considerations"></a>Considerações de pós-failover
Após o failover, considere as recomendações a seguir:
### <a name="retaining-drive-letter-after-failover"></a>Retenção da letra da unidade após failover
Para manter a letra da unidade em máquinas virtuais após failover, você pode definir a **Política SAN** para a máquina virtual como **OnlineAll**. [Leia mais](https://support.microsoft.com/help/3031135/how-to-preserve-the-drive-letter-for-protected-virtual-machines-that-are-failed-over-or-migrated-to-azure).

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar para conectar VMs do Azure após o failover

Se você quiser se conectar às VMs do Azure usando RDP/SSH após o failover, siga os requisitos resumidos na tabela mostrada [aqui](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

Siga as etapas descritas [aqui](site-recovery-failover-to-azure-troubleshoot.md) para solucionar quaisquer problemas de conectividade após o failover.


## <a name="next-steps"></a>Próximas etapas

> [!WARNING]
> Após o failover das máquinas virtuais, quando o datacenter local estiver disponível, você deverá [**Proteger novamente**](vmware-azure-reprotect.md) as máquinas virtuais VMware no datacenter local.

Use a opção [ **Failover planejado** ](hyper-v-azure-failback.md) para fazer o **Failback** das máquinas virtuais Hyper-v para o local com o Azure.

Se fizer o failover de uma máquina virtual Hyper-v para outro datacenter local gerenciado por um servidor VMM e o datacenter principal estiver disponível, use a opção **Replicação inversa** para iniciar a replicação para o datacenter principal.
