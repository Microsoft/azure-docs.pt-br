---
title: "Criando bancos de dados de nuvem escalonáveis | Microsoft Docs"
description: "Crie aplicativos de banco de dados .NET escalonáveis com a biblioteca de cliente do banco de dados elástico"
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: 
ms.assetid: 1f11c52d-13c1-4994-b9b1-5b1ae2f9255f
ms.service: sql-database
ms.custom: scale out apps
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: ddove
ms.openlocfilehash: 0128b333f04847ab646dcb0759fcef5f7e86ffd9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="building-scalable-cloud-databases"></a>Criando bancos de dados de nuvem escalonáveis
A escala horizontal dos bancos de dados pode ser feita facilmente usando recursos e ferramentas escalonáveis do Banco de Dados SQL do Azure. Em particular, você pode usar a **Biblioteca de cliente do banco de dados elástico** para criar e gerenciar bancos de dados escalonados. Esse recurso permite que você desenvolva facilmente aplicativos fragmentados usando centenas — ou até mesmo milhares - de bancos de dados SQL do Azure. [Trabalhos elásticos](sql-database-elastic-jobs-powershell.md) podem ser usados para ajudar a facilitar o gerenciamento desses bancos de dados.

Para instalar a biblioteca, acesse [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). 

## <a name="documentation"></a>Documentação
1. [Introdução às ferramentas do Banco de Dados Elástico](sql-database-elastic-scale-get-started.md)
2. [Recursos do Banco de Dados Elástico](sql-database-elastic-scale-introduction.md)
3. [Gerenciamento de mapa de fragmentos](sql-database-elastic-scale-shard-map-management.md)
4. [Migrar bancos de dados existentes para escala horizontal](sql-database-elastic-convert-to-use-elastic-tools.md)
5. [Roteamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md)
6. [Consultas com vários fragmentos](sql-database-elastic-scale-multishard-querying.md)
7. [Adicionando um fragmento usando ferramentas do Banco de Dados Elástico](sql-database-elastic-scale-add-a-shard.md)
8. [Aplicativos multilocatários com ferramentas de banco de dados elástico e segurança em nível de linha](sql-database-elastic-tools-multi-tenant-row-level-security.md)
9. [Atualizar aplicativos de biblioteca de cliente](sql-database-elastic-scale-upgrade-client-library.md) 
10. [Visão geral de consultas elásticas](sql-database-elastic-query-overview.md)
11. [Glossário de ferramentas de banco de dados elástico](sql-database-elastic-scale-glossary.md)
12. [Biblioteca cliente do Banco de Dados Elástico com Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
13. [Biblioteca de cliente de banco de dados elástico com Dapper](sql-database-elastic-scale-working-with-dapper.md)
14. [Ferramenta de mesclagem/divisão](sql-database-elastic-scale-overview-split-and-merge.md)
15. [Contadores de desempenho do gerenciador de mapa de fragmentos](sql-database-elastic-database-client-library.md) 
16. [Perguntas frequentes sobre ferramentas de banco de dados elástico](sql-database-elastic-scale-faq.md)

## <a name="client-capabilities"></a>Recursos do cliente
Escalar horizontalmente aplicativos usando a *fragmentação* apresenta desafios para o desenvolvedor e para o administrador. A biblioteca de cliente simplifica as tarefas de gerenciamento, fornecendo ferramentas que permitem que desenvolvedores e administradores gerenciam bancos de dados escalados. Em um exemplo típico, há muitos bancos de dados, conhecidos como "fragmentos", para gerenciar. Os clientes são localizados no mesmo banco de dados e há um banco de dados por cliente (um esquema de locatário único). A biblioteca de cliente inclui estes recursos:

- **Gerenciamento de mapa de fragmentos**: um banco de dados especial chamado de "gerenciador de mapa de fragmentos" é criado. O gerenciamento de mapa de fragmentos é a capacidade de um aplicativo de gerenciar metadados sobre seus fragmentos. Os desenvolvedores podem usar essa funcionalidade para registrar bancos de dados como fragmentos, descrever os mapeamentos de chaves de fragmentação individuais ou intervalos de chaves para os bancos de dados e manter esses metadados como o número e a composição de bancos de dados que evolui para refletir as alterações de capacidade. Sem a biblioteca de cliente do banco de dados elástico, você precisaria gastar muito tempo escrevendo o código de gerenciamento ao implementar a fragmentação. Para obter mais detalhes, consulte [Gerenciamento de mapa do fragmento](sql-database-elastic-scale-shard-map-management.md).

- **Roteamento dependente de dados**: imagine uma solicitação chegando ao aplicativo. Com base no valor da chave de fragmentação da solicitação, o aplicativo precisa determinar o banco de dados correto com base no valor da chave. Em seguida, ele abre uma conexão com o banco de dados para processar a solicitação. Roteamento dependente de dados fornece a capacidade de abrir conexões com uma única chamada simples para o mapa do fragmento do aplicativo. O roteamento dependente de dados era outra área do código de infraestrutura que agora é coberta por uma funcionalidade na biblioteca de cliente de banco de dados elástico. Para obter mais detalhes, consulte o [Roteamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md).
- **MSQ (Consulta de vários fragmentos)**: a consulta de vários fragmentos funciona quando uma solicitação envolve vários fragmentos, ou todos eles. Uma consulta de vários fragmento executa o mesmo código T-SQL em todos os fragmentos ou um conjunto de fragmentos. Os resultados de fragmentos de participantes são mesclados em um resultado geral definido usando a semântica UNION ALL. A funcionalidade é exposta por meio da biblioteca cliente que trata muitas tarefas, incluindo: gerenciamento de conexões, gerenciamento de threads, tratamento de falhas e os processamento de resultados intermediários. MSQ pode consultar até centenas de fragmentos. Para obter detalhes, veja [Consulta de vários fragmentos](sql-database-elastic-scale-multishard-querying.md).

Em geral, os clientes que usam as ferramentas de banco de dados elástico podem esperar obter a funcionalidade completa do T-SQL durante o envio de operações de fragmento local em vez de operações entre fragmentos que têm suas próprias semânticas.

## <a name="next-steps"></a>Próximas etapas
Experimente o [aplicativo de exemplo](sql-database-elastic-scale-get-started.md) , que demonstra as funções do cliente. 

Para instalar a biblioteca, vá para a [Biblioteca do cliente do banco de dados elástico](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

Para obter instruções sobre como usar a ferramenta de mesclagem/divisão, confira a [visão geral da ferramenta de mesclagem/divisão](sql-database-elastic-scale-overview-split-and-merge.md).

[A biblioteca de cliente do banco de dados elástico agora é tem código aberto!](https://azure.microsoft.com/blog/elastic-database-client-library-is-now-open-sourced/)

Use as [Consultas elásticas](sql-database-elastic-query-overview.md).

A biblioteca agora está disponível como software livre no [GitHub](https://github.com/Azure/elastic-db-tools). 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-database-client-library/glossary.png

