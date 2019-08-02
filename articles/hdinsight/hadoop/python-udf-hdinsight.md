---
title: Python UDF com Apache Hive e Apache Pig - Azure HDInsight
description: Aprenda a usar as Funções Definidas pelo Usuário Python (UDF) do Apache Hive e do Apache Pig no HDInsight, a pilha da tecnologia Apache Hadoop no Azure.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 03/15/2019
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 49fd69c124ff9053f3934aefd349e039b437df0d
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68354961"
---
# <a name="use-python-user-defined-functions-udf-with-apache-hive-and-apache-pig-in-hdinsight"></a>Usar funções definidas pelo usuário (UDF) do Python com o Apache Hive e o Apache Pig no HDInsight

Aprenda a usar as funções definidas pelo usuário (UDF) do Python com o Apache Hive e o Apache Pig no Apache Hadoop no Azure HDInsight.

## <a name="python"></a>Python no HDInsight

O Python 2.7 é instalado por padrão no HDInsight 3.0 e posteriores. O Apache Hive pode ser usado com essa versão do Python para processamento de fluxo. O processamento de fluxo usa STDOUT e STDIN para enviar dados entre o Hive e o UDF.

O HDInsight também inclui o Jython, que é uma implementação do Python gravada em Java. Jython é executado diretamente na Máquina Virtual Java e não usa streaming. Jython é o interpretador do Python recomendado ao usar Python com Pig.

## <a name="prerequisites"></a>Pré-requisitos

* **Um cluster Hadoop no HDInsight**. Consulte [Introdução ao HDInsight no Linux](apache-hadoop-linux-tutorial-get-started.md).
* **Um cliente SSH**. Para saber mais, confira [Conectar-se ao HDInsight (Apache Hadoop) usando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).
* O [esquema de URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) do seu armazenamento primário de clusters. Isso seria wasb://para o armazenamento do Azure, abfs://para Azure Data Lake Storage Gen2 ou adl://para Azure Data Lake Storage Gen1. Se a transferência segura estiver habilitada para o armazenamento do Azure ou Data Lake Storage Gen2, o URI seria wasbs://ou abfss://, respectivamente, consulte também a [transferência segura](../../storage/common/storage-require-secure-transfer.md).
* **Alteração possível na configuração de armazenamento.**  Consulte [configuração de armazenamento](#storage-configuration) se estiver usando o `BlobStorage`tipo de conta de armazenamento.
* Opcional.  Se estiver planejando usar o PowerShell, você precisará do [módulo AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az) instalado.

> [!NOTE]  
> A conta de armazenamento usada neste artigo foi o armazenamento do Azure com a [transferência segura](../../storage/common/storage-require-secure-transfer.md) habilitada e, portanto, `wasbs` é usada em todo o artigo.

## <a name="storage-configuration"></a>Configuração de armazenamento
Nenhuma ação será necessária se a conta de armazenamento usada for do `Storage (general purpose v1)` tipo `StorageV2 (general purpose v2)`ou.  O processo neste artigo produzirá a saída para pelo menos `/tezstaging`.  Uma configuração padrão do Hadoop conterá `/tezstaging` `fs.azure.page.blob.dir` na variável de configuração `core-site.xml` no para `HDFS`o serviço.  Essa configuração fará com que a saída para o diretório seja blobs de páginas, que não têm suporte para o `BlobStorage`tipo de conta de armazenamento.  Para usar `BlobStorage` neste artigo, remova `/tezstaging` da variável de `fs.azure.page.blob.dir` configuração.  A configuração pode ser acessada na [interface do usuário](../hdinsight-hadoop-manage-ambari.md)do amAmbari.  Caso contrário, você receberá a mensagem de erro:`Page blob is not supported for this account type.`

> [!WARNING]  
> As etapas neste documento fazem as seguintes suposições:  
>
> * Você cria scripts Python em seu ambiente de desenvolvimento local.
> * Você carrega os scripts no HDInsight usando o `scp` comando ou o script do PowerShell fornecido.
>
> Se você quiser usar o [Azure cloud Shell (bash)](https://docs.microsoft.com/azure/cloud-shell/overview) para trabalhar com o HDInsight, deverá:
>
> * Criar os scripts de dentro do ambiente do Cloud Shell.
> * Usar `scp` para carregar os arquivos do Cloud Shell para o HDInsight.
> * Usar `ssh` do Cloud Shell para conectar-se ao HDInsight e executar os exemplos.

## <a name="hivepython"></a>UDF do Apache Hive

O Python pode ser utilizado como um UDF do Hive por meio da instrução HiveQL `TRANSFORM`. Por exemplo, o seguinte HiveQL invoca o arquivo `hiveudf.py` armazenado na conta de Armazenamento do Azure padrão para o cluster.

```hiveql
add file wasbs:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLabel string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

Aqui está o que este exemplo faz:

1. A instrução `add file` no início do arquivo adiciona o arquivo `hiveudf.py` ao cache distribuído, portanto, está acessível por todos os nós no cluster.
2. A instrução `SELECT TRANSFORM ... USING` seleciona dados do `hivesampletable`. Ela também passa os valores clientid, devicemake e devicemodel para o script `hiveudf.py`.
3. A cláusula `AS` descreve os campos retornados de `hiveudf.py`.

<a name="streamingpy"></a>

### <a name="create-file"></a>Criar arquivo

Em seu ambiente de desenvolvimento, crie um arquivo de texto chamado `hiveudf.py`. Use o código a seguir como o conteúdo do arquivo:

```python
#!/usr/bin/env python
import sys
import string
import hashlib

while True:
    line = sys.stdin.readline()
    if not line:
        break

    line = string.strip(line, "\n ")
    clientid, devicemake, devicemodel = string.split(line, "\t")
    phone_label = devicemake + ' ' + devicemodel
    print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])
