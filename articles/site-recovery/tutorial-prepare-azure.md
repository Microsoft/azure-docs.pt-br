---
title: Preparar o Azure para a recuperação de desastre local com o Azure Site Recovery
description: Saiba como preparar o Azure para a recuperação de desastre de computadores locais usando o Azure Site Recovery.
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/09/2019
ms.custom: MVC
ms.openlocfilehash: 340b60cccbabeef404f0de13d493e171a5132331
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579263"
---
# <a name="prepare-azure-for-on-premises-disaster-recovery-to-azure"></a>Preparar o Azure para a recuperação de desastre local no Azure

Este artigo descreve como preparar componentes e recursos do Azure para que você possa configurar a recuperação de desastre de VMs VMware locais, VMs do Hyper-V ou servidores físicos do Windows/Linux para o Azure usando o serviço [Azure Site Recovery](site-recovery-overview.md).

Este artigo é o primeiro tutorial em uma série que mostra como configurar a recuperação de desastres para VMs locais. 


Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Verifique se a conta do Azure tem permissões de replicação.
> * Crie um cofre dos Serviços de Recuperação. Um cofre contém metadados e informações de configuração para máquinas virtuais e outros componentes de replicação.
> * Configurar uma (VNet) rede virtual do Azure. Quando as VMs do Azure são criadas após o failover, elas são ingressadas nessa rede.

> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário. Eles usam opções padrão quando possível e não mostram todas as possíveis configurações e caminhos. Para obter instruções detalhadas, leia o artigo na seção Instruções do Sumário do Site Recovery.

## <a name="before-you-start"></a>Antes de começar

- Examinar a arquitetura para recuperação de desastre de [VMware](vmware-azure-architecture.md), [Hyper-V](hyper-v-azure-architecture.md) e [servidor físico](physical-azure-architecture.md).
- Leia as perguntas comuns sobre [VMware](vmware-azure-common-questions.md) e [Hyper-V](hyper-v-azure-common-questions.md)

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar. Em seguida, entre no [portal do Azure](https://portal.azure.com).


## <a name="verify-account-permissions"></a>Verificar permissões de conta

Se você acabou de criar sua conta gratuita do Azure, você é o administrador da sua assinatura e tem as permissões necessárias. Se você não for o administrador da assinatura, peça para o administrador atribuir as permissões necessárias. Para habilitar a replicação para uma nova máquina virtual, você deve ter permissão para:

- Criar uma VM no grupo de recursos selecionado.
- Criar uma VM na rede virtual selecionada.
- Gravar em uma conta de armazenamento do Azure.
- Gravar em um disco gerenciado do Azure.

Para concluir essas tarefas, sua conta deve receber a função interna de Colaborador de Máquina Virtual. Além disso, para gerenciar as operações do Site Recovery em um cofre, sua conta deve receber a função interna de Colaborador do Site Recovery.


## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

1. No portal do Azure, selecione **Criar um recurso** e pesquise no Marketplace por **Recuperação**.
2. Selecione **Backup e Site Recovery** e, nos resultados da pesquisa e na página de Backup e Site Recovery, clique em **Criar**. 
3. Na página **Criar cofre dos Serviços de Recuperação**, selecione a **Assinatura**. Estamos usando uma **assinatura da Contoso**.
4. No **Grupo de recursos**, selecione um grupo de recursos existente ou crie um. Para este tutorial, estamos usando **contosoRG**.
5. Em **Nome do cofre**, digite um nome amigável para identificar o cofre. Para este conjunto de tutoriais estamos usando **ContosoVMVault**.
6. Em **Região**, selecione a região na qual o cofre deve estar localizado. Estamos usando **Europa Ocidental**.
7. Selecione **Examinar + criar**.

   ![Captura de tela da página Criar cofre dos Serviços de Recuperação.](./media/tutorial-prepare-azure/new-vault-settings.png)

   O novo cofre estará listado agora no **Painel** > **Todos os recursos** e na página **Cofres de Serviços de Recuperação** principal.

## <a name="set-up-an-azure-network"></a>Configure uma rede do Azure

Os computadores locais são replicados para discos gerenciados do Azure. Quando ocorre failover, VMs do Azure são criadas com base nesses discos gerenciados e associadas à rede do Azure que você especifica neste procedimento.

1. No [portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Rede** > **Rede virtual**.
2. Mantenha **Resource Manager** selecionado como o modelo de implantação.
3. Em **Nome**, insira um nome de rede. O nome deve ser exclusivo dentro do grupo de recursos do Azure. Estamos usando **ContosoASRnet** neste tutorial.
4. Em **Espaço de endereço**, insira o intervalo de endereços da rede virtual na notação CDR. Estamos usando **10.1.0.0/24**.
5. Em **Assinatura**, selecione a assinatura na qual deseja criar a rede.
6. Especifique o **Grupo de recursos** no qual a rede será criada. Estamos usando o grupo de recursos existente **contosoRG**.
7. Em **Local**, selecione a mesma região em que o cofre dos Serviços de Recuperação foi criado. Em nosso tutorial, é **Europa Ocidental**. A rede deve estar na mesma região do que o cofre.
8. Em **Intervalo de endereços**, insira o intervalo para a rede. Estamos usando **10.1.0.0/24** e não uma sub-rede.
9. Estamos deixando as opções padrão de proteção contra DDoS básica, sem nenhum ponto de extremidade de serviço nem firewall na rede.
9. Selecione **Criar**.

   ![Captura de tela das opções em Criar rede virtual.](media/tutorial-prepare-azure/create-network.png)

A rede virtual leva alguns segundos para ser criada. Depois de ser criada, ela será exibida no painel do portal do Azure.




## <a name="next-steps"></a>Próximas etapas

- Para recuperação de desastre do VMware, [prepare a infraestrutura do VMware local](./vmware-azure-tutorial-prepare-on-premises.md).
- Para recuperação de desastre do Hyper-V, [prepare os servidores Hyper-V locais](hyper-v-prepare-on-premises-tutorial.md).
- Para recuperação de desastres do servidor físico, [configure o ambiente de origem e o servidor de configuração](physical-azure-disaster-recovery.md)
- [Saiba mais](../virtual-network/virtual-networks-overview.md) sobre as redes do Azure.
- [Saiba mais sobre](../virtual-machines/managed-disks-overview.md) discos gerenciados.
