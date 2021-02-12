---
title: Habilitar VMs VMware para recuperação de desastre usando Azure Site Recovery
description: Este artigo descreve como habilitar a replicação de VM do VMware para recuperação de desastre usando o serviço de Azure Site Recovery
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 12/07/2020
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 19a98b5786f35839d84e1e969c29e45e2b5e8dea
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98573387"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>Habilitar a replicação no Azure de VMs VMware

Este artigo descreve como habilitar a replicação de VMs (máquinas virtuais) VMware locais para o Azure.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que seu sistema atenda aos seguintes critérios:

- [Configure seu ambiente de origem local](vmware-azure-set-up-source.md).
- [Configure seu ambiente de destino no Azure](vmware-azure-set-up-target.md).
- [Verifique os requisitos e os pré-requisitos](vmware-physical-azure-support-matrix.md) antes de começar. As coisas importantes a serem observadas incluem:
  - [Sistemas operacionais com suporte](vmware-physical-azure-support-matrix.md#replicated-machines) para computadores replicados.
  - Suporte a [armazenamento/disco](vmware-physical-azure-support-matrix.md#storage) .
  - [Requisitos do Azure](vmware-physical-azure-support-matrix.md#azure-vm-requirements) com os quais os computadores locais devem estar em conformidade.

### <a name="resolve-common-issues"></a>Resolver problemas comuns

- Cada disco deve ter menos de 4 TB.
- O disco do sistema operacional deve ser um disco básico, não um disco dinâmico.
- Para máquinas virtuais habilitadas para UEFI de geração 2, a família de sistemas operacionais deve ser Windows e o disco de inicialização deve ser menor que 300 GB.

## <a name="before-you-start"></a>Antes de começar

Ao replicar máquinas virtuais VMware, tenha em mente estas informações:

- Sua conta de usuário do Azure precisa ter certas [permissões](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) para habilitar a replicação de uma nova máquina virtual para o Azure.
- VMs VMware são descobertas a cada 15 minutos. Pode levar 15 minutos ou mais para que as VMs apareçam no portal do Azure após a descoberta. Quando você adiciona um novo servidor vCenter ou host vSphere, a descoberta pode levar 15 minutos ou mais.
- Pode levar 15 minutos ou mais para que as alterações de ambiente na máquina virtual sejam atualizadas no Portal. Por exemplo, a instalação das ferramentas do VMware.
- Você pode verificar a hora da última descoberta para VMs VMware: consulte o campo **último contato** em na página **servidores de configuração** para o servidor vCenter/host vSphere.
- Para adicionar máquinas virtuais para replicação sem esperar pela descoberta agendada, realce o servidor de configuração (mas não clique nele) e selecione **Atualizar**.
- Quando você habilita a replicação, se a máquina virtual estiver preparada, o servidor de processo instalará automaticamente o serviço de mobilidade Azure Site Recovery na VM.

## <a name="enable-replication"></a>Habilitar a replicação

Antes de executar as etapas nesta seção, examine as seguintes informações:

- Agora Azure Site Recovery Replica diretamente para o Managed disks para todas as novas replicações. O servidor de processo grava os logs de replicação em uma conta de armazenamento de cache na região de destino. Esses logs são usados para criar pontos de recuperação em discos gerenciados de réplica com Convenção de nomenclatura de `asrseeddisk` .
- O suporte do PowerShell para replicação para discos gerenciados está disponível a partir do [módulo AZ. recoveryservices versão 2.0.0](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview)
- No momento do failover, o ponto de recuperação selecionado é usado para criar o disco gerenciado por destino.
- As VMs que foram configuradas anteriormente para replicar para as contas de armazenamento de destino não são afetadas.
- A replicação para contas de armazenamento para uma nova máquina virtual só está disponível por meio de uma API REST (Representational State Transfer) e do PowerShell. Use a API REST do Azure versão 2016-08-10 ou 2018-01-10 para replicação em contas de armazenamento.

Para habilitar a replicação, siga estas etapas:

1. Vá para a **etapa 2: replicar** a  >  **origem** do aplicativo. Depois de habilitar a replicação pela primeira vez, selecione **+ replicar** no cofre para habilitar a replicação para máquinas virtuais adicionais.
1. Na página **Origem** > **Origem**, selecione o servidor de configuração.
1. Para **tipo de computador**, selecione **máquinas virtuais** ou **máquinas físicas**.
1. Em **Hipervisor do vCenter/vSphere**, selecione o servidor vCenter que gerencia o host vSphere ou selecione o host. Essa configuração não será relevante se você estiver replicando computadores físicos.
1. Selecione o servidor de processo. Se não houver servidores de processo adicionais criados, o servidor de processo embutido do servidor de configuração estará disponível no menu suspenso. O status de integridade de cada servidor de processo é indicado de acordo com os limites recomendados e outros parâmetros. Escolha um servidor de processo íntegro. Um servidor de processo [crítico](vmware-physical-azure-monitor-process-server.md#process-server-alerts) não pode ser escolhido. Você pode [solucionar problemas e resolver](vmware-physical-azure-troubleshoot-process-server.md) os erros **ou** configurar um [servidor de processo de expansão](vmware-azure-set-up-process-server-scale.md).

   :::image type="content" source="./media/vmware-azure-enable-replication/ps-selection.png" alt-text="Habilitar a janela de origem de replicação":::

   > [!NOTE]
   > A partir da [versão 9,24](site-recovery-whats-new.md), alertas adicionais são introduzidos para aprimorar os alertas de integridade do servidor de processo. Atualize os componentes do Site Recovery para a versão 9,24 ou superior para que todos os alertas sejam gerados.

1. Para **destino**, selecione a assinatura e o grupo de recursos em que você deseja criar as máquinas virtuais com failover. Escolha o modelo de implantação que você deseja usar no Azure para as VMs com failover.
1. Selecione a rede e a sub-rede do Azure às quais as VMs do Azure serão conectadas após o failover. A rede deve estar na mesma região que o cofre do serviço de Site Recovery.

   Selecione **Configurar agora para computadores selecionados** para aplicar a configuração de rede a todas as máquinas virtuais selecionadas para proteção. Selecione **configurar mais tarde** para selecionar a rede do Azure por máquina virtual. Se você não tiver uma rede, precisará criar uma. Para criar uma rede usando Azure Resource Manager, selecione **criar nova**. Selecione uma sub-rede, se aplicável, e selecione **OK**.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-rep3.png" alt-text="Janela habilitar destino de replicação":::

1. Para **máquinas virtuais**  >  ,**selecione máquinas virtuais**, selecione cada máquina virtual que você deseja replicar. Você só pode selecionar máquinas virtuais para as quais a replicação pode ser habilitada. Depois, selecione **OK**. Se você não puder ver ou selecionar nenhuma máquina virtual específica, consulte [o computador de origem não está listado no portal do Azure](vmware-azure-troubleshoot-replication.md#step-3-troubleshoot-source-machines-that-arent-available-for-replication) para resolver o problema.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication5.png" alt-text="Janela habilitar replicação selecionar máquinas virtuais":::

1. Para **Propriedades**  >  **Configurar Propriedades**, selecione a conta que o servidor de processo usa para instalar automaticamente o serviço de mobilidade de site Recovery na VM. Além disso, escolha o tipo de disco gerenciado de destino a ser usado para replicação com base em seus padrões de variação de dados.
1. Por padrão, todos os discos de uma VM de origem são replicados. Para excluir discos da replicação, desmarque a caixa de seleção **incluir** para todos os discos que você não deseja replicar. Depois, selecione **OK**. Você pode definir propriedades adicionais posteriormente. [Saiba mais](vmware-azure-exclude-disk.md) sobre a exclusão de discos.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication6.png" alt-text="Habilitar a janela de propriedades de configuração de replicação":::

1. Em **configurações de replicação**  >  **definir configurações de replicação**, verifique se a política de replicação correta está selecionada. Você pode modificar as configurações da política de replicação em **configurações**  >  **políticas de replicação**  >  _nome da política_  >  **Editar configurações**. As alterações aplicadas a uma política também se aplicam à replicação e novas máquinas virtuais.
1. Se você quiser reunir máquinas virtuais em um grupo de replicação, habilite **a consistência de várias VMs**. Especifique um nome para o grupo e, em seguida, selecione **OK**.

   > [!NOTE]
   > - As máquinas virtuais em um grupo de replicação são replicadas em conjunto e têm pontos de recuperação consistentes com o aplicativo e com falhas compartilhadas quando executam failover.
   > - Colete VMs e servidores físicos para que espelhem suas cargas de trabalho. Habilitar a consistência de várias VMs pode afetar o desempenho da carga de trabalho. Faça isso somente se as máquinas virtuais estiverem executando a mesma carga de trabalho e você precisar de consistência.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication7.png" alt-text="Janela habilitar replicação":::

1. Selecione **Habilitar Replicação**. Você pode acompanhar o progresso do trabalho **habilitar proteção** em **configurações**  >  **trabalhos**  >  **site Recovery trabalhos**. Depois que o trabalho **finalizar proteção** for executado, a máquina virtual estará pronta para failover.

## <a name="monitor-initial-replication"></a>Monitorar a replicação inicial

Após a conclusão da "habilitação da replicação" do item protegido, Azure Site Recovery inicia a replicação (sinônimo à sincronização) dos dados do computador de origem para a região de destino. Durante esse período, a réplica dos discos de origem é criada. Somente após a conclusão da cópia dos discos originais, as alterações Delta são copiadas para a região de destino. O tempo necessário para copiar os discos originais depende de vários parâmetros, como:

- tamanho dos discos do computador de origem
- largura de banda disponível para transferir os dados para o Azure (você pode aproveitar o planejador de implantação para identificar a largura de banda ideal necessária)
- processar recursos do servidor como memória, espaço livre em disco, CPU disponível para armazenar em cache & processar os dados recebidos de itens protegidos (verifique se o servidor de processo está [íntegro](vmware-physical-azure-monitor-process-server.md#monitor-proactively))

Para acompanhar o progresso da replicação inicial, navegue até o cofre dos serviços de recuperação em portal do Azure-> itens replicados-> o valor da coluna "status" do item replicado do monitor. O status mostra a porcentagem de conclusão da replicação inicial. Ao passar o mouse sobre o status, o "total de dados transferidos" estaria disponível. Quando você clicar em status, uma página contextual será aberta e exibirá os seguintes parâmetros:

- Última atualização em – indica a hora mais recente em que as informações de replicação de todo o computador foram atualizadas pelo serviço.
- Porcentagem concluída-indica a porcentagem de replicação inicial concluída para a VM
- Total de dados transferidos-quantidade de dados transferidos da VM para o Azure

    :::image type="content" source="media/vmware-azure-enable-replication/initial-replication-state.png" alt-text="Estado de replicação" lightbox="media/vmware-azure-enable-replication/initial-replication-state.png":::

- Progresso da sincronização (para rastrear detalhes em um nível de disco)
    - Estado da replicação
      - Se a replicação ainda não for iniciada, o status será atualizado como "na fila". Durante a replicação inicial, somente três discos são replicados de cada vez. Esse mecanismo é seguido para evitar a limitação no servidor de processo.
      - Depois que a replicação é iniciada, o status é atualizado como "em andamento".
      - Após a conclusão da replicação inicial, o status será marcado como "concluído".        
   - Site Recovery lê o disco original, transfere dados para o Azure e captura o progresso em um nível de disco. Observe que, Site Recovery ignora a replicação do tamanho de disco não ocupado e a adiciona aos dados concluídos. Portanto, a soma dos dados transferidos em todos os discos pode não se somar ao "total de dados transferidos" no nível da VM.
   - Ao clicar no balão de informações em um disco, você pode obter detalhes sobre quando a replicação (sinônimo de sincronização) foi disparada para o disco, dados transferidos para o Azure nos últimos 15 minutos seguidos pelo último carimbo de data/hora atualizado. Esse carimbo de data/hora indica o horário mais recente em que as informações foram recebidas pelo serviço do Azure da máquina de origem :::image type="content" source="media/vmware-azure-enable-replication/initial-replication-info-balloon.png" alt-text="inicial-replicação-informações-balão-detalhes" lightbox="media/vmware-azure-enable-replication/initial-replication-info-balloon.png":::
   - A integridade de cada disco é exibida
      - Se a replicação for mais lenta do que o esperado, o status do disco se transformará em aviso
      - Se a replicação não estiver progredindo, o status do disco se tornará crítico

Se a integridade estiver em estado crítico/de aviso, verifique se a integridade da replicação do computador e do [servidor de processo](vmware-physical-azure-monitor-process-server.md) estão íntegras. 

Assim que habilitar o trabalho de replicação for concluído, o progresso da replicação será 0% e o total de dados transferidos seria NA. Ao clicar, os dados em cada disco identificado seriam como "NA". Isso indica que a replicação ainda está para ser iniciada e Azure Site Recovery ainda está recebendo as estatísticas mais recentes. O progresso é atualizado em um intervalo de 30 min.

> [!NOTE]
> Certifique-se de atualizar os servidores de configuração, os servidores de processo de expansão e os agentes de mobilidade para as versões 9,36 ou superiores para garantir que o progresso preciso seja capturado e enviado aos serviços de Site Recovery.


## <a name="view-and-manage-vm-properties"></a>Exibir e gerenciar as propriedades da VM

Em seguida, verifique as propriedades da máquina virtual de origem. Lembre-se de que o nome da VM do Azure precisa estar em conformidade com os [Requisitos de máquina virtual do Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

1. Vá para **configurações**  >  **itens replicados** e, em seguida, selecione a máquina virtual. A página **Essentials** mostra informações sobre as configurações e o status da VM.
1. Em **Propriedades**, você pode exibir informações de replicação e de failover para a VM.
1. Em **computação e rede**  >  **Propriedades de computação**, você pode alterar várias propriedades de VM.

   :::image type="content" source="./media/vmware-azure-enable-replication/vmproperties.png" alt-text="Janela Propriedades de computação e rede":::

   - **Nome da VM do Azure**: modifique o nome para atender aos requisitos do Azure, se necessário.
   - **Tamanho da VM de destino ou tipo de VM**: o tamanho padrão da VM é escolhido com base em parâmetros que incluem contagem de disco, contagem de NIC, contagem de núcleo de CPU, memória e tamanhos de função VM disponíveis na região do Azure de destino. Azure Site Recovery escolhe o primeiro tamanho de VM disponível que satisfaz todos os critérios. Você pode selecionar um tamanho de VM diferente com base em suas necessidades a qualquer momento antes do failover. O tamanho do disco da VM também é baseado no tamanho do disco de origem e só pode ser alterado após o failover. Saiba mais sobre tamanhos de disco e taxas de IOPS em [escalabilidade e metas de desempenho para discos de VM](../virtual-machines/disks-scalability-targets.md).
   - **Grupo de recursos**: você pode selecionar um [grupo de recursos](../azure-resource-manager/management/overview.md#resource-groups), do qual uma máquina virtual se torna parte de um failover posterior. Você pode alterar essa configuração a qualquer momento antes do failover. Após o failover, se você migrar a máquina virtual para um grupo de recursos diferente, as configurações de proteção dessa quebra de máquina virtual.
   - **Conjunto de disponibilidade**: você pode selecionar um [conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md) se sua máquina virtual precisar fazer parte de um failover posterior. Ao selecionar um conjunto de disponibilidade, tenha em mente as seguintes informações:
     - Somente os conjuntos de disponibilidade que pertencem ao grupo de recursos especificado são listados.
     - As VMs que estão em redes virtuais diferentes não podem fazer parte do mesmo conjunto de disponibilidade.
     - Somente máquinas virtuais do mesmo tamanho podem fazer parte de um mesmo conjunto de disponibilidade.

1. Você também pode adicionar informações sobre a rede de destino, a sub-rede e o endereço IP atribuído à VM do Azure.
1. Em **discos**, você pode ver o sistema operacional e os discos de dados na VM que serão replicados.

### <a name="configure-networks-and-ip-addresses"></a>Configurar redes e endereços IP

Você pode definir o endereço IP de destino:

- Se você não fornecer um endereço, a VM com failover usará DHCP.
- Se você definir um endereço que não esteja disponível no failover, o failover não funcionará.
- Se o endereço estiver disponível na rede de failover de teste, você poderá usar o mesmo endereço IP de destino para o failover de teste.

O número de adaptadores de rede é determinado pelo tamanho especificado para a máquina virtual de destino, da seguinte maneira:

- Se o número de adaptadores de rede na máquina virtual de origem for menor ou igual ao número de adaptadores permitidos para o tamanho da VM de destino, o destino terá o mesmo número de adaptadores que a origem.
- Se o número de adaptadores para a máquina virtual de origem exceder o número permitido para o tamanho da VM de destino, o tamanho máximo de destino será usado. Por exemplo, se uma máquina virtual de origem tiver dois adaptadores de rede e o tamanho da VM de destino der suporte a quatro, a máquina virtual de destino terá dois adaptadores. Se a VM de origem tiver dois adaptadores, mas o tamanho de destino oferecer suporte apenas a um, a VM de destino terá apenas um adaptador.
- Se a máquina virtual tiver vários adaptadores de rede, todos eles se conectarão à mesma rede. Além disso, o primeiro adaptador mostrado na lista se torna o adaptador de rede padrão na máquina virtual do Azure.

### <a name="azure-hybrid-benefit"></a>Benefício Híbrido do Azure

Os clientes do Microsoft Software Assurance podem usar Benefício Híbrido do Azure para economizar em custos de licenciamento para computadores Windows Server que são migrados para o Azure. O benefício também se aplica à recuperação de desastre do Azure. Se você estiver qualificado, poderá atribuir o benefício à máquina virtual que Site Recovery cria se houver um failover.

1. Vá para as **Propriedades de computador e rede** da máquina virtual replicada.
1. Responder quando for perguntado se você tem uma licença do Windows Server que o torna elegível para Benefício Híbrido do Azure.
1. Confirme se você tem uma licença do Windows Server qualificada com o Software Assurance que pode ser usada para aplicar o benefício à VM que será criada no failover.
1. Salve as configurações para a máquina virtual replicada.

[Saiba mais](https://azure.microsoft.com/pricing/hybrid-benefit/) sobre benefício híbrido do Azure.

## <a name="next-steps"></a>Próximas etapas

Depois que a máquina virtual atingir um estado protegido, tente um [failover](site-recovery-failover.md) para verificar se seu aplicativo aparece no Azure.

- [Saiba mais](site-recovery-manage-registration-and-protection.md) sobre como limpar as configurações de proteção e de registro para desabilitar a replicação.
- [Saiba mais](vmware-azure-disaster-recovery-powershell.md) sobre como automatizar a replicação para suas máquinas virtuais usando o PowerShell.
