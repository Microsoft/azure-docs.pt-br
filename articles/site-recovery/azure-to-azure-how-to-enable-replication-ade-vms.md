---
title: Configurar a replicação para VMs habilitadas para o Azure Disk Encryption no Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como configurar a replicação para VMs habilitadas para o Azure Disk Encryption, de uma região do Azure para outra usando o Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: 4943b730bb46ee00200d84faf95a7ccb069d3aa8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60790959"
---
# <a name="replicate-azure-disk-encryption-enabled-virtual-machines-to-another-azure-region"></a>Replicar máquinas virtuais habilitadas para criptografia de disco do Azure para outra região do Azure

Este artigo descreve como replicar VMs habilitadas para criptografia de disco do Azure de uma região do Azure para outro.

>[!NOTE]
>O Azure Site Recovery atualmente dá suporte a apenas as VMs do Azure que executam um sistema de operacional do Windows e que são [habilitado para criptografia com o Azure Active Directory (Azure AD)](https://aka.ms/ade-aad-app).

## <a name="required-user-permissions"></a>Permissões de usuário exigidas
Recuperação de site exige que o usuário tenha permissões para criar o Cofre de chaves nas chaves de região e a cópia do destino para a região.

Para habilitar a replicação de VMs habilitadas para criptografia de disco do portal do Azure, o usuário precisa das seguintes permissões:

- Permissões de cofre de chave
    - Listar
    - Criar
    - Obter

-   Permissões secretas de cofre de chave
    - Listar
    - Criar
    - Obter

- Permissões de chave de Cofre de chaves (necessárias apenas se as VMs usam a chave de criptografia de chave para criptografar chaves de criptografia de disco)
    - Listar
    - Obter
    - Criar
    - Criptografar
    - Descriptografar

Para gerenciar permissões, vá para o recurso de Cofre de chaves no portal. Adicione as permissões necessárias para o usuário. O exemplo a seguir mostra como habilitar as permissões para o Cofre de chaves *ContosoWeb2Keyvault*, que está na região de origem.

1. Vá para **página inicial** > **Keyvaults** > **ContosoWeb2KeyVault > políticas de acesso**.

   ![Janela de permissões do Cofre de chaves](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-1.png)

2. Você pode ver que não há nenhuma permissão de usuário. Selecione **Adicionar nova**. Insira as informações de usuário e permissões.

   ![permissões de Cofre de chaves](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-2.png)

Se o usuário que está habilitando a recuperação de desastres (DR) não tem permissões para copiar as chaves, um administrador de segurança que tem as permissões apropriadas pode usar o script a seguir para copiar as chaves e segredos de criptografia para a região de destino.

Para solucionar problemas de permissões, consulte [problemas de permissão do cofre da chave](#trusted-root-certificates-error-code-151066) mais adiante neste artigo.

>[!NOTE]
>Para habilitar a replicação de VMs habilitadas para criptografia de disco a partir do portal, você precisa de pelo menos "List" permissões sobre os cofres de chaves, segredos e chaves.

## <a name="copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script"></a>Copiar chaves de criptografia de disco para a região de recuperação de desastres, usando o script do PowerShell

1. [Abra o código de script brutos "CopyKeys"](https://aka.ms/ade-asr-copy-keys-code).
2. Copie o script em um arquivo e nomeie- **keys.ps1 cópia**.
3. Abra o aplicativo do Windows PowerShell e vá para a pasta onde você salvou o arquivo.
4. Execute keys.ps1 de cópia.
5. Fornece credenciais do Azure para entrar.
6. Escolha a **assinatura do Azure** de suas VMs.
7. Aguarde até que os grupos de recursos carregar e, em seguida, selecione a **grupo de recursos** das suas VMs.
8. Selecione as VMs da lista que é exibida. Apenas as VMs habilitadas para criptografia de disco estão na lista.
9. Selecione o **local de destino**.

    - **Cofres de chaves de criptografia de disco**
    - **Cofres de chaves de criptografia de chave**

   Por padrão, o Site Recovery cria um novo cofre de chaves na região de destino. O nome do cofre tem um sufixo "asr" se baseia-se as chaves de criptografia de disco VM de origem. Se um cofre de chaves já existir que foi criado pela recuperação de Site, ele será reutilizado. Selecione um cofre de chaves diferente na lista se necessário.

## <a name="enable-replication"></a>Habilitar a replicação

Neste exemplo, a região do Azure primária seja Leste da Ásia e a região secundária seja Sudeste Asiático.

1. No cofre, selecione **+ replicar**.
2. Observe os campos a seguir.
    - **Fonte**: O ponto de origem das VMs que, neste caso, é o **Azure**.
    - **Local de origem**: A região do Azure onde você deseja proteger suas máquinas virtuais. Neste exemplo, o local de origem é "East Asia".
    - **Modelo de implantação**: O modelo de implantação do Azure a computadores de origem.
    - **Assinatura do Azure**: A assinatura à qual suas máquinas virtuais de origem pertencem. Ele pode ser qualquer assinatura que está no mesmo locatário do Azure Active Directory que seu cofre dos serviços de recuperação.
    - **Grupo de recursos**: O grupo de recursos ao qual suas máquinas virtuais de origem pertencem. Todas as VMs no grupo de recursos selecionado são listadas para proteção na próxima etapa.

3. Na **máquinas virtuais** > **selecionar máquinas virtuais**, selecione cada VM que você deseja replicar. Você só pode selecionar computadores para os quais a replicação pode ser habilitada. Depois, selecione **OK**.

4. Na **configurações**, você pode configurar as seguintes configurações de site de destino.

    - **Localização de destino**: O local no qual seus dados de máquina virtual de origem serão replicados. O site Recovery fornece uma lista de regiões de destino adequado com base no local do computador selecionado. É recomendável que você use o mesmo local como local do Cofre de serviços de recuperação.
    - **Assinatura de destino**: A assinatura de destino que é usada para recuperação de desastres. Por padrão, a assinatura de destino será igual à assinatura de origem.
    - **Grupo de recursos de destino**: O grupo de recursos ao qual pertencem todas as máquinas virtuais replicadas. Por padrão, o Site Recovery cria um novo grupo de recursos na região de destino. O nome obtém o sufixo "asr". Se o recurso já existe um grupo que foi criado pelo Azure Site Recovery, ele será reutilizado. Também é possível personalizá-lo, conforme mostrado na seção a seguir. O local do grupo de recursos de destino pode ser qualquer região do Azure, exceto a região onde as máquinas virtuais de origem estão hospedadas.
    - **Rede virtual de destino**: Por padrão, o Site Recovery cria uma nova rede virtual na região de destino. O nome obtém o sufixo "asr". Ele tem mapeado para sua rede de origem e usado para qualquer proteção futura. [Saiba mais](site-recovery-network-mapping-azure-to-azure.md) sobre o mapeamento de rede.
    - **Contas de armazenamento de destino (se sua fonte de VM não usa discos gerenciados)**: Por padrão, o Site Recovery cria uma nova conta de armazenamento de destino por imitando sua configuração de armazenamento VM de origem. Se já existir uma conta de armazenamento, ele será reutilizado.
    - **Discos gerenciados de réplica (se sua VM de origem usa discos gerenciados)**: Site Recovery cria novos discos gerenciados de réplica na região de destino para espelhar a discos gerenciados da VM de origem do mesmo tipo de armazenamento (standard ou premium) como discos gerenciados da VM de origem.
    - **Contas de armazenamento em cache**: Site Recovery precisa de uma conta de armazenamento extra chamada *armazenamento de cache* na região de origem. Todas as alterações nas VMs de origem são controladas e enviadas para a conta de armazenamento de cache. Eles estão, em seguida, replicados para o local de destino.
    - **Conjunto de disponibilidade**: Por padrão, o Site Recovery cria uma novo conjunto de disponibilidade na região de destino. O nome tem o sufixo "asr". Se um conjunto de disponibilidade que foi criado pela recuperação de Site já existir, ele será reutilizado.
    - **Cofres de chaves de criptografia de disco**: Por padrão, o Site Recovery cria um novo cofre de chaves na região de destino. Ele tem um sufixo "asr" se baseia-se as chaves de criptografia de disco VM de origem. Se um cofre de chaves que foi criado pelo Azure Site Recovery já existir, ele será reutilizado.
    - **Cofres de chaves de criptografia de chave**: Por padrão, o Site Recovery cria um novo cofre de chaves na região de destino. O nome tem um sufixo "asr" que se baseia nas chaves de criptografia de chave de VM de origem. Se um cofre de chaves criado pelo Azure Site Recovery já existir, ele será reutilizado.
    - **Política de replicação**: Define as configurações para histórico de retenção de ponto de recuperação e frequência de instantâneo consistente do aplicativo. Por padrão, o Site Recovery cria uma nova política de replicação com configurações padrão de *24 horas* retenção de ponto de recuperação e *60 minutos* para a frequência de instantâneo consistente do aplicativo.

## <a name="customize-target-resources"></a>Personalizar os recursos de destino

Siga estas etapas para modificar as configurações de destino de padrão de recuperação de Site.

1. Selecione **personalizar** ao lado de "Assinatura de destino" para modificar a assinatura de destino padrão. Selecione a assinatura na lista de assinaturas que estão disponíveis no locatário do AD do Azure.

2. Selecione **personalizar** lado "grupo de recursos, conjuntos de disponibilidade, armazenamento e rede" para modificar as seguintes configurações padrão:
    - Para **grupo de recursos de destino**, selecione o grupo de recursos da lista de grupos de recursos no local de destino da assinatura.
    - Para **rede virtual de destino**, selecione a rede de uma lista de redes virtuais no local de destino.
    - Para **conjunto de disponibilidade**, você pode adicionar configurações de conjunto de disponibilidade para a VM, caso elas façam parte de uma conjunto de disponibilidade na região de origem.
    - Para **contas de armazenamento de destino**, selecione a conta a ser usada.

2. Selecione **personalizar** ao lado de "Configurações de criptografia" para modificar as seguintes configurações padrão:
   - Para **os Cofre de chaves de criptografia de disco de destino**, selecione o Cofre de chaves de criptografia de disco alvo da lista de cofres de chaves no local de destino da assinatura.
   - Para **Cofre de chaves de criptografia de chave de destino**, selecione o Cofre de chaves de criptografia de chave de destino na lista de cofres de chaves no local de destino da assinatura.

3. Selecione **criar um recurso de destino** > **habilitar replicação**.
4. Depois que as VMs habilitadas para replicação, você pode verificar o status de integridade as máquinas virtuais sob **itens replicados**.

>[!NOTE]
>Durante a replicação inicial, o status pode levar algum tempo para atualizar, sem andamento aparente. Clique em **Refresh** para obter o status mais recente.

## <a name="update-target-vm-encryption-settings"></a>Atualizar configurações de criptografia da VM de destino
Nos cenários a seguir, você precisará atualizar as configurações de criptografia de VM de destino:
  - Você habilitou a replicação do Site Recovery na máquina virtual. Posteriormente, você habilitou a criptografia de disco na VM de origem.
  - Você habilitou a replicação do Site Recovery na máquina virtual. Posteriormente, você alterou a chave de criptografia de disco ou a chave de criptografia de chave na VM de origem.

Você pode usar [um script](#copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script) para copiar as chaves de criptografia para a região de destino e, em seguida, atualize as configurações de criptografia de destino no **cofre dos serviços de recuperação** > *doitemreplicado*  >  **Properties** > **de computação e rede**.

![Janela de caixa de diálogo de configurações de atualização ADE](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a id="trusted-root-certificates-error-code-151066"></a>Solucionar problemas de permissões do Cofre de chaves durante a replicação de VM do Azure para o Azure

**Causa 1:** Talvez você tenha selecionado da região de destino um cofre de chaves já criado que não tem as permissões necessárias em vez de deixar o Site Recovery crie um. Certifique-se de que o Cofre de chaves tem o exigem permissões, conforme descrito anteriormente.

*Por exemplo*: Tentar replicar uma VM que tenha um cofre de chaves *ContososourceKeyvault* em uma região de origem.
Você tem todas as permissões no cofre de chaves de região de origem. Mas durante a proteção, você selecionar o Cofre de chaves já criado ContosotargetKeyvault, que não tem permissões. Ocorrerá um erro.

**Como corrigir:** Vá para **página inicial** > **Keyvaults** > **ContososourceKeyvault** > **políticas de acesso** e adicione as permissões apropriadas.

**Causa 2:** Talvez você tenha selecionado da região de destino um cofre de chaves já criado que não tenha criptografar decrypt de permissões em vez de deixar o Site Recovery crie um. Certifique-se de que você tenha Criptografar descriptografar as permissões se você também está criptografando a chave na região de origem.</br>

*Por exemplo*: Tentar replicar uma VM que tenha um cofre de chaves *ContososourceKeyvault* na região de origem. Você tem a permissão necessária no cofre de chaves de região de origem. Mas durante a proteção, você selecionar o Cofre de chaves já criado ContosotargetKeyvault, que não tem permissões para descriptografar e criptografar. Ocorrerá um erro.</br>

**Como corrigir:** Vá para **página inicial** > **Keyvaults** > **ContososourceKeyvault** > **políticas de acesso**. Adicionar permissões sob **permissões de chave** > **operações criptográficas**.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais](site-recovery-test-failover-to-azure.md) sobre a execução de failovers de teste.
