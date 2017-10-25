---
title: "Python UDF com Apache Hive e Pig – HDInsight do Azure | Microsoft Docs"
description: "Saiba como usar UDFs (Funções Definidas pelo Usuário) do Python com o Hive e o Pig no HDInsight, a pilha de tecnologias do Hadoop no Azure."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c44d6606-28cd-429b-b535-235e8f34a664
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 10/06/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 766829e1a35a733888973689897ddc4fe66e00a0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-python-user-defined-functions-udf-with-hive-and-pig-in-hdinsight"></a>Usar as UDF (Funções Definidas pelo Usuário) do Python com o Hive e o Pig no HDInsight

Saiba como usar funções definidas pelo usuário do Python (UDF) com o Apache Hive e o Pig no Hadoop no Azure HDInsight.

## <a name="python"></a>Python no HDInsight

O Python 2.7 é instalado por padrão no HDInsight 3.0 e posteriores. O Apache Hive pode ser usado com essa versão do Python para processamento de fluxo. O processamento de fluxo usa STDOUT e STDIN para enviar dados entre o Hive e o UDF.

O HDInsight também inclui o Jython, que é uma implementação do Python gravada em Java. Jython é executado diretamente na Máquina Virtual Java e não usa streaming. Jython é o interpretador do Python recomendado ao usar Python com Pig.

