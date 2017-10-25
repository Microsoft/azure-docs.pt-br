---
title: "Monitorar os backups da máquina virtual implantados pelo Gerenciador de Recursos | Microsoft Docs"
description: "Monitorar eventos e alertas a partir dos backups da máquina virtual implantados pelo Resource Manager. Envie um email com base em alertas."
services: backup
documentationcenter: dev-center-name
author: markgalioto
manager: carmonm
editor: 
ms.assetid: fed32015-2db2-44f8-b204-d89f6fd1bea2
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2016
ms.author: markgal;trinadhk;giridham;
ms.openlocfilehash: b9dc3f52e5fc275bc56b9964f2115833f2dde42e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-alerts-for-azure-virtual-machine-backups"></a>Monitorar alertas para os backups das máquinas virtuais do Azure
Os alertas são respostas do serviço informando que um limite do evento foi atingido ou ultrapassado. Saber quando os problemas iniciam pode ser essencial para manter baixos os custos do negócio. Os alertas normalmente não ocorrem em um agendamento e é útil saber assim que possível após sua ocorrência. Por exemplo, quando um trabalho de backup ou de restauração falha, um alerta ocorre em até cinco minutos após a falha. No painel do cofre, o bloco Alertas de Backup exibe os eventos nos níveis Crítico e Aviso. Nas configurações Alertas de Backup, você pode exibir todos os eventos. Mas o que fazer se um alerta ocorrer quando você estiver trabalhando em um problema separado? Se você não sabe quando o alerta ocorre, pode ser uma inconveniência secundária ou pode comprometer os dados. Para verificar se as pessoas corretas estão cientes de um alerta, quando ele ocorre, configure o serviço para enviar notificações de alerta por email. Para obter detalhes sobre como configurar as notificações por email, confira [Configurar notificações](backup-azure-monitor-vms.md#configure-notifications).

## <a name="how-do-i-find-information-about-the-alerts"></a>Como localizar informações sobre os alertas?
Para exibir informações sobre o evento que gerou um alerta, você deve abrir a folha Alertas de Backup. Há duas maneiras de abrir a folha Alertas de Backup: no bloco Alertas de Backup no painel do cofre ou na folha Alertas e Eventos.

Para abrir a folha Alertas de Backup no bloco Alertas de Backup:

* No bloco **Alertas de Backup** no painel do cofre, clique em **Crítico** ou **Aviso** para exibir os eventos operacionais desse nível de gravidade.

    ![Bloco Alertas de Backup](./media/backup-azure-monitor-vms/backup-alerts-tile.png)

Para abrir a folha Alertas de Backup no bloco Alertas e Eventos:

1. No painel do cofre, clique em **Todas as Configurações**. ![Botão Todas as Configurações](./media/backup-azure-monitor-vms/all-settings-button.png)
2. Na folha **Configurações**, clique em **Alertas e Eventos**. ![Botão Alertas e Eventos](./media/backup-azure-monitor-vms/alerts-and-events-button.png)
3. Na folha **Alertas e Eventos**, clique em **Alertas de Backup**. ![Botão Alertas de Backup](./media/backup-azure-monitor-vms/backup-alerts.png)

    A folha **Alertas de Backup** é aberta e exibe os alertas filtrados.

    ![Bloco Alertas de Backup](./media/backup-azure-monitor-vms/backup-alerts-critical.png)
4. Para exibir informações detalhadas sobre um evento específico, na lista de eventos, clique no alerta para abrir sua folha **Detalhes** .

    ![Detalhe do Evento](./media/backup-azure-monitor-vms/audit-logs-event-detail.png)

    Para personalizar os atributos exibidos na lista, confira [Exibir atributos adicionais do evento](backup-azure-monitor-vms.md#view-additional-event-attributes)

## <a name="configure-notifications"></a>Configurar notificações
 Você pode configurar o serviço para enviar notificações por email para os alertas que ocorreram durante a última hora ou quando ocorrem determinados tipos de eventos.

Para configurar as notificações por email para alertas

1. No menu Alertas de Backup, clique em **Configurar notificações**

    ![Menu Alertas de Backup](./media/backup-azure-monitor-vms/backup-alerts-menu.png)

    A folha Configurar notificações é aberta.

    ![Folha Configurar notificações](./media/backup-azure-monitor-vms/configure-notifications.png)
2. Na folha Configurar notificações, para as Notificações por email, clique em **Ativar**.

    As caixas de diálogo Destinatários e Gravidade têm uma estrela ao lado porque essa informação é necessária. Forneça pelo menos um endereço de email e selecione pelo menos uma Gravidade.
3. Na caixa de diálogo **Destinatários (Email)** , digite os endereços de email para quem recebe as notificações. Use o formato: username@domainname.com. Separe os vários endereços de email com um ponto e vírgula (;).
4. Na área **Notificar**, escolha **Por Alerta** para enviar uma notificação quando ocorrer o alerta especificado ou **Consumir por Hora** para enviar um resumo da última hora.
5. Na caixa de diálogo **Gravidade** , escolha um ou mais níveis que você deseja para disparar a notificação por email.
6. Clique em **Salvar**.

   ### <a name="what-alert-types-are-available-for-azure-iaas-vm-backup"></a>Quais tipos de alertas estão disponíveis para o backup da VM IaaS do Azure?
   | Nível de alerta | Alertas enviados |
   | --- | --- |
   | Crítico |Falha de backup, falha na recuperação |
   | Aviso |Nenhum |
   | Informativo |Nenhum |

### <a name="are-there-situations-where-email-isnt-sent-even-if-notifications-are-configured"></a>Há situações em que o email não será enviado mesmo se as notificações estiverem configuradas?
Há situações em que um alerta não é enviado, mesmo que as notificações tenham sido corretamente configuradas. Nas situações a seguir, notificações por email não serão enviadas para evitar ruídos de alerta:

* Se as notificações forem configuradas como Resumo de Hora em Hora e se um alerta for gerado e resolvido em uma hora.
* O Trabalho é cancelado.
* Um trabalho de backup é disparado e falha e outro trabalho de backup está em andamento.
* Um trabalho de backup agendado para uma VM habilitada para o Resource Manager, mas a VM não existe mais.

## <a name="customize-your-view-of-events"></a>Personalizar a exibição de eventos
A configuração **Logs de auditoria** vem com um conjunto predefinido de filtros e colunas mostrando as informações de evento operacional. Você pode personalizar a exibição para que, quando a folha **Eventos** abrir, ela mostre as informações desejadas.

1. No [painel do cofre](backup-azure-manage-vms.md#open-a-recovery-services-vault-in-the-dashboard), procure e clique em **Logs de Auditoria** para abrir a folha **Eventos**.

    ![Logs de Auditoria](./media/backup-azure-monitor-vms/audit-logs-1606-1.png)

    A folha **Eventos** será aberta para os eventos operacionais filtrados apenas para o cofre atual.

    ![Filtro de Logs da Auditoria](./media/backup-azure-monitor-vms/audit-logs-filter.png)

    A folha mostra a lista de eventos Crítico, Erro, Aviso e Informativo na última semana. O período de tempo é um valor padrão definido em **Filtrar**. A folha **Eventos** também mostra um gráfico de barras controlando quando os eventos ocorreram. Se você não quiser ver o gráfico de barras, no menu **Eventos**, clique em **Ocultar gráfico** para desativar o gráfico. A exibição padrão dos Eventos mostra informações sobre a Operação, Nível, Status, Recurso e Tempo. Para obter informações sobre como expor os atributos adicionais do Evento, confira a seção [expandindo as informações do Evento](backup-azure-monitor-vms.md#view-additional-event-attributes).
2. Para obter mais informações sobre um evento operacional, na coluna **Operação** , clique em um evento operacional para abrir sua folha. A folha contém informações detalhadas sobre os eventos. Os eventos são agrupados por sua ID de correlação e uma lista de eventos ocorridos no Período de tempo.

    ![Detalhes da Operação](./media/backup-azure-monitor-vms/audit-logs-details-window.png)
3. Para exibir informações detalhadas sobre um evento específico, na lista de eventos, clique no evento para abrir sua folha **Detalhes** .

    ![Detalhe do Evento](./media/backup-azure-monitor-vms/audit-logs-details-window-deep.png)

    As informações no nível do Evento são tão detalhadas quanto as informações obtidas. Se você preferir ver muitas informações sobre cada evento e quiser adicionar muitos detalhes à folha **Eventos** , confira a seção [expandindo as Informações do evento](backup-azure-monitor-vms.md#view-additional-event-attributes).

## <a name="customize-the-event-filter"></a>Personalizar o filtro de eventos
Use o **Filtro** para ajustar ou escolher as informações que aparecem em uma folha específica. Para filtrar as informações do evento:

1. No [painel do cofre](backup-azure-manage-vms.md#open-a-recovery-services-vault-in-the-dashboard), procure e clique em **Logs de Auditoria** para abrir a folha **Eventos**.

    ![Logs de Auditoria](./media/backup-azure-monitor-vms/audit-logs-1606-1.png)

    A folha **Eventos** será aberta para os eventos operacionais filtrados apenas para o cofre atual.

    ![Filtro de Logs da Auditoria](./media/backup-azure-monitor-vms/audit-logs-filter.png)
2. No menu **Eventos**, clique em **Filtro** para abrir essa folha.

    ![abrir folha do filtro](./media/backup-azure-monitor-vms/audit-logs-filter-button.png)
3. Na folha **Filtrar**, ajuste os filtros **Nível**, **Período de tempo** e **Chamador**. Os outros filtros não estão disponíveis depois que foram definidos para fornecer as informações atuais para o cofre dos Serviços de Recuperação.

    ![Detalhes da consulta dos Logs de Auditoria](./media/backup-azure-monitor-vms/filter-blade.png)

    Você pode especificar o **Nível** do evento: Crítico, Erro, Aviso ou Informativo. Você pode escolher qualquer combinação de Níveis de evento, mas deve ter, pelo menos, um Nível selecionado. Ative ou desative o Nível. O filtro **Período de tempo** permite especificar o período de tempo para capturar os eventos. Se você usar um Período personalizado, pode definir as horas de início e término.
4. Quando você estiver pronto para consultar os logs de operações usando o filtro, clique em **Atualizar**. Os resultados serão exibidos na folha **Eventos** .

    ![Detalhes da Operação](./media/backup-azure-monitor-vms/edited-list-of-events.png)

### <a name="view-additional-event-attributes"></a>Exibir atributos adicionais do evento
Usando o botão **Colunas**, você pode habilitar os atributos de eventos adicionais para que apareçam na lista na folha **Eventos**. A lista padrão de eventos exibe informações para a Operação, Nível, Status, Recurso e Tempo. Para habilitar os atributos adicionais:

1. Na folha **Eventos**, clique em **Colunas**.

    ![Abrir Colunas](./media/backup-azure-monitor-vms/audi-logs-column-button.png)

    A folha **Escolher colunas** será aberta.

    ![Folha Colunas](./media/backup-azure-monitor-vms/columns-blade.png)
2. Para selecionar o atributo, clique na caixa de seleção. A caixa de seleção do atributo é ativada e desativada.
3. Clique em **Redefinir** para redefinir a lista de atributos na folha **Eventos**. Depois de adicionar ou remover os atributos da lista, use **Redefinir** para exibir a nova lista de atributos do Evento.
4. Clique em **Atualizar** para atualizar os dados nos atributos do Evento. A tabela a seguir fornece informações sobre cada atributo.

| Nome da coluna | Descrição |
| --- | --- |
| Operação |O nome da operação |
| Nível |No nível da operação, os valores podem ser: Informativo, Aviso, Erro ou Crítico |
| Status |Estado descritivo da operação |
| Recurso |A URL que identifica o recurso; também conhecida como ID do recurso |
| Hora |Hora, medida a partir da hora atual, quando o evento ocorreu |
| Chamador |Quem ou o que chamou ou disparou o evento; pode ser o sistema ou um usuário |
| Timestamp |A hora em que o evento foi disparado |
| Grupo de recursos |O grupo de recursos associados |
| Tipo de recurso |O tipo de recurso interno usado pelo Gerenciador de Recursos |
| ID da assinatura |A ID da assinatura associada |
| Categoria |Categoria do evento |
| ID de Correlação |ID comum para eventos relacionados |

## <a name="use-powershell-to-customize-alerts"></a>Usar o PowerShell para personalizar alertas
Você pode obter notificações de alerta personalizadas para os trabalhos no portal. Para obter esses trabalhos, defina regras de alerta baseadas no PowerShell nos eventos de logs operacionais. Use o *PowerShell versão 1.3.0 ou posterior*.

Para definir uma notificação personalizada para alertar sobre as falhas de backup, use um comando como o script a seguir:

```
PS C:\> $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail contoso@microsoft.com
PS C:\> Add-AzureRmLogAlertRule -Name backupFailedAlert -Location "East US" -ResourceGroup RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US -OperationName Microsoft.RecoveryServices/recoveryServicesVault/Backup -Status Failed -TargetResourceId /subscriptions/86eeac34-eth9a-4de3-84db-7a27d121967e/resourceGroups/RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US/providers/Microsoft.RecoveryServices/vaults/trinadhVault -Actions $actionEmail
```

**ResourceId** : você pode obter o ResourceId dos Logs de Auditoria. O ResourceId é uma URL fornecida na coluna Recurso dos Logs de Operação.

**OperationName** : OperationName está no formato "Microsoft.RecoveryServices/recoveryServicesVault/*EventName*", em que *EventName* pode ser:<br/>

* Registrar <br/>
* Cancelar o registro  <br/>
* Configurar Proteção  <br/>
* Backup  <br/>
* Restaurar  <br/>
* Parar Proteção  <br/>
* Excluir Dados de Backup  <br/>
* Criar Política de Proteção  <br/>
* Excluir Política de Proteção  <br/>
* Atualizar Política de Proteção  <br/>

**Status** : os valores com suporte são Iniciado, Êxito ou Falha.

**ResourceGroup** : esse é o Grupo de Recursos ao qual o recurso pertence. Você pode adicionar a coluna Grupo de Recursos aos logs gerados. O Grupo de Recursos é um dos tipos disponíveis das informações dos eventos.

**Name** : O nome da Regra de Alerta.

**CustomEmail** : especifica o endereço de email personalizado para o qual você deseja enviar a notificação de alerta

**SendToServiceOwners** : essa opção envia notificações de alerta para todos os administradores e coadministradores da assinatura. Pode ser usado no cmdlet **New-AzureRmAlertRuleEmail**

### <a name="limitations-on-alerts"></a>Limitações sobre alertas
Os alertas baseados em eventos estão sujeitos às seguintes limitações:

1. Os alertas são disparados em todas as máquinas virtuais no cofre dos Serviços de Recuperação. Não é possível personalizar o alerta para um subconjunto de máquinas virtuais em um cofre dos Serviços de Recuperação.
2. Esse recurso está na Visualização. [Saiba mais](../monitoring-and-diagnostics/insights-powershell-samples.md#create-metric-alerts)
3. Os alertas são enviados de “alerts-noreply@mail.windowsazure.com”. No momento, você não pode modificar o remetente do email.

## <a name="next-steps"></a>Próximas etapas
Os logs de eventos permitem um ótimo post-mortem e suporte de auditoria para as operações de backup. As seguintes operações são registradas:

* Registrar 
* Cancelar o registro 
* Configurar a proteção
* Backup (backup agendado ou sob demanda)
* Restaurar 
* Parar a proteção
* Excluir dados de backup
* Adicionar política
* Excluir política
* Atualizar política
* Cancelar trabalho

Para obter uma explicação abrangente dos eventos, operações e os logs de auditoria entre os serviços do Azure, confira o artigo [Exibir eventos e logs de auditoria](../monitoring-and-diagnostics/insights-debugging-with-events.md).

Para obter informações sobre como recriar uma máquina virtual a partir de um ponto de recuperação, verifique [Restaurar VMs do Azure](backup-azure-restore-vms.md). Se você precisar de informações sobre como proteger suas máquinas virtuais, consulte [Primeira consideração: fazer backup das VMs em um cofre dos Serviços de Recuperação](backup-azure-vms-first-look-arm.md). Saiba mais sobre as tarefas de gerenciamento para backups da VM no artigo [Gerenciar backups da máquina virtual do Azure](backup-azure-manage-vms.md).
