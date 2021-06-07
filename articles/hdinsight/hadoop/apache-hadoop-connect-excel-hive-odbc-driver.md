---
title: Excel & Apache Hadoop com driver ODBC (Open Database Connectivity)-Azure HDInsight
description: Saiba como configurar e usar o driver ODBC do Microsoft Hive para Excel para consultar dados em clusters HDInsight no Microsoft Excel.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: c7125e25c9716a9934c97f9f8f85666d264d5f89
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866940"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Conecte o Excel ao Apache Hadoop no HDInsight do Azure com o driver ODBC do Microsoft Hive

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

A solução de Big data da Microsoft integra componentes de BI (Business Intelligence) da Microsoft com clusters Apache Hadoop implantados no HDInsight. Um exemplo é a capacidade de conectar o Excel ao hive data warehouse de um cluster Hadoop. Conecte-se usando o driver ODBC (Microsoft Hive Open Database Connectivity).

Você pode conectar os dados associados a um cluster HDInsight do Excel com o suplemento do Microsoft Power Query para Excel. Para obter mais informações, consulte [conectar o Excel ao HDInsight com o Power Query](./apache-hadoop-connect-excel-power-query.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, você deve ter os seguintes itens:

* Cluster Hadoop do HDInsight. Para criar um, confira [Introdução ao Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* Uma estação de trabalho com o Office 2010 Professional Plus ou posterior ou o Excel 2010 ou posterior.

## <a name="install-microsoft-hive-odbc-driver"></a>Instalar o driver ODBC do Microsoft Hive

Baixe e instale o [driver ODBC do Microsoft Hive](https://www.microsoft.com/download/details.aspx?id=40886). Escolha a versão que corresponde à versão do aplicativo em que você usará o driver ODBC.  Para este artigo, o driver é usado para o Office Excel.

## <a name="create-apache-hive-odbc-data-source"></a>Criar fonte de dados ODBC do Apache Hive

As etapas a seguir mostram como criar uma fonte de dados ODBC do Hive.

1. No Windows, navegue até **iniciar > ferramentas administrativas do windows > fontes de dados ODBC (32 bits)/(64 bits)**.  Essa ação abre a janela **administrador de fonte de dados ODBC** .

   :::image type="content" source="./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-datasourceadmin1.png" alt-text="Administrador de fonte de dados ODBC" border="true":::

1. Na guia **DSN de Usuário**, selecione **Adicionar** para abrir a janela **Criar Nova Fonte de Dados**.

1. Selecione **Driver ODBC do Microsoft Hive** e selecione **Concluir** para abrir a janela **Configuração de DNS do Driver ODBC do Microsoft Hive**.

1. Digite ou selecione os valores a seguir:

   | Propriedade | Descrição |
   | --- | --- |
   |  Nome da Fonte de Dados |Forneça um nome para a sua fonte de dados |
   |  Hosts |Digite `HDInsightClusterName.azurehdinsight.net`. Por exemplo, `myHDICluster.azurehdinsight.net`. Observação: `HDInsightClusterName-int.azurehdinsight.net` o tem suporte desde que a VM do cliente esteja emparelhada com a mesma rede virtual. |
   |  Porta |Use **443**. (Essa porta foi alterada de 563 para 443.) |
   |  Banco de dados |Usar **padrão**. |
   |  Mecanismo |Selecione **Serviço do Microsoft Azure HDInsight** |
   |  Nome do Usuário |Insira o nome de usuário HTTP do cluster HDInsight. O nome de usuário padrão é **admin**. |
   |  Senha |Insira a senha do usuário do cluster HDInsight. Marque a caixa de seleção **Salvar Senha (Criptografada)**.|

1. Opcional: selecione **Opções avançadas...**  

   | Parâmetro | Descrição |
   | --- | --- |
   |  Use Consulta Nativa |Quando selecionado, o driver ODBC NÃO tenta converter TSQL em HiveQL. Você deve usá-lo somente se você for 100% certeza de que está enviando instruções HiveQL puras. Ao conectar-se ao SQL Server ou ao Banco de Dados SQL do Azure, deixe-a desmarcada. |
   |  Linhas buscadas por bloco |Ao buscar uma grande quantidade de registros, o ajuste desse parâmetro poderá ser necessário para garantir o desempenho ideal. |
   |  Comprimento de coluna de cadeia de caracteres padrão, Comprimento da coluna binária e Escala da coluna decimal |Os tamanhos e as precisões dos tipos de dados podem afetar a maneira como os dados são retornados. Eles fazem com que informações incorretas sejam retornadas devido à perda de precisão e, ou truncamento. |

    :::image type="content" source="./media/apache-hadoop-connect-excel-hive-odbc-driver/hiveodbc-datasource-advancedoptions1.png" alt-text="Opções de configuração avançada de DSN" border="true":::

1. Selecione **Testar** para testar a fonte de dados. Quando a fonte de dados estiver configurada corretamente, o resultado do teste mostrará **êxito!**

1. Selecione **OK** para fechar a janela de teste.  

1. Selecione **OK** para fechar a janela **Configuração de DNS do Driver ODBC do Microsoft Hive**.  

1. Selecione **OK** para fechar a janela **Administrador de Fonte de Dados ODBC**.  

## <a name="import-data-into-excel-from-hdinsight"></a>Importar dados do HDInsight para o Excel

As etapas a seguir descrevem a maneira de importar dados de uma tabela Hive em uma pasta de trabalho do Excel usando a fonte de dados ODBC que você criou na seção anterior.

1. Abra uma pasta de trabalho nova ou existente no Excel.

2. Na guia **Dados**, navegue até **Obter Dados** > **De Outras Fontes** > **Do ODBC** para abrir a janela **Do ODBC**.

   :::image type="content" source="./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-excel-dataconnection1.png" alt-text="Abrir assistente de conexão de dados do Excel" border="true":::

3. Na lista suspensa, selecione o nome da fonte de dados que você criou na última seção e, em seguida, selecione **OK**.

4. Para o primeiro uso, uma caixa de diálogo do **driver ODBC** será aberta. Selecione **Windows** no menu à esquerda. Em seguida, selecione **conectar** para abrir a janela **navegador** .

5. De **Navegador**, navegue até **HIVE** > **padrão** > **hivesampletable** e selecione **Carregar**. Leva alguns minutos para que os dados sejam importados para o Excel.

   :::image type="content" source="./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight-hive-odbc-navigator.png" alt-text="Navegador ODBC do hive do Excel do HDInsight" border="true":::

## <a name="next-steps"></a>Próximas etapas

Neste artigo você aprendeu a usar o driver ODBC do Microsoft Hive para recuperar dados do Serviço do HDInsight no Excel. Da mesma forma, você pode recuperar dados do Serviço do HDInsight no Banco de Dados SQL. Também é possível carregar dados em um serviço do HDInsight. Para obter mais informações, consulte:

* [Visualize Apache Hive dados com o Microsoft Power bi no Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualize dados de hive de consulta interativa com Power bi no Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Conecte o Excel ao Apache Hadoop usando Power Query](apache-hadoop-connect-excel-power-query.md).
* [Conecte-se ao Azure HDInsight e execute consultas do Apache Hive usando o Data Lake Tools para Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).