---
title: Proteção acidental de exclusão para compartilhamentos de arquivos do Azure
description: Saiba como a exclusão reversível pode proteger seus compartilhamentos de arquivos do Azure contra exclusão acidental.
ms.topic: conceptual
ms.date: 02/02/2020
ms.custom: references_regions
ms.openlocfilehash: 52a116320e07c25d4ee7f00b8063ca15faeb8560
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89179905"
---
# <a name="accidental-delete-protection-for-azure-file-shares-using-azure-backup"></a>Proteção acidental de exclusão para compartilhamentos de arquivos do Azure usando o backup do Azure

Para fornecer proteção contra ataques cibernéticos ou exclusão acidental, a [exclusão reversível](../storage/files/storage-files-prevent-file-share-deletion.md) é habilitada para todos os compartilhamentos de arquivos em uma conta de armazenamento quando você configura o backup para qualquer compartilhamento de arquivos na respectiva conta de armazenamento. Com a exclusão reversível, mesmo que um ator mal-intencionado exclua o compartilhamento de arquivos, o conteúdo e os pontos de recuperação (instantâneos) do compartilhamento de arquivos são mantidos por um mínimo de 14 dias adicionais, permitindo a recuperação de compartilhamentos de arquivos sem perda de dados.  A exclusão reversível tem suporte para contas de armazenamento Standard e Premium e a configuração é habilitada pelo backup do Azure para todas as contas de armazenamento que hospedam compartilhamentos de arquivos de backup.

O fluxograma a seguir mostra as diferentes etapas e os Estados de um item de backup quando a exclusão reversível está habilitada para compartilhamentos de arquivos em uma conta de armazenamento:

 ![Gráfico de fluxo de exclusão reversível](./media/soft-delete-afs/soft-delete-flow-chart.png)

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="when-will-soft-delete-be-enabled-for-file-shares-in-my-storage-account"></a>Quando a exclusão reversível será habilitada para compartilhamentos de arquivos na minha conta de armazenamento?

Quando você configura o backup pela primeira vez para qualquer compartilhamento de arquivos em uma conta de armazenamento, o serviço de backup do Azure permite a exclusão reversível para todos os compartilhamentos de arquivos na respectiva conta de armazenamento.

### <a name="can-i-configure-the-number-of-days-for-which-my-snapshots-and-restore-points-will-be-retained-in-soft-deleted-state-after-i-delete-the-file-share"></a>Posso configurar o número de dias pelos quais meus instantâneos e pontos de restauração serão retidos no estado de exclusão reversível depois de excluir o compartilhamento de arquivos?

Sim, você pode definir o período de retenção de acordo com seus requisitos. [Este documento](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal) explica as etapas para configurar o período de retenção. Para contas de armazenamento com compartilhamentos de arquivos com backup, a configuração de retenção mínima deve ser de 14 dias.

### <a name="does-azure-backup-reset-my-retention-setting-because-i-configured-it-to-less-than-14-days"></a>O backup do Azure redefine minha configuração de retenção porque a configurei para menos de 14 dias?

Do ponto de vista da segurança, recomendamos ter retenção mínima de 14 dias para contas de armazenamento com compartilhamentos de arquivos submetidos a backup. Assim, em cada trabalho de backup, será executado se o backup do Azure identificar a configuração para menos de 14 dias, ela a redefinirá para 14 dias.

### <a name="what-is-the-cost-incurred-during-the-retention-period"></a>Qual é o custo incorrido durante o período de retenção?

Durante o período de exclusão reversível, o custo de instância protegida e o armazenamento de instantâneos permanecerão como estão.  Além disso, você será cobrado pela capacidade usada na taxa regular de compartilhamentos de arquivos padrão e na taxa de armazenamento de instantâneos para compartilhamentos de arquivos premium.

### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-deleted-state"></a>Posso executar uma operação de restauração quando meus dados estiverem no estado de exclusão reversível?

Primeiro, você precisa restaurar o compartilhamento de arquivos com exclusão reversível para executar operações de restauração. A operação de desfazer exclusão trará o compartilhamento de arquivos para o estado de backup, no qual você pode restaurar para qualquer ponto no tempo. Para saber como restaurar o compartilhamento de arquivos, visite [este link](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share) ou consulte o [script para restaurar o compartilhamento de arquivos](./scripts/backup-powershell-script-undelete-file-share.md).

### <a name="how-can-i-purge-the-data-of-a-file-share-in-a-storage-account-that-has-at-least-one-protected-file-share"></a>Como posso limpar os dados de um compartilhamento de arquivos em uma conta de armazenamento que tenha pelo menos um compartilhamento de arquivos protegido?

Se você tiver pelo menos um compartilhamento de arquivos protegido em uma conta de armazenamento, isso significará que a exclusão reversível está habilitada para todos os compartilhamentos de arquivos nessa conta e seus dados serão mantidos por 14 dias após a operação de exclusão. Mas se você quiser limpar os dados imediatamente e não quiser que eles sejam retidos, siga estas etapas:

1. Se você já excluiu o compartilhamento de arquivos enquanto a exclusão reversível estava habilitada, primeiro exclua o compartilhamento de arquivos do [portal de arquivos](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share) ou usando o script de compartilhamento de [arquivos para restaurar](./scripts/backup-powershell-script-undelete-file-share.md).
2. Desabilite a exclusão reversível para compartilhamentos de arquivos em sua conta de armazenamento seguindo as etapas mencionadas neste [documento](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#disable-soft-delete).
3. Agora, exclua o compartilhamento de arquivos cujo conteúdo você deseja limpar imediatamente.

>[!NOTE]
>Você deve executar a etapa 2 antes que o próximo trabalho de backup agendado seja executado em relação ao compartilhamento de arquivos protegido em sua conta de armazenamento. Como sempre que o trabalho de backup é executado, ele habilita novamente a exclusão reversível para todos os compartilhamentos de arquivos na conta de armazenamento.

>[!WARNING]
>Depois de desabilitar a exclusão reversível na etapa 2, qualquer operação de exclusão executada em relação aos compartilhamentos de arquivos é uma operação de exclusão permanente. Portanto, se você excluir acidentalmente o compartilhamento de arquivos de backup depois de desabilitar a exclusão reversível, perderá todos os instantâneos e não poderá recuperar seus dados.

### <a name="in-the-context-of-a-file-shares-soft-delete-setting-what-changes-does-azure-backup-do-when-i-unregister-a-storage-account"></a>No contexto da configuração de exclusão reversível de um compartilhamento de arquivos, quais alterações o backup do Azure faz quando eu desregistro uma conta de armazenamento?

No momento do cancelamento do registro, o backup do Azure verifica a configuração do período de retenção para compartilhamentos de arquivos e, se for maior que 14 dias ou menos de 14 dias, ele deixará a retenção como está. No entanto, se a retenção for de 14 dias, consideramos que ela está habilitada pelo backup do Azure e, portanto, desabilitamos a exclusão reversível durante o processo de cancelamento de registro. Se você quiser cancelar o registro da conta de armazenamento enquanto mantém a configuração de retenção como está, habilite-a novamente no painel da conta de armazenamento depois de concluir o cancelamento do registro. Você pode consultar [esse link](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share) para obter as etapas de configuração.

## <a name="next-steps"></a>Próximas etapas

Saiba como [Fazer backup de Compartilhamentos de Arquivos do Azure do portal do Azure](backup-afs.md)
