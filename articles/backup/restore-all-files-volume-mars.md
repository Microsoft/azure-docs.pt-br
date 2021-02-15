---
title: Restaurar todos os arquivos em um volume com MARS
description: Saiba como restaurar todos os arquivos em um volume usando o agente MARS.
ms.topic: conceptual
ms.date: 01/17/2021
ms.openlocfilehash: 44c12809fc94f78721ab1788cb352076dfebabe4
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613086"
---
# <a name="restore-all-the-files-in-a-volume-using-the-mars-agent"></a>Restaurar todos os arquivos em um volume usando o agente MARS

Este artigo explica como restaurar todos os arquivos de backup em um volume inteiro usando o assistente para recuperar dados no agente de Serviços de Recuperação do Microsoft Azure (MARS). Você poderá:

- Restaure todos os arquivos de backup em um volume para o mesmo computador do qual os backups foram feitos.
- Restaurar todos os arquivos de backup em um volume para um computador alternativo.

>[!TIP]
>A opção **Volume** recupera todos os backups de dados em um volume especificado. Essa opção fornece velocidades de transferência mais rápidas (até 40 MBps) e é recomendada para recuperar dados de grande porte ou volumes inteiros.
>
>A **opção arquivos e pastas individuais** permite acesso rápido aos dados do ponto de recuperação. Ele é adequado para recuperação de arquivos individuais e é recomendado para um tamanho total de menos de 80 GB. Ele oferece velocidades de transferência ou cópia de até 6 MBps durante a recuperação.

## <a name="volume-level-restore-to-the-same-machine"></a>Restauração no nível do volume para o mesmo computador

As etapas a seguir ajudarão você a recuperar todos os arquivos de backup em um volume:

1. Abra o snap-in do **Backup do Microsoft Azure** . Se você não souber onde o snap-in foi instalado, pesquise **Backup do Microsoft Azure** no computador ou servidor. O aplicativo da área de trabalho deve aparecer nos resultados da pesquisa.

1. Selecione **Recuperar Dados** para iniciar o assistente.

    ![Menu recuperar dados](./media/restore-all-files-volume-mars/recover.png)

1. Na página **introdução** , para restaurar os dados para o mesmo servidor ou computador, selecione **este servidor (nome do servidor)**  >  **Avançar**.

    ![Página Introdução](./media/restore-all-files-volume-mars/same-machine-instant-restore.png)

1. Na página **selecionar modo de recuperação** , escolha **volume**  >  **Avançar**.

    ![Selecionar modo de recuperação](./media/restore-all-files-volume-mars/select-recovery-mode.png)

1. Na página **selecionar volume e data** , selecione o volume que você deseja restaurar.

    No calendário, selecione um ponto de recuperação. As datas em **negrito** indicam a disponibilidade de pelo menos um ponto de recuperação. Se houver vários pontos de recuperação disponíveis em uma data, escolha o ponto de recuperação específico no menu suspenso **Hora**.

     ![Selecionar volume e data](./media/restore-all-files-volume-mars/select-volume-and-date.png)

1. Na página **especificar opções de recuperação** , configure o comportamento de restauração.
    1. Escolha o destino de recuperação:
        - **Local original**: restaurar dados no caminho original.
        - **Outro local**: especifique um local alternativo para restaurar os dados.
    1. Escolha o comportamento para **quando os itens no backup já estiverem no destino de recuperação**:
        - **Crie cópias para que você tenha ambas as versões**: se um arquivo com o mesmo nome já existir, os dados no ponto de recuperação serão restaurados como uma cópia. A cópia terá um prefixo de nome de arquivo localizado usando a hora do trabalho de restauração local em um dos seguintes formatos:
            - `YYYY-MM-DD HH-mm Copy of <original file name>`
            - `YYYY-MM-DD HH-mm Copy (n) of <original file name>`
        - **Substituir versões existentes por versões recuperadas**: se já existir um arquivo com o mesmo nome, o conteúdo será substituído pelos dados no ponto de recuperação.
        - **Não recupere os itens que já existem no destino de recuperação**: se já existir um arquivo com o mesmo nome, ele será ignorado.
    1. **Habilite as permissões da lista de controle de acesso (ACL) de restauração para o arquivo ou pasta que está sendo recuperada** se o arquivo deve ser restaurado com as permissões originais no ponto de recuperação.
        ![Especificar opções de recuperação](./media/restore-all-files-volume-mars/specify-recovery-options.png)

1. Verifique os detalhes da recuperação no painel de **confirmação** e selecione **recuperar**.

    ![Detalhes da confirmação](./media/restore-all-files-volume-mars/confirmation-details.png)

1. Na página **progresso da recuperação** , monitore o progresso do trabalho de recuperação. O assistente também pode ser fechado com segurança e a operação de recuperação continuará em segundo plano. Você pode exibir o progresso novamente clicando duas vezes no trabalho de recuperação no painel.

## <a name="volume-level-restore-to-an-alternate-machine"></a>Restauração no nível de volume para um computador alternativo

