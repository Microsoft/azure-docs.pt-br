---
title: Ferramentas do Azure HDInsight – Use o Visual Studio Code para o Hive, o LLAP ou PySpark | Microsoft Docs
description: Saiba como usar as Ferramentas do Azure HDInsight para Visual Studio Code para criar e enviar consultas e scripts.
Keywords: Código do Visual Studio, ferramentas do Azure HDInsight, Hive, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, Hive interativo, consulta interativa
documentationcenter: ''
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/04/2019
ms.openlocfilehash: 04e607517bc806b60d7e76e5076f9d3518e530eb
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098330"
---
# <a name="use-azure-hdinsight-tools-for-visual-studio-code"></a>Usar a Ferramenta do Azure HDInsight para Visual Studio Code

Saiba como usar as Ferramentas do Azure HDInsight para Visual Studio Code para criar e enviar trabalho em lotes do Apache Hive, consultas interativas do Apache Hive e scripts PySpark para o Apache Spark. Em primeiro lugar, descreveremos como instalar as ferramentas do HDInsight no Visual Studio Code e, em seguida, examinaremos como enviar trabalhos para o Hive e o Spark.  

As Ferramentas do Azure HDInsight podem ser instaladas nas plataformas compatíveis com o Visual Studio Code, que incluem Windows, Linux e macOS. Abaixo você encontrará os pré-requisitos para as diferentes plataformas.


## <a name="prerequisites"></a>Pré-requisitos

Os itens a seguir são necessários para concluir as etapas neste artigo:

