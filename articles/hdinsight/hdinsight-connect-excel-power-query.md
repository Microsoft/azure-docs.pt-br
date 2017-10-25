---
title: "Conectar o Excel ao Hadoop com o Power Query – Azure HDInsight | Microsoft Docs"
description: Saiba como aproveitar os componentes de business intelligence e usar o Power Query para Excel para acessar dados armazenados no Hadoop no HDInsight.
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 01ad2f90-7520-44d9-8c16-4d936faaff9b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jgao
ms.openlocfilehash: c8ba8743fbadc2c40762d555a8c573c741a75282
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="connect-excel-to-hadoop-by-using-power-query"></a>Conectar o Excel ao Hadoop usando Power Query
Um dos principais recursos da solução de big data da Microsoft é a integração dos componentes de BI (business intelligence) da Microsoft com os clusters Hadoop do HDInsight do Azure. Um dos principais exemplos é a capacidade de conectar o Excel com a conta de Armazenamento do Azure, que contém os dados associados ao seu cluster Hadoop usando o suplemento Microsoft Power Query para Excel. Este artigo explica como configurar e usar o Power Query para consultar dados associados a um cluster Hadoop gerenciado com o HDInsight.

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, você deve ter os seguintes itens:

* **Um cluster HDInsight**. Para configurar um, consulte [Introdução ao Azure HDInsight][hdinsight-get-started].
* **estação de trabalho** que esteja executando Windows 7, Windows Server 2008 R2 ou um sistema operacional posterior.
* **Office 2016, Office 2013 Professional Plus, Office 365 ProPlus, Excel 2013 autônomo ou Office 2010 Professional Plus**.

## <a name="install-power-query"></a>Instalar o Power Query
O Power Query pode importar dados que foram retornados ou que foram gerados por um trabalho Hadoop em execução em um cluster HDInsight.

No Excel 2016, o Power Query foi integrado na faixa de opções Dados na seção Obter e Transformar. Para versões mais antigas do Excel, baixe o Microsoft Power Query para Excel no [Centro de Download da Microsoft][powerquery-download] e instale-o.

## <a name="import-hdinsight-data-into-excel"></a>Importar dados do HDInsight para o Excel
O suplemento do Power Query para Excel facilita a importação de dados de seu cluster HDInsight para o Excel onde ferramentas de BI, como o PowerPivot e o Power Map, podem ser usadas para inspecionar, analisar e apresentar os dados.

**Para importar dados de um cluster HDInsight**

1. Abra o Excel.
2. Crie uma nova pasta de trabalho em branco.
3. Execute as etapas a seguir com base na versão do Excel:

    - Excel 2016

        - Clique no menu **Dados**, clique em **Obter Dados** da faixa de opções **Obter e Transformar Dados**, clique em **Do Azure** e, em seguida, em **Do Azure HDInsight (HDFS)**.

        ![HDI.PowerQuery.SelectHdiSource](./media/hdinsight-connect-excel-power-query/hdi.powerquery.selecthdisource.excel2016.png)

    - Excel 2013/2010

        - Clique no menu **Power Query**, clique em **Do Azure** e, em seguida, clique em **Do Microsoft Azure HDInsight**.
   
        ![HDI.PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]
       
        **Observação:** se você não vir o menu **Power Query**, vá para **Arquivo** > **Opções** > **Suplementos** e selecione **Suplementos COM** na caixa suspensa **Gerenciar** na parte inferior da página. Selecione o botão **Ir...** e verifique se a caixa do suplemento Power Query para Excel está marcada.
       
        **Observação**: o Power Query também permite que você importe dados do HDFS clicando **De outras fontes**.
4. Para **Nome da conta**, insira o nome da conta de Armazenamento de Blobs do Azure associada ao cluster e, em seguida, clique em **OK**. Essa conta pode ser a [conta de armazenamento padrão](hdinsight-administer-use-management-portal.md#find-the-default-storage-account) ou uma conta de armazenamento vinculada.  O formato é *https://&lt;StorageAccountName>.blob.core.windows.net/*.
5. Para **Chave de Conta**, insira a chave para a conta de Armazenamento de Blobs e clique em **Salvar**. (Você precisa inserir as informações da conta somente na primeira vez que acessar este repositório.)
6. No painel **Navegador** à esquerda do Editor de Consulta, clique duas vezes no nome do contêiner de armazenamento de Blob. Por padrão, o nome do contêiner é igual ao nome do cluster.
7. Localize **HiveSampleData.txt** na coluna **Nome** (o caminho da pasta é **../hive/warehouse/hivesampletable/**) e clique em **Binário** à esquerda de HiveSampleData.txt. HiveSampleData.txt acompanha todo o cluster. Se desejar, você pode usar seu próprio arquivo.
   
    ![HDI.PowerQuery.ImportData][image-hdi-powerquery-importdata]
8. Se desejar, você pode renomear os nomes das colunas. Quando estiver pronto, clique em **Fechar e Carregar**.  Os dados foram carregados em sua pasta de trabalho:
   
    ![HDI.PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como usar o Power Query para recuperar dados do HDInsight para o Excel. Da mesma forma, você pode recuperar dados do HDInsight no banco de dados SQL do Azure. Também é possível carregar dados para o HDInsight. Para saber mais, consulte os seguintes artigos:

* [Visualizar dados de Hive com o Microsoft Power BI no Azure HDInsight](./hdinsight-connect-hive-power-bi.md).
* [Usar Zeppelin para executar consultas do Hive no Azure HDInsight ](./hdinsight-connect-hive-zeppelin.md).
* [Conectar o Excel ao HDInsight com o Driver ODBC do Microsoft Hive](./hdinsight-connect-excel-hive-odbc-driver.md).
* [Conectar-se ao Azure HDInsight e executar consultas Hive usando Ferramentas do Data Lake para Visual Studio](./hdinsight-hadoop-visual-studio-tools-get-started.md).
* [Use a Ferramenta do Azure HDInsight para Visual Studio Code](hdinsight-for-vscode.md).
* [Carregue os dados no HDInsight](./hdinsight-upload-data.md).

[image-hdi-powerquery-hdi-source]: ./media/hdinsight-connect-excel-power-query/hdi.powerquery.selecthdisource.png
[image-hdi-powerquery-importdata]: ./media/hdinsight-connect-excel-power-query/hdi.powerquery.importdata.png
[image-hdi-powerquery-imported-table]: ./media/hdinsight-connect-excel-power-query/hdi.powerquery.importedtable.PNG

[powerquery-download]: http://go.microsoft.com/fwlink/?LinkID=286689
