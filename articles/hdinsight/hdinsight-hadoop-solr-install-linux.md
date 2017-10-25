---
title: "Usar a Ação de Script para instalar o Solr no HDInsight baseado em Linux – Azure | Microsoft Docs"
description: "Saiba como instalar o Solr em clusters baseados Hadoop HDInsight baseados em Linux usando as ações de script."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: cc93ed5c-a358-456a-91a4-f179185c0e98
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/28/2017
ms.author: larryfr
ms.openlocfilehash: c7a911474d6fb90f45565c90a72bfd407898ceba
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="install-and-use-solr-on-hdinsight-hadoop-clusters"></a>Instalar e usar o Solr em clusters HDInsight do Hadoop

Saiba como instalar o Solr no Azure HDInsight usando a Ação de Script. O Solr é uma plataforma de pesquisa poderosa e oferece recursos de pesquisa em nível corporativo para os dados gerenciados pelo Hadoop.

> [!IMPORTANT]
    > As etapas deste documento exigem um cluster HDInsight que usa Linux. O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

> [!IMPORTANT]
> O script de exemplo usado neste documento instala o Solr 4.9 com uma configuração específica. Se você quiser configurar o cluster Solr com diferentes coleções, fragmentos, esquemas, réplicas, etc., modifique o script e os binários do Sol.

## <a name="whatis"></a>O que é Solr