As etapas a seguir ajudarão você a recuperar todos os arquivos de backup em um volume para um computador alternativo. Você pode usar estas etapas para recuperar dados do backup do Azure se o servidor inteiro for perdido.

Essas etapas incluem a seguinte terminologia:

- *Máquina de origem*: a máquina original da qual o backup foi feito e que está indisponível no momento.
- *Computador de destino* – O computador para o qual os dados estão sendo recuperados.
- *Cofre de exemplo*: o cofre dos Serviços de Recuperação no qual a máquina de origem e a máquina de destino estão registradas.

> [!NOTE]
> Os backups não podem ser restaurados em um computador de destino que esteja executando uma versão anterior do sistema operacional. Por exemplo, um backup feito em um computador com Windows 7 pode ser restaurado em um computador com Windows 7 (ou posterior). Um backup feito de um computador com Windows 10 não pode ser restaurado para um computador com Windows 7.

1. Abra o snap-in do **Backup do Microsoft Azure** no computador de destino.

1. Verifique se a máquina de destino e a máquina de origem estão registradas no mesmo cofre dos Serviços de Recuperação.

1. Clique em **Recuperar Dados** para abrir o **Assistente de Recuperação de Dados**.

    ![Captura de tela do backup do Azure, com a recuperação de dados realçada (restaurar no computador alternativo)](./media/backup-azure-restore-windows-server/recover.png)

1. Na página **Introdução**, selecione **Outro servidor**.

    ![Captura de tela do assistente para recuperar dados Introdução página (restaurar para computador alternativo)](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

1. Forneça o arquivo de credencial de cofre que corresponde ao cofre de exemplo e selecione **Próximo**.

    Se o arquivo de credencial do cofre for inválido (ou expirado), [Baixe um novo arquivo de credencial do cofre do cofre de exemplo](backup-azure-file-folder-backup-faq.md#where-can-i-download-the-vault-credentials-file) no portal do Azure. Depois de fornecer uma credencial de cofre válida, o nome do cofre de backup correspondente aparecerá.

1. Na página **Selecionar Servidor de Backup**, selecione o computador de origem na lista de computadores exibidos e forneça a senha. Em seguida, selecione **Avançar**.

    ![Captura de tela do assistente de recuperação de dados-página Selecionar servidor de backup (restaurar para computador alternativo)](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

1. Na página **selecionar modo de recuperação** , escolha **volume**  >  **Avançar**.

    ![Selecionar modo de recuperação](./media/restore-all-files-volume-mars/select-recovery-mode.png)

1. Na página **selecionar volume e data** , selecione o volume que você deseja restaurar.

    No calendário, selecione um ponto de recuperação. As datas em **negrito** indicam a disponibilidade de pelo menos um ponto de recuperação. Se houver vários pontos de recuperação disponíveis em uma data, escolha o ponto de recuperação específico no menu suspenso **Hora**.

     ![Selecionar volume e data](./media/restore-all-files-volume-mars/select-volume-and-date.png)

1. Na página **especificar opções de recuperação** , configure o comportamento de restauração.
    1. Escolha o destino de recuperação:
        - **Local original**: restaurar dados no caminho original.
        - **Outro local**: especifique um local alternativo para restaurar os dados.
    1. Escolha o comportamento para **quando os itens no backup já estiverem no destino de recuperação**:
        - **Crie cópias para que você tenha ambas as versões**: se um arquivo com o mesmo nome já existir, os dados no ponto de recuperação serão restaurados como uma cópia. A cópia terá um prefixo de nome de arquivo localizado usando a hora do trabalho de restauração local em um dos seguintes formatos:
            - `YYYY-MM-DD HH-mm Copy of <original file name>`
            - `YYYY-MM-DD HH-mm Copy (n) of <original file name>`
        - **Substituir versões existentes por versões recuperadas**: se já existir um arquivo com o mesmo nome, o conteúdo será substituído pelos dados no ponto de recuperação.
        - **Não recupere os itens que já existem no destino de recuperação**: se já existir um arquivo com o mesmo nome, ele será ignorado.
    1. **Habilite as permissões da lista de controle de acesso (ACL) de restauração para o arquivo ou pasta que está sendo recuperada** se o arquivo deve ser restaurado com as permissões originais no ponto de recuperação.
        ![Especificar opções de recuperação](./media/restore-all-files-volume-mars/specify-recovery-options.png)

1. Verifique os detalhes da recuperação no painel de **confirmação** e selecione **recuperar**.

    ![Detalhes da confirmação](./media/restore-all-files-volume-mars/confirmation-details.png)

1. Na página **progresso da recuperação** , monitore o progresso do trabalho de recuperação. O assistente também pode ser fechado com segurança e a operação de recuperação continuará em segundo plano. Você pode exibir o progresso novamente clicando duas vezes no trabalho de recuperação no painel.

## <a name="next-steps"></a>Próximas etapas

- Agora que você restaurou seus arquivos e pastas, poderá [gerenciar seus backups](backup-azure-manage-windows-server.md).
- Encontre [perguntas comuns sobre como fazer backup de arquivos e pastas](backup-azure-file-folder-backup-faq.md).
