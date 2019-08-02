---
title: 'Serviço de Backup do Azure: Restaurar o estado do sistema para um Windows Server'
description: Explicação passo a passo para restaurar o estado do sistema do Windows Server de um backup no Azure.
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 8/18/2017
ms.author: saurse
ms.openlocfilehash: 48eb9fce7b9863c84518546270a6566bb0cec711
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465642"
---
# <a name="restore-system-state-to-windows-server"></a>Restaurar o Estado do Sistema para Windows Server

Este artigo explica como restaurar backups de estado do sistema do Windows Server de um cofre de Serviços de Recuperação do Azure. Para restaurar o Estado do Sistema, você deve ter um backup de Estado do Sistema (criado usando as instruções em [Fazer backup do Estado do Sistema](backup-azure-system-state.md#back-up-windows-server-system-state)) e ter instalado a [última versão do agente do MARS (Serviços de Recuperação do Microsoft Azure)](https://aka.ms/azurebackup_agent). Recuperar dados de estado do sistema do Windows Server de um cofre de serviços de recuperação do Azure é um processo de duas etapas:

1. Restaure o estado do sistema como arquivos de Backup do Azure. Ao restaurar o estado do sistema como arquivos de Backup do Azure, você pode:
   * Restaurar o estado do sistema para o mesmo servidor em que os backups foram realizados, ou
   * Restaurar o arquivo de estado do sistema para um servidor alternativo.

2. Aplica os arquivos restaurados do estado do sistema para um Windows Server.


## <a name="recover-system-state-files-to-the-same-server"></a>Recuperar arquivos de estado do sistema para o mesmo servidor
As etapas a seguir explicam como reverter a configuração do Windows Server para um estado anterior. Reverter a configuração do servidor para um estado conhecido, estável, pode ser extremamente valioso. As etapas a seguir restauram o estado do sistema do servidor de um cofre de Serviços de Recuperação.

1. Abra o snap-in do **Backup do Microsoft Azure** . Se você não souber onde o snap-in foi instalado, pesquise **Backup do Microsoft Azure** no computador ou servidor.

    O aplicativo da área de trabalho deve aparecer nos resultados da pesquisa.

2. Clique em **Recuperar Dados** para iniciar o assistente.

    ![Recuperar Dados](./media/backup-azure-restore-windows-server/recover.png)

3. No painel **Introdução**, para restaurar os dados para o mesmo computador ou servidor, selecione **Este servidor (`<server name>`)** e clique em **Próximo**.

    ![Escolha a opção Este servidor para restaurar os dados para o mesmo computador](./media/backup-azure-restore-system-state/samemachine.png)

4. No painel **Selecionar Modo de Recuperação**, escolha **Estado do Sistema** e, em seguida, clique em **Avançar**.

    ![Procurar arquivos](./media/backup-azure-restore-system-state/recover-type-selection.png)

5. No calendário no painel **Selecionar Volume e Data**, selecione um ponto de recuperação.

    Você pode restaurar de qualquer ponto de recuperação. As datas em **negrito** indicam a disponibilidade de pelo menos um ponto de recuperação. Depois de selecionar uma data, se houver vários pontos de recuperação disponíveis, escolha o ponto de recuperação específico no menu suspenso **Hora**.

    ![Volume e data](./media/backup-azure-restore-system-state/select-date.png)

6. Depois de ter escolhido o ponto de recuperação a ser restaurado, clique em **Avançar**.

    O Backup do Azure monta o ponto de recuperação local e o usa como um volume de recuperação.

7. No próximo painel, especifique o destino para os arquivos recuperados do estado do sistema e clique em **Procurar** para abrir o Windows Explorer e localize os arquivos e pastas que você deseja. A opção **Criar cópias para ter ambas as versões** cria cópias de arquivos individuais em um arquivo existente de estado do sistema em vez de criar a cópia do arquivo de estado do sistema inteiro.

    ![Opções de recuperação](./media/backup-azure-restore-system-state/recover-as-files.png)

8. Verifique os detalhes da recuperação no painel de **confirmação** e clique em **Recuperar**.

   ![Clique em Recuperar para confirmar a ação de recuperação](./media/backup-azure-restore-system-state/confirm-recovery.png)

9. Copie o diretório *WindowsImageBackup* no destino de recuperação para um volume não crítico do servidor. Geralmente, o volume do sistema operacional Windows é o volume crítico.

10. Quando a recuperação for bem-sucedida, siga as etapas na seção [Aplicar arquivos restaurados de estado do sistema para o Windows Server](backup-azure-restore-system-state.md) para concluir o processo de recuperação do estado do sistema.

## <a name="recover-system-state-files-to-an-alternate-server"></a>Recuperar o arquivo de estado do sistema para um servidor alternativo

Se seu Windows Server estiver corrompido ou inacessível e você deseja restaurá-lo para um estado estável, recuperando o estado do sistema do Windows Server, você pode restaurar o estado do sistema do servidor corrompido de outro servidor. Use as seguintes etapas para a restauração de estado do sistema em um servidor separado.  

A terminologia usada nessas etapas inclui:

- *Máquina de origem* : a máquina original da qual o backup foi feito e que está indisponível no momento.
- *Computador de destino* – O computador para o qual os dados estão sendo recuperados.
- *Cofre de exemplo* – o cofre Serviços de Recuperação no qual a *Máquina de origem* e a *Máquina de destino* estão registradas. <br/>

> [!NOTE]
> Os backups de um computador não podem ser restaurados em um computador que esteja executando uma versão anterior do sistema operacional. Por exemplo, os backups de um computador Windows Server 2016 não podem ser restaurados para o Windows Server 2012 R2. No entanto, o inverso é possível. Você pode usar os backups do Windows Server 2012 R2 para restaurar o Windows Server 2016.
>

1. Abra o snap-in do **Backup do Microsoft Azure** no *Computador de destino*.
2. Verifique se a *Máquina de destino* e a *Máquina de origem* estão registradas no mesmo cofre Serviços de Recuperação.
3. Clique em **Recuperar Dados** para iniciar o fluxo de trabalho.
4. Selecione **Outro servidor**

    ![Outro servidor](./media/backup-azure-restore-system-state/anotherserver.png)

5. Forneça o arquivo de credencial de cofre que corresponde ao *Cofre de exemplo*. Se o arquivo de credencial de cofre for inválido (ou tiver expirado), baixe um novo arquivo de credencial de cofre do *Cofre de exemplo* no Portal do Azure. Depois que o arquivo de credencial de cofre for fornecido, o cofre de Serviços de Recuperação associado ao arquivo de credencial de cofre é exibido.

6. No painel Selecionar Servidor de Backup, selecione o *Computador de origem* na lista de computadores exibidos.
7. No painel Selecionar Modo de Recuperação, escolha **Estado do Sistema** e clique em **Avançar**.

    ![Pesquisar](./media/backup-azure-restore-system-state/recover-type-selection.png)

8. No Calendário no painel **Selecionar Volume e Data**, selecione um ponto de recuperação. Você pode restaurar de qualquer ponto de recuperação. As datas em **negrito** indicam a disponibilidade de pelo menos um ponto de recuperação. Depois de selecionar uma data, se houver vários pontos de recuperação disponíveis, escolha o ponto de recuperação específico no menu suspenso **Hora**.

    ![Pesquisar itens](./media/backup-azure-restore-system-state/select-date.png)

9. Depois de ter escolhido o ponto de recuperação a ser restaurado, clique em **Avançar**.

10. No painel **Selecionar modo de recuperação de estado do sistema**, especifique o destino onde você deseja que os arquivos de estado do sistema sejam recuperados, e em seguida, clique em **Avançar**.

    ![Criptografia](./media/backup-azure-restore-system-state/recover-as-files.png)

    A opção **Criar cópias para ter ambas as versões** cria cópias de arquivos individuais em um arquivo existente de estado do sistema em vez de criar a cópia do arquivo de estado do sistema inteiro.

11. Verifique os detalhes da recuperação no painel de Confirmação e clique em **Recuperar**.

    ![Clique no botão Recuperar para confirmar o processo de recuperação](./media/backup-azure-restore-system-state/confirm-recovery.png)

12. Copie o diretório *WindowsImageBackup* para um volume não crítico do servidor (por exemplo D:\). Geralmente, o volume do sistema operacional Windows é o volume crítico.

13. Para concluir o processo de recuperação, use a seguinte seção para [aplicar os arquivos restaurados do estado do sistema em um Windows Server](#apply-restored-system-state-on-a-windows-server).




## <a name="apply-restored-system-state-on-a-windows-server"></a>Aplicar o estado do sistema restaurado em um Windows Server

Uma vez você tiver recuperado o estado do sistema como arquivos usando o agente de serviços de recuperação do Azure, use o utilitário de Backup do Windows Server para aplicar o estado do sistema recuperado para o Windows Server. O utilitário de Backup do Windows Server já está disponível no servidor. As etapas a seguir explicam como aplicar o estado do sistema recuperado.

1. Use os seguintes comandos para reiniciar o servidor em *Modo de reparo de serviços de diretório*. Em um prompt de comandos com privilégios elevados:

    ```
    PS C:\> Bcdedit /set safeboot dsrepair
    PS C:\> Shutdown /r /t 0
    ```

2. Após a reinicialização, abra o snap-in Backup do Windows Server. Se você não souber onde o snap-in foi instalado, pesquise **Backup do Windows Server** no computador ou servidor.

    O aplicativo da área de trabalho aparece nos resultados da pesquisa.

3. No snap-in, selecione **Backup Local**.

    ![Selecionar Backup Local para restaurar a partir daí](./media/backup-azure-restore-system-state/win-server-backup-local-backup.png)

4. No console do Backup Local, no **Painel Ações**, clique em **Recuperar** para abrir o Assistente de Recuperação.

5. Selecione a opção **um backup armazenado em outro local**e clique em **Avançar**.

   ![Escolha recuperar para um servidor diferente](./media/backup-azure-restore-system-state/backup-stored-in-diff-location.png)

6. Ao especificar o tipo de local, selecione **Pasta compartilhada remota** se o backup do estado do sistema foi recuperado em outro servidor. Se o estado do sistema foi recuperado localmente, então, selecione **unidades locais**.

    ![Selecione se é recuperação de servidor local ou outro](./media/backup-azure-restore-system-state/ss-recovery-remote-shared-folder.png)

7. Insira o caminho para o diretório *WindowsImageBackup*, ou escolha a unidade local que contém esse diretório (por exemplo, D:\WindowsImageBackup) recuperado como parte da recuperação de arquivos de estado do sistema usando o agente de serviços de recuperação do Azure e clique em **Avançar**.

    ![Caminho para o arquivo compartilhado](./media/backup-azure-restore-system-state/ss-recovery-remote-folder.png)

8. Selecione a versão de estado do sistema que você deseja restaurar e clique em **Avançar**.

9. No painel Selecionar Tipo de Recuperação, selecione **Estado do Sistema** e clique em **Avançar**.

10. Para o local da Recuperação do Estado do Sistema, selecione **Local Original**, e clique em **Avançar**.

11. Examine os detalhes da confirmação, verifique as configurações de reinicialização e clique em **Recuperar** para aplicar os arquivos do estado do sistema restaurado.

    ![Inicie a restauração de arquivos de estado do sistema](./media/backup-azure-restore-system-state/launch-ss-recovery.png)

## <a name="special-considerations-for-system-state-recovery-on-active-directory-server"></a>Considerações especiais para recuperação de estado do sistema no servidor do Active Directory

O backup de estado do sistema inclui dados do Active Directory. Use as etapas a seguir para restaurar o Serviço de Domínio do Active Directory (AD DS) de seu estado atual para um estado anterior.

1. Reinicie o controlador de domínio no Modo de Restauração dos Serviços de Diretório (DSRM).
2. Siga as etapas [aqui](https://technet.microsoft.com/library/cc794755(v=ws.10).aspx) para usar cmdlets do Backup do Windows Server para recuperar o AD DS.


## <a name="troubleshoot-failed-system-state-restore"></a>Solucionar problemas de restauração de estado do sistema com falha

Se o processo anterior de aplicação de estado do sistema não for concluído com êxito, use o ambiente de recuperação do Windows (Windows RE) para recuperar o Windows Server. As etapas a seguir explicam como recuperar usando o Win RE. Use esta opção somente se o Windows Server não inicia normalmente após uma restauração de estado do sistema. O processo a seguir apaga dados que não são do sistema, tenha cuidado.

1. Inicialização do Windows Server para o ambiente de recuperação do Windows (Windows RE).

2. Selecione a solução de problemas entre as três opções disponíveis.

    ![Abrir menu](./media/backup-azure-restore-system-state/winre-1.png)

3. Na tela **Opções Avançadas**, selecione o **Prompt de comando** e forneça o nome de usuário de administrador do servidor e a senha.

   ![Abrir menu](./media/backup-azure-restore-system-state/winre-2.png)

4. Forneça o nome de usuário de administrador do servidor e a senha.

    ![Abrir menu](./media/backup-azure-restore-system-state/winre-3.png)

5. Quando você abrir o prompt de comando no modo de administrador, execute o seguinte comando para obter as versões de backup de estado do sistema.

    ```
    Wbadmin get versions -backuptarget:<Volume where WindowsImageBackup folder is copied>:
    ```
    ![obter versões de backup de estado do sistema](./media/backup-azure-restore-system-state/winre-4.png)

6. Execute o seguinte comando para obter todos os volumes disponíveis no backup.

    ```
    Wbadmin get items -version:<copy version from above step> -backuptarget:<Backup volume>
    ```

    ![obter versões de backup de estado do sistema](./media/backup-azure-restore-system-state/winre-5.png)

7. O comando a seguir recupera todos os volumes que fazem parte do backup do estado do sistema. Observe que essa etapa recupera somente os volumes críticos que fazem parte do estado do sistema. Todos os dados que não são do sistema são apagados.

    ```
    Wbadmin start recovery -items:C: -itemtype:Volume -version:<Backupversion> -backuptarget:<backup target volume>
    ```
     ![obter versões de backup de estado do sistema](./media/backup-azure-restore-system-state/winre-6.png)



## <a name="next-steps"></a>Próximas etapas
* Agora que você restaurou seus arquivos e pastas, poderá [gerenciar seus backups](backup-azure-manage-windows-server.md).
