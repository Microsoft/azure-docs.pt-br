---
title: Solucionar problemas de desempenho do Apache HBase no Azure HDInsight
description: Várias diretrizes e dicas de ajuste de desempenho do Apache HBase para obter um desempenho ideal no Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/24/2019
ms.openlocfilehash: 466fac524601e2d569bfa0ccf90179fe9419210d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942900"
---
# <a name="troubleshoot-apache-hbase-performance-issues-on-azure-hdinsight"></a>Solucionar problemas de desempenho do Apache HBase no Azure HDInsight

Este artigo descreve várias diretrizes e dicas de ajuste de desempenho do Apache HBase para obter um desempenho ideal no Azure HDInsight. Muitas dessas dicas dependem da carga de trabalho específica e do padrão de leitura/gravação/verificação. Antes de aplicar as alterações de configuração em um ambiente de produção, teste-as exaustivamente.

## <a name="hbase-performance-insights"></a>Informações de desempenho do HBase

O principal afunilamento na maioria das cargas de trabalho do HBase é o log write ahead (WAL). Isso afeta seriamente o desempenho de gravação. O HBase do HDInsight tem um modelo de computação de armazenamento separado. Os dados são armazenados remotamente no armazenamento do Azure, embora as máquinas virtuais hospedem os servidores de região. Até recentemente, o WAL também foi gravado no armazenamento do Azure. No HDInsight, esse comportamento amplifica esse afunilamento. O recurso de [gravações aceleradas](./apache-hbase-accelerated-writes.md) foi projetado para resolver esse problema. Ele grava o WAL nos discos gerenciados SSD Premium do Azure. Isso beneficia enormemente o desempenho de gravação e ajuda muitos problemas enfrentados por algumas das cargas de trabalho com uso intensivo de gravação.

