---
title: "Usar os Modos de Exibição do Ambari para trabalhar com o Hive no HDInsight (Hadoop) – Azure | Microsoft Docs"
description: "Saiba como usar o Modo de Exibição do Hive em seu navegador da Web para enviar consultas do Hive. O Modo de exibição do Hive faz parte da Interface de Usuário da Web do Ambari fornecida com o cluster HDInsight baseado em Linux."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 1abe9104-f4b2-41b9-9161-abbc43de8294
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/31/2017
ms.author: larryfr
ms.openlocfilehash: b5bf9042921dfb2344c2c6e03990578fa9ce4f5b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-ambari-hive-view-with-hadoop-in-hdinsight"></a>Usar a Exibição do Hive do Ambari com o Hadoop no HDInsight

[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Aprenda a executar consultas do Hive usando a Exibição do Hive do Ambari. O Ambari é um utilitário de monitoramento e de gerenciamento fornecido com clusters HDInsight baseados em Linux. Um dos recursos fornecidos pelo Ambari é uma interface de usuário da Web que pode ser usada para executar consultas do Hive.

> [!NOTE]
> O Ambari possui muitos recursos que não são abordados neste documento. Para saber mais, confira [Gerenciar clusters HDInsight usando a interface do usuário da Web do Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="prerequisites"></a>Pré-requisitos

* Criar um cluster HDInsight baseado em Linux. Para obter informações sobre como criar clusters, consulte [Introdução ao uso do Hadoop no HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).

> [!IMPORTANT]
> As etapas deste documento exigem um cluster do Azure HDInsight que usa Linux. O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou posterior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="open-the-hive-view"></a>Abrir a Exibição do Hive

Você pode abrir Exibições do Ambari do Portal do Azure. Selecione seu cluster HDInsight e escolha **Exibições do Ambari** na seção **Links Rápidos**.

![Seção de links rápidos do portal](./media/hdinsight-hadoop-use-hive-ambari-view/quicklinks.png)

Na lista de exibições, selecione __Exibição de Hive__.

![A exibição de Hive selecionada](./media/hdinsight-hadoop-use-hive-ambari-view/select-hive-view.png)

> [!NOTE]
> Ao acessar o Ambari, você recebe uma solicitação para se autenticar no site. Insira o nome da conta de administrador (o padrão é `admin`) e a senha que você usou ao criar o cluster.

Você deverá ver uma página semelhante à seguinte imagem:

![Imagem da planilha de consulta para a exibição de Hive](./media/hdinsight-hadoop-use-hive-ambari-view/ambari-hive-view.png)

## <a name="run-a-query"></a>Executar uma consulta

Para executar uma consulta do Hive, use as seguintes etapas da exibição do Hive.

1. Da guia __Consulta__, cole as seguintes instruções HiveQL na planilha:

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;
    ```

    As instruções executam as seguintes ações:

   * `DROP TABLE`: exclui a tabela e o arquivo de dados, caso a tabela já exista.

   * `CREATE EXTERNAL TABLE`: cria uma nova tabela "externa" no Hive.
   As tabelas externas armazenam apenas a definição da tabela no Hive. Os dados são mantidos no local original.

   * `ROW FORMAT`: mostra como os dados são formatados. Nesse caso, os campos em cada log são separados por um espaço.

   * `STORED AS TEXTFILE LOCATION`: mostra o local em que os dados são armazenados e se estão armazenados como texto.

   * `SELECT`: seleciona uma contagem de todas as linhas em que a coluna t4 contém o valor [ERROR].

     > [!NOTE]
     > Use tabelas externas quando você espera que os dados subjacentes sejam atualizados por uma origem externa, como um processo automático de upload de dados ou outra operação MapReduce. Remover uma tabela externa *não* exclui os dados, somente a definição de tabela.

    > [!IMPORTANT]
    > Deixe a seleção __Banco de dados__ em __padrão__. Os exemplos neste documento usam o banco de dados padrão incluído no HDInsight.

2. Para iniciar a consulta, use o botão **Executar** abaixo da planilha. O botão fica laranja e o texto é alterado para **Parar**.

3. Depois que a consulta for concluída, a seção **Resultados** exibirá os resultados da operação. O texto a seguir é o resultado da consulta:

        sev       cnt
        [ERROR]   3

    A guia **Logs** pode ser usada para exibir as informações de log criadas pelo trabalho.

   > [!TIP]
   > Baixe ou salve resultados da caixa de diálogo suspensa **Salvar resultados**, no canto superior esquerdo da seção **Resultados do Processo de Consulta**.

4. Selecione as quatro primeiras linhas dessa consulta e escolha **Executar**. Observe que não há resultados quando o trabalho é concluído. O uso do botão **Executar** quando parte da consulta está selecionada executa apenas as instruções escolhidas. Nesse caso, a seleção não incluiu a instrução final que recupera linhas da tabela. Se escolher apenas essa linha e usar **Executar**, você verá os resultados esperados.

5. Para adicionar uma planilha, use o botão **Nova Planilha** na parte inferior do **Editor de Consultas**. Na nova planilha, digite as seguintes instruções HiveQL:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
    ```

  As instruções executam as seguintes ações:

   * **CREATE TABLE IF NOT EXISTS**: criará uma tabela, se ela ainda não existir. Já que a palavra-chave **EXTERNAL** não é usada, uma tabela interna é criada. Uma tabela interna é armazenada no data warehouse do Hive e é totalmente gerenciada pelo Hive. Diferentemente do que ocorre com tabelas externas, o descarte de uma tabela interna excluirá também os dados subjacentes.

   * **STORED AS ORC**: armazena os dados no formato de linha de otimização Colunar (ORC). Esse é um formato altamente otimizado e eficiente para o armazenamento de dados do Hive.

   * **INSERT OVERWRITE ... SELECT**: seleciona linhas da tabela **log4jLogs** que contêm `[ERROR]` e insere os dados na tabela **errorLogs**.

Use o botão **Executar** para executar essa consulta. A guia **Resultados** não contém nenhuma informação quando a consulta retorna zero linhas. O status deve mostrar **SUCCEEDED** após a conclusão da consulta.

### <a name="visual-explain"></a>Explicação visual

Para exibir uma visualização do plano de consulta, selecione a guia **Explicação Visual** abaixo da planilha.

O modo de exibição **Explicação Visual** da consulta pode ser útil na compreensão do fluxo de consultas complexas. Você pode ver um equivalente textual dessa exibição usando o botão **Explicar** no Editor de Consultas.

### <a name="tez-ui"></a>Interface de usuário do Tez

Para exibir a interface do usuário do Tez para a consulta, selecione a guia **Tez** abaixo da planilha.

> [!IMPORTANT]
> O Tez não é usado para resolver todas as consultas. Muitas consultas podem ser resolvidas sem usar o Tez. 

Se o Tez foi usado para resolver a consulta, o DAG (gráfico acíclico dirigido) é exibido. Se você quiser exibir o DAG de consultas executadas anteriormente ou se você quiser depurar o processo do Tez, use a [Exibição do Tez](hdinsight-debug-ambari-tez-view.md) em vez disso.

## <a name="view-job-history"></a>Exibir histórico de trabalho

A guia __Trabalhos__ exibe um histórico das consultas de Hive.

![Imagem do histórico de trabalhos](./media/hdinsight-hadoop-use-hive-ambari-view/job-history.png)

## <a name="database-tables"></a>Tabelas de banco de dados

Você pode usar a guia __tabelas__ para trabalhar com tabelas em um banco de dados de Hive.

![Imagem da guia tabelas](./media/hdinsight-hadoop-use-hive-ambari-view/tables.png)

## <a name="saved-queries"></a>Consultas salvas

Na guia **Consulta** você pode, opcionalmente, salvar consultas. Depois de salvar uma consulta, você pode reutilizá-la da guia __Consultas Salvas__.

![Imagem da guia Consultas Salvas](./media/hdinsight-hadoop-use-hive-ambari-view/saved-queries.png)

## <a name="user-defined-functions"></a>Funções definidas pelo usuário

O Hive também pode ser estendido por meio de UDF (funções definidas pelo usuário). Use uma UDF para implementar funcionalidade ou lógica que não é facilmente modelada em HiveQL.

Declare e salve um conjunto de UDFs usando a guia **UDF** na parte superior da exibição do Hive. Essas UDFs podem ser usadas com o **Editor de Consultas**.

![Imagem da guia UDF](./media/hdinsight-hadoop-use-hive-ambari-view/user-defined-functions.png)

Depois de adicionar uma UDF à exibição do Hive, um botão **Inserir udfs** será exibido na parte inferior do **Editor de Consultas**. Ao selecionar essa entrada, uma lista suspensa de UDFs definidas na Exibição do Hive será exibida. A seleção de uma UDF adiciona instruções HiveQL à sua consulta para habilitar a UDF.

Por exemplo, se você tiver definido uma UDF com as seguintes propriedades:

* Nome de recurso: myudfs

* Caminho do recurso: /myudfs.jar

* Nome da UDF: myawesomeudf

* Nome de classe da  UDF: com.myudfs.Awesome

O uso do botão **Inserir udfs** exibe uma entrada denominada **myudfs**, com outra lista suspensa para cada UDF definida para esse recurso. Nesse caso, ela é **myawesomeudf**. A seleção dessa entrada adiciona o seguinte ao início da consulta:

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

Você pode usar a UDF em sua consulta. Por exemplo: `SELECT myawesomeudf(name) FROM people;`.

Para saber mais sobre como usar UDFs com Hive no HDInsight, consulte os seguintes artigos:

* [Usando o Python com o Hive e com o Pig no HDInsight](hdinsight-python.md)
* [Como adicionar UDF personalizadas do Hive no HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

## <a name="hive-settings"></a>Configurações do Hive

Você pode alterar diversas configurações do Hive, por exemplo, alterar o mecanismo de execução do Hive de Tez (o padrão) para MapReduce.

## <a id="nextsteps"></a>Próximas etapas

Para informações gerais sobre o Hive no HDInsight:

* [Usar o Hive com Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outras maneiras que você pode trabalhar com Hadoop no HDInsight:

* [Usar o Pig com Hadoop no HDInsight](hdinsight-use-pig.md)
* [Usar o MapReduce com Hadoop no HDInsight](hdinsight-use-mapreduce.md)
