---
title: Mover as VMs do Azure para outra região com o Azure Site Recovery
description: Use o Azure Site Recovery para mover as VMs de uma região do Azure para outra.
services: site-recovery
author: Sharmistha-Rai
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: sharrai
ms.custom: MVC
ms.openlocfilehash: 076adbfd4cecf7dae9ffc490e911fcb7ffce48e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93394825"
---
# <a name="move-vms-to-another-azure-region"></a>Mover VMs para outra região do Azure

Há cenários em que é possível mover as VMs (máquinas virtuais) da IaaS do Azure existentes de uma região para outra. Por exemplo, você quer melhorar a confiabilidade e a disponibilidade da VMs existentes, melhorar a capacidade de gerenciamento ou mover por razões de governança. Para obter mais informações, consulte a [visão geral de movimentação da VM do Azure](azure-to-azure-move-overview.md). 

Você pode usar o serviço do [Azure Site Recovery](site-recovery-overview.md) a fim de mover VMs do Azure para uma região secundária.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> 
> * Verificar os pré-requisitos para a movimentação
> * Preparar as VMs de origem e a região de destino
> * Copiar os dados e habilitar a replicação
> * Testar a configuração e executar a movimentação
> * Excluir os recursos na região de origem


> [!IMPORTANT]
> Para mover VMs do Azure para outra região, recomendamos usar o [Azure Resource Mover](../resource-mover/tutorial-move-region-virtual-machines.md). O Resource Mover está em versão prévia pública e fornece:
> - Um único hub para mover recursos entre regiões.
> - Tempo e complexidade de movimentação reduzidos. Tudo o que você precisa está em um único local.
> - Uma experiência simples e consistente para mover os diferentes tipos de recursos do Azure.
> - Uma maneira fácil de identificar dependências entre os recursos que você deseja mover. Isso ajuda a mover os recursos relacionados em conjunto, para que tudo funcione conforme o esperado na região de destino após a movimentação.
> - Se você quiser excluí-los após a movimentação, realize a limpeza automática de recursos na região de origem.
> - Testes. Você pode experimentar uma movimentação e depois descartá-la caso não queira fazer uma movimentação completa.



> [!NOTE]
> Este tutorial mostra como mover as VMs do Azure de uma região para outra. Se você precisar melhorar a disponibilidade, movendo as VMs em um conjunto de disponibilidade para VMs fixas de zona em uma região diferente, consulte o tutorial [Mover as VMs do Azure para Zonas de Disponibilidade](move-azure-vms-avset-azone.md).

## <a name="prerequisites"></a>Prerequisites

