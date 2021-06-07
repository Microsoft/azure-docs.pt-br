---
title: Visão geral dos cofres de backup
description: Uma visão geral dos cofres de backup.
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: c189997ecc4814917182246b35003649d317ac77
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92091280"
---
# <a name="backup-vaults-overview"></a>Visão geral dos cofres de backup

Este artigo descreve os recursos de um cofre de backup. Um cofre de backup é uma entidade de armazenamento no Azure que hospeda dados de backup para determinadas cargas de trabalho mais recentes às quais o backup do Azure dá suporte. Você pode usar os cofres de backup para manter os dados de backup de vários serviços do Azure, como os servidores do banco de dados do Azure para PostgreSQL e as cargas de trabalho mais recentes com suporte no backup do Azure. Os cofres de backup facilitam a organização dos dados de backup, minimizando a sobrecarga de gerenciamento. Os cofres de backup são baseados no modelo de Azure Resource Manager do Azure, que fornece recursos como:

- **Recursos aprimorados para ajudar a proteger os dados de backup**: com cofres de backup, o backup do Azure fornece recursos de segurança para proteger os backups na nuvem. Esses recursos de segurança asseguram que você possa proteger seus backups e recuperar dados com segurança, mesmo que os servidores de produção e de backup estejam comprometidos. [Saiba mais](backup-azure-security-feature.md)

- **Controle de acesso baseado em função do Azure (RBAC do Azure)**: o RBAC do Azure fornece controle de gerenciamento de acesso refinado no Azure. [O Azure fornece várias funções internas](../role-based-access-control/built-in-roles.md), e o Backup do Azure tem três [funções internas para gerenciar pontos de recuperação](backup-rbac-rs-vault.md). Os cofres de backup são compatíveis com o RBAC do Azure, que restringe o acesso de backup e restauração ao conjunto definido de funções de usuário. [Saiba mais](backup-rbac-rs-vault.md)

## <a name="storage-settings-in-the-backup-vault"></a>Configurações de armazenamento no cofre de backup

Um cofre de backup é uma entidade que armazena os backups e os pontos de recuperação criados ao longo do tempo. O cofre de backup também contém as políticas de backup associadas às máquinas virtuais protegidas.

- O backup do Azure manipula automaticamente o armazenamento para o cofre. Escolha a redundância de armazenamento que corresponde às suas necessidades de negócios ao criar o cofre de backup.

