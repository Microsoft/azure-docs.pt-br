---
title: Notas da versão mais recente do Microsoft Azure HDInsight
description: Notas da versão mais recente do Microsoft Azure HDInsight. Obtenha detalhes e dicas de desenvolvimento para Hadoop, Spark, Microsoft R Server, Hive e muito mais.
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: 324d8b4c9fc53ca24e62fe339065d4452577cb1f
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105607211"
---
# <a name="azure-hdinsight-release-notes"></a>Notas de versão do Azure HDInsight

Este artigo fornece informações sobre as atualizações de versão **mais recentes** do Azure HDInsight. Para obter informações sobre versões anteriores, consulte o [Arquivo morto de notas de versão do HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Resumo

O Microsoft Azure HDInsight é um dos serviços mais populares entre os clientes empresariais para análise de software livre no Azure.

Se você quiser assinar as notas de versão, Assista a versões neste [repositório GitHub](https://github.com/hdinsight/release-notes/releases).

## <a name="release-date-03242021"></a>Data de lançamento: 03/24/2021

Esta versão se aplica tanto ao HDInsight 3,6 quanto ao HDInsight 4,0. A versão do HDInsight é disponibilizada para todas as regiões durante vários dias. A data de lançamento mostrada aqui indica a data de lançamento da primeira região. Se você não vir as alterações abaixo, aguarde até que a liberação seja ativada em sua região em vários dias.

## <a name="new-features"></a>Novos recursos
### <a name="spark-30-preview"></a>Versão prévia do Spark 3,0
O HDInsight adicionou suporte do [Spark 3.0.0](https://spark.apache.org/docs/3.0.0/) ao HDInsight 4,0 como um recurso de visualização. 

### <a name="kafka-24-preview"></a>Versão prévia do Kafka 2,4
O HDInsight adicionou o suporte do [Kafka 2.4.1](http://kafka.apache.org/24/documentation.html) ao hdinsight 4,0 como um recurso de visualização.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Migrar para Conjuntos de Dimensionamento de Máquinas Virtuais do Azure
O HDInsight atualmente usa máquinas virtuais do Azure para provisionar o cluster. O serviço está migrando gradualmente para os [conjuntos de dimensionamento de máquinas virtuais do Azure](../virtual-machine-scale-sets/overview.md). Todo o processo pode levar meses. Depois que suas regiões e assinaturas forem migradas, os clusters HDInsight recém-criados serão executados em conjuntos de dimensionamento de máquinas virtuais sem ações do cliente. Nenhuma alteração significativa é esperada.

## <a name="deprecation"></a>Reprovação
Não há preterição nesta versão.

## <a name="behavior-changes"></a>Alterações de comportamento
### <a name="default-cluster-version-is-changed-to-40"></a>A versão de cluster padrão é alterada para 4,0
A versão padrão do cluster HDInsight é alterada de 3,6 para 4,0. Para obter mais informações sobre as versões disponíveis, consulte [versões disponíveis](./hdinsight-component-versioning.md). Saiba mais sobre o que há de novo no [HDInsight 4,0](./hdinsight-version-release.md).

### <a name="default-cluster-vm-sizes-are-changed-to-ev3-series"></a>Os tamanhos de VM de cluster padrão são alterados para a série Ev3 
Os tamanhos de VM de cluster padrão são alterados de D-Series para Ev3-Series. Essa alteração se aplica a nós de cabeçalho e nós de trabalho. Para evitar essa alteração afetando os fluxos de trabalho testados, especifique os tamanhos de VM que você deseja usar no modelo ARM.

### <a name="network-interface-resource-not-visible-for-clusters-running-on-azure-virtual-machine-scale-sets"></a>Recurso de interface de rede não visível para clusters em execução em conjuntos de dimensionamento de máquinas virtuais do Azure
O HDInsight está migrando gradualmente para os conjuntos de dimensionamento de máquinas virtuais do Azure. As interfaces de rede para máquinas virtuais não são mais visíveis para os clientes para clusters que usam conjuntos de dimensionamento de máquinas virtuais do Azure.

## <a name="upcoming-changes"></a>Alterações futuras
As alterações a seguir ocorrerão em versões futuras.

### <a name="os-version-upgrade"></a>Atualização de versão do so
O HDInsight atualizará a versão do sistema operacional do Ubuntu 16, 4 para o 18, 4. A atualização será concluída antes de abril de 2021.

### <a name="hdinsight-36-end-of-support-on-june-30-2021"></a>Fim do suporte do HDInsight 3,6 em junho de 30 2021
O HDInsight 3,6 será o fim do suporte. A partir do formulário de junho de 30 2021, os clientes não podem criar novos clusters HDInsight 3,6. Os clusters existentes serão executados como estão, sem o suporte da Microsoft. Considere migrar para o HDInsight 4,0 para evitar a interrupção potencial do sistema/suporte.

## <a name="bug-fixes"></a>Correções de bug
O HDInsight continua a fazer aprimoramentos de desempenho e confiabilidade do cluster. 

## <a name="component-version-change"></a>Alteração na versão do componente
Adicionado suporte para Spark 3.0.0 e Kafka 2.4.1 como versão prévia. Você pode encontrar as versões de componente atuais para HDInsight 4,0 e HDInsight 3,6 neste [documento](./hdinsight-component-versioning.md).
