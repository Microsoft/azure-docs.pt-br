---
title: Tutorial sobre como configurar a recuperação de desastre de VMs do Azure com o Azure Site Recovery
description: Neste tutorial, você vai configurar a recuperação de desastre de VMs do Azure para outra região do Azure, usando o serviço do Site Recovery.
ms.topic: tutorial
ms.date: 11/03/2020
ms.custom: mvc
ms.openlocfilehash: 473a264ef497cab4bd4f88372600161b33178099
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97656862"
---
# <a name="tutorial-set-up-disaster-recovery-for-azure-vms"></a>Tutorial: Configurar a recuperação de desastre para VMs do Azure

Este tutorial mostrará como configurar a recuperação de desastre de VMs do Azure usando o [Azure Site Recovery](site-recovery-overview.md). Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Verificar configurações e permissões do Azure
> * Preparar as VMs que deseja replicar
> * Criar um cofre dos Serviços de Recuperação
> * Habilitar a replicação de VMs

Ao habilitar a replicação de uma VM para configurar a recuperação de desastre, a extensão do serviço Mobilidade do Site Recovery será instalada na VM e fará o registro dela usando o Azure Site Recovery. Durante a replicação, as gravações de disco da VM são enviadas para uma conta de armazenamento em cache na região de origem. Os dados são enviados de lá para a região de destino e os pontos de recuperação são gerados com base nos dados. Ao fazer failover de uma VM durante a recuperação de desastre, um ponto de recuperação será usado para restaurar a VM na região de destino.

> [!NOTE]
> Os tutoriais fornecerão instruções com as configurações padrão mais simples. Caso queira configurar a recuperação de desastre de VMs do Azure usando configurações personalizadas, examine [este artigo](azure-to-azure-how-to-enable-replication.md).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

