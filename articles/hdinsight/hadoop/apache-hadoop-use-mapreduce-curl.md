---
title: Use MapReduce e Curl com o Apache Hadoop no HDInsight - Azure
description: Aprenda a executar remotamente trabalhos do MapReduce com o Apache Hadoop no HDInsight usando o Curl.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/13/2020
ms.openlocfilehash: e90dc2c7220caf5bd72b7086adc275934652e150
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98939696"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-rest"></a>Executar tarefas do MapReduce com o Apache Hadoop no HDInsight usando o REST

Saiba como usar a API REST do Apache Hive WebHCat para executar trabalhos MapReduce em um Apache Hadoop no cluster HDInsight. O Curl é usado para demonstrar como você pode interagir com o HDInsight usando solicitações HTTP brutas para executar trabalhos MapReduce.

> [!NOTE]  
> Se você já estiver familiarizado com o uso de servidores Hadoop baseados em Linux, mas não com o HDInsight, consulte o documento [O que você precisa saber sobre o Apache Hadoop baseado em Linux no HDInsight](../hdinsight-hadoop-linux-information.md).

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Hadoop no HDInsight. Confira [Criar clusters Apache Hadoop usando o portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md).

Ou:
  * Windows PowerShell ou,
  * [Rotação](https://curl.haxx.se/) com [JQ](https://stedolan.github.io/jq/)

## <a name="run-a-mapreduce-job"></a>Executar um trabalho MapReduce

> [!NOTE]  
> Ao usar o Curl ou quaisquer outras comunicações do REST com WebHCat, deve autenticar as solicitações fornecendo o nome de usuário de administrador de cluster HDInsight e a senha. Você também deve usar o nome do cluster como parte do URI usado para enviar as solicitações para o servidor.
>
> A API REST é protegida usando [autenticação básica de acesso](https://en.wikipedia.org/wiki/Basic_access_authentication). Você deve sempre fazer solicitações usando HTTPS para garantir que suas credenciais sejam enviadas com segurança para o servidor.

### <a name="curl"></a>cURL

1. Para facilitar o uso, defina as variáveis abaixo. Este exemplo se baseia em um ambiente Windows, revise conforme necessário para seu ambiente.

    ```cmd
    set CLUSTERNAME=
    set PASSWORD=
    ```

1. De uma linha de comando, use o seguinte comando para verificar se você pode se conectar ao cluster HDInsight:

    ```bash
    curl -u admin:%PASSWORD% -G https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/status
    ```

    Os parâmetros usados nesse comando são os seguintes:

   * **-u**: o nome de usuário e a senha usada para autenticar a solicitação
   * **-G**: indica que essa operação é uma solicitação GET

   O início do URI, `https://CLUSTERNAME.azurehdinsight.net/templeton/v1`, é o mesmo para todas as solicitações.

    Você deve receber uma resposta semelhante ao JSON a seguir:

    ```json
    {"version":"v1","status":"ok"}
    ```

1. Para enviar um trabalho MapReduce, use o comando a seguir. Modifique o caminho para **JQ** conforme necessário.

    ```cmd
    curl -u admin:%PASSWORD% -d user.name=admin ^
    -d jar=/example/jars/hadoop-mapreduce-examples.jar ^
    -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/output ^
    https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/mapreduce/jar | ^
    C:\HDI\jq-win64.exe .id
    ```

    O final do URI (/mapreduce/jar) informa ao WebHCat que essa solicitação inicia um trabalho MapReduce de uma classe em um arquivo jar. Os parâmetros usados nesse comando são os seguintes:

   * **-d**: `-G` não é usado, portanto, o padrão da solicitação é o método post. `-d` especifica os valores de dados que são enviados com a solicitação.
     * **user.name**: o usuário que está executando o comando
     * **jar**: o local do arquivo jar que contém a classe para ser executada
     * **class**: a classe que contém a lógica do MapReduce
     * **arg**: os argumentos a serem passados para o trabalho MapReduce. Nesse caso, o arquivo de texto de entrada e o diretório usados para a saída

    Esse comando deve retornar uma ID de trabalho que pode ser usada para verificar o status do trabalho: `job_1415651640909_0026` .

1. Para verificar o status do trabalho, use o comando a seguir. Substitua o valor de `JOBID` pelo valor **real** retornado na etapa anterior. Revisar o local do **JQ** conforme necessário.

    ```cmd
    set JOBID=job_1415651640909_0026

    curl -G -u admin:%PASSWORD% -d user.name=admin https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/jobs/%JOBID% | ^
    C:\HDI\jq-win64.exe .status.state
    ```

### <a name="powershell"></a>PowerShell

1. Para facilitar o uso, defina as variáveis abaixo. Substitua `CLUSTERNAME` pelo nome real do cluster. Execute o comando e insira a senha de logon do cluster quando solicitado.

    ```powershell
    $clusterName="CLUSTERNAME"
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login password"
    ```

1. Use o comando a seguir para verificar se você pode se conectar ao seu cluster HDInsight:

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clustername.azurehdinsight.net/templeton/v1/status" `
        -Credential $creds `
        -UseBasicParsing
    $resp.Content
    ```

    Você deve receber uma resposta semelhante ao JSON a seguir:

    ```json
    {"version":"v1","status":"ok"}
    ```

1. Para enviar um trabalho MapReduce, use o seguinte comando:

    ```powershell
    $reqParams = @{}
    $reqParams."user.name" = "admin"
    $reqParams.jar = "/example/jars/hadoop-mapreduce-examples.jar"
    $reqParams.class = "wordcount"
    $reqParams.arg = @()
    $reqParams.arg += "/example/data/gutenberg/davinci.txt"
    $reqparams.arg += "/example/data/output"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/mapreduce/jar" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    O final do URI (/mapreduce/jar) informa ao WebHCat que essa solicitação inicia um trabalho MapReduce de uma classe em um arquivo jar. Os parâmetros usados nesse comando são os seguintes:

    * **user.name**: o usuário que está executando o comando
    * **jar**: o local do arquivo jar que contém a classe para ser executada
    * **class**: a classe que contém a lógica do MapReduce
    * **arg**: os argumentos a serem passados para o trabalho MapReduce. Nesse caso, o arquivo de texto de entrada e o diretório usados para a saída

   Esse comando deve retornar uma ID de trabalho que pode ser usada para verificar o status do trabalho: `job_1415651640909_0026` .

1. Para verificar o status do trabalho, use o comando a seguir:

    ```powershell
    $reqParams=@{"user.name"="admin"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobID" `
       -Credential $creds `
       -Body $reqParams `
       -UseBasicParsing

    # ConvertFrom-JSON can't handle duplicate names with different case
    # So change one to prevent the error
    $fixDup=$resp.Content.Replace("jobID","job_ID")
    (ConvertFrom-Json $fixDup).status.state
    ```

### <a name="both-methods"></a>Ambos os métodos

1. Se o trabalho for concluído, o estado retornado será `SUCCEEDED`.

1. Depois que o estado do trabalho for alterado para `SUCCEEDED`, você poderá recuperar os resultados do trabalho do Armazenamento de Blobs do Azure. O parâmetro `statusdir` transmitido com a consulta contém o local do arquivo de saída. Neste exemplo, o local é `/example/curl`. Esse endereço armazena a saída do trabalho no armazenamento padrão de clusters em `/example/curl`.

Você pode listar e baixar esses arquivos usando a [CLI do Azure](/cli/azure/install-azure-cli). Para obter mais informações sobre como usar o CLI do Azure para trabalhar com o armazenamento de BLOBs do Azure, consulte [início rápido: criar, baixar e listar BLOBs com CLI do Azure](../../storage/blobs/storage-quickstart-blobs-cli.md).

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre outros modos possíveis de trabalhar com Hadoop no HDInsight:

* [Usar o MapReduce com o Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)
* [Usar Apache Hive com Apache Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter mais informações sobre a interface REST usada nesse artigo, consulte a [Referência de WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).
