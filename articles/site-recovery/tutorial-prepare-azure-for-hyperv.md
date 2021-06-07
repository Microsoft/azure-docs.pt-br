---
title: Preparar o Azure para a recuperação de desastre do Hyper-V com o Azure Site Recovery
description: Saiba como preparar o Azure para a recuperação de desastre de VMs locais do Hyper-V usando o Azure Site Recovery
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.custom: MVC
ms.openlocfilehash: ec1c41935d200ad220716940f898549d835510ba
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579285"
---
# <a name="prepare-azure-resources-for-hyper-v-disaster-recovery"></a>Preparar recursos do Azure para recuperação de desastre do Hyper-V

 O [Azure Site Recovery](site-recovery-overview.md) ajuda o BCDR (continuidade dos negócios e recuperação de desastres) mantendo os aplicativos de negócios em execução durante interrupções planejadas e não planejadas. O Site Recovery gerencia e orquestra a recuperação de desastre de máquinas locais e de VMs (máquinas virtuais) do Azure, incluindo replicação, failover e recuperação.

Este tutorial é o primeiro de uma série que descreve como configurar a recuperação de desastre para VMs locais do Hyper-V.

> [!NOTE]
> Desenvolvemos tutoriais para mostrar o caminho de implantação mais simples para um cenário. Esses tutoriais usam opções padrão quando possível e não mostram todas as possíveis configurações e caminhos. Para obter mais informações, confira a seção "Como" de cada cenário correspondente.

Este tutorial mostra como preparar componentes do Azure quando você deseja replicar VMs locais (Hyper-V) para o Azure. Você aprenderá a:

> [!div class="checklist"]
> * Verifique se sua conta do Azure tem permissões de replicação.
> * Crie uma conta de armazenamento do Azure, que armazena imagens de computadores replicados.
> * Crie um cofre dos Serviços de Recuperação, que armazena informações de configuração e metadados para VMs e outros componentes de replicação.
> * Configure uma rede do Azure. Quando as VMs do Azure são criadas após o failover, elas são ingressadas nessa rede.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="sign-in"></a>Entrar

Entre no [portal do Azure](https://portal.azure.com).

## <a name="verify-account-permissions"></a>Verificar permissões de conta

Se você acabou de criar uma conta gratuita do Azure, você é o administrador dessa assinatura. Se você não é o administrador, trabalhe com o administrador para atribuir as permissões necessárias. Para habilitar a replicação para uma nova máquina virtual, você deve ter permissão para:

- Criar uma VM no grupo de recursos selecionado.
- Criar uma VM na rede virtual selecionada.
- Gravar na conta de armazenamento selecionada.

Para concluir essas tarefas, sua conta deve receber a função interna Colaborador de Máquina Virtual. Para gerenciar as operações do Site Recovery em um cofre, sua conta deve receber a função interna Colaborador do Site Recovery.

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

As imagens de máquinas replicadas são mantidas no armazenamento do Azure. As VMs do Azure são criadas do armazenamento quando há failover do local para o Azure. A conta de armazenamento deve estar na mesma região do cofre de Serviços de Recuperação.

1. No menu do [portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Armazenamento** > **Conta de armazenamento – blob, arquivo, tabela, fila**.
2. Em **Criar conta de armazenamento**, insira um nome para a conta.  O nome escolhido precisa ser exclusivo no Azure, ter de 3 a 24 caracteres e apenas usar letras minúsculas e números. Neste tutorial, use **contosovmsacct1910171607**.
3. Em **Modelo de implantação**, selecione **Gerenciador de Recursos**.
4. Em **Tipo de conta**, selecione **Armazenamento (uso geral v1)** . Não selecione armazenamento de blobs.
5. Em **Replicação**, selecione o padrão **Armazenamento com redundância geográfica com acesso de leitura** para redundância de armazenamento.
6. Em **Desempenho**, selecione **Padrão**. Em seguida, em **Camada de acesso**, selecione a opção padrão **Frequente**.
7. Em **Assinatura**, escolha a assinatura na qual deseja criar a conta de armazenamento.
8. Em **Grupo de recursos**, digite um novo grupo de recursos. Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Neste tutorial, use **ContosoRG**.
9. Em **Localização**, escolha a localização geográfica para sua conta de armazenamento. Neste tutorial, use **Oeste da Europa**.
10. Selecione **Criar** para criar a conta de armazenamento.

   ![Captura de tela das opções em Criar uma conta de armazenamento.](media/tutorial-prepare-azure/create-storageacct.png)

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

1. No portal do Azure, selecione **+Criar um recurso** e, em seguida, pesquise Serviços de Recuperação no Azure Marketplace.
2. Selecione **Backup e Site Recovery (OMS)** . Em seguida, na página **Backup e Site Recovery**, selecione **Criar**.
1. Em **Cofre dos Serviços de Recuperação > Nome**, insira um nome amigável para identificar o cofre. Para este tutorial, use **ContosoVMVault**.
2. No **Grupo de recursos**, selecione um grupo de recursos existente ou crie um. Neste tutorial, use **contosoRG**.
3. Em **Localização**, selecione a região na qual o cofre deve estar localizado. Neste tutorial, use **Oeste da Europa**.
4. Para acessar rapidamente o cofre do painel, selecione **Fixar no painel** > **Criar**.

![Captura de tela da página Criar cofre dos Serviços de Recuperação.](./media/tutorial-prepare-azure/new-vault-settings.png)

O novo cofre é exibido em **Painel** > **Todos os recursos** e na página principal **Cofres dos Serviços de Recuperação**.

## <a name="set-up-an-azure-network"></a>Configure uma rede do Azure

Quando as VMs do Azure são criadas do armazenamento após o failover, elas são associadas a esta rede.

1. No [portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Rede** > **Rede virtual**. Mantenha o Resource Manager selecionado como o modelo de implantação.
2. Em **Nome**, insira um nome de rede. O nome deve ser exclusivo dentro do grupo de recursos do Azure. Neste tutorial, use **ContosoASRnet**.
3. Especifique o grupo de recursos no qual a rede será criada. Neste tutorial, use o grupo de recursos existente **contosoRG**.
4. Em **Intervalo de endereços**, insira **10.0.0.0/24** como o intervalo para a rede. Não há nenhuma sub-rede para essa rede.
5. Em **Assinatura**, selecione a assinatura na qual deseja criar a rede.
6. Em **Localização**, escolha **Oeste da Europa**. A rede deve estar na mesma região do que o cofre de Recuperação de Site.
7. Mantenha as opções padrão da proteção contra DDoS básica, sem nenhum ponto de extremidade de serviço na rede.
8. Selecione **Criar**.

![Captura de tela das opções em Criar rede virtual.](media/tutorial-prepare-azure/create-network.png)

A rede virtual leva alguns segundos para ser criada. Depois de ser criada, ela será exibida no painel do portal do Azure.

## <a name="useful-links"></a>Links úteis

Saiba mais sobre:
- [Redes do Azure](../virtual-network/virtual-networks-overview.md)
- [Discos gerenciados](../virtual-machines/managed-disks-overview.md)



## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Preparar a infraestrutura do Hyper-V local para recuperação de desastre para o Azure](hyper-v-prepare-on-premises-tutorial.md)
