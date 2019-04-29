---
title: SQL Data Warehouse do Azure - arquitetura MPP| Microsoft Docs
description: Saiba como o SQL Data Warehouse do Azure combina o processamento paralelo em massa (MPP) com o armazenamento do Azure para obter escalabilidade e alto desempenho.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 0c2ad7e5a707c20db2773324e8047eedaad1a48b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61076926"
---
# <a name="azure-sql-data-warehouse---massively-parallel-processing-mpp-architecture"></a>SQL Data Warehouse do Azure – arquitetura MPP (processamento paralelo massivo)
Saiba como o SQL Data Warehouse do Azure combina o processamento paralelo em massa (MPP) com o armazenamento do Azure para obter escalabilidade e alto desempenho. 

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="mpp-architecture-components"></a>Componentes da arquitetura MPP
O SQL Data Warehouse utiliza uma arquitetura de expansão o processamento computacional dos dados em vários nós de expansão. A unidade de escala é uma abstração de poder de computação que é conhecida como [ unidade de data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md). O SQL Data Warehouse separa computação de armazenamento, permitindo a você fazer escala de computação independentemente dos dados em seu sistema.

![Arquitetura do SQL Data Warehouse](media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

O SQL Data Warehouse usa uma arquitetura baseada em nó. Aplicativos conectam e emitem comandos T-SQL para um nó de Controle, que é o ponto único de entrada para o data warehouse. O nó de Controle executa o mecanismo MPP, que otimiza consultas para processamento paralelo e, em seguida, passa as operações para nós de computação para realizar seu trabalho em paralelo. Os nós de Computação armazenam todos os dados de usuário no Armazenamento do Azure e executam as consultas paralelas. O serviço de movimentação de dados (DMS) é um serviço de nível de sistema interno que move dados entre os nós, conforme necessário para executar consultas em paralelo e retornar resultados precisos. 

Com computação e armazenamento separado, o SQL Data Warehouse pode:

* Utilize computação independentemente do tamanho de suas necessidades de armazenamento.
* Aumentar ou reduzir o poder da computação sem mover dados.
* Pause a capacidade de computação ao deixar dados intactos para que você só pague pelo armazenamento.
* Retomar a capacidade de computação durante horas operacionais.

### <a name="azure-storage"></a>Armazenamento do Azure
O SQL Data Warehouse usa o armazenamento do Azure para proteger seus dados de usuário.  Desde que seus dados estejam armazenados e gerenciados pelo armazenamento do Azure, o SQL Data Warehouse cobra separadamente pelo consumo de armazenamento. Os dados em si são fragmentados em **distribuições** para otimizar o desempenho do sistema. Você pode escolher qual padrão de fragmentação usar para distribuir os dados quando você define a tabela. O SQL Data Warehouse dá suporte a esses padrões de fragmentação:

* Hash
* Round Robin
* Replicar

### <a name="control-node"></a>Nó de controle

O nó de controle é o cérebro do data warehouse. É o front-end que interage com todos os aplicativos e conexões. O mecanismo MPP é executado no nó de controle para otimizar e coordenar consultas paralelas. Quando você envia uma consulta T-SQL ao SQL Data Warehouse, o nó de Controle a transforma em consultas que são executadas em cada distribuição paralelamente.

### <a name="compute-nodes"></a>Nós de computação

Os nós de computação fornecem capacidade de computação. Distribuições são mapeados para nós de computação para processamento. Conforme você paga para obter mais recursos de computação, o SQL Data Warehouse mapeia novamente as distribuições a nós de computação disponíveis. O número de intervalos de nós de 1 a 60 de computação e é determinado pelo nível de serviço para o data warehouse.

Cada nó de computação tem uma ID de nó que está visível nas exibições do sistema. Você pode ver a ID do nó de Computação olhando para a coluna node_id nas exibições do sistema cujos nomes começam com sys.pdw_nodes. Para obter uma lista das exibições de sistema, consulte [Exibição do sistema MPP](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=aps-pdw-2016-au7).

### <a name="data-movement-service"></a>Serviço de movimentação de dados
O Serviço de movimentação de dados (DMS) é a tecnologia de transporte de dados que coordena a movimentação de dados entre os nós de computação. Algumas consultas exigem a movimentação de dados para garantir que as consultas paralelas retornem resultados precisos. Quando a movimentação de dados é necessária, DMS garante que os dados corretos cheguem ao local correto. 

## <a name="distributions"></a>Distribuições

Uma distribuição é a unidade básica de armazenamento e processamento para consultas paralelas que são executadas em dados distribuídos. Quando o SQL Data Warehouse executa uma consulta, o trabalho é dividido em 60 consultas menores que são executadas em paralelo. Cada uma das 60 consultas menores é executada em uma das distribuições de dados. Cada nó de computação gerencia um ou mais de 60 distribuições. Um data warehouse com recursos de computação máximos tem uma distribuição por nó de computação. Um data warehouse com recursos de computação máximos tem todas as distribuições em um nó de computação.  

## <a name="hash-distributed-tables"></a>Tabelas distribuídas em hash
Uma tabela de hash distribuída pode fornecer o melhor desempenho de consulta para junções e agregações em tabelas grandes. 

Para fragmentar dados em uma tabela distribuída por hash, o SQL Data Warehouse usa uma função de hash para atribuir de forma determinada cada linha a uma distribuição. Na definição de tabela, uma das colunas é designada como a coluna de distribuição. A função de hash usa valores na coluna de distribuição para atribuir cada linha a uma distribuição.

O diagrama a seguir ilustra como uma (tabela não distribuída) completa é armazenada como uma tabela distribuída em hash. 

![Tabela distribuída](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "Tabela distribuída")  

* Cada linha pertence a uma distribuição.  
* Um algoritmo de hash determinístico atribui cada linha a uma distribuição.  
* O número de linhas de tabela por distribuição varia conforme mostrado pelos diferentes tamanhos de tabelas.

Há considerações de desempenho para a seleção de uma coluna de distribuição, como distinção, distorção de dados e tipos de consultas executadas no sistema.

## <a name="round-robin-distributed-tables"></a>Tabelas distribuídas round robin
Uma tabela de round-robin é a tabela mais simples para criar e oferece um desempenho rápido quando usada como uma tabela de preparo para cargas.

Uma tabela distribuída round-robin distribui dados uniformemente entre a tabela, mas sem qualquer otimização adicional. Uma distribuição é escolhida primeiramente de forma aleatória e, em seguida, buffers de linhas são atribuídos a distribuições em sequência. É rápido carregar dados em uma tabela de round-robin, mas o desempenho da consulta geralmente pode ser melhor com tabelas de hash distribuídas. Junções de tabelas de round-robin exigem embaralhando dados e isso leva tempo adicional.


## <a name="replicated-tables"></a>Tabelas replicadas
Uma tabela replicada fornece o melhor desempenho de consulta para tabelas pequenas.

Uma tabela replicada faz cache de uma cópia completa da tabela em cada nó de computação. Consequentemente, replicar uma tabela elimina a necessidade de transferir dados entre nós de Computação antes de uma junção ou agregação. Tabelas replicadas são melhor usadas com tabelas pequenas. Armazenamento extra é necessário e não há sobrecarga adicional é incorrida durante a gravação de dados que tornam impraticável a tabelas grandes.  

O diagrama a seguir mostra uma tabela replicada. Para SQL Data Warehouse, a tabela replicada tem cache realizada na primeira distribuição em cada nó de computação.  

![Tabela replicada](media/sql-data-warehouse-distributed-data/replicated-table.png "Tabela replicada") 

## <a name="next-steps"></a>Próximas etapas
Agora que você sabe um pouco sobre o SQL Data Warehouse, saiba como [criar um SQL Data Warehouse][create a SQL Data Warehouse] e [carregar dados de amostra][load sample data] rapidamente. Se você ainda não conhece o Azure, poderá achar o [Glossário do Azure][Azure glossary] útil à medida que encontrar nova terminologia. Ou, dê uma olhada em alguns desses outros Recursos do SQL Data Warehouse.  

* [Histórias de sucesso de clientes]
* [Blogs]
* [Solicitações de recursos]
* [Vídeos]
* [Blogs da Equipe Consultoria para Clientes]
* [Criar um tíquete de suporte]
* [Fórum do MSDN]
* [Fórum Stack Overflow]
* [Twitter]

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Criar um tíquete de suporte]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Histórias de sucesso de clientes]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogs da Equipe Consultoria para Clientes]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Solicitações de recursos]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum do MSDN]: https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse
[Fórum Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
