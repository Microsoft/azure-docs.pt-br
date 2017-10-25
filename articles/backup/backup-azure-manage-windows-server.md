---
title: "Gerenciar servidores e cofres dos serviços de recuperação do Azure | Microsoft Docs"
description: "Use este tutorial para aprender a gerenciar os servidores e cofres dos serviços de recuperação do Azure."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: tysonn
ms.assetid: 4eea984b-7ed6-4600-ac60-99d2e9cb6d8a
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: markgal
ms.openlocfilehash: 5922e308f5c205a07bd329c28322ae82cea0e1fa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-and-manage-azure-recovery-services-vaults-and-servers-for-windows-machines"></a>Monitore e gerencie os cofres dos serviços de recuperação do Azure e os servidores para os computadores que usam o Windows
> [!div class="op_single_selector"]
> * [Gerenciador de Recursos](backup-azure-manage-windows-server.md)
> * [Clássico](backup-azure-manage-windows-server-classic.md)
>
>

Neste artigo, você encontra uma visão geral das tarefas de gerenciamento e monitoramento de backup disponíveis no Portal do Azure e o agente de Backup do Microsoft Azure. Este artigo pressupõe que você já tem uma assinatura do Azure e já criou pelo menos um cofre dos Serviços de Recuperação.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]


## <a name="open-a-recovery-services-vault"></a>Abrir um cofre dos Serviços de Recuperação

O painel do cofre dos Serviços de Recuperação mostra os detalhes ou atributos de um cofre dos Serviços de Recuperação.

