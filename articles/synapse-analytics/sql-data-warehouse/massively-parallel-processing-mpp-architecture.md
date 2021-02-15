---
title: Arquitetura dedicada do pool SQL (anteriormente conhecido como SQL DW)
description: Saiba como o pool do SQL dedicado (anteriormente conhecido como SQL DW) no Azure Synapse Analytics combina recursos de processamento de consultas distribuídas com o armazenamento do Azure para obter alto desempenho e escalabilidade.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 0e87451531750e502f67dc30e6fbd26c8c944d22
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678586"
---
# <a name="dedicated-sql-pool-formerly-sql-dw-architecture-in-azure-synapse-analytics"></a>Arquitetura dedicada do pool SQL (anteriormente conhecido como SQL DW) no Azure Synapse Analytics

O Azure Synapse Analytics é um serviço de análise que reúne data warehouse corporativo e análise de Big Data. Ele oferece a você a liberdade de consultar dados de acordo com seus termos.

> [!NOTE]
>Explore a [documentação do Azure Synapse Analytics](../overview-what-is.md).
>


> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="synapse-sql-architecture-components"></a>Componentes de arquitetura do SQL do Synapse

O [pool dedicado do SQL (anteriormente conhecido como SQL DW)](sql-data-warehouse-overview-what-is.md) aproveita uma arquitetura de expansão para distribuir o processamento computacional de dados em vários nós. A unidade de escala é uma abstração de poder de computação que é conhecida como [ unidade de data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md). A computação é separada do armazenamento, o que permite que você dimensione a computação independentemente dos dados em seu sistema.

