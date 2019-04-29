---
title: Recuperar dados de um Servidor de Backup do Azure
description: Recupere os dados que você protegeu em um cofre dos Serviços de Recuperação de qualquer Servidor de Backup do Azure registrado nesse cofre.
services: backup
author: kasinh
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: kasinh
ms.openlocfilehash: d1fb3434f0d3954a07980963866bcd7cce004379
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60650736"
---
# <a name="recover-data-from-azure-backup-server"></a>Recuperar dados do Servidor de Backup do Azure
Você pode usar o Servidor de Backup do Azure para recuperar os dados de que fez backup em um cofre dos Serviços de Recuperação. O processo para fazer isso é integrado ao console de gerenciamento do Servidor de Backup do Azure e é semelhante ao fluxo de trabalho de recuperação para outros componentes do Backup do Azure.

> [!NOTE]
> Este artigo se aplica ao [System Center Data Protection Manager R2 2012 com UR7 ou posterior](https://support.microsoft.com/en-us/kb/3065246), combinado ao [agente mais recente do Backup do Azure](https://aka.ms/azurebackup_agent).
>
>

Para recuperar dados de um Servidor de Backup do Azure:

1. Na guia **Recuperação** do console de gerenciamento do Servidor de Backup do Azure, clique em **“Adicionar DPM Externo”** (no canto superior esquerdo da tela).   
    ![Adicionar DPM externo](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. Baixe as novas **credenciais do cofre** no cofre associado ao **Servidor de Backup do Azure** cujos dados serão recuperados, escolha o Servidor de Backup do Azure na lista de Servidores de Backup do Azure registrados no cofre dos Serviços de Recuperação e forneça a **senha de criptografia** associada ao servidor cujos dados serão recuperados.

    ![Credenciais do DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > Somente os Servidores de Backup do Azure associados ao mesmo cofre de registro podem recuperar dados um do outro.
   >
   >

    Depois que o Servidor de Backup do Azure externo é adicionado com êxito, você pode procurar os dados do servidor externo e do Servidor de Backup do Azure local na guia **Recuperação**.
3. Procure a lista de servidores de produção protegidos pelo Servidor de Backup do Azure externo disponíveis e selecione a fonte de dados apropriada.

    ![Procurar o servidor DPM externo](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. Selecione **o mês e o ano** da lista suspensa **Pontos de recuperação**, selecione a **Data de recuperação** de quando o ponto de recuperação foi criado e selecione o **Tempo de recuperação**.

    Uma lista de arquivos e pastas será exibida no painel inferior, que pode ser pesquisado e recuperado em qualquer local.

    ![Pontos de recuperação de servidor DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. Clique com o botão direito do mouse no item apropriado e clique em **Recuperar**.

    ![Recuperação do DPM externo](./media/backup-azure-alternate-dpm-server/recover.png)
6. Examine **Recuperar seleção**. Verifique a data e a hora da cópia de backup que está sendo recuperada, bem como a fonte da qual a cópia de backup foi criada. Se a seleção estiver incorreta, clique em **Cancelar** para voltar à guia de recuperação e selecionar o ponto de recuperação apropriado. Se a seleção estiver correta, clique em **Avançar**.

    ![Resumo de recuperação do DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. Selecione **Recuperar em um local alternativo**. **Navegue** até o local correto para a recuperação.

    ![Local alternativo de recuperação do DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. Escolha a opção relacionada a **criar cópia**, **Ignorar** ou **Substituir**.

   * **Criar cópia** – cria uma cópia do arquivo em caso de colisão de nomes.
   * **Ignorar** – se não houver uma colisão de nomes, não recuperará o arquivo que mantém o arquivo original.
   * **Substituir** – se houver uma colisão de nomes, substitui a cópia existente do arquivo.

     Escolha a opção apropriada para **Restaurar a segurança**. Você pode aplicar as configurações de segurança do computador de destino onde os dados serão recuperados ou as configurações de segurança que eram aplicáveis ao produto no momento em que o ponto de recuperação foi criado.

     Identifique se uma **Notificação** será enviada quando a recuperação for concluída com êxito.

     ![Notificações de recuperação do DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. A tela **Resumo** lista as opções escolhidas até agora. Após você clicar em **“Recuperar”**, os dados serão recuperados para o local adequado.

    ![Resumo de opções de recuperação do DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

   > [!NOTE]
   > O trabalho de recuperação pode ser monitorado na guia **Monitoramento** do Servidor de Backup do Azure.
   >
   >

    ![Recuperação de monitoramento](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. Você pode clicar em **Limpar DPM Externo** na guia **Recuperação** do servidor DPM para remover o modo de exibição do servidor DPM externo.

    ![Limpar o DPM externo](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## <a name="troubleshooting-error-messages"></a>Solucionando problemas de mensagens de erro
| Não. | Mensagem de erro | Etapas para solucionar problemas |
|:---:|:--- |:--- |
| 1. |Este servidor não está registrado no cofre especificado nas credenciais do cofre. |**Causa:** este erro aparece quando o arquivo de credencial do cofre selecionado não pertence ao cofre dos Serviços de Recuperação associado ao Servidor de Backup do Azure em que ocorre a tentativa de recuperação. <br> **Resolução:** baixe o arquivo de credencial de cofre do cofre dos Serviços de Recuperação em que o Servidor de Backup do Azure está registrado. |
| 2. |Os dados recuperáveis não estão disponíveis ou o servidor selecionado não é um servidor DPM. |**Causa:** não há outros Servidores de Backup do Azure registrados no cofre dos Serviços de Recuperação ou os servidores ainda não carregaram os metadados ou o servidor selecionado é não um Servidor de Backup do Azure (também conhecido como Windows Server ou Windows Client). <br> **Resolução:** se houver outros Servidores de Backup do Azure registrados no cofre dos Serviços de Recuperação, verifique se o agente do Backup do Azure mais recente está instalado. <br>Se houver outros Servidores de Backup do Azure registrados no cofre dos Serviços de Recuperação, aguarde um dia após a instalação para iniciar o processo de recuperação. A tarefa noturna carregará os metadados de todos os backups protegidos para a nuvem. Os dados estarão disponíveis para recuperação. |
| 3. |Nenhum outro servidor DPM está registrado no cofre. |**Causa:** não há nenhum outro Servidor de Backup do Azure registrado no cofre em que a recuperação está sendo tentada.<br>**Resolução:** se houver outros Servidores de Backup do Azure registrados no cofre dos Serviços de Recuperação, verifique se o agente do Backup do Azure mais recente está instalado.<br>Se houver outros Servidores de Backup do Azure registrados no cofre dos Serviços de Recuperação, aguarde um dia após a instalação para iniciar o processo de recuperação. O trabalho noturno carregará os metadados de todos os backups protegidos para a nuvem. Os dados estarão disponíveis para recuperação. |
| 4. |Senha de criptografia fornecida não corresponde à senha associada ao seguinte servidor:  **\<nome do servidor >** |**Causa:** a senha de criptografia usada no processo de criptografia de dados dos dados do Servidor de Backup do Azure que estão sendo recuperados não corresponde à senha de criptografia fornecida. O agente não pode descriptografar os dados. Portanto, a recuperação falha.<br>**Resolução:** forneça exatamente a mesma senha de criptografia associada ao Servidor de Backup do Azure cujos dados serão recuperados. |

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="why-cant-i-add-an-external-dpm-server-after-installing-ur7-and-latest-azure-backup-agent"></a>Por que não consigo adicionar um servidor DPM externo após instalar o UR7 e o agente de Backup do Azure mais recente?

Para os servidores DPM com fontes de dados protegidas na nuvem (usando um pacote cumulativo de atualizações anterior ao Pacote de Cumulativo de Atualizações 7), é necessário aguardar pelo menos um dia após instalar o UR7 e o agente de Backup do Azure mais recente para começar a **Adicionar servidor DPM externo**. O período de um dia é necessário para carregar os metadados dos grupos de proteção do DPM no Azure. Os metadados do grupo de proteção são carregados na primeira vez por meio de um trabalho noturno.

### <a name="what-is-the-minimum-version-of-the-microsoft-azure-recovery-services-agent-needed"></a>Qual é a versão mínima necessária do agente dos Serviços de Recuperação do Microsoft Azure?

A versão mínima do agente dos Serviços de Recuperação do Microsoft Azure ou do agente de Backup do Azure necessária para habilitar esse recurso é 2.0.8719.0.  Para exibir a versão do agente: abra o Painel de Controle **>** Todos os Itens do Painel de Controle **>** Programas e recursos **>** Agente dos Serviços de Recuperação do Microsoft Azure. Se a versão for anterior à 2.0.8719.0, baixe e instale o [agente do Backup do Azure mais recente](https://go.microsoft.com/fwLink/?LinkID=288905).

![Limpar o DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-azurebackupagentversion.png)

## <a name="next-steps"></a>Próximas etapas:
•    [Perguntas frequentes sobre o Backup do Azure](backup-azure-backup-faq.md)
