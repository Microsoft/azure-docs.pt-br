---
title: Versões com suporte – banco de dados do Azure para MySQL
description: Saiba quais versões do MySQL Server têm suporte no banco de dados do Azure para o serviço MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: 920f6a4fec1ec8a260a98641888268e4955bbf44
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101718770"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Versões com suporte do servidor de Banco de Dados do Azure para MySQL

O banco de dados do Azure para MySQL foi desenvolvido no [MySQL Community Edition](https://www.mysql.com/products/community/), usando o mecanismo de armazenamento InnoDB. O serviço dá suporte a toda a versão principal atual com suporte da Comunidade, o MySQL 5,6, 5,7 e 8,0. O MySQL usa o esquema de nomenclatura X. Y. Z em que X é a versão principal, Y é a versão secundária e Z é o lançamento de correção de bug. Para obter mais informações sobre o esquema, confira a [documentação do MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

> [!NOTE]
> Na opção de implantação de servidor único, um gateway é usado para redirecionar as conexões para instâncias de servidor. Depois que a conexão for estabelecida, o cliente MySQL exibirá a versão do MySQL definida no gateway, não a versão real em execução na instância do servidor MySQL. Para determinar a versão da instância do servidor MySQL, use o `SELECT VERSION();` comando no prompt do MySQL.

Atualmente, o banco de dados do Azure para MySQL dá suporte às seguintes versões principais e secundárias do MySQL:


| Versão | Servidor único <br/> Versão secundária atual |Servidor flexível (versão prévia) <br/> Versão secundária atual  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL Versão 5.6 |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html) | Sem suporte|
|MySQL Versão 5.7 | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)|
|MySQL versão 8,0 | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

## <a name="managing-updates-and-upgrades"></a>Gerenciar atualizações e upgrades
O serviço gerencia automaticamente a aplicação de patch para atualizações de versão de correção de bug. Por exemplo, 5.7.20 a 5.7.21.  

A atualização de versão principal tem suporte no momento pelo serviço para atualizações do MySQL v 5.6 para v 5.7. Para obter mais detalhes, consulte [como executar atualizações de versão principais](how-to-major-version-upgrade.md). Se você quiser atualizar de 5,7 para 8,0, recomendamos que você execute [despejo e restauração](./concepts-migrate-dump-restore.md) em um servidor que foi criado com a nova versão do mecanismo.

## <a name="next-steps"></a>Próximas etapas

- Para obter detalhes sobre a política de controle de versão do banco de dados do Azure para MySQL, consulte [este documento](concepts-version-policy.md).
- Para obter informações sobre cotas e limitações de recursos específicas com base em sua **camada de serviço**, consulte [camadas de serviço](./concepts-pricing-tiers.md)
