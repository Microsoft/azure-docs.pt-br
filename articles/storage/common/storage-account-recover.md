---
title: Recuperar uma conta de armazenamento excluída
titleSuffix: Azure Storage
description: Saiba como recuperar uma conta de armazenamento excluída dentro do portal do Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: f57cd3361d7888d9d7f747955257d96282274fd6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97357338"
---
# <a name="recover-a-deleted-storage-account"></a>Recuperar uma conta de armazenamento excluída

Uma conta de armazenamento excluída pode ser recuperada em alguns casos de dentro do portal do Azure. Para recuperar uma conta de armazenamento, as seguintes condições devem ser verdadeiras:

- A conta de armazenamento foi excluída nos últimos 14 dias.
- A conta de armazenamento foi criada com o modelo de implantação Azure Resource Manager.
- Uma nova conta de armazenamento com o mesmo nome não foi criada desde que a conta original foi excluída.

Antes de tentar recuperar uma conta de armazenamento excluída, verifique se o grupo de recursos dessa conta existe. Se o grupo de recursos tiver sido excluído, você deverá recriá-lo. Não é possível recuperar um grupo de recursos. Para obter mais informações sobre o, consulte [gerenciar grupos de recursos](../../azure-resource-manager/management/manage-resource-groups-portal.md).

Se a conta de armazenamento excluída usou chaves gerenciadas pelo cliente com Azure Key Vault e o cofre de chaves também tiver sido excluído, você deverá restaurar o cofre de chaves antes de restaurar a conta de armazenamento. Para obter mais informações, consulte [visão geral da recuperação de Azure Key Vault](../../key-vault/general/key-vault-recovery.md).

> [!IMPORTANT]
> A recuperação de uma conta de armazenamento excluída não é garantida. A recuperação é uma tentativa de melhor esforço. A Microsoft recomenda o bloqueio de recursos para evitar a exclusão acidental da conta. Para obter mais informações sobre bloqueios de recursos, consulte [Bloquear recursos para evitar alterações](../../azure-resource-manager/management/lock-resources.md).
>
> Outra prática recomendada para evitar a exclusão acidental da conta é limitar o número de usuários que têm permissões para excluir uma conta por meio do controle de acesso baseado em função (RBAC do Azure). Para obter mais informações, consulte [práticas recomendadas para o RBAC do Azure](../../role-based-access-control/best-practices.md).

## <a name="recover-a-deleted-account-from-the-azure-portal"></a>Recuperar uma conta excluída da portal do Azure

Para recuperar uma conta de armazenamento excluída de outra conta de armazenamento, siga estas etapas:

1. Navegue até a página Visão geral de uma conta de armazenamento existente no portal do Azure.
1. Na seção **suporte + solução de problemas** , selecione **recuperar conta excluída**.
1. No menu suspenso, selecione a conta a ser recuperada, conforme mostrado na imagem a seguir. Se a conta de armazenamento que você deseja recuperar não estiver na lista suspensa, ela não poderá ser recuperada.

    :::image type="content" source="media/storage-account-recover/recover-account-portal.png" alt-text="Captura de tela mostrando como recuperar a conta de armazenamento no portal do Azure":::

1. Selecione o botão **recuperar** para restaurar a conta. O portal exibe uma notificação de que a recuperação está em andamento.

## <a name="recover-a-deleted-account-via-a-support-ticket"></a>Recuperar uma conta excluída por meio de um tíquete de suporte

1. Na portal do Azure, navegue até **ajuda + suporte**.
1. Selecione **Nova solicitação de suporte**.
1. Na guia **noções básicas** , no campo **tipo de problema** , selecione **técnico**.
1. No campo **assinatura** , selecione a assinatura que continha a conta de armazenamento excluída.
1. No campo **serviço** , selecione **Gerenciamento de conta de armazenamento**.
1. No campo **recurso** , selecione qualquer recurso de conta de armazenamento. A conta de armazenamento excluída não será exibida na lista.
1. Adicione um breve resumo do problema.
1. No campo **tipo de problema** , selecione **exclusão e recuperação**.
1. No campo **subtipo de problema** , selecione **recuperar conta de armazenamento excluída**. A imagem a seguir mostra um exemplo da guia **noções básicas** que está sendo preenchida:

    :::image type="content" source="media/storage-account-recover/recover-account-support-basics.png" alt-text="Captura de tela mostrando como recuperar uma conta de armazenamento por meio da guia noções básicas do tíquete de suporte":::

1. Em seguida, navegue até a guia **soluções** e selecione **recuperação de conta de armazenamento controlada pelo cliente**, conforme mostrado na imagem a seguir:

    :::image type="content" source="media/storage-account-recover/recover-account-support-solutions.png" alt-text="Captura de tela mostrando como recuperar uma conta de armazenamento por meio do tíquete de suporte – guia soluções":::

1. No menu suspenso, selecione a conta a ser recuperada, conforme mostrado na imagem a seguir. Se a conta de armazenamento que você deseja recuperar não estiver na lista suspensa, ela não poderá ser recuperada.

    :::image type="content" source="media/storage-account-recover/recover-account-support.png" alt-text="Captura de tela mostrando como recuperar uma conta de armazenamento por meio do tíquete de suporte":::

1. Selecione o botão **recuperar** para restaurar a conta. O portal exibe uma notificação de que a recuperação está em andamento.

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da conta de armazenamento](storage-account-overview.md)
- [Criar uma conta de armazenamento](storage-account-create.md)
- [Atualizar para uma conta de armazenamento de uso geral v2](storage-account-upgrade.md)
- [Mover uma conta de armazenamento do Azure para outra região](storage-account-move.md)