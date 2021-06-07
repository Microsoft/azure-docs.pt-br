---
title: Gerenciar réplicas de leitura – Portal do Azure – Banco de Dados do Azure para MySQL
description: Saiba como configurar e gerenciar réplicas de leitura no Banco de Dados do Azure para MySQL usando o portal do Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 6/10/2020
ms.openlocfilehash: 26b503e7d55ed3d2f9bd06837551655e7af05a17
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94541933"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-portal"></a>Como criar e gerenciar réplicas de leitura no Banco de Dados do Azure para MySQL usando o portal do Azure

Neste artigo, você aprenderá a criar e gerenciar réplicas de leitura no serviço Banco de Dados do Azure para MySQL usando o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Um [banco de dados do Azure para servidor MySQL](quickstart-create-mysql-server-database-using-azure-portal.md) que será usado como o servidor de origem.

> [!IMPORTANT]
> O recurso de réplica de leitura está disponível apenas para bancos de dados do Azure para servidores MySQL nas camadas de preços de uso geral ou de otimização de memória. Verifique se o servidor de origem está em um desses tipos de preço.

## <a name="create-a-read-replica"></a>Criar uma réplica de leitura

> [!IMPORTANT]
> Quando você cria uma réplica para uma fonte que não tem réplicas existentes, a origem será reiniciada primeiro para se preparar para a replicação. Leve isso em consideração e realize essas operações durante um período de pouca atividade.

Um servidor de réplica de leitura pode ser criado usando as seguintes etapas:

1. Faça logon no [Portal do Azure](https://portal.azure.com/).

2. Selecione o banco de dados existente do Azure para servidor MySQL que você deseja usar como um mestre. Essa ação abre a página **Visão geral** do runbook.

3. Selecione **Replicação** no menu, em **CONFIGURAÇÕES**.

4. Selecione **para adicionar réplica**.

   :::image type="content" source="./media/howto-read-replica-portal/add-replica.png" alt-text="Banco de Dados do Azure para MySQL - Replicação":::

5. Insira um nome para o servidor de réplica.

    :::image type="content" source="./media/howto-read-replica-portal/replica-name.png" alt-text="Banco de Dados do Azure para MySQL – Nome da réplica":::

6. Selecione o local para o servidor de réplica. O local padrão é o mesmo que o do servidor de origem.

    :::image type="content" source="./media/howto-read-replica-portal/replica-location.png" alt-text="Banco de Dados do Azure para MySQL – Local da réplica":::

   > [!NOTE]
   > Para saber mais sobre em quais regiões você pode criar uma réplica, visite o artigo [conceitos de réplica de leitura](concepts-read-replicas.md). 

7. Selecione **OK** para confirmar a criação da réplica.

> [!NOTE]
> Réplicas de leitura são criadas com a mesma configuração de servidor que o mestre. A configuração do servidor de réplica pode ser alterada depois de criada. O servidor de réplica é sempre criado no mesmo grupo de recursos e na mesma assinatura que o servidor de origem. Se você quiser criar um servidor de réplica para um grupo de recursos diferente ou uma assinatura diferente, poderá [mover o servidor de réplica](../azure-resource-manager/management/move-resource-group-and-subscription.md) após a criação. É recomendável que a configuração do servidor de réplica seja mantida em valores iguais ou maiores do que a origem para garantir que a réplica seja capaz de acompanhar o mestre.

Depois que o servidor de réplica tiver sido criado, ele poderá ser visualizado no blade **Replication**.

   :::image type="content" source="./media/howto-read-replica-portal/list-replica.png" alt-text="Banco de Dados do Azure para MySQL - Listar Réplicas":::

## <a name="stop-replication-to-a-replica-server"></a>Parar a replicação para um servidor de réplica

> [!IMPORTANT]
> Parar a replicação para um servidor é irreversível. Depois que a replicação for interrompida entre uma origem e uma réplica, ela não poderá ser desfeita. O servidor de réplica então se torna um servidor autônomo e agora suporta tanto leitura quanto gravação. Este servidor não pode ser transformado em uma réplica novamente.

Para interromper a replicação entre um servidor de origem e de réplica do portal do Azure, use as seguintes etapas:

1. Na portal do Azure, selecione seu banco de dados do Azure de origem para o servidor MySQL. 

2. Selecione **Replicação** no menu, em **CONFIGURAÇÕES**.

3. Selecione o servidor de réplica para o qual você deseja interromper a replicação.

   :::image type="content" source="./media/howto-read-replica-portal/stop-replication-select.png" alt-text="Banco de Dados do Azure para MySQL - Parar servidor de seleção de replicação":::

4. Selecione **Parar replicação**.

   :::image type="content" source="./media/howto-read-replica-portal/stop-replication.png" alt-text="Banco de Dados do Azure para MySQL - Parar replicação":::

5. Confirme que você deseja interromper a replicação clicando em **OK**.

   :::image type="content" source="./media/howto-read-replica-portal/stop-replication-confirm.png" alt-text="Banco de Dados do Azure para MySQL - Confirme a replicação de parada":::

## <a name="delete-a-replica-server"></a>Excluir um servidor de réplica

Para excluir um servidor de réplica de leitura do portal do Azure, use as seguintes etapas:

1. Na portal do Azure, selecione seu banco de dados do Azure de origem para o servidor MySQL.

2. Selecione **Replicação** no menu, em **CONFIGURAÇÕES**.

3. Selecione o servidor de réplica que você deseja excluir.

   :::image type="content" source="./media/howto-read-replica-portal/delete-replica-select.png" alt-text="Banco de Dados do Azure para MySQL - Excluir Servidor de Seleção de Réplica":::

4. Selecione **Excluir réplica**

   :::image type="content" source="./media/howto-read-replica-portal/delete-replica.png" alt-text="Banco de dados do Azure para MySQL - Excluir réplica":::

5. Digite o nome da réplica e clique em **Excluir** para confirmar a exclusão da réplica.  

   :::image type="content" source="./media/howto-read-replica-portal/delete-replica-confirm.png" alt-text="Confirmar do banco de dados do Azure para MySQL - excluir réplica":::

## <a name="delete-a-source-server"></a>Excluir um servidor de origem

> [!IMPORTANT]
> A exclusão de um servidor de origem interrompe a replicação para todos os servidores de origem e exclui o próprio servidor mestre. Os servidores de réplica tornam-se servidores independentes que agora suportam leitura e gravação.

Para excluir um servidor de origem do portal do Azure, use as seguintes etapas:

1. Na portal do Azure, selecione seu banco de dados do Azure de origem para o servidor MySQL.

2. Na **Visão geral**, selecione **Excluir**.

   :::image type="content" source="./media/howto-read-replica-portal/delete-master-overview.png" alt-text="Banco de dados do Azure para MySQL - Excluir mestre":::

3. Digite o nome do servidor de origem e clique em **excluir** para confirmar a exclusão do servidor de origem.  

   :::image type="content" source="./media/howto-read-replica-portal/delete-master-confirm.png" alt-text="Banco de dados do Azure para MySQL-excluir confirmação mestre":::

## <a name="monitor-replication"></a>Monitorar a replicação

1. No [Portal do Azure](https://portal.azure.com/), selecione a réplica do Banco de Dados do Azure para o servidor MySQL que você deseja monitorar.

2. Na seção **Monitoramento** da barra lateral, selecione **Métricas**:

3. Selecione o **atraso de replicação em segundos** na lista suspensa de métricas disponíveis.

   :::image type="content" source="./media/howto-read-replica-portal/monitor-select-replication-lag.png" alt-text="Selecione o retardo de replicação":::

4. Selecione o intervalo de tempo que você deseja visualizar. A imagem abaixo seleciona um intervalo de tempo de 30 minutos.

   :::image type="content" source="./media/howto-read-replica-portal/monitor-replication-lag-time-range.png" alt-text="Selecionar intervalo de tempo":::

5. Veja o atraso de replicação para o intervalo de tempo selecionado. A imagem a seguir exibe os últimos 30 minutos.

   :::image type="content" source="./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png" alt-text="Selecionar intervalo de tempo 30 minutos":::

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [ler réplicas](concepts-read-replicas.md)