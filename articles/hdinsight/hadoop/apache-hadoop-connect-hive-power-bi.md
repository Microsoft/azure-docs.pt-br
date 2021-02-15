---
title: Visualizar dados do Apache Hive com o Microsoft Power BI no Azure HDInsight
description: Saiba como usar o Microsoft Power BI para visualizar dados de Hive processados pelo Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: a5732c2dc0a92bd5727eeff39a529630e45683d7
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946689"
---
# <a name="visualize-apache-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>Visualize os dados do Apache Hive com o Microsoft Power BI usando o ODBC no Azure HDInsight

Saiba como conectar o Microsoft Power BI Desktop ao Azure HDInsight usando o ODBC e visualize Apache Hive dados.

> [!IMPORTANT]
> Você pode aproveitar o driver ODBC Hive a ser importado por meio do conector ODBC genérico no Power BI Desktop. No entanto, não é recomendável para cargas de trabalho de BI considerando a natureza não interativo do mecanismo de consulta do Hive. [Conector de consulta interativa HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) e [conector do HDInsight Spark](/power-bi/spark-on-hdinsight-with-direct-connect) são as melhores opções para o desempenho.

Neste artigo, você carrega os dados de uma `hivesampletable` tabela do hive para Power bi. A tabela de Hive contém alguns dados de uso de telefone celular. Em seguida, você cria gráficos com os dados de uso em um mapa mundial:

![Relatório de mapa de Power BI do HDInsight](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

As informações também se aplicam ao novo tipo de cluster [Consulta Interativa](../interactive-query/apache-interactive-query-get-started.md). Para saber como se conectar à consulta interativa do HDInsight usando a consulta direta, veja [Visualizar dados de Hive de consulta interativa com o Microsoft Power BI usando consulta direta no Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de prosseguir com este artigo, você deve ter os seguintes itens:

* Cluster HDInsight. O cluster pode ser um cluster HDInsight com Hive ou um cluster de Consulta Interativa recém-lançado. Para a criação de clusters, consulte [Criar cluster](apache-hadoop-linux-tutorial-get-started.md).

* [Microsoft Power bi desktop](https://powerbi.microsoft.com/desktop/). Você pode baixar uma cópia do [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="create-hive-odbc-data-source"></a>Criar uma fonte de dados ODBC do Hive

Consulte [Criar uma fonte de dados ODBC do Hive](apache-hadoop-connect-excel-hive-odbc-driver.md#create-apache-hive-odbc-data-source).

## <a name="load-data-from-hdinsight"></a>Carregar dados do HDInsight

A tabela hive do **hivesampletable** vem com todos os clusters HDInsight.

1. Inicie o Power BI Desktop.

1. No menu superior, navegue até **página inicial**  >  **obter dados**  >  **mais...**.

    ![HDInsight Excel Power BI abrir dados](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)

1. Na caixa de diálogo **obter dados** , selecione **outro** da esquerda, selecione **ODBC** à direita e, em seguida, selecione **conectar** na parte inferior.

1. Na caixa **de diálogo do ODBC** , selecione o nome da fonte de dados que você criou na última seção na lista suspensa. Depois, selecione **OK**.

1. Para o primeiro uso, uma caixa de diálogo do **driver ODBC** será aberta. Selecione **padrão ou personalizado** no menu à esquerda. Em seguida, selecione **conectar-se** para abrir o **navegador**.

1. Na caixa de diálogo **navegador** , expanda **ODBC > Hive > padrão**, selecione **hivesampletable** e, em seguida, selecione **carregar**.

## <a name="visualize-data"></a>Visualizar dados

Continue do último procedimento.

1. No painel visualizações, selecione **mapa**, é um ícone de globo.

    ![O Power BI do HDInsight personaliza o relatório](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)

1. No painel **campos** , selecione **país** e **devicemake**. Você pode ver os dados no gráfico no mapa.

1. Expanda o mapa.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como visualizar os dados de HDInsight usando o Power BI.  Para saber mais, leia os seguintes artigos:

* [Conectar o Excel ao HDInsight com o Driver ODBC do Microsoft Hive](./apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Conecte o Excel ao Apache Hadoop usando Power Query](apache-hadoop-connect-excel-power-query.md).
* [Visualizar dados interativos Apache Hive de consulta com o Microsoft Power BI usando a consulta direta](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)