---
title: Configurar o backup operacional para BLOBs do Azure
description: Saiba como configurar e gerenciar o backup operacional para BLOBs do Azure (em versão prévia)
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 0dc490842389ba9286799aef5d37c1cf7c1ba64e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102051065"
---
# <a name="configure-operational-backup-for-azure-blobs-in-preview"></a>Configurar o backup operacional para BLOBs do Azure (em versão prévia)

O backup do Azure permite configurar facilmente o backup operacional para proteger blobs de blocos em suas contas de armazenamento. Este artigo explica como configurar o backup operacional em uma ou mais contas de armazenamento usando o portal do Azure. O artigo discute o seguinte:

- O que você precisa saber antes de começar
- Criando um cofre de backup
- Concedendo permissões para o cofre de backup nas contas de armazenamento a serem protegidas
- Criando uma política de backup
- Configurando o backup operacional em uma ou mais contas de armazenamento
- Efeitos sobre o backup das contas de armazenamento

## <a name="before-you-start"></a>Antes de começar

- O backup operacional de BLOBs é uma solução de backup local que mantém os dados por uma duração especificada na própria conta de armazenamento de origem. Essa solução não mantém uma cópia adicional dos dados no cofre.
- Essa solução permite que você mantenha seus dados para restauração por até 360 dias. No entanto, as durações de retenção demoradas podem levar mais tempo durante a operação de restauração.
- A solução pode ser usada para executar restaurações somente na conta de armazenamento de origem e pode resultar na substituição dos dados.
- Se você excluir um contêiner da conta de armazenamento chamando a operação excluir contêiner, esse contêiner não poderá ser restaurado com uma operação de restauração. Em vez de excluir um contêiner inteiro, exclua BLOBs individuais se você quiser restaurá-los mais tarde. Além disso, a Microsoft recomenda a habilitação da exclusão reversível para contêineres, além do backup operacional, para proteger contra a exclusão acidental de contêineres.
- Consulte a [matriz de suporte](blob-backup-support-matrix.md) para saber mais sobre os cenários, as limitações e a disponibilidade com suporte.

## <a name="create-a-backup-vault"></a>Criar um cofre de backup

Um [cofre de backup](backup-vault-overview.md) é uma entidade de gerenciamento que armazena pontos de recuperação criados ao longo do tempo e fornece uma interface para executar operações relacionadas a backup. Isso inclui fazer backups sob demanda, executar restaurações e criar políticas de backup. Embora o backup operacional de BLOBs seja um backup local e não "armazene" dados no cofre, o cofre é necessário para várias operações de gerenciamento.

>[!NOTE]
>O cofre de backup é um novo recurso que é usado para fazer backup de novas cargas de trabalho com suporte e é diferente do cofre de serviços de recuperação já existente.

