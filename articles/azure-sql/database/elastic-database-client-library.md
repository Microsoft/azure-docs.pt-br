---
title: Criando bancos de dados de nuvem escalonáveis
description: Crie aplicativos de banco de dados .NET escalonáveis com a biblioteca de cliente do banco de dados elástico.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/25/2018
ms.openlocfilehash: bfe5dc00ba0255520c04ea85157f0b8bdc71b590
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "84038507"
---
# <a name="building-scalable-cloud-databases"></a>Criando bancos de dados de nuvem escalonáveis
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A escala horizontal dos bancos de dados pode ser feita facilmente usando recursos e ferramentas escalonáveis do Banco de Dados SQL do Azure. Em particular, você pode usar a **Biblioteca de cliente do banco de dados elástico** para criar e gerenciar bancos de dados escalonados. Esse recurso permite que você desenvolva facilmente aplicativos fragmentados usando centenas (ou até milhares) de bancos de dados no banco de dados SQL do Azure.

Para baixar:

* A versão do Java da biblioteca, consulte [Repositório Maven Central](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* A versão do .NET da biblioteca, consulte [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="documentation"></a>Documentação

1. [Introdução às ferramentas de banco de dados elástico](elastic-scale-get-started.md)
2. [Recursos do Banco de Dados Elástico](elastic-scale-introduction.md)
3. [Gerenciamento de mapa de fragmentos](elastic-scale-shard-map-management.md)
4. [Migrar bancos de dados existentes para escalar horizontalmente](elastic-convert-to-use-elastic-tools.md)
5. [Roteamento dependente de dados](elastic-scale-data-dependent-routing.md)
6. [Consultas com vários fragmentos](elastic-scale-multishard-querying.md)
7. [Adicionando um fragmento usando ferramentas do Banco de Dados Elástico](elastic-scale-add-a-shard.md)
8. [Aplicativos multilocatários com ferramentas de banco de dados elástico e segurança em nível de linha](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)
9. [Atualizar aplicativos de biblioteca de cliente](elastic-scale-upgrade-client-library.md) 
10. [Visão geral de consultas elásticas](elastic-query-overview.md)
11. [Glossário de ferramentas de banco de dados elástico](elastic-scale-glossary.md)
12. [Biblioteca cliente do Banco de Dados Elástico com Entity Framework](elastic-scale-use-entity-framework-applications-visual-studio.md)
13. [Biblioteca de cliente do banco de dados elástico com Dapper](elastic-scale-working-with-dapper.md)
14. [Ferramenta de mesclagem/divisão](elastic-scale-overview-split-and-merge.md)
15. [Contadores de desempenho do gerenciador de mapa de fragmentos](elastic-database-client-library.md) 
16. [Perguntas frequentes para ferramentas de banco de dados elástico](elastic-scale-faq.md)

## <a name="client-capabilities"></a>Recursos do cliente

Escalar horizontalmente aplicativos usando a *fragmentação* apresenta desafios para o desenvolvedor e para o administrador. A biblioteca de cliente simplifica as tarefas de gerenciamento, fornecendo ferramentas que permitem que desenvolvedores e administradores gerenciam bancos de dados escalados. Em um exemplo típico, há muitos bancos de dados, conhecidos como "fragmentos", para gerenciar. Os clientes são localizados no mesmo banco de dados e há um banco de dados por cliente (um esquema de locatário único). A biblioteca de cliente inclui estes recursos:

- **Gerenciamento de mapa de fragmentos**: um banco de dados especial chamado "Gerenciador de mapa de fragmentos" é criado. O gerenciamento de mapa de fragmentos é a capacidade de um aplicativo de gerenciar metadados sobre seus fragmentos. Os desenvolvedores podem usar essa funcionalidade para registrar bancos de dados como fragmentos, descrever os mapeamentos de chaves de fragmentação individuais ou intervalos de chaves para os bancos de dados e manter esses metadados como o número e a composição de bancos de dados que evolui para refletir as alterações de capacidade. Sem a biblioteca de cliente do banco de dados elástico, você precisaria gastar muito tempo escrevendo o código de gerenciamento ao implementar a fragmentação. Para obter mais detalhes, consulte [Gerenciamento de mapa do fragmento](elastic-scale-shard-map-management.md).

- **Roteamento dependente de dados**: imagine uma solicitação chegando ao aplicativo. Com base no valor da chave de fragmentação da solicitação, o aplicativo precisa determinar o banco de dados correto com base no valor da chave. Em seguida, ele abre uma conexão com o banco de dados para processar a solicitação. Roteamento dependente de dados fornece a capacidade de abrir conexões com uma única chamada simples para o mapa do fragmento do aplicativo. O roteamento dependente de dados era outra área de código de infraestrutura que agora é abordada pela funcionalidade na biblioteca de cliente do banco de dados elástico. Para obter mais detalhes, consulte o [Roteamento dependente de dados](elastic-scale-data-dependent-routing.md).
- **MSQ (Consulta de vários fragmentos)**: a consulta de vários fragmentos funciona quando uma solicitação envolve vários fragmentos, ou todos eles. Uma consulta de vários fragmento executa o mesmo código T-SQL em todos os fragmentos ou um conjunto de fragmentos. Os resultados de fragmentos de participantes são mesclados em um resultado geral definido usando a semântica UNION ALL. A funcionalidade é exposta por meio da biblioteca cliente que trata muitas tarefas, incluindo: gerenciamento de conexões, gerenciamento de threads, tratamento de falhas e os processamentos de resultados intermediários. MSQ pode consultar até centenas de fragmentos. Para obter detalhes, veja [Consulta de vários fragmentos](elastic-scale-multishard-querying.md).

Em geral, os clientes que usam as ferramentas de banco de dados elástico podem esperar obter a funcionalidade completa do T-SQL ao enviar operações de fragmento local em oposição a operações entre fragmentos que têm sua própria semântica.



## <a name="next-steps"></a>Próximas etapas

- Biblioteca de cliente do banco de dados elástico ([Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22), [.net](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)) – para **baixar** a biblioteca.

- [Introdução às ferramentas de banco de dados elástico](elastic-scale-get-started.md) – para experimentar o **aplicativo de exemplo** que demonstra as funções de cliente.

- GitHub ([Java](https://github.com/Microsoft/elastic-db-tools-for-java/blob/master/README.md), [.NET](https://github.com/Azure/elastic-db-tools)) – para fazer contribuições no código.
- [Visão geral da consulta elástica do Banco de Dados SQL do Azure](elastic-query-overview.md) - para usar consultas elásticas.

- [Mover dados entre bancos de dados de nuvem expansíveis](elastic-scale-overview-split-and-merge.md) - para obter instruções sobre como usar o **ferramenta de mesclagem de divisão**.



<!-- Additional resources H2 -->

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]


<!--Anchors-->
<!--Image references-->

[1]: ./media/sql-database-elastic-database-client-library/glossary.png