```

O script executa as ações a seguir:

1. Lê uma linha de dados de STDIN.
2. O caractere de nova linha é removido usando `string.strip(line, "\n ")`.
3. Ao realizar processamento de fluxo, uma única linha contém todos os valores com um caractere de tabulação entre cada par de valores. Assim, `string.split(line, "\t")` pode ser usado para dividir a entrada em cada guia, retornando somente os campos.
4. Quando o processamento está concluído, a saída precisa ser gravada em STDOUT como uma linha única, com uma tabulação entre cada par de campos. Por exemplo, `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. O loop `while` é repetido até que nenhum `line` seja lido.

A saída do script é uma concatenação dos valores de entrada para `devicemake` e `devicemodel`, e um hash do valor concatenado.

### <a name="upload-file-shell"></a>Carregar arquivo (Shell)
Nos comandos abaixo, substitua `sshuser` pelo nome de usuário real, se for diferente.  Substituir `mycluster` pelo nome real do cluster.  Verifique se o diretório de trabalho é onde o arquivo está localizado.

1. Use `scp` para copiar os arquivos para seu cluster HDInsight. Edite e insira o comando a seguir:

    ```cmd
    scp hiveudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Use SSH para conectar-se ao cluster.  Edite e insira o comando a seguir:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Na sessão do SSH, adicione os arquivos do Python carregados anteriormente para o armazenamento do cluster.

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    ```

### <a name="use-hive-udf-shell"></a>Usar UDF do hive (Shell)

1. Para se conectar ao Hive, use o seguinte comando em sua sessão SSH aberta:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

    Esse comando inicia o cliente Beeline.

2. Insira a seguinte consulta no prompt `0: jdbc:hive2://headnodehost:10001/>`:

   ```hive
   add file wasbs:///hiveudf.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python hiveudf.py' AS
       (clientid string, phoneLabel string, phoneHash string)
   FROM hivesampletable
   ORDER BY clientid LIMIT 50;
   ```

3. Depois de inserir a última linha, o trabalho deve ser iniciado. Quando o trabalho for concluído, ele retornará uma saída semelhante ao exemplo a seguir:

        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

4. Para sair do beeline, digite o seguinte comando:

    ```hive
    !q
    ```

### <a name="upload-file-powershell"></a>Carregar arquivo (PowerShell)

