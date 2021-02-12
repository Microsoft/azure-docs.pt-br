---
title: Apache Hive & Data Lake Tools for Visual Studio – Azure HDInsight
description: Saiba como usar as ferramentas de Data Lake para Visual Studio para executar consultas do Apache Hive com Apache Hadoop no Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 3e54991f6afd2ace19bf7b70e57bda6f551f7780
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943163"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Executar consultas do Apache Hive usando as ferramentas do Data Lake para Visual Studio

Saiba como usar as ferramentas do Data Lake para Visual Studio para consultar o Apache Hive. As ferramentas de Data Lake permitem que você facilmente crie, envie e monitore consultas de Hive para Apache Hadoop no Azure HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Hadoop no HDInsight. Para obter informações sobre como criar esse item, consulte [criar Apache Hadoop cluster no Azure HDInsight usando o modelo do Resource Manager](./apache-hadoop-linux-tutorial-get-started.md).

* [Do Visual Studio](https://visualstudio.microsoft.com/vs/). As etapas neste artigo usam o Visual Studio 2019.

* Ferramentas do HDInsight para Visual Studio ou ferramentas do Azure Data Lake para Visual Studio. Para obter informações sobre como instalar e configurar as ferramentas, consulte [instalar o data Lake Tools para Visual Studio](apache-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio).

## <a name="run-apache-hive-queries-using-the-visual-studio"></a> Executar consultas Apache Hive usando o Visual Studio

Você tem duas opções para criar e executar consultas do Hive:

* Criar consultas ad hoc.
* Crie um aplicativo do hive.

### <a name="create-an-ad-hoc-hive-query"></a>Criar uma consulta de Hive ad hoc

Consultas ad hoc podem ser executadas no modo de **lote** ou **interativo** .

1. Inicie o **Visual Studio** e selecione **continuar sem código**.

2. Em **Gerenciador de servidores**, clique com o botão direito do mouse em **Azure**, selecione **conectar a Microsoft Azure assinatura...** e conclua o processo de entrada.

3. Expanda **HDInsight**, clique com o botão direito do mouse no cluster em que você deseja executar a consulta e, em seguida, selecione **gravar uma consulta do hive**.

4. Insira a seguinte consulta de Hive:

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. Selecione **Executar**. O modo de execução usa como padrão a **interatividade**.

    ![Executar consulta de Hive interativa, Visual Studio](./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png)

6. Para executar a mesma consulta no modo de **lote** , alterne a lista suspensa de **interativo** para **lote**. O botão de execução muda de **executar** para **Enviar**.

    ![Enviar consulta do hive do lote, Visual Studio](./media/apache-hadoop-use-hive-visual-studio/visual-studio-batch-query.png)

    O editor do Hive é compatível com o IntelliSense. Agora as Ferramentas do Data Lake para Visual Studio dão suporte à obtenção de metadados remotos quando você edita o script do Hive. Por exemplo, se você digitar `SELECT * FROM` , o IntelliSense listará todos os nomes de tabela sugeridos. Quando um nome de tabela for especificado, o IntelliSense listará os nomes de coluna. As ferramentas dão suporte a quase todas as instruções DML Hive, subconsultas e UDFs internos. O IntelliSense sugere apenas os metadados dos clusters selecionados na Barra de Ferramentas do HDInsight.

7. Na barra de ferramentas de consulta (a área abaixo da guia consulta e acima do texto da consulta), selecione **Enviar** ou selecione a seta suspensa ao lado de **Enviar** e escolha **avançado** na lista suspensa. Se você selecionar a última opção,

8. Se você selecionou a opção de envio avançado, configure **nome do trabalho**, **argumentos**, **configurações adicionais** e **diretório de status** na caixa de diálogo **Enviar script** . Em seguida, selecione **Enviar**.

    ![Caixa de diálogo Enviar script, consulta do hive do HDInsight Hadoop](./media/apache-hadoop-use-hive-visual-studio/vs-tools-submit-jobs-advanced.png)

### <a name="create-a-hive-application"></a>Criar um aplicativo Hive

Para executar uma consulta de Hive criando um aplicativo Hive, siga estas etapas:

1. Abra o **Visual Studio**.

2. Na janela **Iniciar**, selecione **Criar projeto**.

3. Na janela **criar um novo projeto** , na caixa **Pesquisar modelos** , insira *Hive*. Em seguida, escolha **aplicativo Hive** e selecione **Avançar**.

4. Na janela **configurar seu novo projeto** , insira um **nome de projeto**, selecione ou crie um **local** para o novo projeto e, em seguida, selecione **criar**.

5. Abra o arquivo **Script.hql** criado com esse projeto e cole as seguintes instruções HiveQL:

    ```hql
    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    Essas instruções realizam as seguintes ações:

    * `DROP TABLE`: Exclui a tabela se ela existir.

    * `CREATE EXTERNAL TABLE`: cria uma nova tabela 'externa' no Hive. Tabelas externas só armazenam a definição da tabela no Hive. (Os dados são deixados no local original.)

        > [!NOTE]  
        > As tabelas externas devem ser usadas quando você espera que os dados subjacentes sejam atualizados por uma fonte externa, como um trabalho MapReduce ou um serviço do Azure.
        >
        > Remover uma tabela externa **não** exclui os dados, somente a definição de tabela.

    * `ROW FORMAT`: informa ao Hive como os dados são formatados. Nesse caso, os campos em cada log são separados por um espaço.

    * `STORED AS TEXTFILE LOCATION`: Informa ao hive que os dados são armazenados no diretório de *exemplo/dados* e que são armazenados como texto.

    * `SELECT`: Seleciona uma contagem de todas as linhas em que `t4` a coluna contém o valor `[ERROR]` . Essa instrução retorna um valor de `3` , porque três linhas contêm esse valor.

    * `INPUT__FILE__NAME LIKE '%.log'`: Informa ao hive para retornar apenas dados de arquivos que terminam em. log. Essa cláusula restringe a pesquisa ao arquivo *Sample. log* que contém os dados.

6. Na barra de ferramentas do arquivo de consulta (que tem aparência semelhante à barra de ferramentas de consulta ad hoc), selecione o cluster HDInsight que você deseja usar para esta consulta. Em seguida, altere **interativo** para **lote** (se necessário) e selecione **Enviar** para executar as instruções como um trabalho do hive.

   O **Resumo do Trabalho do Hive** aparecerá e exibirá informações sobre o trabalho em execução. Use o link **Atualizar** para atualizar as informações do trabalho, até o **Status do Trabalho** ser alterado para **Concluído**.

   ![Resumo do trabalho do hive concluído, aplicativo Hive, Visual Studio](./media/apache-hadoop-use-hive-visual-studio/hdinsight-job-summary.png)

7. Selecione **saída do trabalho** para exibir a saída desse trabalho. Ele exibe `[ERROR] 3`, que é o valor retornado por essa consulta.

### <a name="additional-example"></a>Exemplo adicional

O exemplo a seguir depende da `log4jLogs` tabela criada no procedimento anterior, [criar um aplicativo do hive](#create-a-hive-application).

1. Em **Gerenciador de servidores**, clique com o botão direito do mouse no cluster e selecione **gravar uma consulta do hive**.

2. Insira a seguinte consulta de Hive:

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Essas instruções realizam as seguintes ações:

    * `CREATE TABLE IF NOT EXISTS`: Cria uma tabela, caso ela ainda não exista. Como a `EXTERNAL` palavra-chave não é usada, essa instrução cria uma tabela interna. As tabelas internas são armazenadas no data warehouse do Hive e gerenciadas por ele.

        > [!NOTE]  
        > Ao contrário das tabelas `EXTERNAL`, o descarte de uma tabela interna excluirá também os dados subjacentes.

    * `STORED AS ORC`: Armazena os dados no formato *colunar de linhas otimizadas* (ORC). Esse é um formato altamente otimizado e eficiente para o armazenamento de dados do Hive.

    * `INSERT OVERWRITE ... SELECT`: seleciona linhas da tabela `log4jLogs` que contêm `[ERROR]` e, então, insere os dados na tabela `errorLogs`.

3. Altere **interativo** para **lote** , se necessário, e selecione **Enviar**.

4. Para verificar se o trabalho criou a tabela, vá para **Gerenciador de servidores** e expanda **Azure**  >  **HDInsight**. Expanda seu cluster HDInsight e, em seguida, expanda **bancos de dados do hive**  >  **padrão**. As tabelas **errorLogs** e **log4jLogs** são listadas.

## <a name="next-steps"></a>Próximas etapas

Como você pode ver, as ferramentas do HDInsight para o Visual Studio fornecem uma maneira fácil de trabalhar com as consultas do Hive no HDInsight.

* Para obter informações gerais sobre o hive no HDInsight, consulte [o que é Apache Hive e HiveQL no Azure HDInsight?](hdinsight-use-hive.md)

* Para obter informações sobre outras maneiras que você pode trabalhar com o Hadoop no HDInsight, consulte [usar o MapReduce no Apache Hadoop no hdinsight](hdinsight-use-mapreduce.md)

* Para obter mais informações sobre as ferramentas do HDInsight para Visual Studio, consulte[usar ferramentas de data Lake para o Visual Studio para se conectar ao Azure HDInsight e executar consultas de Apache Hive](apache-hadoop-visual-studio-tools-get-started.md)
