---
title: Otimizar para recomendações do supervisor de cluster
titleSuffix: Azure HDInsight
description: Otimize as recomendações do Apache HBase para o supervisor de cluster no Azure HDInsight.
author: ramkrish86
ms.author: ramvasu
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/03/2021
ms.openlocfilehash: acb7a6aeb4084949be3b0ad40e770a414a13ab6d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943016"
---
# <a name="apache-hbase-advisories-in-azure-hdinsight"></a>Conselhos do Apache HBase no Azure HDInsight

Este artigo descreve várias consultorias para ajudá-lo a otimizar o desempenho do Apache HBase no Azure HDInsight. 

## <a name="optimize-hbase-to-read-most-recently-written-data"></a>Otimizar o HBase para ler os dados gravados mais recentemente

Se seu UseCase envolver a leitura dos dados gravados mais recentemente do HBase, este comunicado pode ajudá-lo. Para alto desempenho, é ideal que as leituras do HBase sejam servidas do memstore, em vez do armazenamento remoto.

O comunicado de consulta indica que para uma determinada família de coluna em uma tabela > 75% de leituras que estão sendo servidas de memstore. Esse indicador sugere que, mesmo que ocorra uma liberação no memstore, o arquivo recente precisa ser acessado e que precisa estar no cache. Os dados são gravados primeiro em memstore o sistema acessa os dados recentes. Há uma chance de que os threads de liberação do HBase internos detectem que uma determinada região atingiu o tamanho de 128M (padrão) e pode disparar uma liberação. Esse cenário ocorre até mesmo os dados mais recentes que foram gravados quando o memstore estava em cerca de 128M de tamanho. Portanto, uma leitura posterior desses registros recentes pode exigir uma leitura de arquivo em vez de memstore. Portanto, é melhor otimizar que até dados recentes que são liberados recentemente podem residir no cache.

Para otimizar os dados recentes no cache, considere as seguintes definições de configuração:

1. Defina `hbase.rs.cacheblocksonwrite` como `true`. Essa configuração padrão no HBase do HDInsight é `true` , portanto, verifique se ele não é redefinido para `false` .

2. Aumente o `hbase.hstore.compactionThreshold` valor para que você possa evitar a compactação do iniciar no. Por padrão, esse valor é `3`. Você pode aumentá-lo para um valor mais alto como `10` .

3. Se você seguir a etapa 2 e definir compactionThreshold, altere `hbase.hstore.compaction.max` para um valor mais alto, por exemplo `100` , e também aumente o valor da configuração `hbase.hstore.blockingStoreFiles` para valor mais alto, por exemplo `300` .

4. Se você tiver certeza de que precisa ler apenas os dados recentes, defina `hbase.rs.cachecompactedblocksonwrite` configuração como **ativado**. Essa configuração informa ao sistema que, mesmo que ocorra a compactação, os dados permanecem no cache. As configurações também podem ser definidas no nível da família. 

   No Shell do HBase, execute o seguinte comando para definir a `hbase.rs.cachecompactedblocksonwrite` configuração:
   
   ```
   alter '<TableName>', {NAME => '<FamilyName>', CONFIGURATION => {'hbase.hstore.blockingStoreFiles' => '300'}}
   ```

