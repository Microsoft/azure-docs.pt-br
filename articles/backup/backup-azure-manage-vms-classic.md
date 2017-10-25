---
title: "Gerenciar e monitorar backups de máquinas virtuais do Azure | Microsoft Docs"
description: "Saiba como gerenciar e monitorar backups de uma máquina virtual do Azure"
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
ms.assetid: 4372944e-d33a-4f6a-bd5f-d04af2842713
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: trinadhk;markgal;
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d876bb1759600fa29a26730bfa8b4ec19db1e442
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-common-azure-backup-jobs-and-trigger-alerts-in-the-classic-portal"></a>Gerenciar trabalhos comuns do Backup do Azure e disparar alertas no portal clássico
> [!div class="op_single_selector"]
> * [Gerenciar backups da VM do Azure](backup-azure-manage-vms.md)
> * [Gerenciar backups de VMs clássicas](backup-azure-manage-vms-classic.md)
>
>

Este artigo fornece informações sobre tarefas comuns de gerenciamento e monitoramento para máquinas virtuais do modelo Clássico protegidas no Azure.  

> [!NOTE]
> O Azure tem dois modelos de implantação para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../azure-resource-manager/resource-manager-deployment-model.md). Consulte [Preparar seu ambiente para fazer backup de máquinas virtuais do Azure](backup-azure-vms-prepare.md) para obter detalhes sobre como trabalhar com VMs do modelo de implantação Clássico.
>
> [!IMPORTANT]
>A partir de março de 2017, você não poderá mais usar o portal clássico para criar os cofres de Backup.
>
> Agora você pode atualizar os cofres de Backup para cofres dos Serviços de Recuperação. Para obter detalhes, veja o artigo [Atualizar um cofre de Backup para um cofre dos Serviços de Recuperação](backup-azure-upgrade-backup-to-recovery-services.md). A Microsoft incentiva você a atualizar os cofres de Backup para os cofres dos Serviços de Recuperação.<br/> Após 15 de outubro de 2017, você não poderá usar o PowerShell para criar os Cofres do Backup. **Em 1º de novembro de 2017**:
>- Todos os Cofres do Backup restantes serão atualizados automaticamente para os cofres dos Serviços de Recuperação.
>- Você não poderá acessar os dados de backup no portal clássico. Em vez disso, use o portal do Azure para acessar os dados de backup nos cofres dos Serviços de Recuperação.

## <a name="manage-protected-virtual-machines"></a>Gerenciar máquinas virtuais protegidas
Para gerenciar máquinas virtuais protegidas:

1. Para exibir e gerenciar as configurações de backup de uma máquina virtual, clique na guia **Itens Protegidos** .
2. Clique no nome de um item protegido para ver a guia **Detalhes de Backup** , que mostra informações sobre o último backup.

    ![Backup de máquinas virtuais](./media/backup-azure-manage-vms/backup-vmdetails.png)
3. Para exibir e gerenciar as configurações de política de backup de uma máquina virtual, clique na guia **Políticas** .

    ![Política de máquina virtual](./media/backup-azure-manage-vms/manage-policy-settings.png)

    A guia **Políticas de Backup** exibe a política existente. Você pode modificar conforme necessário. Se você precisar criar uma nova política, clique em **Criar** na página **Políticas**. Se você quiser remover uma política, ela não deve ter nenhuma máquina virtual associada a ela.

    ![Política de máquina virtual](./media/backup-azure-manage-vms/backup-vmpolicy.png)
4. Você pode obter mais informações sobre ações ou status de uma máquina virtual na página **Trabalhos** . Clique em um trabalho na lista para obter mais detalhes ou filtrar trabalhos para uma máquina virtual específica.

    ![Trabalhos](./media/backup-azure-manage-vms/backup-job.png)

## <a name="on-demand-backup-of-a-virtual-machine"></a>Backup sob demanda de uma máquina virtual
Você pode obter um backup sob demanda de uma máquina virtual quando ela estiver configurada para proteção. Se o backup inicial estiver pendente para a máquina virtual, o backup sob demanda criará uma cópia completa da máquina virtual no cofre de backup do Azure. Se o primeiro backup for concluído, o backup sob demanda enviará apenas as alterações de backup anterior para o cofre de backup do Azure, ou seja, sempre é incremental.