- Verifique se as VMs do Azure estão na região do Azure a partir da qual você quer mover.
- Verifique se [há suporte para sua escolha de combinação de região de origem – região de destino](./azure-to-azure-support-matrix.md#region-support) e tome uma decisão informada sobre a região de destino.
- Verifique se você entende os [componentes e a arquitetura do cenário](azure-to-azure-architecture.md).
- Examine os [requisitos e limitações com suporte](azure-to-azure-support-matrix.md).
- Verificar permissões da conta. Se você criou a conta gratuita do Azure, você será o administrador da assinatura. Se você não for o administrador da assinatura, trabalhe com o administrador para atribuir as permissões necessárias. Para habilitar a replicação de uma VM e essencialmente copiar dados usando o Azure Site Recovery, você deverá ter:

    - Permissões para criar uma VM em recursos do Azure. A função interna Colaborador da Máquina Virtual tem essas permissões, incluindo:
    - Permissão para criar uma VM no grupo de recursos selecionado
    - Permissão para criar uma VM na rede virtual selecionada
    - Permissão para gravar na conta de armazenamento selecionada
    
    - Permissões para gerenciar as operações do Azure Site Recovery. A função Colaborador do Site Recovery tem todas as permissões necessárias para gerenciar operações do Site Recovery em um cofre dos Serviços de Recuperação.

- Certifique-se de que todos os certificados raiz mais recentes estejam nas VMs do Azure que você quer mover. Se os certificados raiz mais recentes não estiverem na VM, as restrições de segurança impedirão a cópia dos dados na região de destino.

- Para VMs Windows, instale todas as atualizações do Windows mais recentes na VM para que todos os certificados raiz confiáveis estejam no computador. Em um ambiente desconectado, siga os processos padrão do Windows Update e de atualização de certificado para sua organização.
    
- Para VMs do Linux, siga as diretrizes fornecidas pelo distribuidor Linux para obter os certificados raiz confiáveis mais recentes e a lista de certificados revogados na VM.
- Certifique-se de que você não está usando um proxy de autenticação para controlar a conectividade de rede das VMs que você quer mover.

- Se a VM que você está tentando mover não tiver acesso à Internet ou estiver usando um proxy de firewall para controlar o acesso de saída, [verifique os requisitos](azure-to-azure-tutorial-enable-replication.md#set-up-vm-connectivity).

- Identifique o layout de rede de origem e todos os recursos que você está usando atualmente. Isso inclui, mas não se limita a, balanceadores de carga, NSGs (grupos de segurança de rede) e IPs públicos.

- Verifique se a assinatura do Azure permite criar VMs na região de destino utilizada para recuperação de desastre. Contate o suporte para habilitar a cota necessária.

- Certifique-se de que a assinatura tenha recursos suficientes para dar suporte a VMs com tamanhos que correspondam às VMs de origem. Se você estiver usando o Site Recovery para copiar dados para o destino, o Site Recovery escolherá o mesmo tamanho ou o tamanho mais próximo possível para a VM de destino.

- Certifique-se de criar um recurso de destino para cada componente identificado no layout de rede de origem. Essa etapa é importante para garantir que as VMs tenham toda a funcionalidade e recursos na região de destino que você tinha na região de origem.

     > [!NOTE] 
     > O Azure Site Recovery descobre e cria automaticamente uma rede virtual quando você habilita a replicação para a VM de origem. Você também pode pré-criar uma rede e atribuí-la à VM no fluxo do usuário para habilitar a replicação. Conforme mencionado posteriormente, será necessário criar manualmente outros recursos na região de destino.

    Para criar os recursos de rede mais utilizados e relevantes com base na configuração da VM de origem, consulte a documentação a seguir:
    - [Grupos de segurança de rede](../virtual-network/manage-network-security-group.md)
    - [Balanceadores de carga](../load-balancer/index.yml)
    -  [IP público](../virtual-network/virtual-network-public-ip-address.md)
    - Para quaisquer outros componentes de rede, consulte a [documentação da rede](../index.yml?pivot=products&panel=network).



## <a name="prepare"></a>Preparar
As etapas a seguir mostram como preparar a máquina virtual para a movimentação usando o Azure Site Recovery como uma solução. 

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Criar o cofre em qualquer região, exceto a região de origem

1. Entre no [Portal do Azure](https://portal.azure.com)
1. Em pesquisa, digite Serviços de Recuperação > clique em cofres dos Serviços de Recuperação
1. No menu dos cofre dos Serviços de Recuperação, clique em +Adicionar.
1. Em **Nome**, especifique o nome amigável **ContosoVMVault**. Se você tiver mais de uma assinatura, selecione uma delas.
1. Crie o grupo de recursos **ContosoRG**.
1. Especifique uma região do Azure. Para verificar as regiões com suporte, consulte a disponibilidade geográfica em [detalhes de preços do Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
1. Em **Cofres dos Serviços de Recuperação**, selecione **ContosoVMVault** > **Itens replicados** >  **+Replicar**.
1. No menu suspenso, selecione **Máquinas Virtuais do Azure**.
1. Em **Local de origem**, selecione a fonte de região do Azure em que suas VMs estão sendo executados.
1. Selecione o modelo de implantação do Gerenciador de Recursos. Em seguida, selecione a **Assinatura de origem** e o **Grupo de recursos de origem**.
1. Selecione **OK** para salvar as configurações.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Habilitar replicação para VMs do Azure e iniciar cópia dos dados

O Site Recovery recupera uma lista das VMs associadas à assinatura e ao grupo de recursos.

1. Na próxima etapa, selecione a VM que você quer mover e selecione **OK**.
1. Em **Configurações**, selecione **Recuperação de desastre**.
1. Em **Configurar recuperação de desastre** > **Região de destino**, selecione a região de destino para a qual será replicada.
1. Para este tutorial, aceite as outras configurações padrão.
1. Selecione **Habilitar replicação**. Esta etapa inicia um trabalho para habilitar a replicação da VM.



## <a name="move"></a>Mover

As etapas a seguir mostram como executar a movimentação para a região de destino.

1. Vá para o cofre. Em **Configurações** > **Itens replicados**, selecione a VM e, em seguida, selecione **Failover**.
2. Em **Failover**, selecione **Mais recente**.
3. Selecione **Desligar o computador antes do início do failover**. O Site Recovery tenta desligar a VM de origem antes de acionar o failover. O failover continuará mesmo o desligamento falhar. Você pode acompanhar o progresso do failover na página **Trabalhos** .
4. Depois que o trabalho for concluído, verifique se a VM será exibida na região de destino do Azure conforme esperado.


## <a name="discard"></a>Descartar 

Caso você tenha marcado a VM movida e precise fazer alterações no ponto de failover ou deseje voltar a um ponto anterior, nos **Itens replicados**, selecione a VM com o botão direito do mouse > **Alterar ponto de recuperação**. Esta etapa fornece a opção de especificar outro ponto de recuperação e failover para aquele. 


## <a name="commit"></a>Commit 

Depois de marcar a VM movida e estiver pronto para confirmar a alteração, nos **Itens replicados**, selecione a VM com o botão direito do mouse > **Confirmar**. Essa etapa conclui o processo de movimentação para a região de destino. Aguarde até que o trabalho de confirmação seja concluído.

## <a name="clean-up"></a>Limpar

As etapas a seguir orientarão você sobre como limpar a região de origem, bem como os recursos relacionados que foram usados para a movimentação.

Para todos os recursos que foram usados para a movimentação:

- Vá até a VM. Selecione **Desabilitar Replicação**. Essa etapa impede que o processo copie os dados para a VM.

   > [!IMPORTANT]
   > É importante executar essa etapa para evitar ser cobrado pela replicação do Azure Site Recovery.

Caso não tenha planos de reutilizar nenhum dos recursos de origem, conclua estas etapas adicionais:

1. Exclua todos os recursos de rede relevantes da região de origem que você identificou nos [pré-requisitos](#prerequisites).
1. Exclua a conta de armazenamento correspondente da região de origem.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você moveu uma VM do Azure para uma região do Azure diferente. Agora, você pode configurar a recuperação de desastre para a VM que você moveu.

> [!div class="nextstepaction"]
> [Configurar a recuperação de desastres após a migração](azure-to-azure-quickstart.md)
