---
title: Fazer backup de compartilhamentos de arquivo do Azure no portal do Azure
description: Saiba como usar o portal do Azure para fazer backup de compartilhamentos de arquivos do Azure no cofre dos serviços de recuperação
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: ca49f1ad48ab0534b27b91ad6a5a50b393cda782
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88890341"
---
# <a name="back-up-azure-file-shares"></a>Backup de compartilhamentos de arquivos do Azure

Este artigo explica como fazer backup de [compartilhamentos de arquivos do Azure](../storage/files/storage-files-introduction.md) do portal do Azure.

Neste artigo, você aprenderá a:

* Crie um cofre dos Serviços de Recuperação.
* Configurar o backup do cofre dos serviços de recuperação
* Configurar o backup no painel Compartilhamento de arquivos
* Executar um trabalho de backup sob demanda para criar um ponto de restauração

## <a name="prerequisites"></a>Pré-requisitos

* [Saiba mais](azure-file-share-backup-overview.md) sobre a solução de backup baseada em instantâneo do compartilhamento de arquivos do Azure.
* Verifique se o compartilhamento de arquivos está presente em um dos [tipos de conta de armazenamento com suporte](azure-file-share-support-matrix.md).
* Identifique ou crie um [cofre dos serviços de recuperação](#create-a-recovery-services-vault) na mesma região que a conta de armazenamento que hospeda o compartilhamento de arquivos.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="configure-backup-from-the-recovery-services-vault"></a>Configurar o backup do cofre dos serviços de recuperação

As etapas a seguir explicam como você pode configurar o backup para vários compartilhamentos de arquivos no painel cofre dos serviços de recuperação:

1. No [portal do Azure](https://portal.azure.com/), abra o cofre dos serviços de recuperação que você deseja usar para configurar o backup para o compartilhamento de arquivos.

1. No painel **cofre dos serviços de recuperação** , selecione o **+ backup** no menu na parte superior.

   ![Cofre dos Serviços de Recuperação](./media/backup-afs/recovery-services-vault.png)

    1. No painel **meta de backup** , defina **onde sua carga de trabalho está em execução?** no **Azure** , selecionando a opção **Azure** na lista suspensa.

          ![Escolher o Azure como carga de trabalho](./media/backup-afs/backup-goal.png)

    2. Em **o que você deseja fazer backup?**, selecione **compartilhamento de arquivos do Azure** na lista suspensa.

          ![Selecionar FileShare do Azure](./media/backup-afs/select-azure-file-share.png)

    3. Selecione **backup** para registrar a extensão de compartilhamento de arquivos do Azure no cofre.

          ![Selecione backup para associar o compartilhamento de arquivos do Azure ao cofre](./media/backup-afs/register-extension.png)

1. Depois de selecionar **backup**, o painel **backup** é aberto. Para selecionar a conta de armazenamento que hospeda o compartilhamento de arquivos que você deseja proteger, selecione a caixa de texto **selecionar** link abaixo da **conta de armazenamento** .

   ![Escolha o link Selecionar](./media/backup-afs/choose-select-link.png)

1. O **painel Selecionar conta de armazenamento** é aberto à direita, listando um conjunto de contas de armazenamento descobertos com suporte. Eles estão associados a este cofre ou estão presentes na mesma região que o cofre, mas ainda não estão associados a nenhum cofre de serviços de recuperação.

1. Na lista de contas de armazenamento descobertas, selecione uma conta e selecione **OK**.

   ![Selecionar entre as contas de armazenamento descobertas](./media/backup-afs/select-discovered-storage-account.png)

1. A próxima etapa é selecionar os compartilhamentos de arquivos que você deseja fazer backup. Selecione o botão **Adicionar** na seção **fileshares to backup** .

   ![Selecione os compartilhamentos de arquivos para fazer backup](./media/backup-afs/select-file-shares-to-back-up.png)

1. O painel de contexto **selecionar compartilhamentos de arquivos** é aberto à direita. O Azure pesquisa a conta de armazenamento para compartilhamentos de arquivos cujo backup pode ser feito. Se você adicionou recentemente os compartilhamentos de arquivos e não os vê na lista, aguarde algum tempo para que os compartilhamentos de arquivos apareçam.

1. Na lista **selecionar compartilhamentos de arquivos** , selecione um ou mais dos compartilhamentos de arquivos que você deseja fazer backup. Selecione **OK**.

   ![Selecionar os compartilhamentos de arquivos](./media/backup-afs/select-file-shares.png)

1. Para escolher uma política de backup para o compartilhamento de arquivos, você tem três opções:

   * Escolha a política padrão.<br>
   Essa opção permite que você habilite o backup diário que será retido por 30 dias. Se você não tiver uma política de backup existente no cofre, o painel backup será aberto com as configurações de política padrão. Se desejar escolher as configurações padrão, você poderá selecionar **habilitar backup**diretamente.

   * Criar uma nova política <br>

      1. Para criar uma nova política de backup para seu compartilhamento de arquivos, selecione o texto do link abaixo da lista suspensa na seção **política de backup** .<br>

         ![Criar política](./media/backup-afs/create-new-policy.png)

      1. O painel contexto da **política de backup** é aberto à direita. Especifique um nome de política na caixa de texto e escolha o período de retenção de acordo com seu requisito. Somente a opção de retenção diária é habilitada por padrão. Se você quiser ter uma retenção semanal, mensal ou anual, marque a caixa de seleção correspondente e forneça o valor de retenção desejado.

      1. Depois de especificar os valores de retenção e um nome de política válido, selecione **OK**.<br>

         ![Fornecer o nome da política e os valores de retenção](./media/backup-afs/policy-name.png)

   * Escolha uma das políticas de backup existentes <br>

      Para escolher uma das políticas de backup existentes para configurar a proteção, selecione a política desejada na lista suspensa **política de backup** .<br>

      ![Escolher política existente](./media/backup-afs/choose-existing-policy.png)

1. Selecione **habilitar backup** para começar a proteger o compartilhamento de arquivos.

   ![Escolha habilitar backup](./media/backup-afs/enable-backup.png)

Depois de definir uma política de backup, um instantâneo dos compartilhamentos de arquivos é obtido no horário agendado. O ponto de recuperação também é mantido para o período escolhido.

>[!NOTE]
>O backup do Azure agora dá suporte a políticas com retenção diária/semanal/mensal/anual para o backup do compartilhamento de arquivos do Azure.

## <a name="configure-backup-from-the-file-share-pane"></a>Configurar o backup no painel Compartilhamento de arquivos

As etapas a seguir explicam como você pode configurar o backup para compartilhamentos de arquivos individuais no respectivo painel de compartilhamento de arquivos:

1. Na [portal do Azure](https://portal.azure.com/), abra a conta de armazenamento que hospeda o compartilhamento de arquivos que você deseja fazer backup.

1. Uma vez na conta de armazenamento, selecione o bloco rotulado **compartilhamentos de arquivos**. Você também pode navegar até **compartilhamentos de arquivos** por meio do Sumário da conta de armazenamento.

   ![Conta de armazenamento](./media/backup-afs/storage-account.png)

1. Na listagem compartilhamento de arquivos, você deve ver todos os compartilhamentos de arquivos presentes na conta de armazenamento. Selecione o compartilhamento de arquivos do qual você deseja fazer backup.

   ![Lista de compartilhamentos de arquivos](./media/backup-afs/file-shares-list.png)

1. Selecione **backup** na seção **operações** do painel Compartilhamento de arquivos. O painel **Configurar backup** será carregado à direita.

   ![Configurar o painel de backup](./media/backup-afs/configure-backup.png)

1. Para a seleção do cofre dos serviços de recuperação, siga um destes procedimentos:

    * Se você já tiver um cofre, selecione o botão de opção Selecionar cofre de serviços de recuperação **existente** e escolha um dos cofres existentes no menu suspenso **nome do cofre** .

       ![Selecionar cofre existente](./media/backup-afs/select-existing-vault.png)

    * Se você não tiver um cofre, selecione o botão de opção **criar novo** cofre de serviços de recuperação. Especifique um nome para o cofre. Ele é criado na mesma região que o compartilhamento de arquivos. Por padrão, o cofre é criado no mesmo grupo de recursos que o compartilhamento de arquivos. Se você quiser escolher um grupo de recursos diferente, selecione **criar novo** link abaixo do menu suspenso **tipo de recurso** e especifique um nome para o grupo de recursos. Selecione **OK** para continuar.

       ![Criar novo cofre](./media/backup-afs/create-new-vault.png)

      >[!IMPORTANT]
      >Se a conta de armazenamento estiver registrada em um cofre ou se houver poucos compartilhamentos protegidos na conta de armazenamento que hospedam o compartilhamento de arquivos que você está tentando proteger, o nome do cofre dos serviços de recuperação será preenchido previamente e você não poderá editá-lo [mais aqui](backup-azure-files-faq.md#why-cant-i-change-the-vault-to-configure-backup-for-the-file-share).

1. Para a seleção de **política de backup** , siga um destes procedimentos:

    * Deixe a política padrão. Ele agendará backups diários com uma retenção de 30 dias.

    * Selecione uma política de backup existente, se você tiver uma, no menu suspenso **política de backup** .

       ![Escolher política de backup](./media/backup-afs/choose-backup-policy.png)

    * Crie uma nova política com retenção diária/semanal/mensal/anual de acordo com seu requisito.  

         1. Selecione o texto de link **criar uma nova política** .

         2. O painel contexto da **política de backup** é aberto à direita. Especifique um nome de política na caixa de texto e escolha o período de retenção de acordo com seu requisito. Somente a opção de retenção diária é habilitada por padrão. Se você quiser ter uma retenção semanal, mensal ou anual, marque a caixa de seleção correspondente e forneça o valor de retenção desejado.

         3. Depois de especificar os valores de retenção e um nome de política válido, selecione **OK**.

            ![Criar nova política de backup](./media/backup-afs/create-new-backup-policy.png)

1. Selecione **habilitar backup** para começar a proteger o compartilhamento de arquivos.

   ![Selecione Habilitar backup.](./media/backup-afs/select-enable-backup.png)

1. Você pode acompanhar o progresso da configuração nas notificações do portal ou monitorando os trabalhos de backup no cofre que você está usando para proteger o compartilhamento de arquivos.

   ![Notificações do portal](./media/backup-afs/portal-notifications.png)

1. Após a conclusão da operação de configuração de backup, selecione **backup** na seção **operações** do painel Compartilhamento de arquivos. O painel de contexto listando o **cofre Essentials** será carregado à direita. A partir daí, você pode disparar operações de backup e restauração sob demanda.

   ![Conceitos básicos do cofre](./media/backup-afs/vault-essentials.png)

## <a name="run-an-on-demand-backup-job"></a>Executar um trabalho de backup sob demanda

Ocasionalmente, talvez você queira gerar um instantâneo de backup ou um ponto de recuperação, fora dos horários agendados na política de backup. Um motivo comum para gerar um backup sob demanda é logo depois de configurar a política de backup. Com base na agenda na política de backup, pode ser horas ou dias até que um instantâneo seja tirado. Para proteger seus dados até que a política de backup seja ativada, inicie um backup sob demanda. Muitas vezes, é necessário criar um backup sob demanda antes de fazer alterações planejadas em seus compartilhamentos de arquivos.

### <a name="from-the-recovery-services-vault"></a>Do cofre dos serviços de recuperação

1. Abra o cofre dos serviços de recuperação que você usou para fazer backup do compartilhamento de arquivos. No painel **visão geral** , selecione **itens de backup** na seção **itens protegidos** .

   ![Selecionar itens de backup](./media/backup-afs/backup-items.png)

1. Depois de selecionar **itens de backup**, um novo painel que lista todos os **tipos de gerenciamento de backup** é exibido ao lado do painel **visão geral** .

   ![Lista de tipos de gerenciamento de backup](./media/backup-afs/backup-management-types.png)

1. Na lista **tipo de gerenciamento de backup** , selecione **armazenamento do Azure (arquivos do Azure)**. Você verá uma lista de todos os compartilhamentos de arquivos e das contas de armazenamento correspondentes com backup usando esse cofre.

   ![Itens de backup do armazenamento do Azure (arquivos do Azure)](./media/backup-afs/azure-files-backup-items.png)

1. Na lista de compartilhamentos de arquivos do Azure, selecione o compartilhamento de arquivos desejado. Os detalhes do **item de backup** são exibidos. No menu **item de backup** , selecione **fazer backup agora**. Como esse trabalho de backup está sob demanda, não há nenhuma política de retenção associada ao ponto de recuperação.

   ![Selecione fazer backup agora](./media/backup-afs/backup-now.png)

1. O painel **fazer backup agora** é aberto. Especifique até que dia deseja manter o ponto de recuperação escolhendo o último dia. Você pode ter uma retenção máxima de 10 anos para um backup sob demanda.

   ![Escolher data de retenção](./media/backup-afs/retention-date.png)

1. Selecione **OK** para confirmar o trabalho de backup sob demanda que é executado.

1. Monitore as notificações do portal para manter um controle da conclusão da execução do trabalho de backup. Você pode monitorar o andamento do trabalho no painel do cofre. Selecione os **trabalhos**  >  **de backup em andamento**.

### <a name="from-the-file-share-pane"></a>No painel Compartilhamento de arquivos

1. Abra o painel **visão geral** do compartilhamento de arquivos para o qual você deseja fazer um backup sob demanda.

1. Selecione **backup** na seção **operação** . O painel de contexto listando o **cofre Essentials** será carregado à direita. Selecione **fazer backup agora** para fazer um backup sob demanda.

   ![Selecione fazer backup agora](./media/backup-afs/select-backup-now.png)

1. O painel **fazer backup agora** é aberto. Especifique a retenção para o ponto de recuperação. Você pode ter uma retenção máxima de 10 anos para um backup sob demanda.

   ![Reter data do backup](./media/backup-afs/retain-backup-date.png)

1. Selecione **OK** para confirmar.

>[!NOTE]
>O backup do Azure bloqueia a conta de armazenamento quando você configura a proteção para qualquer compartilhamento de arquivos na conta correspondente. Isso fornece proteção contra a exclusão acidental de uma conta de armazenamento com compartilhamentos de arquivos de backup.

## <a name="best-practices"></a>Práticas recomendadas

* Não exclua os instantâneos criados pelo backup do Azure. A exclusão de instantâneos pode resultar na perda de pontos de recuperação e/ou em falhas de restauração.

* Não remova o bloqueio obtido na conta de armazenamento pelo backup do Azure. Se você excluir o bloqueio, sua conta de armazenamento será propenso a exclusão acidental e, se ela for excluída, você perderá seus instantâneos ou backups.

## <a name="next-steps"></a>Próximas etapas

Saiba como:

* [Restaurar compartilhamentos de arquivos do Azure](restore-afs.md)
* [Gerenciar backups do compartilhamento de arquivos do Azure](manage-afs-backup.md)
