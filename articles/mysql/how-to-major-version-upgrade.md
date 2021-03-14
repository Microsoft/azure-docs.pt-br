---
title: Atualização de versão principal no banco de dados do Azure para MySQL-servidor único
description: Este artigo descreve como você pode atualizar a versão principal para o banco de dados do Azure para MySQL-servidor único
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: how-to
ms.date: 1/28/2021
ms.openlocfilehash: 13cf315291cdf788951e352e430976851b30ce0c
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102216710"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server"></a>Atualização de versão principal no banco de dados do Azure para MySQL servidor único

> [!NOTE]
> Este artigo contém referências ao termo _subordinado_, um termo que a Microsoft não usa mais. Quando o termo for removido do software, iremos removê-lo deste artigo.
>

> [!IMPORTANT]
> A atualização de versão principal para o banco de dados do Azure para MySQL servidor único está em visualização pública.

Este artigo descreve como você pode atualizar sua versão principal do MySQL in-loco no banco de dados do Azure para MySQL servidor único.

Esse recurso permitirá que os clientes realizem atualizações in-loco de seus servidores MySQL 5,6 para o MySQL 5,7 com um clique no botão sem nenhuma movimentação de dados ou a necessidade de qualquer alteração na cadeia de conexão do aplicativo.

> [!Note]
> * A atualização da versão principal só está disponível para a atualização da versão principal do MySQL 5,6 para o MySQL 5,7.
> * O servidor não estará disponível durante a operação de atualização. Portanto, é recomendável realizar atualizações durante a janela de manutenção planejada. Você pode considerar a [realização de uma atualização de versão principal de tempo de inatividade mínimo do mysql 5,6 para o mysql 5,7 usando a réplica de leitura.](#perform-minimal-downtime-major-version-upgrade-from-mysql-56-to-mysql-57-using-read-replicas)

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-portal"></a>Executar a atualização da versão principal do MySQL 5,6 para o MySQL 5,7 usando portal do Azure

Siga estas etapas para executar a atualização de versão principal para seu banco de dados do Azure do servidor MySQL 5,6 usando portal do Azure

> [!IMPORTANT]
> É recomendável executar a atualização primeiro na cópia restaurada do servidor em vez de atualizar a produção diretamente. Consulte [como executar uma restauração pontual](howto-restore-server-portal.md#point-in-time-restore).

1. Na [portal do Azure](https://portal.azure.com/), selecione o servidor de banco de dados do Azure para MySQL 5,6 existente.

2. Na página **visão geral** , clique no botão **Atualizar** na barra de ferramentas.

3. Na seção **atualização** , selecione **OK** para atualizar o servidor do banco de dados do Azure para MySQL 5,6 para o servidor 5,7.

   :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Banco de dados do Azure para MySQL-visão geral-atualização":::

4. Uma notificação confirmará que a atualização foi bem-sucedida.


## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-cli"></a>Executar a atualização da versão principal do MySQL 5,6 para o MySQL 5,7 usando CLI do Azure

Siga estas etapas para executar a atualização de versão principal para seu banco de dados do Azure do servidor MySQL 5,6 usando CLI do Azure

> [!IMPORTANT]
> É recomendável executar a atualização primeiro na cópia restaurada do servidor em vez de atualizar a produção diretamente. Consulte [como executar uma restauração pontual](howto-restore-server-cli.md#server-point-in-time-restore).

1. Instale o [CLI do Azure para Windows](/cli/azure/install-azure-cli) ou use CLI do Azure no [Azure cloud Shell](../cloud-shell/overview.md) para executar os comandos de atualização. 
 
   Essa atualização requer a versão 2.16.0 ou posterior do CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada. Execute az version para localizar a versão e as bibliotecas dependentes que estão instaladas. Para fazer a atualização para a versão mais recente, execute az upgrade.

2. Depois de entrar, execute o comando [AZ MySQL Server upgrade](/cli/azure/mysql/server#az_mysql_server_upgrade) :

   ```azurecli
   az mysql server upgrade --name testsvr --resource-group testgroup --subscription MySubscription --target-server-version 5.7"
   ```
   
   O prompt de comando mostra a mensagem "-Running". Depois que essa mensagem não for mais exibida, a atualização da versão será concluída.

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-on-read-replica-using-azure-portal"></a>Execute a atualização da versão principal do MySQL 5,6 para o MySQL 5,7 na réplica de leitura usando portal do Azure

1. Na [portal do Azure](https://portal.azure.com/), selecione o servidor de réplica de leitura existente do banco de dados do Azure para MySQL 5,6.

2. Na página **visão geral** , clique no botão **Atualizar** na barra de ferramentas.

3. Na seção **atualização** , selecione **OK** para atualizar o servidor de réplica de leitura do banco de dados do Azure para MySQL 5,6 para o servidor 5,7.

   :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Banco de dados do Azure para MySQL-visão geral-atualização":::

4. Uma notificação confirmará que a atualização foi bem-sucedida.

5. Na página **visão geral** , confirme se a versão do servidor de réplica de leitura do banco de dados do Azure para MySQL é 5,7.

6. Agora, acesse o servidor primário e [Execute a atualização da versão principal](#perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-portal) nele.

## <a name="perform-minimal-downtime-major-version-upgrade-from-mysql-56-to-mysql-57-using-read-replicas"></a>Executar a atualização de versão principal de tempo de inatividade mínimo do MySQL 5,6 para o MySQL 5,7 usando réplicas de leitura

Você pode executar a atualização de versão principal de tempo de inatividade mínimo do MySQL 5,6 para o MySQL 5,7 utilizando réplicas de leitura. A ideia é atualizar a réplica de leitura do seu servidor para o 5,7 primeiro e depois fazer failover do seu aplicativo para apontar para a réplica de leitura e torná-la uma nova primária.

1. Na [portal do Azure](https://portal.azure.com/), selecione seu banco de dados do Azure existente para MySQL 5,6.

2. Crie uma [réplica de leitura](./concepts-read-replicas.md#create-a-replica) do seu servidor primário.

3. [Atualize sua réplica de leitura](#perform-major-version-upgrade-from-mysql-56-to-mysql-57-on-read-replica-using-azure-portal) para a versão 5,7.

4. Depois de confirmar que o servidor de réplica está em execução na versão 5,7, interrompa seu aplicativo de se conectar ao servidor primário.
 
5. Verifique o status de replicação e certifique-se de que a réplica seja todas obtidas com a primária para que todos os dados estejam em sincronia e verifique se não há nenhuma nova operação executada no primário.

   Chame o [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) comando no servidor de réplica para exibir o status de replicação.

   ```sql
   SHOW SLAVE STATUS\G
   ```

   Se o estado de `Slave_IO_Running` e for `Slave_SQL_Running` "Sim" e o valor de `Seconds_Behind_Master` for "0", a replicação estará funcionando bem. `Seconds_Behind_Master` indica o atraso da réplica. Se o valor não for "0", significa que a réplica está processando atualizações. Quando você confirmar `Seconds_Behind_Master` é "0", é seguro interromper a replicação.

6. Promova a réplica de leitura para primária [interrompendo a replicação](./howto-read-replicas-portal.md#stop-replication-to-a-replica-server).

7. Aponte seu aplicativo para a nova primária (antiga réplica) que está executando o servidor 5,7. Cada servidor tem uma cadeia de conexão exclusiva. Atualize seu aplicativo para apontar para a réplica (antiga) em vez da origem.

> [!Note]
> Esse cenário terá tempo de inatividade somente durante as etapas 4, 5 e 6.


## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="when-will-this-upgrade-feature-be-ga-as-we-have-mysql-v56-in-our-production-environment-that-we-need-to-upgrade"></a>Quando esse recurso de atualização será GA, pois temos o MySQL v 5.6 em nosso ambiente de produção que precisamos atualizar?

A GA desse recurso é planejada antes da desativação do MySQL v 5.6. No entanto, o recurso está pronto para produção e totalmente suportado pelo Azure, portanto, você deve executá-lo com confiança em seu ambiente. Como prática recomendada, sugerimos fortemente que você execute e teste-o primeiro em uma cópia restaurada do servidor para que você possa estimar o tempo de inatividade durante a atualização e executar o teste de compatibilidade de aplicativos antes de executá-lo na produção. Para obter mais informações, consulte [como executar a restauração pontual](howto-restore-server-portal.md#point-in-time-restore) para criar uma cópia pontual do seu servidor. 

### <a name="will-this-cause-downtime-of-the-server-and-if-so-how-long"></a>Isso causará tempo de inatividade do servidor e, nesse caso, quanto tempo?

Sim, o servidor não estará disponível durante o processo de atualização, portanto, recomendamos que você execute essa operação durante a janela de manutenção planejada. O tempo de inatividade estimado depende do tamanho do banco de dados, do tamanho do armazenamento provisionado (IOPs provisionado) e do número de tabelas no banco de dados. O tempo de atualização é diretamente proporcional ao número de tabelas no servidor. Espera-se que as atualizações dos servidores SKU básicos demorem mais tempo como na plataforma de armazenamento padrão. Para estimar o tempo de inatividade do seu ambiente de servidor, é recomendável primeiro executar a atualização na cópia restaurada do servidor. Considere [executar a atualização de versão principal de tempo de inatividade mínimo do mysql 5,6 para o mysql 5,7 usando a réplica de leitura.](#perform-minimal-downtime-major-version-upgrade-from-mysql-56-to-mysql-57-using-read-replicas)

### <a name="what-will-happen-if-we-do-not-choose-to-upgrade-our-mysql-v56-server-before-february-5-2021"></a>O que acontecerá se não optarmos por atualizar nosso servidor MySQL v 5.6 antes de 5 de fevereiro de 2021?

Você ainda pode continuar executando o servidor MySQL v 5.6 como antes. O Azure **nunca** executará a atualização forçada no servidor. No entanto, as restrições documentadas na [política de versão do banco de dados do Azure para MySQL](concepts-version-policy.md) serão aplicadas.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [a política de controle de versão do banco de dados do Azure para MySQL](concepts-version-policy.md).