---
title: Configure o backup contínuo e a restauração pontual usando portal do Azure no Azure Cosmos DB.
description: Saiba como identificar o ponto de restauração e configurar o backup contínuo usando o portal do Azure. Ele mostra como restaurar uma conta dinâmica e excluída.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: ee6eedbc078e1b9c07ed00922ce1c37b38410128
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100381861"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-portal"></a>Configurar e gerenciar o backup contínuo e a restauração pontual (versão prévia)-usando portal do Azure
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> O recurso de restauração pontual (modo de backup contínuo) para Azure Cosmos DB está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O recurso de restauração pontual do Azure Cosmos DB (versão prévia) ajuda você a se recuperar de uma alteração acidental dentro de um contêiner para restaurar uma conta excluída, um banco de dados ou um contêiner ou para restaurar em qualquer região (onde os backups existiam). O modo de backup contínuo permite que você faça a restauração para qualquer ponto de tempo nos últimos 30 dias.

Este artigo descreve como identificar o ponto de restauração e configurar o backup contínuo usando o portal do Azure.

## <a name="provision-an-account-with-continuous-backup"></a><a id="provision"></a>Provisionar uma conta com backup contínuo

Ao criar uma nova conta de Azure Cosmos DB, para a opção de **política de backup** , escolha modo **contínuo** para habilitar a funcionalidade de restauração pontual para a nova conta. Depois que esse recurso estiver habilitado para a conta, todos os bancos de dados e contêineres estarão disponíveis para backup contínuo. Com a restauração pontual, os dados sempre são restaurados para uma nova conta, no momento, você não pode restaurar para uma conta existente.

:::image type="content" source="./media/continuous-backup-restore-portal/configure-continuous-backup-portal.png" alt-text="Provisione uma conta de Azure Cosmos DB com configuração de backup contínua." border="true":::

## <a name="restore-a-live-account-from-accidental-modification"></a><a id="restore-live-account"></a>Restaurar uma conta dinâmica de modificação acidental

Você pode usar portal do Azure para restaurar uma conta dinâmica ou os contêineres e bancos de dados selecionados sob ela. Use as seguintes etapas para restaurar seus dados:

