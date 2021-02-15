---
title: Gerenciar réplicas de leitura-portal do Azure-banco de dados do Azure para PostgreSQL-servidor único
description: Saiba como gerenciar réplicas de leitura banco de dados do Azure para PostgreSQL-servidor único do portal do Azure.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 11/05/2020
ms.openlocfilehash: 9fdef187e9bdf77b29c548f767a4b4edfeb62f44
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422171"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---single-server-from-the-azure-portal"></a>Criar e gerenciar réplicas de leitura no banco de dados do Azure para PostgreSQL-servidor único do portal do Azure

Neste artigo, você aprenderá a criar e gerenciar réplicas de leitura no Banco de Dados do Azure para PostgreSQL no portal do Azure. Para saber mais sobre réplicas de leitura, confira [Visão Geral](concepts-read-replicas.md).


## <a name="prerequisites"></a>Pré-requisitos
Um [banco de dados do Azure para o servidor PostgreSQL](quickstart-create-server-database-portal.md) ser o servidor primário.

## <a name="azure-replication-support"></a>Suporte à replicação do Azure

As [réplicas de leitura](concepts-read-replicas.md) e a [decodificação lógica](concepts-logical.md) dependem do postgres write ahead log (WAL) para obter informações. Esses dois recursos precisam de diferentes níveis de registro em log do Postgres. A decodificação lógica precisa de um nível mais alto de log do que as réplicas de leitura.

Para configurar o nível certo de registro em log, use o parâmetro de suporte de replicação do Azure. O suporte à replicação do Azure tem três opções de configuração:

* **Off** -coloca as informações mínimas no Wal. Essa configuração não está disponível na maioria dos servidores do banco de dados do Azure para PostgreSQL.  
* **Réplica** -mais detalhada do que **desativado**. Esse é o nível mínimo de log necessário para que as [réplicas de leitura](concepts-read-replicas.md) funcionem. Essa configuração é o padrão na maioria dos servidores.
* **Logical** -mais detalhado que a **réplica**. Este é o nível mínimo de registro em log para que a decodificação lógica funcione. As réplicas de leitura também funcionam nessa configuração.


> [!NOTE]
> Ao implantar réplicas de leitura para cargas de trabalho primárias com uso intensivo de demandas persistentes, o retardo de replicação pode continuar crescendo e talvez nunca seja capaz de se deslocar com o primário. Isso também pode aumentar o uso de armazenamento no primário, pois os arquivos WAL não são excluídos até que sejam recebidos na réplica.

## <a name="prepare-the-primary-server"></a>Preparar o servidor primário

1. Na portal do Azure, selecione um banco de dados do Azure para o servidor PostgreSQL a ser usado como mestre.

2. No menu do servidor, selecione **replicação**. Se o suporte à replicação do Azure for definido como pelo menos **réplica** , você poderá criar réplicas de leitura. 

3. Se o suporte à replicação do Azure não estiver definido como pelo menos **réplica** , defina-o. Selecione **Salvar**.

   :::image type="content" source="./media/howto-read-replicas-portal/set-replica-save.png" alt-text="Banco de dados do Azure para PostgreSQL-replicação – definir réplica e salvar":::

4. Reinicie o servidor para aplicar a alteração selecionando **Sim**.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-restart.png" alt-text="Banco de dados do Azure para PostgreSQL-replicação-confirmar reinicialização":::

5. Você receberá duas notificações de portal do Azure quando a operação for concluída. Há uma notificação para atualizar o parâmetro do servidor. Há outra notificação para a reinicialização do servidor que segue imediatamente.

   :::image type="content" source="./media/howto-read-replicas-portal/success-notifications.png" alt-text="Notificações de êxito":::

6. Atualize a página de portal do Azure para atualizar a barra de ferramentas de replicação. Agora você pode criar réplicas de leitura para este servidor.
   

## <a name="create-a-read-replica"></a>Criar uma réplica de leitura
Para criar uma réplica de leitura, siga estas etapas:

1. Selecione um servidor de banco de dados do Azure para PostgreSQL existente para usar como servidor primário. 

2. Na barra lateral do servidor, em **configurações** , selecione **replicação**.

3. Selecione **para adicionar réplica**.

   :::image type="content" source="./media/howto-read-replicas-portal/add-replica.png" alt-text="Adicionar uma réplica":::

4. Insira um nome para a réplica de leitura. 

    :::image type="content" source="./media/howto-read-replicas-portal/name-replica.png" alt-text="Nome da réplica":::

5. Selecione um local para a réplica. O local padrão é o mesmo que o do servidor primário.

    :::image type="content" source="./media/howto-read-replicas-portal/location-replica.png" alt-text="Selecione um local":::

   > [!NOTE]
   > Para saber mais sobre em quais regiões você pode criar uma réplica, visite o artigo [conceitos de réplica de leitura](concepts-read-replicas.md). 

6. Selecione **OK** para confirmar a criação da réplica.

Depois que a réplica de leitura é criada, ela pode ser exibida na janela **Replicação** :

:::image type="content" source="./media/howto-read-replicas-portal/list-replica.png" alt-text="Exibir a nova réplica na janela Replicação":::
 

