---
title: Executar failover e failback de servidores físicos para recuperação de desastre para o Azure com o Site Recovery | Microsoft Docs
description: Saiba como executar failover de servidores físicos para o Azure e failback para o site local para recuperação de desastre com o Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: raynew
ms.openlocfilehash: edb169d131aafd045fdf0f670e1dda87677d57ee
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61036311"
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>Executar failover e failback de servidores físicos replicados para Azure

Este tutorial descreve como executar failover de um servidor físico no Azure. Depois de executar failover, execute failback em um servidor para o site local quando ele estiver disponível.

## <a name="preparing-for-failover-and-failback"></a>Preparando para failover e failback

Servidores físicos replicados no Azure usando o Site Recovery só podem falhar como VMs VMware. Você precisa de uma infraestrutura de VMware local para executar o failback.

O failover e o failback têm quatro fases:

1. **Fazer failover para o Azure**: Faz failover de computadores do site local para o Azure.
2. **Proteger novamente as VMs do Azure**: Proteja as VMs do Azure novamente, para que elas comecem a replicação para as VMs VMware locais.
3. **Executar failover para o local**: Execute um failover, para executar failback do Azure.
4. **Proteger novamente as VMs locais**: Após realizar o failback dos dados, você protege novamente as VMs VMware locais para as quais realizou o failback, para que comecem a ser replicadas no Azure.

## <a name="verify-server-properties"></a>Verificar as propriedades do servidor

