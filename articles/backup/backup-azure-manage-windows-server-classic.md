---
title: "Gerenciar servidores e cofres de Backup do Azure usando o modelo de implantação clássico | Microsoft Docs"
description: Use este tutorial para aprender a gerenciar servidores e cofres de Backup do Azure.
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: tysonn
ms.assetid: f175eb12-0905-437f-91fd-eaee03ab6e81
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: markgal;
ms.openlocfilehash: 91451b2cdc42ed05ef7c1ba9c66ad5b4b45dd788
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-backup-vaults-and-servers-using-the-classic-deployment-model"></a>Gerenciar servidores e cofres de Backup do Azure usando o modelo de implantação clássico
> [!div class="op_single_selector"]
> * [Gerenciador de Recursos](backup-azure-manage-windows-server.md)
> * [Clássico](backup-azure-manage-windows-server-classic.md)
>
>

Neste artigo, você encontra uma visão geral das tarefas de gerenciamento de backup disponíveis no portal clássico do Azure e o agente de Backup do Microsoft Azure.

> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../azure-resource-manager/resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação Clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos.

> [!IMPORTANT]
> Agora você pode atualizar os cofres de Backup para cofres dos Serviços de Recuperação. Para obter detalhes, veja o artigo [Atualizar um cofre de Backup para um cofre dos Serviços de Recuperação](backup-azure-upgrade-backup-to-recovery-services.md). A Microsoft incentiva você a atualizar os cofres de Backup para os cofres dos Serviços de Recuperação.<br/> Após 15 de outubro de 2017, você não poderá usar o PowerShell para criar os Cofres do Backup. **Em 1º de novembro de 2017**:
>- Todos os Cofres do Backup restantes serão atualizados automaticamente para os cofres dos Serviços de Recuperação.
>- Você não poderá acessar os dados de backup no portal clássico. Em vez disso, use o portal do Azure para acessar os dados de backup nos cofres dos Serviços de Recuperação.
>

