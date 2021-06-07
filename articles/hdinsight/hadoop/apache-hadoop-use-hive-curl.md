---
title: Use o Apache Hadoop Hive com o Curl no HDInsight - Azure
description: Saiba como enviar remotamente trabalhos do Apache Pig para o Azure HDInsight usando a rotação.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/06/2020
ms.openlocfilehash: 124661c57f779b9f8a639debcc093ed15e717694
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946455"
---
# <a name="run-apache-hive-queries-with-apache-hadoop-in-hdinsight-using-rest"></a>Executar consultas do Apache Hive com o Apache Hadoop no HDInsight usando a REST

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Aprenda a usar a API REST do WebHCat para executar consultas do Apache Hive com o Apache Hadoop no cluster do Azure HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Hadoop no HDInsight. Consulte [Introdução ao HDInsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Um cliente REST. Este documento usa [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) no Windows PowerShell e a [ondulação](https://curl.haxx.se/) no [bash](/windows/wsl/install-win10).

* Se você usar bash, também precisará de JQ, um processador JSON de linha de comando.  Confira [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

## <a name="base-uri-for-rest-api"></a>URI de base para a API REST

O URI (Uniform Resource Identifier base) para a API REST no HDInsight é `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME` , em que `CLUSTERNAME` é o nome do cluster.  Os nomes de cluster nos URIs diferenciam **maiúsculas de minúsculas**.  Embora o nome do cluster na parte do FQDN (nome de domínio totalmente qualificado) do URI ( `CLUSTERNAME.azurehdinsight.net` ) não diferencia maiúsculas de minúsculas, outras ocorrências no URI diferenciam maiúsculas de minúsculas.

## <a name="authentication"></a>Autenticação

Ao usar o cURL ou qualquer outra comunicação REST com WebHCat, você deve autenticar as solicitações, fornecendo o nome de usuário e a senha para o administrador do cluster HDInsight. A API REST é protegida por meio de [autenticação básica](https://en.wikipedia.org/wiki/Basic_access_authentication). Para ajudar a garantir que suas credenciais sejam enviadas com segurança para o servidor, sempre faça solicitações usando HTTPS (HTTP seguro).

### <a name="setup-preserve-credentials"></a>Instalação (preservar credenciais)

Preserve suas credenciais para evitar reinseri-las para cada exemplo.  O nome do cluster será preservado em uma etapa separada.

**A. bash**  
Edite o script a seguir substituindo `PASSWORD` pela sua senha real.  Em seguida, digite o comando.

```bash
export password='PASSWORD'
```  

**B. PowerShell** Execute o código abaixo e insira suas credenciais na janela pop-up:

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Identificar o nome do cluster em maiúscula corretamente

A grafia de maiúsculas e minúsculas real do nome do cluster pode ser diferente do esperado, dependendo de como o cluster foi criado.  As etapas aqui mostrarão o capital real e, em seguida, o armazenará em uma variável para todos os exemplos posteriores.

Edite os scripts abaixo para substituir `CLUSTERNAME` pelo nome do cluster. Em seguida, digite o comando. (O nome do cluster para o FQDN não diferencia maiúsculas de minúsculas.)

```bash
export clusterName=$(curl -u admin:$password -sS -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
echo $clusterName
```  

```powershell
# Identify properly cased cluster name
$resp = Invoke-WebRequest -Uri "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" `
    -Credential $creds -UseBasicParsing
$clusterName = (ConvertFrom-Json $resp.Content).items.Clusters.cluster_name;

# Show cluster name
$clusterName
```

## <a name="run-a-hive-query"></a>Executar um trabalho do Hive

1. Para verificar se você pode se conectar ao cluster HDInsight, use os seguintes comandos:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/status" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Você deve receber uma resposta semelhante ao texto a seguir:

    ```json
    {"status":"ok","version":"v1"}
    ```

    Os parâmetros usados nesse comando são os seguintes:

    * `-u` - O nome de usuário e a senha usados para autenticar a solicitação.
    * `-G` - Indica que essa solicitação é uma operação GET.

1. O início da URL, `https://$CLUSTERNAME.azurehdinsight.net/templeton/v1`, é o mesmo para todas as solicitações. O caminho, `/status`, indica que a solicitação é para retornar o status de WebHCat (também conhecido como Templeton) ao servidor. Você também pode solicitar a versão do Hive usando o comando a seguir:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Essa solicitação retorna uma resposta semelhante ao seguinte texto:

    ```json
    {"module":"hive","version":"1.2.1000.2.6.5.3008-11"}
    ```

1. Use o seguinte para criar uma tabela chamada **log4jLogs**:

    ```bash
    jobid=$(curl -s -u admin:$password -d user.name=admin -d execute="DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/rest" https://$clusterName.azurehdinsight.net/templeton/v1/hive | jq -r .id)
    echo $jobid
    ```

    ```powershell
    $reqParams = @{"user.name"="admin";"execute"="DROP TABLE log4jLogs;CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED BY ' ' STORED AS TEXTFILE LOCATION '/example/data/;SELECT t4 AS sev,COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;";"statusdir"="/example/rest"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/hive" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    Essa solicitação usa o método POST, que envia dados como parte da solicitação para a API REST. Os valores de dados a seguir são enviados com a solicitação:

     * `user.name` - O usuário que está executando o comando.
     * `execute` - As instruções do HiveQL a executar.
     * `statusdir` - O diretório no qual o status deste trabalho é gravado.

   Essas instruções executam as seguintes ações:

   * `DROP TABLE` -Se a tabela já existir, ela será excluída.
   * `CREATE EXTERNAL TABLE` - Cria uma nova tabela ‘externa’ no Hive. As tabelas externas armazenam apenas a definição da tabela no Hive. Os dados são mantidos no local original.

     > [!NOTE]  
     > As tabelas externas devem ser usadas quando você espera que os dados subjacentes sejam atualizados por uma fonte externa. Por exemplo, um processo de upload de dados automatizados ou outra operação MapReduce.
     >
     > Remover uma tabela externa **não** exclui os dados, somente a definição de tabela.

   * `ROW FORMAT` – o modo como os dados são formatados. Os campos em cada log são separados por um espaço.
   * `STORED AS TEXTFILE LOCATION` -Onde os dados são armazenados (o diretório de exemplo/dados) e que são armazenados como texto.
   * `SELECT` – Seleciona uma contagem de todas as linhas em que a coluna **T4** contém o valor **[ERROR]**. Essa instrução retorna um valor de **3**, visto que há três linhas que contêm esse valor.

     > [!NOTE]  
     > Observe que os espaços entre as instruções HiveQL são substituídos pelo caractere `+` quando usados com o Curl. Os valores entre aspas que contêm um espaço, como o delimitador, não devem ser substituídos por `+`.

      Esse comando retorna uma ID de trabalho que pode ser usada para verificar o status do trabalho.

1. Para verificar o status do trabalho, use o comando a seguir:

    ```bash
    curl -u admin:$password -d user.name=admin -G https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobid | jq .status.state
    ```

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

    Se o trabalho foi concluído, o estado será **SUCCEEDED**.

1. Depois que o estado do trabalho for alterado para **SUCCEEDED**, você poderá recuperar os resultados do trabalho no Armazenamento de Blobs do Azure. O parâmetro `statusdir` transmitido com a consulta contém a localização do arquivo de saída; nesse caso, `/example/rest`. Esse endereço armazena a saída do diretório `example/curl` no armazenamento padrão de clusters.

    Você pode listar e baixar esses arquivos usando a [CLI do Azure](/cli/azure/install-azure-cli). Para obter mais informações sobre como usar a CLI do Azure com o Armazenamento do Azure, consulte o documento [Usar a CLI do Azure com o Armazenamento do Azure](../../storage/blobs/storage-quickstart-blobs-cli.md).

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre outras maneiras que você pode trabalhar com Hadoop no HDInsight:

* [Usar Apache Hive com Apache Hadoop no HDInsight](hdinsight-use-hive.md)
* [Usar o MapReduce com o Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)

Para obter mais informações sobre a API REST usada nesse documento, consulte o documento [Referência de WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).