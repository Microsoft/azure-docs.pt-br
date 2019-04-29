---
title: Visão geral de desenvolvimento de aplicativo de Banco de Dados SQL | Microsoft Docs
description: Saiba mais sobre bibliotecas de conectividade disponíveis e práticas recomendadas para aplicativos que se conectam ao Banco de Dados SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: efb6d932e616ada6b8dfff637af469c16fc2f293
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60723401"
---
# <a name="sql-database-application-development-overview"></a>Visão geral do desenvolvimento de aplicativos de Banco de Dados SQL

Este artigo apresenta as considerações básicas sobre as quais um desenvolvedor deve estar ciente ao escrever código para se conectar ao Banco de Dados SQL do Azure. Este artigo se aplica a todos os modelos de implantação do Banco de Dados SQL do Azure (banco de dados individual, pools elásticos, instância gerenciada).

> [!TIP]
> Examine os guias de introdução de [bancos de dados individuais](sql-database-single-database-quickstart-guide.md) e [instâncias gerenciadas](sql-database-managed-instance-quickstart-guide.md) se precisar configurar o Banco de Dados SQL do Azure.
>

## <a name="language-and-platform"></a>Linguagem e plataforma

Você pode usar várias [plataformas e linguagens de programação](sql-database-connect-query.md) para se conectar ao Banco de Dados SQL do Azure e consultá-lo. Você pode encontrar [aplicativos de exemplo](https://azure.microsoft.com/resources/samples/?service=sql-database&sort=0) que pode usar para se conectar ao Banco de Dados SQL do Azure.

Você pode aproveitar as ferramentas de software livre, como [cheetah](https://github.com/wunderlist/cheetah), [sql-cli](https://www.npmjs.com/package/sql-cli), [Código VS](https://code.visualstudio.com/). Além disso, o Banco de Dados SQL do Azure funciona com ferramentas da Microsoft, como [Visual Studio](https://www.visualstudio.com/downloads/) e [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx). Você também pode usar o portal do Azure, o PowerShell e APIs REST para obter maior produtividade.

## <a name="authentication"></a>Authentication

O acesso ao Banco de Dados SQL do Azure é protegido por logons e firewalls. O Banco de Dados SQL do Azure dá suporte a usuários e logons da [autenticação do AAD (Azure Active Directory)](sql-database-aad-authentication.md) e do SQL Server. Os logons do AAD estão disponíveis apenas na Instância Gerenciada. 

Saiba mais sobre [como gerenciar o acesso e o logon em banco de dados](sql-database-manage-logins.md).

## <a name="connections"></a>conexões

Em sua lógica de conexão de cliente, substitua o tempo limite padrão para ser 30 segundos. O padrão de 15 segundos é muito curto para conexões que dependem da Internet.

Se você estiver usando um [pool de conexões](https://msdn.microsoft.com/library/8xx3tyca.aspx), feche a conexão no instante em que o programa não a estiver utilizando ativamente e não estiver se preparando para reutilizá-la.

Evite transações de longa execução, pois qualquer falha de infraestrutura ou conexão pode reverter a transação. Se possível, divida a transação em várias transações menores e use [envio em lote para melhorar o desempenho](sql-database-use-batching-to-improve-performance.md).

## <a name="resiliency"></a>Resiliência

O Banco de Dados SQL do Azure é um serviço de nuvem, no qual é possível esperar erros transitórios que ocorrem na infraestrutura subjacente ou na comunicação entre entidades de nuvem. Embora o Banco de Dados SQL do Azure seja resiliente em falhas de infraestrutura transitórias, essas falhas podem afetar a conectividade. Quando ocorre um erro transitório ao se conectar ao Banco de Dados SQL, seu código deverá [repetir a chamada](sql-database-connectivity-issues.md). Recomendamos que a lógica de repetição use a lógica de retirada, de modo que ela não sobrecarregue o Banco de Dados SQL com vários clientes realizando novas tentativas ao mesmo tempo. A lógica de repetição depende das [mensagens de erro para programas cliente do Banco de Dados SQL](sql-database-develop-error-messages.md).

Para obter mais informações sobre como se preparar para eventos de manutenção planejada em seu banco de dados SQL do Azure, consulte [planejamento de eventos de manutenção do Azure no Banco de Dados SQL do Azure](sql-database-planned-maintenance.md).

## <a name="network-considerations"></a>Considerações sobre rede

- No computador que hospeda o programa cliente, certifique-se de que o firewall permite comunicação TCP de saída na porta 1433.  Mais informações: [Configurar um firewall do Banco de Dados SQL do Azure](sql-database-configure-firewall-settings.md).
- Se o programa cliente se conectar ao Banco de Dados SQL enquanto seu cliente estiver em execução em uma VM (máquina virtual) do Azure, será necessário abrir determinados intervalos de porta na VM. Mais informações: [Portas acima da 1433 para o ADO.NET 4.5 e o Banco de Dados SQL](sql-database-develop-direct-route-ports-adonet-v12.md).
- Às vezes, as conexões de cliente para o Banco de Dados SQL do Azure ignoram o proxy e interagem diretamente com o banco de dados. Outras portas diferentes da 1433 se tornam importantes. Para obter mais informações, [Arquitetura de conectividade de Banco de Dados SQL do Azure](sql-database-connectivity-architecture.md) e [Portas além da 1433 para ADO.NET 4.5 e Banco de Dados SQL](sql-database-develop-direct-route-ports-adonet-v12.md).
- Para configuração de rede para uma instância gerenciada, consulte [configuração de rede para instâncias gerenciadas](sql-database-howto-managed-instance.md#network-configuration).

## <a name="next-steps"></a>Próximas etapas

Explore todas as [funcionalidades do Banco de Dados SQL](sql-database-technical-overview.md).
