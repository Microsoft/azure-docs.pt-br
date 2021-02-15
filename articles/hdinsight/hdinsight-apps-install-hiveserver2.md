---
title: Dimensionar HiveServer2 no Azure HDInsight
description: Dimensione horizontalmente o HiveServer2 nos clusters do Azure HDInsight usando nós de borda para aumentar a tolerância a falhas e a disponibilidade.
services: hdinsight
ms.service: hdinsight
ms.topic: conceptual
ms.reviewer: hrasheed-msft
ms.author: kecheung
author: kcheeeung
ms.date: 08/12/2020
ms.openlocfilehash: d0a0df4791492c1c9f0d600630d723024c46c1b8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88227013"
---
# <a name="scale-hiveserver2-on-azure-hdinsight-clusters-for-high-availability"></a>Dimensionar HiveServer2 em clusters do Azure HDInsight para alta disponibilidade

Saiba como implantar um HiveServer2 adicional no cluster para aumentar a disponibilidade e a distribuição de carga. Ao aumentar o tamanho de cabeçalho não desejado, você também pode usar nós de borda para implantar o HiveServer2. 

> [!NOTE]
> Dependendo do seu uso, aumentar o número de HiveServer2 pode aumentar o número de conexões com o metastore do Hive. Verifique também se o banco de dados SQL do Azure está dimensionado corretamente.

## <a name="prerequisites"></a>Pré-requisitos

Para usar este guia, você precisará compreender o seguinte artigo:
- [Usar nós de borda vazios em clusters do Apache Hadoop no HDInsight](hdinsight-apps-use-edge-node.md)

## <a name="install-hiveserver2"></a>Instalar o HiveServer2

Nesta seção, você instalará um HiveServer2 adicional nos hosts de destino.

1. Abra o Ambari no navegador e clique no host de destino.

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-a.png" alt-text="Menu hosts de Ambari.":::

2. Clique no botão Adicionar e clique em HiveServer2

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-b.png" alt-text="Menu hosts de Ambari.":::

3. Confirme e o processo será executado. Repita a 1-3 para todos os hosts desejados.

4. Quando você terminar de instalar o, reinicie todos os serviços com configurações obsoletas e inicie o HiveServer2.

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-c.png" alt-text="Menu hosts de Ambari.":::

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a instalar o HiveServer2 no cluster. Para saber mais sobre os nós e aplicativos de borda, confira os seguintes artigos:

* [Instalar nó de borda](hdinsight-apps-use-edge-node.md): saiba como instalar um nó de borda em seu cluster HDInsight.
* [Instalar aplicativos HDInsight](hdinsight-apps-install-applications.md): Saiba como instalar um aplicativo HDInsight em seus clusters.
* [Limites de conexão de DTU do SQL Azure](../azure-sql/database/resource-limits-dtu-single-databases.md): Saiba mais sobre os limites do banco de dados SQL do Azure usando DTU.
* [Limites de conexão VCORE do SQL Azure](../azure-sql/database/resource-limits-vcore-elastic-pools.md): Saiba mais sobre os limites do banco de dados SQL do Azure usando o vCores.