Confira as propriedades do servidor e verifique se ele está em conformidade com os [Requisitos do Azure](vmware-physical-azure-support-matrix.md#replicated-machines) quanto a VMs do Azure.

1. Em **Itens Protegidos**, clique em **Itens Replicados** e selecione o computador.

2. No painel **Item Replicado**, há um resumo das informações do computador, o status de integridade e os últimos pontos de recuperação disponíveis. Clique em **Propriedades** para exibir mais detalhes.
3. Em **Computação e Rede**, você pode modificar o nome do Azure, o grupo de recursos, o tamanho do destino, o [conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md) e as configurações de disco gerenciado
4. Você pode exibir ou modificar as configurações de rede, incluindo a rede/sub-rede na qual a VM do Azure estará localizada após o failover e o endereço IP que será atribuído a ela.
5. Em **Discos**, é possível visualizar as informações sobre o sistema operacional e os discos de dados do computador.

## <a name="run-a-failover-to-azure"></a>Executar um failover para o Azure

1. Em **Configurações** > **Itens replicados** clique no computador > **Failover**.
2. Em **Failover**, selecione um **Ponto de Recuperação** para executar o failover. Você pode usar uma das seguintes opções:
   - **Mais recente**: Essa opção primeiro processa todos os dados enviados ao Site Recovery. Ela fornece o RPO (objetivo de ponto de recuperação) mais baixo porque a VM do Azure criada após o failover tem todos os dados que foram replicados para o Site Recovery quando o failover foi disparado.
   - **Mais recente processado**: Essa opção executa failover do computador para o último ponto de recuperação processado pelo Site Recovery. Essa opção fornece um RTO (Objetivo do Tempo de Recuperação) baixo porque não há tempo gasto para processar dados não processados.
   - **Consistente com o aplicativo mais recente**: Essa opção executa failover do computador para o ponto de recuperação consistente com o aplicativo mais recente pelo Site Recovery.
   - **Personalizado**: Especifica um ponto de recuperação.

3. Selecione **Desligar o computador antes do início do failover** se quiser que o Site Recovery tente realizar um desligamento o computador de origem antes de disparar o failover. O failover continuará mesmo o desligamento falhar. Você pode acompanhar o progresso do failover na página **Trabalhos**.
4. Se você preparou a conexão com a VM do Azure, conecte-se para validá-la após o failover.
5. Depois de verificar, **Confirme** o failover. Essa ação exclui todos os pontos de recuperação disponíveis.

> [!WARNING]
> Não cancele um failover em andamento. Antes do início do failover, a replicação do computador é interrompido. Se você cancelar o failover, ele será interrompido, mas o computador não replicará novamente.
> Para servidores físicos, o processamento adicional de failover pode levar cerca de oito a dez minutos para concluir.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar para conectar VMs do Azure após o failover

Se você quiser se conectar às VMs do Azure usando RDP/SSH após o failover, siga os requisitos resumidos na tabela mostrada [aqui](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

Siga as etapas descritas [aqui](site-recovery-failover-to-azure-troubleshoot.md) para solucionar quaisquer problemas de conectividade após o failover.

## <a name="create-a-process-server-in-azure"></a>Criar um servidor de processo no Azure

O servidor de processo recebe dados da VM do Azure e envia-os para o site local. Uma rede de baixa latência é necessária entre o servidor de processo e o computador protegido.

- Para fins de teste, se você tiver uma conexão Azure ExpressRoute, será possível usar o servidor de processo local que é instalado automaticamente no servidor de configuração.
- Se você tiver uma conexão VPN ou estiver executando o failback em um ambiente de produção, deverá configurar uma VM do Azure como um servidor de processo baseado no Azure para failback.
- Siga as instruções [neste artigo](vmware-azure-set-up-process-server-azure.md) para configurar um servidor de processo no Azure.

## <a name="configure-the-master-target-server"></a>Configurar o servidor de destino mestre

Por padrão, o servidor de destino mestre recebe dados de failback. Ele é executado no servidor de configuração local.

- Se a VM VMware para as quais o failback foi executado estiver em um host ESXi gerenciado por um VMware vCenter Server, o servidor de destino mestre deve ter acesso ao armazenamento de dados da VM (VMDK), para gravar os dados replicados nos discos da VM. Verifique se o armazenamento de dados da VM está montado no host de destino mestre com acesso de leitura/gravação.
- Se o host ESXi não for gerenciado por um vCenter Server, o serviço Site Recovery criará uma nova VM durante o processo de nova proteção. A VM é criada no host ESX na qual você cria a VM destino mestre. O disco rígido da VM precisa estar em um armazenamento de dados acessível para o host no qual o servidor de destino mestre está em execução.
- Em computadores locais para os quais o failback foi executado, você deverá concluir a descoberta do host no qual o servidor de destino mestre está em execução para que seja possível proteger o computador novamente.
- Outra opção, se a VM local já existir para failback, ela deverá ser excluída antes da execução de um failback. O failback vai criar uma nova VM no mesmo host que o host ESX de destino mestre. Ao realizar o failback para um local alternativo, os dados são recuperados no mesmo armazenamento de dados e no mesmo host ESX usados pelo servidor de destino mestre local.
- Você não pode usar o Storage vMotion no servidor de destino mestre. Se você fizer isso, o failback não funcionará, porque os discos não estarão disponíveis para ele. Exclua os servidores de destino mestres de sua lista do vMotion.

## <a name="reprotect-azure-vms"></a>Proteger as VMs do Azure

Este procedimento considera que a VM local não está disponível e que você está protegendo novamente para um local alternativo.

1. Em **Configurações** > **Itens replicados**, clique com o botão direito do mouse na VM com failover > **Proteger novamente**.
2. Em **Proteger novamente**, verifique se **Do Azure para local** está selecionado.
3. Especifique o servidor de destino mestre local e o servidor de processo.

4. Em **Armazenamento de dado**, selecione o armazenamento de dados de destino mestre para o qual deseja recuperar os discos locais. Use esta opção quando a VM local tiver sido excluída e você precisar criar novos discos. Essa configuração será ignorada se os discos já existirem, mas você precisa especificar um valor.
5. Selecione a unidade de retenção de destino mestre. A politica de failback é selecionada automaticamente.
6. Clique em **OK** para iniciar a nova proteção. Um trabalho começará a replicar a máquina virtual do Azure para o site local. Você pode acompanhar o andamento na guia **Trabalhos**.

> [!NOTE]
> Se você deseja recuperar a VM do Azure para uma VM local existente, monte o armazenamento de dados da máquina virtual local com acesso de leitura/gravação no host ESXi do servidor de destino mestre.


## <a name="run-a-failover-from-azure-to-on-premises"></a>Executar um failover do Azure para o local

Para replicar novamente para o local, uma política de failback é usada. Essa política foi criada automaticamente quando você criou uma política de replicação para a replicação para o Azure:

- A política é associada automaticamente ao servidor de configuração.
- A política não pode ser modificada.
- Os valores da política são:
    - Limite RPO = 15 minutos
    - Retenção de ponto de recuperação = 24 horas
    - Frequência de instantâneos consistentes com o aplicativo = 60 minutos

Execute o failover da seguinte maneira:

1. Na página **Itens duplicados**, clique como botão direito do mouse no computador > **Failover Não Planejado**.
2. Em **Confirmar Failover**, verifique se a direção do failover é do Azure.

3. Selecione o ponto de recuperação que você deseja usar para o failover. Um ponto de recuperação consistente com o aplicativo ocorre antes do ponto mais recente no tempo, e vai causar perda de dados. Quando o failover é executado, o Site Recovery encerra as VMs do Azure e inicializa a VM local. Haverá em certo tempo de inatividade, portanto escolha um momento apropriado.
4. Com o botão direito do mouse no computador e, em seguida, clique em **Confirmar**. Isso dispara um trabalho que remove as VMs do Azure.
5. Verifique se as VMs do Azure foram desligadas conforme o esperado.


## <a name="reprotect-on-premises-machines-to-azure"></a>Proteger novamente as máquinas locais para o Azure

Os dados agora devem estar de volta no site local, mas eles não estão sendo replicados para o Azure. Você pode iniciar a replicação para o Azure novamente da seguinte maneira:

1. Em cofre > **Configurações** >**Itens replicados**, selecione as VMs com failback e clique em **Proteger novamente**.
2. Selecione o servidor de processo que é usado para enviar os dados replicados para o Azure e, em seguida, clique em **OK**.

Depois que a nova proteção for concluída, a VM será replicada novamente para o Azure e você poderá executar failover conforme o necessário.
