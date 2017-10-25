---
title: "Usar Hive com Hadoop para análise de log do site – HDInsight do Azure | Microsoft Docs"
description: "Saiba como usar o Hive com o HDInsight para analisar os logs do Website. Você usará um arquivo de log como entrada em uma tabela do HDInsight e usará o HiveQL para consultar os dados."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6fb7b5c2-8df4-40b1-a9e2-6815080004f9
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2016
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: e1cdb786bb6049980aafc0213abf53013e342618
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-hive-with-windows-based-hdinsight-to-analyze-logs-from-websites"></a>Usar o Hive com o HDInsight baseado no Windows para analisar logs de sites
Saiba como usar o HiveQL com HDInsight para analisar os logs de um Website. A análise de log do Website pode ser usada para segmentar seu público com base em atividades semelhantes, categorizar os visitantes do site por demografia e descobrir o conteúdo que eles veem, seus Websites de origem e assim por diante.

> [!IMPORTANT]
> As etapas neste documento só funcionam com clusters HDInsight baseados no Windows. O HDInsight está disponível somente no Windows para versões inferiores ao HDInsight 3.4. O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

Neste exemplo, você utilizará um cluster do HDInsight para analisar arquivos de log do Website para obter informações sobre a frequência de visitas ao Website por meio de Websites externos em um dia. Você também vai gerar um resumo dos erros de site que os usuários enfrentam. Você saberá como:

* Conectar-se a um armazenamento de Blob do Azure, que contém os arquivos de log do site.
* Criar tabelas do HIVE para consultar esses logs.
* Criar consultas do HIVE para analisar os dados.
* Usar o Microsoft Excel para conectar-se ao HDInsight (usando ODBC, conectividade de banco de dados aberta) para recuperar os dados analisados.

![HDI.Samples.Website.Log.Analysis][img-hdi-weblogs-sample]

## <a name="prerequisites"></a>Pré-requisitos
* Você deve ter provisionado um cluster Hadoop no Azure HDInsight. Para obter instruções, confira [Provisionar clusters do HDInsight][hdinsight-provision].
* Você deve ter o Microsoft Excel 2013 ou Microsoft Excel 2010 instalado.
* Você deve ter o [Driver ODBC do Microsoft Hive](http://www.microsoft.com/download/details.aspx?id=40886) para importar dados do Hive no Excel.

## <a name="to-run-the-sample"></a>Para executar a amostra
1. No [Portal do Azure](https://portal.azure.com/), no quadro inicial (caso você tenha fixado o cluster ali), clique no bloco do cluster no qual você deseja executar o exemplo.
2. Na folha do cluster, em **Links Rápidos**, clique em **Painel do Cluster** e, na folha **Painel do Cluster**, clique em **Painel do Cluster HDInsight**. Como alternativa, você pode abrir diretamente o painel usando a seguinte URL:

         https://<clustername>.azurehdinsight.net

    Quando solicitado, faça a autenticação usando o nome de usuário e senha do administrador usados ao provisionar o cluster.
3. Na página da Web que é aberta, clique na guia **Galeria de Introdução** e, sob a categoria **Soluções com Dados de Exemplo**, clique no exemplo **Análise de Log do Site**.
4. Siga as instruções fornecidas na página da Web para concluir a amostra.

## <a name="next-steps"></a>Próximas etapas
Experimente o exemplo a seguir: [Analisando dados do sensor usando o Hive com HDInsight](hdinsight-hive-analyze-sensor-data.md).

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-sensor-data-sample]: ../hdinsight-use-hive-sensor-data-analysis.md

[img-hdi-weblogs-sample]: ./media/hdinsight-hive-analyze-website-log/hdinsight-weblogs-sample.png