> [!IMPORTANT]
> Examine a [seção considerações da visão geral da réplica de leitura](concepts-read-replicas.md#considerations).
>
> Antes que uma configuração do servidor primário seja atualizada para um novo valor, atualize a configuração de réplica para um valor igual ou maior. Essa ação ajuda a réplica a acompanhar as alterações feitas no mestre.

## <a name="stop-replication"></a>Parar replicação
Você pode interromper a replicação entre um servidor primário e uma réplica de leitura.

> [!IMPORTANT]
> Depois de parar a replicação em um servidor primário e em uma réplica de leitura, ela não pode ser desfeita. A réplica de leitura se torna um servidor autônomo que dá suporte a leituras e gravações. O servidor autônomo não pode se tornar uma réplica novamente.

Para interromper a replicação entre um servidor primário e uma réplica de leitura do portal do Azure, siga estas etapas:

1. No portal do Azure, selecione o servidor principal do banco de dados do Azure para PostgreSQL.

2. No menu de servidor, em **CONFIGURAÇÕES** , selecione **Replicação**.

3. Selecione o servidor de réplica para o qual interromper a replicação.

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica.png" alt-text="Selecionar a réplica":::
 
4. Selecione **Parar replicação**.

   :::image type="content" source="./media/howto-read-replicas-portal/select-stop-replication.png" alt-text="Selecione parar replicação":::
 
5. Selecione **OK** para interromper a replicação.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-stop-replication.png" alt-text="Confirme para interromper a replicação":::
 

## <a name="delete-a-primary-server"></a>Excluir um servidor primário
Para excluir um servidor primário, use as mesmas etapas para excluir um servidor autônomo do banco de dados do Azure para PostgreSQL. 

> [!IMPORTANT]
> Quando você exclui um servidor primário, a replicação para todas as réplicas de leitura é interrompida. As réplicas de leitura tornam-se servidores independentes que agora têm suporte para leitura e gravação.

Para excluir um servidor do portal do Azure, siga estas etapas:

1. No portal do Azure, selecione o servidor principal do banco de dados do Azure para PostgreSQL.

2. Abra a página **Visão geral** para o servidor. Selecione **Excluir**.

   :::image type="content" source="./media/howto-read-replicas-portal/delete-server.png" alt-text="Na página Visão geral do servidor, selecione para excluir o servidor primário":::
 
3. Insira o nome do servidor primário a ser excluído. Selecione **excluir** para confirmar a exclusão do servidor primário.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-delete.png" alt-text="Confirmar para excluir o servidor primário":::
 

## <a name="delete-a-replica"></a>Excluir uma réplica
Você pode excluir uma réplica de leitura semelhante a como você exclui um servidor primário.

- No portal do Azure, abra a página **Visão geral** para a réplica de leitura. Selecione **Excluir**.

   :::image type="content" source="./media/howto-read-replicas-portal/delete-replica.png" alt-text="Na página de Visão geral de réplica, selecione para excluir a réplica":::
 
Você também pode excluir a réplica de leitura usando a janela **Replicação** seguindo estas etapas:

1. No portal do Azure, selecione o servidor principal do banco de dados do Azure para PostgreSQL.

2. No menu de servidor, em **CONFIGURAÇÕES** , selecione **Replicação**.

3. Selecione a réplica de leitura a excluir.

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica.png" alt-text="Selecione a réplica a excluir":::
 
4. Selecione **excluir réplica**.

   :::image type="content" source="./media/howto-read-replicas-portal/select-delete-replica.png" alt-text="Selecionar Excluir réplica":::
 
5. Insira o nome da réplica a excluir. Selecione **Excluir** para confirmar a exclusão da réplica.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-delete-replica.png" alt-text="Confirme a exclusão da réplica":::
 

## <a name="monitor-a-replica"></a>Monitorar uma réplica
Duas métricas estão disponíveis para monitorar réplicas de leitura.

### <a name="max-lag-across-replicas-metric"></a>Métrica de Retardo Máximo Entre Réplicas
A métrica de **atraso máximo entre réplicas** mostra o retardo em bytes entre o servidor primário e a réplica mais deatrasante. 

1.  Na portal do Azure, selecione o servidor principal do banco de dados do Azure para PostgreSQL.

2.  Selecione **Métricas**. Na janela **métricas** , selecione **atraso máximo entre réplicas**.

    :::image type="content" source="./media/howto-read-replicas-portal/select-max-lag.png" alt-text="Monitorar o retardo máximo entre réplicas":::
 
3.  Para sua **Agregação** , selecione **Máx**.


### <a name="replica-lag-metric"></a>Métrica de retardo de réplica
A métrica **Retardo da Réplica** mostra o tempo decorrido desde a última transação reproduzida em uma réplica. Se não houver nenhuma transação ocorrendo no mestre, a métrica refletirá esse retardo.

1. No portal do Azure, selecione a réplica de leitura do Banco de Dados do Azure para PostgreSQL.

2. Selecione **Métricas**. Na janela **métricas** , selecione **atraso de réplica**.

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica-lag.png" alt-text="Monitorar o retardo da réplica":::
 
3. Para sua **Agregação** , selecione **Máx**. 
 
## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [réplicas de leitura no Banco de Dados do Azure para PostgreSQL](concepts-read-replicas.md).
* Saiba como [criar e gerenciar réplicas de leitura no CLI do Azure e na API REST](howto-read-replicas-cli.md).