Para obter instruções sobre como criar um cofre de backup, consulte a [documentação do cofre de backup](backup-vault-overview.md#create-a-backup-vault).

## <a name="grant-permissions-to-the-backup-vault-on-storage-accounts"></a>Conceder permissões para o cofre de backup em contas de armazenamento

O backup operacional também protege a conta de armazenamento (que contém os BLOBs a serem protegidos) de exclusões acidentais aplicando um bloqueio de exclusão de Propriedade do backup. Isso requer que o cofre de backup tenha determinadas permissões nas contas de armazenamento que precisam ser protegidas. Para sua conveniência de uso, essas permissões foram consolidadas na função colaborador de backup da conta de armazenamento. Siga as instruções abaixo para contas de armazenamento que precisam ser protegidas:

1. Na conta de armazenamento a ser protegida, navegue até a **guia controle de acesso (iam)** no painel de navegação à esquerda.
1. Selecione **Adicionar atribuições de função** para atribuir a função necessária.

    ![Adicionar atribuições de função](./media/blob-backup-configure-manage/add-role-assignments.png)

1. No painel Adicionar atribuição de função:

    1. Em **função**, escolha **colaborador de backup da conta de armazenamento**.
    1. Em **atribuir acesso a**, escolha **usuário, grupo ou entidade de serviço**.
    1. Digite o **nome do cofre de backup** que você deseja proteger os BLOBs nesta conta de armazenamento e selecione o mesmo nos resultados da pesquisa.
    1. Quando terminar, selecione **salvar**.

        ![Opções de atribuição de função](./media/blob-backup-configure-manage/role-assignment-options.png)

        >[!NOTE]
        >Aguarde até 10 minutos para que a atribuição de função entre em vigor.

## <a name="create-a-backup-policy"></a>Criar uma política de backup

Uma política de backup normalmente governa a retenção e a agenda de seus backups. Como o backup operacional para BLOBs é contínuo por natureza, você não precisa de uma agenda para executar backups. A política é basicamente necessária para especificar o período de retenção. Você pode usar e reutilizar a política de backup para configurar o backup de várias contas de armazenamento em um cofre.

Aqui estão as etapas para criar uma política de backup para o backup operacional de seus BLOBs:

1. No cofre de backup, navegue até **políticas de backup** e selecione **+ Adicionar** para começar a criar uma política de backup.

    ![Políticas de backup](./media/blob-backup-configure-manage/backup-policies.png)

1. Na guia **noções básicas** , forneça um nome para sua política de backup e selecione **BLOBs do Azure** como o tipo DataSource. Você também pode exibir os detalhes do cofre selecionado.

    ![Criar política de backup](./media/blob-backup-configure-manage/create-backup-policy.png)

    >[!NOTE]
    >Embora você veja a **redundância de armazenamento de backup** do cofre, a redundância não se aplica de fato ao backup operacional de BLOBs, já que o backup é local por natureza e nenhum dado é armazenado no cofre de backup. O cofre de backup aqui é a entidade de gerenciamento para ajudá-lo a gerenciar a proteção de blobs de blocos em suas contas de armazenamento.

1. A guia **política de backup** é onde você especifica os detalhes de retenção. Você verá que já existe uma regra de retenção chamada **padrão** com um período de retenção de 30 dias. Se você quiser editar a duração da retenção, use o ícone **Editar regra de retenção** para editar e especificar a duração para a qual você deseja que os dados sejam retidos. Você pode especificar a retenção de até 360 dias.

    ![Guia política de backup](./media/blob-backup-configure-manage/backup-policy-tab.png)

    >[!NOTE]
    >A restauração por longas duração pode levar a uma operação de restauração demorando mais para ser concluída. Além disso, o tempo necessário para restaurar um conjunto de dados é baseado no número de operações de gravação e exclusão feitas durante o período de restauração. Por exemplo, uma conta com 1 milhão objetos com 3.000 objetos adicionados por dia e 1.000 objetos excluídos por dia exigirá aproximadamente duas horas para restaurar para um ponto de 30 dias no passado. Um período de retenção e uma restauração mais de 90 dias no passado não seriam recomendados para uma conta com essa taxa de alteração.

1. No painel **revisar + criar** , verifique todos os detalhes da política e selecione **criar** uma vez concluído para concluir a criação da política. Uma notificação será confirmada quando a política de backup tiver sido criada e estiver pronta para ser usada.

    ![Examinar e criar política](./media/blob-backup-configure-manage/review-create.png)

## <a name="configure-backup"></a>Configurar backup

O backup de BLOBs é configurado no nível da conta de armazenamento. Portanto, todos os BLOBs na conta de armazenamento são protegidos com o backup operacional.

Para iniciar a configuração do backup:

1. Procure **centro de backup** na barra de pesquisa.
1. Navegue até **visão geral**  ->  **+ backup**.

    ![Visão geral do centro de backup](./media/blob-backup-configure-manage/backup-center-overview.png)

1. Na guia **Iniciar: configurar backup** , escolha **BLOBs do Azure (armazenamento do Azure)** como o tipo de fonte de armazenamento.

    ![Iniciar: guia Configurar backup](./media/blob-backup-configure-manage/initiate-configure-backup.png)

1. Na guia **noções básicas** , especifique **BLOBs do Azure (armazenamento do Azure)** como o tipo de **fonte** de armazenamento e selecione o cofre de backup ao qual você deseja associar suas contas. Você pode exibir detalhes do cofre selecionado no painel.

    ![Guia Básico](./media/blob-backup-configure-manage/basics-tab.png)

1. Em seguida, selecione a política de backup que você deseja usar para especificar a retenção. Você pode exibir os detalhes da política selecionada na parte inferior da tela. A coluna armazenamento de dados operacionais mostra a retenção definida na política. "Operacional" significa que os dados são mantidos localmente na própria conta de armazenamento de origem.

    ![Escolher política de backup](./media/blob-backup-configure-manage/choose-backup-policy.png)

    Você também pode criar uma nova política de backup. Para fazer isso, selecione **criar novo** e siga as etapas abaixo:

    1. Forneça um nome para a política que você deseja criar. Verifique se as outras caixas exibem o tipo de fonte de fontes e o nome do cofre corretos.
    1. Na guia **política de backup** , selecione o ícone Editar regra de retenção para editar e especifique a duração para a qual você deseja que os dados sejam retidos. Você pode especificar a retenção de até 360 dias. A restauração por longas duração pode levar a uma operação de restauração demorando mais para ser concluída.

        ![Criar nova política de backup](./media/blob-backup-configure-manage/new-backup-policy.png)

    1. Depois de concluído, selecione **examinar + criar** para criar a política de backup.

1. Em seguida, é necessário escolher as contas de armazenamento para as quais você deseja configurar a proteção de BLOBs. Você pode escolher várias contas de armazenamento ao mesmo tempo e escolher **selecionar**.

    No entanto, verifique se o cofre escolhido tem as permissões necessárias para configurar o backup nas contas de armazenamento conforme detalhado acima em [conceder permissões para o cofre de backup em contas de armazenamento](#grant-permissions-to-the-backup-vault-on-storage-accounts).

    ![Selecione os recursos para fazer backup](./media/blob-backup-configure-manage/select-resources.png)

    O backup verifica se o cofre tem permissões suficientes para permitir a configuração do backup nas contas de armazenamento selecionadas.

    ![O backup valida as permissões](./media/blob-backup-configure-manage/validate-permissions.png)

    Se a validação resultar em erros (como com uma das contas de armazenamento na captura de tela), vá para as contas de armazenamento selecionadas e atribua as funções apropriadas, conforme detalhado [aqui](#grant-permissions-to-the-backup-vault-on-storage-accounts)e selecione **revalidar**. A nova atribuição de função pode levar até 10 minutos para entrar em vigor.

1. Depois que a validação for realizada com sucesso para todas as contas de armazenamento selecionadas, continue a **examinar e configurar** o para configurar o backup. Você verá notificações informando sobre o status da configuração da proteção e sua conclusão.

## <a name="effects-on-backed-up-storage-accounts"></a>Efeitos de backup de contas de armazenamento

Após a configuração do backup, as alterações que ocorrem em blobs de blocos nas contas de armazenamento são rastreadas e os dados são retidos de acordo com a política de backup. Você observará as seguintes alterações nas contas de armazenamento para as quais o backup está configurado:

- Os seguintes recursos estão habilitados na conta de armazenamento. Eles podem ser exibidos na guia **proteção de dados** da conta de armazenamento.
  - Restauração pontual para contêineres: com retenção, conforme especificado na política de backup
  - Exclusão reversível de BLOBs: com retenção conforme especificado na política de backup + 5 dias
  - Controle de versão para BLOBs
  - Feed de alterações de BLOB

  Se a conta de armazenamento configurada para backup já tiver uma  **restauração pontual para contêineres** ou **exclusão reversível para BLOBs** habilitados (antes do backup ter sido configurado), o backup garantirá que a retenção seja pelo menos definida na política de backup. Portanto, para cada propriedade:

  - Se a retenção na política de backup for maior do que a retenção originalmente presente na conta de armazenamento: a retenção na conta de armazenamento é modificada de acordo com a política de backup
  - Se a retenção na política de backup for menor do que a retenção originalmente presente na conta de armazenamento: a retenção na conta de armazenamento permanece inalterada na duração definida originalmente.

  ![Guia proteção de dados](./media/blob-backup-configure-manage/data-protection.png)

- Um **bloqueio de exclusão** é aplicado pelo backup na conta de armazenamento protegido. O bloqueio destina-se a proteger contra casos de exclusão acidental da conta de armazenamento. Isso pode ser exibido em   >  **bloqueios** da conta de armazenamento.

    ![Excluir bloqueios](./media/blob-backup-configure-manage/delete-lock.png)

## <a name="manage-operational-backup"></a>Gerenciar backup operacional

Você pode usar o centro de backup como seu único painel de vidro para gerenciar todos os seus backups. Em relação ao backup operacional para BLOBs do Azure, você pode usar o centro de backup para executar o seguinte:

- Como vimos acima, você pode usá-lo para criar cofres e políticas de backup. Você também pode exibir todos os cofres e políticas nas assinaturas selecionadas.
- O centro de backup oferece uma maneira fácil de monitorar o estado de proteção de contas de armazenamento protegidas, bem como contas de armazenamento para as quais o backup não está atualmente configurado.
- Você pode configurar o backup para qualquer conta de armazenamento usando o botão **+ backup** .
- Você pode iniciar restaurações usando o botão **restaurar** e controlar restaurações usando trabalhos de **backup**. Para obter mais informações sobre como executar restaurações, consulte [restaurar BLOBs do Azure](blob-backup-support-matrix.md).
- Analise o uso de backup usando relatórios de backup.

    ![Centro de Backup](./media/blob-backup-configure-manage/backup-center.png)

Para obter mais informações, consulte [visão geral do centro de backup (versão prévia)](backup-center-overview.md).

## <a name="next-steps"></a>Próximas etapas

- [Restaurar BLOBs do Azure](blob-restore.md)