[Apache Solr](http://lucene.apache.org/solr/features.html) é uma plataforma de pesquisa empresarial que habilita operações poderosas de pesquisa de texto completo nos dados. Enquanto o Hadoop permite armazenar e gerenciar grandes quantidades de dados, o Apache Solr oferece os recursos de pesquisa para recuperar rapidamente os dados.

> [!WARNING]
> Componentes fornecidos com o cluster HDInsight contam com suporte total da Microsoft.
>
> Componentes personalizados, como o Solr, recebem suporte comercialmente razoável para ajudá-lo a solucionar o problema. O suporte da Microsoft pode não ser capaz de resolver problemas com componentes personalizados. Pode ser necessário envolver as comunidades de software livre para obter assistência. Por exemplo, há muitos sites de comunidades que podem ser usados, como o [Fórum do MSDN para o HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Além disso, os projetos do Apache têm sites do projeto em [http://apache.org](http://apache.org), por exemplo: [Hadoop](http://hadoop.apache.org/).

## <a name="what-the-script-does"></a>O que o script faz

Esse script faz as seguintes alterações ao cluster HDInsight:

* Instala o Solr 4.9 em `/usr/hdp/current/solr`
* Cria um usuário, **solrusr**, que é usado para executar o serviço do Solr
* Define **solruser** como o proprietário de `/usr/hdp/current/solr`
* Adiciona uma configuração [Upstart](http://upstart.ubuntu.com/) que inicia automaticamente o Solr.

## <a name="install"></a>Instalar o Solr usando ações de script

Um script de exemplo para instalar o Solr em um cluster HDInsight está disponível no seguinte local:

    https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh

Para criar um cluster com Solr instalado, use as etapas no documento [Criar clusters do HDInsight](hdinsight-hadoop-create-linux-clusters-portal.md). Durante o processo de criação, use as seguintes etapas para instalar o Solr:

1. Na seção __Resumo do cluster__, selecione__Configurações avançadas__ e __Ações de script__. Use as informações a seguir para popular o formulário:

   * **NOME**: insira um nome amigável para a ação de script.
   * **URI do SCRIPT**: https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh
   * **CABEÇALHO**: marque esta opção
   * **TRABALHO**: marque esta opção
   * **ZOOKEEPER**: marque esta opção para instalar no nó Zookeeper
   * **PARÂMETROS**: deixe este campo em branco

2. Na parte inferior da seção **Ações de Script**, use o botão **Selecionar** para salvar a configuração. Por fim, use o botão **Próximo** para retornar ao __Resumo do cluster__

3. Na página __Resumo do cluster__, selecione __Criar__ para criar o cluster.

## <a name="usesolr"></a>Como usar o Solr no HDInsight

> [!IMPORTANT]
> As etapas nesta seção demonstram a funcionalidade básica do Solr. Para obter mais informações sobre como usar o Solr, consulte o [site Apache Solr](http://lucene.apache.org/solr/).

### <a name="index-data"></a>Dados de índice

Use as etapas a seguir para adicionar dados de exemplo para Solr e, em seguida, consultá-los:

1. Conecte-se ao cluster HDInsight usando SSH:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Para obter mais informações, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

     > [!IMPORTANT]
     > Etapas posteriormente nesse documento usam um túnel SSL para conectar-se à interface do usuário da Web do Solr. Para usar essas etapas, você deve estabelecer um túnel SSL e configurar seu navegador para usá-lo.
     >
     > Para obter mais informações, consulte o documento [Usar túnel SSH com HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

2. Use os comandos a seguir para ter dados de exemplo do índice Solr:

    ```bash
    cd /usr/hdp/current/solr/example/exampledocs
    java -jar post.jar solr.xml monitor.xml
    ```

    A seguinte saída será retornada para o console:

        POSTing file solr.xml
        POSTing file monitor.xml
        2 files indexed.
        COMMITting Solr index changes to http://localhost:8983/solr/update..
        Time spent: 0:00:01.624

    O utilitário `post.jar` adiciona os documentos **solr.xml** e **monitor.xml** ao índice.
  
3. Use o comando a seguir para consultar a API REST do Solr:

    ```bash
    curl "http://localhost:8983/solr/collection1/select?q=*%3A*&wt=json&indent=true"
    ```

    Este comando busca **collection1** nos documentos que correspondem a **\*:\*** (codificado como \*%3A\* na cadeia de caracteres de consulta). No documento JSON a seguir há um exemplo de resposta:

            "response": {
                "numFound": 2,
                "start": 0,
                "maxScore": 1,
                "docs": [
                  {
                    "id": "SOLR1000",
                    "name": "Solr, the Enterprise Search Server",
                    "manu": "Apache Software Foundation",
                    "cat": [
                      "software",
                      "search"
                    ],
                    "features": [
                      "Advanced Full-Text Search Capabilities using Lucene",
                      "Optimized for High Volume Web Traffic",
                      "Standards Based Open Interfaces - XML and HTTP",
                      "Comprehensive HTML Administration Interfaces",
                      "Scalability - Efficient Replication to other Solr Search Servers",
                      "Flexible and Adaptable with XML configuration and Schema",
                      "Good unicode support: héllo (hello with an accent over the e)"
                    ],
                    "price": 0,
                    "price_c": "0,USD",
                    "popularity": 10,
                    "inStock": true,
                    "incubationdate_dt": "2006-01-17T00:00:00Z",
                    "_version_": 1486960636996878300
                  },
                  {
                    "id": "3007WFP",
                    "name": "Dell Widescreen UltraSharp 3007WFP",
                    "manu": "Dell, Inc.",
                    "manu_id_s": "dell",
                    "cat": [
                      "electronics and computer1"
                    ],
                    "features": [
                      "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                    ],
                    "includes": "USB cable",
                    "weight": 401.6,
                    "price": 2199,
                    "price_c": "2199,USD",
                    "popularity": 6,
                    "inStock": true,
                    "store": "43.17614,-90.57341",
                    "_version_": 1486960637584081000
                  }
                ]
              }

### <a name="using-the-solr-dashboard"></a>Usando o painel do Solr

O painel do Solr é uma IU Web que permite que você trabalhe com o Solr através do seu navegador da Web. O painel do Solr não é exposto diretamente à Internet no seu cluster HDInsight. É possível usar um túnel SSH para acessá-lo. Para obter mais informações sobre como usar um túnel SSH, consulte o documento [Usar túnel SSH com HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

Depois de estabelecer um túnel SSH, use as seguintes etapas para usar o painel do Solr:

1. Determine o nome de host para o nó de cabeçalho primário:

   1. Use SSH para conectar-se ao nó principal do cluster. Por exemplo: `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.

       Para saber mais sobre como usar SSH, consulte [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

   2. Use o seguinte comando para obter o nome de host totalmente qualificado:

        ```bash
        hostname -f
        ```

        Esse comando retorna um valor semelhante ao nome de host a seguir:

            hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

        Salve o valor retornado, pois ele será usado posteriormente.

2. Em seu navegador, conecte **http://HOSTNAME:8983/solr/#/**, onde **HOSTNAME** é o nome determinado nas etapas anteriores.

    A solicitação é roteada através do túnel SSH para a interface do usuário da Web do Solr no cluster. A página exibida é semelhante à imagem a seguir:

    ![Imagem do painel do Solr](./media/hdinsight-hadoop-solr-install-linux/solrdashboard.png)

3. No painel à esquerda, use a lista suspensa **Seletor de Núcleo** para selecionar **collection1**. Várias entradas deverão aparecer abaixo de **collection1**.

4. Nas entradas abaixo de **collection1**, selecione **Consultar**. Use os valores a seguir para preencher a página de pesquisa:

   * Na caixa de texto **q**, digite **\*:**\*. Essa consulta retorna todos os documentos que são indexados em Solr. Se você quiser procurar uma cadeia de caracteres específica dentro dos documentos, você pode inserir essa cadeia de caracteres aqui.
   * Na caixa de texto **wt** , selecione o formato de saída. O padrão é **json**.

     Por fim, selecione o botão **Executar Consulta** na parte inferior da página de pesquisa.

     ![Usar Ação de Script para personalizar um cluster](./media/hdinsight-hadoop-solr-install-linux/hdi-solr-dashboard-query.png)

     A saída retorna os dois documentos que você adicionou anteriormente ao índice. A saída é semelhante ao documento JSON a seguir:

           "response": {
               "numFound": 2,
               "start": 0,
               "maxScore": 1,
               "docs": [
                 {
                   "id": "SOLR1000",
                   "name": "Solr, the Enterprise Search Server",
                   "manu": "Apache Software Foundation",
                   "cat": [
                     "software",
                     "search"
                   ],
                   "features": [
                     "Advanced Full-Text Search Capabilities using Lucene",
                     "Optimized for High Volume Web Traffic",
                     "Standards Based Open Interfaces - XML and HTTP",
                     "Comprehensive HTML Administration Interfaces",
                     "Scalability - Efficient Replication to other Solr Search Servers",
                     "Flexible and Adaptable with XML configuration and Schema",
                     "Good unicode support: héllo (hello with an accent over the e)"
                   ],
                   "price": 0,
                   "price_c": "0,USD",
                   "popularity": 10,
                   "inStock": true,
                   "incubationdate_dt": "2006-01-17T00:00:00Z",
                   "_version_": 1486960636996878300
                 },
                 {
                   "id": "3007WFP",
                   "name": "Dell Widescreen UltraSharp 3007WFP",
                   "manu": "Dell, Inc.",
                   "manu_id_s": "dell",
                   "cat": [
                     "electronics and computer1"
                   ],
                   "features": [
                     "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                   ],
                   "includes": "USB cable",
                   "weight": 401.6,
                   "price": 2199,
                   "price_c": "2199,USD",
                   "popularity": 6,
                   "inStock": true,
                   "store": "43.17614,-90.57341",
                   "_version_": 1486960637584081000
                 }
               ]
             }

### <a name="starting-and-stopping-solr"></a>Iniciando e parando o Solr

Use o seguintes comandos para interromper ou iniciar o Solr manualmente:

```bash
sudo stop solr
sudo start solr
```

## <a name="backup-indexed-data"></a>Backup de dados indexados

Use as etapas a seguir para fazer backup de dados do Solr no armazenamento padrão do cluster:

1. Conecte-se ao cluster usando o SSH, depois use o comando a seguir para obter o nome de host do nó de cabeçalho:

    ```bash
    hostname -f
    ```

2. Use o comando a seguir para criar um instantâneo dos dados indexados. Substitua **HOSTNAME** pelo nome retornado do comando anterior:

    ```bash
    curl http://HOSTNAME:8983/solr/replication?command=backup
    ```

    A resposta é semelhante ao XML a seguir:

        <?xml version="1.0" encoding="UTF-8"?>
        <response>
          <lst name="responseHeader">
            <int name="status">0</int>
            <int name="QTime">9</int>
          </lst>
          <str name="status">OK</str>
        </response>

3. Altere os diretórios para `/usr/hdp/current/solr/example/solr`. Há um subdiretório aqui para cada coleção. Cada diretório da coleção contém um diretório `data`, que contém o instantâneo dessa coleção.

4. Para criar um arquivo morto compactado da pasta de instantâneos, use o seguinte comando:

    ```bash
    tar -zcf snapshot.20150806185338855.tgz snapshot.20150806185338855
    ```

    Substitua os valores `snapshot.20150806185338855` pelo nome do instantâneo da coleção.

    Esse comando cria um arquivo morto denominado **snapshot.20150806185338855.tgz**, que tem o conteúdo do diretório **snapshot.20150806185338855**.

5. Em seguida, você pode armazenar o arquivo para armazenamento primário do cluster usando o seguinte comando:

    ```bash
    hdfs dfs -put snapshot.20150806185338855.tgz /example/data
    ```

Para obter mais informações sobre como trabalhar com backups e restaurações do Solr, consulte [https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups](https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups).

## <a name="next-steps"></a>Próximas etapas

* [Instalar o Giraph em clusters HDInsight](hdinsight-hadoop-giraph-install-linux.md). Use a personalização do cluster para instalar o Giraph em clusters de Hadoop do HDInsight. O Giraph permite que você realize processamento de tabelas usando o Hadoop, além de poder ser usado com o HDInsight do Azure.

* [Instalar matiz em clusters HDInsight](hdinsight-hadoop-hue-linux.md). Use a personalização do cluster para instalar o Hue em clusters de Hadoop do HDInsight. A Matiz é um conjunto de aplicativos da Web usado para interagir com um cluster Hadoop.

[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
