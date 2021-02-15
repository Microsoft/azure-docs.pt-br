---
title: Práticas recomendadas de desempenho e diretrizes de configuração-Azure SQL Edge
description: Saiba mais sobre práticas recomendadas de desempenho e diretrizes de configuração no Azure SQL Edge
keywords: Borda do SQL, retenção de dados
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 02f22883a0989714d8b74f778cacf1ba2c65d0b4
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392003"
---
# <a name="performance-best-practices-and-configuration-guidelines"></a>Práticas recomendadas de desempenho e diretrizes de configuração

O Azure SQL Edge oferece vários recursos e funcionalidades que podem ser usados para melhorar o desempenho da implantação do SQL Edge. Este artigo fornece algumas práticas recomendadas e recomendações para maximizar o desempenho. 

## <a name="best-practices"></a>Práticas recomendadas 

### <a name="configure-multiple-tempdb-data-files"></a>Configurar vários arquivos de dados tempdb

O Azure SQL Edge, por padrão, cria apenas um arquivo de dados tempdb como parte da inicialização do contêiner. Recomendamos que você considere a criação de vários arquivos de dados tempdb após a implantação. Para obter mais informações, confira as diretrizes no artigo [Recomendações para reduzir a contenção de alocação no banco de dados tempdb do SQL Server](https://support.microsoft.com/help/2154845/recommendations-to-reduce-allocation-contention-in-sql-server-tempdb-d).

### <a name="use-clustered-columnstore-indexes-where-possible"></a>Usar índices columnstore clusterizados sempre que possível

Os dispositivos IoT e Edge tendem a gerar um alto volume de dados que normalmente são agregados em uma janela de tempo para análise. As linhas de dados individuais raramente são usadas para qualquer análise. Os índices Columnstore são ideais para armazenar e consultar esses conjuntos de grandes volumes. Esse índice usa o armazenamento de dados baseado em coluna e o processamento de consulta para obter ganhos de até 10 vezes o desempenho da consulta em relação ao armazenamento tradicional orientado por linha. Também é possível obter ganhos de até 10 vezes na compactação de dados sobre o tamanho dos dados descompactados. Para obter mais informações, consulte [índices Columnstore](/sql/relational-databases/indexes/columnstore-indexes-overview)

Além disso, outros recursos do Azure SQL Edge, como o streaming de dados e a retenção de dados, se beneficiam das otimizações de columnstore em relação à inserção de dados e à remoção 

### <a name="simple-recovery-model"></a>Modelo de recuperação simples

Como o armazenamento pode ser restrito em dispositivos de borda, todos os bancos de dados de usuário no Azure SQL Edge usam o modelo de recuperação simples por padrão. O modelo de recuperação simples recupera automaticamente o espaço de log para manter os requisitos de espaço pequenos, eliminando, essencialmente, a necessidade de gerenciar o espaço do log de transações. Em dispositivos de borda com armazenamento limitado disponível, isso pode ser útil. Para obter mais informações sobre o modelo de recuperação simples e outros modelos de recuperação disponíveis, consulte [modelos de recuperação](/sql/relational-databases/backup-restore/recovery-models-sql-server)

Operações como envio de logs e restaurações point-in-time, que exigem backups de log de transações, não são compatíveis com o modelo de recuperação simples.  

## <a name="advanced-configuration"></a>Configuração avançada 

### <a name="setting-memory-limits"></a>Definindo limites de memória

O Azure SQL Edge dá suporte a até 64 GB de memória para o pool de buffers, enquanto a memória adicional pode ser exigida por processos de satélite em execução no contêiner do SQL Edge. Em dispositivos de borda menores com memória limitada, é aconselhável limitar a memória disponível para contêineres do SQL Edge, de modo que o host do Docker e outros processos ou módulos de borda possam funcionar corretamente. A memória total disponível para o SQL Edge pode ser controlada usando os mecanismos a seguir. 

- Limitando a memória disponível para os contêineres do SQL Edge: a memória total disponível para o contêiner do SQL Edge pode ser limitada usando a opção de configuração de tempo de execução do contêiner `--memory` . Para obter mais informações sobre como limitar a memória disponível para o contêiner do SQL Edge, consulte [Opções de tempo de execução com memória, CPUs e GPUs](https://docs.docker.com/config/containers/resource_constraints/).

- Limitando a memória disponível para o processo SQL no contêiner: por padrão, o processo SQL dentro do contêiner usa apenas 80% da RAM física disponível. Para a maioria das implantações, a configuração padrão será boa. No entanto, pode haver cenários em que a memória adicional pode ser necessária para o streaming de dados e os processos ONNX em execução dentro dos contêineres do SQL Edge. Nesses cenários, a memória disponível para o processo SQL pode ser controlada usando a `memory.memorylimitmb` configuração no arquivo MSSQL-conf. Para obter mais informações, consulte [Configurar usando o arquivo MSSQL. conf](configure.md#configure-by-using-an-mssqlconf-file).

Ao definir os limites de memória, tenha cuidado para não definir esse valor muito baixo. Se você não definir memória suficiente para o processo SQL, ele poderá afetar seriamente o desempenho do SQL.

### <a name="delayed-durability"></a>Durabilidade atrasada

As transações no Azure SQL Edge podem ser totalmente duráveis, o SQL Server padrão ou um durável atrasado (também conhecido como confirmação lenta).

As confirmações de transações totalmente duráveis são síncronas e relatam uma confirmação como bem-sucedida, devolvendo o controle ao cliente somente após a gravação dos registros de log da transação em disco. As confirmações de transações duráveis atrasadas são assíncronas e relatam uma confirmação como bem-sucedida antes que os registros de log da transação sejam gravados no disco. É necessário gravar as entradas de log das transações em disco para que uma transação seja durável. As transações duráveis atrasadas tornam-se duráveis quando as entradas de log de transações são liberadas para o disco. 

Em implantações em que **alguma perda de dados** pode ser tolerada ou em dispositivos de borda com armazenamento lento, a durabilidade atrasada pode ser usada para otimizar a ingestão de dados e a limpeza baseada em retenção de dados. Para obter mais informações, veja [Controlar a durabilidade da transação](/sql/relational-databases/logs/control-transaction-durability).


### <a name="linux-os-configurations"></a>Configurações do SO Linux 

Considere o uso das seguintes definições de [configuração do sistema operacional Linux](/sql/linux/sql-server-linux-performance-best-practices#linux-os-configuration) para experimentar o melhor desempenho para uma instalação do SQL.