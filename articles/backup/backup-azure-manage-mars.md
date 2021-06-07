---
title: Gerenciar e monitorar backups do agente MARS
description: Saiba como gerenciar e monitorar backups de agente MARS (Serviços de Recuperação do Microsoft Azure) usando o serviço de backup do Azure.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 25f0c41b535f9403d0a7027687cc5261cd437275
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97368589"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>Gerenciar backups de agente MARS (Serviços de Recuperação do Microsoft Azure) usando o serviço de backup do Azure

Este artigo descreve como gerenciar arquivos e pastas dos quais é feito backup com o agente de Serviços de Recuperação do Microsoft Azure.

## <a name="modify-a-backup-policy"></a>Modificar uma política de backup

Ao modificar a política de backup, você pode adicionar novos itens, remover itens existentes do backup ou excluir arquivos do backup usando as configurações de exclusão.

- **Adicionar itens** Use esta opção somente para adicionar novos itens para backup. Para remover itens existentes, use a opção **remover itens** ou **configurações de exclusão** .  
- **Remover itens** Use esta opção para remover itens do backup.
  - Use **as configurações de exclusão** para remover todos os itens de um volume em vez de **remover itens**.
  - Limpar todas as seleções em um volume faz com que os backups antigos dos itens sejam retidos de acordo com as configurações de retenção no momento do último backup, sem escopo para modificação.
  - Reselecionando esses itens, os clientes potenciais de um primeiro backup completo e novas alterações de política não são aplicados a backups antigos.
  - Desmarcar o volume inteiro mantém o backup anterior sem nenhum escopo para modificar a política de retenção.
- **Configurações de exclusão** Use essa opção para excluir itens específicos do backup.

### <a name="add-new-items-to-existing-policy"></a>Adicionar novos itens à política existente

1. Em **ações**, selecione **agendar backup**.

    ![Agendar um backup do Windows Server](./media/backup-configure-vault/schedule-first-backup.png)

2. Na guia **selecionar item de política** e selecione **Modificar agendamento de backup para seus arquivos e pastas** e selecione **Avançar**.

    ![Selecionar itens de política](./media/backup-azure-manage-mars/select-policy-items.png)