5. O cache de blocos pode ser desativado para uma determinada família em uma tabela. Verifique se **ele está ativado para famílias** que têm leituras de dados mais recentes. Por padrão, o cache de blocos está ativado para todas as famílias em uma tabela. Caso você tenha desabilitado o cache de blocos para uma família e precise ativá-lo, use o comando ALTER do shell do HBase.

   Essas configurações ajudam a garantir que os dados estão disponíveis no cache e que os dados recentes não passam pela compactação. Se um TTL for possível em seu cenário, considere o uso da compactação em camadas de data. Para obter mais informações, consulte [Guia de referência do Apache HBase: compactação em camadas de data](https://hbase.apache.org/book.html#ops.date.tiered)  

## <a name="optimize-the-flush-queue"></a>Otimizar a fila de liberação

Esse comunicado indica que as liberações do HBase podem precisar de ajuste. A configuração atual para manipuladores de liberação pode não ser alta o suficiente para lidar com o tráfego de gravação, o que pode levar a lentidão de liberações.

Na interface do usuário do servidor de região, observe se a fila de liberação cresce além de 100. Esse limite indica que as liberações são lentas e talvez você precise ajustar a   `hbase.hstore.flusher.count` configuração. Por padrão, o valor é 2. Verifique se os threads de liberação máxima não aumentam além de 6.

Além disso, veja se você tem uma recomendação para o ajuste da contagem de regiões. Se sim, sugerimos que você experimente o ajuste de região para ver se isso ajuda em liberações mais rápidas. Caso contrário, ajustar os threads de liberação pode ajudá-lo.

## <a name="region-count-tuning"></a>Ajuste da contagem de regiões

O comunicado de ajuste da contagem de regiões indica que o HBase bloqueou atualizações e a contagem de regiões pode ser maior do que o tamanho de heap com suporte ideal. Você pode ajustar o tamanho do heap, o tamanho do memstore e a contagem de regiões.

Como um cenário de exemplo:

- Suponha que o tamanho do heap para o servidor de região seja de 10 GB. Por padrão, o `hbase.hregion.memstore.flush.size` é `128M` . O valor padrão para `hbase.regionserver.global.memstore.size` é `0.4`. Isso significa que, de 10 GB, 4 GB são alocados para memstore (globalmente).

- Suponha que haja uma distribuição uniforme da carga de gravação em todas as regiões e presumindo que cada região cresça até 128 MB apenas, o número máximo de regiões nessa configuração é de `32` regiões. Se um determinado servidor de região estiver configurado para ter 32 regiões, o sistema evitará melhor o bloqueio de atualizações.

- Com essas configurações em vigor, o número de regiões é 100. O memstore global de 4 GB agora está dividido em regiões de 100. Efetivamente, cada região obtém apenas 40 MB para memstore. Quando as gravações são uniformes, o sistema faz liberações frequentes e o tamanho menor da ordem < 40 MB. Ter muitos threads de liberação pode aumentar a velocidade de liberação `hbase.hstore.flusher.count` .

A consultoria significa que seria bom reconsiderar o número de regiões por servidor, o tamanho do heap e a configuração do tamanho global do memstore junto com o ajuste dos threads de liberação para evitar que as atualizações sejam bloqueadas.

## <a name="compaction-queue-tuning"></a>Ajuste da fila de compactação

Se a fila de compactação do HBase aumentar para mais de 2000 e ocorrer periodicamente, você poderá aumentar os threads de compactação para um valor maior.

Quando há um número excessivo de arquivos para compactação, ele pode levar a mais uso de heap relacionado ao modo como os arquivos interagem com o sistema de arquivos do Azure. Portanto, é melhor concluir a compactação o mais rápido possível. Algumas vezes em clusters mais antigos, as configurações de compactação relacionadas à limitação podem levar à taxa de compactação mais lenta.

Verifique as configurações `hbase.hstore.compaction.throughput.lower.bound` e `hbase.hstore.compaction.throughput.higher.bound` . Se eles já estiverem definidos como 50 milhões e 100 ms, deixe-os como estão. No entanto, se você tiver configurado essas configurações para um valor mais baixo (que era o caso com clusters mais antigos), altere os limites para 50 milhões e 100 ms, respectivamente.

As configurações são `hbase.regionserver.thread.compaction.small` e `hbase.regionserver.thread.compaction.large` (os padrões são 1 cada).
Limite o valor máximo para que essa configuração seja menor que 3.

## <a name="full-table-scan"></a>Verificação de tabela completa

O aviso de verificação de tabela completa indica que mais de 75% das verificações emitidas são verificações completas de tabela/região. Você pode revisitar a maneira como seu código chama as verificações para melhorar o desempenho da consulta. Considere as seguintes práticas:

* Defina a linha de início e de parada apropriada para cada verificação.

* Use a API **MultiRowRangeFilter** para que você possa consultar intervalos diferentes em uma chamada de verificação. Para obter mais informações, consulte a [documentação da API do MultiRowRangeFilter](https://hbase.apache.org/2.1/apidocs/org/apache/hadoop/hbase/filter/MultiRowRangeFilter.html).

* Nos casos em que você precisa de uma verificação completa de tabela ou de região, verifique se há uma possibilidade de evitar o uso do cache para essas consultas, para que outras consultas que usam o cache não possam remover os blocos que estejam ativos. Para garantir que as verificações não usem o cache, use a API de **verificação** com a opção **setcache (false)** em seu código: 

   ```
   scan#setCaching(false)
   ```
   
## <a name="next-steps"></a>Próximas etapas

[Otimizar o Apache HBase usando Ambari](../optimize-hbase-ambari.md)
