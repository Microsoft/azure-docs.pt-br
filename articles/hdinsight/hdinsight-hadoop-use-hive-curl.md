---
title: "Usar o Hive do Hadoop com Curl no HDInsight – Azure | Microsoft Docs"
description: Saiba como enviar remotamente trabalhos do Pig para o HDInsight usando o Curl.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6ce18163-63b5-4df6-9bb6-8fcbd4db05d8
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/03/2017
ms.author: larryfr
ms.openlocfilehash: 8c6877b923c6000abe3aece37a24b275462ba378
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="run-hive-queries-with-hadoop-in-hdinsight-using-rest"></a>Executar consultas Hive com Hadoop no HDInsight usando REST

[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Saiba como usar a API REST do WebHCat para executar consultas Hive com o Hadoop no cluster HDInsight do Azure.

O [Curl](http://curl.haxx.se/) é usado para demonstrar como você pode interagir com o HDInsight usando solicitações HTTP brutas. O utilitário [jq](http://stedolan.github.io/jq/) é usado para processar os dados JSON retornados de solicitações REST.

> [!NOTE]
> Se você já estiver familiarizado com o uso de servidores Hadoop baseados em Linux, mas for iniciante no HDInsight, consulte o documento [O que você precisa saber sobre o Hadoop no HDInsight baseado em Linux](hdinsight-hadoop-linux-information.md).

## <a id="curl"></a>Executar consultas Hive

> [!NOTE]
> Ao usar o cURL ou qualquer outra comunicação REST com WebHCat, você deve autenticar as solicitações, fornecendo o nome de usuário e a senha para o administrador do cluster HDInsight.
>
> Para os comandos nesta seção, substitua **admin** pelo usuário para se autenticar no cluster. Substitua **CLUSTERNAME** pelo nome do cluster. Quando solicitado, insira a senha para a conta de usuário.
>
> A API REST é protegida por meio de [autenticação básica](http://en.wikipedia.org/wiki/Basic_access_authentication). Para ajudar a garantir que suas credenciais sejam enviadas com segurança para o servidor, sempre faça solicitações usando HTTPS (HTTP seguro).

1. De uma linha de comando, use o seguinte comando para verificar se você pode se conectar ao cluster HDInsight:

    ```bash
    curl -u admin -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Você deve receber uma resposta semelhante ao texto a seguir:

        {"status":"ok","version":"v1"}

    Os parâmetros usados nesse comando são os seguintes:

    * **-u** - o nome de usuário e a senha usada para autenticar a solicitação.
    * **-G** – Indica que essa solicitação é uma operação GET.

   O início da URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, será o mesmo para todas as solicitações. O caminho, **/status**, indica que a solicitação é para retornar o status de WebHCat (também conhecido como Templeton) ao servidor. Você também pode solicitar a versão do Hive usando o comando a seguir:

    ```bash
    curl -u admin -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive
    ```

    Essa solicitação retorna uma resposta semelhante ao seguinte texto:

        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}

2. Use o seguinte para criar uma tabela chamada **log4jLogs**:

    ```bash
    curl -u admin -d user.name=admin -d execute="set+hive.execution.engine=tez;DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive
    ```

    Os seguintes parâmetros são usados com esta solicitação:

   * **-d** – uma vez que `-G` não é usado; a solicitação padrão é o método POST. `-d` especifica os valores de dados que são enviados com a solicitação.

     * **user.name** - o usuário que está executando o comando.
     * **execute** - as instruções do HiveQL a executar.
     * **statusdir** – O diretório no qual o status deste trabalho será gravado.

   As instruções executam as seguintes ações:
   
   * **DROP TABLE** – Se a tabela já existir, ela será excluída.
   * **CREATE EXTERNAL TABLE** - cria uma nova tabela “externa" em Hive. As tabelas externas armazenam apenas a definição da tabela no Hive. Os dados são mantidos no local original.

     > [!NOTE]
     > As tabelas externas devem ser usadas quando você espera que os dados subjacentes sejam atualizados por uma fonte externa. Por exemplo, um processo de upload de dados automatizados ou outra operação MapReduce.
     >
     > Remover uma tabela externa **não** exclui os dados, somente a definição de tabela.

   * **ROW FORMAT** – O modo como os dados são formatados. Os campos em cada log são separados por um espaço.
   * **STORED AS TEXTFILE LOCATION** – Onde os dados são armazenados (o diretório de exemplos/dados) e que estão armazenados como texto.
   * **SELECT** - Seleciona uma contagem de todas as linhas em que a coluna **t4** contém o valor **[ERROR]**. Essa instrução retorna um valor de **3**, visto que há três linhas que contêm esse valor.

     > [!NOTE]
     > Observe que os espaços entre as instruções HiveQL são substituídos pelo caractere `+` quando usados com o Curl. Os valores entre aspas que contêm um espaço, como o delimitador, não devem ser substituídos por `+`.

   * **INPUT__FILE__NAME LIKE '%25.log'** – Essa instrução limita a pesquisa a usar somente os arquivos que terminam em .log.

     > [!NOTE]
     > Observe que `%25` é o formato de % codificado para URL, então, a condição real é `like '%.log'`. O % deve ser codificado em URL, pois será tratado como um caractere especial em URLs.

   Esse comando retorna uma ID de trabalho que pode ser usada para verificar o status do trabalho.

       {"id":"job_1415651640909_0026"}

3. Para verificar o status do trabalho, use o comando a seguir:

    ```bash
    curl -G -u admin -d user.name=admin https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    Substitua **JOBID** pelo valor retornado na etapa anterior. Por exemplo, se o valor retornado for **, `{"id":"job_1415651640909_0026"}`JOBID** será `job_1415651640909_0026`.

    Se o trabalho foi concluído, o estado será **SUCCEEDED**.

   > [!NOTE]
   > Essa solicitação de Curl retorna um documento JSON (JavaScript Object Notation) com informações sobre o trabalho. Jq é usado para recuperar apenas o valor de estado.

4. Depois que o estado do trabalho for alterado para **SUCCEEDED**, você poderá recuperar os resultados do trabalho no Armazenamento de Blobs do Azure. O parâmetro `statusdir` transmitido com a consulta contém o local do arquivo de saída; nesse caso, **/example/curl**. Esse endereço armazena a saída do diretório **exemplo/curl** no armazenamento padrão de clusters.

    Você pode listar e baixar esses arquivos usando a [CLI do Azure](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli). Para obter mais informações sobre como usar a CLI do Azure com o Armazenamento do Azure, consulte o armazenamento [Usar a CLI 2.0 do Azure com o Armazenamento do Azure](https://docs.microsoft.com/en-us/azure/storage/storage-azure-cli#create-and-manage-blobs).

5. Use as instruções a seguir para criar uma nova tabela "interna" chamada **errorLogs**:

    ```bash
    curl -u admin -d user.name=admin -d execute="set+hive.execution.engine=tez;CREATE+TABLE+IF+NOT+EXISTS+errorLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+STORED+AS+ORC;INSERT+OVERWRITE+TABLE+errorLogs+SELECT+t1,t2,t3,t4,t5,t6,t7+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log';SELECT+*+from+errorLogs;" -d statusdir="/example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive
    ```

    Essas instruções executam as seguintes ações:

   * **CREATE TABLE IF NOT EXISTS** - cria uma tabela, se ela ainda não existir. Esta instrução cria uma tabela interna, que é armazenada no data warehouse do Hive. Esta tabela é gerenciada pelo Hive.

     > [!NOTE]
     > Diferentemente de tabelas externas, o descarte de uma tabela interna excluirá também os dados subjacentes.

   * **STORES AS ORC** : armazena os dados no formato ORC (Optimized Row Columnar). Esse é um formato altamente otimizado e eficiente para o armazenamento de dados do Hive.
   * **INSERT OVERWRITE ... SELECT** - seleciona linhas da tabela **log4jLogs** que contêm **[ERROR]** e insere os dados na tabela **errorLogs**.
   * **SELECT** – Seleciona todas as linhas da nova tabela **errorLogs**.

6. Use a ID de trabalho retornada para verificar o status do trabalho. Assim que tiver êxito, use a CLI do Azure, conforme descrito anteriormente, para baixar e exibir os resultados. A saída deve conter três linhas, todos os quais contêm **[ERROR]**.

## <a id="nextsteps"></a>Próximas etapas

Para obter informações gerais sobre o Hive com HDInsight:

* [Usar o Hive com Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outras maneiras que você pode trabalhar com Hadoop no HDInsight:

* [Usar o Pig com Hadoop no HDInsight](hdinsight-use-pig.md)
* [Usar o MapReduce com Hadoop no HDInsight](hdinsight-use-mapreduce.md)

Se você estiver usando o Tez com o Hive, consulte os seguintes documentos para as informações de depuração:

* [Usar a exibição de Ambari Tez no HDInsight baseado em Linux](hdinsight-debug-ambari-tez-view.md)

Para obter mais informações sobre a API REST usada nesse documento, consulte o documento [Referência de WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md




[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


