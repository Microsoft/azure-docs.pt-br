---
title: Azure HDInsight para Visual Studio Code
description: Saiba como usar as ferramentas do Spark & Hive (Azure HDInsight) para Visual Studio Code para criar e enviar consultas e scripts.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: da5cdd36e70166d274d50fcb093c0889cf534172
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489008"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>Use as ferramentas do Spark & Hive para Visual Studio Code

Saiba como usar as ferramentas do Spark & Hive para Visual Studio Code para criar e enviar trabalhos de Apache Hive lote, consultas interativas de Hive e scripts PySpark para Apache Spark. Primeiro, descreveremos como instalar as ferramentas do Spark & Hive em Visual Studio Code e, em seguida, veremos como enviar trabalhos para o hive e o Spark.  

As ferramentas do hive do Spark & podem ser instaladas em plataformas com suporte no Visual Studio Code, que incluem Windows, Linux e macOS. Abaixo você encontrará os pré-requisitos para as diferentes plataformas.


## <a name="prerequisites"></a>Pré-requisitos

Os itens a seguir são necessários para concluir as etapas neste artigo:

- Um cluster HDInsight. Para criar um cluster, consulte [Introdução ao HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md). Ou um cluster Spark/Hive com suporte para o ponto de extremidade Livy.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono só é necessário para Linux e MacOS.
- [Configurar o ambiente interativo do PySpark para Visual Studio Code](set-up-pyspark-interactive-environment.md).
- Um diretório local chamado **HDexample**.  Este artigo usa **C:\HD\HDexample**.

## <a name="install-spark--hive-tools"></a>Instalar as ferramentas do Spark & Hive

Depois de concluir os pré-requisitos, você poderá instalar as ferramentas do Spark & Hive para Visual Studio Code.  Conclua as etapas a seguir para instalar as ferramentas do Spark & Hive:

1. Abra o Visual Studio Code.

2. Na barra de menus, navegue para **Exibir** > **Extensões**.

3. Na caixa de pesquisa, insira **Spark & Hive**.

