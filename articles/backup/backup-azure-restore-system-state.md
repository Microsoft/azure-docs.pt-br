---
title: Restaurar o estado do sistema para um Windows Server
description: Explicação passo a passo para restaurar o estado do sistema do Windows Server de um backup no Azure.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 4ef23d6ff16c263e310304cc240c2090751640b1
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97008462"
---
# <a name="restore-system-state-to-windows-server"></a>Restaurar o Estado do Sistema para Windows Server

Este artigo explica como restaurar backups de estado do sistema do Windows Server de um cofre de Serviços de Recuperação do Azure. Para restaurar o estado do sistema, você deve ter um backup de estado do sistema (criado usando as instruções em [fazer backup do estado do sistema](backup-azure-system-state.md#back-up-windows-server-system-state)e verificar se você instalou a [versão mais recente do agente de serviços de recuperação do Microsoft Azure (MARS)](https://aka.ms/azurebackup_agent). Recuperar dados de estado do sistema do Windows Server de um cofre de serviços de recuperação do Azure é um processo de duas etapas:

1. Restaure o estado do sistema como arquivos de Backup do Azure. Ao restaurar o estado do sistema como arquivos de Backup do Azure, você pode:
   * Restaurar o estado do sistema para o mesmo servidor em que os backups foram realizados, ou
   * Restaurar o arquivo de estado do sistema para um servidor alternativo.

2. Aplique os arquivos de estado do sistema restaurados a um Windows Server usando o utilitário Backup do Windows Server.

## <a name="recover-system-state-files-to-the-same-server"></a>Recuperar arquivos de estado do sistema para o mesmo servidor

As etapas a seguir explicam como reverter a configuração do Windows Server para um estado anterior. Reverter a configuração do servidor para um estado conhecido, estável, pode ser extremamente valioso. As etapas a seguir restauram o estado do sistema do servidor de um cofre de Serviços de Recuperação.

1. Abra o snap-in do **Backup do Microsoft Azure** . Se você não souber onde o snap-in foi instalado, pesquise **Backup do Microsoft Azure** no computador ou servidor.

    O aplicativo da área de trabalho deve aparecer nos resultados da pesquisa.

2. Selecione **Recuperar Dados** para iniciar o assistente.

    ![Recuperar Dados](./media/backup-azure-restore-windows-server/recover.png)

3. No painel de **introdução** , para restaurar os dados para o mesmo servidor ou computador, selecione **este servidor ( `<server name>` )** e selecione **Avançar**.

    ![Escolha esta opção de servidor para restaurar os dados no mesmo computador](./media/backup-azure-restore-system-state/samemachine.png)

4. No painel **selecionar modo de recuperação** , escolha **estado do sistema** e, em seguida, selecione **Avançar**.

    ![Procurar arquivos](./media/backup-azure-restore-system-state/recover-type-selection.png)

5. No calendário no painel **Selecionar Volume e Data**, selecione um ponto de recuperação.

    Você pode restaurar de qualquer ponto de recuperação. As datas em **negrito** indicam a disponibilidade de pelo menos um ponto de recuperação. Depois de selecionar uma data, se houver vários pontos de recuperação disponíveis, escolha o ponto de recuperação específico no menu suspenso **Hora**.

    ![Volume e data](./media/backup-azure-restore-system-state/select-date.png)

6. Depois de escolher o ponto de recuperação a ser restaurado, selecione **Avançar**.

    O Backup do Azure monta o ponto de recuperação local e o usa como um volume de recuperação.

7. No próximo painel, especifique o destino para os arquivos de estado do sistema recuperados. Em seguida, selecione **procurar** para abrir o Windows Explorer e localize os arquivos e pastas desejados. A opção **Criar cópias para ter ambas as versões** cria cópias de arquivos individuais em um arquivo existente de estado do sistema em vez de criar a cópia do arquivo de estado do sistema inteiro.

    ![Opções de recuperação](./media/backup-azure-restore-system-state/recover-as-files.png)

8. Verifique os detalhes da recuperação no painel de **confirmação** e selecione **recuperar**.

   ![Selecione recuperar para confirmar a ação de recuperação](./media/backup-azure-restore-system-state/confirm-recovery.png)

9. Copie o diretório *WindowsImageBackup* no destino de recuperação para um volume não crítico do servidor. Geralmente, o volume do sistema operacional Windows é o volume crítico.

10. Depois que a recuperação for bem-sucedida, siga as etapas na seção [aplicar o estado do sistema restaurado em um Windows Server](#apply-restored-system-state-on-a-windows-server)para concluir o processo de recuperação do estado do sistema.

## <a name="recover-system-state-files-to-an-alternate-server"></a>Recuperar o arquivo de estado do sistema para um servidor alternativo

Se seu Windows Server estiver corrompido ou inacessível e você deseja restaurá-lo para um estado estável, recuperando o estado do sistema do Windows Server, você pode restaurar o estado do sistema do servidor corrompido de outro servidor. Use as seguintes etapas para a restauração de estado do sistema em um servidor separado.  

A terminologia usada nessas etapas inclui:

* *Computador de origem* – o computador original do qual o backup foi feito e que não está disponível no momento.
* *Computador de destino* – O computador para o qual os dados estão sendo recuperados.
* *Cofre de exemplo* – o cofre dos serviços de recuperação no qual o *computador de origem* e o *computador de destino* estão registrados.

> [!NOTE]
> Os backups extraídos de um computador não podem ser restaurados em um computador que executa uma versão anterior do sistema operacional. Por exemplo, os backups de um computador Windows Server 2016 não podem ser restaurados para o Windows Server 2012 R2. No entanto, o inverso é possível. Você pode usar os backups do Windows Server 2012 R2 para restaurar o Windows Server 2016.
>

1. Abra o snap-in **backup do Microsoft Azure** no *computador de destino*.
2. Verifique se o *computador de destino* e o *computador de origem* estão registrados no mesmo cofre dos serviços de recuperação.
3. Selecione **recuperar dados** para iniciar o fluxo de trabalho.
4. Selecione **Outro servidor**

    ![Outro servidor](./media/backup-azure-restore-system-state/anotherserver.png)

5. Forneça o arquivo de credencial de cofre que corresponde ao *Cofre de exemplo*. Se o arquivo de credencial do cofre for inválido (ou expirado), baixe um novo arquivo de credencial do cofre do *cofre de exemplo* no portal do Azure. Depois que o arquivo de credencial de cofre for fornecido, o cofre de Serviços de Recuperação associado ao arquivo de credencial de cofre é exibido.

6. No painel Selecionar Servidor de Backup, selecione o *Computador de origem* na lista de computadores exibidos.
7. No painel Selecionar modo de recuperação, escolha **estado do sistema** e selecione **Avançar**.

    ![Search](./media/backup-azure-restore-system-state/recover-type-selection.png)

8. No Calendário no painel **Selecionar Volume e Data**, selecione um ponto de recuperação. Você pode restaurar de qualquer ponto de recuperação. As datas em **negrito** indicam a disponibilidade de pelo menos um ponto de recuperação. Depois de selecionar uma data, se houver vários pontos de recuperação disponíveis, escolha o ponto de recuperação específico no menu suspenso **Hora**.

    ![Pesquisar itens](./media/backup-azure-restore-system-state/select-date.png)

9. Depois de escolher o ponto de recuperação a ser restaurado, selecione **Avançar**.

10. No painel **selecionar modo de recuperação do estado do sistema** , especifique o destino onde você deseja que os arquivos de estado do sistema sejam recuperados e, em seguida, selecione **Avançar**.

    ![Criptografia](./media/backup-azure-restore-system-state/recover-as-files.png)

    A opção **Criar cópias para ter ambas as versões** cria cópias de arquivos individuais em um arquivo existente de estado do sistema em vez de criar a cópia do arquivo de estado do sistema inteiro.

11. Verifique os detalhes da recuperação no painel de confirmação e selecione **recuperar**.

    ![Selecione o botão recuperar para confirmar o processo de recuperação](./media/backup-azure-restore-system-state/confirm-recovery.png)

12. Copie o diretório *WindowsImageBackup* para um volume não crítico do servidor (por exemplo D:\). Geralmente, o volume do sistema operacional Windows é o volume crítico.

13. Para concluir o processo de recuperação, use a seguinte seção para [aplicar os arquivos restaurados do estado do sistema em um Windows Server](#apply-restored-system-state-on-a-windows-server).

## <a name="apply-restored-system-state-on-a-windows-server"></a>Aplicar o estado do sistema restaurado em um Windows Server

Depois de recuperar o estado do sistema como arquivos usando o agente dos serviços de recuperação do Azure, use o utilitário Backup do Windows Server para aplicar o estado do sistema recuperado ao Windows Server. O utilitário de Backup do Windows Server já está disponível no servidor. As etapas a seguir explicam como aplicar o estado do sistema recuperado.

1. Abra o snap-in Backup do Windows Server. Se você não souber onde o snap-in foi instalado, pesquise **Backup do Windows Server** no computador ou servidor.

    O aplicativo da área de trabalho aparece nos resultados da pesquisa. Se ele não aparecer ou se você encontrar erros ao abrir o aplicativo, você deve instalar os **recursos de backup do Windows Server** e os componentes dependentes abaixo dele, que estão disponíveis no **Assistente para adicionar recursos** no **Gerenciador do servidor**.

1. No snap-in, selecione **Backup Local**.

    ![Selecionar Backup Local para restaurar a partir daí](./media/backup-azure-restore-system-state/win-server-backup-local-backup.png)

1. No console de backup local, no **painel Ações**, selecione **recuperar** para abrir o assistente de recuperação.

1. Selecione a opção, **um backup armazenado em outro local** e selecione **Avançar**.

   ![Escolha recuperar para um servidor diferente](./media/backup-azure-restore-system-state/backup-stored-in-diff-location.png)

1. Ao especificar o tipo de local, selecione **Pasta compartilhada remota** se o backup do estado do sistema foi recuperado em outro servidor. Se o estado do sistema foi recuperado localmente, então, selecione **unidades locais**.

    ![Selecione se é recuperação de servidor local ou outro](./media/backup-azure-restore-system-state/ss-recovery-remote-shared-folder.png)

1. Insira o caminho para o diretório *WindowsImageBackup* ou escolha a unidade local que contém esse diretório (por exemplo, D:\WindowsImageBackup), recuperada como parte da recuperação dos arquivos de estado do sistema usando o agente dos serviços de recuperação do Azure e selecione **Avançar**.

    ![Caminho para o arquivo compartilhado](./media/backup-azure-restore-system-state/ss-recovery-remote-folder.png)

1. Selecione a versão do estado do sistema que você deseja restaurar e selecione **Avançar**.

1. No painel Selecionar tipo de recuperação, selecione **estado do sistema** e selecione **Avançar**.

1. Para o local da recuperação do estado do sistema, selecione **local original** e selecione **Avançar**.

    Se estiver restaurando um controlador de domínio, você verá a seguinte opção adicional:

    ![Local para recuperação de estado do sistema](./media/backup-azure-restore-system-state/location-for-system-state-recovery.png)

    >[!NOTE]
    >Selecione apenas "executar uma restauração autoritativa de arquivos de Active Directory" se você pretender explicitamente fazer uma restauração autoritativa de todos os dados de Active Directory.

1. Examine os detalhes de confirmação, verifique as configurações de reinicialização, selecione **recuperar** para aplicar os arquivos de estado do sistema restaurados.

    ![Inicie a restauração de arquivos de estado do sistema](./media/backup-azure-restore-system-state/launch-ss-recovery.png)

    >[!NOTE]
    >Não selecione a opção **reinicializar automaticamente o servidor** se estiver executando a restauração no modo DSRM.

1. Depois de concluir uma restauração com êxito, você precisará reiniciar o servidor no modo normal. Abra um prompt de comando e digite o seguinte: `bcdedit /deletevalue safeboot`
1. Reinicialize o servidor.

## <a name="special-considerations-for-system-state-recovery-on-a-domain-controller"></a>Considerações especiais sobre a recuperação de estado do sistema em um controlador de domínio

O backup de estado do sistema inclui dados do Active Directory. Use as etapas a seguir para restaurar o Serviço de Domínio do Active Directory (AD DS) de seu estado atual para um estado anterior. Esse tipo de restauração pode ser feito em dois cenários:

* Restaurar todos os dados de Active Directory quando não há controladores de domínio em funcionamento restantes na floresta
* Restaurar uma parte do Active Directory dados quando esses objetos tiverem sido excluídos ou corrompidos

Este artigo abordará apenas o primeiro cenário, que chama uma restauração nonauthorative de AD DS e uma restauração autoritativa da pasta SYSVOL.  Se você precisar executar o segundo cenário (em que os controladores de domínio ainda estão funcionais, mas precisar restaurar objetos específicos do AD), consulte [estas instruções](https://support.microsoft.com/help/840001/how-to-restore-deleted-user-accounts-and-their-group-memberships-in-ac).

1. Siga as etapas aqui para [recuperar arquivos de estado do sistema para um servidor alternativo](#recover-system-state-files-to-an-alternate-server).
1. Use os seguintes comandos para reiniciar o servidor em *Modo de reparo de serviços de diretório*. Em um prompt de comandos com privilégios elevados:

    ```cmd
    Bcdedit /set safeboot dsrepair
    Shutdown /r /t 0
    ```

1. Para recuperar Active Directory como parte de uma restauração de estado do sistema, você pode escolher um dos dois métodos:

    * Siga as instruções acima para [aplicar o estado do sistema restaurado em um Windows Server](#apply-restored-system-state-on-a-windows-server) com o utilitário backup do Windows Server.

        >[!NOTE]
        >Se você estiver restaurando todos os dados de Active Directory (e não houver controladores de domínio em funcionamento restantes na floresta), na etapa 9 acima, certifique-se de selecionar **executar uma restauração autoritativa de arquivos de Active Directory**.

    * Use o utilitário [Wbadmin](/windows-server/administration/windows-commands/wbadmin-start-systemstaterecovery) para executar a restauração a partir da linha de comando.

        Você precisará do identificador de versão do backup que deseja usar. Você pode obter uma lista de identificadores de versão executando este comando:

        ```cmd
        wbadmin get versions -backuptarget <servername\sharename>
        ```

        Em seguida, você usa esse identificador de versão para executar a restauração.

        Por exemplo, para executar uma [restauração nonauthorative de AD DS e uma restauração autoritativa da pasta SYSVOL](/windows-server/identity/ad-ds/manage/ad-forest-recovery-nonauthoritative-restore) usando o backup de 04/30/2020 às 9:00, que é armazenado no recurso compartilhado `\\servername\share` para `server01` , digite:

        ```cmd
        wbadmin start systemstaterecovery -version:04/30/2020-09:00 -backupTarget:\\servername\share -machine:server01 -authsysvol
        ```

1. Depois de concluir uma restauração com êxito, você deve reiniciar o servidor no modo normal. Abra um prompt de comando e digite o seguinte: `bcdedit /deletevalue safeboot`
1. Reinicialize o servidor.

Para obter mais informações, consulte [fazer backup e restaurar Active Directory controladores de domínio](active-directory-backup-restore.md).

## <a name="troubleshoot-failed-system-state-restore"></a>Solucionar problemas de restauração de estado do sistema com falha

Se o processo anterior de aplicação do estado do sistema não for concluído com êxito, use o ambiente de recuperação do Windows (Win RE) para recuperar o Windows Server. As etapas a seguir explicam como recuperar usando o Win RE. Use esta opção somente se o Windows Server não inicializar normalmente após uma restauração do estado do sistema. O processo a seguir apaga dados que não são do sistema, tenha cuidado.

1. Inicialização do Windows Server para o ambiente de recuperação do Windows (Windows RE).

2. Selecione a solução de problemas entre as três opções disponíveis.

    ![Selecionar solução de problemas](./media/backup-azure-restore-system-state/winre-1.png)

3. Na tela **Opções Avançadas**, selecione o **Prompt de comando** e forneça o nome de usuário de administrador do servidor e a senha.

   ![Selecionar prompt de comando](./media/backup-azure-restore-system-state/winre-2.png)

4. Forneça o nome de usuário de administrador do servidor e a senha.

    ![Digitar senha](./media/backup-azure-restore-system-state/winre-3.png)

5. Quando você abrir o prompt de comando no modo de administrador, execute o seguinte comando para obter as versões de backup de estado do sistema.

    ```cmd
    Wbadmin get versions -backuptarget:<Volume where WindowsImageBackup folder is copied>:
    ```

    ![Obter versões de backup do estado do sistema](./media/backup-azure-restore-system-state/winre-4.png)

6. Execute o seguinte comando para obter todos os volumes disponíveis no backup.

    ```cmd
    Wbadmin get items -version:<copy version from above step> -backuptarget:<Backup volume>
    ```

    ![Obter todos os volumes disponíveis](./media/backup-azure-restore-system-state/winre-5.png)

7. O comando a seguir recupera todos os volumes que fazem parte do backup do estado do sistema. Observe que essa etapa recupera somente os volumes críticos que fazem parte do estado do sistema. Todos os dados que não são do sistema são apagados.

    ```cmd
    Wbadmin start recovery -items:C: -itemtype:Volume -version:<Backupversion> -backuptarget:<backup target volume>
    ```

     ![Recuperar todos os volumes](./media/backup-azure-restore-system-state/winre-6.png)

## <a name="next-steps"></a>Próximas etapas

* Agora que você restaurou seus arquivos e pastas, poderá [gerenciar seus backups](backup-azure-manage-windows-server.md).