![Arquitetura dedicada do pool SQL (anteriormente conhecido como SQL DW)](./media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

O pool dedicado do SQL (anteriormente conhecido como SQL DW) usa uma arquitetura baseada em nó. Os aplicativos conectam e emitem comandos T-SQL para um nó de controle. O nó de controle hospeda o mecanismo de consulta distribuída, que otimiza as consultas para processamento paralelo e, em seguida, passa as operações para nós de computação para fazer seu trabalho em paralelo.

Os nós de Computação armazenam todos os dados de usuário no Armazenamento do Azure e executam as consultas paralelas. O serviço de movimentação de dados (DMS) é um serviço de nível de sistema interno que move dados entre os nós, conforme necessário para executar consultas em paralelo e retornar resultados precisos.

Com o armazenamento e a computação separados, ao usar um pool SQL dedicado (anteriormente conhecido como SQL DW), é possível:

- Utilize computação independentemente do tamanho de suas necessidades de armazenamento.
- Aumente ou reduza a potência de computação em um pool SQL dedicado (anteriormente conhecido como SQL DW), sem mover os dados.
- Pause a capacidade de computação ao deixar dados intactos para que você só pague pelo armazenamento.
- Retomar a capacidade de computação durante horas operacionais.

### <a name="azure-storage"></a>Armazenamento do Azure

O SQL do pool dedicado SQL (anteriormente conhecido como SQL DW) aproveita o armazenamento do Azure para manter os dados do usuário seguros.  Como os dados são armazenados e gerenciados pelo armazenamento do Azure, é gerada uma cobrança separada para o consumo de armazenamento. Os dados em si são fragmentados em **distribuições**, para otimizar o desempenho do sistema. Você pode escolher qual padrão de fragmentação usar para distribuir os dados quando você define a tabela. Esses padrões de fragmentação são compatíveis com:

- Hash
- Round Robin
- Replicar

### <a name="control-node"></a>Nó de controle

O nó de controle é o cérebro da arquitetura. É o front-end que interage com todos os aplicativos e conexões. O mecanismo de consulta distribuída é executado no nó de controle para otimizar e coordenar consultas paralelas. Quando você envia uma consulta T-SQL, o nó de Controle a transforma em consultas que são executadas em cada distribuição paralelamente.

### <a name="compute-nodes"></a>Nós de computação

Os nós de computação fornecem capacidade de computação. Distribuições são mapeados para nós de computação para processamento. À medida que você paga por mais recursos de computação, as distribuições são remapeadas para os nós de computação disponíveis. O número de intervalos de nós de 1 a 60 de computação e é determinado pelo nível de serviço para o SQL do Synapse.

Cada nó de computação tem uma ID de nó que está visível nas exibições do sistema. Você pode ver a ID do nó de Computação olhando para a coluna node_id nas exibições do sistema cujos nomes começam com sys.pdw_nodes. Para obter uma lista dessas exibições do sistema, consulte [exibições do sistema SQL do Synapse](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

### <a name="data-movement-service"></a>Serviço de movimentação de dados

O Serviço de movimentação de dados (DMS) é a tecnologia de transporte de dados que coordena a movimentação de dados entre os nós de computação. Algumas consultas exigem a movimentação de dados para garantir que as consultas paralelas retornem resultados precisos. Quando a movimentação de dados é necessária, DMS garante que os dados corretos cheguem ao local correto.

## <a name="distributions"></a>Distribuições

Uma distribuição é a unidade básica de armazenamento e processamento para consultas paralelas que são executadas em dados distribuídos. Quando SQL do Synapse executa uma consulta, o trabalho é dividido em 60 consultas menores que são executadas em paralelo.

Cada uma das 60 consultas menores é executada em uma das distribuições de dados. Cada nó de computação gerencia um ou mais de 60 distribuições. Um pool SQL dedicado (anteriormente conhecido como SQL DW) com recursos de computação máximos tem uma distribuição por nó de computação. Um pool SQL dedicado (anteriormente conhecido como SQL DW) com recursos de computação mínimos tem todas as distribuições em um nó de computação.  

## <a name="hash-distributed-tables"></a>Tabelas distribuídas em hash

Uma tabela de hash distribuída pode fornecer o melhor desempenho de consulta para junções e agregações em tabelas grandes.

Para fragmentar dados em uma tabela distribuída por hash, uma função de hash é usada para atribuir de forma determinística cada linha a uma distribuição. Na definição de tabela, uma das colunas é designada como a coluna de distribuição. A função de hash usa valores na coluna de distribuição para atribuir cada linha a uma distribuição.

O diagrama a seguir ilustra como uma (tabela não distribuída) completa é armazenada como uma tabela distribuída em hash.

![Tabela distribuída](./media/massively-parallel-processing-mpp-architecture/hash-distributed-table.png "Tabela distribuída")  

- Cada linha pertence a uma distribuição.  
- Um algoritmo de hash determinístico atribui cada linha a uma distribuição.  
- O número de linhas de tabela por distribuição varia conforme mostrado pelos diferentes tamanhos de tabelas.

Há considerações de desempenho para a seleção de uma coluna de distribuição, como distinção, distorção de dados e tipos de consultas executadas no sistema.

## <a name="round-robin-distributed-tables"></a>Tabelas distribuídas round robin

Uma tabela de round-robin é a tabela mais simples para criar e oferece um desempenho rápido quando usada como uma tabela de preparo para cargas.

Uma tabela distribuída round-robin distribui dados uniformemente entre a tabela, mas sem qualquer otimização adicional. Uma distribuição é escolhida primeiramente de forma aleatória e, em seguida, buffers de linhas são atribuídos a distribuições em sequência. É rápido carregar dados em uma tabela de round-robin, mas o desempenho da consulta geralmente pode ser melhor com tabelas de hash distribuídas. Junções de tabelas de round-robin exigem nova recombinação de dados, e isso leva tempo adicional.

## <a name="replicated-tables"></a>Tabelas replicadas

Uma tabela replicada fornece o melhor desempenho de consulta para tabelas pequenas.

Uma tabela replicada faz cache de uma cópia completa da tabela em cada nó de computação. Consequentemente, replicar uma tabela elimina a necessidade de transferir dados entre nós de Computação antes de uma junção ou agregação. Tabelas replicadas são melhor usadas com tabelas pequenas. Armazenamento adicional é necessário e há custos adicionais ao gravar dados que tornam grandes tabelas inexequíveis.  

O diagrama a seguir mostra uma tabela replicada armazenada em cache na primeira distribuição em cada nó de computação.  

![Tabela replicada](./media/massively-parallel-processing-mpp-architecture/replicated-table.png "Tabela replicada")

## <a name="next-steps"></a>Próximas etapas

Agora que você já sabe um pouco sobre o Azure Synapse, saiba como [criar rapidamente um pool SQL dedicado (anteriormente conhecido como SQL DW)](create-data-warehouse-portal.md) e [carregar dados de exemplo](./load-data-from-azure-blob-storage-using-copy.md). Se você for novo no Azure, você pode encontrar o [Glossário do Azure](../../azure-glossary-cloud-terminology.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) úteis à medida que encontrar nova terminologia. Ou, dê uma olhada em alguns desses outros recursos do Azure Synapse.  

- [Histórias de sucesso de clientes](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Blogs](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [Solicitações de recursos](https://feedback.azure.com/forums/307516-sql-data-warehouse)
- [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [Criar um tíquete de suporte](sql-data-warehouse-get-started-create-support-ticket.md)
- [Página de perguntas e respostas da Microsoft](/answers/topics/azure-synapse-analytics.html)
- [Fórum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)