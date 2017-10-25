---
title: "Conectar o Excel ao Hadoop com o driver ODBC do Hive – Azure HDInsight | Microsoft Docs"
description: Saiba como configurar e usar o driver ODBC do Microsoft Hive para Excel para consultar dados em clusters HDInsight no Microsoft Excel.
keywords: hadoop excel,hive excel,hive odbc
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
tags: azure-portal
editor: cgronlun
ms.assetid: a7665a14-0211-4521-b3e7-3b26e8029cc0
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/22/2017
ms.author: jgao
ms.openlocfilehash: 65a93003eb829a2eaf4684d2a6ecacf74b0adc75
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="connect-excel-to-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Conectar o Excel ao Hadoop no Azure HDInsight com o driver ODBC do Microsoft Hive

[!INCLUDE [ODBC-JDBC-selector](../../includes/hdinsight-selector-odbc-jdbc.md)]

A solução de Big Data da Microsoft integra componentes do Microsoft Business Intelligence (BI) com os clusters do Apache Hadoop que foram implantados pelo Azure HDInsight. Um exemplo dessa integração é a capacidade de conectar o Excel ao data warehouse do Hive de um cluster Hadoop no HDInsight usando o driver ODBC do Microsoft Hive.

Também é possível conectar os dados associados a um cluster HDInsight e outras fontes de dados, incluindo outros clusters Hadoop (não HDInsight), do Excel usando o suplemento Microsoft Power Query para Excel. Para obter informações sobre como instalar e usar o Power Query, confira [Conectar o Excel ao HDInsight com o Power Query][hdinsight-power-query].

> [!NOTE]
> Embora as etapas deste artigo possam ser usadas com um cluster HDInsight com base no Linux ou Windows, é preciso ter o Windows para a estação de trabalho cliente.
> 
> 

**Pré-requisitos**:

Antes de começar este artigo, você deve ter os seguintes itens:

* **Um cluster HDInsight**. Para criar um, confira [Introdução ao Azure HDInsight][hdinsight-get-started].
* **estação de trabalho** com Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone ou Office 2010 Professional Plus.

## <a name="install-microsoft-hive-odbc-driver"></a>Instalar o driver ODBC do Microsoft Hive
Baixe e instale o driver ODBC do Microsoft Hive no [Centro de download][hive-odbc-driver-download].

Este driver pode ser instalado em versões de 32 ou 64 bits do Windows 7, Windows 8, Windows 10, Windows Server 2008 R2 e Windows Server 2012. O driver permite a conexão com o Azure HDInsight (versão 1.6 ou posterior) e o Emulador do Azure HDInsight (1.0.0.0 e posterior). Você deve instalar a versão que corresponda à versão do aplicativo onde você usa o driver ODBC. Para este tutorial, o driver é usado do Office Excel.

## <a name="create-hive-odbc-data-source"></a>Criar uma fonte de dados ODBC do Hive
As etapas a seguir mostram como criar uma fonte de dados ODBC do Hive.

1. No Windows 8 ou no Windows 10, pressione a tecla Windows para abrir a tela Iniciar e digite **fontes de dados**.
2. Clique em **Configurar fontes de dados ODBC (32 bits)** ou **Configurar fontes de dados ODBC (64 bits)**, dependendo da sua versão do Office. Se estiver usando o Windows 7, escolha **Fontes de Dados ODBC (32 bits)** ou **Fontes de Dados ODBC (64 bits)** em **Ferramentas Administrativas**. Você deverá ver a caixa de diálogo **Administrador de Fonte de Dados ODBC**.
   
    ![Administrador de fonte de dados ODBC](./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png "Configurar um DSN usando o Administrador de Fonte de Dados ODBC")

