---
title: Restaurar dados no Azure para um computador Windows ou Windows Server | Microsoft Docs
description: Saiba como restaurar os dados armazenados no Azure para um computador Windows ou Windows Server.
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
ms.assetid: 742f4b9e-c0ab-4eeb-8e22-ee29b83c22c4
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/16/2017
ms.author: saurse;trinadhk;markgal;
ms.openlocfilehash: 231dd61f95267b3a504ed70e9b3a5abc470b69b2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="restore-files-to-a-windows-server-or-windows-client-machine-using-resource-manager-deployment-model"></a>Restaurar arquivos em um computador de cliente do Windows ou Windows Server usando o modelo de implantação do Gerenciador de recursos
> [!div class="op_single_selector"]
> * [Portal do Azure](backup-azure-restore-windows-server.md)
> * [Portal clássico](backup-azure-restore-windows-server-classic.md)
>
>

Este artigo explica como restaurar dados de um cofre de backup. Para restaurar dados, você pode usar o assistente recuperar dados do agente do Serviços de Recuperação do Microsoft Azure (MARS). Ao restaurar dados, é possível:

* Restaurar dados para o mesmo computador do qual os backups foram realizados.
* Restaurar dados para um computador alternativo.

Em janeiro de 2017, a Microsoft lançou uma atualização de Versão Prévia para o agente do MARS. Juntamente com as correções de bug, essa atualização permite Restauração Instantânea, que permite que você monte um instantâneo de ponto de recuperação gravável como um volume de recuperação. Em seguida, é possível explorar os arquivos de volume de recuperação e de cópia em um computador local e, assim, restaurar arquivos de forma seletiva.

> [!NOTE]
> A [atualização do Backup do Azure de janeiro de 2017](https://support.microsoft.com/en-us/help/3216528?preview) será necessária se você desejar usar a Restauração Instantânea para restaurar dados. Além disso, os dados de backup devem ser protegidos em cofres nas localidades listadas no artigo de suporte. Consulte a [atualização do Backup do Azure de janeiro de 2017](https://support.microsoft.com/en-us/help/3216528?preview) para obter a lista mais recente de localidades que oferecem suporte à Restauração Instantânea. No momento, a Restauração Instantânea **não** está disponível em todas as localidades.
>

A Restauração Instantânea está disponível para uso em cofres dos Serviços de Recuperação no portal do Azure e em cofres de Backup no portal clássico. Se desejar usar a Restauração Instantânea, baixe a atualização do MARS e siga os procedimentos que mencionam a Restauração Instantânea.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

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
    > Se você não clicar em Desmontar, o Volume de Recuperação permanecerá montado por seis horas a partir da hora em que foi montado. No entanto, o tempo de montagem é estendido até um máximo de 24 horas no caso de uma cópia de arquivo em andamento. Não será executada nenhuma operação de backup enquanto o volume estiver montado. Qualquer operação de backup agendada para execução durante o tempo em que o volume estiver montado será executada após o volume de recuperação ser desmontado.
    >


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
    > Se você não clicar em Desmontar, o Volume de Recuperação permanecerá montado por seis horas a partir da hora em que foi montado. No entanto, o tempo de montagem é estendido até um máximo de 24 horas no caso de uma cópia de arquivo em andamento. Não será executada nenhuma operação de backup enquanto o volume estiver montado. Qualquer operação de backup agendada para execução durante o tempo em que o volume estiver montado será executada após o volume de recuperação ser desmontado.
    >

## <a name="troubleshooting"></a>Solucionar problemas
Se o Backup do Azure não tiver montado com êxito o volume de recuperação mesmo após os diversos minutos de clicar em **Montar** ou falha ao montar o volume de recuperação com um ou mais erros, siga as etapas abaixo para iniciar a recuperação normalmente.

1.  Cancele o processo de montagem em andamento caso ele esteja sendo executado por vários minutos.

2.  Verifique se você está na versão mais recente do agente de Backup do Azure. Para obter as informações de versão do agente de Backup do Azure, clique em **Sobre o Agente dos Serviços de Recuperação do Microsoft Azure** no painel **Ações** do console de Backup do Microsoft Azure e verifique se o número de **Versão** é igual a ou maior do que a versão mencionada [neste artigo](https://go.microsoft.com/fwlink/?linkid=229525). Você pode baixar a versão mais recente [aqui](https://go.microsoft.com/fwLink/?LinkID=288905)

3.  Vá para **Gerenciador de Dispositivos** -> **Controladores de Armazenamento** e verifique se você pode localizar o **Iniciador do Microsoft iSCSI**. Se você puder localizá-lo, vá diretamente para a etapa 7 abaixo. 

4.  Se você não puder localizar o serviço Iniciador do Microsoft iSCSI conforme mencionado na etapa 3, verifique se você pode encontrar uma entrada em **Gerenciador de Dispositivos** -> **Controladores de Armazenamento** chamada **Dispositivo Desconhecido** com a ID de Hardware **ROOT\ISCSIPRT**.

5.  Clique com o botão direito do mouse em **Dispositivo Desconhecido** e selecione **Atualizar Software de Driver**.

6.  Atualize o driver ao selecionar a opção para **Pesquisar automaticamente software de driver atualizado**. A conclusão da atualização deve alterar o **Dispositivo Desconhecido** para o **Iniciador do Microsoft iSCSI** conforme mostrado abaixo. 

    ![Criptografia](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  Vá para **Gerenciador de Tarefas** -> **Serviços (Local)** -> **Serviço Iniciador do Microsoft iSCSI**. 

    ![Criptografia](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)
    
8.  Reinicie o serviço Iniciador do Microsoft iSCSI ao clicar com o botão direito do mouse no serviço, clicar em **Parar** e continue clicando com o botão direito do mouse e clicando em **Iniciar**.

9.  Repita a recuperação usando a Restauração Instantânea. 

Se a recuperação ainda falhar, reinicialize o servidor/cliente. Se uma reinicialização não for desejável ou se a recuperação ainda falhar mesmo após a reinicialização do servidor, tente recuperar de um Computador Alternativo e contate o Suporte do Azure no [Portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) e envie uma solicitação de suporte.

## <a name="next-steps"></a>Próximas etapas
* Agora que você restaurou seus arquivos e pastas, poderá [gerenciar seus backups](backup-azure-manage-windows-server.md).