- Para saber mais sobre a redundância de armazenamento, consulte estes artigos sobre redundância [geográfica](../storage/common/storage-redundancy.md#geo-redundant-storage) e [local](../storage/common/storage-redundancy.md#locally-redundant-storage) .

## <a name="encryption-settings-in-the-backup-vault"></a>Configurações de criptografia no cofre de backup

Esta seção discute as opções disponíveis para criptografar os dados de backup armazenados no cofre de backup.

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Criptografia de dados de backup usando chaves gerenciadas pela plataforma

Por padrão, todos os seus dados são criptografados usando chaves gerenciadas por plataforma. Você não precisa executar nenhuma ação explícita do seu fim para habilitar essa criptografia. Ele se aplica a todas as cargas de trabalho cujo backup está sendo feito em seu cofre de backup.

## <a name="create-a-backup-vault"></a>Criar um cofre de backup

Um cofre de backup é uma entidade de gerenciamento que armazena pontos de recuperação criados ao longo do tempo e fornece uma interface para executar operações relacionadas a backup. Isso inclui fazer backups sob demanda, executar restaurações e criar políticas de backup.

Para criar um cofre de backup, siga estas etapas.

### <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em <https://portal.azure.com>.

### <a name="create-backup-vault"></a>Criar cofre de backup

1. Digite **cofres de backup** na caixa de pesquisa.
1. Em **Serviços**, selecione **cofres de backup**.
1. Na página **cofres de backup** , selecione **Adicionar**.
1. Na **guia noções básicas**, em **detalhes do projeto**, verifique se a assinatura correta está selecionada e escolha **criar novo** grupo de recursos. Digite *myResourceGroup* para o nome.

  ![Criar novo grupo de recursos](./media/backup-vault-overview/new-resource-group.png)

1. Em **detalhes da instância**, *digite myvault* para o **nome do cofre de backup** e escolha sua região de preferência, neste caso, leste dos *EUA* para sua **região**.
1. Agora escolha sua **redundância de armazenamento**. A redundância de armazenamento não pode ser alterada após a proteção de itens ao cofre.
1. Recomendamos que, se você estiver usando o Azure como um ponto de extremidade de armazenamento de backup primário, continue a usar a configuração padrão **com redundância geográfica** .
1. Se você não usar o Azure como um ponto de extremidade de armazenamento de backup principal, escolha **Localmente redundante**, que reduz os custos de armazenamento do Azure.
1. Saiba mais sobre a redundância [geográfica](../storage/common/storage-redundancy.md#geo-redundant-storage) e [local](../storage/common/storage-redundancy.md#locally-redundant-storage) .

  ![Escolher redundância de armazenamento](./media/backup-vault-overview/storage-redundancy.png)

1. Selecione o botão revisar + criar na parte inferior da página.

    ![Selecione revisão + criar](./media/backup-vault-overview/review-and-create.png)

## <a name="delete-a-backup-vault"></a>Excluir um cofre de Backup

Esta seção descreve como excluir um cofre de backup. Ele contém instruções para remover dependências e, em seguida, excluir um cofre.

### <a name="before-you-start"></a>Antes de começar

Você não pode excluir um cofre de backup com nenhuma das seguintes dependências:

- Não é possível excluir um cofre que contenha fontes de dados protegidas (por exemplo, o Azure Database para servidores PostgreSQL).
- Você não pode excluir um cofre que contém dados de backup.

Se você tentar excluir o cofre sem remover as dependências, encontrará as seguintes mensagens de erro:

>Não é possível excluir o cofre de backup porque há instâncias de backup ou políticas de backup existentes no cofre. Exclua todas as instâncias de backup e políticas de backup presentes no cofre e tente excluir o cofre.

### <a name="proper-way-to-delete-a-vault"></a>Maneira apropriada de excluir um cofre

>[!WARNING]
A operação a seguir é destrutiva e não pode ser desfeita. Todos os dados de backup e itens de backup associados ao servidor protegido serão excluídos permanentemente. Continue com cuidado.

Para excluir um cofre corretamente, você deve seguir as etapas nesta ordem:

- Você deve verificar para verificar se há itens protegidos:
  - Vá para **instâncias de backup** na barra de navegação à esquerda. Todos os itens listados aqui devem ser excluídos primeiro.

Depois de concluir essas etapas, você poderá continuar a excluir o cofre.

### <a name="delete-the-backup-vault"></a>Excluir o cofre de backup

Quando não houver mais itens no cofre, selecione **excluir** no painel do cofre. Você verá um texto de confirmação perguntando se deseja excluir o cofre.

![Excluir cofre](./media/backup-vault-overview/delete-vault.png)

1. Selecione **Sim** para verificar se deseja excluir o cofre. O cofre é excluído. O portal retorna para o **novo** menu de serviço.

## <a name="monitor-and-manage-the-backup-vault"></a>Monitorar e gerenciar o cofre de backup

Esta seção explica como usar o painel **visão geral** do cofre de backup para monitorar e gerenciar seus cofres de backup. O painel Visão geral contém dois blocos: **trabalhos** e **instâncias**.

![Visão geral do dashboard](./media/backup-vault-overview/overview-dashboard.png)

### <a name="manage-backup-instances"></a>Gerenciar instâncias de backup

No bloco **trabalhos** , você obtém uma exibição resumida de todos os trabalhos relacionados a backup e restauração em seu cofre de backup. A seleção de qualquer um dos números neste bloco permite que você exiba mais informações sobre os trabalhos para um tipo de fonte de dados específico, tipo de operação e status.

![Instâncias de backup](./media/backup-vault-overview/backup-instances.png)

### <a name="manage-backup-jobs"></a>Gerenciar trabalhos de Backup

No bloco **instâncias de backup** , você obtém uma exibição resumida de todas as instâncias de backup em seu cofre de backup. A seleção de qualquer um dos números neste bloco permite que você exiba mais informações sobre instâncias de backup para um tipo de fonte de dados e status de proteção específicos.

![Trabalhos de backup](./media/backup-vault-overview/backup-jobs.png)

## <a name="next-steps"></a>Próximas etapas

- [Configurar backup em bancos de dados PostgreSQL do Azure](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases)
