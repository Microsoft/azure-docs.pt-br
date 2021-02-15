---
title: Visão geral do desenvolvimento de aplicativos
description: Saiba mais sobre bibliotecas de conectividade disponíveis e práticas recomendadas para aplicativos que se conectam ao Banco de Dados SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 11/14/2019
ms.custom: sqldbrb=2
ms.openlocfilehash: 7ea3456dbd1d0942cba48c97d70982ae088d73f9
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92782970"
---
# <a name="application-development-overview---sql-database--sql-managed-instance"></a>Visão geral do desenvolvimento de aplicativos-banco de dados SQL & SQL Instância Gerenciada

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Este artigo apresenta as considerações básicas que um desenvolvedor deve conhecer ao escrever código para se conectar ao banco de dados no Azure. Este artigo se aplica ao banco de dados SQL do Azure e ao Azure SQL Instância Gerenciada.

## <a name="language-and-platform"></a>Linguagem e plataforma

Você pode usar várias [plataformas e linguagens de programação](connect-query-content-reference-guide.md) para se conectar ao Banco de Dados SQL do Azure e consultá-lo. Você pode encontrar [aplicativos de exemplo](https://azure.microsoft.com/resources/samples/?service=sql-database&sort=0) que você pode usar para se conectar ao banco de dados.

Você pode aproveitar as ferramentas de software livre, como [cheetah](https://github.com/wunderlist/cheetah), [sql-cli](https://www.npmjs.com/package/sql-cli), [Código VS](https://code.visualstudio.com/). Além disso, o Banco de Dados SQL do Azure funciona com ferramentas da Microsoft, como [Visual Studio](https://www.visualstudio.com/downloads/) e [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms). Você também pode usar o portal do Azure, o PowerShell e APIs REST para obter maior produtividade.

## <a name="authentication"></a>Autenticação

O acesso ao Banco de Dados SQL do Azure é protegido por logons e firewalls. O banco de dados SQL do Azure dá suporte a usuários e logons de autenticação SQL Server e [Azure Active Directory](authentication-aad-overview.md) . Azure Active Directory logons estão disponíveis somente no SQL Instância Gerenciada. 

Saiba mais sobre [como gerenciar o acesso e o logon em banco de dados](logins-create-manage.md).

## <a name="connections"></a>conexões

Em sua lógica de conexão de cliente, substitua o tempo limite padrão para ser 30 segundos. O padrão de 15 segundos é muito curto para conexões que dependem da Internet.

Se você estiver usando um [pool de conexões](/dotnet/framework/data/adonet/sql-server-connection-pooling), feche a conexão no instante em que o programa não a estiver utilizando ativamente e não estiver se preparando para reutilizá-la.

Evite transações de longa execução, pois qualquer falha de infraestrutura ou conexão pode reverter a transação. Se possível, divida a transação em várias transações menores e use [envio em lote para melhorar o desempenho](../performance-improve-use-batching.md).

## <a name="resiliency"></a>Resiliência

O Banco de Dados SQL do Azure é um serviço de nuvem, no qual é possível esperar erros transitórios que ocorrem na infraestrutura subjacente ou na comunicação entre entidades de nuvem. Embora o Banco de Dados SQL do Azure seja resiliente em falhas de infraestrutura transitórias, essas falhas podem afetar a conectividade. Quando ocorre um erro transitório ao se conectar ao Banco de Dados SQL, seu código deverá [repetir a chamada](troubleshoot-common-connectivity-issues.md). Recomendamos que a lógica de repetição use a lógica de retirada, para que ela não sobrecarregue o serviço com vários clientes repetindo simultaneamente. A lógica de repetição depende das [mensagens de erro para programas cliente do Banco de Dados SQL](troubleshoot-common-errors-issues.md).

Para obter mais informações sobre como se preparar para eventos de manutenção planejada em seu banco de dados SQL do Azure, consulte [planejando eventos de manutenção do Azure no banco de dados SQL do Azure](planned-maintenance.md).

## <a name="network-considerations"></a>Considerações de rede

- No computador que hospeda o programa cliente, certifique-se de que o firewall permite comunicação TCP de saída na porta 1433.  Mais informações: [Configurar um firewall do Banco de Dados SQL do Azure](firewall-configure.md).
- Se o programa cliente se conectar ao Banco de Dados SQL enquanto seu cliente estiver em execução em uma VM (máquina virtual) do Azure, será necessário abrir determinados intervalos de porta na VM. Mais informações: [portas além de 1433 para ADO.NET 4,5 e banco de dados SQL](adonet-v12-develop-direct-route-ports.md).
- Às vezes, as conexões de cliente para o Banco de Dados SQL do Azure ignoram o proxy e interagem diretamente com o banco de dados. Outras portas diferentes da 1433 se tornam importantes. Para obter mais informações, [Arquitetura de conectividade de Banco de Dados SQL do Azure](connectivity-architecture.md) e [Portas além da 1433 para ADO.NET 4.5 e Banco de Dados SQL](adonet-v12-develop-direct-route-ports.md).
- Para configuração de rede para uma instância do SQL Instância Gerenciada, consulte [configuração de rede para sql instância gerenciada](../managed-instance/how-to-content-reference-guide.md#network-configuration).

## <a name="next-steps"></a>Próximas etapas

Explore todos os recursos do [banco de dados SQL](sql-database-paas-overview.md) e do [SQL instância gerenciada](../managed-instance/sql-managed-instance-paas-overview.md).

Para começar, consulte os guias para o [banco de dados SQL do Azure](quickstart-content-reference-guide.md) e [instâncias gerenciadas do Azure SQL](../managed-instance/quickstart-content-reference-guide.md).