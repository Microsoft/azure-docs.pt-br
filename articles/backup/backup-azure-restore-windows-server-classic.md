---
title: "Restaurar dados para um Windows Server ou Client do Azure usando o modelo de implantação clássico | Microsoft Docs"
description: Saiba como restaurar de um Windows Server ou Windows Client.
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
ms.assetid: 85585dfc-c764-4e8c-8f0e-40b969640ac2
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: saurse;trinadhk;markgal;
ms.openlocfilehash: 300b2b17b44e21ed446fd63d572a2461e2fc1343
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="restore-files-to-a-windows-server-or-windows-client-machine-using-the-classic-deployment-model"></a>Restaurar arquivos em um computador de cliente do Windows ou Windows Server usando o modelo de implantação do clásico
> [!div class="op_single_selector"]
> * [Portal clássico](backup-azure-restore-windows-server-classic.md)
> * [Portal do Azure](backup-azure-restore-windows-server.md)
>
>

Este artigo explica como recuperar dados de um cofre de backup e restaurá-lod para um computador ou servidor. A partir de março de 2017, você não poderá mais usar o portal clássico para criar os cofres de backup.

> [!IMPORTANT]
> Agora você pode atualizar os cofres de Backup para cofres dos Serviços de Recuperação. Para obter detalhes, veja o artigo [Atualizar um cofre de Backup para um cofre dos Serviços de Recuperação](backup-azure-upgrade-backup-to-recovery-services.md). A Microsoft incentiva você a atualizar os cofres de Backup para os cofres dos Serviços de Recuperação.<br/> **15 de outubro de 2017**, você não poderá mais usar o PowerShell para criar cofres de Backup. <br/> **A partir de 1º de novembro de 2017**:
>- Nenhum cofre de Backup restante será atualizado automaticamente para os cofres dos Serviços de Recuperação.
>- Você não poderá acessar os dados de backup no portal clássico. Em vez disso, use o portal do Azure para acessar os dados de backup nos cofres dos Serviços de Recuperação.
>

Para restaurar dados, você pode usar o assistente recuperar dados do agente do Serviços de Recuperação do Microsoft Azure (MARS). Ao restaurar dados, é possível:

* Restaurar dados para o mesmo computador do qual os backups foram realizados.
* Restaurar dados para um computador alternativo.

Em janeiro de 2017, a Microsoft lançou uma atualização de Versão Prévia para o agente do MARS. Juntamente com as correções de bug, essa atualização permite Restauração Instantânea, que permite que você monte um instantâneo de ponto de recuperação gravável como um volume de recuperação. Em seguida, é possível explorar os arquivos de volume de recuperação e de cópia em um computador local e, assim, restaurar arquivos de forma seletiva.

