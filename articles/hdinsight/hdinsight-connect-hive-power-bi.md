---
title: Visualizar Big Data com o Power BI no Azure HDInsight | Microsoft Docs
description: Saiba como usar o Microsoft Power BI para visualizar dados de Hive processados pelo Azure HDInsight.
keywords: hdinsight, hadoop, hive, consulta interativa, hive interativo, LLAP, odbc
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: jgao
ms.openlocfilehash: 9a17c984f5ca801dfa017f1d6fc8fa5ccdeaf39b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="visualize-hive-data-with-microsoft-power-bi-in-azure-hdinsight"></a>Visualizar dados de Hive com o Microsoft Power BI no Azure HDInsight

Saiba como conectar o Microsoft Power BI ao Azure HDInsight e visualizar os dados do Hive. Atualmente, o Power BI dá suporte apenas à conexão ODBC para HDInsight. Neste tutorial, você carrega os dados de uma tabela de Hive hivesampletable no Power BI. A tabela de Hive contém alguns dados de uso de telefone celular. Em seguida, você plota os dados de uso em um mapa mundial:

![Relatório de mapa de Power BI do HDInsight](./media/hdinsight-connect-power-bi/hdinsight-power-bi-visualization.png)

As informações também se aplicam ao novo tipo de cluster [Consulta Interativa](./hdinsight-hadoop-use-interactive-hive.md).

## <a name="prerequisites"></a>Pré-requisitos
Antes de prosseguir com este artigo, você deve ter os seguintes itens:

* **Cluster HDInsight**. O cluster pode ser um cluster HDInsight com Hive ou um cluster de Consulta Interativa recém-lançado. Para a criação de clusters, consulte [Criar cluster](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster).
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. Você pode baixar uma cópia do [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="create-hive-odbc-data-source"></a>Criar uma fonte de dados ODBC do Hive

Consulte [Criar uma fonte de dados ODBC do Hive](hdinsight-connect-excel-hive-odbc-driver.md#create-hive-odbc-data-source).

## <a name="load-data-from-hdinsight"></a>Carregar dados do HDInsight

A tabela de Hive hivesampletable acompanha todos os clusters HDInsight.

1. Entre no Power BI Desktop.
2. Clique no ícone **Início**, clique em **Obter Dados** na faixa de opções **Dados externos** e, em seguida, selecione **Mais**.

    ![Abrir dados do Power BI do HDInsight](./media/hdinsight-connect-power-bi/hdinsight-power-bi-open-odbc.png)
3. Do painel **Obter Dados**, clique em **Outros** na esquerda, clique em **ODBC** na direita e, em seguida, clique em **Conectar** na parte inferior.
4. Do painel **ODBC**, selecione o nome da fonte de dados que você criou na última seção e clique em **OK**.
5. Do painel **Navegador**, expanda **ODBC->HIVE->padrão**, selecione **hivesampletable** e, em seguida, clique em **Carregar**.

## <a name="visualize-date"></a>Visualizar dados

Continue do último procedimento.

1. No painel Visualizações, selecione **Mapa**.  Ele é um ícone de globo.

    ![O Power BI do HDInsight personaliza o relatório](./media/hdinsight-connect-power-bi/hdinsight-power-bi-customize.png)
2. No painel Campos, selecione **país** e **devicemake**. Você pode ver os dados plotados no mapa.
3. Expanda o mapa.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como visualizar os dados de HDInsight usando o Power BI.  Para saber mais, consulte os seguintes artigos:

* [Usar Zeppelin para executar consultas do Hive no Azure HDInsight ](./hdinsight-connect-hive-zeppelin.md).
* [Conectar o Excel ao HDInsight com o Driver ODBC do Microsoft Hive](./hdinsight-connect-excel-hive-odbc-driver.md).
* [Conectar o Excel ao Hadoop usando Power Query](./hdinsight-connect-excel-power-query.md).
* [Conectar-se ao Azure HDInsight e executar consultas Hive usando Ferramentas do Data Lake para Visual Studio](./hdinsight-hadoop-visual-studio-tools-get-started.md).
* [Use a Ferramenta do Azure HDInsight para Visual Studio Code](hdinsight-for-vscode.md).
* [Carregue os Dados no HDInsight](./hdinsight-upload-data.md).
