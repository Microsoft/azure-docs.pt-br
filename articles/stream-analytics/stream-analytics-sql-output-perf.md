---
title: Saída do Azure Stream Analytics para Banco de Dados SQL do Azure
description: Saiba mais sobre realizar a saída de dados para o SQL Azure do Azure Stream Analytics e alcançar maiores taxas de transferência de gravação.
services: stream-analytics
author: chetanmsft
ms.author: chetang
manager: katiiceva
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/18/2019
ms.openlocfilehash: e0775bff1e7bdeeaf2c544fd815c2ce3bf129eae
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620868"
---
# <a name="azure-stream-analytics-output-to-azure-sql-database"></a>Saída do Azure Stream Analytics para Banco de Dados SQL do Azure

Este artigo discute dicas para obter o melhor desempenho de taxa de transferência de gravação quando você está carregando dados para o Banco de Dados SQL do Azure usando o Azure Stream Analytics.

A saída do SQL no Azure Stream Analytics dá suporte à gravação em paralelo como uma opção. Essa opção permite topologias de trabalho [totalmente paralelas](stream-analytics-parallelization.md#embarrassingly-parallel-jobs), em que várias partições de saída estão gravando na tabela de destino em paralelo. Porém, habilitar essa opção no Azure Stream Analytics pode não ser suficiente para alcançar taxas de transferência maiores, pois isso depende significativamente da sua configuração de banco de dados do SQL Azure e do esquema da tabela. A escolha de índices, chave de clustering, fator de preenchimento de índice e compactação afetam o tempo para carregar as tabelas. Para obter mais informações sobre como otimizar seu banco de dados do SQL Azure para melhorar a consulta e o desempenho de carga com base nos parâmetros de comparação internos, veja [Diretrizes de desempenho do Banco de Dados SQL](../sql-database/sql-database-performance-guidance.md). A ordem das gravações não é garantida ao gravar em paralelo no Banco de Dados SQL do Azure.

Aqui estão algumas configurações dentro de cada serviço que podem ajudar a melhorar a taxa de transferência geral de sua solução.

## <a name="azure-stream-analytics"></a>Stream Analytics do Azure

- **Herdar Particionamento** – essa opção de configuração de saída SQL permite herdar o esquema de particionamento de sua entrada ou etapa de consulta anterior. Com esse recurso habilitado, gravando em uma tabela baseada em disco e tendo uma topologia [totalmente paralela](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) para seu trabalho, espere ver melhores taxas de transferência. Esse particionamento já acontece automaticamente para muitas outras [saídas](stream-analytics-parallelization.md#partitions-in-sources-and-sinks). Bloqueio de tabela (TABLOCK) também será desabilitado para inserções em massa feitas com essa opção.

> [!NOTE] 
> Quando há mais de oito partições de entrada, a herança de entrada de esquema de particionamento pode não ser uma opção apropriada. Esse limite superior foi observado em uma tabela com uma coluna de identidade única e um índice clusterizado. Nesse caso, considere o uso de [INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count) 8 em sua consulta, para especificar explicitamente o número de gravadores de saída. Com base no seu esquema e na escolha de índices, suas observações podem variar.

- **Tamanho do Lote** – a configuração de saída do SQL permite que você especifique o tamanho do lote máximo em uma saída do SQL do Azure Stream Analytics com base na natureza de sua tabela de destino/carga de trabalho. O tamanho do lote é o número máximo de registros enviados com cada transação de inserção em massa. Em índices columnstore clusterizados, tamanhos de lote próximos de [100 mil](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance) permitem mais paralelização, mínimo registro em log e otimizações de bloqueio. Em tabelas baseadas em disco, 10 mil (padrão) ou inferior pode ser ideal para sua solução, uma vez que tamanhos de lote maiores podem disparar escalonamento de bloqueio durante inserções em massa.

- **Ajuste de Mensagem de Entrada** – se você tiver otimizado usando particionamento de herança e tamanho de lote, aumentar o número de eventos de entrada por mensagem por partição ajudará a incrementar ainda mais sua taxa de transferência de gravação. O ajuste de mensagem de entrada permite que os tamanhos de lote do Azure Stream Analytics tenham até o tamanho de lote especificado, melhorando a taxa de transferência. Isso pode ser obtido por meio [compactação](stream-analytics-define-inputs.md) ou aumentando os tamanhos de mensagem de entrada no hub de eventos ou Blob.

## <a name="sql-azure"></a>SQL Azure

- **Índices e Tabela Particionados** – usar uma tabela SQL [particionada](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes?view=sql-server-2017) e índices particionados na tabela com a mesma coluna como chave de partição (por exemplo, PartitionId) pode reduzir significativamente as contenções entre partições durante gravações. Para uma tabela particionada, você precisará criar uma [função de partição](https://docs.microsoft.com/sql/t-sql/statements/create-partition-function-transact-sql?view=sql-server-2017) e um [esquema de partição](https://docs.microsoft.com/sql/t-sql/statements/create-partition-scheme-transact-sql?view=sql-server-2017) no grupo de arquivos PRIMARY. Isso também aumentará a disponibilidade dos dados existentes enquanto novos dados estiverem sendo carregados. O limite de E/S de log pode ser atingido com base no número de partições, que pode ser aumentado atualizando a SKU.

- **Evitar violações de chave exclusivas** – se você receber [várias mensagens de aviso de violação de chave](stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output) no Log de Atividades do Azure Stream Analytics, verifique se seu trabalho não é afetado por violações de restrição exclusivas que têm probabilidade de acontecer durante a casos de recuperação. Isso pode ser evitado configurando a opção [IGNORE\_DUP\_KEY](stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output) em seus índices.

## <a name="azure-data-factory-and-in-memory-tables"></a>Azure Data Factory e tabelas na memória

- **A tabela na memória como tabela temporária** – [tabelas na memória](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) permitir para carregamentos de dados de alta velocidade, mas os dados precisam caber na memória. Parâmetros de comparação mostram que o carregamento em massa de uma tabela na memória para uma tabela baseada em disco é cerca de 10 vezes mais rápido do que inserir diretamente em massa usando um único gravador na tabela baseada em disco com uma coluna de identidade e um índice clusterizado. Para aproveitar esse desempenho de inserção em massa, configure um [trabalho de cópia usando o Azure Data Factory](../data-factory/connector-azure-sql-database.md) que copia dados da tabela na memória para a tabela baseada em disco.

## <a name="avoiding-performance-pitfalls"></a>Como evitar armadilhas de desempenho
Inserindo dados em massa é muito mais rápida do que o carregamento de dados com inserções únicas porque o repetidas é evitada a sobrecarga de transferência de dados, analisar a instrução insert, que executa a instrução e emitir um registro de transação. Em vez disso, um caminho mais eficiente é usado para o mecanismo de armazenamento, transmitir os dados. O custo de instalação desse caminho é porém muito maior do que uma única instrução insert em uma tabela baseada em disco. O ponto de neutralização de custo é normalmente cerca de 100 linhas, além do que em massa Carregando quase sempre é mais eficiente. 

Se a taxa de eventos de entrada for baixa, ele pode criar facilmente tamanhos de lote menor do que 100 linhas, o que torna a inserção em massa ineficiente e usa muito espaço em disco. Para contornar essa limitação, você pode fazer uma destas ações:
* Criar um INSTEAD OF [disparador](/sql/t-sql/statements/create-trigger-transact-sql) usar insert simple para cada linha.
* Use uma tabela temporária de na memória, conforme descrito na seção anterior.

Outro cenário ocorre ao escrever em um índice columnstore não clusterizado (NCCI), em que as inserções de menor em massa podem criar muitos segmentos, que podem causar falha no índice. Nesse caso, a recomendação é usar um índice Columnstore clusterizado em vez disso.

## <a name="summary"></a>Resumo

Em resumo, com o recurso de saída particionada na saída do Azure Stream Analytics para SQL, a paralelização alinhada do seu trabalho com uma tabela particionada no SQL Azure deve fornecer melhorias de produtividade significativas. Aproveitar o Azure Data Factory para orquestrar a movimentação de dados de uma tabela Na Memória para tabelas baseadas em Disco pode fornecer grandes ganhos de taxa de transferência. Se for viável, melhorar a densidade da mensagem também poderá ser um fator importante no aprimoramento da taxa de transferência geral.
