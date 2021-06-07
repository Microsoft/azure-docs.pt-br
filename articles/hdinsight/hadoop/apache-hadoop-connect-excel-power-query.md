---
title: Conectar o Excel ao Apache Hadoop com o Power Query - Azure HDInsight
description: Saiba como aproveitar os componentes de business intelligence e usar o Power Query para Excel para acessar dados armazenados no Hadoop no HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 13862e642c6a91fe6f3c635df2efde91672ecbad
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866804"
---
# <a name="connect-excel-to-apache-hadoop-by-using-power-query"></a>Conectar o Excel ao Apache Hadoop com o Power Query

Um dos principais recursos da solução de big data da Microsoft é a integração dos componentes de BI (business intelligence) da Microsoft com clusters Apache Hadoop no Azure HDInsight. Um dos principais exemplos é a capacidade de conectar o Excel com a conta de Armazenamento do Azure, que contém os dados associados ao seu cluster Hadoop usando o suplemento Microsoft Power Query para Excel. Este artigo explica como configurar e usar o Power Query para consultar dados associados a um cluster Hadoop gerenciado com o HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Hadoop no HDInsight. Consulte [Introdução ao HDInsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).
* Uma estação de trabalho que esteja executando o Windows 10, 7, Windows Server 2008 R2 ou um sistema operacional posterior.
* Microsoft 365 aplicativos para Enterprise, Office 2016, Office 2013 Professional Plus, Excel 2013 standalone ou Office 2010 Professional Plus.

## <a name="install-microsoft-power-query"></a>Instalar o Microsoft Power Query

O Power Query pode importar dados que foram retornados ou que foram gerados por um trabalho Hadoop em execução em um cluster HDInsight.

No Excel 2016, o Power Query foi integrado na faixa de opções Dados na seção Obter e Transformar. Para versões mais antigas do Excel, baixe o Microsoft Power Query para Excel no [Centro de Download da Microsoft](https://go.microsoft.com/fwlink/?LinkID=286689) e instale-o.

## <a name="import-hdinsight-data-into-excel"></a>Importar dados do HDInsight para o Excel

O suplemento do Power Query para Excel facilita a importação de dados de seu cluster HDInsight para o Excel onde ferramentas de BI, como o PowerPivot e o Power Map, podem ser usadas para inspecionar, analisar e apresentar os dados.

1. Inicie o Excel.

1. Crie uma nova pasta de trabalho em branco.

1. Execute as etapas a seguir com base na versão do Excel:

   * Excel 2016

     * Selecione > **dados**  >  **obter dados**  >  **do Azure**  >  **do Azure HDInsight (HDFS)**.

       :::image type="content" source="./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource-excel2016.png" alt-text="HDI. PowerQuery. SelectHdiSource. 2016" border="true":::

   * Excel 2013/2010

     * Selecione **Power Query**  >  **do Azure**  >  **do Microsoft Azure HDInsight**.

       :::image type="content" source="./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource.png" alt-text="HDI.PowerQuery.SelectHdiSource" border="true":::

       **Observação:** Se você não vir o menu de **Power Query** , vá para **arquivo**  >  **Opções**  >  **suplementos** e selecione suplementos de **com** na caixa suspensa **gerenciar** na parte inferior da página. Selecione o botão **Ir...** e verifique se a caixa do suplemento Power Query para Excel está marcada.

       **Observação:** Power Query também permite importar dados do HDFS selecionando **de outras fontes**.

1. No diálogo **Azure HDInsight (HDFS)** , na caixa de texto **nome da conta ou URL** , digite o nome da conta de armazenamento de BLOBs do Azure associada ao cluster. Depois, selecione **OK**. Essa conta pode ser a conta de armazenamento padrão ou uma conta de armazenamento vinculada.  O formato é `https://StorageAccountName.blob.core.windows.net/`.

1. Para **chave de conta**, insira a chave para a conta de armazenamento de BLOBs e, em seguida, selecione **conectar**. (Você precisa inserir as informações da conta somente na primeira vez que acessar este repositório.)

1. No painel **navegador** à esquerda do editor de consultas, clique duas vezes no nome do contêiner de armazenamento de blob associado ao cluster. Por padrão, o nome do contêiner é igual ao nome do cluster.

1. Localize **HiveSampleData.txt** na coluna **nome** (o caminho da pasta é **.. /Hive/warehouse/hivesampletable/**) e, em seguida, selecione **Binary** à esquerda de HiveSampleData.txt. HiveSampleData.txt acompanha todo o cluster. Se desejar, você pode usar seu próprio arquivo.

    :::image type="content" source="./media/apache-hadoop-connect-excel-power-query/powerquery-importdata.png" alt-text="Importar dados do HDI Excel Power Query" border="true":::

1. Se desejar, você pode renomear os nomes das colunas. Quando estiver pronto, selecione **fechar & carregar**.  Os dados foram carregados em sua pasta de trabalho:

    :::image type="content" source="./media/apache-hadoop-connect-excel-power-query/powerquery-importedtable.png" alt-text="Tabela importada do HDI Excel Power Query" border="true":::

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como usar o Power Query para recuperar dados do HDInsight para o Excel. Da mesma forma, você pode recuperar dados do HDInsight no banco de dados SQL do Azure. Também é possível carregar dados no HDInsight. Confira os seguintes artigos para saber mais:

* [Visualize Apache Hive dados com o Microsoft Power bi no Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualize dados de hive de consulta interativa com Power bi no Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Use o Apache Zeppelin para executar Apache Hive consultas no Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Conectar o Excel ao HDInsight com o Driver ODBC do Microsoft Hive](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Conecte-se ao Azure HDInsight e execute consultas do Apache Hive usando o Data Lake Tools para Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Use a Ferramenta do Azure HDInsight para Visual Studio Code](../hdinsight-for-vscode.md).
* [Carregar dados no HDInsight](./../hdinsight-upload-data.md).
