---
title: Gerenciar réplicas de leitura-portal do Azure-banco de dados do Azure para MySQL-servidor flexível
description: Saiba como configurar e gerenciar réplicas de leitura no banco de dados do Azure para MySQL servidor flexível usando o portal do Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/26/2020
ms.openlocfilehash: fd303804706f9ae210e6714cc8698c94c39ebef6
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105106846"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-flexible-server-using-the-azure-portal"></a>Como criar e gerenciar réplicas de leitura no banco de dados do Azure para MySQL servidor flexível usando o portal do Azure

> [!IMPORTANT]
> Ler réplicas no banco de dados do Azure para MySQL-o servidor flexível está em versão prévia.

Neste artigo, você aprenderá a criar e gerenciar réplicas de leitura no banco de dados do Azure para o servidor flexível do MySQL usando o portal do Azure.

> [!Note]
> Não há suporte para a réplica no servidor habilitado para alta disponibilidade. 

## <a name="prerequisites"></a>Pré-requisitos

- Um [servidor de banco de dados do Azure para MySQL servidor flexível](quickstart-create-server-portal.md) que será usado como o servidor de origem.

## <a name="create-a-read-replica"></a>Criar uma réplica de leitura

> [!IMPORTANT]
> Quando você cria uma réplica para uma fonte que não tem réplicas existentes, a origem será reiniciada primeiro para se preparar para a replicação. Leve isso em consideração e realize essas operações durante um período de pouca atividade.

Um servidor de réplica de leitura pode ser criado usando as seguintes etapas:

