---
title: Fazer backup de arquivos e pastas do Windows no Azure (Resource Manager) | Microsoft Docs
description: "Saiba como fazer backup de arquivos e pastas do Windows no Azure em uma implantação do Resource Manager."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: como fazer backup; como fazer backup; backup de arquivos e pastas
ms.assetid: 5b15ebf1-2214-4722-b937-96e2be8872bb
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 8/15/2017
ms.author: markgal;
ms.openlocfilehash: 7a016ed92c68ce624aeb09d766adbc6fc8ba2b42
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="first-look-back-up-files-and-folders-in-resource-manager-deployment"></a>Introdução: fazer backup de arquivos e pastas na implantação do Resource Manager
Este artigo explica como fazer backup de arquivos e pastas do Windows Server (ou computador Windows) para o Azure com o Backup do Azure usando uma implantação do Resource Manager. É um tutorial que pretende explicar os conceitos básicos. Se deseja obter uma introdução ao uso do Backup do Azure, você está no lugar certo.

Se você quiser saber mais sobre o Backup do Azure, leia esta [visão geral](backup-introduction-to-azure-backup.md).

Se não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) , que permitirá o acesso a qualquer serviço do Azure.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação
Para fazer backup de seus arquivos e pastas, você precisa criar um cofre de Serviços de Recuperação na região onde deseja armazenar os dados. Você também precisa determinar como deseja que o armazenamento seja replicado.

