---
title: "Usar o MapReduce e Curl com o Hadoop no HDInsight – Azure | Microsoft Docs"
description: Saiba como executar trabalhos MapReduce remotamente com Hadoop no HDInsight usando o Curl.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: bc6daf37-fcdc-467a-a8a8-6fb2f0f773d1
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/03/2017
ms.author: larryfr
ms.openlocfilehash: 28d23cf397db204a22fea785521ea6a164d84374
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-rest"></a>Executar trabalhos MapReduce com Hadoop no HDInsight usando REST

Saiba como usar a API REST do WebHCat para executar trabalhos MapReduce em um Hadoop no cluster HDInsight. O Curl é usado para demonstrar como você pode interagir com o HDInsight usando solicitações HTTP brutas para executar trabalhos MapReduce.

> [!NOTE]
> Se você já estiver familiarizado com o uso de servidores Hadoop baseados em Linux, mas for iniciante no HDInsight, consulte o documento [O que você precisa saber sobre Hadoop baseado em Linux no HDInsight](hdinsight-hadoop-linux-information.md).


## <a id="prereq"></a>Pré-requisitos

* Um Hadoop no cluster HDInsight
* [Curl](http://curl.haxx.se/)
* [jq](http://stedolan.github.io/jq/)

## <a id="curl"></a>Executar trabalhos MapReduce usando o Curl

> [!NOTE]
> Ao usar o Curl ou quaisquer outras comunicações do REST com WebHCat, deve autenticar as solicitações fornecendo o nome de usuário de administrador de cluster HDInsight e a senha. Você também deve usar o nome do cluster como parte do URI usado para enviar as solicitações para o servidor.
>
> Para os comandos nesta seção, substitua **admin** pelo usuário para se autenticar no cluster. Substitua **CLUSTERNAME** pelo nome do cluster. Quando solicitado, forneça a senha para a conta de usuário.
>
> A API REST é protegida usando [autenticação básica de acesso](http://en.wikipedia.org/wiki/Basic_access_authentication). Você deve sempre fazer solicitações usando HTTPS para garantir que suas credenciais sejam enviadas com segurança para o servidor.


1. De uma linha de comando, use o seguinte comando para verificar se você pode se conectar ao cluster HDInsight:

    ```bash
    curl -u admin -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Você deve receber uma resposta semelhante ao JSON a seguir:

        {"status":"ok","version":"v1"}

    Os parâmetros usados nesse comando são os seguintes:

   * **-u**: o nome de usuário e a senha usada para autenticar a solicitação
   * **-G**: indica que essa operação é uma solicitação GET

   O início do URI, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, será o mesmo para todas as solicitações.

2. Para enviar um trabalho MapReduce, use o seguinte comando:

    ```bash
    curl -u admin -d user.name=admin -d jar=/example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/CurlOut https://CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar
    ```

    O final do URI (/mapreduce/jar) informa ao WebHCat que essa solicitação inicia um trabalho MapReduce de uma classe em um arquivo jar. Os parâmetros usados nesse comando são os seguintes:

   * **-d** já que `-G` não é usado; a solicitação padrão será o método POST. `-d` especifica os valores de dados que são enviados com a solicitação.
    * **user.name**: o usuário que está executando o comando
    * **jar**: o local do arquivo jar que contém a classe para ser executada
    * **class**: a classe que contém a lógica do MapReduce
    * **arg**: os argumentos a serem passados para o trabalho MapReduce. Nesse caso, o arquivo de texto de entrada e o diretório usados para a saída

   Esse comando deve retornar uma ID de trabalho que pode ser usada para verificar o status do trabalho:

       {"id":"job_1415651640909_0026"}

3. Para verificar o status do trabalho, use o comando a seguir:

    ```bash
    curl -G -u admin -d user.name=admin https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    Substitua o **JOBID** pelo valor retornado na etapa anterior. Por exemplo, se o valor retornado foi `{"id":"job_1415651640909_0026"}`, JOBID será `job_1415651640909_0026`.

    Se o trabalho for concluído, o estado retornado será `SUCCEEDED`.

   > [!NOTE]
   > Essa solicitação de Curl retorna um documento JSON com informações sobre o trabalho. Jq é usado para recuperar apenas o valor de estado.

4. Depois que o estado do trabalho for alterado para `SUCCEEDED`, você poderá recuperar os resultados do trabalho do Armazenamento de Blobs do Azure. O parâmetro `statusdir` transmitido com a consulta contém o local do arquivo de saída. Neste exemplo, o local é `/example/curl`. Esse endereço armazena a saída do trabalho no armazenamento padrão de clusters em `/example/curl`.

Você pode listar e baixar esses arquivos usando a [CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Para obter mais informações sobre como trabalhar com blobs na CLI do Azure, consulte o documento [Usar a CLI 2.0 do Azure com o Armazenamento do Azure](../storage/common/storage-azure-cli.md#create-and-manage-blobs).

## <a id="nextsteps"></a>Próximas etapas

Para obter informações gerais sobre trabalhos de MapReduce no HDInsight:

* [Usar o MapReduce com Hadoop no HDInsight](hdinsight-use-mapreduce.md)

Para obter informações sobre outros modos possíveis de trabalhar com Hadoop no HDInsight:

* [Usar o Hive com Hadoop no HDInsight](hdinsight-use-hive.md)
* [Usar o Pig com Hadoop no HDInsight](hdinsight-use-pig.md)

Para obter mais informações sobre a interface REST usada nesse artigo, consulte a [Referência de WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).