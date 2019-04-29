---
title: Configurar a recuperação de desastre em VMs locais do Hyper-V em nuvens do VMM para o Azure com o Azure Site Recovery | Microsoft Docs
description: Saiba como configurar a recuperação de desastre de VMs locais do Hyper-V em nuvens do System Center VMM para o Azure com o serviço Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 64559f653ba8a466de7bec10db34383b508e3e4b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60947396"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>Configurar a recuperação de desastre de VMs locais do Hyper-V em nuvens do VMM para o Azure

Este artigo descreve como habilitar a replicação para VMs do Hyper-V locais gerenciadas pelo System Center Virtual Machine Manager (VMM), para recuperação de desastres para o Azure usando o [Azure Site Recovery](site-recovery-overview.md) service. Se você não estiver usando o VMM, em seguida, [siga este tutorial](hyper-v-azure-tutorial.md).

Este é o terceiro tutorial em uma série que mostra como configurar a recuperação de desastre para o Azure para VMs VMware locais. No tutorial anterior, podemos [preparou o ambiente do Hyper-V local](hyper-v-prepare-on-premises-tutorial.md) para recuperação de desastres no Azure. 

Neste tutorial, você aprenderá como:


> [!div class="checklist"]
> * Selecionar a origem e o destino de replicação.
> * Configurar o ambiente de replicação de origem, incluindo componentes de Site Recovery locais e o ambiente de replicação de destino.
> * Configurar o mapeamento de rede para mapear entre as redes de VM do VMM e as redes virtuais do Azure.
> * Criar uma política de replicação
> * Habilitar a replicação para uma VM


> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário. Eles usam opções padrão quando possível e não mostram todas as possíveis configurações e caminhos. Para obter instruções detalhadas, leia o artigo na seção Instruções do Sumário do Site Recovery.

## <a name="before-you-begin"></a>Antes de começar

Este é o terceiro tutorial de uma série. Este tutorial presume que você já tenha concluído as tarefas nos tutoriais anteriores:

1. [Preparar o Azure](tutorial-prepare-azure.md)
2. [Preparar Hyper-V local](tutorial-prepare-on-premises-hyper-v.md) este é o terceiro tutorial em uma série. Este tutorial presume que você já tenha concluído as tarefas nos tutoriais anteriores:


## <a name="select-a-replication-goal"></a>Selecione uma meta de replicação

1. Em **Cofres dos Serviços de Recuperação**, selecione o cofre. Preparamos o cofre **ContosoVMVault** no tutorial anterior.
2. Em **Introdução**, clique em **Site Recovery**. A seguir, clique em **Preparar Infraestrutura**
3. Na **meta de proteção** > **onde estão suas máquinas localizadas?**, selecione **locais**.
4. Em **Para qual deseja replicar os seus computadores?**, selecione **Para o Azure**.
5. Em **Os seus computadores estão virtualizados?**, selecione **Sim, com o Hyper-V**.
6. Em **Você está usando o System Center VMM?**, selecione **Sim**. Em seguida, clique em **OK**.

    ![Meta de replicação](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)


## <a name="confirm-deployment-planning"></a>Confirmar planejamento de implantação

1. Em **Planejamento de implantação**, se você estiver planejando uma implantação grande, baixe o Planejador de Implantações do Hyper-V usando o link na página. [Saiba mais](hyper-v-deployment-planner-overview.md) sobre o planejamento de implantação do Hyper-V.
2. Para os fins deste tutorial, não precisamos do planejador de implantação. Na **você concluiu o planejamento de implantação?**, selecione **farei isso mais tarde**. Em seguida, clique em **OK**.


## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Quando você configura o ambiente de origem, instale o provedor Azure Site Recovery no servidor do VMM e registrar o servidor no cofre. Você pode instalar o agente de serviços de recuperação do Azure em cada host Hyper-V. 