Para obter uma melhoria significativa nas operações de leitura, use a [conta de armazenamento de blob de blocos Premium](https://azure.microsoft.com/blog/azure-premium-block-blob-storage-is-now-generally-available/) como seu armazenamento remoto. Essa opção só será possível se o recurso WAL estiver habilitado.

## <a name="compaction"></a>Compactação

A compactação é outro afunilamento potencial que é fundamentalmente acordado na Comunidade. Por padrão, a compactação principal é desabilitada em clusters HBase do HDInsight. A compactação é desabilitada porque, embora seja um processo de uso intensivo de recursos, os clientes têm total flexibilidade para agendá-lo de acordo com suas cargas de trabalho. Por exemplo, eles podem agendá-lo fora do horário de pico. Além disso, a localidade dos dados não é uma preocupação porque nosso armazenamento é remoto (apoiado pelo armazenamento do Azure) em vez de um Sistema de Arquivos Distribuído do Hadoop local (HDFS).

Os clientes devem agendar a compactação principal em sua conveniência. Se eles não fizerem essa manutenção, a compactação afetará negativamente o desempenho de leitura a longo prazo.

Para operações de verificação, as latências médias muito maiores que 100 milissegundos devem ser uma causa de preocupação. Verifique se a compactação principal foi agendada com precisão.

## <a name="apache-phoenix-workload"></a>Carga de trabalho Apache Phoenix

Responder às perguntas a seguir ajudará você a entender melhor sua carga de trabalho Apache Phoenix:

* Todas as suas "leituras" estão sendo traduzidas para verificações?
    * Em caso afirmativo, quais são as características dessas verificações?
    * Você otimizou o esquema da tabela Phoenix para essas verificações, incluindo a indexação apropriada?
* Você usou a `EXPLAIN` instrução para entender os planos de consulta gerados pela geração de "leituras"?
* Suas gravações "Upsert-selects"?
    * Nesse caso, eles também fariam verificações. Latência esperada para verificações médias de aproximadamente 100 milissegundos, em comparação com 10 milissegundos para o ponto chegar no HBase.  

## <a name="test-methodology-and-metrics-monitoring"></a>Metodologia de teste e monitoramento de métricas

Se você estiver usando parâmetros de comparação como o Yahoo! Nuvem servindo benchmark, JMeter ou Pherf para testar e ajustar o desempenho, certifique-se de que:

- Os computadores cliente não se tornam um afunilamento. Para fazer isso, verifique o uso da CPU em computadores cliente.

- As configurações do lado do cliente, como o número de threads, são ajustadas adequadamente para saturar a largura de banda do cliente.

- Os resultados de teste são registrados de forma precisa e sistemática.

Se suas consultas começaram a fazer muito pior do que antes, verifique se há bugs em potencial no código do aplicativo. Ele gera repentinamente grandes quantidades de dados inválidos? Se for, ele pode aumentar as latências de leitura.

## <a name="migration-issues"></a>Problemas de migração

Se você estiver migrando para o Azure HDInsight, certifique-se de que sua migração seja feita sistematicamente e com precisão, preferencialmente por meio da automação. Evite a migração manual. Certifique-se de que:

- Os atributos de tabela são migrados com precisão. Os atributos podem incluir como compactação, filtros de flor e assim por diante.

- As configurações de Salt em tabelas Phoenix são mapeadas adequadamente para o novo tamanho do cluster. Por exemplo, o número de buckets Salt deve ser um múltiplo do número de nós de trabalho no cluster. E você deve usar um múltiplo que seja um fator da quantidade de pontos de espera.

## <a name="server-side-configuration-tunings"></a>Ajustes de configuração no lado do servidor

No HBase do HDInsight, os HFiles são armazenados no armazenamento remoto. Quando há um erro de cache, o custo de leituras é maior do que os sistemas locais, pois os dados em sistemas locais são apoiados pelo HDFS local. Para a maioria dos cenários, o uso inteligente de caches do HBase (cache de blocos e cache de buckets) foi projetado para contornar esse problema. Nos casos em que o problema não é burlado, usar uma conta de blob de blocos Premium pode ajudar esse problema. O driver do Windows Azure Storage Blob depende de determinadas propriedades, como `fs.azure.read.request.size` para buscar dados em blocos com base no que ele determina para ser modo de leitura (sequencial versus aleatório), para que possa continuar a ser uma instância de latências mais altas com leituras. Por meio de experimentos de empírica, descobrimos que definir o tamanho do bloco de solicitação de leitura ( `fs.azure.read.request.size` ) para 512 KB e corresponder o tamanho do bloco das tabelas do HBase para que o mesmo tamanho produza o melhor resultado na prática.

Para a maioria dos clusters de nós de tamanho grande, o HBase do HDInsight fornece `bucketcache` como um arquivo em um SSD Premium local que é anexado à máquina virtual, que é executada `regionservers` . O uso do cache off-heap pode fornecer um aperfeiçoamento. Essa solução alternativa tem a limitação de usar a memória disponível e, potencialmente, ser menor do que o cache baseado em arquivo, de modo que talvez nem sempre seja a melhor opção.

A seguir estão alguns dos outros parâmetros específicos que ajustamos, e isso parecia ajudar em diferentes graus:

- Aumente o `memstore` tamanho do padrão de 128 MB a 256 MB. Normalmente, essa configuração é recomendada para cenários de gravação pesada.

- Aumente o número de threads dedicados para compactação, da configuração padrão de **1** a **4**. Essa configuração será relevante se observarmos compactações secundárias frequentes.

- Evite o bloqueio `memstore` de liberação devido ao limite de armazenamento. Para fornecer esse buffer, aumente a `Hbase.hstore.blockingStoreFiles` configuração para **100**.

- Para controlar liberações, use as seguintes configurações:

    - `Hbase.regionserver.maxlogs`: **140** (evita liberações por causa de limites de Wal)

    - `Hbase.regionserver.global.memstore.lowerLimit`: **0,55**

    - `Hbase.regionserver.global.memstore.upperLimit`: **0,60**

- Configurações específicas de Phoenix para ajuste de pool de threads:

    - `Phoenix.query.queuesize`: **10000**

    - `Phoenix.query.threadpoolsize`: **512**

- Outras configurações específicas de Phoenix:

    - `Phoenix.rpc.index.handler.count`: **50** (se houver grandes ou muitas pesquisas de índice)

    - `Phoenix.stats.updateFrequency`: **1 hora**

    - `Phoenix.coprocessor.maxmetadatacachetimetolivems`: **1 hora**

    - `Phoenix.coprocessor.maxmetadatacachesize`: **50 MB**

- Tempos limite de RPC: **3 minutos**

   - Os tempos limite de RPC incluem tempo limite de RPC do HBase, tempo limite do scanner de cliente HBase e tempo limite de consulta de Phoenix. 
   - Verifique se o `hbase.client.scanner.caching` parâmetro está definido com o mesmo valor na extremidade do servidor e na extremidade do cliente. Se eles não forem iguais, essa configuração levará a erros de término do cliente relacionados ao `OutOfOrderScannerException` . Essa configuração deve ser definida como um valor baixo para verificações grandes. Definimos esse valor como **100**.

## <a name="other-considerations"></a>Outras considerações

Estes são os parâmetros adicionais para considerar o ajuste:

- `Hbase.rs.cacheblocksonwrite` – Por padrão, em HDI, essa configuração é definida como **true**.

- Configurações que permitem adiar a compactação secundária para mais tarde.

- Configurações experimentais, como ajustar percentuais de filas que são reservadas para solicitações de leitura e gravação.

## <a name="next-steps"></a>Próximas etapas

Se o problema permanecer sem resolução, visite um dos seguintes canais para obter mais suporte:

- Obtenha respostas de especialistas do Azure por meio do [Suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

- Conecte-se com [@AzureSupport](https://twitter.com/azuresupport). Essa é a conta de Microsoft Azure oficial para melhorar a experiência do cliente. Ele conecta a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

- Se precisar de mais ajuda, poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o hub **Ajuda + suporte**. Para obter informações mais detalhadas, consulte [Como criar uma solicitação de Suporte do Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Sua assinatura do Microsoft Azure inclui acesso ao gerenciamento de assinaturas e suporte de cobrança, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).