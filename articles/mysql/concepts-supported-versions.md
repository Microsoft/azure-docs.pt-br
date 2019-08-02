---
title: Versões com suporte no Banco de Dados do Azure para MySQL
description: Descreve as versões com suporte no Banco de Dados do Azure para MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 7/25/2019
ms.openlocfilehash: 3d4bab4558ebfd0f6031ef00a0b67bb0d5b61120
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501439"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Versões com suporte do servidor de Banco de Dados do Azure para MySQL

O Banco de Dados do Azure para MySQL foi desenvolvido a partir do [MySQL Community Edition](https://www.mysql.com/products/community/), usando o mecanismo InnoDB.

O MySQL usa o esquema de nomenclatura X.Y.Z. X é a versão principal, Y é a versão secundária e Z é a versão de correção de bug. Para obter mais informações sobre o esquema, confira a [documentação do MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

> [!NOTE]
> No serviço, um gateway é usado para redirecionar as conexões para as instâncias de servidor. Depois que a conexão for estabelecida, o cliente MySQL exibirá a versão do MySQL definida no gateway, não a versão real em execução na instância do servidor MySQL. Para determinar a versão da instância do servidor MySQL, use o `SELECT VERSION();` comando no prompt do MySQL.

No momento, o Banco de Dados do Azure para MySQL dá suporte às seguintes versões:

## <a name="mysql-version-56"></a>MySQL Versão 5.6

Versão de correção de bug: 5.6.42

Consulte as notas de [versão](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-42.html) do MySQL para saber mais sobre melhorias e correções no MySQL 5.6.42.

## <a name="mysql-version-57"></a>MySQL Versão 5.7

Versão de correção de bug: 5.7.24

Consulte as notas de [versão](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-24.html) do MySQL para saber mais sobre melhorias e correções no MySQL 5.7.24.

## <a name="mysql-version-80"></a>MySQL versão 8,0

> [!NOTE]
> O MySQL 8,0 está atualmente em versão prévia. Se você não vir o MySQL 8,0 no portal do Azure, a implantação poderá não ter sido concluída em sua região. 

Versão de correção de bug: 8.0.15

Consulte as notas de [versão](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) do MySQL para saber mais sobre melhorias e correções no MySQL 8.0.15.

## <a name="managing-updates-and-upgrades"></a>Gerenciar atualizações e upgrades
O serviço gerencia automaticamente a aplicação de patch para atualizações de versão de correção de bug. Por exemplo, 5.7.20 a 5.7.21.  

Atualmente, não há suporte para atualizações de versão principal e secundária. Por exemplo, não há suporte para a atualização do MySQL 5.6 para o MySQL 5.7. Caso deseje atualizar da versão 5.6 para a 5.7, faça um [despejo e restaure-a](./concepts-migrate-dump-restore.md) para um servidor que foi criado com a nova versão do mecanismo.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre cotas e limitações específicas de recursos com base em sua **camada de serviço**, confira [Camadas de serviço](./concepts-pricing-tiers.md)
