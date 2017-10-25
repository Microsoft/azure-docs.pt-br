---
title: "Passo a passos da ciência de dados do HDInsight Hadoop usando o Hive no Azure | Microsoft Docs"
description: "Os exemplos do Processo de Ciência de Dados de Equipe que mostram o passo a passo do uso do Hive no Azure HDInsight Hadoop para executar análises preditivas."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: bradsev
ms.openlocfilehash: 5dbfd043f1809f407baf6aa4ca5c5272af425012
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="hdinsight-hadoop-data-science-walkthroughs-using-hive-on-azure"></a>Passo a passos da ciência de dados do HDInsight Hadoop usando o Hive no Azure 

Essas orientações usam o Hive com um cluster do HDInsight Hadoop para executar a análise preditiva. Eles seguem as etapas descritas no Processo de Ciência de Dados de Equipe. Para obter uma visão geral do Processo de Ciência de Dados de Equipe, veja [Processo de Ciência de Dados](overview.md). Para obter uma introdução ao Azure HDInsight, veja [Introdução ao Azure HDInsight, a pilha de tecnologia do Hadoop e clusters do Hadoop](../../hdinsight/hdinsight-hadoop-introduction.md).

Outras instruções passo a passo de ciência de dados que executam o Processo de ciência de dados de equipe estão agrupadas pela **plataforma** que elas usam. Consulte [Instruções passo a passo que executam o Processo de ciência de dados de equipe](walkthroughs.md) para obter uma discriminação desses exemplos.


## <a name="predict-taxi-tips-using-hive-with-hdinsight-hadoop"></a>Prever gorjetas de táxi usando Hive com HDInsight Hadoop

O passo a passo [Usar clusters do HDInsight Hadoop](hive-walkthrough.md) usa dados dos táxis de Nova York para prever: 

- Se uma gorjeta é paga 
- A distribuição de valores de dica

O cenário é implementado usando o Hive com um [cluster do Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/). Saiba como armazenar, explorar e destacar os dados de engenharia de uma corrida de táxi de NYC disponível publicamente e um conjunto de dados de tarifas. Você também pode usar o Azure Machine Learning para criar e implantar os modelos.

## <a name="predict-advertisement-clicks-using-hive-with-hdinsight-hadoop"></a>Prever cliques de anúncio usando Hive com HDInsight Hadoop

O passo a passo [Usar clusters HDInsight Hadoop do Azure em um conjunto de dados de 1 TB](hive-criteo-walkthrough.md) usa um conjunto de dados de clique [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) publicamente disponível para prever se uma gorjeta é paga e se está no intervalo de valores esperados. O cenário é implementado usando o Hive com um [cluster HDInsight Hadoop do Azure](https://azure.microsoft.com/services/hdinsight/) para armazenamento, exploração, engenharia de recursos e amostragem de dados. Ele usa o Azure Machine Learning para criar, treinar e classificar um modelo de classificação binária prevendo se um usuário clica em um anúncio. O passo a passo termina mostrando como publicar um destes modelos como um serviço Web.


## <a name="next-steps"></a>Próximas etapas

Para uma discussão sobre os principais componentes do Processo de Ciência de Dados de Equipe, veja [Visão geral do Processo de Ciência de Dados de Equipe](overview.md).

Para uma discussão sobre o ciclo de vida do Processo de Ciência de Dados de Equipe que você pode usar para estruturar seus projetos de ciência de dados, veja [Ciclo de vida do Processo de Ciência de Dados de Equipe](lifecycle.md). O ciclo de vida descreve as etapas, do início ao fim, que os projetos normalmente seguem quando são executados. 