> [!WARNING]
> As etapas neste documento fazem as seguintes suposições: 
>
> * Você cria scripts Python em seu ambiente de desenvolvimento local.
> * Você carregar os scripts para o HDInsight usando o comando `scp` de uma sessão de Bash local ou o script do PowerShell fornecido.
>
> Se você quiser usar a versão prévia do [Azure Cloud Shell (bash)](https://docs.microsoft.com/azure/cloud-shell/overview) para trabalhar com o HDInsight, você deverá:
>
> * Criar os scripts de dentro do ambiente do Cloud Shell.
> * Usar `scp` para carregar os arquivos do Cloud Shell para o HDInsight.
> * Usar `ssh` do Cloud Shell para conectar-se ao HDInsight e executar os exemplos.

## <a name="hivepython"></a>UDF do Hive

O Python pode ser utilizado como um UDF do Hive por meio da instrução HiveQL `TRANSFORM`. Por exemplo, o seguinte HiveQL invoca o arquivo `hiveudf.py` armazenado na conta de Armazenamento do Azure padrão para o cluster.

**HDInsight baseado em Linux**

```hiveql
add file wasb:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLable string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

**HDInsight baseado em Windows**

```hiveql
add file wasb:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'D:\Python27\python.exe hiveudf.py' AS
    (clientid string, phoneLable string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

> [!NOTE]
> Em clusters de HDInsight baseados no Windows, a cláusula `USING` deve especificar o caminho completo para python.exe.

Aqui está o que este exemplo faz:

1. A instrução `add file` no início do arquivo adiciona o arquivo `hiveudf.py` ao cache distribuído, portanto, está acessível por todos os nós no cluster.
2. A instrução `SELECT TRANSFORM ... USING` seleciona dados do `hivesampletable`. Ela também passa os valores clientid, devicemake e devicemodel para o script `hiveudf.py`.
3. A cláusula `AS` descreve os campos retornados de `hiveudf.py`.

<a name="streamingpy"></a>

### <a name="create-the-hiveudfpy-file"></a>Criar o arquivo hiveudf.py


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

1. Ler uma linha de dados do STDIN.
2. O caractere de nova linha é removido usando `string.strip(line, "\n ")`.
3. Ao realizar processamento de fluxo, uma única linha contém todos os valores com um caractere de tabulação entre cada par de valores. Assim, `string.split(line, "\t")` pode ser usado para dividir a entrada em cada guia, retornando somente os campos.
4. Quando o processamento está concluído, a saída precisa ser gravada em STDOUT como uma linha única, com uma tabulação entre cada par de campos. Por exemplo: `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. O loop `while` é repetido até que nenhum `line` seja lido.

A saída do script é uma concatenação dos valores de entrada para `devicemake` e `devicemodel`, e um hash do valor concatenado.

Consulte [Executando os exemplos](#running) para saber como executar este exemplo em seu cluster HDInsight.

## <a name="pigpython"></a>UDF do Pig

Um script Python pode ser utilizado como um UDF do Pig por meio da instrução `GENERATE`. Você pode executar o script usando o Jython ou o Python C.

* Jython é executado em JVM e pode ser chamado nativamente do Pig.
* O Python C é um processo externo para que os dados do Pig no JVM sejam enviados para o script executado em um processo do Python. A saída do script Python é enviada de volta ao Pig.

Para especificar o interpretador do Python, use `register` ao referenciar o script do Python. Os exemplos a seguir registram os scripts com o Pig como `myfuncs`:

* **Para usar o Jython**: `register '/path/to/pigudf.py' using jython as myfuncs;`
* **Para usar o Python C**: `register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]
> Ao usar o Jython, o caminho para o arquivo pig_jython pode ser um caminho local ou um caminho WASB://. No entanto, ao usar o Python C, você deve fazer referência a um arquivo no sistema de arquivos local do nó que está usando para enviar o trabalho de Pig.

Depois do registro, o Pig Latin para o exemplo é o mesmo para ambos:

```pig
LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

Aqui está o que este exemplo faz:

1. A primeira linha carrega o arquivo de dados de exemplo, `sample.log` em `LOGS`. Também define cada registro como `chararray`.
2. A próxima linha filtra e remove quaisquer valores nulos, armazenando o resultado da operação no `LOG`.
3. Em seguida, itera nos registros em `LOG` e usa `GENERATE` para invocar o método `create_structure` contido no script de Python/Jython carregado como `myfuncs`. `LINE` é usado para passar o registro atual para a função.
4. Por fim, as saídas são despejadas em STDOUT usando o comando `DUMP`. Esse comando exibe os resultados após a conclusão da operação.

### <a name="create-the-pigudfpy-file"></a>Criar o arquivo pigudf.py

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

No exemplo de Pig Latin, definimos a entrada `LINE` como um chararray porque não há nenhum esquema consistente para a entrada. O script Python transforma os dados em um esquema consistente para a saída.

1. A instrução `@outputSchema` define o formato dos dados que são retornados ao Pig. Nesse caso, é uma **mala de dados**, que é um tipo de dado do Pig. A mala contém os campos a seguir, todos eles sendo matrizes de caracteres (cadeias de caracteres):

   * date - a data em que a entrada de log foi criada
   * time - o horário em que a entrada de log foi criada
   * classname - o nome da classe para a qual a entrada foi criada
   * level - o nível do log
   * detail - detalhes para a entrada de log

2. Em seguida, o `def create_structure(input)` define a função para a qual o Pig passa itens de linha.

3. Os dados de exemplo, `sample.log`, na maior parte das vezes estão em conformidade com o esquema de data, horário, nome de classe, nível e detalhe que desejamos retornar. No entanto, contêm algumas linhas que começam com `*java.lang.Exception*`. Essas linhas devem ser modificadas para que correspondam ao esquema. A instrução `if` verifica essas linhas, então movimenta os dados de entrada para levar a cadeia de caracteres `*java.lang.Exception*` para o final, colocando os dados em linha com o nosso esquema de saída esperado.

4. Em seguida, o comando `split` é utilizado para dividir os dados nos quatro primeiros caracteres de espaço. A saída é atribuída a `date`, `time`, `classname`, `level` e `detail`.

5. Por fim, os valores são devolvidos ao Pig.

Quando os dados são devolvidos ao Pig, eles têm um esquema consistente conforme definido na instrução `@outputSchema`.

## <a name="running"></a>Carregar e executar os exemplos

> [!IMPORTANT]
> As etapas de **SSH** funcionam apenas com um cluster do HDInsight baseado em Linux. As etapas do **PowerShell** funcionam com um cluster do HDInsight baseado em Windows ou Linux, mas requer um cliente Windows.

### <a name="ssh"></a>SSH

Para saber mais sobre como usar SSH, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

1. Use `scp` para copiar os arquivos para seu cluster HDInsight. Por exemplo, o comando a seguir copia os arquivos para um cluster chamado **mycluster**.

    ```bash
    scp hiveudf.py pigudf.py myuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Use SSH para conectar-se ao cluster.

    ```bash
    ssh myuser@mycluster-ssh.azurehdinsight.net
    ```

    Para saber mais, consulte o documento [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Na sessão de SSH, adicione ao cluster os arquivos de python carregados no armazenamento WASB anteriormente.

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    hdfs dfs -put pigudf.py /pigudf.py
    ```

Após carregar os arquivos, use as etapas a seguir para executar os trabalhos de Hive e Pig.

#### <a name="use-the-hive-udf"></a>Usar UDF do Hive

1. Para se conectar ao Hive, use o seguinte comando:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

    Esse comando inicia o cliente Beeline.

2. Insira a seguinte consulta no prompt `0: jdbc:hive2://headnodehost:10001/>`:

   ```hive
   add file wasb:///hiveudf.py;
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

4. Para sair do Beeline, use o seguinte comando:

    ```hive
    !q
    ```

#### <a name="use-the-pig-udf"></a>Usar UDF do Pig

1. Para se conectar ao pig, use o seguinte comando:

    ```bash
    pig
    ```

2. No prompt `grunt>`, insira as seguintes instruções:

   ```pig
   Register wasb:///pigudf.py using jython as myfuncs;
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

    Isso modifica o script Python para trabalhar com Python C em vez de Jython. Depois que a alteração for feita, use **Ctrl+X** para sair do editor. Selecione **Y** e **Enter** para salvar as alterações.

6. Use o comando `pig` para iniciar o shell novamente. No prompt `grunt>` , use o que segue para executar o script de Python usando o interpretador de Python C.

   ```pig
   Register 'pigudf.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    Quando o trabalho for concluído, você verá a mesma saída de quando executou o script usando Jython.

### <a name="powershell-upload-the-files"></a>PowerShell: carregar os arquivos

Você pode usar o PowerShell para carregar os arquivos para o servidor do HDInsight. Use o script a seguir para carregar os arquivos do Python:

> [!IMPORTANT] 
> As etapas nesta seção usam o Azure PowerShell. Para obter mais informações sobre como usar o Azure PowerShell, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

[!code-powershell[main](../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=5-41)]

> [!IMPORTANT]
> Alterar o valor `C:\path\to` para o caminho para os arquivos no seu ambiente de desenvolvimento.

Este script obtém informações a partir de seu cluster HDInsight, então, extrai a conta e a chave para a conta de armazenamento padrão, além de carregar os arquivos para a raiz do contêiner.

> [!NOTE]
> Para obter mais informações sobre como carregar arquivos, consulte o documento [Carregar dados para trabalhos do Hadoop no HDInsight](hdinsight-upload-data.md).

#### <a name="powershell-use-the-hive-udf"></a>PowerShell: usar UDF do Hive

O PowerShell também pode ser usado para executar remotamente consultas do Hive. Use o seguinte script do PowerShell para executar uma consulta do Hive que use o script **hiveudf.py**:

> [!IMPORTANT]
> Antes da execução, o script o solicita a fornecer as informações de HTTPs/conta do administrador do seu cluster do HDInsight.

[!code-powershell[main](../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=45-94)]

A saída para o trabalho do **Hive** deve ser semelhante ao exemplo a seguir:

    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

#### <a name="pig-jython"></a>Pig (Jython)

O PowerShell também pode ser usado para executar trabalhos do Pig Latin. Para executar um trabalho do Pig Latin que use o script **pigudf.py**, utilize o seguinte script do PowerShell:

> [!NOTE]
> Ao enviar um trabalho remotamente usando o PowerShell, não é possível usar o Python C como interpretador.

[!code-powershell[main](../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=98-144)]

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

[!code-powershell[main](../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=148-150)]

### <a name="powershell-scripts"></a>Scripts do PowerShell

Ambos os scripts de exemplo do PowerShell usados para executar os exemplos contêm uma linha comentada que exibe a saída de erro do trabalho. Se você não estiver vendo a saída esperada para o trabalho, remova o comentário da linha a seguir e veja se as informações de erro indicam um problema.

[!code-powershell[main](../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=135-139)]

As informações de erro (STDERR) e o resultado do trabalho (STDOUT) também são registrados em seu armazenamento do HDInsight.

| Para este trabalho… | Veja estes arquivos no contêiner blob |
| --- | --- |
| Hive |/HivePython/stderr<p>/HivePython/stdout |
| Pig |/PigPython/stderr<p>/PigPython/stdout |

## <a name="next"></a>Próximas etapas

Se você precisar carregar módulos do Python que não são fornecidos por padrão, consulte [Como implantar um módulo para o HDInsight do Azure](http://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx).

Para obter outras formas de usar o Pig e o Hive e para saber como usar o MapReduce, consulte os documentos a seguir:

* [Usar o Hive com o HDInsight](hdinsight-use-hive.md)
* [Usar o Pig com o HDInsight](hdinsight-use-pig.md)
* [Usar o MapReduce com o HDInsight](hdinsight-use-mapreduce.md)
