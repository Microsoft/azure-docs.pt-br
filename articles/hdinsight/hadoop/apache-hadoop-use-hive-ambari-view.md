---
title: Use o Apache Ambari Views para trabalhar com o Hive no HDInsight (Apache Hadoop) - Azure
description: Saiba como usar o Modo de Exibição do Hive em seu navegador da Web para enviar consultas do Hive. O Modo de exibição do Hive faz parte da Interface de Usuário da Web do Ambari fornecida com o cluster HDInsight baseado em Linux.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: hrasheed
ms.openlocfilehash: 55f8f453faf35d52c5c292e6b309194443980466
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62095312"
---
# <a name="use-apache-ambari-hive-view-with-apache-hadoop-in-hdinsight"></a>Use o Apache Ambari Hive View com o Apache Hadoop no HDInsight

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Saiba como executar as consultas do Hive usando o Apache Ambari Hive View. A Exibição do Hive permite que você crie, otimize e execute consultas de Hive do seu navegador da Web.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster de Hadoop no HDInsight. Ver [Introdução ao HDInsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).
* Um navegador da Web

## <a name="run-a-hive-query"></a>Executar um trabalho do Hive

1. Dos [portal do Azure](https://portal.azure.com/), selecione o cluster.  Ver [listar e mostrar clusters](../hdinsight-administer-use-portal-linux.md#showClusters) para obter instruções. O cluster é aberto em uma nova folha de portal.

2. Partir **painéis do Cluster**, selecione **Ambari views**. Quando solicitado a autenticar, use o nome e senha da conta de logon de cluster (padrão `admin`) que você forneceu ao criar o cluster.

3. Na lista de exibições, selecione __Exibição de Hive__.

    ![A exibição de Hive selecionada](./media/apache-hadoop-use-hive-ambari-view/select-hive-view.png)

    A página de exibição do Hive é semelhante à seguinte imagem:

    ![Imagem da planilha de consulta para a exibição de Hive](./media/apache-hadoop-use-hive-ambari-view/ambari-hive-view.png)

4. Da guia __Consulta__, cole as seguintes instruções HiveQL na planilha:

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs(
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS loglevel, COUNT(*) AS count FROM log4jLogs 
        WHERE t4 = '[ERROR]' 
        GROUP BY t4;
    ```

    Essas instruções executam as seguintes ações:

   * `DROP TABLE`: Exclui a tabela e o arquivo de dados, caso a tabela já exista.

   * `CREATE EXTERNAL TABLE`: Cria uma nova tabela "externa" no Hive.
     As tabelas externas armazenam apenas a definição da tabela no Hive. Os dados são mantidos no local original.

   * `ROW FORMAT`: Mostra como os dados são formatados. Nesse caso, os campos em cada log são separados por um espaço.

   * `STORED AS TEXTFILE LOCATION`: Mostra o local em que os dados são armazenados e se estão armazenados como texto.

   * `SELECT`: Seleciona uma contagem de todas as linhas em que a coluna t4 contém o valor [ERROR].

   > [!IMPORTANT]  
   > Deixe a seleção __Banco de dados__ em __padrão__. Os exemplos neste documento usam o banco de dados padrão incluído no HDInsight.

5. Para iniciar a consulta, selecione **Execute** abaixo da planilha. O botão fica laranja e o texto é alterado para **Parar**.

6. Depois que a consulta for concluída, a seção **Resultados** exibirá os resultados da operação. O texto a seguir é o resultado da consulta:

        loglevel       count
        [ERROR]        3

    Você pode usar o **LOG** guia para exibir as informações de log criadas pelo trabalho.

   > [!TIP]  
   > Baixe ou salve os resultados do **ações** caixa de diálogo suspensa sob a **resultados** guia.

### <a name="visual-explain"></a>Explicação visual

Para exibir uma visualização do plano de consulta, selecione a guia **Explicação Visual** abaixo da planilha.

O modo de exibição **Explicação Visual** da consulta pode ser útil na compreensão do fluxo de consultas complexas.

### <a name="tez-ui"></a>Interface de usuário do Tez

Para exibir a UI Tez para a consulta, selecione a **Tez UI** guia abaixo da planilha.

> [!IMPORTANT]  
> O Tez não é usado para resolver todas as consultas. Muitas consultas podem ser resolvidas sem usar o Tez. 

## <a name="view-job-history"></a>Exibir histórico de trabalho

A guia __Trabalhos__ exibe um histórico das consultas de Hive.

![Imagem do histórico de trabalhos](./media/apache-hadoop-use-hive-ambari-view/job-history.png)

## <a name="database-tables"></a>Tabelas de banco de dados

Você pode usar a guia __tabelas__ para trabalhar com tabelas em um banco de dados de Hive.

![Imagem da guia tabelas](./media/apache-hadoop-use-hive-ambari-view/tables.png)

## <a name="saved-queries"></a>Consultas salvas

Na guia **Consulta** você pode, opcionalmente, salvar consultas. Depois de salvar uma consulta, você pode reutilizá-la da guia __Consultas Salvas__.

![Imagem da guia Consultas Salvas](./media/apache-hadoop-use-hive-ambari-view/saved-queries.png)

> [!TIP]  
> Consultas salvas são armazenadas no armazenamento de cluster padrão. Você pode encontrar as consultas salvas no caminho `/user/<username>/hive/scripts`. Elas são armazenadas como arquivos de texto sem formatação `.hql`.
>
> Se você excluir o cluster, mas manter o armazenamento, pode usar um utilitário como o [Gerenciador de Armazenamento do Microsoft Azure](https://azure.microsoft.com/features/storage-explorer/) ou o Gerenciador de Armazenamento do Data Lake (do [Portal do Azure](https://portal.azure.com)) para recuperar as consultas.

## <a name="user-defined-functions"></a>Funções definidas pelo usuário

O Hive pode ser estendido por meio de UDF (funções definidas pelo usuário). Use uma UDF para implementar funcionalidade ou lógica que não é facilmente modelada em HiveQL.

Declare e salve um conjunto de UDFs usando a guia **UDF** na parte superior da exibição do Hive. Essas UDFs podem ser usadas com o **Editor de Consultas**.

![Imagem da guia UDF](./media/apache-hadoop-use-hive-ambari-view/user-defined-functions.png)

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

Você pode usar a UDF em sua consulta. Por exemplo, `SELECT myawesomeudf(name) FROM people;`.

Para saber mais sobre como usar UDFs com Hive no HDInsight, consulte os seguintes artigos:

* [Usar o Python com Apache Hive e Apache Pig no HDInsight](python-udf-hdinsight.md)
* [Como adicionar uma UDF personalizada do Apache Hive ao HDInsight](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

## <a name="hive-settings"></a>Configurações do Hive

Você pode alterar diversas configurações do Hive, por exemplo, alterar o mecanismo de execução do Hive de Tez (o padrão) para MapReduce.

## <a id="nextsteps"></a>Próximas etapas

Para informações gerais sobre o Hive no HDInsight:

* [Use o Apache Hive com o Apache Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outras maneiras que você pode trabalhar com Hadoop no HDInsight:

* [Use o Apache Pig com o Apache Hadoop no HDInsight](hdinsight-use-pig.md)
* [Usar o MapReduce com o Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)
