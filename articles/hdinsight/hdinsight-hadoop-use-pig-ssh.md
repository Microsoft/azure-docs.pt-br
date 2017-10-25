---
title: "Usar o Hadoop Pig com o SSH em um cluster HDInsight – Azure | Microsoft Docs"
description: "Saiba como conectar a um cluster Hadoop baseado em Linux com o SSH e use o comando Pig para executar instruções Pig Latin interativamente ou como um trabalho em lotes."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: b646a93b-4c51-4ba4-84da-3275d9124ebe
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/03/2017
ms.author: larryfr
ms.openlocfilehash: 303a8de4f644b11bda68136fe42af6bc091bb892
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="run-pig-jobs-on-a-linux-based-cluster-with-the-pig-command-ssh"></a>Executar trabalhos do Pig em um cluster baseado em Linux com o comando Pig (SSH)

[!INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Saiba como executar trabalhos do Pig interativamente de uma conexão SSH para seu cluster HDInsight. A linguagem de programação Pig Latin permite descrever as transformações que são aplicadas aos dados de entrada para produzir a saída desejada.

> [!IMPORTANT]
> As etapas deste documento exigem um cluster HDInsight baseado em Linux. O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a id="ssh"></a>Conexão com o SSH

Use o SSH para conectar-se ao cluster HDInsight. O exemplo a seguir se conecta a um cluster chamado **myhdinsight** como a conta denominada **sshuser**:

    ssh sshuser@myhdinsight-ssh.azurehdinsight.net

**Se você tiver fornecido uma chave de certificado para autenticação de SSH** ao criar o cluster HDInsight, talvez seja necessário especificar o local da chave privada no sistema cliente.

    ssh sshuser@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Se você forneceu uma senha para autenticação SSH** ao criar o cluster HDInsight, forneça a senha quando solicitado.

Para saber mais sobre como usar o SSH com HDInsight, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="pig"></a>Usar o comando Pig

1. Uma vez conectado, inicie a CLI (interface de linha de comando) do Pig usando o comando a seguir:

        pig

    Após um momento, você deverá ver um prompt `grunt>` .

2. Insira a instrução a seguir:

        LOGS = LOAD '/example/data/sample.log';

    Esse comando carrega o conteúdo do arquivo sample.log em LOGS. Você pode exibir o conteúdo do arquivo usando a seguinte instrução:

        DUMP LOGS;

3. Em seguida, transforme os dados aplicando uma expressão regular para extrair apenas o nível de registro em log de cada registro usando a seguinte instrução:

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

    Você pode usar **DUMP** para exibir os dados após a transformação. Nesse caso, use `DUMP LEVELS;`.

4. Continue a aplicar transformações usando as instruções na tabela a seguir:

    | Instrução de Pig Latin | O que a instrução faz |
    | ---- | ---- |
    | `FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;` | Remove as linhas que contêm um valor nulo para o nível de log e armazena os resultados em `FILTEREDLEVELS`. |
    | `GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;` | Agrupa as linhas pelo nível de log e armazena os resultados em `GROUPEDLEVELS`. |
    | `FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;` | Cria um conjunto de dados que contém cada valor de nível de log exclusivo e quantas vezes ele ocorre. O conjunto de dados é armazenado em `FREQUENCIES`. |
    | `RESULT = order FREQUENCIES by COUNT desc;` | Ordena os níveis de log por contagem (decrescente) e armazena em `RESULT`. |

    > [!TIP]
    > Use `DUMP` para exibir o resultado da transformação após cada etapa.

5. Você também pode salvar os resultados de uma transformação usando a instrução `STORE` . Por exemplo, a instrução a seguir salva o `RESULT` no diretório `/example/data/pigout` no armazenamento padrão para seu cluster:

        STORE RESULT into '/example/data/pigout';

   > [!NOTE]
   > Os dados são armazenados no diretório especificado nos arquivos chamados `part-nnnnn`. Se o diretório já existir, você receberá um erro.

6. Para sair do prompt do grunt, insira a instrução a seguir:

        QUIT;

### <a name="pig-latin-batch-files"></a>Arquivos de lote do Pig Latin

Você também pode usar o comando Pig para executar o Pig Latin contido em um arquivo.

1. Depois de sair do prompt do grunt, use o seguinte comando para canalizar STDIN em um arquivo chamado `pigbatch.pig`. Esse arquivo é criado no diretório inicial para a conta de usuário do SSH.

        cat > ~/pigbatch.pig

2. Digite ou cole as seguintes linhas e use Ctrl+D quando terminar.

        LOGS = LOAD '/example/data/sample.log';
        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;

3. Use o seguinte comando para executar o arquivo `pigbatch.pig` utilizando o comando Pig.

        pig ~/pigbatch.pig

    Quando o trabalho em lotes for concluído, você verá o seguinte resultado:

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)


## <a id="nextsteps"></a>Próximas etapas

Para obter informações gerais sobre como usar o Pig no HDInsight, consulte o seguinte documento:

* [Usar o Pig com Hadoop no HDInsight](hdinsight-use-pig.md)

Para saber mais sobre outras maneiras de trabalhar com o Hadoop no HDInsight, consulte os seguintes documentos:

* [Usar o Hive com Hadoop no HDInsight](hdinsight-use-hive.md)
* [Usar o MapReduce com Hadoop no HDInsight](hdinsight-use-mapreduce.md)