1. Entre no [Portal do Azure](https://portal.azure.com/) usando sua assinatura do Azure.
2. No menu Hub, clique em **Mais Serviços**.

    ![Abrir a lista de cofres dos Serviços de Recuperação, etapa 1](./media/backup-azure-manage-windows-server/open-rs-vault-list.png) <br/>

3. Convém abrir um cofre dos Serviços de Recuperação. Na caixa de diálogo, comece a digitar **Serviços de Recuperação**. Quando você começar a digitar, a lista será filtrada com base em sua entrada. Clique em **Cofres dos Serviços de Recuperação** para exibir a lista de cofres dos Serviços de Recuperação em sua assinatura.

    ![Criar Cofre de Serviços de Recuperação - etapa 1](./media/backup-azure-manage-windows-server/browse-to-rs-vaults-2.png) <br/>

    A lista de cofres dos Serviços de Recuperação será aberta.

    ![Criar Cofre de Serviços de Recuperação - etapa 1](./media/backup-azure-manage-windows-server/list-of-rs-vaults.png) <br/>

4. Na lista de cofres, selecione o nome do cofre dos Serviços de Recuperação que você deseja abrir. A folha de painel do cofre dos Serviços de Recuperação é aberta.

    ![painel do cofre dos serviços de recuperação](./media/backup-azure-manage-windows-server/rs-vault-blade.png) <br/>

    Agora que você abriu o cofre dos Serviços de Recuperação, experimente qualquer uma das tarefas de monitoramento ou gerenciamento.

## <a name="monitor-backup-jobs-and-alerts"></a>Monitorar trabalhos e alertas de backup

Você monitora os trabalhos e alertas no painel do cofre dos Serviços de Recuperação, onde vê:

* Detalhes dos alertas de backup
* Arquivos e pastas, bem como máquinas virtuais do Azure protegidas na nuvem
* Armazenamento total consumido no Azure
* Status do trabalho de backup

![Tarefas do painel Backup](./media/backup-azure-manage-windows-server/dashboard-tiles.png)

Clicar nas informações em cada um desses blocos abrirá a folha associada onde você gerencia as tarefas relacionadas.

Na parte superior do Painel:

* As configurações fornecem acesso às tarefas de backup disponíveis.
* Backup - ajuda a fazer backup de novos arquivos e pastas (ou VMs do Azure) no cofre dos Serviços de Recuperação.
* Excluir - se um cofre dos serviços de recuperação ainda não estiver sendo usado, você poderá exclui-lo para liberar espaço de armazenamento. A exclusão é habilitada somente depois que todos os servidores protegidos foram excluídos do cofre.

![Tarefas do painel Backup](./media/backup-azure-manage-windows-server/dashboard-tasks.png)

## <a name="alerts-for-backups-using-azure-backup-agent"></a>Alertas de backups usando o agente de backup do Azure:
| Nível de alerta | Alertas enviados |
| --- | --- |
| Crítico |Falha de backup, falha na recuperação |
| Aviso |Backup foi concluído com avisos (quando menos de centenas de arquivos ficam sem backup devido a problemas de corrupção e mais de um milhão de arquivos têm o backup feito com êxito) |
| Informativo |Nenhum |

## <a name="manage-backup-alerts"></a>Gerenciar alertas de Backup
Clique no bloco **Alertas de Backup** para abrir a folha **Alertas de Backup** e gerenciar os alertas.

![Alertas de Backup](./media/backup-azure-manage-windows-server/manage-backup-alerts.png)

O bloco Alertas de Backup mostra o número de:

* alertas críticos não resolvidos nas últimas 24 horas
* alertas de aviso não resolvidos nas últimas 24 horas

Clicar em cada um desses links leva você para a folha **Alertas de Backup** com uma exibição filtrada desses alertas (crítico ou aviso).

Na folha Alertas de Backup, você:

* Escolhe as informações apropriadas para incluir com seus alertas.

    ![Escolher colunas](./media/backup-azure-manage-windows-server/choose-alerts-colunms.png)
* Filtra os alertas quanto à gravidade, status e horários de início/término.

    ![Filtrar alertas](./media/backup-azure-manage-windows-server/filter-alerts.png)
* Configura as notificações quanto à gravidade, frequência e destinatários, bem como ativa ou desativa os alertas.

    ![Filtrar alertas](./media/backup-azure-manage-windows-server/configure-notifications.png)

Se **Por Alerta** estiver selecionado como a frequência para **Notificar**, não ocorrerá nenhum agrupamento ou redução nos emails. Cada alerta resulta em uma notificação. Essa é a configuração padrão e o email de resolução também é enviado imediatamente.

Se **Consumir por Hora** estiver selecionado como a frequência para **Notificar**, um email será enviado para o usuário informando que há novos alertas não resolvidos gerados na última hora. Um email de resolução é enviado no final da hora.

Os alertas podem ser enviados para os seguintes níveis de gravidade:

* Crítico
* Aviso
* informações

Você desativa o alerta com o botão **desativar** na folha de detalhes do trabalho. Quando você clica em desativar, pode fornecer observações de resolução.

Você escolhe as colunas que deseja que apareça como parte do alerta com o botão **Escolher colunas** .

> [!NOTE]
> Na folha **Configurações**, você gerencia os alertas de backup selecionando **Monitoramento e Relatórios > Alertas e Eventos > Alertas de Backup**, em seguida, clicando em **Filtro** ou **Configurar Notificações**.
>
>

## <a name="manage-backup-items"></a>Gerenciar itens de Backup
O gerenciamento de backups locais agora está disponível no portal de gerenciamento. Na seção Backup do painel, o bloco **Itens de Backup** mostra o número de itens de backup protegidos no cofre.

Clique em **Pastas de Arquivos** no bloco Itens de Backup.

![Bloco Itens de backup](./media/backup-azure-manage-windows-server/backup-items-tile.png)

A folha Itens de Backup abre com o filtro definido para Pasta de Arquivos onde você vê cada item de backup específico listado.

![Itens de Backup](./media/backup-azure-manage-windows-server/backup-item-list.png)

Se você selecionar um item de backup específico na lista, verá os detalhes essenciais desse item.

> [!NOTE]
> Na folha **Configurações**, você gerencia os arquivos e pastas selecionando **Itens Protegidos > Itens de Backup**, em seguida, selecionando **Pastas de Arquivos** no menu suspenso.
>
>

![Itens de backup das configurações](./media/backup-azure-manage-windows-server/backup-files-and-folders.png)

## <a name="manage-backup-jobs"></a>Gerenciar trabalhos de Backup
Os trabalhos de Backup para os backups locais (quando o servidor local está fazendo backup no Azure) e do Azure são visíveis no painel.

Na seção Backup do painel, o bloco Trabalho de Backup mostra o número de trabalhos:

* em andamento
* com falha nas últimas 24 horas

Para gerenciar seus trabalhos de backup, clique no bloco **Trabalhos de Backup** , que abre a folha Trabalhos de Backup.

![Itens de backup das configurações](./media/backup-azure-manage-windows-server/backup-jobs.png)

Você modifica as informações disponíveis na folha Trabalhos de Backup com o botão **Escolher colunas** na parte superior da página.

Use o botão **Filtro** para selecionar entre arquivos e pastas e o backup da máquina virtual do Azure.

Se você não vir os arquivos e pastas com backup, clique no botão **Filtrar** na parte superior da página e selecione **Arquivos e pastas** no menu Tipo de Item.

> [!NOTE]
> Na folha **Configurações**, você gerencia os trabalhos de backup selecionando **Monitoramento e Relatórios > Trabalhos > Trabalhos de Backup**, em seguida, selecionando **Pastas de Arquivos** no menu suspenso.
>
>

## <a name="monitor-backup-usage"></a>Monitorar o uso do Backup
Na seção Backup do painel, o bloco Uso do Backup mostra o armazenamento consumido no Azure. O uso do armazenamento é fornecido para:

* Uso do armazenamento LRS de nuvem associado ao cofre
* Uso do armazenamento GRS de nuvem associado ao cofre

## <a name="manage-your-production-servers"></a>Gerenciar seus servidores de produção
Para gerenciar seus servidores de produção, clique em **Configurações**.

Em Gerenciar, clique em **Infraestrutura do Backup > Servidores de Produção**.

A folha Servidores de Produção lista todos os servidores de produção disponíveis. Clique em um servidor na lista para abrir os detalhes do servidor.

![Itens protegidos](./media/backup-azure-manage-windows-server/production-server-list.png)


## <a name="open-the-azure-backup-agent"></a>Abrir o agente de Backup do Azure
Abra o **agente de Backup do Microsoft Azure** (você o localiza procurando *Backup do Microsoft Azure*em seu computador).

![Agendar um Backup do Windows Server](./media/backup-azure-manage-windows-server/snap-in-search.png)

Nas **Ações** disponíveis à direita do console do agente de backup, você executa as seguintes tarefas de gerenciamento:

* Registrar Servidor
* Agendar backup
* Fazer backup agora
* Alterar propriedades

![Ações do console do agente de Backup do Microsoft Azure](./media/backup-azure-manage-windows-server/console-actions.png)

> [!NOTE]
> Para **Recuperar Dados**, consulte [Restaurar arquivos para um computador cliente Windows ou um servidor Windows](backup-azure-restore-windows-server.md).
>
>

## <a name="modify-the-backup-schedule"></a>Modificar o agendamento de backup
1. No agente de Backup do Microsoft Azure, clique em **Agendar Backup**.

    ![Agendar um Backup do Windows Server](./media/backup-azure-manage-windows-server/schedule-backup.png)
2. No **Assistente de Agendamento de Backup**, deixe a opção **Fazer alterações aos itens ou horários de backup** selecionada e clique em **Avançar**.

    ![Agendar um Backup do Windows Server](./media/backup-azure-manage-windows-server/modify-or-stop-a-scheduled-backup.png)
3. Se quiser adicionar ou alterar itens, na tela **Selecionar Itens para Backup**, clique em **Adicionar Itens**.

    Você também pode definir **Configurações de Exclusão** nesta página do assistente. Se você quiser excluir arquivos ou tipos de arquivo, leia o procedimento para adicionar [configurações de exclusão](#manage-exclusion-settings).
4. Selecione os arquivos e as pastas dos quais você deseja fazer backup e clique em **OK**.

    ![Agendar um Backup do Windows Server](./media/backup-azure-manage-windows-server/add-items-modify.png)
5. Especifique o **agendamento de backup** e clique em **Avançar**.

    Você pode agendar backups diários (no máximo três vezes por dia) ou backups semanais.

    ![Itens para o backup do Windows Server](./media/backup-azure-manage-windows-server/specify-backup-schedule-modify-close.png)

   > [!NOTE]
   > A especificação do agendamento de backup é explicada em detalhes neste [artigo](backup-azure-backup-cloud-as-tape.md).
   >

6. Selecione a **Política de Retenção** para a cópia de backup e clique em **Avançar**.

    ![Itens para o backup do Windows Server](./media/backup-azure-manage-windows-server/select-retention-policy-modify.png)
7. Na tela **Confirmação**, examine as informações e clique em **Concluir**.
8. Depois que o assistente terminar de criar o **agendamento de backup**, clique em **Fechar**.

    Depois de modificar a proteção, é possível confirmar se os backups estão sendo acionados corretamente acessando a guia **Trabalhos** e confirmando se as alterações são refletidas nos trabalhos de backup.

## <a name="enable-network-throttling"></a>Habilitar a limitação de rede

O agente de Backup do Azure fornece uma guia Limitação, que permite controlar como a largura de banda é usada durante a transferência de dados. Esse controle pode ser útil se você precisa fazer backup de dados durante o horário de expediente, mas não quer que o processo de backup interfira no outro tráfego de Internet. A limitação da transferência de dados aplica-se a atividades de backup e restauração.  

Para habilitar a limitação:

1. No **agente de backup**, clique em **Alterar Propriedades**.
2. Na guia **Limitação, selecione **Habilitar limitação de uso de largura de banda da Internet para operações de backup**.

    ![Limitação de rede](./media/backup-azure-manage-windows-server/throttling-dialog.png)

    Depois de habilitar a limitação, especifique a largura de banda permitida para a transferência de dados de backup durante as **Horas úteis** e **Horas não úteis**.

    Os valores de largura de banda começam em 512 quilobytes por segundo (Kbps) e podem ir até 1023 megabytes por segundo (Mbps). Você também pode indicar o início e o término das **Horas úteis**e quais dias da semana são considerados dias úteis. O tempo fora das Horas úteis indicadas é considerado como hora não útil.
3. Clique em **OK**.

## <a name="manage-exclusion-settings"></a>Gerenciar configurações de exclusão
1. Abra o **agente de Backup do Microsoft Azure** (você poderá localizá-lo procurando *Backup do Microsoft Azure*em seu computador).

    ![Agendar um Backup do Windows Server](./media/backup-azure-manage-windows-server/snap-in-search.png)
2. No agente de Backup do Microsoft Azure, clique em **Agendar Backup**.

    ![Agendar um Backup do Windows Server](./media/backup-azure-manage-windows-server/schedule-backup.png)
3. No Assistente de Agendamento de Backup, deixe a opção **Fazer alterações aos itens ou horários de backup** selecionada e clique em **Avançar**.

    ![Agendar um Backup do Windows Server](./media/backup-azure-manage-windows-server/modify-or-stop-a-scheduled-backup.png)
4. Clique em **Configurações de Exclusões**.

    ![Agendar um Backup do Windows Server](./media/backup-azure-manage-windows-server/exclusion-settings.png)
5. Clique em **Adicionar Exclusão**.

    ![Agendar um Backup do Windows Server](./media/backup-azure-manage-windows-server/add-exclusion.png)
6. Selecione o local e clique em **OK**.

    ![Agendar um Backup do Windows Server](./media/backup-azure-manage-windows-server/exclusion-location.png)
7. Adicione a extensão de arquivo no campo **Tipo de Arquivo** .

    ![Agendar um Backup do Windows Server](./media/backup-azure-manage-windows-server/exclude-file-type.png)

    Adição de uma extensão .mp3

    ![Agendar um Backup do Windows Server](./media/backup-azure-manage-windows-server/exclude-mp3.png)

    Para adicionar outra extensão, clique em **Adicionar Exclusão** e insira outra extensão do tipo de arquivo (adicionando uma extensão .jpeg).

    ![Agendar um Backup do Windows Server](./media/backup-azure-manage-windows-server/exclude-jpg.png)
8. Quando tiver adicionado todas as extensões, clique em **OK**.
9. Prossiga com o Assistente de Agendamento de Backup clicando em **Avançar** até a **página Confirmação** e clique em **Concluir**.

    ![Agendar um Backup do Windows Server](./media/backup-azure-manage-windows-server/finish-exclusions.png)

## <a name="frequently-asked-questions"></a>Perguntas frequentes
**P1. O status do trabalho de backup mostra concluído no agente de backup do Azure. Por que isso não é refletido imediatamente no portal?**

R1. Há, no máximo, um atraso de 15 minutos entre o status do trabalho de backup refletido no agente de backup do Azure e o portal do Azure.

**P2. Quando um trabalho de backup falha, quanto tempo demora para gerar um alerta?**

R2. Um alerta é gerado dentro de 20 minutos na falha do backup do Azure.

**P3. Há um caso em que um email não será enviado se as notificações forem configuradas?**

R3. A seguir, os casos quando a notificação não será enviada para reduzir o ruído de alerta:

* Se as notificações forem configuradas por hora e um alerta for gerado e resolvido em uma hora
* o Trabalho será cancelado.
* O segundo trabalho de backup falhou porque o trabalho de backup original estava em andamento.

## <a name="troubleshooting-monitoring-issues"></a>Solucionando problemas de monitoramento
**Problema:** trabalhos e/ou alertas do agente de Backup do Azure não aparecem no portal.

**Etapas de solução de problemas:** o processo, ```OBRecoveryServicesManagementAgent```, envia os dados de alerta e de trabalho para o serviço de Backup do Azure. Ocasionalmente, esse processo pode ficar travado ou ser interrompido.

1. Para verificar se o processo não está em execução, abra **Gerenciador de Tarefas** e verifique se o processo ```OBRecoveryServicesManagementAgent``` está em execução.
2. Supondo que o processo não esteja em execução, abra o **Painel de Controle** e navegue pela lista de serviços. Inicie ou reinicie o **agente de gerenciamento dos Serviços de Recuperação do Microsoft Azure**.

    Para saber mais, procure os logs em:<br/>
   `<AzureBackup_agent_install_folder>\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider*` Por exemplo:<br/>
   `C:\Program Files\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider0.errlog`

## <a name="next-steps"></a>Próximas etapas
* [Restaurar o Windows Server ou o Windows Client do Azure](backup-azure-restore-windows-server.md)
* Para saber mais sobre o Backup do Azure, confira [Visão geral do backup do Azure](backup-introduction-to-azure-backup.md)
* Visite o [Fórum de backup do Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933)
