---
title: "Ferramentas do Azure HDInsight – use o Visual Studio Code para o Hive, o LLAP ou pySpark | Microsoft Docs"
description: Saiba como usar as Ferramentas do Azure HDInsight para Visual Studio Code para criar e enviar consultas e scripts.
Keywords: VSCode, Ferramentas do Azure HDInsight, Hive, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, Hive Interativo, Consulta Interativa
services: HDInsight
documentationcenter: 
author: jejiang
manager: 
editor: jgao
tags: azure-portal
ms.assetid: 
ms.service: HDInsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/26/2017
ms.author: jejiang
ms.openlocfilehash: fdf1911f4f5b515592b6197ea738d808104cb43e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-hdinsight-tool-for-visual-studio-code"></a>Usar a Ferramenta do Azure HDInsight para Visual Studio Code

Saiba como usar as Ferramentas do Azure HDInsight para VSCode (Visual Studio Code) para criar e enviar trabalhos em lotes do Hive, consultas interativas do Hive e scripts pySpark. As Ferramentas do Azure HDInsight podem ser instaladas nas plataformas compatíveis com o VSCode, que incluem Windows, Linux e MacOS. Você pode encontrar os pré-requisitos para diferentes plataformas.


## <a name="prerequisites"></a>Pré-requisitos

Os itens a seguir são necessários para a conclusão deste artigo:

- Um cluster HDInsight.  Para criar um cluster, consulte [Introdução ao HDInsight]( hdinsight-hadoop-linux-tutorial-get-started.md).
- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx).
- [Mono](http://www.mono-project.com/docs/getting-started/install/). O Mono só é necessário para Linux e MacOS.

## <a name="install-the-hdinsight-tools"></a>Instalar as Ferramentas do HDInsight
   
Depois de instalar os pré-requisitos, você pode instalar as Ferramentas do Azure HDInsight para VSCode. 

**Para instalar as Ferramentas do Azure HDInsight**

1. Abra o **Visual Studio Code**.
2. Clique em **Extensões** no painel esquerdo. Digite **HDInsight** na caixa de pesquisa.
3. Clique em **Instalar** ao lado de **Ferramentas do Azure HDInsight**. Depois de alguns segundos, o botão **Instalar** será alterado para **Recarregar**.
4. Clique em **Recarregar** para ativar a extensão **Ferramentas do Azure HDInsight**.
5. Clique na **Janela Recarregar** para confirmar. Você pode ver as **Ferramentas do Azure HDInsight** no painel Extensões.

   ![Instalação do Python no HDInsight para Visual Studio Code](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

Para usuários do Spark, a extensão do Python é recomendada para melhores experiências de serviço de linguagem. A extensão do Python não é necessária para os usuários do Hive. 

**Para instalar o Python**

1. Abra o **Visual Studio Code**.
2. Clique em **Extensões** no painel esquerdo. Na caixa de pesquisa, digite **python**. Você pode ver uma lista de extensões de python. Uma delas é **Python**.
3. Clique em **Instalar** ao lado de **Python**. Depois de alguns segundos, o botão **Instalar** será alterado para **Recarregar**.
4. Clique em **Recarregar** para ativar a extensão **Python**.
5. Clique na **Janela Recarregar** para confirmar. Você pode ver **Python** no painel Extensões.

     ![Instalação do Python no HDInsight para Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

## <a name="open-hdinsight-workspace"></a>Abrir o espaço de trabalho do HDInsight

Você deve criar um espaço de trabalho no VSCode antes que possa se conectar ao Azure.

**Para abrir um espaço de trabalho**

1. Do menu **Arquivo**, clique em **Abrir pasta** e especifique uma pasta existente ou crie uma nova pasta como a pasta de trabalho. A pasta aparece no painel esquerdo.
2. No painel esquerdo, clique no ícone **Novo Arquivo** ao lado da pasta de trabalho.

   ![novo arquivo](./media/hdinsight-for-vscode/new-file.png)
3. Nomeie o novo arquivo com a extensão de arquivo .hql (consultas de Hive) ou .py (script Spark). Observe que um arquivo de configuração **XXXX_hdi_settings.json** é adicionado automaticamente à pasta de trabalho.
4. Abra **XXXX_hdi_settings.json** de **EXPLORER** ou com o clique com o botão direito do mouse no editor de scripts para selecionar **Definir Configuração**. Você pode configurar a entrada de logon, o cluster padrão e parâmetros de envio de trabalho, conforme visto na amostra no arquivo. Você também pode deixar os parâmetros restantes vazios.

## <a name="connect-to-azure"></a>Conecte-se ao Azure

Antes que você possa enviar scripts para clusters HDInsight do VSCode, você precisará se conectar à sua conta do Azure.

**Para se conectar ao Azure**

1. Crie uma nova pasta de trabalho e um novo arquivo de script se você não tiver um.
2. Clique com o botão direito do mouse no editor de scripts e, em seguida, selecione **HDInsight: Logon** no menu de contexto. Você também pode pressionar **CTRL + SHIFT + P** e inserir **HDInsight: Logon**.

    ![Logon das Ferramentas do HDInsight para Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)
3. Siga as instruções de logon no painel **SAÍDA** para fazer logon.

    **Azure:** ![informações de logon das Ferramentas do HDInsight para Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-Azurelogin-info.png)

    Uma vez conectado, o nome da sua conta do Azure será mostrado na barra de status na parte inferior esquerda da janela do VSCode. 

    > [!NOTE] 
    > Se sua conta do Azure tiver dois fatores habilitados, é recomendável usar a autenticação por telefone em vez do Pin.
    > Há um problema conhecido sobre um logon do Azure usando o Google Chrome.

4. Clique com o botão direito do mouse no editor de scripts para abrir o menu de contexto:

    ![Menu de contexto do editor de scripts das Ferramentas do HDInsight para Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-context-menu.png)

    Do menu de contexto, você pode executar as seguintes tarefas:

    - logout
    - Listar os clusters
    - Definir cluster padrão
    - Enviar consultas interativas do Hive
    - Enviar o script em lotes do Hive
    - Definir a configuração

## <a name="list-hdinsight-clusters"></a>Listar clusters HDInsight

Para testar a conexão, você pode listar seus clusters HDInsight:

**Para listar os clusters HDInsight na assinatura do Azure**
1. Abra um espaço de trabalho e conecte-se ao Azure. Veja [Abrir espaço de trabalho do HDInsight](#open-hdinsight-workspace) e [Conectar-se ao Azure](#connect-to-azure).
2. Clique com o botão direito do mouse no editor de scripts e, em seguida, selecione **HDInsight: Listar Clusters** no menu de contexto. 
3. Os clusters Hive e Spark aparecem no painel **Saída**.

    ![definir configuração do cluster padrão](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-default-cluster"></a>Definir cluster padrão
1. Abra um espaço de trabalho e conecte-se ao Azure. Veja [Abrir espaço de trabalho do HDInsight](#open-hdinsight-workspace) e [Conectar-se ao Azure](#connect-to-azure).
2. Clique com o botão direito do mouse no editor de scripts e, em seguida, clique em **HDInsight: Definir Cluster Padrão**. 
3. Selecione um cluster como o padrão para o arquivo de script atual. As Ferramentas atualizam automaticamente o arquivo de configuração, **XXXX_hdi_settings.json**. 

   ![definir configuração do cluster padrão](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-azure-environment"></a>Definir ambiente do Azure 
1. Abra a paleta de comandos pressionando **CTRL+SHIFT+P**.
2. Entre em **HDInsight: Definir o Ambiente do Azure**.
3. Selecione um modo entre Azure e AzureChina como sua entrada de logon padrão.
4. Enquanto isso, nossa ferramenta já salvou a entrada de logon padrão que você selecionou em **XXXX_hdi_settings.json**. Você também a atualiza diretamente nesse arquivo de configuração. 

   ![definir configuração de entrada de logon padrão](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="submit-interactive-hive-queries"></a>Enviar consultas interativas do Hive

As Ferramentas do HDInsight para VSCode permitem enviar consultas interativas do Hive a clusters de Consulta Interativa do HDInsight.

1. Crie uma nova pasta de trabalho e um novo arquivo de script do Hive se você não tiver um.
2. Conecte-se à sua conta do Azure e, em seguida, configure o cluster padrão se você ainda não tiver feito isso.
3. Copie e cole o código a seguir no arquivo do Hive, depois salve-o.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. Clique com o botão direito do mouse no editor de scripts e, em seguida, clique em **HDInsight: Hive Interativo** para enviar a consulta. As ferramentas também permitem que você, usando o menu de contexto, envie um bloco de código em vez do arquivo de script inteiro. Logo depois, o resultado da consulta é mostrado em uma nova guia:

   ![resultado do Hive interativo](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Painel **RESULTADOS**: você pode salvar o resultado inteiro como CSV, JSON ou EXCEL para o caminho local ou então apenas selecionar várias linhas.
    - Painel **MENSAGENS**: clicando no número de **Linha**, salta para a primeira linha do script em execução.

Em comparação com a [execução de um trabalho em lotes do Hive](#submit-hive-batch-scripts), a consulta interativa leva muito menos tempo.

## <a name="submit-hive-batch-scripts"></a>Enviar scripts em lotes do Hive

1. Crie uma nova pasta de trabalho e um novo arquivo de script do Hive se você não tiver um.
2. Conecte-se à sua conta do Azure e, em seguida, configure o cluster padrão se você ainda não tiver feito isso.
3. Copie e cole o código a seguir no arquivo do Hive, depois salve-o.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. Clique com o botão direito do mouse no editor de scripts e, em seguida, clique em **HDInsight: Enviar Script em Lotes do Hive** para enviar um trabalho do Hive. 
4. Selecione um cluster para o qual você deseja enviar.  

    Depois de enviar um trabalho de Hive, as informações de sucesso de envio e jobid são mostradas no painel **SAÍDA**. Além disso, ele abre o **NAVEGADOR DA WEB**, em que os logs de trabalho em tempo real e o status são mostrados.

   ![enviar resultado de trabalho do Hive](./media/hdinsight-for-vscode/submit-Hivejob-result.png)

Comparando com o [envio de consultas interativas do Hive](#submit-interactive-hive-queries), o trabalho em lotes leva muito mais tempo.


## <a name="submit-pyspark-job"></a>Enviar trabalho do PySpark

1. Crie uma nova pasta de trabalho e um novo arquivo de script com a extensão .py, se você não tiver um.
2. Conecte-se à sua conta do Azure e, em seguida, configure o cluster padrão se você ainda não tiver feito isso.
3. Copie e cole o seguinte código no arquivo de script:

    ```python
    from __future__ import print_function
    import sys
    from operator import add
    from pyspark.sql import SparkSession
    if __name__ == "__main__":
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
    
        lines = spark.read.text('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv').rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' '))\
                    .map(lambda x: (x, 1))\
                    .reduceByKey(add)
        output = counts.collect()
        for (word, count) in output:
            print("%s: %i" % (word, count))
        spark.stop()
    ```
4. Clique com o botão direito do mouse no editor de scripts e, em seguida, clique em **HDInsight: Enviar Trabalho do PySpark**. 
5. Selecione um cluster para enviar o trabalho do PySpark. 

   ![enviar o resultado do trabalho do Python](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

Depois de enviar um trabalho do Python, os logs de envio são mostrados na janela **SAÍDA** no VSCode. A **URL de interface do usuário do Spark** e a **URL de interface do usuário do Yarn** também são mostradas. Você pode abrir a URL em um navegador da Web para acompanhar o status do trabalho.


## <a name="additional-features"></a>Recursos adicionais

O HDInsight para VSCode dá suporte aos seguintes recursos:

- **Preenchimento automático do IntelliSense**. As sugestões são exibidas ao redor de palavras-chave, métodos, variáveis, etc. Os diferentes ícones representam diferentes tipos dos objetos:

    ![Tipos de objeto do IntelliSense das Ferramentas do HDInsight para Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **Marcador de erro do IntelliSense**. O serviço de linguagem sublinha os erros de edição do script do Hive.     
- **Destaques da sintaxe**. O serviço de linguagem usa cores diferentes para diferenciar variáveis, palavras-chave, tipo de dados, funções, etc. 

    ![Destaques da sintaxe das Ferramentas do HDInsight para Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>Próximas etapas

### <a name="demo"></a>Demonstração
* HDInsight para VScode: [Vídeo](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Usar o kit de ferramentas do Azure para IntelliJ a fim de depurar aplicativos Spark remotamente por meio da VPN](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usar o kit de ferramentas do Azure para IntelliJ a fim de depurar aplicativos Spark remotamente por meio do SSH](hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Usar ferramentas do HDInsight para IntelliJ com a área restrita do Hortonworks](hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Usar as Ferramentas do HDInsight no Kit de Ferramentas do Azure para Eclipse para criar aplicativos Spark](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Usar blocos de anotações do Zeppelin com um cluster Spark no HDInsight](hdinsight-apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de anotações Jupyter no cluster do Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com blocos de notas Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](hdinsight-apache-spark-jupyter-notebook-install-locally.md)
* [Visualizar dados de Hive com o Microsoft Power BI no Azure HDInsight](./hdinsight-connect-hive-power-bi.md).
* [Usar Zeppelin para executar consultas do Hive no Azure HDInsight ](./hdinsight-connect-hive-zeppelin.md).

### <a name="scenarios"></a>Cenários
* [Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Spark com Aprendizado de Máquina: usar o Spark no HDInsight para analisar a temperatura de prédios usando dados do sistema HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Machine Learning: usar o Spark no HDInsight para prever resultados da inspeção de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark: usar o Spark no HDInsight para a criação de aplicativos de streaming em tempo real](hdinsight-apache-spark-eventhub-streaming.md)
* [Análise de log do site usando o Spark no HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Criando e executando aplicativos
* [Criar um aplicativo autônomo usando Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster do Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="managing-resources"></a>Gerenciando recursos
* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](hdinsight-apache-spark-job-debugging.md)



