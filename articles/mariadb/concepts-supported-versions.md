---
title: Versões com suporte – banco de dados do Azure para MariaDB
description: Saiba quais versões do servidor MariaDB têm suporte no banco de dados do Azure para o serviço MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 7/20/2020
ms.openlocfilehash: dbf32626714200e6712c67b701ebc597c4a7ba7e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98662433"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Banco de Dados do Azure Suportado para as versões do servidor MariaDB

O banco de dados do Azure para MariaDB foi desenvolvido a partir do [servidor MariaDB](https://downloads.mariadb.org/)de código-fonte aberto, usando o mecanismo de InnoDB.

MariaDB usa o esquema de nomenclatura X. Y. Z. X é a versão principal, Y é a versão secundária e Z é a versão do patch.

> [!NOTE]
> No serviço, um gateway é usado para redirecionar as conexões para as instâncias de servidor. Depois que a conexão é estabelecida, o cliente MySQL exibe a versão do MariaDB definida no gateway, não a versão real em execução na sua instância do servidor MariaDB. Para determinar a versão da sua instância do servidor MariaDB, use o `SELECT VERSION();` comando.

O Banco de Dados do Azure para MariaDB atualmente suporta a seguinte versão:

## <a name="mariadb-version-102"></a>MariaDB versão 10,2

Versão do patch: 10.2.32

Consulte a [documentação do MariaDB](https://mariadb.com/kb/en/mariadb-10232-release-notes/) para saber mais sobre melhorias e correções nesta versão.

## <a name="mariadb-version-103"></a>MariaDB versão 10,3

Versão do patch: 10.3.23

Consulte a [documentação do MariaDB](https://mariadb.com/kb/en/mariadb-10323-release-notes/) para saber mais sobre melhorias e correções nesta versão.

## <a name="managing-updates-and-upgrades"></a>Gerenciar atualizações e upgrades
O serviço gerencia automaticamente atualizações para atualizações de patch. Por exemplo, 10.2.21 para 10.2.23.  

Atualmente, não há suporte para atualizações de versão principal e secundária. Por exemplo, não há suporte para a atualização do MariaDB 10.2 para o MariaDB 10.3. Se você quiser atualizar de 10,2 para 10,3, faça um [despejo e restaure](./howto-migrate-dump-restore.md) -o para um servidor que foi criado com a nova versão do mecanismo.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre cotas e limitações específicas de recursos com base em sua **camada de serviço**, confira [Camadas de serviço](./concepts-pricing-tiers.md).
