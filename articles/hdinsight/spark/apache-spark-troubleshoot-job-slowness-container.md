---
title: Apache Spark lento quando o armazenamento do Azure HDInsight tiver muitos arquivos
description: Apache Spark trabalho é executado lentamente quando o contêiner de armazenamento do Azure contém muitos arquivos no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/21/2019
ms.openlocfilehash: c26baec66248ca00ef212acf3d773c2566b3aea9
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946359"
---
# <a name="apache-spark-job-run-slowly-when-the-azure-storage-container-contains-many-files-in-azure-hdinsight"></a>Os trabalhos do Apache Spark são executados lentamente quando o contêiner de armazenamento do Azure contém muitos arquivos no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções de problemas ao usar os componentes do Apache Spark nos clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Ao executar um cluster HDInsight, o trabalho de Apache Spark que grava no contêiner de armazenamento do Azure torna-se lento quando há muitos arquivos/subpastas. Por exemplo, ele leva 20 segundos ao gravar em um novo contêiner, mas cerca de 2 minutos ao gravar em um contêiner que tem 200 arquivos.

## <a name="cause"></a>Causa

Esse é um problema conhecido do Spark. O lentidão vem das `ListBlob` operações e `GetBlobProperties` durante a execução do trabalho do Spark.

Para rastrear partições, o Spark precisa manter um `FileStatusCache` que contém informações sobre a estrutura do diretório. Usando esse cache, o Spark pode analisar os caminhos e estar atento às partições disponíveis. O benefício do acompanhamento de partições é que o Spark só toca os arquivos necessários quando você lê os dados. Para manter essas informações atualizadas, quando você grava novos dados, o Spark precisa listar todos os arquivos no diretório e atualizar esse cache.

No Spark 2,1, embora não seja necessário atualizar o cache após cada gravação, o Spark verificará se uma coluna de partição existente corresponde ao proposto na solicitação de gravação atual, de modo que também levará à lista de operações no início de cada gravação.

No Spark 2,2, ao gravar dados com o modo de acréscimo, esse problema de desempenho deve ser corrigido.

## <a name="resolution"></a>Resolução

Quando você cria um conjunto de dados particionado, é importante usar um esquema de particionamento que limitará o número de arquivos que o Spark tem a listar para atualizar o `FileStatusCache` .

Para cada enésimo micro batch em que N %100 = = 0 (100 é apenas um exemplo), mova os dados existentes para outro diretório, que pode ser carregado pelo Spark.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]