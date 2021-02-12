---
title: Replicação de dados do Azure para MySQL
description: Saiba mais sobre como usar a replicação de dados para sincronizar de um servidor externo no banco de dados do Azure para o serviço MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 8/7/2020
ms.openlocfilehash: 99beddba470f73d6eadb448dfe1b77453ce6426d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996212"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>Replicar dados no Banco de Dados do Azure para MySQL

A Replicação de Dados permite sincronizar os dados de um servidor MySQL externo no serviço Banco de Dados do Azure para MySQL. O servidor externo pode ser local, em máquinas virtuais ou um serviço de banco de dados hospedado por outros provedores de nuvem. A Replicação nos dados se baseia na replicação nativa baseada na posição do arquivo de log binário (binlog) para o MySQL. Para saber mais sobre a replicação do binlog, confira a [visão geral da replicação do binlog do MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html). 

## <a name="when-to-use-data-in-replication"></a>Quando usar a replicação nos dados
Os cenários principais nos quais considerar o uso da replicação nos dados são:

- **Sincronização de dados híbrida:** com a replicação nos dados, você pode manter os dados sincronizados entre os servidores locais e o Banco de Dados do Azure para MySQL. Essa sincronização é útil para criar aplicativos híbridos. Esse método é atraente quando você tem um servidor de banco de dados local existente, mas deseja mover os dados para uma região mais próxima aos usuários finais.
- **Sincronização de várias nuvens:** para soluções de nuvem complexas, use a replicação nos dados para sincronizar dados entre o Banco de Dados do Azure para MySQL e provedores de nuvem diferentes, incluindo máquinas virtuais e serviços de banco de dados nessas nuvens.
 
Para cenários de migração, use o [serviço de migração de banco de dados do Azure](https://azure.microsoft.com/services/database-migration/)(DMS).

## <a name="limitations-and-considerations"></a>Limitações e considerações

### <a name="data-not-replicated"></a>Dados não replicados
O [*banco de dados do sistema MySQL*](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) no servidor de origem não está replicado. As alterações nas contas e permissões no servidor de origem não são replicadas. Se você criar uma conta no servidor de origem e essa conta precisar acessar o servidor de réplica, crie manualmente a mesma conta no lado do servidor de réplica. Para entender quais tabelas estão contidas no banco de dados do sistema, confira o [Manual do MySQL](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html).

### <a name="filtering"></a>Filtragem
Para ignorar a replicação de tabelas do seu servidor de origem (hospedadas localmente, em máquinas virtuais ou em um serviço de banco de dados hospedado por outros provedores de nuvem), `replicate_wild_ignore_table` há suporte para o parâmetro. Opcionalmente, atualize esse parâmetro no servidor de réplica hospedado no Azure usando o [portal do Azure](howto-server-parameters.md) ou [CLI do Azure](howto-configure-server-parameters-using-cli.md).

Consulte a [documentação do MySQL](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html#option_mysqld_replicate-wild-ignore-table) para saber mais sobre esse parâmetro.

### <a name="requirements"></a>Requisitos
- A versão do servidor de origem deve ser pelo menos a versão 5,6 do MySQL. 
- As versões do servidor de origem e de réplica devem ser as mesmas. Por exemplo, ambos devem ser MySQL versão 5.6 ou ambos devem ser MySQL versão 5.7.
- Cada tabela deve ter uma chave primária.
- O servidor de origem deve usar o mecanismo InnoDB do MySQL.
- O usuário deve ter permissões para configurar o log binário e criar novos usuários no servidor de origem.
- Se o servidor de origem tiver o SSL habilitado, verifique se o certificado de autoridade de certificação SSL fornecido para o domínio foi incluído no `mysql.az_replication_change_master` procedimento armazenado. Consulte os [exemplos](./howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication) a seguir e o `master_ssl_ca` parâmetro.
- Verifique se o endereço IP do servidor de origem foi adicionado ao banco de dados do Azure para as regras de firewall do servidor de réplica do MySQL. Atualizar regras de firewall usando o [Portal do Azure](./howto-manage-firewall-using-portal.md) ou a [CLI do Azure](./howto-manage-firewall-using-cli.md).
- Verifique se o computador que hospeda o servidor de origem permite o tráfego de entrada e de saída na porta 3306.
- Verifique se o servidor de origem tem um **endereço IP público**, se o DNS está acessível publicamente ou se tem um FQDN (nome de domínio totalmente qualificado).

### <a name="other"></a>Outro
- A replicação de dados têm suporte apenas em tipos de preços de Uso Geral e Otimizados para Memória.
- O GTID (identificadores de transação globais) não são compatíveis.

## <a name="next-steps"></a>Próximas etapas
- Saiba como [Configurar a replicação de dados em](howto-data-in-replication.md)
- Saiba mais sobre [replicar no Azure com réplicas de leitura](concepts-read-replicas.md)
- Saiba mais sobre como [migrar dados com tempo de inatividade mínimo usando DMS](howto-migrate-online.md)