4. Selecione **Ferramentas do hive do Spark &** nos resultados da pesquisa e selecione **instalar**.  

   ![& Hive do Spark para Visual Studio Code instalação do Python](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. **Recarregue** quando necessário.


## <a name="open-work-folder"></a>Abrir pasta de trabalho

Conclua as etapas a seguir para abrir uma pasta de trabalho e criar um arquivo no Visual Studio Code:

1. Na barra de menus, navegue para **Arquivo** > **Abrir Pasta...**  > **C:\HD\HDexample** e, em seguida, selecione o botão **Selecionar Pasta**. A pasta aparece na exibição do **Explorador** à esquerda.

2. Na exibição do **Explorador**, selecione a pasta, **HDexample** e, em seguida, o ícone **Novo Arquivo** ao lado da pasta de trabalho.

   ![Novo arquivo](./media/hdinsight-for-vscode/new-file.png)

3. Nomeie o novo arquivo com a extensão `.hql` de arquivo (consultas do hive `.py` ) ou (script do Spark).  Este exemplo usa **HelloWorld.hql**.

## <a name="set-the-azure-environment"></a>Configurar o ambiente do Azure

Para o usuário da nuvem nacional, siga as etapas para definir o ambiente do Azure **primeiro e, em seguida, use o Azure:**  Comando de entrada para entrar no Azure.
   
1. Clique em **File\Preferences\Settings**.
2. Pesquisar **no Azure: Nuvem**
3. Selecione a nuvem nacional na lista.

   ![Definir a configuração de entrada de logon padrão](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-azure-account"></a>Conectar-se à conta do Azure

Antes de enviar scripts para seus clusters do Visual Studio Code, você precisa se conectar à sua conta do Azure ou vincular um cluster (usando Ambari nome de usuário/senha ou conta ingressada no domínio).  Conclua as seguintes etapas para se conectar ao Azure:

1. Na barra de menus, navegue até **Exibir** > **paleta de comandos...** e **insira Azure:** Entre.

    ![Ferramentas do hive do Spark & para o logon do Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Siga as instruções de entrada para entrar no Azure. Depois que você estiver conectado, o nome da sua conta do Azure será mostrado na barra de status na parte inferior da janela de Visual Studio Code.  
  

## <a name="link-a-cluster"></a>Vincular um cluster

### <a name="link-azure-hdinsight"></a>Vínculo: Azure HDInsight

É possível vincular um cluster normal usando um nome de usuário gerenciado do [Apache Ambari](https://ambari.apache.org/) ou vincular um cluster Hadoop seguro do pacote Enterprise Security usando um nome de usuário de domínio (como: `user1@contoso.com`).

1. Na barra de menus, navegue até **Exibir** > **paleta de comandos...** e **insira Spark/Hive: Vincular um Cluster**.

   ![comando para vincular cluster](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Selecione o tipo de cluster vinculado **Azure HDInsight**.

3. Insira a URL do cluster do HDInsight.

4. Insira o nome de usuário do Ambari, o padrão é **admin**.

5. Insira a senha do Ambari.

6. Selecione o tipo de cluster.

7. Defina o nome de exibição do cluster (opcional).

8. Verifique a exibição **SAÍDA**.

   > [!NOTE]  
   > O nome de usuário e a senha vinculados serão usados se o cluster foi registrado na assinatura do Azure e vinculou um cluster.  


### <a name="link-generic-livy-endpoint"></a>Vínculo: Ponto de Extremidade Genérico do Livy

1. Na barra de menus, navegue até **Exibir** > **paleta de comandos...** e **insira Spark/Hive: Vincular um Cluster**.

2. Selecione o tipo de cluster vinculado **Ponto de Extremidade Genérico do Livy**.

3. Digite o ponto de extremidade genérico do Livy, por exemplo: http\://10.172.41.42:18080.

4. Selecione o tipo de autorização, **Básico** ou **Nenhum**.  Se **Básico**, então:  
    &emsp;a. Insira o nome de usuário do Ambari, o padrão é **admin**.  
    &emsp;b. Insira a senha do Ambari.

5. Verifique a exibição **SAÍDA**.

## <a name="list-clusters"></a>Listar clusters

1. Na barra de menus, navegue até **Exibir** > **paleta de comandos...** e **insira Spark/Hive: Listar clusters**.

2. Selecione a assinatura desejada.

3. Verifique a exibição **SAÍDA**.  A exibição mostrará os clusters vinculados e todos os clusters sob sua assinatura do Azure.

    ![Definir uma configuração do cluster padrão](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-default-cluster"></a>Definir cluster padrão

1. Abra de novo a pasta **HDexample** criada [anteriormente](#open-work-folder), se estiver fechada.  

2. Selecione o arquivo **HelloWorld.hql** criado [anteriormente](#open-work-folder) e ele abrirá no editor de scripts.

3. Clique com o botão direito do mouse no editor **de scripts e selecione Spark/Hive: Definir cluster padrão**.  

4. [Conecte-](#connect-to-azure-account) se à sua conta do Azure ou vincule um cluster, caso ainda não tenha feito isso.

5. Selecione um cluster como o padrão para o arquivo de script atual. As ferramentas atualizam automaticamente o arquivo de configuração **.VSCode\settings.json**. 

   ![Definir configuração do cluster padrão](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)


## <a name="submit-interactive-hive-queries-hive-batch-scripts"></a>Enviar consultas interativas do Hive, scripts em lotes do Hive

Com as ferramentas do Spark & Hive para Visual Studio Code, você pode enviar consultas interativas do hive e scripts de lote do hive para seus clusters.

1. Abra de novo a pasta **HDexample** criada [anteriormente](#open-work-folder), se estiver fechada.  

2. Selecione o arquivo **HelloWorld.hql** criado [anteriormente](#open-work-folder) e ele abrirá no editor de scripts.


3. Copie e cole o código a seguir no arquivo do Hive e depois salve-o.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. [Conecte-](#connect-to-azure-account) se à sua conta do Azure ou vincule um cluster, caso ainda não tenha feito isso.

5. Clique com o botão direito do mouse no **editor de scripts, selecione Hive: Interativo** para enviar a consulta ou use o atalho **Ctrl + Alt + I**.  Selecionar **Hive: Lote** para enviar o script ou use o atalho **Ctrl + Alt + H**.  

6. Selecione o cluster se você ainda não especificou um cluster padrão. As ferramentas também permitem que você envie um bloco de código, em vez do arquivo de script inteiro, usando o menu de contexto. Após alguns instantes, os resultados da consulta aparecem em uma nova guia.

   ![Resultado do Hive interativo](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Painel **RESULTADOS**: É possível salvar o resultado inteiro como um arquivo CSV, JSON ou Excel para o caminho local ou apenas selecionar várias linhas.

    - Painel **MENSAGENS**: Ao selecionar o número de **Linha**, ele salta para a primeira linha do script em execução.

## <a name="submit-interactive-pyspark-queries"></a>Enviar consultas interativas do PySpark

Você pode enviar consultas PySpark interativas seguindo as etapas abaixo:

1. Abra de novo a pasta **HDexample** criada [anteriormente](#open-work-folder), se estiver fechada.  

2. Crie um novo arquivo **HelloWorld.py** seguindo as etapas [anteriores](#open-work-folder).

3. Copie e cole o seguinte código no arquivo de script:
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

4. [Conecte-](#connect-to-azure-account) se à sua conta do Azure ou vincule um cluster, caso ainda não tenha feito isso.

5. Escolha todo o código e clique com o botão direito do mouse no **editor de scripts, selecione Spark: PySpark Interativo** para enviar a consulta, ou use o atalho **Ctrl+Alt+I**.

   ![menu de contexto interativo do pyspark](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

6. Selecione o cluster se você ainda não especificou um cluster padrão. Após alguns instantes, os resultados interativos do **Python** aparecem em uma nova guia. As ferramentas também permitem que você envie um bloco de código, em vez do arquivo de script inteiro, usando o menu de contexto. 

   ![janela interativa do Python pyspark Interactive](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png) 

7. Insira **"%% info"** e pressione **Shift + Enter** para exibir informações do trabalho. (Opcional)

   ![exibir informações do trabalho](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

8. A ferramenta também dá suporte à consulta **SQL do Spark** .

   ![Resultado da exibição interativa do Pyspark](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)

   O status de envio é exibido à esquerda da barra de status inferior quando você está executando consultas. Não envie outras consultas quando o status for **PySpark Kernel (ocupado)** .  

   > [!NOTE] 
   >
   > Quando a **extensão do Python habilitada** está desmarcada nas configurações (a configuração padrão está marcada), os resultados da interação pyspark enviada usarão a janela antiga.
   >
   > ![extensão do Python interativa do pyspark desabilitada](./media/hdinsight-for-vscode/pyspark-interactive-python-extension-disabled.png)


## <a name="submit-pyspark-batch-job"></a>Enviar o trabalho em lotes de PySpark

1. Abra de novo a pasta **HDexample** criada [anteriormente](#open-work-folder), se estiver fechada.  

2. Crie um novo arquivo **BatchFile.py** seguindo as etapas [anteriores](#open-work-folder).

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

4. [Conecte-](#connect-to-azure-account) se à sua conta do Azure ou vincule um cluster, caso ainda não tenha feito isso.

5. Clique com o botão direito do mouse no editor de **scripts e selecione Spark: Lote do PySpark**, ou use o atalho **Ctrl + Alt + I**. 

6. Selecione um cluster ao qual enviar seu trabalho PySpark. 

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
    Corpo de Solicitação

    | name | description | type | 
    | :- | :- | :- | 
    | Arquivo | Arquivo que contém o aplicativo a ser executado | caminho (obrigatório) | 
    | proxyUser | Usuário a ser representado ao executar o trabalho | cadeia de caracteres | 
    | className | Classe principal Java/Spark do aplicativo | cadeia de caracteres |
    | args | Argumentos de linha de comando do aplicativo | lista de cadeias de caracteres | 
    | jars | jars a serem usados nesta sessão | Lista de cadeias de caracteres | 
    | pyFiles | Arquivos Python a serem usados nesta sessão | Lista de cadeias de caracteres |
    | files | arquivos a serem usados nesta sessão | Lista de cadeias de caracteres |
    | driverMemory | Quantidade de memória a ser usada para o processo de driver | cadeia de caracteres |
    | driverCores | Quantidade de núcleos a ser usado para o processo de driver | int |
    | executorMemory | Quantidade de memória a ser usada por processo de executor | cadeia de caracteres |
    | executorCores | Número de núcleos a serem usados para cada executor | int |
    | numExecutors | Número de executores a serem iniciados para esta sessão | int |
    | archives | Arquivos a serem usados nesta sessão | Lista de cadeias de caracteres |
    | queue | O nome da fila YARN ao qual foi enviado | cadeia de caracteres |
    | name | O nome desta sessão | cadeia de caracteres |
    | conf | Propriedades de configuração do Spark | Mapa de key=val |

    Corpo da Resposta   
    O objeto de lote criado.

    | name | description | type | 
    | :- | :- | :- | 
    | id | A id da sessão | int | 
    | appId | A ID de aplicativo desta sessão |  Cadeia |
    | appInfo | As informações detalhadas do aplicativo | Mapa de key=val |
    | log | As linhas do log | lista de cadeias de caracteres |
    | state |   O estado do lote | cadeia de caracteres |

>[!NOTE]
>A configuração de Livy atribuída será exibida no painel de saída ao enviar o script.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Integrar ao Azure HDInsight pelo Explorer

O **Azure HDInsight** foi adicionado à exibição do Explorador. Você pode procurar e gerenciar clusters diretamente por meio do **Azure HDInsight**.

1. [Conecte-](#connect-to-azure-account) se à sua conta do Azure ou vincule um cluster, caso ainda não tenha feito isso.

2. Na barra de menus, navegue para **Exibir** > **Explorador**.

3. No painel à esquerda, expanda **AZURE HDINSIGHT**.  As assinaturas e os clusters disponíveis (Spark, Hadoop e HBase são compatíveis) serão listados. 

   ![Assinatura do Azure HDInsight](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

4. Expanda o cluster para exibir o esquema de banco de dados de metadados e tabela do Hive.

   ![Cluster do Azure HDInsight](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)


## <a name="preview-hive-table"></a>Visualizar tabela do hive
Você pode visualizar a tabela do hive em seus clusters diretamente por meio **do Azure HDInsight** Explorer.
1. [Conecte-se](#connect-to-azure-account) à sua conta do Azure se ainda não tiver feito isso.

2. Clique no ícone **do Azure** na coluna da extrema esquerda.

3. No painel à esquerda, expanda **AZURE HDINSIGHT**. As assinaturas e os clusters disponíveis serão listados.

4. Expanda o cluster para exibir o esquema de banco de dados de metadados e tabela do Hive.

5. Clique com o botão direito do mouse na tabela do hive, por exemplo, hivesampletable. Selecione **Visualização**. 

   ![Hive do Spark & para a tabela do hive da visualização do Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. A janela **Visualizar resultados** será aberta.

   ![Hive do Spark & para janela de resultados da visualização do Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)
   
- Painel de **resultados**

   É possível salvar o resultado inteiro como um arquivo CSV, JSON ou Excel para o caminho local ou apenas selecionar várias linhas.

- Painel de **mensagens**
   1. Quando o número de linhas na tabela for maior que 100 linhas, a mensagem mostrará: **As primeiras 100 linhas são exibidas para a tabela Hive**.
   2. Quando o número de linhas na tabela for menor ou igual a 100 linhas, a mensagem mostrará: **60 linhas são exibidas para a tabela do hive**.
   3. Quando não há nenhum conteúdo na tabela, a mensagem mostra: **0 a linha é exibida para a tabela do hive**.

>[!NOTE]
>
>No Linux, instale o XCLIP para habilitar a cópia de dados da tabela.
>
>![Spark & Hive para Visual Studio Code no Linux](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)
## <a name="additional-features"></a>Recursos adicionais

O Spark & Hive para Visual Studio Code dá suporte aos seguintes recursos:

- **Preenchimento automático do IntelliSense**. Sugestões aparecem em pop-up para palavras-chave, métodos, variáveis e assim por diante. Os diferentes ícones representam diferentes tipos dos objetos.

    ![Ferramentas do hive & Spark para tipos de objeto Visual Studio Code IntelliSense](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **Marcador de erro do IntelliSense**. O serviço de linguagem sublinha os erros de edição do script do Hive.     
- **Destaques da sintaxe**. O serviço de linguagem usa cores diferentes para diferenciar variáveis, palavras-chave, tipo de dados, funções e assim por diante. 

    ![Ferramentas do Spark & Hive para Visual Studio Code destaques da sintaxe](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>Função somente leitor

Os usuários com a **função** **somente** **leitor** de cluster não podem mais enviar trabalho para o cluster HDInsight nem exibir o banco de dados do hive. Contate o administrador de cluster para atualizar sua função para o [ **operador** de **cluster** **HDInsight** ](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) no [portal do Azure](https://ms.portal.azure.com/). Se você souber credenciais do Ambari, poderá vincular manualmente o cluster seguindo a instrução abaixo.

### <a name="browse-hdinsight-cluster"></a>Procurar cluster HDInsight  

Ao clicar no Azure HDInsight Explorer para expandir um cluster HDInsight, você será solicitado a vincular o cluster se você for uma função somente leitor para o cluster. Siga as etapas abaixo para vincular ao cluster por meio de credenciais do Ambari. 

### <a name="submit-job-to-hdinsight-cluster"></a>Enviar trabalho para o cluster HDInsight

Ao enviar o trabalho para um cluster HDInsight, você será solicitado a vincular o cluster se você for a função somente leitor para o cluster. Siga as etapas abaixo para vincular ao cluster por meio de credenciais do Ambari. 

### <a name="link-to-cluster"></a>Vincular ao cluster

1.  Insira o nome de usuário do Ambari 
2.  Insira a senha de usuário do Ambari.

   ![Ferramentas do hive & Spark para Visual Studio Code nome de usuário](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![Ferramentas do hive do Spark & para Visual Studio Code senha](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

> [!NOTE]
>
>Você pode usar o Spark/Hive: Listar cluster para verificar o cluster vinculado.
>
>![Ferramentas do hive do Spark & para Visual Studio Code Reader vinculado](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="azure-data-lake-storage-gen2-adls-gen2"></a>Azure Data Lake Storage Gen2 (ADLS Gen2)

### <a name="browse-an-adls-gen2-account"></a>Procurar uma conta de ADLS Gen2

Ao clicar no Azure HDInsight Explorer para expandir uma conta de ADLS Gen2, você será solicitado a inserir a **chave de acesso** de armazenamento se sua conta do Azure não tiver acesso ao armazenamento Gen2. A conta de ADLS Gen2 será expandida automaticamente depois que a chave de acesso for validada com êxito. 

### <a name="submit-jobs-to-hdinsight-cluster-with-adls-gen2"></a>Enviar trabalhos para o cluster HDInsight com ADLS Gen2

Ao enviar o trabalho para um cluster HDInsight com ADLS Gen2, você será solicitado a inserir a **chave de acesso** de armazenamento se sua conta do Azure não tiver acesso de gravação ao armazenamento Gen2.  O trabalho será enviado com êxito depois que a chave de acesso for validada com êxito. 

![Ferramentas do hive & Spark para Visual Studio Code AccessKey](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)   

> [!NOTE]
> 
>Você pode obter a chave de acesso para a conta de armazenamento do portal do Azure. Para obter informações, consulte [Exibir e copiar chaves de acesso](https://docs.microsoft.com/azure/storage/common/storage-account-manage#access-keys).

## <a name="unlink-cluster"></a>Desvincular cluster

1. Na barra de menus, navegue até **Exibir** > **paleta de comandos...** e, **em seguida, insira Spark/Hive: Desvincular um Cluster**.  

2. Selecione o cluster a desvincular.  

3. Verifique a exibição **SAÍDA**.  

## <a name="sign-out"></a>Sair  

Na barra de menus, navegue até **Exibir** > **paleta de comandos...** e, **em seguida, insira Azure:** Saia.


## <a name="next-steps"></a>Próximas etapas
Para ver um vídeo de demonstração de uso do Spark & Hive para Visual Studio Code, consulte [spark & Hive para Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706)
