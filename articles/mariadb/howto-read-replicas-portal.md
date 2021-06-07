---
title: Gerenciar réplicas de leitura-portal do Azure-banco de dados do Azure para MariaDB
description: Este artigo descreve como configurar e gerenciar réplicas de leitura no banco de dados do Azure para MariaDB usando o portal
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 6/10/2020
ms.openlocfilehash: 3ca6ef3c368a5f578cc90fae3923caa89f3b076a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664997"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-portal"></a>Como criar e gerenciar réplicas de leitura no banco de dados do Azure para MariaDB usando o portal do Azure

Neste artigo, você aprenderá a criar e gerenciar réplicas de leitura no banco de dados do Azure para o serviço MariaDB usando o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Um [banco de dados do Azure para o servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md) que será usado como o servidor de origem.

> [!IMPORTANT]
> O recurso ler réplica só está disponível para o banco de dados do Azure para servidores MariaDB nos tipos de preço Uso Geral ou com otimização de memória. Verifique se o servidor de origem está em um desses tipos de preço.

## <a name="create-a-read-replica"></a>Criar uma réplica de leitura

> [!IMPORTANT]
> Quando você cria uma réplica para uma fonte que não tem réplicas existentes, a origem será reiniciada primeiro para se preparar para a replicação. Leve isso em consideração e realize essas operações durante um período de pouca atividade.

Um servidor de réplica de leitura pode ser criado usando as seguintes etapas:

1. Faça logon no [Portal do Azure](https://portal.azure.com/).

2. Selecione o banco de dados do Azure existente para o servidor MariaDB que você deseja usar como mestre. Essa ação abre a página **Visão geral** do runbook.

3. Selecione **Replicação** no menu, em **CONFIGURAÇÕES**.

4. Selecione **para adicionar réplica**.

   ![Banco de dados do Azure para MariaDB-replicação](./media/howto-read-replica-portal/add-replica.png)

5. Insira um nome para o servidor de réplica.

    ![Banco de dados do Azure para MariaDB-nome da réplica](./media/howto-read-replica-portal/replica-name.png)

6. Selecione o local para o servidor de réplica. O local padrão é o mesmo que o do servidor de origem.

    ![Banco de dados do Azure para MariaDB-local da réplica](./media/howto-read-replica-portal/replica-location.png)

7. Selecione **OK** para confirmar a criação da réplica.

> [!NOTE]
> Réplicas de leitura são criadas com a mesma configuração de servidor que o mestre. A configuração do servidor de réplica pode ser alterada depois de criada. É recomendável que a configuração do servidor de réplica seja mantida em valores iguais ou maiores do que a origem para garantir que a réplica seja capaz de acompanhar o mestre.

Depois que o servidor de réplica tiver sido criado, ele poderá ser visualizado no blade **Replication**.

   ![Banco de dados do Azure para MariaDB-listar réplicas](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>Parar a replicação para um servidor de réplica

> [!IMPORTANT]
> Parar a replicação para um servidor é irreversível. Depois que a replicação for interrompida entre uma origem e uma réplica, ela não poderá ser desfeita. O servidor de réplica então se torna um servidor autônomo e agora suporta tanto leitura quanto gravação. Este servidor não pode ser transformado em uma réplica novamente.

Para interromper a replicação entre um servidor de origem e de réplica do portal do Azure, use as seguintes etapas:

1. Na portal do Azure, selecione seu banco de dados do Azure de origem para o servidor MariaDB. 

2. Selecione **Replicação** no menu, em **CONFIGURAÇÕES**.

3. Selecione o servidor de réplica para o qual você deseja interromper a replicação.

   ![Banco de dados do Azure para MariaDB-parar replicação selecionar servidor](./media/howto-read-replica-portal/stop-replication-select.png)

4. Selecione **Parar replicação**.

   ![Banco de dados do Azure para MariaDB-interromper replicação](./media/howto-read-replica-portal/stop-replication.png)

5. Confirme que você deseja interromper a replicação clicando em **OK**.

   ![Banco de dados do Azure para MariaDB-parar confirmação de replicação](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Excluir um servidor de réplica

Para excluir um servidor de réplica de leitura do portal do Azure, use as seguintes etapas:

1. Na portal do Azure, selecione seu banco de dados do Azure de origem para o servidor MariaDB.

2. Selecione **Replicação** no menu, em **CONFIGURAÇÕES**.

3. Selecione o servidor de réplica que você deseja excluir.

   ![Banco de dados do Azure para MariaDB-excluir réplica selecionar servidor](./media/howto-read-replica-portal/delete-replica-select.png)

4. Selecione **Excluir réplica**

   ![Banco de dados do Azure para MariaDB-excluir réplica](./media/howto-read-replica-portal/delete-replica.png)

5. Digite o nome da réplica e clique em **Excluir** para confirmar a exclusão da réplica.  

   ![Banco de dados do Azure para MariaDB-excluir confirmação de réplica](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-source-server"></a>Excluir um servidor de origem

> [!IMPORTANT]
> A exclusão de um servidor de origem interrompe a replicação para todos os servidores de origem e exclui o próprio servidor mestre. Os servidores de réplica tornam-se servidores independentes que agora suportam leitura e gravação.

Para excluir um servidor de origem do portal do Azure, use as seguintes etapas:

1. Na portal do Azure, selecione seu banco de dados do Azure de origem para o servidor MariaDB.

2. Na **Visão geral**, selecione **Excluir**.

   ![Banco de dados do Azure para MariaDB-excluir mestre](./media/howto-read-replica-portal/delete-master-overview.png)

3. Digite o nome do servidor de origem e clique em **excluir** para confirmar a exclusão do servidor de origem.  

   ![Banco de dados do Azure para MariaDB-excluir confirmação mestre](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>Monitorar a replicação

1. Na [portal do Azure](https://portal.azure.com/), selecione a réplica do banco de dados do Azure para o servidor MariaDB que você deseja monitorar.

2. Na seção **Monitoramento** da barra lateral, selecione **Métricas**:

3. Selecione o **atraso de replicação em segundos** na lista suspensa de métricas disponíveis.

   ![Selecione o retardo de replicação](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Selecione o intervalo de tempo que você deseja visualizar. A imagem abaixo seleciona um intervalo de tempo de 30 minutos.

   ![Selecionar intervalo de tempo](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. Veja o atraso de replicação para o intervalo de tempo selecionado. A imagem abaixo exibe os últimos 30 minutos para uma carga de trabalho grande.

   ![Selecionar intervalo de tempo 30 minutos](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [ler réplicas](concepts-read-replicas.md)