- Um cluster HDInsight. Para criar um cluster, consulte [Introdução ao HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md).
- [Visual Studio Code](https://code.visualstudio.com/).
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono só é necessário para Linux e MacOS.
- A [Extensão da Conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) para o Visual Studio Code.
- [Configurar o ambiente interativo do PySpark para Visual Studio Code](set-up-pyspark-interactive-environment.md).
- Um diretório local chamado **HDexample**.  Este artigo usa **C:\HD\HDexample**.

## <a name="install-azure-hdinsight-tools"></a>Instalar as Ferramentas do Azure HDInsight

Depois de concluir os pré-requisitos, você pode instalar as Ferramentas do Azure HDInsight para Visual Studio Code.  Conclua as etapas a seguir para instalar as Ferramentas do Azure HDInsight:

1. Abra o Visual Studio Code.

2. Na barra de menus, navegue para **Exibir** > **Extensões**.

3. Digite **HDInsight** na caixa de pesquisa.

4. Selecione **Ferramentas do Azure HDInsight** nos resultados da pesquisa e selecione **Instalar**.  

   ![Instalação do Python no HDInsight para Visual Studio Code](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. Selecione **Recarregar** para ativar a extensão **Ferramentas do Azure HDInsight** depois da instalação.


## <a name="open-hdinsight-work-folder"></a>Abrir a pasta de trabalho do HDInsight

Conclua as etapas a seguir para abrir uma pasta de trabalho e criar um arquivo no Visual Studio Code:

1. Na barra de menus, navegue para **Arquivo** > **Abrir Pasta...** > **C:\HD\HDexample** e, em seguida, selecione o botão **Selecionar Pasta**. A pasta aparece na exibição do **Explorador** à esquerda.

2. Na exibição do **Explorador**, selecione a pasta, **HDexample** e, em seguida, o ícone **Novo Arquivo** ao lado da pasta de trabalho.

   ![Novo arquivo](./media/hdinsight-for-vscode/new-file.png)

3. Nomeie o novo arquivo com a extensão de arquivo .hql (consultas de Hive) ou .py (script Spark).  Este exemplo usa **HelloWorld.hql**.

## <a name="connect-to-hdinsight-cluster"></a>Conectar ao Cluster HDInsight

Antes de enviar scripts para clusters do HDInsight a partir do Visual Studio Code, você precisa se conectar à sua conta do Azure ou vincular um cluster (usando nome de usuário/senha ou a conta de domínio associado do Ambari).  Conclua as seguintes etapas para se conectar ao Azure:

1. Na barra de menus, navegue para **Exibir** > **Paleta de Comandos...**, e insira **HDInsight: Logon**.

    ![Logon das Ferramentas do HDInsight para Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Siga as instruções de conexão no painel **SAÍDA**.
    + Para o ambiente global do Azure, o comando **HDInsight: Login** disparará a ação **Entrar no Azure** no Gerenciador do HDInsight e vice-versa.

        ![Instruções de entrada no Azure](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-signin.png)

    + Para outros ambientes, siga as instruções de entrada.

        ![Instruções de entrada para outro ambiente](./media/hdinsight-for-vscode/hdi-azure-hdinsight-hdinsight-signin.png)

   Depois que você estiver conectado, o nome da conta do Azure será mostrado na barra de status no canto inferior esquerdo da janela do Visual Studio Code.  
  

<h2 id="linkcluster">Criar link: Azure HDInsight</h2>

É possível vincular um cluster normal usando um nome de usuário gerenciado do [Apache Ambari](https://ambari.apache.org/) ou vincular um cluster Hadoop seguro do pacote Enterprise Security usando um nome de usuário de domínio (como: user1@contoso.com).

1. Na barra de menus, navegue para **Exibir** > **Paleta de Comandos...**, e insira **HDInsight: Vincular um Cluster**.

   ![comando para vincular cluster](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Selecione o tipo de cluster vinculado **Azure HDInsight**.

3. Insira a URL do cluster do HDInsight.

4. Insira o nome de usuário do Ambari, o padrão é **admin**.

5. Insira a senha do Ambari.

6. Selecione o tipo de cluster.

7. Verifique a exibição **SAÍDA**.

   > [!NOTE]  
   > O nome de usuário e a senha vinculados serão usados se o cluster foi registrado na assinatura do Azure e vinculou um cluster.  


## <a name="create-link-generic-livy-endpoint"></a>Criar link: Ponto de Extremidade Genérico do Livy

1. Na barra de menus, navegue para **Exibir** > **Paleta de Comandos...**, e insira **HDInsight: Vincular um Cluster**.

2. Selecione o tipo de cluster vinculado **Ponto de Extremidade Genérico do Livy**.

3. Digite o ponto de extremidade genérico do Livy, por exemplo: http\://10.172.41.42:18080.

4. Selecione o tipo de autorização, **Básico** ou **Nenhum**.  Se **Básico**, então:  
    &emsp;a. Insira o nome de usuário do Ambari, o padrão é **admin**.  
    &emsp;b. Insira a senha do Ambari.

5. Verifique a exibição **SAÍDA**.

## <a name="list-hdinsight-clusters"></a>Listar clusters HDInsight

1. Na barra de menus, navegue para **Exibir** > **Paleta de Comandos...**, e insira **HDInsight: Listar clusters**.

2. Selecione a assinatura desejada.

3. Verifique a exibição **SAÍDA**.  A exibição mostrará os clusters vinculados e todos os clusters sob sua assinatura do Azure.

    ![Definir uma configuração do cluster padrão](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-default-cluster"></a>Definir cluster padrão

1. Abra de novo a pasta **HDexample** criada [anteriormente](#open-hdinsight-work-folder), se estiver fechada.  

2. Selecione o arquivo **HelloWorld.hql** criado [anteriormente](#open-hdinsight-work-folder) e ele abrirá no editor de scripts.

3. [Conecte-se](#connect-to-hdinsight-cluster) à sua conta do Azure se ainda não tiver feito isso.

4. Clique com o botão direito do mouse no editor de scripts e selecione **HDInsight: Definir cluster padrão**.  

5. Selecione um cluster como o padrão para o arquivo de script atual. As ferramentas atualizam automaticamente o arquivo de configuração **.VSCode\settings.json**. 

   ![Definir configuração do cluster padrão](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-the-azure-environment"></a>Configurar o ambiente do Azure

1. [Conecte-se](#connect-to-hdinsight-cluster) à sua conta do Azure se ainda não tiver feito isso.

2. Na barra de menus, navegue para **Exibir** > **Paleta de Comandos...**, e insira **HDInsight: Definir Ambiente do Azure**.

3. Selecione um ambiente como sua entrada de log padrão.

4. Enquanto isso, a ferramenta já salvou sua entrada de logon padrão em **.VSCode\settings.json**. Você também pode atualizá-la diretamente nesse arquivo de configuração. 

   ![Definir a configuração de entrada de logon padrão](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)


## <a name="submit-interactive-hive-queries-hive-batch-scripts"></a>Enviar consultas interativas do Hive, scripts em lotes do Hive

Com as Ferramentas do HDInsight para Visual Studio Code, você pode enviar consultas interativas e scripts em lotes do Hive a clusters do HDInsight.

1. Abra de novo a pasta **HDexample** criada [anteriormente](#open-hdinsight-work-folder), se estiver fechada.  

2. Selecione o arquivo **HelloWorld.hql** criado [anteriormente](#open-hdinsight-work-folder) e ele abrirá no editor de scripts.

3. [Conecte-se](#connect-to-hdinsight-cluster) à sua conta do Azure se ainda não tiver feito isso.

4. Copie e cole o código a seguir no arquivo do Hive e depois salve-o.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

5. Clique com o botão direito do mouse no editor de scripts, selecione **HDInsight: Hive Interativo** para enviar a consulta, ou use o atalho **Ctrl + Alt + I**.  Selecione **HDInsight: Lote do Hive** para enviar o script ou usar atalho **Ctrl + Alt + H**.  

6. Selecione o cluster se você ainda não especificou um cluster padrão. As ferramentas também permitem que você envie um bloco de código, em vez do arquivo de script inteiro, usando o menu de contexto. Após alguns instantes, os resultados da consulta aparecem em uma nova guia.

   ![Resultado do Hive interativo](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Painel **RESULTADOS**: É possível salvar o resultado inteiro como um arquivo CSV, JSON ou Excel para o caminho local ou apenas selecionar várias linhas.

    - Painel **MENSAGENS**: Ao selecionar o número de **Linha**, ele salta para a primeira linha do script em execução.

## <a name="submit-interactive-pyspark-queries"></a>Enviar consultas interativas do PySpark

1. Abra de novo a pasta **HDexample** criada [anteriormente](#open-hdinsight-work-folder), se estiver fechada.  

2. Crie um novo arquivo **HelloWorld.py** seguindo as etapas [anteriores](#open-hdinsight-work-folder).

3. Se não tiver instalado o Python durante os pré-requisitos, você verá uma caixa de diálogo de recomendação da extensão do Python.  Instale e recarregue o Visual Studio Code para concluir a instalação.

    >![Instalação do Python no HDInsight para Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

4. [Conecte-se](#connect-to-hdinsight-cluster) à sua conta do Azure se ainda não tiver feito isso.

5. Copie e cole o seguinte código no arquivo de script:
   ```python
   from operator import add
   lines = spark.read.text("/HdiSamples/HdiSamples/FoodInspectionData/README").rdd.map(lambda r: r[0])
   counters = lines.flatMap(lambda x: x.split(' ')) \
                .map(lambda x: (x, 1)) \
                .reduceByKey(add)

   coll = counters.collect()
   sortedCollection = sorted(coll, key = lambda r: r[1], reverse = True)

   for i in range(0, 5):
        print(sortedCollection[i])
   ```

6. Clique com o botão direito do mouse no editor de scripts, selecione **HDInsight: PySpark Interativo** para enviar a consulta, ou use o atalho **Ctrl+Alt+I**.  

7. Selecione o cluster se você ainda não especificou um cluster padrão. As ferramentas também permitem que você envie um bloco de código, em vez do arquivo de script inteiro, usando o menu de contexto. Após alguns instantes, os resultados da consulta aparecem em uma nova guia.

   ![Enviar o resultado do trabalho do Python](./media/hdinsight-for-vscode/pyspark-interactive-result.png) 

8. A ferramenta também é compatível com a consulta **Cláusula SQL**.

   ![Enviar resultado do trabalho do Python](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png) O status de envio aparece à esquerda da barra de status inferior ao executar consultas. Não envie outras consultas quando o status for **PySpark Kernel (ocupado)**.  

>[!NOTE]  
>Os clusters podem manter as informações da sessão. A variável definida, a função e os valores correspondentes são mantidos na sessão para que possam ser consultados em várias chamadas de serviço para o mesmo cluster. 

### <a name="pyspark3-is-not-supported-with-spark2223"></a>PySpark3 não é compatível com Spark2.2/2.3

O PySpark3 não é mais compatível com o cluster do Spark 2.2 e Spark 2.3. Somente "PySpark" é compatível com o Python. Trata-se de um problema conhecido que envia para o Spark 2.2/2.3 a falha com o Python3.

   ![Enviar para o python3 o erro get](./media/hdinsight-for-vscode/hdi-azure-hdinsight-py3-error.png)

Siga as etapas para usar o Python2.x: 

1. Instale o Python 2.7 no computador local e adicione-o ao caminho do sistema.

2. Reinicie o Visual Studio Code.

3. Mude para o Python 2 clicando no **Python XXX** na barra de status e escolha o Python de destino.

   ![Selecione a versão do Python](./media/hdinsight-for-vscode/hdi-azure-hdinsight-select-python.png)


## <a name="submit-pyspark-batch-job"></a>Enviar o trabalho em lotes de PySpark

1. Abra de novo a pasta **HDexample** criada [anteriormente](#open-hdinsight-work-folder), se estiver fechada.  

2. Crie um novo arquivo **BatchFile.py** seguindo as etapas [anteriores](#open-hdinsight-work-folder).

3. [Conecte-se](#connect-to-hdinsight-cluster) à sua conta do Azure se ainda não tiver feito isso.

4. Copie e cole o seguinte código no arquivo de script:

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

4. Clique com o botão direito do mouse no editor de scripts e, em seguida, selecione **HDInsight: Lote do PySpark**, ou use o atalho **Ctrl + Alt + I**. 

5. Selecione um cluster ao qual enviar seu trabalho PySpark. 

   ![Enviar o resultado do trabalho do Python](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

Depois de enviar um trabalho Python, os logs de envio aparecem na janela **SAÍDA** no Visual Studio Code. A **URL de interface do usuário do Spark** e a **URL de interface do usuário do Yarn** também são mostradas. Você pode abrir a URL em um navegador da Web para acompanhar o status do trabalho.

## <a name="apache-livy-configuration"></a>Configuração do Apache Livy

A configuração do [Apache Livy](https://livy.incubator.apache.org/) é compatível, ela pode ser definida no **.VSCode\settings.json** na pasta do espaço de trabalho. Atualmente, a configuração de Livy é compatível apenas com script do Python. Para obter mais detalhes, consulte o [LEIAME do Livy](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**Como disparar a configuração de Livy**

Método 1  
1. Na barra de menus, navegue para **Arquivo** > **Preferências** > **Configurações**.  
2. Na caixa de texto **Configurações de Pesquisa**, insira **Envio de Trabalho através do HDInsight: Conf Livy**.  
3. Selecione **Editar no settings.json** para o resultado da pesquisa relevante.

Método 2   
Envie um arquivo, observe que a pasta .vscode é adicionada automaticamente à pasta de trabalho. Você pode encontrar a configuração de Livy clicando em **.vscode\settings.json**.

+ As configurações do projeto:

    ![Configuração de Livy](./media/hdinsight-for-vscode/hdi-livyconfig.png)

>[!NOTE]
>Para as configurações **driverMomory** e **executorMomry**, defina o valor com unidade, por exemplo, 1 g ou 1.024 m. 

+ As configurações de Livy compatíveis:   

    **POST /batches**   
    Corpo da solicitação

    | Nome | description | Tipo | 
    | :- | :- | :- | 
    | file | Arquivo que contém o aplicativo a ser executado | caminho (obrigatório) | 
    | proxyUser | Usuário a ser representado ao executar o trabalho | string | 
    | className | Classe principal Java/Spark do aplicativo | string |
    | args | Argumentos de linha de comando do aplicativo | lista de cadeias de caracteres | 
    | jars | jars a serem usados nesta sessão | Lista de cadeias de caracteres | 
    | pyFiles | Arquivos Python a serem usados nesta sessão | Lista de cadeias de caracteres |
    | de entrada | arquivos a serem usados nesta sessão | Lista de cadeias de caracteres |
    | driverMemory | Quantidade de memória a ser usada para o processo de driver | string |
    | driverCores | Quantidade de núcleos a ser usado para o processo de driver | int |
    | executorMemory | Quantidade de memória a ser usada por processo de executor | string |
    | executorCores | Número de núcleos a serem usados para cada executor | int |
    | numExecutors | Número de executores a serem iniciados para esta sessão | int |
    | archives | Arquivos a serem usados nesta sessão | Lista de cadeias de caracteres |
    | fila | O nome da fila YARN ao qual foi enviado | string |
    | Nome | O nome desta sessão | string |
    | conf | Propriedades de configuração do Spark | Mapa de key=val |

    Corpo da resposta   
    O objeto de lote criado.

    | Nome | description | tipo | 
    | :- | :- | :- | 
    | ID | A id da sessão | int | 
    | appId | A ID de aplicativo desta sessão |  Cadeia de caracteres |
    | appInfo | As informações detalhadas do aplicativo | Mapa de key=val |
    | log | As linhas do log | lista de cadeias de caracteres |
    | state |   O estado do lote | string |

>[!NOTE]
>A configuração de Livy atribuída será exibida no painel de saída ao enviar o script.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Integrar ao Azure HDInsight pelo Explorer

O **Azure HDInsight** foi adicionado à exibição do Explorador. Você pode procurar e gerenciar clusters diretamente por meio do **Azure HDInsight**.

1. [Conecte-se](#connect-to-hdinsight-cluster) à sua conta do Azure se ainda não tiver feito isso.

2. Na barra de menus, navegue para **Exibir** > **Explorador**.

3. No painel à esquerda, expanda **AZURE HDINSIGHT**.  As assinaturas e os clusters disponíveis (Spark, Hadoop e HBase são compatíveis) serão listados. 

   ![Assinatura do Azure HDInsight](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

4. Expanda o cluster para exibir o esquema de banco de dados de metadados e tabela do Hive.

   ![Cluster do Azure HDInsight](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)


## <a name="additional-features"></a>Recursos adicionais

O HDInsight para Visual Studio Code é compatível com os seguintes recursos:

- **Preenchimento automático do IntelliSense**. Sugestões aparecem em pop-up para palavras-chave, métodos, variáveis e assim por diante. Os diferentes ícones representam diferentes tipos dos objetos.

    ![Tipos de objeto do IntelliSense das Ferramentas do HDInsight para Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **Marcador de erro do IntelliSense**. O serviço de linguagem sublinha os erros de edição do script do Hive.     
- **Destaques da sintaxe**. O serviço de linguagem usa cores diferentes para diferenciar variáveis, palavras-chave, tipo de dados, funções e assim por diante. 

    ![Destaques da sintaxe das Ferramentas do HDInsight para Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)


## <a name="unlink-cluster"></a>Desvincular cluster

1. Na barra de menus, navegue para **Exibir** > **Paleta de Comandos...** e, em seguida, insira **HDInsight: Desvincular um Cluster**.  

2. Selecione o cluster a desvincular.  

3. Verifique a exibição **SAÍDA**.  


## <a name="logout"></a>Logoff  

Na barra de menus, navegue para **Exibir** > **Paleta de Comandos...** e, em seguida, insira **HDInsight: Logoff**.  Haverá um pop-up no canto inferior direito informando **Logoff bem sucedido!**.


## <a name="next-steps"></a>Próximas etapas
Para assistir a um vídeo de demonstração do uso do HDInsight para o Visual Studio Code, consulte [HDInsight para Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706)