3. No DNS do Usuário, clique em **Adicionar** para abrir o assistente **Criar Nova Fonte de Dados**.
4. Selecione **Driver ODBC do Microsoft Hive** e clique em **Concluir**. Você deverá ver a caixa de diálogo **Configuração DNS do Driver ODBC do Microsoft Hive**.
5. Digite ou selecione os valores a seguir:
   
   | Propriedade | Descrição |
   | --- | --- |
   |  Nome da fonte de dados |Forneça um nome para a sua fonte de dados |
   |  Host |Digite &lt;HDInsightClusterName>.azurehdinsight.net. Por exemplo, meu_Cluster_HDI.azurehdinsight.net |
   |  Port |Use <strong>443</strong>. (Essa porta foi alterada de 563 para 443.) |
   |  Banco de dados |Use <strong>Padrão</strong>. |
   |  Mecanismo |Selecione <strong>Serviço do Azure HDInsight</strong> |
   |  Nome de usuário |Insira o nome de usuário HTTP do cluster HDInsight. O nome de usuário padrão é <strong>admin</strong>. |
   |  Senha |Insira a senha do usuário do cluster HDInsight. |
   
    </table>
   
    Há alguns parâmetros importantes a serem lembrados ao clicar em **Opções Avançadas**.
   
   | Parâmetro | Descrição |
   | --- | --- |
   |  Use Consulta Nativa |Quando selecionado, o driver ODBC NÃO tenta converter TSQL em HiveQL. Você deverá usar essa opção somente se estiver 100% certo de que está enviando instruções HiveQL puras. Ao conectar-se ao SQL Server ou ao Banco de Dados SQL do Azure, deixe-a desmarcada. |
   |  Linhas buscadas por bloco |Ao buscar uma grande quantidade de registros, o ajuste desse parâmetro poderá ser necessário para garantir o desempenho ideal. |
   |  Comprimento de coluna de cadeia de caracteres padrão, Comprimento da coluna binária e Escala da coluna decimal |Os tamanhos e as precisões dos tipos de dados podem afetar a maneira como os dados são retornados. Eles farão com que informações incorretas sejam retornadas devido à perda de precisão e/ou truncamento. |

    ![Opções avançadas](./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png "Opções de configuração de DSN avançadas")

1. Clique em **Testar** para testar a fonte de dados. Quando a fonte de dados estiver configurada corretamente, será mostrado *TESTES CONCLUÍDOS COM ÊXITO!*.
2. Clique em **OK** para fechar o diálogo Testar. A nova fonte de dados deve estar listada no **Administrador de Fonte de Dados ODBC**.
3. Clique em **OK** para sair do assistente.

## <a name="import-data-into-excel-from-hdinsight"></a>Importar dados do HDInsight para o Excel
As etapas a seguir descrevem a maneira de importar dados de uma tabela Hive em uma pasta de trabalho do Excel usando a fonte de dados ODBC que você criou na seção anterior.

1. Abra uma pasta de trabalho nova ou existente no Excel.
2. Na guia **Dados**, clique em **Obter Dados**, em **De Outras Fontes** e em **Do ODBC** para iniciar o **Assistente para Conexão de Dados**.
   
    ![Abrir assistente de conexão de dados](./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png "Abrir assistente de conexão de dados")
4. Selecione o nome da fonte de dados que você criou na última seção e clique em **OK**.
5. Insira o nome de usuário do Hadoop (o nome padrão é admin) e a senha e, em seguida, clique em **Conectar**.
6. No Navegador, expanda **HIVE**, expanda **padrão**, clique em **hivesampletable** e clique em **Carregar**. Leva alguns segundos para que os dados sejam importados para o Excel.

    ![Navegador do ODBC Hive do HDInsight](./media/hdinsight-connect-excel-hive-ODBC-driver/hdinsight.hive.odbc.navigator.png "Assistente para conexão de dados aberto")


## <a name="next-steps"></a>Próximas etapas
Neste artigo você aprendeu a usar o driver ODBC do Microsoft Hive para recuperar dados do Serviço do HDInsight no Excel. Da mesma forma, você pode recuperar dados do Serviço do HDInsight no Banco de Dados SQL. Também é possível carregar dados em um Serviço do HDInsight. Para obter mais informações, consulte:

* [Visualizar dados de Hive com o Microsoft Power BI no Azure HDInsight](./hdinsight-connect-hive-power-bi.md).
* [Usar Zeppelin para executar consultas do Hive no Azure HDInsight ](./hdinsight-connect-hive-zeppelin.md).
* [Conectar o Excel ao Hadoop usando Power Query](./hdinsight-connect-excel-power-query.md).
* [Conectar-se ao Azure HDInsight e executar consultas Hive usando Ferramentas do Data Lake para Visual Studio](./hdinsight-hadoop-visual-studio-tools-get-started.md).
* [Use a Ferramenta do Azure HDInsight para Visual Studio Code](hdinsight-for-vscode.md).
* [Carregue os dados no HDInsight](./hdinsight-upload-data.md).

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-get-started]: hdinsight-hadoop-tutorial-get-started-windows.md

[hive-odbc-driver-download]: http://go.microsoft.com/fwlink/?LinkID=286698