3. Na guia **modificar ou parar backup de agenda** , selecione **fazer alterações nos itens ou horários de backup** e selecione **Avançar**.

    ![Modificar ou agendar backup](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. Na guia **selecionar itens para backup** , selecione **Adicionar itens** para adicionar os itens que você deseja fazer backup.

    ![Modificar ou agendar adicionar itens de backup](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. Na janela **selecionar itens** , selecione os arquivos ou as pastas que você deseja adicionar e selecione **OK**.

    ![Selecionar os itens](./media/backup-azure-manage-mars/select-item.png)

6. Conclua as próximas etapas e selecione **concluir** para concluir a operação.

### <a name="add-exclusion-rules-to-existing-policy"></a>Adicionar regras de exclusão à política existente

Você pode adicionar regras de exclusão para ignorar arquivos e pastas dos quais você não deseja fazer backup. Você pode fazer isso durante a definição de uma nova política ou a modificação de uma política existente.

1. No painel Ações, selecione **agendar backup**. Vá para **selecionar itens para backup** e selecione **configurações de exclusão**.

    ![Configurações de Exclusão](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. Em **configurações de exclusão**, selecione **Adicionar exclusão**.

    ![Adicionar exclusão](./media/backup-azure-manage-mars/add-exclusion.png)

3. Em **selecionar itens para excluir**, procure os arquivos e as pastas e selecione os itens que você deseja excluir e selecione **OK**.

    ![Selecionar os itens a serem excluídos](./media/backup-azure-manage-mars/select-items-exclude.png)

4. Por padrão, todas as **subpastas** dentro das pastas selecionadas são excluídas. Você pode alterar isso selecionando **Sim** ou **não**. Você pode editar e especificar os tipos de arquivo a serem excluídos, conforme mostrado abaixo:

    ![Selecionar tipos de subpastas](./media/backup-azure-manage-mars/subfolders-type.png)

5. Conclua as próximas etapas e selecione **concluir** para concluir a operação.

### <a name="remove-items-from-existing-policy"></a>Remover itens da política existente

1. No painel Ações, selecione **agendar backup**. Vá para **selecionar itens para backup**. Na lista, selecione os arquivos e as pastas que você deseja remover do agendamento de backup e selecione **remover itens**.

    ![Selecionar os itens a serem removidos](./media/backup-azure-manage-mars/select-items-remove.png)

    > [!NOTE]
    > Continue com cuidado ao remover completamente um volume da política.  Se você precisar adicioná-lo novamente, ele será tratado como um novo volume. O próximo backup agendado executará um backup inicial (backup completo) em vez de backup incremental. Se você precisar remover temporariamente e adicionar itens posteriormente, é recomendável usar **as configurações de exclusões** em vez de **remover itens** para garantir o backup incremental em vez do backup completo.

2. Conclua as próximas etapas e selecione **concluir** para concluir a operação.

## <a name="stop-protecting-files-and-folder-backup"></a>Parar de proteger arquivos e pastas de backup

Há duas maneiras de interromper a proteção de arquivos e pastas backup:

- **Interromper a proteção e manter os dados de backup**.
  - Esta opção irá parar todos os trabalhos de backup futuros da proteção.
  - O serviço de backup do Azure continuará a reter todos os pontos de recuperação existentes.  
  - Você poderá restaurar os dados de backup para pontos de recuperação não expirados.
  - Se você decidir retomar a proteção, poderá usar a opção *habilitar agendamento de backup novamente* . Depois disso, os dados serão retidos com base na nova política de retenção.
- **Interrompa a proteção e exclua os dados de backup**.
  - Esta opção impedirá que todos os trabalhos de backup futuros protejam seus dados e excluam todos os pontos de recuperação.
  - Você receberá um email de alerta de exclusão de dados de backup com uma mensagem *os dados deste item de backup foram excluídos. Esses dados estarão temporariamente disponíveis por 14 dias, após o qual serão excluídos permanentemente* e a ação recomendada *proteger novamente o item de backup dentro de 14 dias para recuperar os dados.*
  - Para retomar a proteção, proteja novamente dentro de 14 dias a partir da operação de exclusão.

### <a name="stop-protection-and-retain-backup-data"></a>Interromper a proteção e manter os dados de backup

1. Abra o console de gerenciamento do MARS, vá para o **painel Ações** e **selecione agendar backup**.

    ![Selecione agendar backup](./media/backup-azure-manage-mars/mars-actions.png)
1. Na página **selecionar item de política** , selecione **modificar um agendamento de backup para seus arquivos e pastas** e selecione **Avançar**.

    ![Selecionar item de política](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. Na página **modificar ou parar um backup agendado** , selecione **parar de usar este agendamento de backup, mas mantenha os backups armazenados até que uma agenda seja ativada novamente**. Em seguida, selecione **Avançar**.

    ![Interromper um backup agendado.](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. Em **Pausar backup agendado**, examine as informações e selecione **concluir**.

    ![Pausar um backup agendado.](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. Em **Modificar o progresso do backup**, verifique se a pausa do backup de agenda está em status de êxito e selecione **fechar** para concluir.

### <a name="stop-protection-and-delete-backup-data"></a>Interromper a proteção e excluir dados de backup

1. Abra o console de gerenciamento do MARS, vá para o painel **ações** e selecione **agendar backup**.
2. Na página **modificar ou parar um backup agendado** , selecione **parar de usar este agendamento de backup e excluir todos os backups armazenados**. Em seguida, selecione **Avançar**.

    ![Modificar ou parar um backup agendado.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Na página **parar um backup agendado** , selecione **concluir**.

    ![Pare um backup agendado e selecione concluir](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. Você será solicitado a inserir um PIN de segurança (número de identificação pessoal), que deve ser gerado manualmente. Para fazer isso, primeiro entre no portal do Azure.
5. Vá para **serviços de recuperação**  >  **configurações** do cofre  >  **Propriedades**.
6. Em **PIN de segurança**, selecione **gerar**. Copie este PIN. O PIN é válido por apenas cinco minutos.
7. No console de gerenciamento, Cole o PIN e selecione **OK**.

    ![Gerar um PIN de segurança.](./media/backup-azure-delete-vault/security-pin.png)

8. Na página **progresso do backup de modificação** , a seguinte mensagem é exibida: os *dados de backup excluídos serão mantidos por 14 dias. Após esse período, os dados de backup serão excluídos permanentemente.*  

    ![Modificar o progresso do backup](./media/backup-azure-delete-vault/deleted-backup-data.png)

Depois de excluir os itens de backup locais, siga as próximas etapas do Portal.

## <a name="re-enable-protection"></a>Reabilitar a proteção

Se você interrompeu a proteção enquanto retém os dados e decidiu retomar a proteção, você pode reabilitar o agendamento de backup usando modificar política de backup.

1. Em **ações** , selecione **agendar backup**.
1. Selecione **habilitar novamente o agendamento de backup. Você também pode modificar itens ou horários de backup** e selecionar **Avançar**.<br>

    ![Reabilitar agendamento de backup](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. Em **selecionar itens para backup**, selecione **Avançar**.

    ![Selecionar itens para backup](./media/backup-azure-manage-mars/re-enable-next.png)
1. Em **especificar agendamento de backup**, especifique o agendamento de backup e selecione **Avançar**.
1. Em **selecionar política de retenção**, especifique a duração da retenção e selecione **Avançar**.
1. Por fim, na tela de **confirmação** , examine os detalhes da política e selecione **concluir**.

## <a name="re-generate-passphrase"></a>Gerar novamente a senha

Uma frase secreta é usada para criptografar e descriptografar dados durante o backup ou a restauração do seu computador local ou localmente usando o agente MARS para ou do Azure. Se você perdeu ou esqueceu a frase secreta, poderá regenerar a frase secreta (desde que seu computador ainda esteja registrado com o cofre dos serviços de recuperação e o backup esteja configurado) seguindo estas etapas:

1. No console do agente Mars, vá para o **painel Ações**  >  **alterar propriedades** >. Em seguida, vá para a **guia criptografia**.<br>
1. Selecione a caixa de seleção **alterar senha** .<br>
1. Insira uma nova senha ou selecione **gerar senha**.
1. Selecione **procurar** para salvar a nova senha.

    ![Gerar frase secreta.](./media/backup-azure-manage-mars/passphrase.png)

1. Selecione **OK** para aplicar as alterações.  Se o [recurso de segurança](./backup-azure-security-feature.md#enable-security-features) estiver habilitado no portal do Azure para o cofre dos serviços de recuperação, será solicitado que você insira o PIN de segurança. Para receber o PIN, siga as etapas listadas neste [artigo](./backup-azure-security-feature.md#authentication-to-perform-critical-operations).<br>
1. Cole o PIN de segurança do portal e selecione **OK** para aplicar as alterações.<br>

    ![Colar o PIN de segurança](./media/backup-azure-manage-mars/passphrase2.png)
1. Certifique-se de que a frase secreta seja salva com segurança em um local alternativo (que não seja o computador de origem), preferencialmente na Azure Key Vault. Mantenha o controle de todas as senhas se você tiver várias máquinas cujo backup está sendo feito com os agentes MARS.

## <a name="managing-backup-data-for-unavailable-machines"></a>Gerenciando dados de backup para computadores não disponíveis

Esta seção discute um cenário em que o computador de origem que foi protegido com MARS não está mais disponível porque foi excluído, corrompido, infectado com malware/ransomware ou encerrado.

Para esses computadores, o serviço de backup do Azure garante que o ponto de recuperação mais recente não expire (ou seja, não seja removido) de acordo com as regras de retenção especificadas na política de backup. Portanto, você pode restaurar o computador com segurança.  Considere os seguintes cenários que você pode executar nos dados de backup:

### <a name="scenario-1-the-source-machine-is-unavailable-and-you-no-longer-need-to-retain-backup-data"></a>Cenário 1: o computador de origem está indisponível e você não precisa mais manter os dados de backup

- Você pode excluir os dados de backup do portal do Azure usando as etapas listadas neste [artigo](backup-azure-delete-vault.md#delete-protected-items-on-premises).

### <a name="scenario-2-the-source-machine-is-unavailable-and-you-need-to-retain-backup-data"></a>Cenário 2: o computador de origem está indisponível e você precisa reter os dados de backup

O gerenciamento da política de backup para MARS é feito por meio do console do MARS e não pelo portal. Se você precisar estender as configurações de retenção para os pontos de recuperação existentes antes que eles expirem, será necessário restaurar o computador, instalar o console do MARS e estender a política.

- Para restaurar o computador, execute as seguintes etapas:
  1. [Restaurar a VM em um computador de destino alternativo](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)
  1. Recriar o computador de destino com o mesmo nome de host que o computador de origem
  1. Instalar o agente e registrar novamente no mesmo cofre e com a mesma senha
  1. Inicie o cliente MARS para estender a duração da retenção de acordo com seus requisitos
- Seu computador recentemente restaurado, protegido com MARS, continuará a fazer backups.  

## <a name="configuring-antivirus-for-the-mars-agent"></a>Configurando o antivírus para o agente MARS

Recomendamos a configuração a seguir para seu software antivírus para evitar conflitos com a operação do agente MARS.

1. **Adicionar exclusões de caminho**: para evitar degradação de desempenho e possíveis conflitos, exclua os seguintes caminhos do monitoramento em tempo real pelo software antivírus:
    1. `%ProgramFiles%\Microsoft Azure Recovery Services Agent` e subpastas
    1. **Pasta de rascunho**: se a pasta de rascunho não estiver no local padrão, adicione-a também às exclusões.  [Consulte aqui para obter as etapas](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible) para determinar o local da pasta de rascunho.
1. **Adicionar exclusões binárias**: para evitar a degradação de atividades de backup e de console, exclua processos para os seguintes binários do monitoramento em tempo real pelo software antivírus:
    1. `%ProgramFiles%\Microsoft Azure Recovery Services Agent\bin\cbengine.exe`

>[!NOTE]
>Embora a exclusão desses caminhos seja suficiente para a maioria dos softwares antivírus, algumas delas ainda podem continuar a interferir nas operações do agente MARS. Se você estiver vendo falhas inesperadas, desinstale o software antivírus temporariamente e monitore para ver se o problema desaparece. Se isso resolver o problema, entre em contato com seu fornecedor de software antivírus para obter assistência com a configuração adequada de seus produtos.

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre cenários e limitações com suporte, consulte a [matriz de suporte para o agente Mars](./backup-support-matrix-mars-agent.md).
- Saiba mais sobre o [comportamento de retenção da política de backup sob demanda](backup-windows-with-mars-agent.md#set-up-on-demand-backup-policy-retention-behavior).
- Para perguntas mais frequentes, consulte as [perguntas frequentes do agente Mars](backup-azure-file-folder-backup-faq.md).
