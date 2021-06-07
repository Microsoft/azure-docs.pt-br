---
title: Versões com suporte – banco de dados do Azure para MySQL
description: Saiba quais versões do MySQL Server têm suporte no banco de dados do Azure para o serviço MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: 314462517ba4e63694266b5e49231cb8536f3635
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105604712"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Versões com suporte do servidor de Banco de Dados do Azure para MySQL

O banco de dados do Azure para MySQL foi desenvolvido no [MySQL Community Edition](https://www.mysql.com/products/community/), usando o mecanismo de armazenamento InnoDB. O serviço dá suporte a toda a versão principal atual com suporte da Comunidade, o MySQL 5,6, 5,7 e 8,0. O MySQL usa o esquema de nomenclatura X. Y. Z em que X é a versão principal, Y é a versão secundária e Z é o lançamento de correção de bug. Para obter mais informações sobre o esquema, confira a [documentação do MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).



## <a name="connect-to-a-gateway-node-that-is-running-a-specific-mysql-version"></a>Conectar-se a um nó de gateway que está executando uma versão específica do MySQL

Na opção de implantação de servidor único, um gateway é usado para redirecionar as conexões para instâncias de servidor. Depois que a conexão for estabelecida, o cliente MySQL exibirá a versão do MySQL definida no gateway, não a versão real em execução na instância do servidor MySQL. Para determinar a versão da instância do servidor MySQL, use o `SELECT VERSION();` comando no prompt do MySQL. Examine a [arquitetura de conectividade](https://docs.microsoft.com/azure/mysql/concepts-connectivity-architecture#connectivity-architecture) para saber mais sobre gateways na arquitetura de serviço banco de dados do Azure para MySQL.

Como o banco de dados do Azure para MySQL dá suporte à versão principal do v 5.6, v 5.7 e v 8.0, a porta padrão 3306 para se conectar ao banco de dados do Azure para MySQL executa o cliente MySQL versão 5,6 (o denominador menos comum) para dar suporte a conexões com servidores de todas as três versões principais com suporte. No entanto, se seu aplicativo tiver um requisito para se conectar a uma versão principal específica, digamos que v 5.7 ou v 8.0, você poderá fazer isso alterando a porta na cadeia de conexão do servidor.

No serviço de banco de dados do Azure para MySQL, os nós de gateway escutam a porta 3308 para clientes v 5.7 e a porta 3309 para clientes v 8.0. Em outras palavras, se você quiser se conectar ao cliente do gateway v 5.7, deverá usar o nome do servidor totalmente qualificado e a porta 3308 para se conectar ao servidor do aplicativo cliente. Da mesma forma, se você quiser se conectar ao cliente do gateway v 8.0, poderá usar o nome do servidor totalmente qualificado e a porta 3309 para se conectar ao servidor. Verifique o exemplo a seguir para maior clareza.

:::image type="content" source="./media/concepts-supported-versions/concepts-supported-versions-gateway.png" alt-text="Exemplo de conexão por meio de diferentes versões de MySQL de gateway":::


## <a name="azure-database-for-mysql-currently-supports-the-following-major-and-minor-versions-of-mysql"></a>Atualmente, o banco de dados do Azure para MySQL dá suporte às seguintes versões principais e secundárias do MySQL:


| Versão | Servidor único <br/> Versão secundária atual |Servidor Flexível (versão prévia) <br/> Versão secundária atual  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL Versão 5.6 |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html) (desativado) | Sem suporte|
|MySQL Versão 5.7 | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)|
|MySQL versão 8,0 | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

Leia a política de suporte de versão para versões desativadas na [documentação da política de suporte de versão.](concepts-version-policy.md#retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql)

## <a name="managing-updates-and-upgrades"></a>Gerenciar atualizações e upgrades
O serviço gerencia automaticamente a aplicação de patch para atualizações de versão de correção de bug. Por exemplo, 5.7.20 a 5.7.21.  

A atualização de versão principal tem suporte no momento pelo serviço para atualizações do MySQL v 5.6 para v 5.7. Para obter mais detalhes, consulte [como executar atualizações de versão principais](how-to-major-version-upgrade.md). Se você quiser atualizar de 5,7 para 8,0, recomendamos que você execute [despejo e restauração](./concepts-migrate-dump-restore.md) em um servidor que foi criado com a nova versão do mecanismo.

## <a name="next-steps"></a>Próximas etapas

- Para obter detalhes sobre a política de controle de versão do banco de dados do Azure para MySQL, consulte [este documento](concepts-version-policy.md).
- Para obter informações sobre cotas e limitações de recursos específicas com base em sua **camada de serviço**, consulte [camadas de serviço](./concepts-pricing-tiers.md)