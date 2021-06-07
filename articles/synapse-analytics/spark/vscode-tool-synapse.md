---
title: Tutorial – Ferramentas Spark e Hive para VSCode (aplicativo Spark)
description: Tutorial – Use as Ferramentas Spark e Hive para VSCode a fim de desenvolver aplicativos Spark escritos em Python e enviá-los a um Pool do Apache Spark sem servidor.
services: synapse-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 09/03/2020
ms.openlocfilehash: e5d335ee14709ec330405419f5be8ac5fbd6ce75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943777"
---
# <a name="tutorial-create-an-apache-spark-applications-with-vscode-using-a-synapse-workspace"></a>Tutorial: criar aplicativos Apache Spark com o VSCode usando um workspace do Azure Synapse

Saiba como usar as Ferramentas Apache Spark e Hive para Visual Studio Code. Use as ferramentas para criar e enviar trabalho em lotes do Apache Hive, consultas interativas do Hive e scripts PySpark para o Apache Spark. Primeiro, descreveremos como instalar as Ferramentas Spark e Hive no Visual Studio Code. Em seguida, vamos examinar passo a passo como enviar trabalhos para as Ferramentas Spark e Hive.

As Ferramentas Spark e Hive podem ser instaladas em plataformas compatíveis com o Visual Studio Code. Observe os pré-requisitos a seguir para diferentes plataformas.

## <a name="prerequisites"></a>Pré-requisitos

Os itens a seguir são necessários para concluir as etapas neste artigo:

- Um Pool do Apache Spark sem servidor. Para criar um Pool do Apache Spark sem servidor, confira [Criar um Pool do Apache Spark usando o portal do Azure](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md).
- [Visual Studio Code](https://code.visualstudio.com/).
- [Mono](https://www.mono-project.com/docs/getting-started/install/). O Mono é necessário apenas para Linux e macOS.
- [Um ambiente do PySpark interativo para Visual Studio Code](../../hdinsight/set-up-pyspark-interactive-environment.md).
- Um diretório Local. Este artigo usa **C:\HD\Synaseexample**.

## <a name="install-spark--hive-tools"></a>Instalar Ferramentas do Spark & Hive

Após cumprir os pré-requisitos, você poderá instalar as Ferramentas Spark e Hive para Visual Studio Code executando as seguintes etapas:

1. Abra o Visual Studio Code.

2. Na barra de menus, navegue até **Exibir** > **Extensões**.

3. Na caixa de pesquisa, insira **Spark & Hive**.

4. Selecione **Ferramentas Spark e Hive** nos resultados da pesquisa e escolha **Instalar**:

     ![Instalação do Python no Spark e Hive para Visual Studio Code](./media/vscode-tool-synapse/install-hdInsight-plugin.png)

5. Selecione **Recarregar** quando necessário.

## <a name="open-a-work-folder"></a>Abrir uma pasta de trabalho

Para abrir uma pasta de trabalho e criar um arquivo no Visual Studio Code, execute as seguintes etapas:

1. Na barra de menus, navegue até **Arquivo** > **Abrir Pasta...**  > **C:\HD\Synaseexample** e escolha o botão **Selecionar Pasta**. A pasta aparece no modo de exibição do **Explorer** à esquerda.

2. Na exibição do **Explorer**, selecione a pasta **Synaseexample** e depois o ícone **Novo Arquivo** ao lado da pasta de trabalho:

     ![Ícone de novo arquivo do Visual Studio Code](./media/vscode-tool-synapse/visual-studio-code-new-file.png)

3. Dê um nome ao novo arquivo usando a extensão de arquivo `.py` (script do Spark). Este exemplo usa **HelloWorld.py**.

## <a name="connect-to-your-spark-pools"></a>Conectar-se aos seus pools do Spark

Entre na assinatura do Azure para se conectar aos seus pools do Spark.

### <a name="sign-in-to-your-azure-subscription"></a>Entre em sua assinatura do Azure

Siga estas etapas para se conectar ao Azure:

1. Na barra de menus, navegue para **Exibir** > **Paleta de Comandos...** e insira **Azure: Entrar**:

     ![Logon nas Ferramentas Spark e Hive para Visual Studio Code](./media/vscode-tool-synapse/hdinsight-for-vscode-extension-login.png)

2. Siga as instruções de entrada para entrar no Azure. Depois que você estiver conectado, o nome da conta do Azure será mostrado na barra de status, na parte inferior da janela do Visual Studio Code.

## <a name="set-the-default-spark-pool"></a>Definir o Pool do Spark padrão

1. Reabra a pasta **Synaseexample** discutida [anteriormente](#open-a-work-folder), se estiver fechada.  

2. Selecione o arquivo **HelloWorld.py**, criado [anteriormente](#open-a-work-folder). Ele será aberto no editor de scripts.

3. Clique com o botão direito do mouse no editor de scripts e selecione **Synapse: Definir o Pool do Spark padrão**.  

4. [Conecte-se](#connect-to-your-spark-pools) à sua conta do Azure se ainda não tiver feito isso.

5. Selecione um Pool do Spark como padrão para o arquivo de script atual. As ferramentas atualizam automaticamente o arquivo de configuração **.VSCode\settings.json**:

     ![Definir configuração de cluster padrão](./media/vscode-tool-synapse/set-default-cluster-configuration.png)

## <a name="submit-interactive-synapse-pyspark-queries-to-spark-pool"></a>Para enviar consultas interativas do Synapse PySpark ao Pool do Spark

Os usuários podem executar o Synapse PySpark interativo no Pool do Spark das seguintes maneiras:

### <a name="using-the-synapse-pyspark-interactive-command-in-py-file"></a>Usando o comando do Synapse PySpark interativo no arquivo PY
Usando o comando do PySpark interativo para enviar as consultas, siga estas etapas:

1. Reabra a pasta **Synaseexample** discutida [anteriormente](#open-a-work-folder), se estiver fechada.  

2. Crie um arquivo **HelloWorld.py** seguindo as etapas [anteriores](#open-a-work-folder).

3. Copie e cole o seguinte código no arquivo de script:

```python
import sys
from operator import add
from pyspark.sql import SparkSession, Row
 
spark = SparkSession\
 .builder\
 .appName("PythonWordCount")\
 .getOrCreate()
 
data = [Row(col1='pyspark and spark', col2=1), Row(col1='pyspark', col2=2), Row(col1='spark vs hadoop', col2=2), Row(col1='spark', col2=2), Row(col1='hadoop', col2=2)]
df = spark.createDataFrame(data)
lines = df.rdd.map(lambda r: r[0])
 
counters = lines.flatMap(lambda x: x.split(' ')) \
 .map(lambda x: (x, 1)) \
 .reduceByKey(add)
 
output = counters.collect()
sortedCollection = sorted(output, key = lambda r: r[1], reverse = True)
 
for (word, count) in sortedCollection:
 print("%s: %i" % (word, count))
```

4. O prompt para instalar o kernel do PySpark/Synapse Pyspark é exibido no canto inferior direito da janela. Você pode clicar no botão **Instalar** para prosseguir com as instalações do PySpark/Synapse Pyspark; ou clicar no botão **Ignorar** para ignorar esta etapa.

     ![Instalar kernel do Pyspark](./media/vscode-tool-synapse/install-the-pyspark-kernel.png)

5. Se precisar instalá-lo mais tarde, você poderá navegar até **Arquivo** > **Preferência** > **Configurações** e desmarcar **HDInsight: habilitar a opção de ignorar instalação do Pyspark** nas configurações. 
    
     ![Habilitar a opção de ignorar a instalação do Pyspark](./media/vscode-tool-synapse/enable-skip-pyspark-installation.png)

6. Se a instalação for bem-sucedida na etapa 4, a caixa de mensagem "PySpark/Synapse Pyspark instalado com sucesso" será exibida no canto inferior direito da janela. Clique no botão **Recarregar** para recarregar a janela.

     ![Pyspark instalado com sucesso](./media/vscode-tool-synapse/pyspark-kernel-installed-successfully.png)

7. Na barra de menus, navegue até **Exibir** > **Paleta de Comandos...** ou use o atalho de teclado **Shift+Ctrl+P** e insira **Python: selecionar interpretador para iniciar o Servidor Jupyter**.

     ![Selecionar interpretador para iniciar o servidor Jupyter](./media/vscode-tool-synapse/select-interpreter-to-start-jupyter-server.png)

8. Selecione a opção Python abaixo.

     ![Escolha a opção abaixo](./media/vscode-tool-synapse/choose-the-below-option.png)
    
9. Na barra de menus, navegue até **Exibir** > **Paleta de Comandos...** ou use o atalho de teclado **Shift+Ctrl+P** e insira **Desenvolvedor: Reload Window**.

     ![Recarregar janela](./media/vscode-tool-synapse/reload-window.png)

10. [Conecte-se](#connect-to-your-spark-pools) à sua conta do Azure se ainda não tiver feito isso.

11. Selecione todo o código, clique com o botão direito do mouse no editor de scripts e selecione **Synapse: Pyspark Interativo** para enviar a consulta. 

     ![menu de contexto do pyspark interativo](./media/vscode-tool-synapse/pyspark-interactive-right-click.png)

12. Selecione o Pool do Spark, caso ainda não tenha especificado um Pool do Spark padrão. Após alguns instantes, os resultados de **Python Interativo** aparecem em uma nova guia. Clique em PySpark para mudar o kernel do **Synapse PySpark**, depois envie o código selecionado novamente e o código será executado com sucesso. As ferramentas também permitem que você envie um bloco de código em vez do arquivo de script inteiro usando o menu de contexto:

     ![Interativo](./media/vscode-tool-synapse/pyspark-interactive-python-interactive-window.png)

### <a name="perform-interactive-query-in-py-file-using-a--comment"></a>Executar consulta interativa no arquivo PY usando um comentário #%%

1. Adicione **#%%** antes do código para obter experiência com o notebook.

     ![adicionar #%%](./media/vscode-tool-synapse/run-cell.png)

2. Clique em **Executar Célula**. Após alguns instantes, os resultados do Python Interativo aparecem em uma nova guia. Clique em PySpark para mudar o kernel do **Synapse PySpark**, depois clique em **Executar Célula** novamente e o código será executado com sucesso. 

     ![Resultados do executar célula](./media/vscode-tool-synapse/run-cell-get-results.png)

## <a name="leverage-ipynb-support-from-python-extension"></a>Aproveite a compatibilidade do IPYNB com a extensão do Python

1. Você pode criar um Jupyter Notebook por comando na Paleta de Comandos ou criando um arquivo .ipynb em seu workspace. Para obter mais informações, confira [Trabalhando com Jupyter Notebooks no Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support)

2. Clique no botão **Executar Célula**, siga os prompts para **Definir o Pool do Spark padrão** (é altamente recomendável definir o cluster/pool padrão toda vez antes de abrir um notebook) e **Recarregue** a janela.

     ![Definir o Pool do Spark padrão e recarregar](./media/vscode-tool-synapse/set-the-default-spark-pool-and-reload.png)

3. Clique em PySpark para mudar o kernel para **Synapse Pyspark** e depois clique em **Executar Célula**. Após um tempo, o resultado será exibido.

     ![Executar resultados do ipynb](./media/vscode-tool-synapse/run-ipynb-file-results.png)


> [!NOTE]
>
>1. O MS-Python versão 2020.5.78807 ou posterior não ser compatível com esta extensão é um [problema conhecido](#known-issues).
>  
>2. Mude para o kernel do Synapse Pyspark, recomenda-se desabilitar as configurações automáticas no portal do Azure. Caso contrário, pode levar muito tempo para ativar o cluster e definir o kernel do Azure Synapse para o primeiro uso. 
>
>    ![Configurações automáticas](./media/vscode-tool-synapse/auto-settings.png)

## <a name="submit-pyspark-batch-job-to-spark-pool"></a>Enviar trabalho em lotes do PySpark para o Pool do Spark

1. Reabra a pasta **Synaseexample** discutida [anteriormente](#open-a-work-folder), se estiver fechada.  

2. Crie um arquivo **BatchFile.py** seguindo as etapas [anteriores](#open-a-work-folder).

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

4. [Conecte-se](#connect-to-your-spark-pools) à sua conta do Azure se ainda não tiver feito isso.

5. Clique com o botão direito do mouse no editor de scripts e selecione **Synapse: lote do PySpark**.

6. Selecione um Pool do Spark para enviar o trabalho do PySpark para:

     ![Enviar a saída do trabalho do Python](./media/vscode-tool-synapse/submit-pythonjob-result.png)

Após enviar um trabalho em lotes para o Pool do Spark, os logs de envio aparecem na janela de **SAÍDA** do Visual Studio Code. A URL da **Interface do Usuário do Spark** e a URL da **Interface do Usuário do Aplicativo de Trabalho do Spark** também são mostradas. Você pode abrir a URL em um navegador da Web para acompanhar o status do trabalho.

## <a name="access-and-manage-synapse-workspace"></a>Acessar e gerenciar o workspace do Synapse

Você pode executar diferentes operações no Azure Explorer dentro das Ferramentas Spark e Hive para VSCode. No Azure Explorer.

![Imagem do Azure](./media/vscode-tool-synapse/azure.png)

### <a name="launch-workspace"></a>Iniciar o workspace

1. No Azure Explorer, navegue até **SYNAPSE**, expanda e exiba a lista de assinaturas do Azure Synapse.

     ![Explorer do Azure Synapse](./media/vscode-tool-synapse/synapse-explorer.png)

2. Clique na Assinatura do workspace do Azure Synapse, expanda e exiba a lista do workspace.

3. Clique com o botão direito do mouse em um workspace e selecione **Exibir aplicativos do Apache Spark**: a página de aplicativo do Apache Spark no site do Synapse Studio será aberta.

     ![Clique com o botão direito do mouse em workspace](./media/vscode-tool-synapse/right-click-on-workspace.png)

     ![Aplicativo do Synapse Studio](./media/vscode-tool-synapse/synapse-studio-application.png)

4. Expanda um workspace, **Armazenamento Padrão** e **Pools do Spark** serão exibidos.

5. Clique com o botão direito do mouse em **Armazenamento Padrão**, as opções **Copiar Caminho Completo** e **Abrir no Synapse Studio** serão exibidas. 

     ![Clique com o botão direito do mouse em armazenamento padrão](./media/vscode-tool-synapse/right-click-on-default-storage.png)

     - Clique em **Copiar Caminho Completo**, a URL principal da conta ADLS Gen2 será copiada e você poderá colá-la onde precisar.

     - Clique em **Abrir no Synapse Studio**, a Conta de Armazenamento principal será aberta no Synapse Studio.

     ![Armazenamento padrão no Synapse Studio](./media/vscode-tool-synapse/default-storage-in-synapse-studio.png)

6. Expanda **Armazenamento Padrão**, a Conta de Armazenamento Principal será exibida.

7. Expanda os **Pools do Spark**, todos os Pools do Spark no workspace são exibidos.

     ![Expanda o pool de armazenamento](./media/vscode-tool-synapse/expand-storage-pool.png)


## <a name="known-issues"></a>Problemas conhecidos

### <a name="ms-python-2020578807-version-is-not-supported-on-this-extention"></a>O MS-Python versão 2020.5.78807 ou posterior não é compatível com esta extensão 

"Falha ao conectar ao Jupyter Notebook." é um problema conhecido para a versão do Python 2020.5.78807 ou posterior. É recomendável que os usuários adotem a versão **[2020.4.76186](https://github.com/microsoft/vscode-python/releases/download/2020.4.76186/ms-python-release.vsix)** do MS-Python para evitar esse problema.

![Problemas conhecidos do](./media/vscode-tool-synapse/known-issue.png)


## <a name="next-steps"></a>Próximas etapas

- [Azure Synapse Analytics](../overview-what-is.md)
- [Criar um Pool do Apache Spark para um workspace do Azure Synapse Analytics](../../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)
