---
title: Versões com suporte no banco de dados do Azure para MariaDB
description: Descreve as versões com suporte no Banco de Dados do Azure para o MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 69330e9d5a05fbcc892889f70a04f5eb4a4a2fb9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60935544"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Banco de Dados do Azure Suportado para as versões do servidor MariaDB
O Banco de Dados do Azure para o MariaDB foi desenvolvido a partir do [servidor MariaDB de código aberto](https://downloads.mariadb.org/), usando o mecanismo InnoDB. O Banco de Dados do Azure para MariaDB atualmente suporta a seguinte versão:

## <a name="mariadb-version-10217"></a>Versão do MariaDB 10.2.17
Consulte a [documentação do MariaDB](https://downloads.mariadb.org/mariadb/10.2.17/) para saber mais sobre melhorias e correções no MariaDB 10.2.17.

> [!NOTE]
> No serviço, um gateway é usado para redirecionar as conexões para as instâncias de servidor. Depois que a conexão é estabelecida, o cliente MySQL exibe a versão do MariaDB definida no gateway, não a versão real em execução na sua instância do servidor MariaDB. Para determinar a versão da sua instância do servidor MariaDB, use o comando `SELECT VERSION();` no prompt do MySQL.

## <a name="managing-updates-and-upgrades"></a>Gerenciar atualizações e upgrades
O serviço gerencia automaticamente a aplicação de patch para atualizações secundárias de versão.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre cotas e limitações específicas de recursos com base em sua **camada de serviço**, confira [Camadas de serviço](./concepts-pricing-tiers.md)
