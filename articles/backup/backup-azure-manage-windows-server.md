---
title: Gerenciar servidores e cofres dos Serviços de Recuperação do Microsoft Azure
description: Neste artigo, saiba como usar o painel Visão geral do cofre dos serviços de recuperação para monitorar e gerenciar seus cofres dos serviços de recuperação.
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: 74351d781287d863db8be0fc7d20517e0479106c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89002123"
---
# <a name="monitor-and-manage-recovery-services-vaults"></a>Monitorar e gerenciar cofres dos Serviços de Recuperação

Este artigo explica como usar o painel de **Visão geral** do cofre dos Serviços de Recuperação para monitorar e gerenciar os cofres dos Serviços de Recuperação. Quando você abre um cofre dos Serviços de Recuperação na lista, o painel de **Visão geral** do cofre selecionado será aberto. O painel fornece vários detalhes sobre o cofre. Há *blocos* que mostram: o status de alertas críticos e de aviso, trabalhos de backup em andamento e com falha e a quantidade de armazenamento com redundância local (LRS) e o armazenamento com redundância geográfica (GRS) usados. Se você fizer backup de VMs do Azure no cofre, o bloco [**Status da Pré-Verificação de Backup** exibe todos os itens críticos ou de aviso](#backup-pre-check-status). A imagem a seguir é o painel de **Visão geral** do **cofre da Contoso**. O bloco **Itens de Backup** mostra que existem nove itens registrados no cofre.

![Painel do cofre dos serviços de recuperação](./media/backup-azure-manage-windows-server/rs-vault-blade.png)

Os pré-requisitos para este artigo são: uma assinatura do Azure, um cofre dos serviços de recuperação e que há pelo menos um item de backup configurado para o cofre.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="open-a-recovery-services-vault"></a>Abrir um cofre dos Serviços de Recuperação

Para monitorar alertas ou exibir dados de gerenciamento sobre um cofre dos Serviços de Recuperação, abra o cofre.

1. Entre no [portal do Azure](https://portal.azure.com/) usando a assinatura do Azure.

2. No portal, selecione **Todos os serviços**.

   ![Abrir a lista de cofres dos Serviços de Recuperação, etapa 1](./media/backup-azure-manage-windows-server/open-rs-vault-list.png)

3. Na caixa de diálogo **Todos os Serviços**, digite **Serviços de Recuperação**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Quando a opção **cofres dos serviços de recuperação** for exibida, selecione-a para abrir a lista de cofres dos serviços de recuperação em sua assinatura.

    ![Criar cofre de serviços de recuperação-etapa 1](./media/backup-azure-manage-windows-server/list-of-rs-vaults.png) <br/>

4. Na lista de cofres, selecione um cofre para abrir seu painel de **visão geral** .

    ![Painel do cofre dos serviços de recuperação](./media/backup-azure-manage-windows-server/rs-vault-blade.png) <br/>

    O painel de Visão geral usa blocos para fornecer alertas e dados do trabalho de backup.

## <a name="monitor-backup-jobs-and-alerts"></a>Monitorar trabalhos e alertas de backup

O painel de **Visão geral** do cofre dos Serviços de Recuperação fornece os blocos para informações de Monitoramento e Uso. Os blocos na seção de Monitoramento exibem alertas Críticos e de Aviso e os trabalhos Em andamento e Com falha. Selecione um alerta ou trabalho específico para abrir o menu alertas de backup ou trabalhos de backup, filtrados para esse trabalho ou alerta.

![Tarefas do painel Backup](./media/backup-azure-manage-windows-server/monitor-dashboard-tiles-warning.png)

A seção de Monitoramento mostra os resultados das consultas de **Alertas de Backup** e **Trabalhos de Backup** predefinidas. Os blocos de Monitoramento fornecem informações atualizadas sobre:

* Alertas Críticos e de Aviso para trabalhos de Backup (nas últimas 24 horas)
* Status de pré-verificação para VMs do Azure. Para obter informações completas sobre o status de pré-verificação, consulte [status de pré-verificação de backup](#backup-pre-check-status).
* Os trabalhos de Backup em andamento e os trabalhos com falha (nas últimas 24 horas).

Os blocos de Uso fornecem:

* O número de itens de Backup configurados para o cofre.
* O armazenamento do Azure (separado por LRS e GRS) consumido pelo cofre.

Selecione os blocos (exceto armazenamento de backup) para abrir o menu associado. Na imagem acima, o bloco de Alertas de Backup mostra três alertas Críticos. A seleção da linha alertas críticos no bloco alertas de backup abre os alertas de backup filtrados para alertas críticos.

![Menu alertas de backup filtrado para alertas críticos](./media/backup-azure-manage-windows-server/critical-backup-alerts.png)

O menu Alertas de Backup, na imagem acima, está filtrado por: Status está ativo, Severidade é Crítica e Hora são as 24 horas anteriores.

### <a name="backup-pre-check-status"></a>Status de pré-verificação de backup

Verificações prévias de backup verificam a configuração de suas VMs para problemas que podem afetar negativamente os backups. Eles agregam essas informações para que você possa exibi-las diretamente do painel do cofre dos serviços de recuperação e fornecer recomendações para medidas corretivas, a fim de garantir o êxito dos backups consistentes com o arquivo ou consistente com o aplicativo. Eles não precisam de infraestrutura e não têm nenhum custo adicional.  

Verificações prévias de backup são executadas como parte das operações de backup agendadas para suas VMs do Azure. Eles terminam com um dos seguintes Estados:

* **Aprovado**: esse estado indica que a configuração da VM deve levar a backups bem-sucedidos e nenhuma ação corretiva precisa ser executada.
* **Aviso**: esse estado indica um ou mais problemas na configuração da VM que *podem* levar a falhas de backup. Ele fornece etapas *recomendadas* para garantir backups bem-sucedidos. Por exemplo, não ter o agente de VM mais recente instalado pode fazer com que os backups falhem intermitentemente. Essa situação fornecerá um estado de aviso.
* **Crítico**: esse estado indica um ou mais problemas críticos na configuração da *VM que* levará a falhas de backup e fornece as etapas *necessárias* para garantir os backups bem-sucedidos. Por exemplo, um problema de rede causado por uma atualização das regras NSG de uma VM fará com que os backups falhem, pois impede que a VM se comunique com o serviço de backup do Azure. Essa situação fornecerá um estado crítico.

Siga as etapas abaixo para iniciar a resolução de quaisquer problemas relatados pelas verificações prévias de backup para backups de VM no cofre dos serviços de recuperação.

* Selecione o bloco **status da pré-verificação de backup (VMS do Azure)** no painel do cofre dos serviços de recuperação.
* Selecione qualquer VM com um status de pré-verificação de backup de **crítico** ou de **aviso**. Essa ação abrirá o painel **detalhes da VM** .
* Selecione a notificação do painel na parte superior do painel para revelar a descrição do problema de configuração e as etapas corretivas.

## <a name="manage-backup-alerts"></a>Gerenciar alertas de Backup

Para acessar o menu alertas de backup, no menu cofre de serviços de recuperação, selecione **alertas de backup**.

![Alertas de Backup](./media/backup-azure-manage-windows-server/backup-alerts-menu.png)

O relatório de Alertas de Backup lista os alertas para o cofre.

![Relatório de alertas de backup](./media/backup-azure-manage-windows-server/backup-alerts.png)

### <a name="alerts"></a>Alertas

A lista de Alertas de Backup exibe as informações selecionadas para os alertas filtrados. No menu Alertas de Backup, é possível filtrar alertas Críticos ou de Aviso.

| Nível de alerta | Eventos que geram alertas |
| ----------- | ----------- |
| Crítico | Alertas críticos são recebidos quando: trabalhos de Backup falham, trabalhos de recuperação falham e quando você interrompe a proteção em um servidor, mas retém os dados.|
| Aviso | Você recebe alertas de aviso quando: trabalhos de backup são concluídos com avisos. Por exemplo, quando menos de 100 arquivos não são submetidos a backup devido a problemas de corrupção ou quando mais de 1 milhão arquivos são submetidos a backup com êxito). |
| Informativo | no momento, nenhum alerta informativo está em uso. |

### <a name="viewing-alert-details"></a>Exibir detalhes do alerta

O relatório de Alertas de Backup rastreia oito detalhes sobre cada alerta. Use o botão **Escolher colunas** para editar os detalhes no relatório.

![Botão alertas de backup escolher colunas](./media/backup-azure-manage-windows-server/backup-alerts.png)

Por padrão, todos os detalhes, exceto **Última hora da ocorrência**, são exibidos no relatório.

* Alerta
* Item de backup
* Servidor protegido
* Severidade
* Duration
* Hora de criação
* Status
* Última hora da ocorrência

### <a name="change-the-details-in-alerts-report"></a>Alterar os detalhes no relatório de alertas

1. Para alterar as informações do relatório, no menu **alertas de backup** , selecione **escolher colunas**.

   ![Selecione escolher colunas](./media/backup-azure-manage-windows-server/alerts-menu-choose-columns.png)

   O menu **Escolher colunas** será aberto.

2. No menu **Escolher colunas**, escolha os detalhes que você quer exibir no relatório.

    ![Menu Escolher colunas](./media/backup-azure-manage-windows-server/choose-columns-menu.png)

3. Selecione **concluído** para salvar as alterações e feche o menu escolher colunas.

   Se você fizer alterações, mas não quiser manter as alterações, selecione **Redefinir** para retornar a selecionada para a última configuração salva.

### <a name="change-the-filter-in-alerts-report"></a>Alterar o filtro no relatório de alertas

Use o menu **Filtro** para alterar a Severidade, Status, Hora de início e Hora de término dos alertas.

> [!NOTE]
> Editar o filtro de Alertas de Backup não altera os alertas Críticos ou de Aviso no painel de Visão geral do cofre.
>  

1. Para alterar o filtro de alertas de backup, no menu alertas de backup, selecione **Filtrar**.

   ![Escolher o menu Filtro](./media/backup-azure-manage-windows-server/alerts-menu-choose-filter.png)

   O menu Filtro é exibido.

   ![Menu Filtrar alerta](./media/backup-azure-manage-windows-server/filter-alert-menu.png)

2. Edite a gravidade, o status, a hora de início ou a hora de término e selecione **concluído** para salvar as alterações.

## <a name="configuring-notifications-for-alerts"></a>Configurar notificações para alertas

Configure notificações para gerar emails quando ocorrer um alerta Crítico ou de Aviso. É possível enviar alertas por email a cada hora, ou quando ocorre um alerta específico.

   ![Filtrar alertas](./media/backup-azure-manage-windows-server/configure-notification.png)

Por padrão, as notificações por email são **Ativadas**. Selecione **desativado** para interromper as notificações por email.

No controle **Notificar**, escolha **Por alerta** se não quiser agrupamento ou não tiver muitos itens que possam gerar alertas. Cada alerta resulta em uma notificação (a configuração padrão), e um email de resolução é enviado imediatamente.

Se você selecionar **Resumo Horário**, um email será enviado aos destinatários explicando os alertas não resolvidos gerados na última hora. Um email de resolução é enviado no final da hora.

Escolha a severidade do alerta (Crítico ou Aviso) usada para gerar o email. Atualmente, não há alertas de Informações.

## <a name="manage-backup-items"></a>Gerenciar itens de Backup

Um cofre dos Serviços de Recuperação contém muitos tipos de dados de backup. [Saiba mais](backup-overview.md#what-can-i-back-up) sobre o que você pode fazer backup. Para gerenciar os vários servidores, computadores, bancos de dados e cargas de trabalho, selecione o bloco **itens de backup** para exibir o conteúdo do cofre.

![Bloco Itens de backup](./media/backup-azure-manage-windows-server/backup-items.png)

A lista de Itens de Backup, organizados por tipo de gerenciamento de Backup, é aberta.

![Lista de itens de backup](./media/backup-azure-manage-windows-server/list-backup-items.png)

Para explorar um tipo específico de instância protegida, selecione o item na coluna tipo de gerenciamento de backup. Por exemplo, na imagem acima, há duas máquinas virtuais do Azure protegidas nesse cofre. Selecionar **máquina virtual do Azure**, abre a lista de máquinas virtuais protegidas neste cofre.

![Lista de máquinas virtuais protegidas](./media/backup-azure-manage-windows-server/list-of-protected-virtual-machines.png)

A lista de máquinas virtuais tem dados úteis: o Grupo de Recursos associados, [Pré-verificação de Backup](#backup-pre-check-status) anterior, último Status de Backup e a data do Ponto de Restauração mais recente. As reticências, na última coluna, abrem o menu para disparar tarefas comuns. Os dados úteis fornecidos nas colunas são diferentes para cada tipo de backup.

![Abrir menu de reticências para tarefas comuns](./media/backup-azure-manage-windows-server/ellipsis-menu.png)

## <a name="manage-backup-jobs"></a>Gerenciar trabalhos de Backup

O bloco **Trabalhos de Backup** no painel do cofre mostra o número de trabalhos que estão Em andamento ou Com falha nas últimas 24 horas. O bloco fornece uma breve visão sobre o menu Trabalhos de Backup.

![Bloco trabalhos traseiros](./media/backup-azure-manage-windows-server/backup-jobs-tile.png)

Para ver detalhes adicionais sobre os trabalhos, selecione **em andamento** ou **falha** ao abrir o menu trabalhos de backup filtrado para esse estado.

### <a name="backup-jobs-menu"></a>Menu Trabalhos de Backup

O menu **Trabalhos de Backup** exibe informações sobre o Tipo de item, Operação, Status, Hora de início e Duração.  

Para abrir o menu trabalhos de backup, no menu principal do cofre, selecione **trabalhos de backup**.

![Selecionar trabalhos de backup](./media/backup-azure-manage-windows-server/backup-jobs-menu-item.png)

A lista de Trabalhos de Backup é aberta.

![Lista de trabalhos de backup](./media/backup-azure-manage-windows-server/backup-jobs-list.png)

O menu Trabalhos de Backup mostra o status para todas as operações, em todos os tipos de backup, nas últimas 24 horas. Use **Filtro** para alterar os filtros. Os filtros são explicados nas seções a seguir.

Para alterar os filtros:

1. No menu trabalhos de backup do cofre, selecione **Filtrar**.

   ![Selecionar filtro para trabalhos de backup](./media/backup-azure-manage-windows-server/vault-backup-job-menu-filter.png)

    O menu Filtro é aberto.

   ![O menu Filtrar é aberto para trabalhos de backup](./media/backup-azure-manage-windows-server/filter-menu-backup-jobs.png)

2. Escolha as configurações de filtro e selecione **concluído**. A lista filtrada é atualizada com base nas novas configurações.

#### <a name="item-type"></a>Tipo de item

O Tipo de Item é o tipo de gerenciamento de backup da instância protegida. Há quatro tipos; consulte a lista a seguir. É possível exibir todos os tipos de item ou um tipo de item. Não é possível selecionar dois ou três tipos de item. Os tipos de Item disponíveis são:

* Todos os tipos de item
* Máquina virtual do Azure
* Arquivos e pastas
* Armazenamento do Azure
* Carga de trabalho do Azure

#### <a name="operation"></a>Operação

É possível exibir uma única operação ou todas as operações. Não é possível selecionar duas ou três operações. As Operações disponíveis são:

* Todas as operações
* Registre-se
* Configurar backup
* Backup
* Restaurar
* Desabilitar o backup
* Excluir dados de backup

#### <a name="status"></a>Status

É possível exibir o Status de todos os de um. Não é possível selecionar dois ou três status. Os status disponíveis são:

* Todos os status
* Concluído
* Em andamento
* Com falha
* Canceled
* Concluído com avisos

#### <a name="start-time"></a>Hora de início

O dia e a hora em que a consulta começa. O padrão é um período de 24 horas.

#### <a name="end-time"></a>Hora de término

O dia e a hora em que a consulta termina.

### <a name="export-jobs"></a>Exportar trabalhos

Use **Exportar trabalhos** para criar uma planilha que contém todas as informações do menu Trabalhos. A planilha eletrônica tem uma planilha que contém um resumo de todos os trabalhos e planilhas individuais para cada trabalho.

Para exportar as informações de trabalhos para uma planilha, selecione **Exportar trabalhos**. O serviço cria uma planilha usando o nome do cofre e a data, mas você pode alterar o nome.

## <a name="monitor-backup-usage"></a>Monitorar o uso do Backup

O bloco Armazenamento de Backup no painel mostra o armazenamento consumido no Azure. O uso do armazenamento é fornecido para:

* Uso do armazenamento LRS de nuvem associado ao cofre
* Uso do armazenamento GRS de nuvem associado ao cofre

## <a name="troubleshooting-monitoring-issues"></a>Solucionando problemas de monitoramento

**Problema:** Trabalhos e/ou alertas do agente de backup do Azure não aparecem no Portal.

**Etapas de solução de problemas:** o processo, ```OBRecoveryServicesManagementAgent```, envia os dados de alerta e de trabalho para o serviço de Backup do Azure. Ocasionalmente, esse processo pode ficar travado ou ser interrompido.

1. Para verificar se o processo não está em execução, abra o **Gerenciador de Tarefas** e verifique se ```OBRecoveryServicesManagementAgent``` está em execução.

2. Se o processo não estiver em execução, abra o **Painel de Controle** e navegue pela lista de serviços. Inicie ou reinicie o **agente de gerenciamento dos Serviços de Recuperação do Microsoft Azure**.

    Para saber mais, procure os logs em:<br/>
   `<AzureBackup_agent_install_folder>\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider*` Por exemplo:<br/>
   `C:\Program Files\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider0.errlog`

## <a name="next-steps"></a>Próximas etapas

* [Restaurar o Windows Server ou o Windows Client do Azure](backup-azure-restore-windows-server.md)
* Para saber mais sobre o Backup do Azure, confira [Visão geral do backup do Azure](./backup-overview.md)