## <a name="management-portal-tasks"></a>Tarefas do portal de gerenciamento
1. Entre no [Portal de Gerenciamento](https://manage.windowsazure.com).
2. Clique em **Serviços de Recuperação**, em seguida, clique no nome do cofre de backup para exibir a página de Início Rápido.

    ![Serviços de Recuperação](./media/backup-azure-manage-windows-server-classic/rs-left-nav.png)

Selecionando as opções na parte superior da página Início Rápido, você pode ver as tarefas de gerenciamento disponíveis.

![Gerenciar guias](./media/backup-azure-manage-windows-server-classic/qs-page.png)

### <a name="dashboard"></a>Painel
Clique em **Painel** para ver a visão geral do uso para o servidor. A **visão geral de uso** inclui:

* O número de Windows Servers registrados na nuvem
* O número de máquinas virtuais do Azure protegidas na nuvem
* O armazenamento total consumido no Azure
* O status de trabalhos recentes

Na parte inferior do Painel, você pode executar as seguintes tarefas:

* **Gerenciar certificado** - se um certificado tiver sido usado para registrar o servidor, então use isso para atualizar o certificado. Se estiver usando as credenciais do cofre, não use **Gerenciar certificados**.
* **Excluir** - exclui o cofre de backup atual. Se não estiver sendo usado um cofre de backup, você poderá excluir para liberar mais espaço de armazenamento. **Excluir** é ativado somente depois que todos os servidores registrados foram excluídos do cofre.

![Tarefas do painel Backup](./media/backup-azure-manage-windows-server-classic/dashboard-tasks.png)

## <a name="registered-items"></a>Itens registrados
Selecione **Itens registrados** para exibir os nomes dos servidores registrados para o cofre.

![Itens registrados](./media/backup-azure-manage-windows-server-classic/registered-items.png)

O filtro **Tipo** tem como padrão a Máquina Virtual do Azure. Para exibir os nomes dos servidores registrados neste cofre, selecione **Windows Server** no menu suspenso.

A partir daqui, você pode executar as seguintes tarefas:

* **Permitir Novo Registro** - quando essa opção estiver selecionada para um servidor, você poderá usar o **Assistente de Registro** no agente de Backup do Microsoft Azure local para registrar o servidor com o cofre de backup uma segunda vez. Talvez você precise registrar novamente devido a um erro no certificado ou se um servidor tiver que ser refeito.
* **Excluir** - Exclui um servidor do cofre de backup. Todos os dados armazenados associados ao servidor serão excluídos imediatamente.

    ![Tarefas de itens registrados](./media/backup-azure-manage-windows-server-classic/registered-items-tasks.png)

## <a name="protected-items"></a>Itens protegidos
Clique em **Itens Protegidos** para ver os itens que foram colocados no backup dos servidores.

![Itens protegidos](./media/backup-azure-manage-windows-server-classic/protected-items.png)

## <a name="configure"></a>Configurar
Na guia **Configurar** , você pode selecionar a opção de redundância de armazenamento apropriada. O melhor momento para selecionar a opção de redundância de armazenamento é logo após a criação de um cofre e antes de qualquer computador ser registrado nele.

> [!WARNING]
> Depois que um item tiver sido registrado no cofre, a opção de redundância de armazenamento será bloqueada e não poderá ser modificada.
>
>

![Configurar](./media/backup-azure-manage-windows-server-classic/configure.png)

Confira este artigo para saber mais sobre a [redundância de armazenamento](../storage/common/storage-redundancy.md).

## <a name="microsoft-azure-backup-agent-tasks"></a>Tarefas do agente de Backup do Microsoft Azure
### <a name="console"></a>Console
Abra o **agente de Backup do Microsoft Azure** (você poderá localizá-lo procurando *Backup do Microsoft Azure*em seu computador).

![Agente de backup](./media/backup-azure-manage-windows-server-classic/snap-in-search.png)

Na guia **Ações** , disponibilizada à direita do console do agente de backup, você pode executar as seguintes tarefas de gerenciamento:

* Registrar Servidor
* Agendar backup
* Fazer backup agora
* Alterar propriedades

![Ações do console do agente](./media/backup-azure-manage-windows-server-classic/console-actions.png)

> [!NOTE]
> Para **Recuperar Dados**, consulte [Restaurar arquivos para um computador cliente Windows ou um servidor Windows](backup-azure-restore-windows-server.md).
>
>

### <a name="modify-an-existing-backup"></a>Modificar um backup existente
1. No agente de Backup do Microsoft Azure, clique em **Agendar Backup**.

    ![Agendar um Backup do Windows Server](./media/backup-azure-manage-windows-server-classic/schedule-backup.png)
2. No **Assistente de Agendamento de Backup**, deixe a opção **Fazer alterações aos itens ou horários de backup** selecionada e clique em **Avançar**.

    ![Modificar um backup agendado](./media/backup-azure-manage-windows-server-classic/modify-or-stop-a-scheduled-backup.png)
3. Se quiser adicionar ou alterar itens, na tela **Selecionar Itens para Backup**, clique em **Adicionar Itens**.

    Você também pode definir **Configurações de Exclusão** nesta página do assistente. Se você quiser excluir arquivos ou tipos de arquivo, leia o procedimento para adicionar [configurações de exclusão](#exclusion-settings).
4. Selecione os arquivos e as pastas dos quais você deseja fazer backup e clique em **OK**.

    ![Adicionar Itens](./media/backup-azure-manage-windows-server-classic/add-items-modify.png)
5. Especifique o **agendamento de backup** e clique em **Avançar**.

    Você pode agendar backups diários (no máximo três vezes por dia) ou backups semanais.

    ![Especificar o agendamento do Backup](./media/backup-azure-manage-windows-server-classic/specify-backup-schedule-modify-close.png)

   > [!NOTE]
   > A especificação do agendamento de backup é explicada em detalhes neste [artigo](backup-azure-backup-cloud-as-tape.md).
   >
   >
6. Selecione a **Política de Retenção** para a cópia de backup e clique em **Avançar**.

    ![Selecionar a política de retenção](./media/backup-azure-manage-windows-server-classic/select-retention-policy-modify.png)
7. Na tela **Confirmação**, examine as informações e clique em **Concluir**.
8. Depois que o assistente terminar de criar o **agendamento de backup**, clique em **Fechar**.

    Depois de modificar a proteção, é possível confirmar se os backups estão sendo acionados corretamente acessando a guia **Trabalhos** e confirmando se as alterações são refletidas nos trabalhos de backup.

### <a name="enable-network-throttling"></a>Habilitar a limitação de rede
O agente de Backup do Azure fornece uma guia Limitação, que permite controlar como a largura de banda é usada durante a transferência de dados. Esse controle pode ser útil se você precisa fazer backup de dados durante o horário de expediente, mas não quer que o processo de backup interfira no outro tráfego de Internet. A limitação da transferência de dados aplica-se a atividades de backup e restauração.  

Para habilitar a limitação:

1. No **agente de backup**, clique em **Alterar Propriedades**.
2. Marque a caixa de seleção **Habilitar limitação de uso de largura de banda da Internet para operações de backup** .

    ![Limitação de rede](./media/backup-azure-manage-windows-server-classic/throttling-dialog.png)
3. Depois de habilitar a limitação, especifique a largura de banda permitida para a transferência de dados de backup durante as **Horas úteis** e **Horas não úteis**.

    Os valores de largura de banda começam em 512 quilobytes por segundo (Kbps) e podem ir até 1023 megabytes por segundo (Mbps). Você também pode indicar o início e o término das **Horas úteis**e quais dias da semana são considerados dias úteis. O tempo fora das Horas úteis indicadas é considerado como hora não útil.
4. Clique em **OK**.

## <a name="exclusion-settings"></a>Configurações de Exclusão
1. Abra o **agente de Backup do Microsoft Azure** (você poderá localizá-lo procurando *Backup do Microsoft Azure*em seu computador).

    ![Abrir agente de backup](./media/backup-azure-manage-windows-server-classic/snap-in-search.png)
2. No agente de Backup do Microsoft Azure, clique em **Agendar Backup**.

    ![Agendar um Backup do Windows Server](./media/backup-azure-manage-windows-server-classic/schedule-backup.png)
3. No Assistente de Agendamento de Backup, deixe a opção **Fazer alterações aos itens ou horários de backup** selecionada e clique em **Avançar**.

    ![Modificar um agendamento](./media/backup-azure-manage-windows-server-classic/modify-or-stop-a-scheduled-backup.png)
4. Clique em **Configurações de Exclusões**.

    ![Selecione itens a serem excluídos](./media/backup-azure-manage-windows-server-classic/exclusion-settings.png)
5. Clique em **Adicionar Exclusão**.

    ![Adicionar exclusões](./media/backup-azure-manage-windows-server-classic/add-exclusion.png)
6. Selecione o local e clique em **OK**.

    ![Selecionar um local para exclusão](./media/backup-azure-manage-windows-server-classic/exclusion-location.png)
7. Adicione a extensão de arquivo no campo **Tipo de Arquivo** .

    ![Excluir por tipo de arquivo](./media/backup-azure-manage-windows-server-classic/exclude-file-type.png)

    Adição de uma extensão .mp3

    ![Exemplo de tipo de arquivo](./media/backup-azure-manage-windows-server-classic/exclude-mp3.png)

    Para adicionar outra extensão, clique em **Adicionar Exclusão** e insira outra extensão do tipo de arquivo (adicionando uma extensão .jpeg).

    ![Outro exemplo de tipo de arquivo](./media/backup-azure-manage-windows-server-classic/exclude-jpg.png)
8. Quando tiver adicionado todas as extensões, clique em **OK**.
9. Prossiga com o Assistente de Agendamento de Backup clicando em **Avançar** até a **página Confirmação** e clique em **Concluir**.

    ![Confirmação de exclusão](./media/backup-azure-manage-windows-server-classic/finish-exclusions.png)

## <a name="next-steps"></a>Próximas etapas
* [Restaurar o Windows Server ou o Windows Client do Azure](backup-azure-restore-windows-server.md)
* Para saber mais sobre o Backup do Azure, confira [Visão geral do backup do Azure](backup-introduction-to-azure-backup.md)
* Visite o [Fórum de backup do Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933)
