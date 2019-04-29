---
title: Arquitetura de recuperação de desastre do Hyper-V para o Azure no Azure Site Recovery | Microsoft Docs
description: Este artigo apresenta uma visão geral dos componentes e da arquitetura usados ao implantar recuperação de desastre para VMs Hyper-V locais (sem VMM) para o Azure com o serviço Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: raynew
ms.openlocfilehash: f77069592fb34caf409b387f5c8452159f55e296
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60553231"
---
# <a name="hyper-v-to-azure-disaster-recovery-architecture"></a>Arquitetura de recuperação de desastre do Hyper-V para o Azure


Este artigo descreve a arquitetura e os processos usados quando você faz a replicação, o failover e a recuperação de VMs (máquinas virtuais) do Hyper-V entre hosts Hyper-V locais e o Azure usando o serviço [Azure Site Recovery](site-recovery-overview.md).

Os hosts Hyper-V podem ser opcionalmente gerenciados em nuvens privadas do System Center VMM (Virtual Machine Manager).



## <a name="architectural-components---hyper-v-without-vmm"></a>Componentes de arquitetura – Hyper-V sem VMM

A tabela e o gráfico a seguir fornecem uma visão geral dos componentes usados para replicação do Hyper-V para o Azure quando hosts Hyper-V não são gerenciados por VMM.

**Componente** | **Requisito** | **Detalhes**
--- | --- | ---
**As tabelas** | Uma assinatura do Azure, uma conta de armazenamento do Azure e uma rede do Azure. | Os dados replicados de cargas de trabalho de VMs locais são colocados na conta de armazenamento. As VMs do Azure são criadas com os dados de carga de trabalho replicados quando ocorre failover do site local.<br/><br/> As VMs do Azure se conectam à rede virtual do Azure quando são criadas.
**Hyper-V** | Durante a implantação do Site Recovery, você reúne hosts Hyper-V e clusters em sites do Hyper-V. Você instala o Provedor do Azure Site Recovery e o agente dos Serviços de Recuperação em cada host autônomo do Hyper-V em cada nó de cluster do Hyper-V. | O Provedor coordena a replicação com o Site Recovery pela internet. O agente dos Serviços de Recuperação trata da replicação de dados.<br/><br/> As comunicações do provedor e do agente são protegidas e criptografadas. Os dados replicados no armazenamento do Azure também são criptografados.
**VMs Hyper-V** | Uma ou mais VMs em execução no Hyper-V. | Nada precisa ser explicitamente instalado em VMs.


**Arquitetura do Hyper-V para o Azure (sem VMM)**

![Arquitetura](./media/hyper-v-azure-architecture/arch-onprem-azure-hypervsite.png)



## <a name="architectural-components---hyper-v-with-vmm"></a>Componentes de arquitetura – Hyper-V com VMM

A tabela e o gráfico a seguir fornecem uma visão geral dos componentes usados para replicação do Hyper-V para o Azure quando hosts Hyper-V são gerenciados em nuvens VMM.

**Componente** | **Requisito** | **Detalhes**
--- | --- | ---
**As tabelas** | Uma assinatura do Azure, uma conta de armazenamento do Azure e uma rede do Azure. | Os dados replicados de cargas de trabalho de VMs locais são colocados na conta de armazenamento. As VMs do Azure são criadas com os dados replicados quando ocorre failover do site local.<br/><br/> As VMs do Azure se conectam à rede virtual do Azure quando são criadas.
**Servidor VMM** | O servidor VMM tem uma ou mais nuvens que contém hosts Hyper-V. | Você instala o Provedor do Site Recovery no servidor VMM para orquestrar a replicação com o Site Recovery e registre o servidor no cofre dos Serviços de Recuperação.
**Host Hyper-V** | Um ou mais hosts/clusters Hyper-V gerenciados pelo VMM. |  Você pode instalar o agente dos Serviços de Recuperação em cada host ou nó do cluster do Hyper-V.
**VMs Hyper-V** | Uma ou mais VMs em execução em um servidor host do Hyper-V. | Nada precisa ser explicitamente instalado em VMs.
**Rede** | Redes lógicas e de VM configuradas no servidor VMM. A rede de VMs deve ser vinculada a uma rede lógica associada à nuvem. | Redes de VMs são mapeadas para redes virtuais do Azure. Quando as VMs do Azure são criadas após o failover, elas são adicionadas à rede do Azure que é mapeada para a rede de VMs.

