---
title: "Configurar a recuperação de desastre em VMs locais do Hyper-V para o Azure com o Azure Site Recovery | Microsoft Docs"
description: "Saiba como configurar a recuperação de desastre de VMs locais do Hyper-V para o Azure com o serviço Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 45e9b4dc-20ca-4c14-bee3-cadb8d9b8b24
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: raynew
ms.openlocfilehash: 96e5027adfb443aba18895213e8d83894e3f060a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Configurar a recuperação de desastre de VMs locais do Hyper-V para o Azure

O serviço [Azure Site Recovery](site-recovery-overview.md) contribui para sua estratégia de recuperação de desastre ao gerenciar e orquestrar a replicação, o failover e o failback de computadores locais e de VMs (máquinas virtuais) do Azure.

Este tutorial mostra como configurar a recuperação de desastre de VMs locais do Hyper-V para o Azure. O tutorial é relevante para VMs do Hyper-V que são gerenciadas em nuvens do VMM (System Center Virtual Machine Manager) e aquelas que não são. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar os pré-requisitos locais e do Azure
> * Criar um cofre de Serviços de Recuperação para o Site Recovery 
> * Configurar os ambientes de replicação de origem e destino 
> * Configurar o mapeamento de rede (se o Hyper-V for gerenciado pelo System Center VMM)
> * Criar uma política de replicação
> * Habilitar a replicação para uma VM


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- Verifique se você entende os [componentes e a arquitetura do cenário](concepts-hyper-v-to-azure-architecture.md).
- Examine os [requisitos de suporte](site-recovery-support-matrix-to-azure.md) de todos os componentes.
- Verifique se as VMs que você deseja replicar estão em conformidade com os [requisitos de VM do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
- Para descobrir o planejamento de capacidade:
    - Use a [Ferramenta Planejador de Capacidade do Hyper-V](http://aka.ms/asr-capacity-planner-excel)para calcular a taxa de rotatividade.
    - Use o [Planejador de Capacidade do Site Recovery](http://aka.ms/asr-capacity-planner-excel) para analisar seu ambiente de origem, calcular a largura de banda e os requisitos de destino.
- Prepare o Azure. Você precisa de uma assinatura do Azure, de uma rede virtual do Azure e de uma conta de armazenamento.
- Prepare os hosts do Hyper-V local e os servidores do VMM, se for relevante.





### <a name="set-up-an-azure-account"></a>Configurar uma conta do Azure

Obtenha uma [conta do Microsoft Azure](http://azure.microsoft.com/).

- Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
- Saiba mais sobre os [preços do Site Recovery](site-recovery-faq.md#pricing) e obtenha [detalhes dos preços](https://azure.microsoft.com/pricing/details/site-recovery/).
- Descubra quais [regiões têm suporte](https://azure.microsoft.com/pricing/details/site-recovery/) do Site Recovery.

### <a name="verify-azure-account-permissions"></a>Verificar as permissões de conta do Azure

Verifique se sua conta do Azure tem as permissões necessárias para replicar as VMs.

- Examine as [permissões](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) que você precisa para replicar computadores para o Azure
- Verifique e modifique as permissões de [acesso baseado em função](../active-directory/role-based-access-control-configure.md). 


### <a name="set-up-an-azure-network"></a>Configurar uma rede do Azure

Configurar uma [rede do Azure](../virtual-network/virtual-network-get-started-vnet-subnet.md).

- As VMs do Azure são colocadas nessa rede quando são criadas após o failover.
- A rede deve estar na mesma região que o cofre dos Serviços de Recuperação.


### <a name="set-up-an-azure-storage-account"></a>Configure uma conta de armazenamento do Azure

Configure uma [conta de armazenamento do Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account).

- O Site Recovery replica máquinas locais para o armazenamento do Azure. As VMs do Azure são criadas a partir do armazenamento após o failover.
- A conta de armazenamento deve estar na mesma região do cofre de Serviços de Recuperação.
- A conta de armazenamento pode ser padrão ou [premium](../storage/common/storage-premium-storage.md).
- Se configurar uma conta premium, você precisará de uma conta padrão adicional para dados de log.

### <a name="prepare-hyper-v-hosts"></a>Prepare os hosts do Hyper-V

1. Verifique se os hosts do Hyper-V cumprem com os [requisitos de suporte](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers).
2. Verifique se os hosts podem acessar estas URLs:

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - Qualquer regra de firewall baseada em endereço IP deve permitir a comunicação com o Azure.
    - Permita os [Intervalos de IP do Datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e a porta HTTPS (443).
    - Permita os intervalos de endereços IP para a região do Azure da sua assinatura e para o Oeste dos EUA (usados para Controle de Acesso e Gerenciamento de Identidade).

### <a name="prepare-vmm-servers"></a>Preparar servidores VMM

Se os hosts do Hyper-V forem gerenciados pelo VMM, você precisará preparar o servidor do VMM localmente. 

- Verifique se o servidor do VMM cumpre com os [requisitos de suporte](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers).
- Verifique se o servidor do VMM tem pelo menos uma nuvem, com um ou mais grupos de host. O host do Hyper-V no qual as VMs estão em execução deve estar localizado na nuvem.
- O servidor do VMM precisa de acesso à Internet, com acesso às seguintes URLs:

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - Qualquer regra de firewall baseada em endereço IP deve permitir a comunicação com o Azure.
    - Permita os [Intervalos de IP do Datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e a porta HTTPS (443).
    - Permita os intervalos de endereços IP para a região do Azure da sua assinatura e para o Oeste dos EUA (usados para Controle de Acesso e Gerenciamento de Identidade).
- Prepare o servidor do VMM para o mapeamento de rede.


#### <a name="prepare-vmm-for-network-mapping"></a>Preparar VMM para mapeamento de rede

Se você estiver usando o VMM, o [mapeamento de rede](site-recovery-network-mapping.md) mapeará entre as redes de VM do VMM local e as redes virtuais do Azure. O mapeamento garante que as VMs do Azure serão conectadas à rede certa quando elas forem criadas após o failover.

Prepare o VMM para o mapeamento de rede da seguinte maneira:

1. Verifique se você tem uma [rede lógica do VMM](https://docs.microsoft.com/system-center/vmm/network-logical) associada à nuvem em que os hosts do Hyper-V estão localizados.
2. Verifique se você tem uma [rede de VM](https://docs.microsoft.com/system-center/vmm/network-virtual) vinculada à rede lógica.
3. Conecte as VMs à rede de VM.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="select-a-protection-goal"></a>Selecionar uma meta de proteção

Selecione o que será replicado e o local em que será replicado.

1. No cofre, clique em **Site Recovery** > **Preparar Infraestrutura** > **Meta de proteção**.
2. Em **Objetivo de proteção**, selecione **Para o Azure**> **Sim, com o Hyper-V**. 
    - Se os hosts Hyper-V não forem gerenciados pelo VMM, selecione **Não** para confirmar que você não está usando o VMM.
    - Se os hosts forem gerenciados em nuvens do VMM, selecione **Sim**.

## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Ao configurar o ambiente de origem, você instala o provedor do Azure Site Recovery e o agente do Serviços de Recuperação do Azure, e registra servidores locais no cofre. 

1. Em **Preparar a infraestrutura**, clique em **Origem**. 
    - Se você não estiver usando o VMM, clique em **+Site do Hyper-V** e especifique um nome de site. Em seguida, clique em **+Servidor do Hyper-V** e adicione um host ou cluster para o site.
    - Se você estiver usando o VMM, em **Preparar origem**, clique em **+ VMM** para adicionar um servidor VMM. Em **Adicionar Servidor**, verifique se o **Servidor do System Center VMM** aparece em **Tipo de servidor**.
2. Baixe os componentes do provedor e do agente, dependendo do seu ambiente.
    - Somente para o Hyper-V, baixe o arquivo de instalação do Provedor. Execute o arquivo em cada host Hyper-V para instalar o provedor e o agente.
        
        ![Provedor sem VMM](./media/tutorial-hyper-v-to-azure/download-no-vmm.png)
    
    - Para o Hyper-V com o VMM, baixe o provedor e o agente separadamente. Execute a instalação do provedor no servidor do VMM. Execute a instalação do agente em cada host do Hyper-V.
    
        ![Provedor e agente com o VMM](./media/tutorial-hyper-v-to-azure/download-vmm.png)
    
3. Baixe a chave do registro do cofre. Você precisará dela quando executar a instalação do Provedor. A chave é válida por cinco dias após ser gerada.

### <a name="install-components"></a>Instalar componentes

Instale os componentes como resumido na tabela. 

**Componente** | **Detalhes** | **Somente o Hyper-V** | **Hyper-V com o VMM**
--- | --- | --- | ---
**Provedor do Azure Site Recovery** | Coordena a replicação para o Azure | Instalar em cada host do Hyper-V | Instalar no servidor do VMM
**Agente dos Serviços de Recuperação** | Trata da replicação de dados | Instalar em cada host do Hyper-V | Instalar no host do Hyper-V


#### <a name="install-the-provider-on-hyper-v-without-vmm"></a>Instalar o provedor no Hyper-V sem o VMM

Instale o Provedor em cada host do Hyper-V que você adicionou ao site do Hyper-V. Se você estiver instalando em um cluster do Hyper-V, execute a instalação em cada nó de cluster. Instalar e registrar cada nó de cluster do Hyper-V garante que as VMs estarão protegidas, mesmo se migrarem entre nós.

1. No **Microsoft Update**, você pode aceitar as atualizações para que as atualizações do Provedor sejam instaladas de acordo com a política do Microsoft Update.
2. Em **Instalação**, aceite ou modifique o local de instalação padrão do Provedor e clique em **Instalar**.
4. Em **Configurações do Cofre**, clique em **Procurar** para selecionar o arquivo da chave do cofre baixado. Especifique a assinatura do Azure Site Recovery, o nome do cofre e o site de Hyper-V ao qual pertence o servidor Hyper-V.
5. Em **Configurações de Proxy**, especifique como o Provedor em execução no servidor da VMM ou no host do Hyper-V se conecta ao Site Recovery pela Internet.
    * Para obter uma conexão direta, selecione **Conectar diretamente o Azure Site Recovery sem um proxy**.
    * Para obter um proxy, selecione **Conectar-se ao Azure Site Recovery usando um servidor proxy**. Especifique o endereço de proxy, a porta e as credenciais, se necessário.
6. Depois que o servidor foi registrado no cofre, clique em **Concluir**.

Os metadados do servidor Hyper-V são recuperados pelo Azure Site Recovery e o servidor é exibido em **Infraestrutura do Site Recovery** > **Hosts Hyper-V**.


#### <a name="install-the-recovery-services-agent-on-hyper-v-host-without-vmm"></a>Instalar o agente dos Serviços de Recuperação no host do Hyper-V sem VMM

Se você estiver usando o VMM na sua implantação, execute a instalação do agente dos Serviços de Recuperação em cada host do Hyper-V.

1. No Assistente de Instalação > **Verificação de pré-requisitos**, clique em **Avançar**. Quaisquer pré-requisitos faltantes serão instalados automaticamente.

    ![Agente de Serviços de Recuperação de Pré-requisitos](./media/tutorial-hyper-v-to-azure/hyperv-agent-prerequisites.png)
3. Em **Configurações da Instalação**, aceite ou modifique o local de instalação e o local do cache. A unidade de cache precisa de pelo menos 5 GB de armazenamento. É recomendável uma unidade com 600 GB ou mais de espaço livre. Em seguida, clique em **Instalar**.
4. Em **Instalação**, quando a instalação for concluída, clique em **Fechar** para concluir o assistente.

##### <a name="set-up-internet-access-via-a-proxy"></a>Configurar o acesso à Internet por meio de um proxy

O agente dos Serviços de Recuperação no host do Hyper-V precisa de acesso à Internet para o Azure para a replicação de VMs. Se você estiver acessando a Internet por meio de um proxy, configure-o da seguinte maneira:

1. Abra o snap-in MMC do Backup do Microsoft Azure no host do Hyper-V. Por padrão, um atalho para o Backup do Microsoft Azure está disponível na área de trabalho ou C:\Arquivos de Programas\Agente dos Serviços de Recuperação do Microsoft Azure\bin\wabadmin.
2. No snap-in, clique em **Alterar Propriedades**.
3. Na guia **Configuração do Proxy**, especifique as informações do proxy.

    ![Registrar o Agente MARS](./media/tutorial-hyper-v-to-azure/mars-proxy.png)
4. Verifique se o agente pode alcançar as [URLs exigidas](#prepare-hyper-v-hosts).

#### <a name="install-the-provider-on-the-vmm-server-hyper-v-with-vmm"></a>Instale o provedor no servidor do VMM (Hyper-V com o VMM)

1. No **Microsoft Update**, você pode aceitar as atualizações para que as atualizações do Provedor sejam instaladas de acordo com a política do Microsoft Update.
2. Em **Instalação**, aceite ou modifique o local de instalação padrão do Provedor e clique em **Instalar**. 
3. Agora, registre o servidor VMM no cofre. Em **Configurações do Cofre**, clique em **Procurar** para selecionar o arquivo da chave do cofre baixado. Especifique a assinatura do Azure Site Recovery e o nome do cofre.

    ![Registros do servidor](./media/tutorial-hyper-v-to-azure/provider-vault-settings.png)

4. Em **Configurações de Proxy**, especifique como o Provedor em execução no servidor da VMM ou no host do Hyper-V se conecta ao Site Recovery pela Internet.

    * Para obter uma conexão direta, selecione **Conectar diretamente o Azure Site Recovery sem um proxy**.
    * Para obter um proxy, selecione **Conectar-se ao Azure Site Recovery usando um servidor proxy**. Especifique o endereço de proxy, a porta e as credenciais, se necessário.
    - Uma conta RunAs do VMM (DRAProxyAccount) será criada automaticamente usando as credenciais de proxy especificadas. Configure o servidor proxy para que essa conta possa ser autenticada com êxito. As configurações de conta executar como podem ser modificadas no console do VMM > **configurações** > **segurança** > **contas executar como**. Reinicie o serviço VMM para atualizar as alterações.
5. Em **Criptografia de dados**, especifique se deseja criptografar todos os dados enviados para o Azure. Se você selecionar essa opção, o Site Recovery emitirá um certificado. Você precisará desse certificado para descriptografar os dados mais tarde.
6. No **Servidor de VMM**, especifique um nome amigável para identificar o servidor de VMM no cofre. Em uma configuração de cluster, especifique o nome de função de cluster do VMM. Em **Sincronizar metadados da nuvem com o cofre**, selecione se você deseja sincronizar os metadados para todas as nuvens no servidor VMM com o cofre. Esta ação só precisa acontecer uma vez em cada servidor. Se você não desejar sincronizar todas as nuvens, deixe essa configuração desmarcada e sincronize cada nuvem individualmente nas propriedades da nuvem no console do VMM.

Após a conclusão do registro, os metadados do servidor são recuperados pelo Azure Site Recovery e o servidor VMM é exibido em **Infraestrutura do Site Recovery**.






## <a name="set-up-the-target-environment"></a>Configurar o ambiente de origem

Selecione e verifique os recursos de destino. 

1. Clique em **Preparar a Infraestrutura** > **Destino**.
2. Selecione a assinatura e o grupo de recursos em que as VMs do Azure serão criadas após o failover. Escolha o modelo de implantação que você deseja usar no Azure para as VMs.

3. A Recuperação de Site verifica se você tem uma ou mais contas de armazenamento e redes do Azure compatíveis.

## <a name="configure-network-mapping-with-vmm"></a>Configurar o mapeamento de rede (com VMM)

Se você estiver usando o VMM, configure o mapeamento de rede.

1. Em **Infraestrutura do Site Recovery** > **Mapeamentos de rede** > **Mapeamento de Rede**, clique no ícone **+Mapeamento de Rede**.
2. Em **Adicionar mapeamento de rede**, selecione o servidor do VMM de origem. Selecione **Azure** como o destino.
3. Verifique a assinatura e o modelo de implantação após o failover.
4. Em **Rede de origem**, selecione a rede de origem da VM local.
5. Em **Rede de destino**, selecione a rede do Azure na qual as VMs do Azure de réplica estarão localizadas quando criadas após o failover. Em seguida, clique em **OK**.

    ![Mapeamento de rede](./media/tutorial-hyper-v-to-azure/network-mapping-vmm.png)

## <a name="create-a-replication-policy"></a>Criar uma política de replicação

1. Clique em **Preparar a Infraestrutura** > **Configurações de Replicação** > **+Criar e associar**.
2. Em **Criar e associar política**, especifique um nome de política.
3. Em **Frequência de cópia**, especifique com que frequência você deseja replicar os dados delta após a replicação inicial (a cada 30 segundos, 5 ou 15 minutos).

    > [!NOTE]
    >  Não há suporte para uma frequência de 30 segundos ao replicar para armazenamento premium. A limitação é determinada pelo número de instantâneos por blob (100) com suporte pelo armazenamento premium. [Saiba mais](../storage/common/storage-premium-storage.md#snapshots-and-copy-blob).

4. Em **Retenção do ponto de recuperação**, especifique qual será a duração da janela de retenção (em horas) para cada ponto de recuperação. Os computadores protegidos podem ser recuperados para qualquer ponto nessa janela.
5. Em **Frequência do instantâneo consistente com aplicativo**, especifique com que frequência (1 a 12 horas) são criados os pontos de recuperação que incluam instantâneos consistentes com aplicativos. O Hyper-V usa dois tipos de instantâneos:
    - **Instantâneo padrão**: fornece um instantâneo incremental da máquina virtual inteira.
    - **Instantâneo consistente com o aplicativo**: cria um instantâneo pontual dos dados do aplicativo dentro da VM. O VSS (Serviço de Cópias de Sombra de Volume) garante que os aplicativos estejam em um estado consistente quando o instantâneo é criado. A habilitação de instantâneos consistentes com aplicativos afeta o desempenho do aplicativo nas VMs de origem. Defina um valor que seja menor do que o número de pontos de recuperação adicionais configurados.
6. Em **Hora de início para replicação inicial**, indique quando a replicação inicial deve começar. A replicação ocorre pela largura de banda da Internet, pois talvez você queira agendá-la fora dos horários de pico.
7. Em **Criptografar os dados armazenados no Azure**, especifique se os dados em repouso serão criptografados no armazenamento do Azure. Em seguida, clique em **OK**.

    ![Política de replicação](./media/tutorial-hyper-v-to-azure/replication-policy.png)


Quando você cria uma nova política, ela é automaticamente associada à nuvem do VMM ou ao site do Hyper-V.

## <a name="enable-replication"></a>Habilitar a replicação


1. Clique em **Replicar aplicativo** > **Origem**. 
2. Em **Origem**, selecione o site do Hyper-V/servidor do VMM/nuvem. Em seguida, clique em **OK**.
3. Em **Destino**, verifique o Azure como o destino, a assinatura do cofre e o modelo que você deseja usar no Azure após o failover.
4. Selecione a conta de armazenamento para os dados replicados e a rede do Azure na qual as VMs do Azure estarão localizadas após o failover.
5. Em **Máquinas Virtuais** > **Selecionar**, selecione as VMs que deseja replicar. Em seguida, clique em **OK**.

 É possível acompanhar o progresso da ação **Habilitar Proteção** em **Trabalhos** > **Trabalhos do Site Recovery**. Após a conclusão do trabalho **Finalizar Proteção**, a replicação inicial é concluída, e a máquina virtual estará pronta para failover.

## <a name="next-steps"></a>Próximas etapas
[Realizar uma simulação de recuperação de desastre](tutorial-dr-drill-azure.md)