1. Em **Preparar a infraestrutura**, clique em **Origem**.
2. Em **Preparar origem**, clique em **+ VMM** para adicionar um servidor VMM. Em **Adicionar Servidor**, verifique se o **Servidor do System Center VMM** aparece em **Tipo de servidor**.
3. Baixe o instalador do Provedor do Microsoft Azure Site Recovery.
4. Baixe a chave do registro do cofre. Você precisará dela quando executar a instalação do Provedor. A chave é válida por cinco dias após ser gerada.
5. Baixe o instalador para o agente de serviços de recuperação do Microsoft Azure.

    ![Baixar](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>Instalar o Provedor no servidor VMM

1. No assistente de Instalação do Provedor do Azure Site Recovery > **Microsoft Update**, opte por usar o Microsoft Update para verificar se há atualizações do provedor.
2. Em **Instalação**, aceite o local de instalação padrão do Provedor e clique em **Instalar**. 
3. Após a instalação, no Assistente de Registro do Microsoft Azure Site Recovery > **Configurações do Cofre**, clique em **Procurar** e, em **Arquivo de Chave**, selecione a chave do cofre do arquivo baixado.
4. Especifique a assinatura do Azure Site Recovery e o nome do cofre (**ContosoVMVault**). Especifique um nome amigável para o servidor de VMM para identificá-lo no cofre.
5. Em **Configurações de Proxy**, selecione **Conectar diretamente ao Azure Site Recovery sem um proxy**.
6. Aceite o local padrão para o certificado que é usado para criptografar dados. Dados criptografados serão descriptografados quando você executar o failover.
7. Em **Sincronizar metadados de nuvem**, selecione **Sincronizar metadados da nuvem com o portal de recuperação de site**. Esta ação só precisa acontecer uma vez em cada servidor. Em seguida, clique em **Registrar**.
8. Depois que o servidor foi registrado no cofre, clique em **Concluir**.

Após a conclusão do registro, os metadados do servidor são recuperados pelo Azure Site Recovery e o servidor VMM é exibido em **Infraestrutura do Site Recovery**.

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>Instalar o agente de serviços de recuperação em hosts Hyper-V

Instale o agente em cada host Hyper-V que contém máquinas virtuais que você deseja replicar.

1. No Assistente de Instalação do Agente de Serviços de Recuperação do Microsoft Azure > **Verificação de Pré-requisitos**, clique em **Avançar**. Pré-requisitos faltantes serão instalados automaticamente.
2. Em **Configurações da Instalação**, aceite o local de instalação e o local do cache. A unidade de cache precisa de pelo menos 5 GB de armazenamento. É recomendável uma unidade com 600 GB ou mais de espaço livre. Em seguida, clique em **Instalar**.
3. Em **Instalação**, quando a instalação for concluída, clique em **Fechar** para concluir o assistente.

    ![Instalar o agente](./media/hyper-v-vmm-azure-tutorial/mars-install.png)
    

## <a name="set-up-the-target-environment"></a>Configurar o ambiente de origem

1. Clique em **Preparar a Infraestrutura** > **Destino**.
2. Selecione a assinatura e o grupo de recursos **ContosoRG**, em que as VMs do Azure serão criadas após o failover.
3. Selecione o **Resource Manager** modelo de implantação.

A Recuperação de Site verifica se você tem uma ou mais contas de armazenamento e redes do Azure compatíveis.


## <a name="configure-network-mapping"></a>Configurar o mapeamento de rede

1. Em **Infraestrutura do Site Recovery** > **Mapeamentos de rede** > **Mapeamento de Rede**, clique no ícone **+Mapeamento de Rede**.
2. Em **Adicionar mapeamento de rede**, selecione o servidor do VMM de origem. Selecione **Azure** como o destino.
3. Verifique a assinatura e o modelo de implantação após o failover.
4. Em **Rede de origem**, selecione a rede de origem da VM local.
5. Em **Rede de destino**, selecione a rede do Azure na qual as VMs do Azure de réplica estarão localizadas quando criadas após o failover. Em seguida, clique em **OK**.

    ![Mapeamento de rede](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Configurar uma política de replicação

1. Clique em **Preparar a Infraestrutura** > **Configurações de Replicação** > **+Criar e associar**.
2. Em **Criar e associar política**, especifique um nome de política. Estamos usando **ContosoReplicationPolicy**.
3. Deixe as configurações padrão e clique em **OK**.
    - **Frequência de cópia** indica que os dados delta (após a replicação inicial) serão replicadas a cada cinco minutos.
    - **Retenção do ponto de recuperação** indica para cada ponto de recuperação será mantido por duas horas.
    - **Frequência do instantâneo consistente com o aplicativo** indica que os pontos de recuperação contendo instantâneos consistentes com aplicativos serão criados a cada hora.
    - **Hora de início para replicação inicial** indica que a replicação inicial deve começar imediatamente.
    - **Criptografar dados armazenados no Azure** – a configuração padrão **Desativado** indica que dados em repouso no Azure não são criptografados.
4. Depois que a política for criada, clique em **OK**. Quando você cria uma nova política, ela é automaticamente associada à nuvem do VMM.

## <a name="enable-replication"></a>Habilitar a replicação

1. Em **Replicar aplicativo**, clique em **Origem**. 
2. Em **Origem**, selecione a nuvem do VMM. Em seguida, clique em **OK**.
3. Em **Destino**, verifique o Azure como o destino, a assinatura do cofre e selecione o modelo do **Resource Manager**.
4. Selecione a conta de armazenamento **contosovmsacct1910171607** e a rede do Azure **ContosoASRnet**.
5. Em **Máquinas Virtuais** > **Selecionar**, selecione a VM que deseja replicar. Em seguida, clique em **OK**.

   É possível acompanhar o progresso da ação **Habilitar Proteção** em **Trabalhos** > **Trabalhos do Site Recovery**. Depois que o trabalho **Finalizar Proteção** for concluído, a replicação inicial será concluída e a VM estará pronta para failover.



## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Realizar uma simulação de recuperação de desastre](tutorial-dr-drill-azure.md)