**Arquitetura do Hyper-V para o Azure (com VMM)**

![Componentes](./media/hyper-v-azure-architecture/arch-onprem-onprem-azure-vmm.png)



## <a name="replication-process"></a>Processo de replicação

![Replicação do Hyper-V para o Azure](./media/hyper-v-azure-architecture/arch-hyperv-azure-workflow.png)

**Processo de replicação e recuperação**


### <a name="enable-protection"></a>Habilitar proteção

1. Depois de habilitar a proteção para uma máquina virtual do Hyper-V, no portal do Azure ou no local, a opção **Habilitar proteção** é iniciada.
2. O trabalho verifica se o computador está em conformidade com os pré-requisitos antes de invocar [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) para configurar a replicação com as configurações definidas por você.
3. O trabalho é iniciado com a replicação inicial, invocando o método [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx), para inicializar uma replicação completa de VM e enviar os discos virtuais da VM no Azure.
4. Você pode monitorar o trabalho na guia **Trabalhos** .      ![Lista de trabalhos](media/hyper-v-azure-architecture/image1.png) ![Habilitar busca detalhada da proteção](media/hyper-v-azure-architecture/image2.png)


### <a name="initial-data-replication"></a>Replicação de dados inicial

1. Um [Instantâneo da VM do Hyper-V](https://technet.microsoft.com/library/dd560637.aspx) é tirado quando a replicação inicial é disparada.
2. Discos rígidos virtuais na VM são replicados individualmente até serem todos copiados para o Azure. Isso pode demorar um pouco, dependendo do tamanho da VM e largura de banda de rede. [Saiba como](https://support.microsoft.com/kb/3056159) aumentar a largura de banda de rede.
3. Se houver alterações no disco durante a replicação inicial, o Rastreador de Replicação de Réplica do Hyper-V mostrará essas alterações como logs de replicação do Hyper-V (.hrl). Esses arquivos de log estão localizados na mesma pasta que os discos. Cada disco tem um arquivo .hrl associado que é enviado ao armazenamento secundário. O instantâneo e os arquivos de log consomem recursos de disco durante a replicação inicial.
4. Quando a replicação inicial termina, o instantâneo de VM é excluído.
5. As alterações de disco delta no log são sincronizadas e mescladas para o disco pai.


### <a name="finalize-protection-process"></a>Finalizar processo de proteção

1. Após a conclusão da replicação inicial, o trabalho **Finalizar proteção na máquina virtual** é executado. Ele configura a rede e outras configurações pós-replicação, de modo que a VM fica protegida.
2. Neste estágio, você pode verificar as configurações da VM para certificar-se de que ela está pronta para failover. Você pode executar uma simulação de recuperação de desastre (failover de teste) para a VM, para verificar se ela faz failover conforme esperado. 


## <a name="delta-replication"></a>Replicação delta

1. Após a replicação inicial, a replicação delta se inicia, de acordo com a política de replicação.
2. O Rastreador de Replicação de Réplica do Hyper-V acompanha as alterações em um disco rígido virtual como arquivos .hrl. Cada disco configurado para a replicação tem um arquivo .hrl associado.
3. Esse log é enviado para a conta de armazenamento do cliente. Quando um log está em trânsito para o Azure, as alterações no disco primário são rastreadas em outro arquivo de log na mesma pasta.
4. Durante a replicação inicial e delta, você pode monitorar a VM no Portal do Azure.

### <a name="resynchronization-process"></a>Processo de ressincronização

1. Se a replicação delta falhar, e uma replicação completa for dispendiosa em termos de largura de banda ou tempo, a VM fica marcada para ressincronização.
    - Por exemplo, se os arquivos .hrl alcançarem 50% do tamanho do disco, a VM será marcada para ressincronização.
    -  Por padrão, a ressincronização está agendada para execução automática fora das horas de trabalho.
1.  A ressincronização envia somente dados delta.
    - Ela minimiza a quantidade de dados enviados calculando somas de verificação das VMs de origem e de destino.
    - Ela usa um algoritmo de agrupamento de bloco fixo em que os arquivos de origem e destino são divididos em partes fixas.
    - As somas de verificação para cada parte são geradas. Elas são comparadas para determinar quais blocos da origem precisam ser aplicados ao destino.
2. Após a conclusão da ressincronização, a replicação delta normal deverá ser retomada.
3. Se você não quiser aguardar a ressincronização fora do horário de trabalho, você poderá ressincronizar uma VM manualmente. Por exemplo, se ocorrer uma interrupção. Para fazer isso, no Portal do Azure, selecione a VM > **Ressincronizar**.

    ![Ressincronização manual](./media/hyper-v-azure-architecture/image4-site.png)


### <a name="retry-process"></a>Processo de tentar novamente

Se um erro de replicação ocorrer, haverá uma repetição interna. Tentar novamente é classificado conforme descrito na tabela.

**Categoria** | **Detalhes**
--- | ---
**Erros não recuperáveis** | Nenhuma nova tentativa é feita. O status da VM será **Crítico** e há necessidade de intervenção do administrador.<br/><br/> Exemplos desses erros incluem uma cadeia VHD quebrada, um estado inválido para a VM de réplica, erros de autenticação de rede, erros de autorização e erros de VM não encontrada (para servidores Hyper-V autônomos).
**Erros recuperáveis** | Novas tentativas ocorrem a cada intervalo da replicação usando de modo exponencial a retirada, o que aumenta o intervalo de repetição desde o início da primeira tentativa por 1, 2, 4, 8 e 10 minutos. Se o erro persistir, repita a operação a cada 30 minutos. Exemplos desses erros incluem: erros da rede, erros de pouco espaço em disco, condições de memória insuficiente.



## <a name="failover-and-failback-process"></a>Processo de failover e failback

1. Você pode executar um failover planejado ou não planejado de VMs Hyper-V locais para o Azure. Se você executar um failover planejado, as VMs de origem serão desligadas para evitar a perda de dados. Execute um failover não planejado se o seu site primário não está acessível.
2. Você pode fazer o failover de um único computador ou criar planos de recuperação para orquestrar o failover de várias máquinas virtuais.
3. Você executa um failover. Após a conclusão do primeiro estágio do failover, você deverá ser capaz de ver as VMs de réplica criadas no Azure. Você pode atribuir um endereço IP público à VM, se necessário.
4. Você confirma então o failover para começar a acessar a carga de trabalho por meio da VM do Azure de réplica.

Depois que sua infraestrutura local estiver funcionando novamente, você poderá fazer failback. O failback ocorre em três estágios:

1. Dispare um failover planejado do Azure para o site local:
    - **Minimizar o tempo de inatividade**: Se você usar essa opção, o Site Recovery sincronizará os dados antes do failover. Ele verifica blocos de dados alterados e baixa-os para o site local, enquanto a VM do Azure continua em execução, minimizando o tempo de inatividade. Quando você especificar manualmente que o failover deve ser concluído, a VM do Azure será desligada, quaisquer eventuais alterações delta finais serão copiadas e o failover iniciará.
    - **Download completo**: Com essa opção, os dados são sincronizados durante o failover. Esta opção baixa todo o disco. Ela é mais rápida porque nenhuma soma de verificação é calculada, mas há mais tempo de inatividade. Use essa opção se você esteve executando as VMs de réplica do Azure por algum tempo ou se a VM local foi excluída.
    - **Criar VM**: Você pode selecionar para fazer failback para a mesma VM ou para uma outra VM. Você pode especificar que o Site Recovery deve criar a VM se ela ainda não existe.

2. Após a conclusão da sincronização inicial, você seleciona para concluir o failover. Depois de concluí-lo, você pode fazer logon na VM local para verificar se tudo está funcionando conforme o esperado. No Portal do Azure, você pode ver que as VMs do Azure foram interrompidas.
3.  Em seguida, você confirma o failover para concluir e começar a acessar a carga de trabalho da VM local novamente.
4. Após o failback das cargas de trabalho, você habilita a replicação inversa, de modo que as VMs locais são replicadas novamente para o Azure.



## <a name="next-steps"></a>Próximas etapas


Siga [este tutorial](tutorial-prepare-azure.md) para conhecer a replicação do Hyper-V para o Azure.


