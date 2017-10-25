---
title: Instalar o Jupyter localmente e conectar-se a um cluster do Spark do Azure HDInsight | Microsoft Docs
description: "Saiba mais sobre como instalar o bloco de anotações do Jupyter localmente em seu computador e se conectar a um cluster Apache Spark no Azure HDInsight."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 48593bdf-4122-4f2e-a8ec-fdc009e47c16
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: ed57a261d81352ec06fedc3ee6c6541a95370d17
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Instalar o bloco de anotações do Jupyter em seu computador e conectar-se ao Apache Spark no HDInsight

Neste artigo, você aprende como instalar blocos de notas Jupyter, com o PySpark personalizado (para o Python) e kernels Spark (para Scala) com a mágica de Spark, e conectar o bloco de notas a um cluster HDInsight. Pode haver inúmeros motivos para instalar o Jupyter no computador local e alguns desafios também. Para saber mais sobre isso, confira a seção [Por que devo instalar o Jupyter no meu computador](#why-should-i-install-jupyter-on-my-computer) no final deste artigo.

Há três etapas principais envolvidas na instalação do Jupyter e da mágica do Spark em seu computador.

* Instalar bloco de notas Jupyter
* Instalar os kernels PySpark e Spark com a mágica do Spark
* Configure a mágica do Spark para acessar o cluster Spark no HDInsight

Para saber mais sobre os kernels personalizados e a sobre a mágica Spark disponível para blocos de notas Jupyter com o cluster HDInsight, confira [Kernels disponíveis para blocos de notas Jupyter com clusters do Apache Spark Linux no HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Pré-requisitos
Os pré-requisitos listados aqui não são para a instalação do Jupyter. Eles são para conectar o bloco de notas Jupyter a um cluster HDInsight depois que o bloco de notas está instalado.

* Uma assinatura do Azure. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="install-jupyter-notebook-on-your-computer"></a>Instalar bloco de notas Jupyter em seu computador

Você deve instalar o Python antes de instalar notebooks do Jupyter. Ambos o Python e o Jupyter estão disponíveis como parte da [distribuição do Anaconda](https://www.continuum.io/downloads). Quando instala o Anaconda, você instala uma distribuição do Python. Quando o Anaconda é instalado, você adiciona a instalação do Jupyter executando comandos apropriados.

1. Baixe o [instalador do Anaconda](https://www.continuum.io/downloads) para sua plataforma e execute a instalação. Ao executar o assistente de instalação, não deixe de selecionar a opção de adicionar o Anaconda à variável PATH.
2. Execute o comando a seguir para instalar o Jupyter.

        conda install jupyter

    Para obter mais informações sobre a instalação do Jupyter, confira [Installing Jupyter using Anaconda](http://jupyter.readthedocs.io/en/latest/install.html)(Instalando o Jupyter usando o Anaconda).

## <a name="install-the-kernels-and-spark-magic"></a>Instalar os kernels e a mágica do Spark

Para obter instruções sobre como instalar a mágica do Spark, os kernels PySpark e Spark, siga as instruções da instalação na [documentação do sparkmagic](https://github.com/jupyter-incubator/sparkmagic#installation) no GitHub. A primeira etapa na documentação da mágica do Spark solicita a instalação da mágica do Spark. Substitua essa primeira etapa no link com os seguintes comandos, dependendo da versão do cluster do HDInsight de conexão. Depois disso, siga as etapas restantes na documentação da mágica do Spark. Se quiser instalar os kernels diferentes, você deverá executar a Etapa 3 na seção de instruções da instalação da mágica do Spark.

* Para clusters v3.4, instale sparkmagic 0.2.3 executando `pip install sparkmagic==0.2.3`

* Para clusters v3.5 e v3.6, instale sparkmagic 0.11.2 executando `pip install sparkmagic==0.11.2`

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>Configurar a mágica do Spark para se conectar ao cluster do HDInsight Spark

Nesta seção, você configura a mágica do Spark instalada anteriormente para se conectar a um cluster Apache Spark que já deve ter criado no Azure HDInsight.

1. As informações de configuração do Jupyter normalmente são armazenadas no diretório base dos usuários. Para localizar seu diretório base em qualquer plataforma de sistema operacional, digite os comandos a seguir.

    Inicie o shell do Python. Em uma janela de comando, digite o seguinte:

        python

    No shell do Python, digite o comando a seguir para localizar o diretório base.

        import os
        print(os.path.expanduser('~'))

2. Navegue até o diretório base e crie uma pasta chamada **.sparkmagic** , caso ela ainda não exista.
3. Dentro da pasta, crie um arquivo chamado **config.json** e adicione o trecho de código JSON a seguir dentro dele.

        {
          "kernel_python_credentials" : {
            "username": "{USERNAME}",
            "base64_password": "{BASE64ENCODEDPASSWORD}",
            "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
          },
          "kernel_scala_credentials" : {
            "username": "{USERNAME}",
            "base64_password": "{BASE64ENCODEDPASSWORD}",
            "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
          }
        }

4. Substitua **{USERNAME}**, **{CLUSTERDNSNAME}** e **{BASE64ENCODEDPASSWORD}** pelos valores apropriados. Você pode usar vários utilitários em sua linguagem de programação favorita ou online para gerar uma senha codificada em base64 para sua senha real.

5. Defina as configurações corretas de Pulsação em `config.json`. Você deve adicionar essas configurações no mesmo nível que os trechos `kernel_python_credentials` e `kernel_scala_credentials` adicionados anteriormente. Para obter um exemplo de como e onde adicionar as configurações de pulsação, consulte este [exemplo de config.json](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json).

    * Para `sparkmagic 0.2.3` (clusters v3.4), incluem:

            "should_heartbeat": true,
            "heartbeat_refresh_seconds": 5,
            "heartbeat_retry_seconds": 1

    * Para `sparkmagic 0.11.2` (clusters v3.5 e v3.6), inclua:

            "heartbeat_refresh_seconds": 5,
            "livy_server_heartbeat_timeout_seconds": 60,
            "heartbeat_retry_seconds": 1

    >[!TIP]
    >As pulsações são enviadas para garantir que as sessões não sejam perdidas. Quando um computador entra em suspensão ou está desligado, a pulsação não é enviada e, como resultado, a sessão é limpa. Para clusters v3.4, se desejar desabilitar esse comportamento, você poderá definir a configuração Livy `livy.server.interactive.heartbeat.timeout` para `0` da interface do usuário do Ambari. Para clusters v3.5, se você não definir a configuração de 3.5 ou acima, a sessão não será excluída.

6. Inicie o Jupyter. Use o comando do prompt de comando a seguir.

        jupyter notebook

7. Verifique se você pode se conectar ao cluster usando o bloco de notas Jupyter e se você pode usar a mágica Spark disponível com os kernels. Execute as seguintes etapas:

    a. Crie um novo bloco de anotações. Do canto direito, clique em **Novo**. Você deve ver o kernel padrão **Python2** e os dois kernels novos instalados, **PySpark** e **Spark**. Clique em **PySpark**.

    ![Kernels no bloco de anotações do Jupyter](./media/hdinsight-apache-spark-jupyter-notebook-install-locally/jupyter-kernels.png "Kernels no bloco de anotações do Jupyter")

    b. Execute o trecho de código a seguir.

        %%sql
        SELECT * FROM hivesampletable LIMIT 5

    Se você puder recuperar a saída com êxito, a conexão com o cluster HDInsight será testada.

    >[!TIP]
    >Se você quiser atualizar a configuração do bloco de notas para se conectar a um cluster diferente, atualize o config.json com o novo conjunto de valores, conforme mostrado na Etapa 3 acima.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Por que devo instalar o Jupyter no meu computador?
Pode haver vários motivos pelos quais você possa querer instalar o Jupyter no computador e conectá-lo a um cluster Spark no HDInsight.

* Mesmo que os blocos de notas Jupyter já estejam disponíveis no cluster Spark no Azure HDInsight, a instalação do Jupyter no seu computador fornece a opção de criar os blocos de notas localmente, de testar o aplicativo em um cluster em execução e de carregar os blocos de notas no cluster. Para carregar os blocos de notas no cluster, você pode carregá-los usando o bloco de notas Jupyter que está em execução ou o cluster, ou salvá-los na pasta /HdiNotebooks na conta de armazenamento associada ao cluster. Para saber mais sobre como os blocos de notas são armazenados no cluster, confira [Onde os blocos de notas Jupyter são armazenados](hdinsight-apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)?
* Com os blocos de notas disponíveis localmente, você pode se conectar a clusters Spark diferentes com base nos requisitos do aplicativo.
* É possível usar o GitHub para implementar um sistema de controle de origem e ter o controle de versão para os blocos de notas. Você também pode ter um ambiente colaborativo, onde vários usuários podem trabalhar com o mesmo bloco de notas.
* Você pode trabalhar com blocos de notas localmente sem sequer ter um cluster em operação. É preciso apenas um cluster no qual testar seus blocos de notas, e não para gerenciar manualmente os blocos de notas ou um ambiente de desenvolvimento.
* Pode ser mais fácil configurar seu próprio ambiente de desenvolvimento local que configurar a instalação do Jupyter no cluster.  É possível aproveitar todo o software que você tem instalado localmente sem configurar um ou mais clusters remotos.

> [!WARNING]
> Com o Jupyter instalado no computador local, vários usuários podem executar o mesmo bloco de notas no mesmo cluster Spark ao mesmo tempo. Nessa situação, várias sessões Livy são criadas. Se você tiver um problema e desejar depurá-lo, será uma tarefa complexa rastrear qual sessão Livy pertence a qual usuário.
>
>

## <a name="seealso"></a>Consulte também
* [Visão geral: Apache Spark no Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Spark com Aprendizado de Máquina: usar o Spark no HDInsight para analisar a temperatura de prédios usando dados do sistema HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Aprendizado de Máquina: usar o Spark no HDInsight para prever resultados da inspeção de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark: usar o Spark no HDInsight para a criação de aplicativos de streaming em tempo real](hdinsight-apache-spark-eventhub-streaming.md)
* [Análise de log do site usando o Spark no HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicativos
* [Criar um aplicativo autônomo usando Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster do Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Use o Plug-in de Ferramentas do HDInsight para IntelliJ IDEA para criar e enviar aplicativos Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Usar o plug-in de Ferramentas do HDInsight para depurar aplicativos Spark remotamente](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usar blocos de anotações do Zeppelin com um cluster Spark no HDInsight](hdinsight-apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de anotações Jupyter no cluster do Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com blocos de notas Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

### <a name="manage-resources"></a>Gerenciar recursos
* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](hdinsight-apache-spark-job-debugging.md)