### <a name="to-create-a-recovery-services-vault"></a>Para criar um cofre de Serviços de Recuperação
1. Se ainda não tiver feito isso, entre no [Portal do Azure](https://portal.azure.com/) usando a sua assinatura do Azure.
2. No menu Hub, clique em **Mais serviços** e, na lista de recursos, digite **Serviços de Recuperação** e clique em **Cofres dos Serviços de Recuperação**.

    ![Criar Cofre de Serviços de Recuperação - etapa 1](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png) <br/>

    Se houver cofres dos serviços de recuperação na assinatura, os cofres serão listados.
3. No menu **Cofres de Serviços de Recuperação**, clique em **Adicionar**.

    ![Criar Cofre de Serviços de Recuperação - etapa 2](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

    A folha do cofre dos Serviços de Recuperação será aberta, solicitando que você forneça o **Nome**, a **Assinatura**, o **Grupo de recursos** e o **Local**.

    ![Criar Cofre de Serviços de Recuperação - etapa 3](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

4. Em **Nome**, insira um nome amigável para identificar o cofre. O nome deve ser exclusivo para a assinatura do Azure. Digite um nome que contenha de 2 a 50 caracteres. Ele deve começar com uma letra e pode conter apenas letras, números e hifens.

5. Na seção **Assinatura**, use o menu suspenso para escolher a assinatura do Azure. Se você usar apenas uma assinatura, essa assinatura será exibida e você poderá pular para a próxima etapa. Se você não tiver certeza sobre qual assinatura usar, utilize a assinatura padrão (ou sugerida). Só haverá múltiplas opções se sua conta organizacional estiver associada a várias assinaturas do Azure.

6. Na seção **Grupo de recursos**:

    * selecione **Criar novo** se quiser criar um novo Grupo de recursos.
    Ou
    * Selecione **Usar existente** e clique no menu suspenso para ver a lista de grupos de recursos disponíveis.

  Para obter informações completas sobre Grupos de recursos, confira a [Visão geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

7. Clique em **Local** para selecionar a região geográfica do cofre. Essa escolha determina a região geográfica para a qual os dados de backup são enviados.

8. Na parte inferior da folha Cofre dos Serviços de Recuperação, clique em **Criar**.

    Talvez demore alguns minutos para o cofre de Serviços de Recuperação ser criado. Monitore as notificações de status na área superior direita do portal. Depois que o cofre é criado, ele aparece na lista de cofres dos Serviços de Recuperação. Se após alguns minutos, você não vir seu cofre, clique em **Atualizar**.

    ![Clique no botão Atualizar](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)</br>

    Depois de ver seu cofre na lista de cofres dos Serviços de Recuperação, você estará pronto para configurar a redundância de armazenamento.

### <a name="set-storage-redundancy-for-the-vault"></a>Definir a redundância de armazenamento para o cofre
Quando você criar um cofre de Serviços de Recuperação, certifique-se de que a redundância de armazenamento esteja configurada da maneira desejada.

1. Na folha **Cofres dos Serviços de Recuperação**, clique no novo cofre.

    ![Selecionar o novo cofre da lista de cofres do Serviços de Recuperação](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

    Quando você selecionar o cofre, a folha **Cofre de Serviços de Recuperação** será reduzida e a folha Configurações (*que tem o nome do cofre na parte superior*) e a folha de detalhes do cofre serão abertas.

    ![Exibir a configuração de armazenamento para um novo cofre](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-2.png)
2. Na folha de configurações do novo cofre, use o slide vertical para rolar para baixo até a seção Gerenciar e clique em **Infraestrutura de Backup**.
    A folha Infraestrutura de Backup é aberta.
3. Na folha Infraestrutura de Backup, clique em **Configuração de Backup** para abrir a folha **Configuração de Backup**.

    ![Definir a configuração de armazenamento para o novo cofre](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration.png)
4. Escolha a opção de replicação de armazenamento adequada para o cofre.

    ![opções de configuração de armazenamento](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

    Por padrão, seu cofre tem armazenamento com redundância geográfica. Se você usar o Azure como um ponto de extremidade de armazenamento de backup principal, continue a usar **Georredundante**. Se você não usar o Azure como um ponto de extremidade de armazenamento de backup principal, escolha **Localmente redundante**, que reduz os custos de armazenamento do Azure. Leia mais sobre as opções de armazenamento [com redundância geográfica](../storage/common/storage-redundancy.md#geo-redundant-storage) e [com redundância local](../storage/common/storage-redundancy.md#locally-redundant-storage) nesta [Visão geral de redundância de armazenamento](../storage/common/storage-redundancy.md).

Agora que você criou um cofre, configure-o para fazer backup de arquivos e pastas.

## <a name="configure-the-vault"></a>Configurar o cofre
1. Na folha do cofre dos Serviços de Recuperação (para o cofre recém-criado), na seção Introdução, clique em **Backup**, na folha **Introdução ao Backup**, selecione **Meta de Backup**.

    ![Abrir folha de meta backup](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

    A folha **Meta de Backup** será aberta.

    ![Abrir folha de meta backup](./media/backup-try-azure-backup-in-10-mins/backup-goal-blade.png)

2. No menu suspenso **Onde sua carga de trabalho é executada?**, selecione **Local**.

    Você escolhe **Local** porque o Windows Server ou o computador do Windows é uma máquina física que não está no Azure.

3. No menu **Do que você deseja fazer backup?**, selecione **Arquivos e pastas** e clique em **OK**.

    ![Configuração de arquivos e pastas](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

    Depois de clicar em OK, uma marca de seleção aparece ao lado de **Meta de backup** e a folha **Preparar infraestrutura** será aberta.

    ![Meta de backup configurada, prepare a infraestrutura em seguida](./media/backup-try-azure-backup-in-10-mins/backup-goal-configed.png)

4. Na folha **Preparar infraestrutura**, clique em **Baixar agente do Windows Server ou Windows Client**.

    ![Preparar infraestrutura](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

    Se você estiver usando o Windows Server Essential, opte por baixar o agente para o Windows Server Essential. Um menu pop-up solicitará que você execute ou salve MARSAgentInstaller.exe.

    ![Diálogo MARSAgentInstaller](./media/backup-try-azure-backup-in-10-mins/mars-installer-run-save.png)

5. Clique em **Salvar** no menu pop-up de download.

    Por padrão, o arquivo **MARSagentinstaller.exe** será salvo em sua pasta Downloads. Quando o instalador for concluído, será exibido um pop-up perguntando se você deseja executar o instalador ou abrir a pasta.

    ![Preparar infraestrutura](./media/backup-try-azure-backup-in-10-mins/mars-installer-complete.png)

    Você não precisa instalar o agente ainda. Você poderá instalar o agente depois de baixar as credenciais do cofre.

6. Na folha **Preparar infraestrutura**, clique em **Baixar**.

    ![baixar as credenciais do cofre](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

    As credenciais do cofre são baixadas para a pasta Downloads. Após o término do download das credenciais do cofre, você verá um pop-up perguntando se deseja abrir ou salvar as credenciais. Clique em **Salvar**. Se você clicar acidentalmente em **Abrir**, deixe a caixa de diálogo que tenta abrir as credenciais do cofre falhar. Não é possível abrir as credenciais do cofre. Vá para a próxima etapa. As credenciais do cofre estão na pasta Downloads.   

    ![o download das credenciais do cofre foi concluído](./media/backup-try-azure-backup-in-10-mins/vault-credentials-downloaded.png)

## <a name="install-and-register-the-agent"></a>Instalar e registrar o agente

> [!NOTE]
> A habilitação do backup pelo portal do Azure ainda não está disponível. Use o Agente dos Serviços de Recuperação do Microsoft Azure para fazer backup de seus arquivos e pastas.
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
     > Se você perder ou esquecer a senha, a Microsoft não poderá ajudar a recuperar os dados de backup. Salve o arquivo em um local seguro. Isso é necessário para restaurar um backup.
     >
     >

Agora, o agente está instalado e seu computador está registrado no cofre. Você está pronto para configurar e agendar o backup.

## <a name="network-and-connectivity-requirements"></a>Requisitos de conectividade e rede

Se seu computador/proxy tiver acesso limitado à Internet, verifique se as configurações do firewall no computador/proxy estão definidas para permitir as seguintes URLs: <br>
    1. www.msftncsi.com
    2. *.Microsoft.com
    3. *.WindowsAzure.com
    4. *.microsoftonline.com
    5. *.windows.net

## <a name="back-up-your-files-and-folders"></a>Fazer o backup de arquivos e pastas
O backup inicial inclui duas tarefas principais:

* Agendar o backup
* Fazer backup de arquivos e pastas pela primeira vez

Para concluir o backup inicial, use o agente dos Serviços de Recuperação do Microsoft Azure.

### <a name="to-schedule-the-backup-job"></a>Para agendar o trabalho de backup
1. Abra o agente dos Serviços de Recuperação do Microsoft Azure Você pode localizá-lo pesquisando no seu computador por **Backup do Microsoft Azure**.

    ![Inicialize o agente dos Serviços de Recuperação do Azure](./media/backup-try-azure-backup-in-10-mins/snap-in-search.png)
2. O agente dos Serviços de Recuperação, clique em **Agendar Backup**.

    ![Agendar um backup do Windows Server](./media/backup-try-azure-backup-in-10-mins/schedule-first-backup.png)
3. Na página de Introdução do Assistente de Agendamento de Backup, clique em **Avançar**.
4. Na tela Selecionar Itens para Backup, clique em **Adicionar Itens**.
5. Selecione os arquivos e pastas dos quais você deseja fazer backup e clique em **Ok**.
6. Clique em **Avançar**.
7. Na tela **Especificar Agendamento de Backup**, especifique o **agendamento de backup** e clique em **Avançar**.

    Você pode agendar backups diários (com uma taxa máxima de três vezes por dia) ou backups semanais.

    ![Itens para o backup do Windows Server](./media/backup-try-azure-backup-in-10-mins/specify-backup-schedule-close.png)

   > [!NOTE]
   > Para saber mais sobre como especificar o agendamento de backup, confira o artigo [Usar o Backup do Azure para substituir a sua infraestrutura de fita](backup-azure-backup-cloud-as-tape.md).
   >

8. Na página **Selecionar Política de Retenção**, escolha a **Política de Retenção** para a cópia de backup.

    A política de retenção especifica por quanto tempo os dados de backup são armazenados. Em vez de especificar uma “política simples” para todos os pontos de backup, você pode especificar políticas de retenção diferentes com base em quando o backup ocorre. Você pode modificar as políticas de retenção diária, semanal, mensal e anual para atender às suas necessidades.
9. Na tela Escolher Tipo de Backup Inicial, escolha o tipo de backup inicial. Deixe a opção **Automaticamente pela rede** selecionada e clique em **Avançar**.

    Você pode fazer backup automaticamente pela rede ou pode fazer backup offline. O restante deste artigo descreve o processo para realização de backup automático. Se preferir fazer um backup offline, examine o artigo [Fluxo de trabalho de backup offline no Backup do Azure](backup-azure-backup-import-export.md) para obter informações adicionais.
10. Na página Confirmação, examine as informações e clique em **Concluir**.
11. Depois que o assistente terminar de criar o agendamento de backup, clique em **Fechar**.

### <a name="to-back-up-files-and-folders-for-the-first-time"></a>Para fazer backup de arquivos e pastas pela primeira vez
1. No Agente dos Serviços de Recuperação, clique em **Fazer Backup Agora** para concluir a propagação inicial pela rede.

    ![Fazer backup do Windows Server agora](./media/backup-try-azure-backup-in-10-mins/backup-now.png)
2. Na página Confirmação, examine as configurações que o Assistente Fazer Backup Agora usará para fazer backup do computador. Em seguida, clique em **Fazer Backup**.
3. Clique em **Fechar** para fechar o assistente. Se você fechar o assistente antes da conclusão do processo de backup, o assistente continuará a ser executado em segundo plano.

Depois que o backup inicial for concluído, o status **Trabalho concluído** aparecerá no Console de backup.

![IR completo](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

## <a name="questions"></a>Perguntas?
Se você tiver dúvidas ou gostaria de ver algum recurso incluído, [envie-nos seus comentários](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Próximas etapas
* Obtenha mais detalhes sobre o [backup de computadores que usam o Windows](backup-configure-vault.md).
* Agora que você faz backup de seus arquivos e pastas, poderá [gerenciar seus servidores e cofres](backup-azure-manage-windows-server.md).
* Se você precisar restaurar um backup, use este artigo para [restaurar os arquivos para um computador que usa o Windows](backup-azure-restore-windows-server.md).
