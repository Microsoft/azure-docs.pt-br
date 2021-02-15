---
title: Excluir um cofre de Serviços de Recuperação do Microsoft Azure
description: Neste artigo, saiba como remover dependências e, em seguida, excluir um cofre dos serviços de recuperação de backup do Azure.
ms.topic: conceptual
ms.date: 06/04/2020
ms.openlocfilehash: 28a0c4d5f643b980d93df2592da38f5da12dd01a
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520452"
---
# <a name="delete-an-azure-backup-recovery-services-vault"></a>Excluir um cofre dos serviços de recuperação de backup do Azure

Este artigo descreve como excluir um cofre dos serviços de recuperação de [backup do Azure](backup-overview.md) . Ele contém instruções para remover dependências e, em seguida, excluir um cofre.

## <a name="before-you-start"></a>Antes de começar

Não é possível excluir um cofre dos Serviços de Recuperação com qualquer uma das seguintes dependências:

- Você não pode excluir um cofre que contém fontes de dados protegidas (por exemplo, VMs de IaaS, bancos de dados SQL, compartilhamentos de arquivos do Azure).
- Você não pode excluir um cofre que contém dados de backup. Depois que os dados de backup forem excluídos, eles entrarão no estado de exclusão temporária.
- Você não pode excluir um cofre que contém dados de backup no estado de exclusão reversível.
- Não é possível excluir um cofre que tenha contas de armazenamento registradas.

Se você tentar excluir o cofre sem remover as dependências, encontrará uma das seguintes mensagens de erro:

- O cofre não pode ser excluído porque há recursos existentes nesse cofre. Verifique se não há itens de backup, servidores protegidos ou servidores de gerenciamento de backup associados a este cofre. Cancele o registro dos seguintes contêineres associados a este cofre antes de continuar a exclusão.

- O cofre dos Serviços de Recuperação não pode ser excluído, pois há itens de backup no estado de exclusão temporária no cofre. Os itens com exclusão reversível são excluídos permanentemente após 14 dias após a operação de exclusão. Tente excluir o cofre depois que os itens de backup forem excluídos permanentemente e não houver nenhum item no estado de exclusão reversível deixado no cofre. Para obter mais informações, consulte [exclusão reversível para o backup do Azure](./backup-azure-security-feature-cloud.md).

## <a name="proper-way-to-delete-a-vault"></a>Maneira apropriada de excluir um cofre

>[!WARNING]
>A operação a seguir é destrutiva e não pode ser desfeita. Todos os dados de backup e itens de backup associados ao servidor protegido serão excluídos permanentemente. Continue com cuidado.

Para excluir um cofre corretamente, você deve seguir as etapas nesta ordem:

- **Etapa 1**: desabilitar o recurso de exclusão reversível. [Consulte aqui](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) para obter as etapas para desabilitar a exclusão reversível.