> [!NOTE]
> O período de retenção de um backup sob demanda é definido como o valor de retenção especificado para retenção diária na política de backup correspondente à VM.  
>
>

Para fazer backup sob demanda de uma máquina virtual:

1. Navegue até a página **Itens Protegidos** e selecione **Máquina Virtual do Azure** como **Tipo** (se ainda não estiver selecionada) e clique no botão **Selecionar**.

    ![Tipo de VM](./media/backup-azure-manage-vms/vm-type.png)
2. Selecione a máquina virtual na qual você deseja fazer um backup sob demanda e clique no botão **Backup agora** na parte inferior da página.

    ![Fazer backup agora](./media/backup-azure-manage-vms/backup-now.png)

    Isso criará um trabalho de backup na máquina virtual selecionada. O período de retenção do ponto de recuperação criado por esse trabalho será o mesmo que o especificado na política associada à máquina virtual.

    ![Criando um trabalho de backup](./media/backup-azure-manage-vms/creating-job.png)

   > [!NOTE]
   > Para exibir a política associada a uma máquina virtual, faça uma busca detalhada na máquina virtual na página **Itens protegidos** e vá para a guia da política de backup.
   >
   >
3. Depois que o trabalho é criado, você pode clicar no botão **Exibir trabalho** na barra de notificação do sistema para ver o trabalho correspondente na página de trabalhos.

    ![Trabalho de backup criado](./media/backup-azure-manage-vms/created-job.png)
4. Depois da conclusão bem-sucedida do trabalho, um ponto de recuperação será criado, o qual você poderá usar para restaurar a máquina virtual. Isso também incrementará o valor da coluna do ponto de recuperação em 1 na página **Itens protegidos** .

## <a name="stop-protecting-virtual-machines"></a>Interromper a proteção de máquinas virtuais
Você pode optar por parar os futuros backups de uma máquina virtual com as seguintes opções:

* Reter dados de backup associados à máquina virtual no cofre de Backup do Azure
* Excluir dados de backup associados à máquina virtual

