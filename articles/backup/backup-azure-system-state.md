---
title: Fazer backup de estado do sistema para o Windows Azure
description: Saiba como fazer backup do estado do sistema de computadores Windows Server no Azure.
ms.topic: conceptual
ms.date: 05/23/2018
ms.openlocfilehash: 1b3573d757d2f7b1ffec9ae718aa791488960f3b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91332875"
---
# <a name="back-up-windows-system-state-to-azure"></a>Fazer backup de estado do sistema para o Windows Azure

Este artigo explica como fazer backup do estado do sistema Windows Server para o Azure. O objetivo é orientá-lo pelas noções básicas.

Se você quiser saber mais sobre o Backup do Azure, leia esta [visão geral](backup-overview.md).

Se não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) , que permitirá o acesso a qualquer serviço do Azure.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="set-storage-redundancy-for-the-vault"></a>Definir a redundância de armazenamento para o cofre

Quando você criar um cofre de Serviços de Recuperação, certifique-se de que a redundância de armazenamento esteja configurada da maneira desejada.

1. No painel **cofres dos serviços de recuperação** , selecione o novo cofre.

    ![Selecionar o novo cofre da lista de cofres do Serviços de Recuperação](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

    Quando você seleciona o cofre, o painel **cofre dos serviços de recuperação** é limitado e o painel de configurações (*que tem o nome do cofre na parte superior*) e o painel detalhes do cofre são abertos.

    ![Exibir a configuração de armazenamento para um novo cofre](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-2.png)
2. No painel configurações do novo cofre, use o slide vertical para rolar para baixo até a seção Gerenciar e selecione **infraestrutura de backup**.
    O painel infraestrutura de backup é aberto.
3. No painel infraestrutura de backup, selecione **configuração de backup** para abrir o painel **configuração de backup** .

    ![Definir a configuração de armazenamento para o novo cofre](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration.png)
4. Escolha a opção de replicação de armazenamento adequada para o cofre.

    ![Opções de configuração de armazenamento](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration-for-vault.png)

    Por padrão, seu cofre tem armazenamento com redundância geográfica. Se você usar o Azure como um ponto de extremidade de armazenamento de backup principal, continue a usar **Georredundante**. Se você não usar o Azure como um ponto de extremidade de armazenamento de backup principal, escolha **Localmente redundante**, que reduz os custos de armazenamento do Azure. Leia [mais sobre as](../storage/common/storage-redundancy.md#geo-redundant-storage)opções de armazenamento com redundância local e com redundância de [zona](../storage/common/storage-redundancy.md#zone-redundant-storage) [localmente](../storage/common/storage-redundancy.md#locally-redundant-storage) nesta [visão geral](../storage/common/storage-redundancy.md)de Unificação de armazenamento.

Agora que você criou um cofre, configure-o para fazer backup do Estado do Sistema do Windows.

## <a name="configure-the-vault"></a>Configurar o cofre

1. No painel cofre dos serviços de recuperação (para o cofre que você acabou de criar), na seção Introdução, selecione **backup** e, em seguida, no painel **introdução com backup** , selecione **meta de backup**.

    ![Abrir configurações de backup](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

    O painel **meta de backup** é aberto.

    ![Abrir o painel meta de backup](./media/backup-try-azure-backup-in-10-mins/backup-goal-blade.png)

2. No menu suspenso **Onde sua carga de trabalho é executada?**, selecione **Local**.

    Você escolhe o **local** porque seu computador Windows Server ou Windows é um computador físico que não está no Azure.

3. No menu do **que você deseja fazer backup?** , selecione estado do **sistema** e selecione **OK**.

    ![Configuração de arquivos e pastas](./media/backup-azure-system-state/backup-goal-system-state.png)

    Depois de selecionar **OK**, uma marca de seleção é exibida ao lado de **meta de backup** e o painel **preparar infraestrutura** é aberto.

    ![Meta de backup configurada, prepare a infraestrutura em seguida](./media/backup-try-azure-backup-in-10-mins/backup-goal-configed.png)

4. No painel **preparar infraestrutura** , selecione **baixar agente para Windows Server ou Windows Client**.

    ![Preparar a infraestrutura](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

    Se você estiver usando o Windows Server Essential, escolha baixar o agente para o Windows Server Essential. Um menu pop-up solicitará que você execute ou salve MARSAgentInstaller.exe.

    ![Diálogo MARSAgentInstaller](./media/backup-try-azure-backup-in-10-mins/mars-installer-run-save.png)

5. No menu pop-up baixar, selecione **salvar**.

    Por padrão, o arquivo **MARSagentinstaller.exe** será salvo em sua pasta Downloads. Quando o instalador for concluído, você verá um pop-up perguntando se deseja executar o instalador ou abrir a pasta.

    ![O instalador do MARS foi concluído](./media/backup-try-azure-backup-in-10-mins/mars-installer-complete.png)

    Você não precisa instalar o agente ainda. Você pode instalar o agente depois de baixar as credenciais do cofre.

6. No painel **preparar infraestrutura** , selecione **baixar**.

    ![baixar as credenciais do cofre](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

    As credenciais do cofre são baixadas para a pasta **downloads** . Depois que as credenciais do cofre terminarem de baixar, você verá um pop-up perguntando se deseja abrir ou salvar as credenciais. Selecione **Salvar**. Se você selecionar acidentalmente **abrir**, permita que a caixa de diálogo que tenta abrir as credenciais do cofre falhe. Você não poderá abrir as credenciais do cofre. Siga para a próxima etapa. As credenciais do cofre estão na pasta **downloads** .

    ![o download das credenciais do cofre foi concluído](./media/backup-try-azure-backup-in-10-mins/vault-credentials-downloaded.png)
   > [!NOTE]
   > As credenciais do cofre devem ser salvas somente em um local que seja local para o Windows Server no qual você pretende usar o agente.
   >

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="install-and-register-the-agent"></a>Instalar e registrar o agente

> [!NOTE]
> A habilitação do backup por meio do portal do Azure não está disponível. Use o agente de Serviços de Recuperação do Microsoft Azure para fazer backup do Estado do Sistema do Windows Server.
>

1. Localize e clique duas vezes no **MARSagentinstaller.exe** na pasta Downloads (ou em outro local salvo).

    O instalador fornece uma série de mensagens, pois extrai, instala e registra o agente dos Serviços de Recuperação.

    ![execute as credenciais do instalador do agente dos Serviços de Recuperação](./media/backup-try-azure-backup-in-10-mins/mars-installer-registration.png)

2. Conclua o Assistente de Instalação do Agente do Serviços de Recuperação do Microsoft Azure. Para concluir o assistente, você precisa fazer o seguinte:

   * Escolher um local para a instalação e a pasta de cache.
   * Fornecer as informações de seu servidor proxy se você usar um servidor proxy para conectar-se à Internet.
   * Forneça os detalhes do seu nome de usuário e de sua senha se usar um proxy autenticado.
   * Forneça as credenciais do cofre baixado
   * Salve a senha de criptografia em um local seguro.

     > [!NOTE]
     > Se você perder ou esquecer a senha, a Microsoft não poderá ajudar a recuperar os dados de backup. Salve o arquivo em um local seguro. É necessário restaurar um backup.
     >
     >

Agora, o agente está instalado e seu computador está registrado no cofre. Você está pronto para configurar e agendar o backup.

## <a name="back-up-windows-server-system-state"></a>Fazer backup do Estado do Sistema do Windows Server

O backup inicial inclui duas tarefas:

* Agendar o backup
* Fazer backup do Estado do Sistema pela primeira vez

Para concluir o backup inicial, use o agente dos Serviços de Recuperação do Microsoft Azure.

> [!NOTE]
> Você pode fazer backup do Estado do Sistema no Windows Server 2008 R2 por meio do Windows Server 2016. Não há suporte para backup de estado do sistema em SKUs do cliente. O estado do sistema não é mostrado como uma opção para clientes Windows ou computadores com Windows Server 2008 SP2.
>
>

### <a name="to-schedule-the-backup-job"></a>Para agendar o trabalho de backup

1. Abra o agente dos Serviços de Recuperação do Microsoft Azure Você pode localizá-lo pesquisando no seu computador por **Backup do Microsoft Azure**.

    ![Inicialize o agente dos Serviços de Recuperação do Azure](./media/backup-try-azure-backup-in-10-mins/snap-in-search.png)

2. No agente dos serviços de recuperação, selecione **agendar backup**.

    ![Agendar um backup do Windows Server](./media/backup-try-azure-backup-in-10-mins/schedule-first-backup.png)

3. Na página **Guia de introdução** do assistente de agendamento de backup, selecione **Avançar**.

4. Na página **selecionar itens para backup** , selecione **Adicionar itens**.

5. Selecione **estado do sistema** e, em seguida, selecione **OK**.

6. Selecione **Avançar**.

7. Selecione a frequência de Backup necessária e a política de retenção para os backups de Estado do Sistema nas próximas páginas.

8. Na página confirmação, examine as informações e selecione **concluir**.

9. Depois que o assistente terminar de criar o agendamento de backup, escolha **Fechar**.

### <a name="to-back-up-windows-server-system-state-for-the-first-time"></a>Para fazer backup do Estado do Sistema do Windows Server pela primeira vez

1. Certifique-se de que não existem atualizações pendentes para o Windows Server que exigem uma reinicialização.

2. No agente dos serviços de recuperação, selecione **fazer backup agora** para concluir a propagação inicial pela rede.

    ![Backup do Windows Server agora](./media/backup-try-azure-backup-in-10-mins/backup-now.png)

3. Selecione **estado do sistema** na tela **selecionar item de backup** que aparece e selecione **Avançar**.

4. Na página Confirmação, examine as configurações que o Assistente Fazer Backup Agora usará para fazer backup do computador. Em seguida, selecione **Fazer Backup**.

5. Selecione **Fechar** para fechar o assistente. Se você fechar o assistente antes da conclusão do processo de backup, o assistente continuará a ser executado em segundo plano.
    > [!NOTE]
    > O agente MARS é disparado `SFC /verifyonly` como parte das preverificações antes de todos os backups de estado do sistema. Isso é para garantir que os arquivos submetidos a backup como parte do estado do sistema tenham as versões corretas correspondentes à versão do Windows. Saiba mais sobre o verificador de arquivos do sistema (SFC) neste [artigo](/windows-server/administration/windows-commands/sfc).
    >

Depois que o backup inicial for concluído, o status **Trabalho concluído** aparecerá no Console de backup.

  ![IR completo](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

## <a name="questions"></a>Perguntas?

Se você tiver dúvidas, [envie-nos seus comentários](https://feedback.azure.com/forums/258995-azure-backup).

## <a name="next-steps"></a>Próximas etapas

* Obtenha mais detalhes sobre o [backup de computadores que usam o Windows](backup-windows-with-mars-agent.md).
* Agora que você fez backup do Estado do Sistema do Windows Server, [gerencie seus cofres e servidores](backup-azure-manage-windows-server.md).
* Se você precisar restaurar um backup, use este artigo para [restaurar os arquivos para um computador que usa o Windows](backup-azure-restore-windows-server.md).
