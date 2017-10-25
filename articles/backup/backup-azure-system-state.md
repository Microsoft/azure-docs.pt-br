---
title: Fazer backup de estado do sistema para o Windows Azure | Microsoft Docs
description: Saiba como fazer backup do estado do sistema do Windows Server e/ou de computadores Windows para o Azure.
services: backup
documentationcenter: 
author: saurabhsensharma
manager: carmonm
editor: 
keywords: como fazer backup; como fazer backup; backup de arquivos e pastas
ms.assetid: 5b15ebf1-2214-4722-b937-96e2be8872bb
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: saurse;markgal
ms.openlocfilehash: 6fbd96935f444d8b0c6d068ebd0d28e612f19816
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-windows-system-state-in-resource-manager-deployment"></a>Fazer backup de estado do sistema do Windows na implementação do Gerenciador de Recursos
Este artigo explica como fazer backup do estado do sistema Windows Server para o Azure. É um tutorial que pretende explicar os conceitos básicos.

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

    * selecione **Criar novo** se você quiser criar um Grupo de recursos.
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

Agora que você criou um cofre, configure-o para fazer backup do Estado do Sistema do Windows.

## <a name="configure-the-vault"></a>Configurar o cofre
1. Na folha do cofre dos Serviços de Recuperação (para o cofre recém-criado), na seção Introdução, clique em **Backup**, na folha **Introdução ao Backup**, selecione **Meta de Backup**.

    ![Abrir folha de meta backup](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

    A folha **Meta de Backup** será aberta.

    ![Abrir folha de meta backup](./media/backup-try-azure-backup-in-10-mins/backup-goal-blade.png)

2. No menu suspenso **Onde sua carga de trabalho é executada?**, selecione **Local**.

    Você escolhe **Local** porque o Windows Server ou o computador do Windows é uma máquina física que não está no Azure.

3. No menu **Do que você deseja fazer backup?**, selecione **Estado do Sistema** e clique em **OK**.

    ![Configuração de arquivos e pastas](./media/backup-azure-system-state/backup-goal-system-state.png)

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
> A habilitação do backup pelo portal do Azure ainda não está disponível. Use o agente de Serviços de Recuperação do Microsoft Azure para fazer backup do Estado do Sistema do Windows Server.
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

## <a name="back-up-windows-server-system-state-preview"></a>Fazer backup do Estado do Sistema do Windows Server (visualização prévia)
O backup inicial inclui três tarefas:

* Habilitar o Backup do Estado do Sistema usando o agente de Backup do Azure
* Agendar o backup
* Fazer backup de arquivos e pastas pela primeira vez

Para concluir o backup inicial, use o agente dos Serviços de Recuperação do Microsoft Azure.

### <a name="to-enable-system-state-backup-using-the-azure-backup-agent"></a>Para habilitar o backup do Estado do Sistema usando o agente de Backup da Azure

1. Em uma sessão do PowerShell, execute o seguinte comando para interromper o mecanismo de Backup do Azure.

  ```
  PS C:\> Net stop obengine
  ```

2. Abra o registro do Windows.

  ```
  PS C:\> regedit.exe
  ```

3. Insira a seguinte chave do registro com o Valor DWord especificado.

  | Caminho do registro | Chave do registro | Valor DWord |
  |---------------|--------------|-------------|
  | HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider | TurnOffSSBFeature | 2 |

4. Reinicie o mecanismo do backup executando o seguinte comando em um prompt de comandos com privilégios elevados.

  ```
  PS C:\> Net start obengine
  ```

### <a name="to-schedule-the-backup-job"></a>Para agendar o trabalho de backup

1. Abra o agente dos Serviços de Recuperação do Microsoft Azure Você pode localizá-lo pesquisando no seu computador por **Backup do Microsoft Azure**.

    ![Inicialize o agente dos Serviços de Recuperação do Azure](./media/backup-try-azure-backup-in-10-mins/snap-in-search.png)

2. O agente dos Serviços de Recuperação, clique em **Agendar Backup**.

    ![Agendar um backup do Windows Server](./media/backup-try-azure-backup-in-10-mins/schedule-first-backup.png)

3. Na página de Introdução do Assistente de Agendamento de Backup, clique em **Avançar**.

4. Na tela Selecionar Itens para Backup, clique em **Adicionar Itens**.

5. Selecione **Estado do sistema** e, em seguida, clique em **OK**.

6. Clique em **Avançar**.

7. O agendamento de backup e retenção do estado do sistema está configurado automaticamente para fazer backup todos os domingos às 21 horas do horário local, e o período de retenção está definido como 60 dias.

   > [!NOTE]
   > A política de backup e retenção do estado do sistema é configurada automaticamente. Se você fizer backup de arquivos e pastas, além do estado do sistema do Windows Server, especifique apenas a política de Backup e Retenção de backups de arquivo do assistente. 
   >

8. Na página Confirmação, examine as informações e clique em **Concluir**.

9. Depois que o assistente terminar de criar o agendamento de backup, clique em **Fechar**.

### <a name="to-back-up-windows-server-system-state-for-the-first-time"></a>Para fazer backup do Estado do Sistema do Windows Server pela primeira vez

1. Certifique-se de que não existem atualizações pendentes para o Windows Server que exigem uma reinicialização.

2. No Agente dos Serviços de Recuperação, clique em **Fazer Backup Agora** para concluir a propagação inicial pela rede.

    ![Fazer backup do Windows Server agora](./media/backup-try-azure-backup-in-10-mins/backup-now.png)

3. Na página Confirmação, examine as configurações que o Assistente Fazer Backup Agora usará para fazer backup do computador. Em seguida, clique em **Fazer Backup**.

4. Clique em **Fechar** para fechar o assistente. Se você fechar o assistente antes da conclusão do processo de backup, o assistente continuará a ser executado em segundo plano.

5. Se você fizer backup de Arquivos e Pastas no servidor, além de estado do sistema Windows Server, o assistente Fazer Backup agora fará backup somente de arquivos. Para executar um backup ad hoc do estado do sistema, use o seguinte comando do PowerShell:

    ```
    PS C:\> Start-OBSystemStateBackup
    ```

  Depois que o backup inicial for concluído, o status **Trabalho concluído** aparecerá no Console de backup.

  ![IR completo](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

## <a name="frequently-asked-questions"></a>Perguntas frequentes

As perguntas e respostas a seguir fornecem informações adicionais. 

### <a name="what-is-the-staging-volume"></a>O que é o Volume de Preparo?

O Volume de Preparo representa o local intermediário onde o backup do Windows Server disponível nativamente prepara o backup do Estado do Sistema.  O agente de Backup do Azure comprime e criptografa esse backup intermediário e o envia de modo seguro, por meio do Protocolo HTTPS, para o Cofre de Serviços de Recuperação configurado. **É altamente recomendável estabelecer o Volume de Preparo em um volume de um sistema operacional que não seja o Windows. Se você encontrar dificuldades com os backups do Estado do Sistema, o primeiro passo para a resolução do problema é verificar o local do Volume de Preparo.** 

### <a name="how-can-i-change-the-staging-volume-path-specified-in-the-azure-backup-agent"></a>Como alterar o caminho do Volume de Preparo especificado no agente de Backup da Azure?

O Volume de Preparo está configurado para estar localizado na pasta de cache. 

1. Para alterar esse local, use o seguinte comando (em um prompt de comandos com privilégios elevados):
  ```
  PS C:\> Net stop obengine
  ```

2. Atualize as seguintes entradas de registro com o caminho para a nova pasta de Volume de Preparo.

  |Caminho do registro|Chave do registro|Valor|
  |-------------|------------|-----|
  |HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config\CloudBackupProvider | SSBStagingPath | novo local do volume de preparo |

O Caminho de Preparo diferencia maiúsculas de minúsculas. Portanto, as maiúsculas e as minúsculas devem estar exatamente como no servidor. 

3. Depois que você alterar o caminho do Volume de Preparo, reinicie o mecanismo de backup:
  ```
  PS C:\> Net start obengine
  ```
4. Para obter o caminho alterado, abra o agente de Serviços de Recuperação do Microsoft Azure e dispare um backup ad hoc do estado do sistema.

### <a name="why-is-the-system-state-default-retention-set-to-60-days"></a>Por que a retenção do estado do sistema padrão é definida como 60 dias?

A vida útil de um backup de estado do sistema é a mesma que a configuração de "tempo de vida da marca de exclusão" para a função do Active Directory do Windows Server. O valor padrão para a entrada de tempo de vida da marca de exclusão é de 60 dias. Esse valor pode ser definido no objeto de configuração do Serviço de Diretório (NTDS).

### <a name="how-do-i-change-the-default-backup-and-retention-policy-for-system-state"></a>Como alterar o padrão de backup e a política de retenção para o estado do sistema?

Para alterar o padrão de backup e a política de retenção para o estado do sistema:
1. Interrompa o mecanismo de backup. Execute o seguinte comando em um prompt de comandos com privilégios elevados.

  ```
  PS C:\> Net stop obengine
  ```

2. Adicionar ou atualizar as seguintes entradas de chave do registro em HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider.

  |Nome do Registro|Descrição|Valor|
  |-------------|-----------|-----|
  |SSBScheduleTime|Usado para configurar a hora do backup. O horário padrão está configurado para as 21 horas da hora local.|DWord: Formato HHMM (decimal). 2130, por exemplo, significa 21h30 da hora local.|
  |SSBScheduleDays|Usado para configurar os dias quando o backup do estado do sistema deve ser executado no momento especificado. Dígitos individuais especificam dias da semana. 0 representa domingo, 1 é a segunda-feira, e assim por diante. O dia padrão para o backup é domingo.|DWord: dias da semana para realizar o backup (decimal). 1230, por exemplo, agenda backups na segunda-feira, terça-feira, quarta-feira e domingo.|
  |SSBRetentionDays|Usado para configurar os dias para manter o backup. O valor padrão é de 60. O valor máximo permitido é de 180.|DWord: Dias para retenção de backup (decimal).|

3. Use o seguinte comando para reiniciar o mecanismo de backup.
    ```
    PS C:\> Net start obengine
    ```

4. Abra o agente de Serviços de Recuperação da Microsoft.

5. Clique em **Agendamento de Backup** e, em seguida, clique em **Próximo** até ver as alterações refletidas.

6. Clique em **Finalizar**para aplicar as alterações.


## <a name="questions"></a>Perguntas?
Se você tiver dúvidas ou gostaria de ver algum recurso incluído, [envie-nos seus comentários](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Próximas etapas
* Obtenha mais detalhes sobre o [backup de computadores que usam o Windows](backup-configure-vault.md).
* Agora que você faz backup de seus arquivos e pastas, poderá [gerenciar seus servidores e cofres](backup-azure-manage-windows-server.md).
* Se você precisar restaurar um backup, use este artigo para [restaurar os arquivos para um computador que usa o Windows](backup-azure-restore-windows-server.md).