1. Entre no [Portal do Azure](https://portal.azure.com/)
1. Navegue até sua conta do Azure Cosmos DB e abra o painel **restauração pontual** .

   > [!NOTE]
   > O painel restaurar no portal do Azure só será populado se você tiver a `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` permissão. Para saber mais sobre como definir essa permissão, consulte o artigo [permissões de backup e restauração](continuous-backup-restore-permissions.md) .

1. Preencha os seguintes detalhes para restaurar:

   * **Ponto de restauração (UTC)** – um carimbo de data/hora nos últimos 30 dias. A conta deve existir nesse carimbo de data/hora. Você pode especificar o ponto de restauração em UTC. Pode ser tão próximo ao segundo quando você deseja restaurá-lo. Selecione o link **clique aqui** para obter ajuda para [identificar o ponto de restauração](#event-feed).

   * **Local** – a região de destino em que a conta é restaurada. A conta deve existir nessa região no carimbo de data/hora fornecido (por exemplo, Oeste dos EUA ou leste dos EUA). Uma conta pode ser restaurada somente para as regiões nas quais a conta de origem existia.

   * **Restaurar recurso** – você pode escolher a **conta inteira** ou um **banco de dados/contêiner selecionado** para restaurar. Os bancos de dados e contêineres devem existir no carimbo de data/hora fornecido. Com base no ponto de restauração e no local selecionados, os recursos de restauração são populados, o que permite ao usuário selecionar bancos de dados ou contêineres específicos que precisam ser restaurados.

   * **Grupo de recursos** -grupo de recursos sob o qual a conta de destino será criada e restaurada. O grupo de recursos já deve existir.

   * **Restaurar conta de destino** – o nome da conta de destino. O nome da conta de destino precisa seguir as mesmas diretrizes que quando você está criando uma nova conta. Essa conta será criada pelo processo de restauração na mesma região em que a conta de origem existe.
 
   :::image type="content" source="./media/continuous-backup-restore-portal/restore-live-account-portal.png" alt-text="Restaure uma conta dinâmica de portal do Azure de modificação acidental." border="true":::

1. Depois de selecionar os parâmetros acima, selecione o botão **Enviar** para iniciar uma restauração. O custo de restauração é um encargo único, que é baseado na quantidade de dados e cobranças para o armazenamento em determinada região. Para saber mais, confira a seção de [preços](continuous-backup-restore-introduction.md#continuous-backup-pricing) .

## <a name="use-event-feed-to-identify-the-restore-time"></a><a id="event-feed"></a>Usar o feed de eventos para identificar o tempo de restauração

Ao preencher a hora do ponto de restauração no portal do Azure, se precisar de ajuda com a identificação do ponto de restauração, selecione o link **clique aqui** , ele levará você para a folha feed de eventos. O feed de eventos fornece uma lista completa de fidelidade de criar, substituir, excluir eventos em bancos de dados e contêineres da conta de origem. 

Por exemplo, se você deseja restaurar para o ponto antes de um determinado contêiner ser excluído ou atualizado, verifique este feed de eventos. Os eventos são exibidos em ordem cronológica decrescente de tempo quando ocorreram, com eventos recentes na parte superior. Você pode navegar pelos resultados e selecionar a hora antes ou depois do evento para restringir ainda mais o tempo.

:::image type="content" source="./media/continuous-backup-restore-portal/event-feed-portal.png" alt-text="Use o feed de eventos para identificar o tempo do ponto de restauração." border="true":::

> [!NOTE]
> O feed de eventos não exibe as alterações para os recursos do item. Você sempre pode especificar manualmente qualquer carimbo de data/hora nos últimos 30 dias (desde que a conta exista naquele momento) para restauração.

## <a name="restore-a-deleted-account"></a><a id="restore-deleted-account"></a>Restaurar uma conta excluída

Você pode usar portal do Azure para restaurar completamente uma conta excluída dentro de 30 dias após sua exclusão. Use as seguintes etapas para restaurar uma conta excluída:

1. Entre no [Portal do Azure](https://portal.azure.com/)
1. Pesquise *Azure Cosmos DB* recursos na barra de pesquisa global. Ele lista todas as suas contas existentes.
1. Em seguida, selecione o botão **restaurar** . O painel restaurar exibe uma lista de contas excluídas que podem ser restauradas dentro do período de retenção, que é de 30 dias a partir do tempo de exclusão.
1. Escolha a conta que você deseja restaurar.

   :::image type="content" source="./media/continuous-backup-restore-portal/restore-deleted-account-portal.png" alt-text="Restaurar uma conta excluída do portal do Azure." border="true":::

   > [!NOTE]
   > Observação: o painel de restauração no portal do Azure só será populado se você tiver a `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` permissão. Para saber mais sobre como definir essa permissão, consulte o artigo [permissões de backup e restauração](continuous-backup-restore-permissions.md) .

1. Selecione uma conta para restaurar e insira os seguintes detalhes para restaurar uma conta excluída:

   * **Ponto de restauração (UTC)** – um carimbo de data/hora nos últimos 30 dias. A conta deve ter existido nesse carimbo de data/hora. Especifique o ponto de restauração em UTC. Pode ser tão próximo ao segundo quando você deseja restaurá-lo.

   * **Local** – a região de destino em que a conta precisa ser restaurada. A conta de origem deve existir nessa região no carimbo de data/hora fornecido. Exemplo oeste dos EUA ou leste dos EUA.  

   * **Grupo de recursos** -grupo de recursos sob o qual a conta de destino será criada e restaurada. O grupo de recursos já deve existir.

   * **Restaurar conta de destino** – o nome da conta de destino precisa seguir as mesmas diretrizes que quando você está criando uma nova conta. Essa conta será criada pelo processo de restauração na mesma região em que a conta de origem existe.

## <a name="track-the-status-of-restore-operation"></a><a id="track-restore-status"></a>Acompanhar o status da operação de restauração

Depois de iniciar uma operação de restauração, selecione o ícone de sino de **notificação** no canto superior direito do Portal. Ele fornece um link que exibe o status da conta que está sendo restaurada. Enquanto a restauração estiver em andamento, o status da conta será *criado*, após a conclusão da operação de restauração, o status da conta será alterado para *online*.

:::image type="content" source="./media/continuous-backup-restore-portal/track-restore-operation-status.png" alt-text="O status das alterações de conta restauradas de criação para online quando a operação é concluída." border="true":::

## <a name="next-steps"></a>Próximas etapas

* Configure e gerencie o backup contínuo usando [CLI do Azure](continuous-backup-restore-command-line.md), [PowerShell](continuous-backup-restore-powershell.md)ou [Azure Resource Manager](continuous-backup-restore-template.md).
* [Modelo de recurso do modo de backup contínuo](continuous-backup-restore-resource-model.md)
* [Gerencie as permissões](continuous-backup-restore-permissions.md) necessárias para restaurar dados com o modo de backup contínuo.
