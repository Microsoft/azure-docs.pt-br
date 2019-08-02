---
title: Excluir um cofre dos serviços de recuperação no Azure
description: Descreve como excluir um cofre dos serviços de recuperação.
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/11/2019
ms.author: raynew
ms.openlocfilehash: f98b9a02d12cc53ba23857b203ee3eaed9dd7cfa
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466649"
---
# <a name="delete-a-recovery-services-vault"></a>Excluir um cofre dos Serviços de Recuperação

Este artigo descreve como excluir um cofre dos serviços de recuperação de [backup do Azure](backup-overview.md) . Ele contém instruções para remover dependências e, em seguida, excluir um cofre.


## <a name="before-you-start"></a>Antes de iniciar

Você não pode excluir um cofre dos serviços de recuperação que tem dependências como servidores protegidos ou servidores de gerenciamento de backup associados ao cofre.<br/>
O cofre que contém os dados de backup não pode ser excluído (ou seja, mesmo que você tenha interrompido a proteção, mas manteve os dados de backup).

Se você excluir um cofre que contém dependências, ele exibirá o seguinte erro:

![erro ao excluir o cofre](./media/backup-azure-delete-vault/error.png)

Para excluir o cofre normalmente, escolha o cenário que corresponde à sua configuração e siga as etapas recomendadas:

Cenário | Etapas para remover dependências para excluir o cofre |
-- | --
Tenho arquivos e pastas locais protegidos usando o MARS (agente de backup do Azure) fazendo backup no Azure | Execute as etapas em excluir dados de backup e itens de backup- [para o agente Mars](#for-mars-agent)
Tenho computadores locais protegidos usando o MABS (Backup do Microsoft Azure Server) ou o DPM no Azure (System Center Data Protection Manager) | Execute as etapas em excluir dados de backup e itens de backup- [para o agente mAbs](#for-mabs-agent)
Tenho itens protegidos na nuvem (por exemplo, VM laaS, compartilhamento de arquivos do Azure, etc.)  | Execute as etapas em excluir dados de backup e itens de backup- [para itens protegidos na nuvem](#for-protected-items-in-cloud)
Eu protegi itens locais e na nuvem | Execute as etapas em excluir dados de backup e itens de backup na sequência abaixo: <br> - [Para itens protegidos na nuvem](#for-protected-items-in-cloud)<br> - [Para o agente MABS](#for-mars-agent) <br> - [Para o agente MABS](#for-mabs-agent)
Não tenho itens protegidos no local ou na nuvem; no entanto, ainda estou recebendo o erro de exclusão do cofre | Execute as etapas em [excluir o cofre dos serviços de recuperação usando Azure Resource Manager cliente](#delete-the-recovery-services-vault-using-azure-resource-manager-client)
Não tenho mais esse servidor local original (perdido/descomissionado) e desejo excluir o cofre dos serviços de recuperação | Contate o suporte da Microsoft.

## <a name="delete-backup-data-and-backup-items"></a>Excluir dados de backup e itens de backup

Antes de continuar, leia **[esta](#before-you-start)** seção para entender as dependências e o processo de exclusão de cofre.

### <a name="for-protected-items-in-cloud"></a>Para itens protegidos na nuvem

Para interromper a proteção e excluir os dados de backup, execute o seguinte:

1. No portal > cofre dos serviços de recuperação > itens de backup escolha os itens protegidos na nuvem.

    ![selecione o tipo de backup](./media/backup-azure-delete-vault/azure-storage-selected.jpg)

2. Para cada item, você precisa clicar com o botão direito do mouse e escolher **parar backup**.

    ![selecione o tipo de backup](./media/backup-azure-delete-vault/stop-backup-item.png)

3. Em **parar backup** > ,**escolha uma opção**, selecione **excluir dados de backup**.
4. Digite o nome do item e clique em **parar backup**.
   - Isso verifica se você deseja excluir o item.
   - O botão **parar backup** é ativado após a verificação.
   - Se você mantiver e não excluir os dados, não poderá excluir o cofre.

     ![Excluir dados de backup](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. Verifique a **notificação** ![excluir dados](./media/backup-azure-delete-vault/messages.png)de backup. Após a conclusão, o serviço exibirá a mensagem: Parando **backup e excluindo dados de backup para "*item de backup*"** . **Operação concluída com êxito**.
6. Clique em **Atualizar** no menu **itens de backup** para verificar se o item de backup foi removido.

      ![Excluir dados de backup](./media/backup-azure-delete-vault/empty-items-list.png)

### <a name="for-mars-agent"></a>Para o agente MARS

Para interromper a proteção e excluir dados de backup, execute as etapas na ordem listada abaixo:

- [Etapa 1: Excluir itens de backup do console de gerenciamento do MARS](#step-1-delete-backup-items-from-mars-management-console)
- [Etapa 2: Do portal, remova o agente de backup do Azure](#step-1-delete-backup-items-from-mars-management-console)


#### <a name="step-1-delete-backup-items-from-mars-management-console"></a>Etapa 1: Excluir itens de backup do console de gerenciamento do MARS

Se não for possível executar essa etapa devido à indisponibilidade do servidor, contate o suporte da Microsoft.

- Inicie o console de gerenciamento do MARS, vá para o painel **ações** e escolha **agendar backup**.
- Em **modificar ou parar um** assistente de backup agendado, escolha a opção **parar de usar essa agenda de backup e excluir todos os backups armazenados** e clique em **Avançar**.

    ![Modificar ou Parar um Backup Agendado](./media/backup-azure-delete-vault/modify-schedule-backup.png)

- Em **parar um** assistente de backup agendado, clique em **concluir**.

    ![Parar um backup agendado](./media/backup-azure-delete-vault/stop-schedule-backup.png)
- Você será solicitado a inserir um PIN de segurança. Para gerar o PIN, execute as etapas a seguir:
  - Entre no Portal do Azure.
  - Navegue até **Cofre dos Serviços de Recuperação** > **Configurações** > **Propriedades**.
  - Em **PIN de Segurança**, clique em **Gerar**. Copie este PIN. (Esse PIN é válido por apenas cinco minutos)
- No console de gerenciamento (aplicativo cliente), Cole o PIN e clique em **OK**.

  ![PIN de segurança](./media/backup-azure-delete-vault/security-pin.png)

- No assistente de **modificação de progresso de backup** , *você verá que os dados de backup excluídos serão mantidos por 14 dias. Após esse período, os dados de backup serão excluídos permanentemente.*  

    ![Excluir infraestrutura de backup](./media/backup-azure-delete-vault/deleted-backup-data.png)

Agora que você excluiu os itens de backup do local, conclua as próximas etapas do Portal.

#### <a name="step-2-from-portal-remove-azure-backup-agent"></a>Etapa 2: Do portal, remova o agente de backup do Azure

Verifique se a [etapa 1](#step-1-delete-backup-items-from-mars-management-console) está concluída antes de continuar:

1. No menu do painel do cofre, clique em **infraestrutura de backup**.
2. Clique em **servidores protegidos** para exibir os servidores de infraestrutura.

    ![selecione seu cofre para abrir o painel dele](./media/backup-azure-delete-vault/identify-protected-servers.png)

3. Na lista **Servidores Protegidos**, clique em Agente de Backup do Azure.

    ![selecione o tipo de backup](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

4. Clique no servidor na lista de servidores protegidos usando o agente de backup do Azure.

    ![selecione o servidor protegido específico](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

5. No painel do servidor selecionado, clique em **excluir**.

    ![exclua o servidor selecionado](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

6. No menu **excluir** , digite o nome do servidor e clique em **excluir**.

     ![Excluir dados de backup](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

> [!NOTE]
> Se você estiver vendo o erro abaixo, primeiro execute as etapas listadas em [excluindo itens de backup do console de gerenciamento](#step-1-delete-backup-items-from-mars-management-console).
>
>![falha na exclusão](./media/backup-azure-delete-vault/deletion-failed.png)
>
>Se não for possível executar as etapas para excluir backups do console de gerenciamento do, por exemplo, devido à indisponibilidade do servidor com o console de gerenciamento, entre em contato com o suporte da Microsoft.

7. Verifique a **notificação** ![excluir dados](./media/backup-azure-delete-vault/messages.png)de backup. Após a conclusão, o serviço exibirá a mensagem: Parando **backup e excluindo dados de backup para "*item de backup*"** . **Operação concluída com êxito**.
8. Clique em **Atualizar** no menu **itens de backup** para verificar se o item de backup foi removido.


### <a name="for-mabs-agent"></a>Para o agente MABS

Para interromper a proteção e excluir dados de backup, execute as etapas na ordem listada abaixo:

- [Etapa 1: Excluir itens de backup do console de gerenciamento do MABS](#step-1-delete-backup-items-from-mabs-management-console)
- [Etapa 2: Do portal remover servidores de gerenciamento de backup do Azure](#step-2-from-portal-remove-azure-backup-agent)

#### <a name="step-1-delete-backup-items-from-mabs-management-console"></a>Etapa 1: Excluir itens de backup do console de gerenciamento do MABS

Se não for possível executar essa etapa devido à indisponibilidade do servidor, contate o suporte da Microsoft.

**Método 1** Para interromper a proteção e excluir dados de backup, execute as etapas a seguir:

1.  Em Console do Administrador do DPM, clique em **proteção** na barra de navegação.
2.  No painel de exibição, selecione o membro do grupo de proteção que você deseja remover. Clique com o botão direito do mouse para escolher a opção **parar proteção de membros do grupo** .
3.  Na caixa de diálogo **interromper proteção** , selecione **excluir dados** > protegidos**Excluir armazenamento online** e clique em **parar proteção**.

    ![Excluir armazenamento online](./media/backup-azure-delete-vault/delete-storage-online.png)

O status do membro protegido agora é alterado para **réplica inativa disponível**.

5. Clique com o botão direito do mouse no grupo de proteção inativo e selecione **remover proteção inativa**.

    ![Remover proteção inativa](./media/backup-azure-delete-vault/remove-inactive-protection.png)

6. Na janela **excluir proteção inativa** , selecione **Excluir armazenamento online** e clique em **OK**.

    ![Remover réplicas em disco e online](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

**Método 2** Inicie o console de **Gerenciamento do mAbs** . Na seção **selecionar método de proteção de dados** , cancele a seleção **desejo proteção online**.

  ![selecionar método de proteção de dados](./media/backup-azure-delete-vault/data-protection-method.png)

Agora que você excluiu os itens de backup do local, conclua as próximas etapas do Portal.

#### <a name="step-2-from-portal-remove-azure-backup-management-servers"></a>Etapa 2: Do portal remover servidores de gerenciamento de backup do Azure

Verifique se a [etapa 1](#step-1-delete-backup-items-from-mabs-management-console) está concluída antes de continuar:

1. No menu do painel do cofre, clique em **infraestrutura de backup**.
2. Clique em **servidores de gerenciamento de backup** para exibir servidores.

    ![Selecione cofre para abrir seu painel](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Clique com o botão direito do mouse no item > **excluir**.
4. No menu **excluir** , digite o nome do servidor e clique em **excluir**.

     ![Excluir dados de backup](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

> [!NOTE]
> Se você estiver vendo o erro abaixo, primeiro execute as etapas listadas em [excluindo itens de backup do console de gerenciamento](#step-2-from-portal-remove-azure-backup-management-servers).
>
>![falha na exclusão](./media/backup-azure-delete-vault/deletion-failed.png)
>
> Se não for possível executar as etapas para excluir backups do console de gerenciamento do, por exemplo, devido à indisponibilidade do servidor com o console de gerenciamento, entre em contato com o suporte da Microsoft.

5. Verifique a **notificação** ![excluir dados](./media/backup-azure-delete-vault/messages.png)de backup. Após a conclusão, o serviço exibirá a mensagem: Parando **backup e excluindo dados de backup para "*item de backup*"** . **Operação concluída com êxito**.
6. Clique em **Atualizar** no menu **itens de backup** para verificar se o item de backup foi removido.


## <a name="delete-the-recovery-services-vault"></a>Excluir o Cofre de Serviços de Recuperação

1. Quando todas as dependências tiverem sido removidas, role até o painel **Essentials** no menu do cofre.
2. Verifique se não há **itens de backup**, **servidores de gerenciamento de backup**ou **itens replicados** listados. Se os itens ainda aparecerem no cofre, [remova-os](#delete-backup-data-and-backup-items).

3. Quando não houver nenhum outro item no cofre, clique em **Excluir** no painel do cofre.

    ![Excluir dados de backup](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Para verificar se você deseja excluir o cofre, clique em **Sim**. O cofre é excluído e o portal retorna para o menu de serviço **Novo** .

## <a name="delete-the-recovery-services-vault-using-azure-resource-manager-client"></a>Excluir o cofre dos serviços de recuperação usando Azure Resource Manager cliente

Esta opção para excluir o cofre dos serviços de recuperação só é recomendada quando todas as dependências são removidas e você ainda está obtendo o *erro de exclusão do cofre*.



- No painel **Essentials** no menu do cofre, verifique se não há itens de **backup**, **servidores de gerenciamento de backup**ou **itens replicados** listados. Se houver itens de backup, execute as etapas em [excluir dados de backup e itens de backup](#delete-backup-data-and-backup-items).
- Tente [excluir o cofre do portal](#delete-the-recovery-services-vault)novamente.
- Se todas as dependências forem removidas e você ainda estiver recebendo o *erro de exclusão do cofre* , use a ferramenta ARMClient para executar as etapas abaixo;

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Instale o Chocolatey [aqui](https://chocolatey.org/) e para instalar o ARMClient execute o comando abaixo:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Entre em sua conta do Azure e execute este comando:

    `ARMClient.exe login [environment name]`

3. Na portal do Azure, reúna a ID da assinatura e o nome do grupo de recursos para o cofre que você deseja excluir.

Para obter mais informações sobre o comando ARMClient, consulte este [documento](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-azure-resource-manager-client-to-delete-recovery-services-vault"></a>Usar Azure Resource Manager cliente para excluir o cofre dos serviços de recuperação

1. Execute o comando a seguir usando sua ID de assinatura, o nome do grupo de recursos e o nome do cofre. Quando você executar o comando, ele excluirá o cofre se você não tiver nenhuma dependência.

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
2. Se o cofre não estiver vazio, você receberá o erro "o cofre não pode ser excluído, pois há recursos existentes neste cofre". Para remover um contêiner/itens protegidos em um cofre, faça o seguinte:

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. No portal do Azure, verifique se o cofre foi excluído.


## <a name="next-steps"></a>Próximas etapas

[Saiba mais](backup-azure-recovery-services-vault-overview.md) Cofres dos serviços de recuperação.<br/>
[Saiba mais sobre como](backup-azure-manage-windows-server.md) monitorar e gerenciar cofres de serviços de recuperação.
