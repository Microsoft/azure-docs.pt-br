---
title: Tutorial - Usar o Apache Storm para gravar no Armazenamento/Data Lake Storage - Azure HDInsight
description: Tutorial - Saiba como usar o Apache Storm para gravar no armazenamento compatível com o HDFS para Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/24/2019
ms.openlocfilehash: 5c1376c7d1afe9c9702cfb43a146ac1cd17d6e58
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67428356"
---
# <a name="tutorial-write-to-apache-hadoop-hdfs-from-apache-storm-on-azure-hdinsight"></a>Tutorial: Gravar no HDFS do Apache Hadoop usando o Apache Storm no Azure HDInsight

Este tutorial demonstra como usar o Apache Storm para gravar dados no armazenamento compatível com HDFS usado pelo Apache Storm no HDInsight. O HDInsight pode usar o Armazenamento do Microsoft Azure e o Azure Data Lake Storage como armazenamento compatível com HDFS. O Storm fornece um componente [HdfsBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) que grava dados no HDFS. Esse documento fornece informações sobre como gravar em qualquer um dos tipos de armazenamento do HdfsBolt.

A topologia de exemplo usada neste documento depende de componentes que estão incluídos com o Storm no HDInsight. Pode exigir modificações para funcionar com o Azure Data Lake Storage quando usado com outros clusters do Apache Storm.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar o cluster com a ação de script
> * Compilar e criar o pacote da topologia
> * Implantar e executar a topologia
> * Exibir dados de saída
> * Parar a topologia

## <a name="prerequisites"></a>Pré-requisitos