1. Faça logon no [Portal do Azure](https://portal.azure.com/).

2. Selecione o servidor existente do banco de dados do Azure para MySQL flexível que você deseja usar como origem. Essa ação abre a página **Visão geral** do runbook.

3. Selecione **Replicação** no menu, em **CONFIGURAÇÕES**.

4. Selecione **para adicionar réplica**.

   :::image type="content" source="./media/how-to-read-replica-portal/add-replica.png" alt-text="Banco de Dados do Azure para MySQL - Replicação":::

5. Insira um nome para o servidor de réplica.

    :::image type="content" source="./media/how-to-read-replica-portal/replica-name.png" alt-text="Banco de Dados do Azure para MySQL – Nome da réplica":::

6. Selecione **OK** para confirmar a criação da réplica.

> [!NOTE]
> As réplicas de leitura são criadas com a mesma configuração de servidor que a origem. A configuração do servidor de réplica pode ser alterada depois de criada. O servidor de réplica é sempre criado no mesmo grupo de recursos, no mesmo local e na mesma assinatura que o servidor de origem. Se você quiser criar um servidor de réplica para um grupo de recursos diferente ou uma assinatura diferente, poderá [mover o servidor de réplica](../../azure-resource-manager/management/move-resource-group-and-subscription.md) após a criação. É recomendável que a configuração do servidor de réplica seja mantida em valores iguais ou maiores do que a origem para garantir que a réplica seja capaz de acompanhar a origem.

Depois que o servidor de réplica tiver sido criado, ele poderá ser visualizado no blade **Replication**.

   [:::image type="content" source="./media/how-to-read-replica-portal/list-replica.png" alt-text="Banco de Dados do Azure para MySQL - Listar Réplicas":::](./media/how-to-read-replica-portal/list-replica.png#lightbox)

## <a name="stop-replication-to-a-replica-server"></a>Parar a replicação para um servidor de réplica

> [!IMPORTANT]
> Parar a replicação para um servidor é irreversível. Depois que a replicação for interrompida entre uma origem e uma réplica, ela não poderá ser desfeita. O servidor de réplica então se torna um servidor autônomo e agora suporta tanto leitura quanto gravação. Este servidor não pode ser transformado em uma réplica novamente.

Para interromper a replicação entre um servidor de origem e de réplica do portal do Azure, use as seguintes etapas:

1. Na portal do Azure, selecione o servidor de origem banco de dados do Azure para MySQL flexível. 

2. Selecione **Replicação** no menu, em **CONFIGURAÇÕES**.

3. Selecione o servidor de réplica para o qual você deseja interromper a replicação.

   [:::image type="content" source="./media/how-to-read-replica-portal/stop-replication-select.png" alt-text="Banco de Dados do Azure para MySQL - Parar servidor de seleção de replicação":::](./media/how-to-read-replica-portal/stop-replication-select.png#lightbox)

4. Selecione **Parar replicação**.

   [:::image type="content" source="./media/how-to-read-replica-portal/stop-replication.png" alt-text="Banco de Dados do Azure para MySQL - Parar replicação":::](./media/how-to-read-replica-portal/stop-replication.png#lightbox)

5. Confirme que você deseja interromper a replicação clicando em **OK**.

   [:::image type="content" source="./media/how-to-read-replica-portal/stop-replication-confirm.png" alt-text="Banco de Dados do Azure para MySQL - Confirme a replicação de parada":::](./media/how-to-read-replica-portal/stop-replication-confirm.png#lightbox)

## <a name="delete-a-replica-server"></a>Excluir um servidor de réplica

Para excluir um servidor de réplica de leitura do portal do Azure, use as seguintes etapas:

1. Na portal do Azure, selecione o servidor de origem banco de dados do Azure para MySQL flexível.

2. Selecione **Replicação** no menu, em **CONFIGURAÇÕES**.

3. Selecione o servidor de réplica que você deseja excluir.

   [:::image type="content" source="./media/how-to-read-replica-portal/delete-replica-select.png" alt-text="Banco de Dados do Azure para MySQL - Excluir Servidor de Seleção de Réplica":::](./media/how-to-read-replica-portal/delete-replica-select.png#lightbox)

4. Selecione **Excluir réplica**

   :::image type="content" source="./media/how-to-read-replica-portal/delete-replica.png" alt-text="Banco de dados do Azure para MySQL - Excluir réplica":::

5. Digite o nome da réplica e clique em **Excluir** para confirmar a exclusão da réplica.  

   :::image type="content" source="./media/how-to-read-replica-portal/delete-replica-confirm.png" alt-text="Confirmar do banco de dados do Azure para MySQL - excluir réplica":::

## <a name="delete-a-source-server"></a>Excluir um servidor de origem

> [!IMPORTANT]
> A exclusão de um servidor de origem interrompe a replicação para todos os servidores de origem e exclui o próprio servidor mestre. Os servidores de réplica tornam-se servidores independentes que agora suportam leitura e gravação.

Para excluir um servidor de origem do portal do Azure, use as seguintes etapas:

1. Na portal do Azure, selecione o servidor de origem banco de dados do Azure para MySQL flexível.

2. Na **Visão geral**, selecione **Excluir**.

   [:::image type="content" source="./media/how-to-read-replica-portal/delete-master-overview.png" alt-text="Banco de dados do Azure para MySQL – excluir origem":::](./media/how-to-read-replica-portal/delete-master-overview.png#lightbox)

3. Digite o nome do servidor de origem e clique em **excluir** para confirmar a exclusão do servidor de origem.  

   :::image type="content" source="./media/how-to-read-replica-portal/delete-master-confirm.png" alt-text="Banco de dados do Azure para MySQL – excluir origem confirmar":::

## <a name="monitor-replication"></a>Monitorar a replicação

1. Na [portal do Azure](https://portal.azure.com/), selecione a réplica do banco de dados do Azure para MySQL servidor flexível que você deseja monitorar.

2. Na seção **Monitoramento** da barra lateral, selecione **Métricas**:

3. Selecione o **atraso de replicação em segundos** na lista suspensa de métricas disponíveis.

   [:::image type="content" source="./media/how-to-read-replica-portal/monitor-select-replication-lag.png" alt-text="Selecione o retardo de replicação":::](./media/how-to-read-replica-portal/monitor-select-replication-lag.png#lightbox)

4. Selecione o intervalo de tempo que você deseja visualizar. A imagem abaixo seleciona um intervalo de tempo de 30 minutos.

   [:::image type="content" source="./media/how-to-read-replica-portal/monitor-replication-lag-time-range.png" alt-text="Selecionar intervalo de tempo":::](./media/how-to-read-replica-portal/monitor-replication-lag-time-range.png#lightbox)

5. Veja o atraso de replicação para o intervalo de tempo selecionado. A imagem a seguir exibe os últimos 30 minutos.

   [:::image type="content" source="./media/how-to-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png" alt-text="Selecionar intervalo de tempo 30 minutos":::](./media/how-to-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [ler réplicas](concepts-read-replicas.md)