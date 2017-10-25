---
title: "Configurar a recuperação de desastre para VMs do Hyper-V entre os sites locais com o Azure Site Recovery | Microsoft Docs"
description: "Saiba como configurar a recuperação de desastre para VMs do Hyper-V entre os sites locais com o Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 65eda71c-3ca3-41bc-b02d-00fecc1557d7
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: raynew
ms.openlocfilehash: 9764e48e04eb0c83afea09934ce64d2485ab15d5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-disaster-recovery-for-hyper-v-vms-to-your-secondary-on-premises-site"></a>Configurar a recuperação de desastre de VMs do Hyper-V para seu site local secundário

O serviço [Azure Site Recovery](site-recovery-overview.md) contribui para sua estratégia de recuperação de desastre ao gerenciar e orquestrar a replicação, o failover e o failback de computadores locais e de VMs (máquinas virtuais) do Azure.

Este tutorial mostra como configurar a recuperação de desastre para um site secundário, para VMs do Hyper-V locais gerenciadas nas nuvens do System Center VMM (Virtual Machine Manager). Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Preparar servidores do VMM locais e hosts do Hyper-V
> * Criar um cofre de Serviços de Recuperação para o Site Recovery 
> * Configurar os ambientes de replicação de origem e destino. 
> * Configurar o mapeamento de rede (se o Hyper-V for gerenciado pelo System Center VMM)
> * Criar uma política de replicação
> * Habilitar a replicação para uma VM

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- Examine os [componentes e a arquitetura do cenário](concepts-hyper-v-to-secondary-architecture.md).
- Examine os [requisitos de suporte](site-recovery-support-matrix-to-sec-site.md) de todos os componentes.
- Verifique se os servidores do VMM e os hosts do Hyper-V estão em conformidade com [os requisitos de suporte](site-recovery-support-matrix-to-sec-site.md#on-premises-servers).
- Verifique se as VMs que você deseja replicar estão em conformidade com o [suporte ao computador replicado](site-recovery-support-matrix-to-sec-site.md#support-for-replicated-machine-os-versions).
- Prepare os servidores do VMM para o mapeamento de rede.

### <a name="prepare-for-network-mapping"></a>Prepare-se para o mapeamento de rede

O [mapeamento de rede](site-recovery-network-mapping.md) mapeia entre as redes de VM do VMM locais de origem e as nuvens de destino. Mapeamento faz o seguinte:

- Conecta as VMs às redes de VM de destino apropriadas depois do failover. 
- Quando possível, coloca VMs de réplica nos servidores host do Hyper-V de destino. 
- Se você não configurar o mapeamento de rede, as VMs de réplica não serão conectadas a uma rede de VM após o failover.

Prepare o VMM da seguinte maneira:

1. Verifique se você tem [redes lógicas do VMM](https://docs.microsoft.com/system-center/vmm/network-logical) na origem e servidores do VMM de destino.
    - A rede lógica no servidor de origem deve ser associada à nuvem de origem na qual os hosts do Hyper-V estão localizados.
    - A rede lógica no servidor de destino deve ser associada com a nuvem de destino.
1. Verifique se você tem [Redes de VM](https://docs.microsoft.com/system-center/vmm/network-virtual) nos servidores do VMM de origem e destino. As redes de VM devem ser vinculadas à rede lógica em cada local.
2. Conecte as VMs nos hosts do Hyper-V de origem à rede de VM de origem. 


## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="choose-a-protection-goal"></a>Escolher um objetivo de proteção

Selecione o que você deseja replicar e para onde deseja replicar.

1. Clique em **Site Recovery** > **Etapa 1: Preparar a Infraestrutura** > **Meta de proteção**.
2. Selecione **Para site de recuperação** e selecione **Sim, com o Hyper-V**.
3. Selecione **Sim** para indicar que você está usando o VMM para gerenciar os hosts do Hyper-V.
4. Selecione **Sim** se você tiver um servidor VMM secundário. Se você estiver implantando a replicação entre nuvens em um único servidor VMM, clique em **Não**. Em seguida, clique em **OK**.


## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Instale o Provedor do Azure Site Recovery nos servidores VMM e descubra e registre os servidores no cofre.

1. Clique em **Preparar a Infraestrutura** > **Origem**.
2. Em **Preparar origem**, clique em **+ VMM** para adicionar um servidor VMM.
3. Em **Adicionar Servidor**, verifique se o **Servidor do System Center VMM** aparece em **Tipo de servidor**.
4. Baixe o arquivo de instalação do Provedor do Azure Site Recovery.
5. Baixe a chave do registro. Ela é necessária para instalar o provedor. A chave é válida por cinco dias após ser gerada.

    ![Configurar origem](./media/tutorial-vmm-to-vmm/source-settings.png)

6. Instale o provedor em cada servidor VMM. Você não precisa instalar nada explicitamente nos hosts do Hyper-V.

### <a name="install-the-azure-site-recovery-provider"></a>Instalar o Provedor de Recuperação do Site do Azure

1. Execute o arquivo de configuração do Provedor em cada servidor VMM. Se o VMM for implantado em um cluster, instale os seguintes itens pela primeira vez:
    -  Instale o provedor em um nó ativo e conclua a instalação para registrar o servidor VMM no cofre.
    - Em seguida, instale o Provedor nos outros nós. Nós de cluster devem todos executar a mesma versão do provedor.
2. A instalação executa algumas verificações de pré-requisito e solicita permissão para interromper o serviço do VMM. O serviço VMM será reiniciado automaticamente quando a instalação for finalizada. Se estiver instalando em um cluster do VMM, será solicitado que você pare a função de Cluster.
3. No **Microsoft Update**, você pode aceitar que as atualizações do Provedor sejam instaladas de acordo com a política do Microsoft Update.
4. Em **Instalação**, aceite ou modifique o local de instalação padrão e clique em **Instalar**.
5. Quando a instalação for concluída, clique em **Registrar** para registrar o servidor no cofre.

    ![Local de instalação](./media/tutorial-vmm-to-vmm/provider-register.png)
6. Em **Nome do cofre**, verifique o nome do cofre para o qual o servidor será registrado. Clique em **Avançar**.
7. Em **Conexão de Proxy**, especifique como o provedor em execução no servidor VMM se conecta ao Azure.
   - Você pode especificar que o provedor deve se conectar diretamente à internet ou por meio de um proxy. Especifique as configurações de proxy conforme o necessário.
   - Se você usar um proxy, uma conta RunAs do VMM (DRAProxyAccount) será criada automaticamente usando as credenciais de proxy especificadas. Configure o servidor proxy para que essa conta possa ser autenticada com êxito. As configurações de conta executar como podem ser modificadas no console do VMM > **configurações** > **segurança** > **contas executar como**.
   - Reinicie o serviço VMM para atualizar as alterações.
8. Em **Chave de Registro**, selecione a chave que você baixou e copiou para o servidor VMM.
9. A configuração de criptografia não é relevante nesse cenário. 
10. Em **Nome do servidor**, especifique um nome amigável para identificar o servidor VMM no cofre. Em um cluster, especifique o nome da função do cluster do VMM.
11. Em **Sincronizar metadados de nuvem**, selecione se deseja sincronizar os metadados de todas as nuvens no servidor VMM. Esta ação só precisa acontecer uma vez em cada servidor. Se você não quiser sincronizar todas as nuvens, deixe essa configuração desmarcada. Você pode sincronizar cada nuvem individualmente nas propriedades de nuvem no console do VMM.
12. Clique em **Avançar** para concluir o processo. Após o registro, o Site Recovery recupera os metadados do servidor VMM. O servidor é exibido em **Servidores** > **Servidores VMM** no cofre.
13. Depois que o servidor aparece no cofre, em **Origem** > **Preparar origem** selecione o servidor VMM e selecione a nuvem na qual o host do Hyper-V está localizado. Em seguida, clique em **OK**.


## <a name="set-up-the-target-environment"></a>Configurar o ambiente de origem

Selecione o servidor VMM e a nuvem de destino:

1. Clique em **Preparar infraestrutura** > **Destino** e selecione o servidor VMM de destino.
2. As nuvens do VMM que são sincronizados com o Site Recovery são exibidas. Selecione a nuvem de destino.

   ![Destino](./media/tutorial-vmm-to-vmm/target-vmm.png)


## <a name="set-up-a-replication-policy"></a>Configurar uma política de replicação

Antes de começar, verifique se todos os hosts que estão usando a política têm o mesmo sistema operacional. Se os hosts estiverem executando versões diferentes do Windows Server, você precisará de várias políticas de replicação.

1. Para criar uma nova política de replicação, clique em **Preparar a Infraestrutura** > **Configurações de Replicação** > **+Criar e associar**.
2. Em **Criar e associar política**, especifique um nome de política. Os tipos de origem e de destino devem ser **Hyper-V**.
3. Em **Versão do host do Hyper-V**, selecione qual sistema operacional está em execução no host.
4. Em **Tipo de autenticação** e **Porta de autenticação**, especifique como o tráfego é autenticado entre os servidores host Hyper-V primário e de recuperação.
    - Selecione **Certificado** , a menos que você tenha um ambiente Kerberos em funcionamento. A Recuperação de Site do Azure configurará automaticamente os certificados para autenticação HTTPS. Você não precisa fazer nada manualmente.
    - Por padrão, as portas 8083 e 8084 (para certificados) serão abertas no Firewall do Windows nos servidores host Hyper-V.
    - Se você selecionar **Kerberos**, um tíquete Kerberos será usado para autenticação mútua dos servidores host. O Kerberos só é relevante para servidores host do Hyper-V em execução no Windows Server 2012 R2 ou posterior.
1. Em **Frequência de cópia**, especifique com que frequência você deseja replicar os dados delta após a replicação inicial (a cada 30 segundos, 5 ou 15 minutos).
2. Em **Retenção de ponto de recuperação**, especifique quanto tempo (em horas) terá o período de retenção de cada ponto de recuperação. As máquinas replicadas podem ser recuperadas para qualquer ponto dentro de um período.
3. Em **Frequência do instantâneo consistente com aplicativo**, especifique com que frequência (1 a 12 horas) são criados os pontos de recuperação que contêm instantâneos consistentes com o aplicativo. O Hyper-V usa dois tipos de instantâneos:
    - **Instantâneo padrão**: fornece um instantâneo incremental da máquina virtual inteira.
    - **Instantâneo consistente com o aplicativo**: cria um instantâneo pontual dos dados do aplicativo dentro da VM. O VSS (Serviço de Cópias de Sombra de Volume) garante que os aplicativos estejam em um estado consistente quando o instantâneo é criado. Habilitar instantâneos consistentes com aplicativos afeta o desempenho do aplicativo em VMs de origem. Defina um valor que seja menor do que o número de pontos de recuperação adicionais configurados.
4. Em **Compactação de transferência de dados**, especifique se os dados de replicação transferidos devem ser compactados.
5. Selecione **Excluir VM de réplica** para especificar que a máquina virtual de réplica deverá ser excluída se você desabilitar a proteção para a VM de origem. Se você habilitar essa configuração, quando você desabilitar a proteção para a VM de origem, ela será removida do console de Recuperação de Site, as configurações de Recuperação de Site para o VMM serão removidas do console do VMM e a réplica será excluída.
6. Em **Método de replicação inicial**, se você estiver replicando na rede, especifique se deseja iniciar a replicação inicial ou agendá-la. Para economizar largura de banda de rede, talvez você deseje agendá-la fora de seus horários mais ocupados. Em seguida, clique em **OK**.

     ![Política de replicação](./media/tutorial-vmm-to-vmm/replication-policy.png)
     
7. A nova política é associada automaticamente à nuvem do VMM. Em **Política de replicação**, clique em **OK**. 


## <a name="enable-replication"></a>Habilitar a replicação

1. Clique em **Replicar aplicativo** > **Origem**. 
2. Em **Origem**, selecione o servidor VMM e a nuvem na qual os hosts Hyper-V que você deseja replicar estão localizados. Em seguida, clique em **OK**.
3. Em **Destino**, verifique o servidor VMM secundário e a nuvem.
4. Em **Máquinas virtuais**, selecione as VMs que você deseja proteger na lista.


É possível acompanhar o progresso da ação **Habilitar Proteção** em **Trabalhos** > **Trabalhos do Site Recovery**. Depois que o trabalho **Finalizar Proteção** for concluído, a replicação inicial será concluída e a VM estará pronta para failover.

## <a name="next-steps"></a>Próximas etapas

[Realizar uma simulação de recuperação de desastre](tutorial-dr-drill-secondary.md)
