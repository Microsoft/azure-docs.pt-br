---
title: Visualizar dados da consulta interativa do Hive com o Power BI no Azure HDInsight
description: Usar o Microsoft Power BI para visualizar dados da consulta interativa do Hive do Azure HDInsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.openlocfilehash: 04af3f211674458c51bbb5cdbc6012833a790584
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190964"
---
# <a name="visualize-interactive-query-apache-hive-data-with-microsoft-power-bi-using-direct-query-in-azure-hdinsight"></a>Visualize dados do Apache Hive de Consulta Interativa com o Microsoft Power BI usando a consulta direta no Azure HDInsight

Este artigo descreve como conectar o Microsoft Power BI a clusters da consulta interativa do Azure HDInsight e visualizar os dados do Apache Hive usando a consulta direta. O exemplo fornecido carrega os dados de um `hivesampletable` tabela Hive para o Power BI. O `hivesampletable` tabela Hive contém alguns dados de uso do telefone celular. Em seguida, você cria gráficos com os dados de uso em um mapa mundial:

![Relatório de mapa de Power BI do HDInsight](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

Você pode aproveitar o [Driver ODBC do Apache Hive](../hadoop/apache-hadoop-connect-hive-power-bi.md) para fazer a importação por meio do conector ODBC genérico no Power BI Desktop. No entanto, não é recomendável para cargas de trabalho de BI considerando a natureza não interativo do mecanismo de consulta do Hive. [Os conectores do HDInsight Interactive Query](./apache-hadoop-connect-hive-power-bi-directquery.md) e do conector do [HDInsight Apache Spark](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) são opções melhores para seu desempenho.

## <a name="prerequisites"></a>Pré-requisitos
Antes de prosseguir com este artigo, você deve ter os seguintes itens:

* **Cluster HDInsight**. O cluster pode ser um cluster HDInsight com o Apache Hive ou um cluster recém-lançado de Consulta Interativa. Para a criação de clusters, consulte [Criar cluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)** . Você pode baixar uma cópia do [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="load-data-from-hdinsight"></a>Carregar dados do HDInsight

O `hivesampletable` tabela Hive vem com todos os clusters do HDInsight.

1. Inicie o Power BI Desktop.

2. Na barra de menus, navegue até **página inicial** > **obter dados** > **mais...** .

    ![Abrir dados do Power BI do HDInsight](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)

3. Dos **obter dados** janela, insira **hdinsight** na caixa de pesquisa.  

4. Resultados da pesquisa, selecione **consulta interativa do HDInsight**e, em seguida, selecione **Connect**.  Se você não vir **consulta interativa do HDInsight**, você precisará atualizar seu Power BI Desktop para a versão mais recente.

5. Selecione **Continue** para fechar o **conectando a um serviço de terceiros** caixa de diálogo.

6. No **consulta interativa do HDInsight** janela, insira as seguintes informações e, em seguida, selecione **Okey**:

    |Propriedade | Value |
    |---|---|
    |Servidor |Insira o nome do cluster, por exemplo *myiqcluster.azurehdinsight.net*.|
    |Banco de dados |Insira **padrão** para este artigo.|
    |Modo de conectividade de dados |Selecione **DirectQuery** para este artigo.|

    ![Consulta interativa do HDInsight, conexão com o DirectQuery do Power BI](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)

7. Insira as credenciais HTTP e, em seguida, selecione **Connect**. O nome de usuário padrão é **admin**.

8. Dos **Navigator** janela no painel esquerdo, selecione **hivesampletale**.

9. Selecione **carga** na janela principal.

    ![Consulta interativa do HDInsight, hivesampletable do Power BI](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data-on-a-map"></a>Visualizar dados em um mapa

Continue do último procedimento.

1. No painel de visualizações, selecione **mapa**, o ícone de globo. Um mapa genérico, em seguida, é exibida na janela principal.

    ![O Power BI do HDInsight personaliza o relatório](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)

2. No painel Campos, selecione **país** e **devicemake**. Um mapa-múndi com os pontos de dados aparece na janela principal após alguns instantes.

3. Expanda o mapa.

## <a name="next-steps"></a>Próximas etapas
Este artigo, você aprendeu como visualizar dados do HDInsight usando o Microsoft Power BI.  Para obter mais informações sobre visualização de dados, consulte os seguintes artigos:

* [Visualize os dados do Apache Hive com o Microsoft Power BI usando o ODBC no Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md). 
* [Use o Apache Zeppelin para executar consultas do Apache Hive no HDInsight do Azure](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Conectar o Excel ao HDInsight com o Driver ODBC do Microsoft Hive](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Conecte o Excel ao Apache Hadoop usando o Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md).
* [Conecte-se ao Azure HDInsight e execute consultas do Apache Hive usando o Data Lake Tools para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Use a Ferramenta do Azure HDInsight para Visual Studio Code](../hdinsight-for-vscode.md).
* [Carregue os Dados no HDInsight](./../hdinsight-upload-data.md).