> [!NOTE]
> A [atualização do Backup do Azure de janeiro de 2017](https://support.microsoft.com/en-us/help/3216528?preview) será necessária se você desejar usar a Restauração Instantânea para restaurar dados. Além disso, os dados de backup devem ser protegidos em cofres nas localidades listadas no artigo de suporte. Consulte a [atualização do Backup do Azure de janeiro de 2017](https://support.microsoft.com/en-us/help/3216528?preview) para obter a lista mais recente de localidades que oferecem suporte à Restauração Instantânea. No momento, a Restauração Instantânea **não** está disponível em todas as localidades.
>

A Restauração Instantânea está disponível para uso em cofres dos Serviços de Recuperação no portal do Azure e em cofres de Backup no portal clássico. Se desejar usar a Restauração Instantânea, baixe a atualização do MARS e siga os procedimentos que mencionam a Restauração Instantânea.


## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>Use a Restauração Instantânea para recuperar dados no mesmo computador

Se você excluiu acidentalmente um arquivo e deseja restaurá-lo para o mesmo computador (do qual o backup foi feito), as etapas a seguir o ajudarão a recuperar os dados.

1. Abra o snap-in do **Backup do Microsoft Azure** . Se você não souber onde o snap-in foi instalado, pesquise **Backup do Microsoft Azure** no computador ou servidor.

    O aplicativo da área de trabalho deve aparecer nos resultados da pesquisa.

2. Clique em **Recuperar Dados** para iniciar o assistente.

    ![Recuperar Dados](./media/backup-azure-restore-windows-server/recover.png)

3. No painel **Introdução**, para restaurar os dados para o mesmo computador ou servidor, selecione **Este servidor (`<server name>`)** e clique em **Próximo**.

    ![Escolha a opção Este servidor para restaurar os dados para o mesmo computador](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. No painel **Selecionar Modo de Recuperação**, escolha **Pastas e arquivos individuais** e, em seguida, clique em **Avançar**.

    ![Procurar arquivos](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)

5. No painel **Selecionar Volume e Data**, selecione o volume que contém os arquivos e/ou pastas que deseja restaurar.

    No calendário, selecione um ponto de recuperação. Você pode restaurar de qualquer ponto de recuperação. As datas em **negrito** indicam a disponibilidade de pelo menos um ponto de recuperação. Depois de selecionar uma data, se houver vários pontos de recuperação disponíveis, escolha o ponto de recuperação específico no menu suspenso **Hora**.

    ![Volume e data](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. Depois de ter escolhido o ponto de recuperação a ser restaurado, clique em **Montar**.

    O Backup do Azure monta o ponto de recuperação local e o usa como um volume de recuperação.

7. No painel **Procurar e Recuperar Arquivos**, clique em **Procurar** para abrir o Windows Explorer e localize os arquivos e pastas desejados.

    ![Opções de recuperação](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)


8. No Windows Explorer, copie os arquivos e/ou pastas que deseja restaurar e cole-os em qualquer localização local no servidor ou computador. Você pode abrir ou transmitir os arquivos diretamente do volume de recuperação e verificar se as versões corretas são recuperadas.

    ![Copiar e colar arquivos e pastas do volume montado na localização local](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)

9. Quando você terminar de restaurar os arquivos e/ou pastas, no painel **Procurar e Recuperar Arquivos**, clique em **Desmontar**. Clique em **Sim** para confirmar que deseja desmontar o volume.

    ![Desmontar o volume e confirmar](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Se você não clicar em Desmontar, o Volume de Recuperação permanecerá montado por seis horas a partir da hora em que foi montado. Não será executada nenhuma operação de backup enquanto o volume estiver montado. Qualquer operação de backup agendada para execução durante o tempo em que o volume estiver montado será executada após o volume de recuperação ser desmontado.
    >


## <a name="recover-data-to-the-same-machine"></a>Recuperar dados para o mesmo computador
Se você excluiu acidentalmente um arquivo e deseja restaurá-lo para o mesmo computador (do qual o backup foi feito), as etapas a seguir o ajudarão a recuperar os dados.

1. Abra o snap-in do **Backup do Microsoft Azure** .
2. Clique em **Recuperar Dados** para iniciar o fluxo de trabalho.

    ![Recuperar Dados](./media/backup-azure-restore-windows-server-classic/recover.png)
3. Selecione a opção **Este servidor (*nomeseucomputador*)** para restaurar o backup do arquivo no mesmo computador.

    ![Mesmo computador](./media/backup-azure-restore-windows-server-classic/samemachine.png)
4. Escolha **Procurar arquivos** ou **Pesquisar arquivos**.

    Deixe a opção padrão se você planeja restaurar um ou mais arquivos cujo caminho é conhecido. Se você não tiver certeza sobre a estrutura de pastas, mas gostaria de procurar por um arquivo, selecione a opção **Pesquisar arquivos** . Para fins desta seção, prosseguiremos com a opção padrão.

    ![Procurar arquivos](./media/backup-azure-restore-windows-server-classic/browseandsearch.png)
5. Selecione o volume do qual você deseja restaurar o arquivo.

    Você pode restaurar de qualquer momento anterior. As datas que aparecem em **negrito** no controle de calendário indicam a disponibilidade de um ponto de restauração. Quando uma data é selecionada, com base em seu agendamento de backup (e o sucesso de uma operação de backup), você pode selecionar um ponto no tempo no menu suspenso **Tempo** .

    ![Volume e data](./media/backup-azure-restore-windows-server-classic/volanddate.png)
6. Selecione os itens que serão recuperados. Você pode selecionar várias pastas/arquivos que deseja restaurar.

    ![Selecionar arquivos](./media/backup-azure-restore-windows-server-classic/selectfiles.png)
7. Especifique os parâmetros de recuperação.

    ![Opções de recuperação](./media/backup-azure-restore-windows-server-classic/recoveroptions.png)

   * Você tem uma opção de restaurar no local original (em que a pasta/o arquivo será substituído) ou em outro local no mesmo computador.
   * Se o arquivo/pasta que você deseja restaurar existir no local de destino, você tem a opção de criar cópias (duas versões do mesmo arquivo), substituir os arquivos no local de destino ou ignorar a recuperação dos arquivos que existem no destino.
   * É altamente recomendável que você deixe a opção padrão de restaurar as ACLs nos arquivos que estão sendo recuperados.
8. Depois que essas entradas forem fornecidas, clique em **Próximo**. O fluxo de trabalho de recuperação, que restaura os arquivos para essa máquina, será iniciado.

## <a name="recover-to-an-alternate-machine"></a>Recuperar em um computador alternativo
Se o servidor inteiro for perdido, você ainda pode recuperar dados do backup do Azure para um computador diferente. As etapas a seguir ilustram o fluxo de trabalho.  

A terminologia usada nessas etapas inclui:

* *Máquina de origem* : a máquina original da qual o backup foi feito e que está indisponível no momento.
* *Computador de destino* – O computador para o qual os dados estão sendo recuperados.
* *Cofre de exemplo* – Cofre de backup em que o *Computador de origem* e o *Computador de destino são registrados*. <br/>

> [!NOTE]
> Os backups de um computador não podem ser restaurados em um computador que esteja executando uma versão anterior do sistema operacional. Por exemplo, se um backup for de um computador com Windows 7, ele poderá ser restaurado em um computador com Windows 8 ou superior. No entanto, o contrário não pode ocorrer.
>
>

1. Abra o snap-in do **Backup do Microsoft Azure** no *Computador de destino*.
2. Verifique se o *Computador de destino* e o *Computador de origem* serão restaurados no mesmo cofre de backup.
3. Clique em **Recuperar Dados** para iniciar o fluxo de trabalho.

    ![Recuperar Dados](./media/backup-azure-restore-windows-server-classic/recover.png)
4. Selecione **Outro servidor**

    ![Outro servidor](./media/backup-azure-restore-windows-server-classic/anotherserver.png)
5. Forneça o arquivo de credencial de cofre que corresponde ao *Cofre de exemplo*. Se o arquivo de credencial do cofre for inválido (ou tiver expirado), baixe um novo arquivo de credencial do *Cofre de exemplo* no Portal do Azure clássico. Depois que o arquivo de credencial de cofre for fornecido, o cofre de backup relacionado ao arquivo de credencial de cofre será exibido.
6. Selecione o *Computador de origem* na lista de computadores exibidos.

    ![Lista de computadores](./media/backup-azure-restore-windows-server-classic/machinelist.png)
7. Selecione a opção **Pesquisar arquivos** ou **Procurar arquivos**. Para os fins desta seção, usaremos a opção **Pesquisar arquivos** .

    ![Pesquisar](./media/backup-azure-restore-windows-server-classic/search.png)
8. Na próxima tela, selecione o volume e a data. Procure o nome do arquivo/pasta que você deseja restaurar.

    ![Pesquisar itens](./media/backup-azure-restore-windows-server-classic/searchitems.png)
9. Selecione o local no qual os arquivos precisam ser restaurados.

    ![Restaurar local](./media/backup-azure-restore-windows-server-classic/restorelocation.png)
10. Forneça a senha de criptografia determinada durante o registro da *Máquina de origem* no *Cofre de exemplo*.

    ![Criptografia](./media/backup-azure-restore-windows-server-classic/encryption.png)
11. Após a entrada ser fornecida, clique no botão **Recuperar**, o que inicia a restauração dos arquivos de backup no destino fornecido.

## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>Usar a Restauração Instantânea para restaurar dados em um computador alternativo
Se o servidor inteiro for perdido, você ainda pode recuperar dados do backup do Azure para um computador diferente. As etapas a seguir ilustram o fluxo de trabalho.

A terminologia usada nessas etapas inclui:

* *Máquina de origem* : a máquina original da qual o backup foi feito e que está indisponível no momento.
* *Computador de destino* – O computador para o qual os dados estão sendo recuperados.
* *Cofre de exemplo* – o cofre Serviços de Recuperação no qual a *Máquina de origem* e a *Máquina de destino* estão registradas. <br/>

> [!NOTE]
> Os backups não podem ser restaurados em um computador de destino executando uma versão anterior do sistema operacional. Por exemplo, um backup feito em um computador com Windows 7 pode ser restaurado em um computador com Windows 8 ou mais recente. Um backup feito em um computador com Windows 8 não pode ser restaurado em um computador com Windows 7.
>
>

1. Abra o snap-in do **Backup do Microsoft Azure** no *Computador de destino*.

2. Verifique se o *Computador de destino* e o *Computador de origem* estão registrados no mesmo cofre de Serviços de Recuperação.

3. Clique em **Recuperar Dados** para abrir o **Assistente de Recuperação de Dados**.

    ![Recuperar Dados](./media/backup-azure-restore-windows-server/recover.png)

4. No painel **Introdução**, selecione **Outro servidor**

    ![Outro servidor](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. Forneça o arquivo de credencial de cofre que corresponde ao *Cofre de exemplo* e clique em **Próximo**.

    Se o arquivo de credencial de cofre for inválido (ou tiver expirado), baixe um novo arquivo de credencial de cofre do *Cofre de exemplo* no Portal do Azure. Depois de fornecer uma credencial de cofre válida, o nome do Cofre de Backup correspondente aparecerá.

6. No painel **Selecionar Servidor de Backup**, selecione o *Computador de origem* na lista de computadores exibidos e forneça a senha. Em seguida, clique em **Próximo**.

    ![Lista de computadores](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. No painel **Selecionar Modo de Recuperação**, selecione **Pastas e arquivos individuais** e clique em **Próximo**.

    ![Pesquisar](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. No painel **Selecionar Volume e Data**, selecione o volume que contém os arquivos e/ou pastas que deseja restaurar.

    No calendário, selecione um ponto de recuperação. Você pode restaurar de qualquer ponto de recuperação. As datas em **negrito** indicam a disponibilidade de pelo menos um ponto de recuperação. Depois de selecionar uma data, se houver vários pontos de recuperação disponíveis, escolha o ponto de recuperação específico no menu suspenso **Hora**.

    ![Pesquisar itens](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. Clique em **Montar** para montar localmente o ponto de recuperação como um volume de recuperação em seu *Computador de destino*.

10. No painel **Procurar e Recuperar Arquivos**, clique em **Procurar** para abrir o Windows Explorer e localize os arquivos e pastas desejados.

    ![Criptografia](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. No Windows Explorer, copie os arquivos e/ou pastas do volume de recuperação e cole-os na localização de seu *Computador de destino*. Você pode abrir ou transmitir os arquivos diretamente do volume de recuperação e verificar se as versões corretas são recuperadas.

    ![Criptografia](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. Quando você terminar de restaurar os arquivos e/ou pastas, no painel **Procurar e Recuperar Arquivos**, clique em **Desmontar**. Clique em **Sim** para confirmar que deseja desmontar o volume.

    ![Criptografia](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > Se você não clicar em Desmontar, o Volume de Recuperação permanecerá montado por seis horas a partir da hora em que foi montado. Não será executada nenhuma operação de backup enquanto o volume estiver montado. Qualquer operação de backup agendada para execução durante o tempo em que o volume estiver montado será executada após o volume de recuperação ser desmontado.
    >


## <a name="next-steps"></a>Próximas etapas
* [Backup do Azure - Perguntas frequentes](backup-azure-backup-faq.md)
* Visite o [Fórum de backup do Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933).

## <a name="learn-more"></a>Saiba mais
* [Visão geral do backup do Azure](http://go.microsoft.com/fwlink/p/?LinkId=222425)
* [Fazer backup de máquinas virtuais do Azure](backup-azure-vms-introduction.md)
* [Fazer backup de cargas de trabalho Microsoft](backup-azure-dpm-introduction.md)
