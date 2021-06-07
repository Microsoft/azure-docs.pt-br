---
title: Consulte dados de hive de consulta interativa com Power BI no Azure HDInsight
description: Usar o Microsoft Power BI para visualizar dados da consulta interativa do Hive do Azure HDInsight
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/17/2019
ms.openlocfilehash: 05199968339329632c2e68e9604e3f5308e8b12b
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104871649"
---
# <a name="visualize-interactive-query-apache-hive-data-with-microsoft-power-bi-using-direct-query-in-hdinsight"></a>Visualizar dados interativos Apache Hive de consulta com o Microsoft Power BI usando a consulta direta no HDInsight

Este artigo descreve como conectar o Microsoft Power BI a clusters da consulta interativa do Azure HDInsight e visualizar os dados do Apache Hive usando a consulta direta. O exemplo fornecido carrega os dados de uma `hivesampletable` tabela do hive para Power bi. A `hivesampletable` tabela Hive contém alguns dados de uso de telefone celular. Em seguida, você cria gráficos com os dados de uso em um mapa mundial:

:::image type="content" source="./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png" alt-text="Relatório de mapa de Power BI do HDInsight" border="true":::

Você pode aproveitar o [Driver ODBC do Apache Hive](../hadoop/apache-hadoop-connect-hive-power-bi.md) para fazer a importação por meio do conector ODBC genérico no Power BI Desktop. No entanto, não é recomendável para cargas de trabalho de BI considerando a natureza não interativo do mecanismo de consulta do Hive. [Os conectores do HDInsight Interactive Query](./apache-hadoop-connect-hive-power-bi-directquery.md) e do conector do [HDInsight Apache Spark](/power-bi/spark-on-hdinsight-with-direct-connect) são opções melhores para seu desempenho.

## <a name="prerequisites"></a>Pré-requisitos
Antes de prosseguir com este artigo, você deve ter os seguintes itens:

* **Cluster HDInsight**. O cluster pode ser um cluster HDInsight com o Apache Hive ou um cluster recém-lançado de Consulta Interativa. Para a criação de clusters, consulte [Criar cluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md).
* **[Microsoft Power bi desktop](https://powerbi.microsoft.com/desktop/)**. Você pode baixar uma cópia do [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="load-data-from-hdinsight"></a>Carregar dados do HDInsight

A `hivesampletable` tabela Hive vem com todos os clusters HDInsight.

1. Inicie o Power BI Desktop.

2. Na barra de menus, navegue até **página inicial**  >  **obter dados**  >  **mais...**.

    :::image type="content" source="./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png" alt-text="O HDInsight Power BI obter mais dados" border="true":::

3. Na janela **obter dados** , insira **hdinsight** na caixa de pesquisa.  

4. Nos resultados da pesquisa, selecione **consulta interativa do HDInsight** e, em seguida, selecione **conectar**.  Se você não vir a **consulta interativa do HDInsight**, precisará atualizar seu Power bi desktop para a versão mais recente.

5. Selecione **continuar** para fechar a caixa de diálogo **conectando a um serviço** de terceiros.

6. Na janela de **consulta interativa do HDInsight** , insira as seguintes informações e, em seguida, selecione **OK**:

    |Propriedade | Valor |
    |---|---|
    |Servidor |Insira o nome do cluster, por exemplo, *myiqcluster.azurehdinsight.net*.|
    |Banco de dados |Insira o **padrão** para este artigo.|
    |Modo de Conectividade de Dados |Selecione **DirectQuery** para este artigo.|

    :::image type="content" source="./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png" alt-text="Consulta interativa do HDInsight, conexão com o DirectQuery do Power BI" border="true":::

7. Insira as credenciais HTTP e, em seguida, selecione **conectar**. O nome de usuário padrão é **admin**.

8. Na janela **navegador** no painel esquerdo, selecione **hivesampletale**.

9. Selecione **carregar** na janela principal.

    :::image type="content" source="./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png" alt-text="Consulta interativa do HDInsight, hivesampletable do Power BI" border="true":::

## <a name="visualize-data-on-a-map"></a>Visualizar dados em um mapa

Continue do último procedimento.

1. No painel visualizações, selecione **mapa**, o ícone de globo. Um mapa genérico aparece na janela principal.

    :::image type="content" source="./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png" alt-text="O Power BI do HDInsight personaliza o relatório" border="true":::

2. No painel Campos, selecione **país** e **devicemake**. Um mapa mundial com os pontos de dados aparece na janela principal após alguns instantes.

3. Expanda o mapa.

## <a name="next-steps"></a>Próximas etapas
Este artigo, você aprendeu como visualizar dados do HDInsight usando o Microsoft Power BI.  Para obter mais informações sobre visualização de dados, consulte os seguintes artigos:

* [Visualize Apache Hive dados com o Microsoft Power bi usando ODBC no Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md). 
* [Use o Apache Zeppelin para executar Apache Hive consultas no Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Conectar o Excel ao HDInsight com o Driver ODBC do Microsoft Hive](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Conecte o Excel ao Apache Hadoop usando Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md).
* [Conecte-se ao Azure HDInsight e execute consultas do Apache Hive usando o Data Lake Tools para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Use a Ferramenta do Azure HDInsight para Visual Studio Code](../hdinsight-for-vscode.md).
* [Carregar dados no HDInsight](./../hdinsight-upload-data.md).