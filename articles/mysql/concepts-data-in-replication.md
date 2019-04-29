---
title: Replicar dados no Banco de Dados do Azure para MySQL.
description: Este artigo descreve a replicação nos dados para o Banco de Dados do Azure para MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: f91a6da9a305c6620e4e01ab7aa3c554374cb5d7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60996787"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>Replicar dados no Banco de Dados do Azure para MySQL

A Replicação de Dados permite sincronizar os dados de um servidor MySQL externo no serviço Banco de Dados do Azure para MySQL. O servidor externo pode ser local, em máquinas virtuais ou um serviço de banco de dados hospedado por outros provedores de nuvem. A Replicação nos dados se baseia na replicação nativa baseada na posição do arquivo de log binário (binlog) para o MySQL. Para saber mais sobre a replicação do binlog, confira a [visão geral da replicação do binlog do MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html). 

## <a name="when-to-use-data-in-replication"></a>Quando usar a replicação nos dados
Os cenários principais nos quais considerar o uso da replicação nos dados são:

- **Sincronização de Dados Híbrida:** Com a Replicação de Dados, você pode manter os dados sincronizados entre os servidores locais e o Banco de Dados do Azure para MySQL. Essa sincronização é útil para criar aplicativos híbridos. Esse método é atraente quando você tem um servidor de banco de dados local existente, mas deseja mover os dados para uma região mais próxima aos usuários finais.
- **Sincronização de várias nuvens:** Para soluções na nuvem complexas, use Replicação de Dados para sincronizar dados entre o Banco de Dados do Azure para MySQL e diferentes provedores de nuvem, incluindo máquinas virtuais e serviços de banco de dados hospedados nessas nuvens.

## <a name="limitations-and-considerations"></a>Limitações e considerações

### <a name="data-not-replicated"></a>Dados não replicados
O [*banco de dados de sistema de mysql*](https://dev.mysql.com/doc/refman/5.7/en/system-database.html) no servidor mestre não é replicado. Alterações nas contas e permissões no servidor mestre não são replicadas. Se você criar uma conta no servidor mestre e essa conta precisar acessar o servidor de réplica, crie manualmente a mesma conta no lado do servidor de réplica. Para entender quais tabelas estão contidas no banco de dados do sistema, confira o [Manual do MySQL](https://dev.mysql.com/doc/refman/5.7/en/system-database.html).

### <a name="requirements"></a>Requisitos
- A versão do servidor mestre deve ser pelo menos a versão 5.6 do MySQL. 
- As versões do servidor principal e de réplica devem ser as mesmas. Por exemplo, ambos devem ser MySQL versão 5.6 ou ambos devem ser MySQL versão 5.7.
- Cada tabela deve ter uma chave primária.
- O servidor mestre deve usar o mecanismo MySQL InnoDB.
- O usuário deve ter permissões para configurar o log binário e criar novos usuários no servidor mestre.

### <a name="other"></a>Outros
- A replicação de dados têm suporte apenas em tipos de preços de Uso Geral e Otimizados para Memória.
- O GTID (identificadores de transação globais) não são compatíveis.

## <a name="next-steps"></a>Próximas etapas
- Saiba como [configurar a replicação nos dados](howto-data-in-replication.md)
- Saiba mais sobre [replicar no Azure com réplicas de leitura](concepts-read-replicas.md)