O PowerShell também pode ser usado para executar remotamente consultas do Hive. Verifique se o diretório de trabalho `hiveudf.py` está localizado.  Use o seguinte script do PowerShell para executar uma consulta de Hive que `hiveudf.py` usa o script:

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# Revise file path as needed
$pathToStreamingFile = ".\hiveudf.py"

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
$container=$clusterInfo.DefaultStorageContainer
$storageAccountKey=(Get-AzStorageAccountKey `
   -ResourceGroupName $resourceGroup `
   -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

# Upload local files to an Azure Storage blob
Set-AzStorageBlobContent `
    -File $pathToStreamingFile `
    -Blob "hiveudf.py" `
    -Container $container `
    -Context $context
```

> [!NOTE]  
> Para obter mais informações sobre como carregar arquivos, consulte o documento [Carregar dados para trabalhos do Apache Hadoop no HDInsight](../hdinsight-upload-data.md).


#### <a name="use-hive-udf"></a>Usar UDF do hive


```PowerShell
# Script should stop on failures
$ErrorActionPreference = "Stop"

# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -UserName "admin" -Message "Enter the login for the cluster"

$HiveQuery = "add file wasbs:///hiveudf.py;" +
                "SELECT TRANSFORM (clientid, devicemake, devicemodel) " +
                "USING 'python hiveudf.py' AS " +
                "(clientid string, phoneLabel string, phoneHash string) " +
                "FROM hivesampletable " +
                "ORDER BY clientid LIMIT 50;"

# Create Hive job object
$jobDefinition = New-AzHDInsightHiveJobDefinition `
    -Query $HiveQuery

# For status bar updates
$activity="Hive query"

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Starting query..."

# Start defined Azure HDInsight job on specified cluster.
$job = Start-AzHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Waiting on query to complete..."

# Wait for completion or failure of specified job
Wait-AzHDInsightJob `
    -JobId $job.JobId `
    -ClusterName $clusterName `
    -HttpCredential $creds

# Uncomment the following to see stderr output
<#
Get-AzHDInsightJobOutput `
   -Clustername $clusterName `
   -JobId $job.JobId `
   -HttpCredential $creds `
   -DisplayOutputType StandardError
#>

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Retrieving output..."

# Gets the log output
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

A saída para o trabalho do **Hive** deve ser semelhante ao exemplo a seguir:

    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9


## <a name="pigpython"></a>UDF do Apache Pig

Um script Python pode ser utilizado como um UDF do Pig por meio da instrução `GENERATE`. Você pode executar o script usando o Jython ou o Python C.

* Jython é executado em JVM e pode ser chamado nativamente do Pig.
* O Python C é um processo externo para que os dados do Pig no JVM sejam enviados para o script executado em um processo do Python. A saída do script Python é enviada de volta ao Pig.

Para especificar o interpretador do Python, use `register` ao referenciar o script do Python. Os exemplos a seguir registram os scripts com o Pig como `myfuncs`:

* **Para usar o Jython**: `register '/path/to/pigudf.py' using jython as myfuncs;`
* **Para usar o Python C**: `register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]  
> Ao usar Jython, o caminho para o arquivo pig_jython pode ser um caminho local ou um caminho de WASBS://. No entanto, ao usar o Python C, você deve fazer referência a um arquivo no sistema de arquivos local do nó que está usando para enviar o trabalho de Pig.

Depois do registro, o Pig Latin para o exemplo é o mesmo para ambos:

```pig
LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

Aqui está o que este exemplo faz:

1. A primeira linha carrega o arquivo de dados de exemplo, `sample.log` em `LOGS`. Também define cada registro como `chararray`.
2. A próxima linha filtra e remove quaisquer valores nulos, armazenando o resultado da operação no `LOG`.
3. Em seguida, itera nos registros em `LOG` e usa `GENERATE` para invocar o método `create_structure` contido no script de Python/Jython carregado como `myfuncs`. `LINE` é usado para passar o registro atual para a função.
4. Por fim, as saídas são despejadas em STDOUT usando o comando `DUMP`. Esse comando exibe os resultados após a conclusão da operação.

### <a name="create-file"></a>Criar arquivo

Em seu ambiente de desenvolvimento, crie um arquivo de texto chamado `pigudf.py`. Use o código a seguir como o conteúdo do arquivo:

<a name="streamingpy"></a>

```python
# Uncomment the following if using C Python
#from pig_util import outputSchema


@outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
def create_structure(input):
    if (input.startswith('java.lang.Exception')):
        input = input[21:len(input)] + ' - java.lang.Exception'
    date, time, classname, level, detail = input.split(' ', 4)
    return date, time, classname, level, detail
```

No exemplo Pig Latin, a entrada `LINE` é definida como uma matriz de caracteres porque não existe um esquema consistente para a entrada. O script Python transforma os dados em um esquema consistente para a saída.

1. A instrução `@outputSchema` define o formato dos dados que são retornados ao Pig. Nesse caso, é uma **mala de dados**, que é um tipo de dado do Pig. A mala contém os campos a seguir, todos eles sendo matrizes de caracteres (cadeias de caracteres):

   * date - a data em que a entrada de log foi criada
   * time - o horário em que a entrada de log foi criada
   * classname - o nome da classe para a qual a entrada foi criada
   * level - o nível do log
   * detail - detalhes para a entrada de log

2. Em seguida, o `def create_structure(input)` define a função para a qual o Pig passa itens de linha.

3. Os dados de exemplo, `sample.log`, estão em conformidade com a data, hora, nome da classe, nível e esquema detalhado. No entanto, contêm algumas linhas que começam com `*java.lang.Exception*`. Essas linhas devem ser modificadas para que correspondam ao esquema. A instrução `if` verifica essas linhas e, então, movimenta os dados de entrada para levar a cadeia de caracteres `*java.lang.Exception*` para o final, colocando os dados em linha com o esquema de saída esperado.

4. Em seguida, o comando `split` é utilizado para dividir os dados nos quatro primeiros caracteres de espaço. A saída é atribuída a `date`, `time`, `classname`, `level` e `detail`.

5. Por fim, os valores são devolvidos ao Pig.

Quando os dados são devolvidos ao Pig, eles têm um esquema consistente conforme definido na instrução `@outputSchema`.



### <a name="upload-file-shell"></a>Carregar arquivo (Shell)

Nos comandos abaixo, substitua `sshuser` pelo nome de usuário real, se for diferente.  Substituir `mycluster` pelo nome real do cluster.  Verifique se o diretório de trabalho é onde o arquivo está localizado.

1. Use `scp` para copiar os arquivos para seu cluster HDInsight. Edite e insira o comando a seguir:

    ```cmd
    scp pigudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Use SSH para conectar-se ao cluster.  Edite e insira o comando a seguir:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Na sessão do SSH, adicione os arquivos do Python carregados anteriormente para o armazenamento do cluster.

    ```bash
    hdfs dfs -put pigudf.py /pigudf.py
    ```


### <a name="use-pig-udf-shell"></a>Usar UDF Pig (Shell)

1. Para se conectar ao Pig, use o seguinte comando em sua sessão SSH aberta:

    ```bash
    pig
    ```

2. No prompt `grunt>`, insira as seguintes instruções:

   ```pig
   Register wasbs:///pigudf.py using jython as myfuncs;
   LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. Depois de inserir a linha a seguir, o trabalho será iniciado. Quando o trabalho for concluído, ele retornará uma saída semelhante aos dados a seguir:

        ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
        ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
        ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
        ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
        ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

4. Use `quit` para sair do shell do Grunt e use o seguinte para editar o arquivo pigudf.py no sistema de arquivos local:

    ```bash
    nano pigudf.py
    ```

5. No editor, remova a seguinte linha removendo o caractere `#` do início da linha:

    ```bash
    #from pig_util import outputSchema
    ```

    Essa linha modifica o script Python para trabalhar com Python C em vez de Jython. Depois que a alteração for feita, use **Ctrl+X** para sair do editor. Selecione **Y** e **Enter** para salvar as alterações.

6. Use o comando `pig` para iniciar o shell novamente. No prompt `grunt>` , use o que segue para executar o script de Python usando o interpretador de Python C.

   ```pig
   Register 'pigudf.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    Quando o trabalho for concluído, você verá a mesma saída de quando executou o script usando Jython.


### <a name="upload-file-powershell"></a>Carregar arquivo (PowerShell)

O PowerShell também pode ser usado para executar remotamente consultas do Hive. Verifique se o diretório de trabalho `pigudf.py` está localizado.  Use o seguinte script do PowerShell para executar uma consulta de Hive que `pigudf.py` usa o script:

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# Revise file path as needed
$pathToJythonFile = ".\pigudf.py"


# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
$container=$clusterInfo.DefaultStorageContainer
$storageAccountKey=(Get-AzStorageAccountKey `
   -ResourceGroupName $resourceGroup `
   -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

# Upload local files to an Azure Storage blob
Set-AzStorageBlobContent `
    -File $pathToJythonFile `
    -Blob "pigudf.py" `
    -Container $container `
    -Context $context
```

### <a name="use-pig-udf-powershell"></a>Usar UDF Pig (PowerShell)

> [!NOTE]  
> Ao enviar um trabalho remotamente usando o PowerShell, não é possível usar o Python C como interpretador.

O PowerShell também pode ser usado para executar trabalhos do Pig Latin. Para executar um trabalho Pig Latin que usa o `pigudf.py` script, use o seguinte script do PowerShell:

```PowerShell
# Script should stop on failures
$ErrorActionPreference = "Stop"

# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -UserName "admin" -Message "Enter the login for the cluster"


$PigQuery = "Register wasbs:///pigudf.py using jython as myfuncs;" +
            "LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);" +
            "LOG = FILTER LOGS by LINE is not null;" +
            "DETAILS = foreach LOG generate myfuncs.create_structure(LINE);" +
            "DUMP DETAILS;"

# Create Pig job object
$jobDefinition = New-AzHDInsightPigJobDefinition -Query $PigQuery

# For status bar updates
$activity="Pig job"

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Starting job..."

# Start defined Azure HDInsight job on specified cluster.
$job = Start-AzHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Waiting for the Pig job to complete..."

# Wait for completion or failure of specified job
Wait-AzHDInsightJob `
    -Job $job.JobId `
    -ClusterName $clusterName `
    -HttpCredential $creds

# Uncomment the following to see stderr output
<#
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds `
    -DisplayOutputType StandardError
#>

# Progress bar (optional)
Write-Progress -Activity $activity "Retrieving output..."

# Gets the log output
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

A saída para o trabalho **Pig** deve ser parecida com os seguintes dados:

    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

## <a name="troubleshooting"></a>Solucionar problemas

### <a name="errors-when-running-jobs"></a>Erros durante a execução de trabalhos

Ao executar o trabalho do hive, você poderá encontrar um erro semelhante ao texto a seguir:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Esse problema pode ser causado pelas terminações de linha no arquivo do Python. Muitos editores Windows usam CRLF como padrão como a terminação de linha, mas aplicativos Linux geralmente esperam LF.

Você pode seguir as seguintes instruções do PowerShell para remover os caracteres CR antes de carregar o arquivo no HDInsight:

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=148-150)]

### <a name="powershell-scripts"></a>Scripts do PowerShell

Ambos os scripts de exemplo do PowerShell usados para executar os exemplos contêm uma linha comentada que exibe a saída de erro do trabalho. Se você não estiver vendo a saída esperada para o trabalho, remova o comentário da linha a seguir e veja se as informações de erro indicam um problema.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=135-139)]

As informações de erro (STDERR) e o resultado do trabalho (STDOUT) também são registrados em seu armazenamento do HDInsight.

| Para este trabalho… | Veja estes arquivos no contêiner blob |
| --- | --- |
| Hive |/HivePython/stderr<p>/HivePython/stdout |
| Pig |/PigPython/stderr<p>/PigPython/stdout |

## <a name="next"></a>Próximas etapas

Se você precisar carregar módulos do Python que não são fornecidos por padrão, consulte [Como implantar um módulo para o HDInsight do Azure](https://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx).

Para obter outras formas de usar o Pig e o Hive e para saber como usar o MapReduce, consulte os documentos a seguir:

* [Usar o Apache Hive com o HDInsight](hdinsight-use-hive.md)
* [Usar o Apache Pig com o HDInsight](hdinsight-use-pig.md)
* [Usar o MapReduce com o HDInsight](hdinsight-use-mapreduce.md)