Se você tiver optado por reter dados de backup associados à máquina virtual, você pode usar os dados de backup para restaurá-la. Para obter detalhes de preço dessas máquinas virtuais, clique [aqui](https://azure.microsoft.com/pricing/details/backup/).

Para interromper a proteção para uma máquina virtual:

1. Navegue até a página **Itens Protegidos**, selecione **Máquina virtual do Azure** como o tipo de filtro (se ainda não estiver selecionada) e clique no botão **Selecionar**.

    ![Tipo de VM](./media/backup-azure-manage-vms/vm-type.png)
2. Selecione a máquina virtual e clique em **Parar proteção** na parte inferior da página.

    ![Parar proteção](./media/backup-azure-manage-vms/stop-protection.png)
3. Por padrão, o Backup do Azure não exclui os dados de backup associados à máquina virtual.

    ![Confirmar a interrupção da proteção](./media/backup-azure-manage-vms/confirm-stop-protection.png)

    Se você deseja excluir os dados de backup, marque a caixa de seleção.

    ![Caixa de seleção](./media/backup-azure-manage-vms/checkbox.png)

    Selecione um motivo para interromper o backup. Embora seja opcional, fornecer um motivo ajudará o Backup do Azure a trabalhar com os comentários e priorizar os cenários do cliente.
4. Clique no botão **Enviar** para enviar o trabalho **Interromper proteção**. Clique em **Exibir trabalho** para ver o trabalho correspondente na página **Trabalhos**.

    ![Parar a proteção](./media/backup-azure-manage-vms/stop-protect-success.png)

    Se você não selecionou a opção **Excluir dados de backup associados** durante o assistente **Parar proteção**, poste a conclusão do trabalho, as alterações de status de proteção para **Proteção interrompida**. Os dados permanecem com o Backup do Azure até que sejam explicitamente excluídos. Você sempre pode excluir os dados selecionando a máquina virtual na página **Itens protegidos** e clicando em **Excluir**.

    ![Proteção interrompida](./media/backup-azure-manage-vms/protection-stopped-status.png)

    Se você tiver selecionado a opção **Excluir dados de backup associados**, a máquina virtual não fará parte da página **Itens Protegidos**.

## <a name="re-protect-virtual-machine"></a>Proteger novamente a Máquina virtual
Se você não selecionou a opção **Excluir dados de backup associados** em **Parar proteção**, pode proteger novamente a máquina virtual seguindo as etapas semelhantes para fazer backup de máquinas virtuais registradas. Depois de protegida, essa máquina virtual terá os dados de backup retidos antes da interrupção da proteção e os pontos de recuperação serão criados após a nova proteção.

Após proteger novamente, o status de proteção da máquina virtual será alterado para **Protegido** se houver pontos de recuperação anteriores a **Parar proteção**.

  ![Máquina virtual protegida novamente](./media/backup-azure-manage-vms/reprotected-status.png)

> [!NOTE]
> Ao proteger novamente a máquina virtual, você pode escolher uma política diferente da política com a qual a máquina virtual foi inicialmente protegida.
>
>

## <a name="unregister-virtual-machines"></a>Cancelar o registro das máquinas virtuais
Se você quiser remover a máquina virtual do cofre de backup:

1. Clique no botão **CANCELAR REGISTRO** , na parte inferior da página.

    ![Desabilitar a proteção](./media/backup-azure-manage-vms/unregister-button.png)

    Uma notificação do sistema será exibida na parte inferior da tela solicitando confirmação. Clique em **SIM** para continuar.

    ![Desabilitar a proteção](./media/backup-azure-manage-vms/confirm-unregister.png)

## <a name="delete-backup-data"></a>Excluir dados de backup
Você pode excluir os dados de backup associados a uma máquina virtual, ou:

* Durante a interrupção do trabalho da proteção
* Após uma interrupção no trabalho de proteção ser concluída em uma máquina virtual

Para excluir dados de backup em uma máquina virtual, que está no estado *Proteção Interrompida* , poste a conclusão bem-sucedida do trabalho em **Parar Backup** :

1. Navegue até a página **Itens Protegidos** e selecione **Máquina Virtual do Azure** como o *tipo* e clique no botão **Selecionar**.

    ![Tipo de VM](./media/backup-azure-manage-vms/vm-type.png)
2. Selecione a máquina virtual. A máquina virtual estará no estado **Proteção interrompida** .

    ![Proteção interrompida](./media/backup-azure-manage-vms/protection-stopped-b.png)
3. Clique no botão **EXCLUIR** na parte inferior da página.

    ![Excluir backup](./media/backup-azure-manage-vms/delete-backup.png)
4. No assistente **Excluir dados de backup**, selecione um motivo para a exclusão de dados de backup (altamente recomendado) e clique em **Enviar**.

    ![Excluir dados de backup](./media/backup-azure-manage-vms/delete-backup-data.png)
5. Isso criará um trabalho para excluir os dados de backup da máquina virtual selecionada. Clique em **Exibir trabalho** para ver o trabalho correspondente na página Trabalhos.

    ![Exclusão de dados bem-sucedida](./media/backup-azure-manage-vms/delete-data-success.png)

    Depois que o trabalho for concluído, a entrada correspondente à máquina virtual será removida da página **Itens protegidos** .

## <a name="dashboard"></a>Painel
Na página **Painel** , você pode examinar informações sobre máquinas virtuais do Azure, seu armazenamento e os trabalhos associados a elas nas últimas 24 horas. Você pode exibir o status de backup e quaisquer erros de backup associados.

![Painel](./media/backup-azure-manage-vms/dashboard-protectedvms.png)

> [!NOTE]
> Os valores no painel são atualizados a cada 24 horas.
>
>

## <a name="auditing-operations"></a>Operações de auditoria
O backup do Azure oferece análise dos “logs de operação” das operações de backup disparadas pelo cliente que facilita ver exatamente quais operações de gerenciamento foram realizadas no cofre de backup. Os logs de operações permitem um ótimo suporte a auditoria e análise posterior das operações de backup.

As seguintes operações são registradas nos logs de operação:

* Registrar
* Cancelar o registro 
* Configurar a proteção
* Backup (tanto as agendadas quanto as sob demanda por meio do BackupNow)
* Restaurar
* Parar a proteção
* Excluir dados de backup
* Adicionar política
* Excluir política
* Atualizar política
* Cancelar trabalho

Para exibir logs de operação correspondentes a um cofre de backup:

1. Navegue até **Serviços de gerenciamento** no portal do Azure e clique na guia **Logs de Operação**.

    ![Logs de operação](./media/backup-azure-manage-vms/ops-logs.png)
2. Em filtros, selecione **Backup** como *Tipo*, especifique o nome do cofre de backup em *nome do serviço* e clique em **Enviar**.

    ![Filtro de logs de operação](./media/backup-azure-manage-vms/ops-logs-filter.png)
3. Nos logs de operações, selecione qualquer operação e clique em **Detalhes** para ver os detalhes correspondentes a uma operação.

    ![Detalhes de busca de logs de operação](./media/backup-azure-manage-vms/ops-logs-details.png)

    O **Assistente de detalhes** contém informações sobre a operação disparada, a Id do trabalho, o recurso em que essa operação é disparada e a hora de início da operação.

    ![Detalhes da Operação](./media/backup-azure-manage-vms/ops-logs-details-window.png)

## <a name="alert-notifications"></a>Notificações de alerta
Você pode obter notificações de alerta personalizadas para os trabalhos no portal. Isso é possível ao definir regras de alerta do PowerShell baseadas em eventos de logs operacionais. É recomendável usar o *PowerShell versão 1.3.0 ou superior*.

Para definir uma notificação personalizada para o alerta de falhas de backup, um comando de exemplo terá a seguinte aparência:

```
PS C:\> $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail contoso@microsoft.com
PS C:\> Add-AzureRmLogAlertRule -Name backupFailedAlert -Location "East US" -ResourceGroup RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US -OperationName Microsoft.Backup/backupVault/Backup -Status Failed -TargetResourceId /subscriptions/86eeac34-eth9a-4de3-84db-7a27d121967e/resourceGroups/RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US/providers/microsoft.backupbvtd2/BackupVault/trinadhVault -Actions $actionEmail
```

**ResourceId**: você pode obter isso no pop-up Logs de Operações, conforme descrito na seção acima. O ResourceUri na janela pop-up de detalhes de uma operação é a ResourceId a ser fornecida para esse cmdlet.

**OperationName**: terá o formato "Microsoft.Backup/backupvault/<EventName> em que NomeDoEvento é uma das seguintes opções: Register,Unregister,ConfigureProtection,Backup,Restore,StopProtection,DeleteBackupData,CreateProtectionPolicy,DeleteProtectionPolicy,UpdateProtectionPolicy

**Status**: os valores com suporte são Started, Succeeded e Failed.

**ResourceGroup**: ResourceGroup do recurso em que a operação é disparada. Você pode obter isso do valor ResourceId. O valor entre os campos */resourceGroups/* e */providers/* em um valor ResourceId é o valor para ResourceGroup.

**Name**: nome da regra de alerta.

**CustomEmail**: especifica o endereço de email personalizado para o qual você deseja enviar a notificação de alerta

**SendToServiceOwners**: essa opção envia a notificação de alerta para todos os administradores e coadministradores da assinatura. Pode ser usado no cmdlet **New-AzureRmAlertRuleEmail**

### <a name="limitations-on-alerts"></a>Limitações sobre alertas
Os alertas baseados em eventos estão sujeitos às seguintes limitações:

1. Os alertas são disparados em todas as máquinas virtuais no cofre de backup. Você não pode personalizá-lo para obter alertas de um conjunto específico de máquinas virtuais em um cofre de backup.
2. Esse recurso está em Preview. [Saiba mais](../monitoring-and-diagnostics/insights-powershell-samples.md#create-metric-alerts)
3. Você receberá alertas de “alerts-noreply@mail.windowsazure.com”. No momento, você não pode modificar o remetente do email.

## <a name="next-steps"></a>Próximas etapas
* [Restaurar máquinas virtuais do Azure](backup-azure-restore-vms.md)