* [JDK (Java Developer Kit) versão 8](https://aka.ms/azure-jdks)

* [Apache Maven](https://maven.apache.org/download.cgi) corretamente [instalado](https://maven.apache.org/install.html) de acordo com o Apache.  O Maven é um sistema de construção de projetos para projetos Java.

* Um cliente SSH. Para saber mais, confira [Conectar-se ao HDInsight (Apache Hadoop) usando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* O [esquema de URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) do seu armazenamento primário de clusters. Isso seria `wasb://` para o Armazenamento do Azure, `abfs://` para o Azure Data Lake Storage Gen2 ou `adl://` para o Azure Data Lake Storage Gen1. Se a transferência segura estiver habilitada para o Armazenamento do Azure ou para o Data Lake Storage Gen2, o URI será `wasbs://` ou `abfss://`, respectivamente. Confira também [transferência segura](../../storage/common/storage-require-secure-transfer.md).

### <a name="example-configuration"></a>Exemplo de configuração

O YAML a seguir é um trecho do arquivo `resources/writetohdfs.yaml` incluído no exemplo. Esse arquivo define a topologia do Storm usando a estrutura [Flux](https://storm.apache.org/releases/1.1.2/flux.html) para o Apache Storm.

```yaml
components:
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1000

  # Rotate files when they hit 5 MB
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - "MB"

  - id: "fileNameFormat"
    className: "org.apache.storm.hdfs.bolt.format.DefaultFileNameFormat"
    configMethods:
      - name: "withPath"
        args: ["${hdfs.write.dir}"]
      - name: "withExtension"
        args: [".txt"]

  - id: "recordFormat"
    className: "org.apache.storm.hdfs.bolt.format.DelimitedRecordFormat"
    configMethods:
      - name: "withFieldDelimiter"
        args: ["|"]

# spout definitions
spouts:
  - id: "tick-spout"
    className: "com.microsoft.example.TickSpout"
    parallelism: 1


# bolt definitions
bolts:
  - id: "hdfs-bolt"
    className: "org.apache.storm.hdfs.bolt.HdfsBolt"
    configMethods:
      - name: "withConfigKey"
        args: ["hdfs.config"]
      - name: "withFsUrl"
        args: ["${hdfs.url}"]
      - name: "withFileNameFormat"
        args: [ref: "fileNameFormat"]
      - name: "withRecordFormat"
        args: [ref: "recordFormat"]
      - name: "withRotationPolicy"
        args: [ref: "rotationPolicy"]
      - name: "withSyncPolicy"
        args: [ref: "syncPolicy"]
```

Esse YAML define os seguintes itens:

* `syncPolicy`: Define quando arquivos são sincronizados/liberados para o sistema de arquivos. Neste exemplo, a cada 1.000 tuplas.
* `fileNameFormat`: Define o padrão de nome de arquivo e caminho a ser usado ao gravar os arquivos. Neste exemplo, o caminho é fornecido no tempo de execução usando um filtro e a extensão de arquivo é `.txt`.
* `recordFormat`: Define o formato interno dos arquivos gravados. Neste exemplo, os campos são delimitados pelo caractere `|`.
* `rotationPolicy`: Define quando girar arquivos. Neste exemplo, nenhuma rotação é executada.
* `hdfs-bolt`: Usa os componentes anteriores como parâmetros de configuração para a classe `HdfsBolt`.

Para obter mais informações sobre a estrutura do Flux, consulte [https://storm.apache.org/releases/current/flux.html](https://storm.apache.org/releases/current/flux.html).

## <a name="configure-the-cluster"></a>Configurar o cluster

Por padrão, o Storm no HDInsight não inclui os componentes que o `HdfsBolt` usa para se comunicar com o Armazenamento do Microsoft Azure ou o Data Lake Storage no classpath do Storm. Use a ação de script a seguir para adicionar esses componentes ao diretório `extlib` para Storm no seu cluster:

| Propriedade | Valor |
|---|---|
|Tipo de script |- Personalizado|
|URI do script Bash |`https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`|
|Tipo(s) de nó |Nimbus, Supervisor|
|parâmetros |Nenhum|

Para obter informações sobre como usar esse script com o cluster, consulte o documento [Personalizar clusters do HDInsight usando ações de script](./../hdinsight-hadoop-customize-cluster-linux.md).

## <a name="build-and-package-the-topology"></a>Compilar e criar o pacote da topologia

1. Faça download do projeto de exemplo em [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) para seu ambiente de desenvolvimento.

2. De um prompt de comando, terminal ou shell sessão, altere diretórios para a raiz do projeto baixado. Para criar e empacotar a topologia, use o seguinte comando:

    ```cmd
    mvn compile package
    ```

    Depois de terminar de criar e empacotar, há um novo diretório chamado `target` contendo um arquivo denominado `StormToHdfs-1.0-SNAPSHOT.jar`. Esse arquivo contém a topologia compilada.

## <a name="deploy-and-run-the-topology"></a>Implantar e executar a topologia

1. Use o seguinte comando para copiar a topologia para o cluster HDInsight. Substitua `CLUSTERNAME` pelo nome do cluster.

    ```cmd
    scp target\StormToHdfs-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
    ```

1. Quando o carregamento for concluído, use o seguinte para se conectar ao cluster HDInsight usando o SSH. Substitua `CLUSTERNAME` pelo nome do cluster.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Depois de conectado, use o seguinte comando para criar um arquivo chamado `dev.properties`:

    ```bash
    nano dev.properties
    ```

1. Use o texto a seguir como o conteúdo do arquivo `dev.properties`. Revise conforme necessário com base em seu [esquema de URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme).

    ```
    hdfs.write.dir: /stormdata/
    hdfs.url: wasbs:///
    ```

    Para salvar o arquivo, use __Ctrl + X__, em seguida, __Y__ e, por fim, __Enter__. Os valores desse arquivo definem a URL do armazenamento e o nome do diretório no qual os dados são gravados.

1. Use o seguinte comando para iniciar a topologia:

    ```bash
    storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties
    ```

    Esse comando inicia a topologia usando a estrutura Flux, enviando-a para o nó Nimbus do cluster. A topologia é definida pelo arquivo `writetohdfs.yaml` incluído no jar. O arquivo `dev.properties` é passado como um filtro e os valores contidos no arquivo são lidos pela topologia.

## <a name="view-output-data"></a>Exibir dados de saída

Para exibir os dados, use o seguinte comando:

  ```bash
  hdfs dfs -ls /stormdata/
  ```

Uma lista dos arquivos criados por essa topologia é exibida. A lista a seguir é um exemplo dos dados retornados pelos comandos anteriores:

```output
Found 23 items
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-0-1561407909895.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-1-1561407915577.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-10-1561407943327.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-11-1561407946312.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-12-1561407949320.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-13-1561407952662.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-14-1561407955502.txt
```

## <a name="stop-the-topology"></a>Parar a topologia

As topologias do Storm são executadas até serem interrompidas ou o cluster ser excluído. Para interromper a topologia, use o seguinte comando:

```bash
storm kill hdfswriter
```

## <a name="clean-up-resources"></a>Limpar recursos

Para limpar os recursos criados por este tutorial, você pode excluir o grupo de recursos. A exclusão do grupo de recursos também exclui o cluster HDInsight associado e todos os outros recursos associados ao grupo de recursos.

Para remover o grupo de recursos usando o portal do Azure:

1. No portal do Azure, expanda o menu à esquerda para abrir o menu de serviços e escolha __Grupo de Recursos__ para exibir a lista dos seus grupos de recursos.
2. Localize o grupo de recursos a ser excluído e clique com o botão direito do mouse no botão __Mais__ (...) do lado direito da lista.
3. Selecione __Excluir grupo de recursos__ e confirme.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a usar o Apache Storm para gravar dados no armazenamento compatível com HDFS usado pelo Apache Storm no HDInsight.

> [!div class="nextstepaction"]
> Descubra outros [exemplos do Apache Storm para HDInsight](apache-storm-example-topology.md)