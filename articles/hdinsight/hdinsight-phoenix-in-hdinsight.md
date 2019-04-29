---
title: Apache Phoenix no HDInsight - Azure HDInsight
description: ''
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: ashishth
ms.openlocfilehash: 7d9aafeb920eab7f6a87061a135bf2e464add436
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763649"
---
# <a name="apache-phoenix-in-hdinsight"></a>Apache Phoenix no HDInsight

O [Apache Phoenix](https://phoenix.apache.org/) é um banco de dados relacional de software livre altamente paralelo criado no [Apache HBase](hbase/apache-hbase-overview.md). Phoenix permite que você use consultas SQL em HBase. O Phoenix usa drivers JDBC abaixo para permitir aos usuários criar, excluir, alterar tabelas SQL, índices, exibições e as sequências e linhas upsert individualmente e em massa. O Phoenix usa compilação nativa noSQL, em vez de usar o MapReduce para compilar consultas, permitindo a criação de aplicativos de baixa latência sobre HBase. O Phoenix adiciona coprocessadores para oferecer suporte à execução de código fornecido pelo cliente no espaço de endereço do servidor, executando o código colocalizado junto com os dados. Essa abordagem minimiza a transferência de dados do cliente/servidor.

O Apache Phoenix abre consultas de Big Data para não desenvolvedores que podem usar uma sintaxe semelhante ao SQL em vez de programação. O Phoenix é altamente otimizado para o HBase, diferentemente de outras ferramentas, como o [Apache Hive](hadoop/hdinsight-use-hive.md) e o Apache Spark SQL. Os benefícios para os desenvolvedores está gravando altamente consultas de alto desempenho com muito menos código.
<!-- [Spark SQL](spark/apache-spark-sql-with-hdinsight.md)  -->

Quando você envia uma consulta SQL, Phoenix compila a consulta a chamadas nativas HBase e executa a verificação (ou plano) em paralelo para otimização. Essa camada de abstração libera o desenvolvedor de gravar os trabalhos de MapReduce para se concentrar na lógica de negócios e no fluxo de trabalho de seu aplicativo de armazenamento de big data do Phoenix.

## <a name="query-performance-optimization-and-other-features"></a>Otimização de desempenho de consulta e outros recursos

Apache Phoenix adiciona vários recursos e aprimoramentos de desempenho para consultas do HBase.

### <a name="secondary-indexes"></a>Índices secundários

HBase tem um único índice que é lexicograficamente classificado na chave da linha primária. Esses registros só podem ser acessados pela chave da linha. Acessar registros por meio de qualquer coluna que não seja a chave da linha requer a verificação de todos os dados ao aplicar o filtro obrigatório. Em um índice secundário, as colunas ou expressões que são indexadas formam uma chave de linha alternada, permitindo pesquisas e verificações de intervalo nesse índice.

Criar um índice secundário com o comando `CREATE INDEX`:

```sql
CREATE INDEX ix_purchasetype on SALTEDWEBLOGS (purchasetype, transactiondate) INCLUDE (bookname, quantity);
```

Essa abordagem pode produzir um aumento significativo de desempenho executando consultas únicas indexadas. Esse tipo de índice secundário é um **índice de cobertura**, que contém todas as colunas incluídas na consulta. Portanto, a pesquisa de tabela não é necessária e o índice satisfaz a consulta inteira.

### <a name="views"></a>Modos de exibição

Exibições de Phoenix fornecem uma maneira de resolver uma limitação do HBase, onde o desempenho começará a ser prejudicado quando você criar mais de 100 tabelas físicas aproximadamente. Exibições de Phoenix habilitam várias *tabelas virtuais* para compartilhar uma tabela do HBase física subjacente.

Criar um modo de exibição de Phoenix é semelhante ao uso de sintaxe de exibição padrão do SQL. Uma diferença é que você pode definir colunas de exibição, além de colunas herdadas de sua tabela base. Você também pode adicionar novos colunas `KeyValue`.

Por exemplo, aqui está uma tabela física chamada `product_metrics` com a seguinte definição:

```sql
CREATE  TABLE product_metrics (
    metric_type CHAR(1),
    created_by VARCHAR, 
    created_date DATE, 
    metric_id INTEGER
    CONSTRAINT pk PRIMARY KEY (metric_type, created_by, created_date, metric_id));
```

Defina uma exibição sobre essa tabela, com colunas adicionais:

```sql
CREATE VIEW mobile_product_metrics (carrier VARCHAR, dropped_calls BIGINT) AS
SELECT * FROM product_metrics
WHERE metric_type = 'm';
```

Para adicionar mais colunas posteriormente, use a instrução `ALTER VIEW`.

### <a name="skip-scan"></a>Verificação de ignorar

Verificação de ignorar usa uma ou mais colunas de um índice composto para localizar valores distintos. Ao contrário de uma verificação de intervalo, verificação de ignorar implementa verificação intralinha, produzindo [melhor desempenho](https://phoenix.apache.org/performance.html#Skip-Scan). Durante a verificação, o primeiro valor correspondente será ignorado junto com o índice até o próximo valor ser encontrado.

Uma verificação de ignorar usa a enumeração `SEEK_NEXT_USING_HINT` do filtro HBase. Usando `SEEK_NEXT_USING_HINT`, a verificação de ignorar mantém o controle do conjunto de chaves ou intervalos de chaves, que estão sendo pesquisados para cada coluna. A verificação de ignorar, em seguida, usa uma chave que foi passada durante a avaliação de filtro e determina se ela é uma das combinações. Caso contrário, a verificação de ignorar avalia a próxima chave mais alta a ser avaliada.

### <a name="transactions"></a>Transações

Enquanto HBase fornece transações de nível de linha, Phoenix integra [Tephra](https://tephra.io/) para adicionar suporte a transações entre linhas e de tabela cruzada com semântica [ACID](https://en.wikipedia.org/wiki/ACID) completa.

Da mesma forma que transações SQL tradicionais, as transações fornecidas por meio do gerenciador de transações Phoenix permitem que você certifique-se de que uma unidade atômica de dados é introduzida com êxito, revertendo a transação se a operação de inserção falhar em qualquer tabela de transações habilitadas.

Para habilitar transações Phoenix, consulte a [documentação de transação do Apache Phoenix](https://phoenix.apache.org/transactions.html).

Para criar uma nova tabela com transações habilitadas, defina a `TRANSACTIONAL` propriedade `true` em uma `CREATE` instrução:

```sql
CREATE TABLE my_table (k BIGINT PRIMARY KEY, v VARCHAR) TRANSACTIONAL=true;
```

Para alterar uma tabela existente para ser transacional, use a mesma propriedade em uma `ALTER` instrução:

```sql
ALTER TABLE my_other_table SET TRANSACTIONAL=true;
```

> [!NOTE]
> Você não pode reverter uma tabela transacional novamente em uma não-transacional.

### <a name="salted-tables"></a>Tabelas Distribuídas

*Hotspotting de servidor região* pode ocorrer durante a gravação de registros com chaves sequenciais HBase. Embora você tenha vários servidores de região no seu cluster, suas gravações estão ocorrendo em apenas um. Essa concentração cria o problema de hotspotting onde, em vez de sua carga de trabalho de gravação ser distribuída em todos os servidores de região disponíveis, apenas um deles está controlando a carga. Como cada região tem um tamanho máximo predefinido, quando uma região atinge esse limite de tamanho, será dividida em duas regiões pequenas. Quando isso acontece, uma nova região tem todos os novos registros, tornando-se o novo ponto de acesso.

Para atenuar esse problema e obter melhor desempenho, pré-dividir tabelas de modo que todos os servidores de região são igualmente usados. Phoenix fornece*tabelas distribuídas*, de modo transparente adicionando o byte salting para a chave de linha para uma tabela específica. A tabela é previamente dividida em limites salt byte para garantir a distribuição de carga entre os servidores de região durante a fase inicial da tabela. Essa abordagem distribui a carga de trabalho de gravação em todos os servidores de região disponíveis, melhorando a gravação e o desempenho de leitura. Para distribuir uma tabela, especifique a `SALT_BUCKETS` propriedade de tabela quando a tabela for criada:

```sql
CREATE TABLE Saltedweblogs (
    transactionid varchar(500) Primary Key,
    transactiondate Date NULL,
    customerid varchar(50) NULL,
    bookid varchar(50) NULL,
    purchasetype varchar(50) NULL,
    orderid varchar(50) NULL,
    bookname varchar(50) NULL,
    categoryname varchar(50) NULL,
    invoicenumber varchar(50) NULL,
    invoicestatus varchar(50) NULL,
    city varchar(50) NULL,
    state varchar(50) NULL,
    paymentamount DOUBLE NULL,
    quantity INTEGER NULL,
    shippingamount DOUBLE NULL) SALT_BUCKETS=4;
```

## <a name="enable-and-tune-phoenix-with-apache-ambari"></a>Habilitar e ajustar o Phoenix com o Apache Ambari

Um cluster HDInsight HBase inclui a [interface de usuário do Ambari](hdinsight-hadoop-manage-ambari.md) para fazer alterações de configuração.

1. Para habilitar ou desabilitar Phoenix e controlar as configurações de tempo limite de consulta do Phoenix, faça logon na interface do usuário do Ambari Web (`https://YOUR_CLUSTER_NAME.azurehdinsight.net`) usando suas credenciais de usuário do Hadoop.

2. Selecione **HBase** na lista de serviços no menu esquerdo, em seguida selecione a guia **Configurações**.

    ![Configuração do Ambari HBase](./media/hdinsight-phoenix-in-hdinsight/ambari-hbase-config.png)

3. Localize a seção de configuração **Phoenix SQL** para habilitar ou desabilitar phoenix e definir o tempo limite da consulta.

    ![Seção de configuração do Ambari Phoenix SQL](./media/hdinsight-phoenix-in-hdinsight/ambari-phoenix.png)

## <a name="see-also"></a>Consulte também

* [Usar o Apache Phoenix com clusters do HBase baseados em Linux no HDInsight](hbase/apache-hbase-phoenix-squirrel-linux.md)
