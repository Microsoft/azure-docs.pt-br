---
title: "Gravação do Apache Storm para Armazenamento/Data Lake Store – HDInsight do Azure | Microsoft Docs"
description: "Saiba como usar o Apache Storm para gravar no armazenamento compatível com o HDFS para HDInsight. Azure Storage ou Azure Data Lake Store fornece o armazenamento de compatível com HDFS para HDInsight. Este documento e o exemplo associado demonstram como o componente de HdfsBolt pode ser usado para gravar o armazenamento padrão de um Storm no cluster HDInsight."
services: hdinsight
documentationcenter: na
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 1df98653-a6c8-4662-a8c6-5d288fc4f3a6
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/11/2017
ms.author: larryfr
ms.openlocfilehash: d1f629ffb4ec3e6473e0a9a87c4e397d63310e9a
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2017
---
# <a name="write-to-hdfs-from-apache-storm-on-hdinsight"></a>Gravar para HDFS do Apache Storm no HDInsight

Saiba como usar o Storm para gravar dados no armazenamento compatível com HDFS usado pelo Apache Storm no HDInsight. O HDInsight pode usar o Armazenamento do Azure e o Azure Data Lake Store como armazenamento compatível com HDFS. O Storm fornece um componente [HdfsBolt](http://storm.apache.org/releases/1.1.0/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) que grava dados no HDFS. Esse documento fornece informações sobre como gravar em qualquer um dos tipos de armazenamento do HdfsBolt. 

> [!IMPORTANT]
> A topologia de exemplo usada neste documento depende de componentes que estão incluídos com o Storm no HDInsight. Pode exigir modificações para funcionar com o Azure Data Lake Store quando usado com outros clusters do Apache Storm.

## <a name="get-the-code"></a>Obter código

O projeto que contém essa topologia está disponível como um download em [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store).

Para compilar esse projeto, você precisará da seguinte configuração para seu ambiente de desenvolvimento:

* [Java JDK 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) ou superior. HDInsight 3.5 ou superior exige Java 8.

* [Maven 3.x](https://maven.apache.org/download.cgi)

As seguintes variáveis de ambiente podem ser definidas quando você instala o Java e o JDK em sua estação de trabalho de desenvolvimento. No entanto, você deve verificar se elas existem e se contêm os valores corretos para o seu sistema.

* `JAVA_HOME` – deve apontar para o diretório em que o JDK está instalado.
* `PATH` – deve conter os seguintes caminhos:
  
    * `JAVA_HOME` (ou o caminho equivalente).
    * `JAVA_HOME\bin` (ou o caminho equivalente).
    * O diretório em que o Maven está instalado.

## <a name="how-to-use-the-hdfsbolt-with-hdinsight"></a>Como usar o HdfsBolt com HDInsight

> [!IMPORTANT]
> Antes de usar o HdfsBolt com Storm no HDInsight, primeiro você deve usar uma ação de script para copiar arquivos jar necessários para o `extpath` para o Storm. Para obter mais informações, consulte a seção [Configurar o cluster](#configure).

O HdfsBolt usa o esquema de arquivo que você fornece para compreender como gravar no HDFS. Com o HDInsight, use um dos seguintes esquemas:

* `wasb://`: usado com uma conta de Armazenamento do Azure.
* `adl://`: usado com o Azure Data Lake Store.

A tabela a seguir fornece exemplos de como usar o esquema de arquivos para cenários diferentes:

| Esquema | Observações |
| ----- | ----- |
| `wasb:///` | A conta de armazenamento padrão é um contêiner de blob em uma conta de armazenamento do Azure |
| `adl:///` | A conta de armazenamento padrão é um diretório no Azure Data Lake Store. Durante a criação do cluster, você pode especificar o diretório no Data Lake Store que é a raiz do HDFS do cluster. Por exemplo, o diretório `/clusters/myclustername/`. |
| `wasb://CONTAINER@ACCOUNT.blob.core.windows.net/` | Uma conta de armazenamento do Azure não padrão (adicional) associada ao cluster. |
| `adl://STORENAME/` | A raiz do Data Lake Store usada pelo cluster. Esse esquema permite que você acesse dados localizados fora do diretório que contém o sistema de arquivos do cluster. |

Para obter mais informações, consulte a referência [HdfsBolt](http://storm.apache.org/releases/1.1.0/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) em Apache.org.

### <a name="example-configuration"></a>Exemplo de configuração

O YAML a seguir é um trecho do arquivo `resources/writetohdfs.yaml` incluído no exemplo. Esse arquivo define a topologia do Storm usando a estrutura [Flux](https://storm.apache.org/releases/1.1.0/flux.html) para o Apache Storm.

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

* `syncPolicy`: define quando arquivos são sincronizados/liberados para o sistema de arquivos. Neste exemplo, a cada 1.000 tuplas.
* `fileNameFormat`: define o padrão de nome de arquivo e caminho a ser usado ao gravar os arquivos. Neste exemplo, o caminho é fornecido no tempo de execução usando um filtro e a extensão de arquivo é `.txt`.
* `recordFormat`: define o formato interno dos arquivos gravados. Neste exemplo, os campos são delimitados pelo caractere `|`.
* `rotationPolicy`: define quando girar arquivos. Neste exemplo, nenhuma rotação é executada.
* `hdfs-bolt`: usa os componentes anteriores como parâmetros de configuração para a classe `HdfsBolt`.

Para saber mais sobre a estrutura Flux, confirma [https://storm.apache.org/releases/1.1.0/flux.html](https://storm.apache.org/releases/1.1.0/flux.html).

## <a name="configure-the-cluster"></a>Configurar o cluster

Por padrão, o Storm no HDInsight não inclui os componentes que o HdfsBolt usa para se comunicar com o Armazenamento do Azure ou o Data Lake Store no classpath do Storm. Use a ação de script a seguir para adicionar esses componentes ao diretório `extlib` para Storm no seu cluster:

* URI do script:`https://000aarperiscus.blob.core.windows.net/certs/stormextlib.sh`
* Nós para aplicar a: Nimbus, Supervisor
* Parâmetros: nenhum

Para obter informações sobre como usar esse script com o cluster, consulte o documento [Personalizar clusters do HDInsight usando ações de script](./hdinsight-hadoop-customize-cluster-linux.md).

## <a name="build-and-package-the-topology"></a>Compilar e criar o pacote da topologia

1. Baixe o projeto de exemplo em [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) em seu ambiente de desenvolvimento.

2. De um prompt de comando, terminal ou shell sessão, altere diretórios para a raiz do projeto baixado. Para criar e empacotar a topologia, use o seguinte comando:
   
        mvn compile package
   
    Depois de terminar de criar e empacotar, há um novo diretório chamado `target` contendo um arquivo denominado `StormToHdfs-1.0-SNAPSHOT.jar`. Esse arquivo contém a topologia compilada.

## <a name="deploy-and-run-the-topology"></a>Implantar e executar a topologia

1. Use o seguinte comando para copiar a topologia para o cluster HDInsight. Substitua**USER** pelo nome de usuário SSH usado ao criar o cluster. Substitua **CLUSTERNAME** pelo nome do cluster.
   
        scp target\StormToHdfs-1.0-SNAPSHOT.jar USER@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs1.0-SNAPSHOT.jar
   
    Quando solicitado, digite a senha usada ao criar o usuário SSH para o cluster. Se você usou uma chave pública em vez de uma senha, talvez precise usar o parâmetro `-i` para especificar o caminho para a chave privada correspondente.
   
   > [!NOTE]
   > Para saber mais sobre como usar o `scp` com HDInsight, confira [Usar SSH com HDInsight](./hdinsight-hadoop-linux-use-ssh-unix.md).

2. Quando o carregamento for concluído, use o seguinte para se conectar ao cluster HDInsight usando o SSH. Substitua**USER** pelo nome de usuário SSH usado ao criar o cluster. Substitua **CLUSTERNAME** pelo nome do cluster.
   
        ssh USER@CLUSTERNAME-ssh.azurehdinsight.net
   
    Quando solicitado, digite a senha usada ao criar o usuário SSH para o cluster. Se você usou uma chave pública em vez de uma senha, talvez precise usar o parâmetro `-i` para especificar o caminho para a chave privada correspondente.
   
   Para obter mais informações, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Depois de conectado, use o seguinte comando para criar um arquivo chamado `dev.properties`:

        nano dev.properties

4. Use o seguinte texto como o conteúdo do arquivo `dev.properties`:

        hdfs.write.dir: /stormdata/
        hdfs.url: wasb:///

    > [!IMPORTANT]
    > Esse exemplo presume que o cluster usa uma conta de Armazenamento do Azure como armazenamento padrão. Se o cluster usar o Azure Data Lake Store, use `hdfs.url: adl:///`.
    
    Para salvar o arquivo, use __Ctrl + X__, em seguida, __Y__ e, por fim, __Enter__. Os valores desse arquivo definem a URL do Data Lake Store e o nome do diretório no qual os dados são gravados.

3. Use o seguinte comando para iniciar a topologia:
   
        storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties

    Esse comando inicia a topologia usando a estrutura Flux, enviando-a para o nó Nimbus do cluster. A topologia é definida pelo arquivo `writetohdfs.yaml` incluído no jar. O arquivo `dev.properties` é passado como um filtro e os valores contidos no arquivo são lidos pela topologia.

## <a name="view-output-data"></a>Exibir dados de saída

Para exibir os dados, use o seguinte comando:

    hdfs dfs -ls /stormdata/

Uma lista dos arquivos criados por essa topologia é exibida.

A seguinte lista é um exemplo dos dados retornados pelos comandos anteriores:

    Found 30 items
    -rw-r-----+  1 sshuser sshuser       488000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-0-1488568403092.txt
    -rw-r-----+  1 sshuser sshuser       444000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-1-1488568404567.txt
    -rw-r-----+  1 sshuser sshuser       502000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-10-1488568408678.txt
    -rw-r-----+  1 sshuser sshuser       582000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-11-1488568411636.txt
    -rw-r-----+  1 sshuser sshuser       464000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-12-1488568411884.txt

## <a name="stop-the-topology"></a>Parar a topologia

As topologias do Storm são executadas até serem interrompidas ou o cluster ser excluído. Para interromper a topologia, use o seguinte comando:

    storm kill hdfswriter

## <a name="delete-your-cluster"></a>Excluir o cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a usar o Storm para gravar no Armazenamento do Azure e no Azure Data Lake Store, descubra outros [exemplos do Storm para HDInsight](hdinsight-storm-example-topology.md).