- [Examine as regiões com suporte](azure-to-azure-support-matrix.md#region-support). É possível configurar a recuperação de desastre de VMs do Azure entre duas regiões na mesma localização.
- Será necessário obter uma ou mais VMs do Azure. Verifique se as VMs do [Windows](azure-to-azure-support-matrix.md#windows) ou do [Linux](azure-to-azure-support-matrix.md#replicated-machines---linux-file-systemguest-storage) têm suporte.
- Examine os requisitos de [computação](azure-to-azure-support-matrix.md#replicated-machines---compute-settings), [armazenamento](azure-to-azure-support-matrix.md#replicated-machines---storage) e [rede](azure-to-azure-support-matrix.md#replicated-machines---networking) das VMs.
- Este tutorial pressupõe que as VMs não são criptografadas. Caso queira configurar a recuperação de desastre de VMs criptografadas, [confira este artigo](azure-to-azure-how-to-enable-replication-ade-vms.md).

## <a name="check-azure-settings"></a>Verificar as configurações do Azure

Verifique permissões e configurações da região de destino.

### <a name="check-permissions"></a>Verificar permissões

Sua conta do Azure precisará de permissões para criar um cofre dos Serviços de Recuperação e VMs na região de destino.

- Caso já tenha criado uma assinatura gratuita do Azure, você é o administrador da conta e nenhuma ação adicional será necessária.
- Caso contrário, trabalhe com o administrador para obter as permissões necessárias.
    - **Criar um cofre**: ter permissões de administrador ou proprietário da assinatura. 
    - **Gerenciar operações do Site Recovery no cofre**: ter a função interna de *Colaborador do Site Recovery* do Azure.
    - **Criar VMs do Azure na região de destino**: ter a função interna de *Colaborador da Máquina Virtual* ou permissões específicas para:
        - Criar uma VM na rede virtual selecionada.
        - Gravar em uma conta de armazenamento do Azure.
        - Gravar em um disco gerenciado do Azure.

### <a name="verify-target-settings"></a>Verificar as configurações de destino

Durante a recuperação de desastre, quando você faz failover da região de origem, as VMs são criadas na região de destino. 

Verifique se a assinatura tem recursos suficientes na região de destino. Você deverá ser capaz de criar VMs com tamanhos que correspondam às VMs da região de origem. Ao configurar a recuperação de desastres, o Site Recovery escolherá o mesmo tamanho (ou o tamanho mais próximo possível) para a VM de destino.


## <a name="prepare-vms"></a>Como preparar as VMs

Verifique se as VMs têm conectividade de saída e os certificados raiz mais recentes. 


### <a name="set-up-vm-connectivity"></a>Configurar a conectividade de uma VM

As VMs que você deseja replicar precisam de uma conectividade de rede de saída.

> [!NOTE]
> O Site Recovery não dá suporte ao uso de um proxy de autenticação para controlar a conectividade de rede.

#### <a name="outbound-connectivity-for-urls"></a>Conectividade de saída para URLs

Caso esteja usando um proxy de firewall baseado em URL para controlar a conectividade de saída, permita acesso a estas URLs:

| **Nome**                  | **Comercial**                               | **Governo**                                 | **Descrição** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Armazenamento                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net` | Permite que os dados sejam gravados da VM para a conta de armazenamento de cache da região de origem. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Fornece autorização e autenticação para as URLs do serviço Site Recovery. |
| Replicação               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Permite que a VM se comunique com o serviço Site Recovery. |
| Barramento de Serviço               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Permite que a VM grave o monitoramento do Site Recovery e os dados de diagnóstico. |

#### <a name="outbound-connectivity-for-ip-address-ranges"></a>Conectividade de saída para intervalos de endereços IP

Caso esteja usando NSGs (grupos de segurança de rede) para controlar a conectividade, crie regras de NSG baseadas em marcas de serviço que permitam a saída de HTTPS pela porta 443 dessas [marcas de serviço](../virtual-network/service-tags-overview.md#available-service-tags)(grupos de endereços IP):

**Tag** | **Permitir** 
--- | ---
Tag Storage  |Permite que os dados sejam gravados da VM para a conta de armazenamento em cache.   
Tag Azure AD | Permite o acesso a todos os endereços IP que correspondem ao Azure AD.   
Tag EventsHub | Permite o acesso ao monitoramento do Site Recovery.  
Tag AzureSiteRecovery | Permite o acesso ao serviço Site Recovery em qualquer região.   
Marca do GuestAndHybridManagement | Use para atualizar automaticamente o agente Mobilidade do Site Recovery em execução nas VMs habilitadas para replicação.

[Saiba mais](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) sobre marcas obrigatórias e exemplos de marcação.

### <a name="verify-vm-certificates"></a>Verificar os certificados de VM

Verifique se as VMs têm os certificados raiz mais recentes. Caso contrário, a VM não poderá ser registrada usando o Site Recovery devido a restrições de segurança.

- **VMs do Windows**: instale as atualizações do Windows mais recentes na VM para que todos os certificados raiz confiáveis estejam no computador. Em um ambiente desconectado, siga os processos padrão do Windows Update e as atualizações de certificados.
- **VMs Linux**: siga as diretrizes fornecidas pelo distribuidor do Linux para obter os certificados raiz confiáveis mais recentes e a CRL (lista de certificados revogados).

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

Crie um cofre dos Serviços de Recuperação em qualquer região, exceto na região de origem da qual você deseja replicar as VMs.

1. Entre no [portal do Azure](https://portal.azure.com).
2. Na caixa de pesquisa, digite *recuperação*. Em **Serviços**, selecione **Cofres dos Serviços de Recuperação**.

    ![Pesquisar cofres dos Serviços de Recuperação](./media/azure-to-azure-tutorial-enable-replication/search.png)

3. Em **Cofres dos Serviços de Recuperação**, clique em **Adicionar**.
4. Em **Criar um Cofre dos Serviços de Recuperação** > **Noções básicas**, selecione a assinatura na qual você deseja criar o cofre.
5. Em **Grupo de recursos**, selecione um grupo de recursos existente para o cofre ou crie um.
6. Em **Nome do cofre**, especifique um nome amigável para identificar o cofre.
7. Em **Região**, selecione a região do Azure na qual o cofre será colocado. [Verifique as regiões com suporte](https://azure.microsoft.com/pricing/details/site-recovery/).
8. Selecione **Examinar + criar**.

   ![Configurações do cofre na página de criação de cofres](./media/azure-to-azure-tutorial-enable-replication/vault-basics.png)

9. Em **Examinar + criar**, clique em **Criar**.

10. A implantação do cofre começará. Siga o progresso por meio de notificações.
11. Após a implantação do cofre, selecione **Fixar no painel** com o objetivo de salvá-lo para obter uma referência rápida. Clique em **Ir para o recurso** para abrir o novo cofre. 
    
    ![Botões para abrir o cofre após a implantação e fixá-lo no painel](./media/azure-to-azure-tutorial-enable-replication/vault-deploy.png)

### <a name="enable-site-recovery"></a>Habilitar o Site Recovery

Nas configurações do cofre, selecione **Habilitar o Site Recovery**.

![Seleção para habilitar o Site Recovery no cofre](./media/azure-to-azure-tutorial-enable-replication/enable-site-recovery.png)

## <a name="enable-replication"></a>Habilitar a replicação

Selecione as configurações de origem e habilite a replicação de VMs. 

### <a name="select-source-settings"></a>Selecionar configurações de origem

1. No cofre > na página do **Site Recovery**, em **Máquinas virtuais do Azure**, selecione **Habilitar a replicação**.

    ![Seleção para habilitar a replicação de VMs do Azure](./media/azure-to-azure-tutorial-enable-replication/enable-replication.png)

2. Em **Origem**> **Local de origem**, selecione a região do Azure de origem na qual as VMs estão em execução atualmente.
3. Em **modelo de implantação da máquina virtual do Azure**, deixe a configuração padrão do **Resource Manager**.
4. Em **Assinatura de origem**, selecione a assinatura na qual as VMs estão em execução. É possível selecionar qualquer assinatura que esteja no mesmo locatário do Azure AD (Active Directory) que o cofre.
5. Em **Grupo de recursos de origem**, selecione o grupo de recursos que contém as VMs.
6. Em **Recuperação de desastre entre zonas de disponibilidade**, deixe a configuração padrão **Não**.

     ![Configurar origem](./media/azure-to-azure-tutorial-enable-replication/source.png)

7. Selecione **Avançar**.

### <a name="select-the-vms"></a>Selecione as VMs

O Site Recovery vai recuperar VMs associadas às assinaturas ou ao grupo de recursos selecionado.

1. Em **Máquinas Virtuais**, selecione as VMs que você deseja habilitar para a recuperação de desastre.

     ![Página de seleção de VMs para replicação](./media/azure-to-azure-tutorial-enable-replication/select-vm.png)

2. Selecione **Avançar**.

### <a name="review-replication-settings"></a>Examinar as configurações de replicação

1. Em **Configurações de replicação**, examine as configurações. O Site Recovery criará configurações/políticas padrão para a região de destino. Para fins deste tutorial, usaremos as configurações padrão.
2. Selecione **Habilitar replicação**.

    ![Página para personalizar configurações e habilitar a replicação](./media/azure-to-azure-tutorial-enable-replication/enable-vm-replication.png)   

3. Acompanhe o progresso da replicação por meio de notificações.

     ![Acompanhar o progresso por meio de notificações](./media/azure-to-azure-tutorial-enable-replication/notification.png) ![Rastrear a notificação de uma replicação bem-sucedida](./media/azure-to-azure-tutorial-enable-replication/notification-success.png)

4. As VMs habilitadas aparecerão no cofre > na página **Itens replicados**.

    ![VM na página de Itens Replicados](./media/azure-to-azure-tutorial-enable-replication/replicated-items.png)


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você habilitou a recuperação de desastre de uma VM do Azure. Agora, execute uma análise para verificar se o failover está funcionando conforme o esperado.

> [!div class="nextstepaction"]
> [Realizar uma simulação de recuperação de desastre](azure-to-azure-tutorial-dr-drill.md)