- **Etapa 2**: depois de desabilitar a exclusão reversível, verifique se há itens restantes anteriormente no estado de exclusão reversível. Se houver itens no estado de exclusão reversível, você precisará *restaurar* e *excluí* -los novamente. [Siga estas etapas](./backup-azure-security-feature-cloud.md#permanently-deleting-soft-deleted-backup-items) para localizar itens de exclusão reversível e excluí-los permanentemente.

- **Etapa 3**: você deve verificar todos os três locais a seguir para verificar se há itens protegidos:

  - **Itens protegidos na nuvem**: acesse o menu do painel do cofre > **itens de backup**. Todos os itens listados aqui devem ser removidos com **parar backup** ou **excluir dados de backup** junto com seus dados de backup.  [Siga estas etapas](#delete-protected-items-in-the-cloud) para remover esses itens.
  - **Instância de SQL Server**: Vá para o menu do painel do cofre >   >  **servidores protegidos** de infraestrutura de backup. Em Servidores Protegidos, selecione o servidor para cancelar o registro. Para excluir o cofre, você deve cancelar o registro de todos os servidores. Clique com o botão direito do mouse no servidor protegido e selecione **Cancelar registro**.
  - **Servidores protegidos por Mars**: Vá para o menu do painel do cofre >  >  **servidores protegidos** de infraestrutura de backup. Se você tiver servidores protegidos por MARS, todos os itens listados aqui deverão ser excluídos junto com seus dados de backup. [Siga estas etapas](#delete-protected-items-on-premises) para excluir servidores protegidos por Mars.
  - **Servidores de gerenciamento do mAbs ou do DPM**: Vá para o menu do painel do cofre > backup **Infrastructure**  >  **Management Servers**. Se você tiver o DPM ou o Servidor de Backup do Azure (MABS), todos os itens listados aqui deverão ser excluídos ou desregistrados junto com seus dados de backup. [Siga estas etapas](#delete-protected-items-on-premises) para excluir os servidores de gerenciamento.

- **Etapa 4**: você deve garantir que todas as contas de armazenamento registradas sejam excluídas. Acesse o menu do painel do cofre >  >  **as contas de armazenamento** de infraestrutura de backup. Se você tiver contas de armazenamento listadas aqui, deverá cancelar o registro de todas elas. Para saber como cancelar o registro da conta, consulte [cancelar o registro de uma conta de armazenamento](manage-afs-backup.md#unregister-a-storage-account).

Depois de concluir essas etapas, você poderá continuar a [excluir o cofre](#delete-the-recovery-services-vault).

Se você não tiver itens protegidos no local ou na nuvem, mas ainda estiver obtendo o erro de exclusão do cofre, execute as etapas em [excluir o cofre dos serviços de recuperação usando Azure Resource Manager](#delete-the-recovery-services-vault-by-using-azure-resource-manager)

## <a name="delete-protected-items-in-the-cloud"></a>Excluir itens protegidos na nuvem

Primeiro, leia a seção **[antes de começar](#before-you-start)** para entender as dependências e o processo de exclusão de cofre.

Para interromper a proteção e excluir os dados de backup, execute as seguintes etapas:

1. No portal, vá para **cofre dos serviços de recuperação** e vá para **itens de backup**. Em seguida, na lista **tipo de gerenciamento de backup** , selecione os itens protegidos na nuvem (por exemplo, máquinas virtuais do Azure, armazenamento do Azure [serviço de arquivos do Azure] ou SQL Server em máquinas virtuais do Azure).

    ![Selecione o tipo de backup.](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. Você verá uma lista de todos os itens da categoria. Clique com o botão direito do mouse para selecionar o item de backup. Dependendo se o item de backup está protegido ou não, o menu exibe o painel **parar backup** ou o painel **excluir dados de backup** .

    - Se o painel **parar backup** for exibido, selecione **excluir dados de backup** no menu suspenso. Insira o nome do item de backup (esse campo diferencia maiúsculas de minúsculas) e, em seguida, selecione um motivo no menu suspenso. Insira seus comentários, se houver. Em seguida, selecione **parar backup**.

        ![O painel parar backup.](./media/backup-azure-delete-vault/stop-backup-item.png)

    - Se o painel **excluir dados de backup** for exibido, insira o nome do item de backup (esse campo diferencia maiúsculas de minúsculas) e, em seguida, selecione um motivo no menu suspenso. Insira seus comentários, se houver. Em seguida, selecione **Excluir**.

         ![O painel excluir dados de backup.](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

3. Verifique o ícone de **notificação** : ![ o ícone de notificação.](./media/backup-azure-delete-vault/messages.png) Depois que o processo for concluído, o serviço exibirá a seguinte mensagem: *parando backup e excluindo dados de backup para "* item de backup *"*. *Operação concluída com êxito*.
4. Selecione **Atualizar** no menu **itens de backup** para verificar se o item de backup foi excluído.

      ![A página excluir itens de backup.](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>Excluir itens protegidos localmente

Primeiro, leia a seção **[antes de começar](#before-you-start)** para entender as dependências e o processo de exclusão de cofre.

1. No menu do painel do cofre, selecione **infraestrutura de backup**.
2. Dependendo do seu cenário local, escolha uma das seguintes opções:

      - Para MARS, selecione **servidores protegidos** e o  **agente de backup do Azure**. Em seguida, selecione o servidor que você deseja excluir.

        ![Para MARS, selecione seu cofre para abrir seu painel.](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - Para MABS ou DPM, selecione **backup servidores de gerenciamento**. Em seguida, selecione o servidor que você deseja excluir.

          ![Para MABS ou DPM, selecione seu cofre para abrir seu painel.](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. O painel **excluir** é exibido com uma mensagem de aviso.

     ![O painel excluir.](./media/backup-azure-delete-vault/delete-protected-server.png)

     Examine a mensagem de aviso e as instruções na caixa de seleção consentimento.
    > [!NOTE]
    >
    >- Se o servidor protegido for sincronizado com os serviços do Azure e os itens de backup existirem, a caixa de seleção consentimento exibirá o número de itens de backup dependentes e o link para exibir os itens de backup.
    >- Se o servidor protegido não estiver sincronizado com os serviços do Azure e os itens de backup existirem, a caixa de seleção de consentimento exibirá apenas o número de itens de backup.
    >- Se não houver nenhum item de backup, a caixa de seleção de consentimento solicitará a exclusão.

4. Marque a caixa de seleção consentimento e, em seguida, selecione **excluir**.

5. Verifique o ícone de **notificação** ![ excluir dados de backup ](./media/backup-azure-delete-vault/messages.png) . Depois que a operação for concluída, o serviço exibirá a mensagem: *parando o backup e excluindo os dados de backup de "item de backup".* *Operação concluída com êxito*.
6. Selecione **Atualizar** no menu **itens de backup** para verificar se o item de backup foi excluído.

>[!NOTE]
>Se você excluir um item protegido local de um portal que contém dependências, receberá um aviso dizendo que "Excluir registro do servidor é uma operação destrutiva e não pode ser desfeito. Todos os dados de backup (pontos de recuperação necessários para restaurar os dados) e itens de backup associados ao servidor protegido serão excluídos permanentemente. "

Após a conclusão desse processo, você pode excluir os itens de backup do console de gerenciamento:

- [Excluir itens de backup do console de gerenciamento do MARS](#delete-backup-items-from-the-mars-management-console)
- [Excluir itens de backup do console de gerenciamento do MABS ou do DPM](#delete-backup-items-from-the-mabs-or-dpm-management-console)

### <a name="delete-backup-items-from-the-mars-management-console"></a>Excluir itens de backup do console de gerenciamento do MARS

>[!NOTE]
>Se você excluiu ou perdeu a máquina de origem sem interromper o backup, o próximo backup agendado falhará. O ponto de recuperação antigo expira de acordo com a política, mas o último ponto de recuperação único é sempre retido até que você interrompa o backup e exclua os dados. Você pode fazer isso seguindo as etapas nesta [seção](#delete-protected-items-on-premises).

1. Abra o console de gerenciamento do MARS, vá para o painel **ações** e selecione **agendar backup**.
2. Na página **modificar ou parar um backup agendado** , selecione **parar de usar este agendamento de backup e excluir todos os backups armazenados**. Em seguida, selecione **Avançar**.

    ![Modificar ou parar um backup agendado.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Na página **parar um backup agendado** , selecione **concluir**.

    ![Interromper um backup agendado.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. Você será solicitado a inserir um PIN de segurança (número de identificação pessoal), que deve ser gerado manualmente. Para fazer isso, primeiro entre no portal do Azure.
5. Vá para **serviços de recuperação**  >  **configurações** do cofre  >  **Propriedades**.
6. Em **PIN de segurança**, selecione **gerar**. Copie este PIN. O PIN é válido por apenas cinco minutos.
7. No console de gerenciamento, Cole o PIN e selecione **OK**.

    ![Gerar um PIN de segurança.](./media/backup-azure-delete-vault/security-pin.png)

8. Na página **progresso do backup de modificação** , a seguinte mensagem é exibida: os *dados de backup excluídos serão mantidos por 14 dias. Após esse período, os dados de backup serão excluídos permanentemente.*  

    ![Exclua a infraestrutura de backup.](./media/backup-azure-delete-vault/deleted-backup-data.png)

Depois de excluir os itens de backup locais, siga as próximas etapas do Portal.

### <a name="delete-backup-items-from-the-mabs-or-dpm-management-console"></a>Excluir itens de backup do console de gerenciamento do MABS ou do DPM

>[!NOTE]
>Se você excluiu ou perdeu a máquina de origem sem interromper o backup, o próximo backup agendado falhará. O ponto de recuperação antigo expira de acordo com a política, mas o último ponto de recuperação único é sempre retido até que você interrompa o backup e exclua os dados. Você pode fazer isso seguindo as etapas nesta [seção](#delete-protected-items-on-premises).

Há dois métodos que podem ser usados para excluir itens de backup do console de gerenciamento do MABS ou do DPM.

#### <a name="method-1"></a>Método 1

Para interromper a proteção e excluir os dados de backup, execute as seguintes etapas:

1. Abra o Console do Administrador do DPM e, em seguida, selecione **proteção** na barra de navegação.
2. No painel de exibição, selecione o membro do grupo de proteção que você deseja remover. Clique com o botão direito do mouse para selecionar a opção **parar proteção de membros do grupo** .
3. Na caixa de diálogo **parar proteção** , selecione **excluir dados protegidos** e marque a caixa de seleção **Excluir armazenamento online** . Em seguida, selecione **parar proteção**.

    ![Selecione excluir dados protegidos no painel parar proteção.](./media/backup-azure-delete-vault/delete-storage-online.png)

    O status do membro protegido é alterado para *réplica inativa disponível*.

4. Clique com o botão direito do mouse no grupo de proteção inativo e selecione **remover proteção inativa**.

    ![Remova a proteção inativa.](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. Na janela **excluir proteção inativa** , marque a caixa de seleção **Excluir armazenamento online** e selecione **OK**.

    ![Exclua o armazenamento online.](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

#### <a name="method-2"></a>Método 2

Abra o **mAbs Management** ou o console de **Gerenciamento do DPM** . Em **selecionar método de proteção de dados**, desmarque a caixa de seleção  **desejo proteção online** .

  ![Selecione o método de proteção de dados.](./media/backup-azure-delete-vault/data-protection-method.png)

Depois de excluir os itens de backup locais, siga as próximas etapas do Portal.

## <a name="delete-the-recovery-services-vault"></a>Excluir o Cofre de Serviços de Recuperação

1. Quando todas as dependências tiverem sido removidas, role até o painel **Essentials** no menu do cofre.
2. Verifique se não há itens de backup, servidores de gerenciamento de backup ou itens replicados listados. Se os itens ainda aparecerem no cofre, consulte a seção [antes de começar](#before-you-start) .

3. Quando não houver mais itens no cofre, selecione **excluir** no painel do cofre.

    ![Selecione Excluir no painel do cofre.](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Selecione **Sim** para verificar se deseja excluir o cofre. O cofre é excluído. O portal retorna para o **novo** menu de serviço.

## <a name="delete-the-recovery-services-vault-by-using-powershell"></a>Excluir o cofre dos serviços de recuperação usando o PowerShell

Primeiro, leia a seção **[antes de começar](#before-you-start)** para entender as dependências e o processo de exclusão de cofre.

Para interromper a proteção e excluir os dados de backup:

- Se você estiver usando o SQL no backup de VMs do Azure e tiver habilitado a proteção automática para instâncias do SQL, primeiro desabilite a proteção automática.

    ```PowerShell
        Disable-AzRecoveryServicesBackupAutoProtection
           [-InputItem] <ProtectableItemBase>
           [-BackupManagementType] <BackupManagementType>
           [-WorkloadType] <WorkloadType>
           [-PassThru]
           [-VaultId <String>]
           [-DefaultProfile <IAzureContextContainer>]
           [-WhatIf]
           [-Confirm]
           [<CommonParameters>]
    ```

  [Saiba mais](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection) sobre como desabilitar a proteção para um item protegido pelo backup do Azure.

- Interrompa a proteção e exclua dados de todos os itens protegidos por backup na nuvem (por exemplo: VM IaaS, compartilhamento de arquivos do Azure e assim por diante):

    ```PowerShell
       Disable-AzRecoveryServicesBackupProtection
       [-Item] <ItemBase>
       [-RemoveRecoveryPoints]
       [-Force]
       [-VaultId <String>]
       [-DefaultProfile <IAzureContextContainer>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
    ```

    [Saiba mais](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection)   sobre desabilita a proteção para um item protegido por backup.

- Para arquivos e pastas locais protegidos usando o agente de backup do Azure (MARS) fazendo backup no Azure, use o seguinte comando do PowerShell para excluir os dados de backup de cada módulo MARS PowerShell:

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Posteriormente, o prompt a seguir será exibido:

    *Backup do Microsoft Azure tem certeza de que deseja remover esta política de backup? Os dados de backup excluídos serão mantidos por 14 dias. Após esse período, os dados de backup serão excluídos permanentemente. <br/> [Y] Sim [A] Sim para todos [N] não [L] não para todos [S] suspender [?] Ajuda (o padrão é "Y"):*

- Para computadores locais protegidos usando o MABS (Backup do Microsoft Azure Server) ou o DPM (System Center Data Protection Manager) no Azure, use o comando a seguir para excluir os dados de backup no Azure.

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Posteriormente, o prompt a seguir será exibido:

   *Backup do Microsoft Azure* Tem certeza de que deseja remover esta política de backup? Os dados de backup excluídos serão mantidos por 14 dias. Após esse período, os dados de backup serão excluídos permanentemente. <br/>
   [Y] Sim [A] Sim para todos [N] não [L] não para todos [S] suspender [?] Ajuda (o padrão é "Y"):*

Depois de excluir os dados de backup, cancele o registro de todos os contêineres locais e servidores de gerenciamento.

- Para arquivos e pastas locais protegidos usando o MARS (agente de backup do Azure) fazendo backup no Azure:

    ```PowerShell
    Unregister-AzRecoveryServicesBackupContainer
              [-Container] <ContainerBase>
              [-PassThru]
              [-VaultId <String>]
              [-DefaultProfile <IAzureContextContainer>]
              [-WhatIf]
              [-Confirm]
              [<CommonParameters>]
    ```

    [Saiba mais](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer) sobre o cancelamento do registro de um Windows Server ou outro contêiner do cofre.

- Para computadores locais protegidos usando o MABS (servidor de Backup do Microsoft Azure) ou o DPM para o Azure (proteção de dados do System Center, gerencie:

    ```PowerShell
        Unregister-AzRecoveryServicesBackupManagementServer
          [-AzureRmBackupManagementServer] <BackupEngineBase>
          [-PassThru]
          [-VaultId <String>]
          [-DefaultProfile <IAzureContextContainer>]
          [-WhatIf]
          [-Confirm]
          [<CommonParameters>]
    ```

    [Saiba mais](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer) sobre o cancelamento do registro de um contêiner de gerenciamento de backup do cofre.

Depois de excluir permanentemente os dados de backup e cancelar o registro de todos os contêineres, continue para excluir o cofre.

Para excluir um cofre dos Serviços de Recuperação:

   ```PowerShell
       Remove-AzRecoveryServicesVault
      -Vault <ARSVault>
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
   ```

[Saiba mais](/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault) sobre como excluir um cofre dos serviços de recuperação.

## <a name="delete-the-recovery-services-vault-by-using-cli"></a>Excluir o cofre dos serviços de recuperação usando a CLI

Primeiro, leia a seção **[antes de começar](#before-you-start)** para entender as dependências e o processo de exclusão de cofre.

> [!NOTE]
> Atualmente, a CLI do backup do Azure dá suporte ao gerenciamento apenas de backups de VM do Azure, portanto, o seguinte comando para excluir o cofre funciona apenas se o cofre contiver backups de VM do Azure. Você não poderá excluir um cofre usando a CLI de backup do Azure, se o cofre contiver qualquer item de backup do tipo diferente de VMs do Azure.

Para excluir o cofre de serviços de recuperação existente, execute as seguintes etapas:

- Para interromper a proteção e excluir os dados de backup

    ```azurecli
    az backup protection disable --container-name
                             --item-name
                             [--delete-backup-data {false, true}]
                             [--ids]
                             [--resource-group]
                             [--subscription]
                             [--vault-name]
                             [--yes]
    ```

    Para obter mais informações, consulte este [artigo](/cli/azure/backup/protection#az-backup-protection-disable).

- Excluir um cofre de serviços de recuperação existente:

    ```azurecli
    az backup vault delete [--force]
                       [--ids]
                       [--name]
                       [--resource-group]
                       [--subscription]
                       [--yes]
    ```

    Para obter mais informações, consulte este [artigo](/cli/azure/backup/vault)

## <a name="delete-the-recovery-services-vault-by-using-azure-resource-manager"></a>Excluir o cofre dos serviços de recuperação usando Azure Resource Manager

Esta opção para excluir o cofre dos serviços de recuperação é recomendada somente se todas as dependências forem removidas e você ainda estiver obtendo o *erro de exclusão do cofre*. Tente uma ou todas as seguintes dicas:

- No painel **Essentials** no menu do cofre, verifique se não há itens de backup, servidores de gerenciamento de backup ou itens replicados listados. Se houver itens de backup, consulte a seção [antes de começar](#before-you-start) .
- Tente [excluir o cofre do portal](#delete-the-recovery-services-vault) novamente.
- Se todas as dependências forem removidas e você ainda estiver recebendo o *erro de exclusão do cofre*, use a ferramenta ARMClient para executar as etapas a seguir (após a observação).

1. Acesse [Chocolatey.org](https://chocolatey.org/) para baixar e instalar o Chocolatey. Em seguida, instale o ARMClient executando o seguinte comando:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Entre em sua conta do Azure e, em seguida, execute o seguinte comando:

    `ARMClient.exe login [environment name]`

3. Na portal do Azure, reúna a ID da assinatura e o nome do grupo de recursos para o cofre que você deseja excluir.

Para obter mais informações sobre o comando ARMClient, consulte [ARMCLIENT README](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-the-azure-resource-manager-client-to-delete-a-recovery-services-vault"></a>Usar o cliente Azure Resource Manager para excluir um cofre dos serviços de recuperação

1. Execute o comando a seguir usando sua ID de assinatura, o nome do grupo de recursos e o nome do cofre. Se você não tiver nenhuma dependência, o cofre será excluído quando você executar o seguinte comando:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<Recovery Services vault name>?api-version=2015-03-15
   ```

2. Se o cofre não estiver vazio, você receberá a seguinte mensagem de erro: o *cofre não pode ser excluído, pois há recursos existentes nesse cofre.* Para remover um contêiner ou item protegido em um cofre, execute o seguinte comando:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<Recovery Services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. No portal do Azure, verifique se o cofre foi excluído.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre cofres](backup-azure-recovery-services-vault-overview.md) 
 dos serviços de recuperação [Saiba mais sobre como monitorar e gerenciar cofres de serviços de recuperação](backup-azure-manage-windows-server.md)
