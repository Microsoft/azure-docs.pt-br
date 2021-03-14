---
title: Notas da versão mais recente do Microsoft Azure HDInsight
description: Notas da versão mais recente do Microsoft Azure HDInsight. Obtenha detalhes e dicas de desenvolvimento para Hadoop, Spark, Microsoft R Server, Hive e muito mais.
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 3ea5168641f233f2e9580612a2f245fd8fef860f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699344"
---
# <a name="azure-hdinsight-release-notes"></a>Notas de versão do Azure HDInsight

Este artigo fornece informações sobre as atualizações de versão **mais recentes** do Azure HDInsight. Para obter informações sobre versões anteriores, consulte o [Arquivo morto de notas de versão do HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Resumo

O Microsoft Azure HDInsight é um dos serviços mais populares entre os clientes empresariais para análise de software livre no Azure.

Se você quiser assinar as notas de versão, Assista a versões neste [repositório GitHub](https://github.com/hdinsight/release-notes/releases).

## <a name="release-date-02052021"></a>Data de lançamento: 02/05/2021

Esta versão se aplica tanto ao HDInsight 3,6 quanto ao HDInsight 4,0. A versão do HDInsight é disponibilizada para todas as regiões durante vários dias. A data de lançamento mostrada aqui indica a data de lançamento da primeira região. Se você não vir as alterações abaixo, aguarde até que a liberação seja ativada em sua região em vários dias.

## <a name="new-features"></a>Novos recursos
### <a name="dav4-series-support"></a>Suporte da série Dav4
O HDInsight acrescentou suporte à série Dav4 nesta versão. Saiba mais sobre [a série Dav4 aqui](/azure/virtual-machines/dav4-dasv4-series).

### <a name="kafka-rest-proxy-ga"></a>GA do proxy REST do Kafka 
O proxy REST do Kafka permite que você interaja com o cluster do Kafka por meio de uma API REST por HTTPS. O proxy REST do Kafka está geral disponível a partir desta versão. Saiba mais sobre o [proxy REST do Kafka aqui](/azure/hdinsight/kafka/rest-proxy).

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Migrar para Conjuntos de Dimensionamento de Máquinas Virtuais do Azure
O HDInsight atualmente usa máquinas virtuais do Azure para provisionar o cluster. O serviço está migrando gradualmente para os [conjuntos de dimensionamento de máquinas virtuais do Azure](../virtual-machine-scale-sets/overview.md). Todo o processo pode levar meses. Depois que suas regiões e assinaturas forem migradas, os clusters HDInsight recém-criados serão executados em conjuntos de dimensionamento de máquinas virtuais sem ações do cliente. Nenhuma alteração significativa é esperada.

## <a name="deprecation"></a>Reprovação
### <a name="disabled-vm-sizes"></a>Tamanhos de VM desabilitados
A partir do formulário de janeiro de 9 2021, o HDInsight bloqueará todos os clientes que criam clusters usando standand_A8, standand_A9, standand_A10 e standand_A11 tamanhos de VM. Os clusters existentes serão executados como estão. Considere migrar para o HDInsight 4,0 para evitar a interrupção potencial do sistema/suporte.

## <a name="behavior-changes"></a>Alterações de comportamento
### <a name="default-cluster-vm-size-changes-to-ev3-series"></a>Alterações de tamanho de VM de cluster padrão para a série Ev3 
Os tamanhos de VM de cluster padrão serão alterados de D-Series para Ev3-Series. Essa alteração se aplica a nós de cabeçalho e nós de trabalho. Para evitar essa alteração afetando os fluxos de trabalho testados, especifique os tamanhos de VM que você deseja usar no modelo ARM.

### <a name="network-interface-resource-not-visible-for-clusters-running-on-azure-virtual-machine-scale-sets"></a>Recurso de interface de rede não visível para clusters em execução em conjuntos de dimensionamento de máquinas virtuais do Azure
O HDInsight está migrando gradualmente para os conjuntos de dimensionamento de máquinas virtuais do Azure. As interfaces de rede para máquinas virtuais não são mais visíveis para os clientes para clusters que usam conjuntos de dimensionamento de máquinas virtuais do Azure.


### <a name="breaking-change-for-net-for-apache-spark-100"></a>Alteração significativa para .NET para Apache Spark 1.0.0
Com a versão mais recente, o HDInsight apresenta a primeira versão oficial v 1.0.0 da biblioteca [".NET for Apache Spark"](https://github.com/dotnet/spark) . Ele fornece a integridade do dataframe API para o Spark 2.4. x e o Spark 3.0. x, juntamente com um host de [outros recursos](https://github.com/dotnet/spark/blob/master/docs/release-notes/1.0.0/release-1.0.0.md). Haverá alterações significativas para essa versão principal, consulte [o guia de migração do .net para Apache Spark](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) para entender as etapas necessárias para atualizar seu código e pipelines. Para saber mais, consulte este [.net para Apache Spark v 1.0 no guia do Azure HDInsight](/azure/hdinsight/spark/spark-dotnet-version-update#using-net-for-apache-spark-v10-in-hdinsight).


## <a name="upcoming-changes"></a>Alterações futuras
As alterações a seguir ocorrerão em versões futuras.

### <a name="default-cluster-version-will-be-changed-to-40"></a>A versão de cluster padrão será alterada para 4,0
A partir de fevereiro de 2021, a versão padrão do cluster HDInsight será alterada de 3,6 para 4,0. Para obter mais informações sobre as versões disponíveis, consulte [versões disponíveis](./hdinsight-component-versioning.md). Saiba mais sobre o que há de novo no [HDInsight 4,0](./hdinsight-version-release.md).

### <a name="os-version-upgrade"></a>Atualização de versão do so
O HDInsight está atualizando a versão do sistema operacional do Ubuntu 16, 4 para 18, 4. A atualização será concluída antes de abril de 2021.

### <a name="hdinsight-36-end-of-support-on-june-30-2021"></a>Fim do suporte do HDInsight 3,6 em junho de 30 2021
O HDInsight 3,6 será o fim do suporte. A partir do formulário de junho de 30 2021, os clientes não podem criar novos clusters HDInsight 3,6. Os clusters existentes serão executados como estão, sem o suporte da Microsoft. Considere migrar para o HDInsight 4,0 para evitar a interrupção potencial do sistema/suporte.

## <a name="bug-fixes"></a>Correções de bug
O HDInsight continua a fazer aprimoramentos de desempenho e confiabilidade do cluster. 

## <a name="component-version-change"></a>Alteração na versão do componente
Nenhuma alteração de versão de componente para esta versão. Você pode encontrar as versões de componente atuais para HDInsight 4,0 e HDInsight 3,6 neste [documento](./hdinsight-component-versioning.md).
