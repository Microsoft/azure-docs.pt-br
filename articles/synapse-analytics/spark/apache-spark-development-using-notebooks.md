---
title: Blocos de anotações do Synapse Studio
description: Neste artigo, você aprenderá a criar e desenvolver blocos de anotações do Azure Synapse Studio para fazer a preparação e a visualização dos dados.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 10/19/2020
ms.author: ruxu
ms.reviewer: ''
ms.custom: devx-track-python
ms.openlocfilehash: c5dfd442bb52a5b1d319bd0a40b656d549134e7e
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612294"
---
# <a name="create-develop-and-maintain-synapse-studio-notebooks-in-azure-synapse-analytics"></a>Criar, desenvolver e manter blocos de anotações do Synapse Studio no Azure Synapse Analytics

Um notebook do Synapse Studio é uma interface da Web para que você crie arquivos que contenham código ao vivo, visualizações e texto de narração. Os notebooks são um bom lugar para validar ideias e fazer experimentos rápidos para obter insights de seus dados. Os notebooks também são amplamente usados na preparação e visualização de dados, no aprendizado de máquina e em outros cenários de Big Data.

Com um notebook do Azure Synapse Studio, você pode:

* Começar a trabalhar com um mínimo de configuração.
* Manter os dados protegidos com recursos internos de segurança corporativa.
* Analisar dados em formatos brutos (CSV, txt, JSON, etc.), formatos de arquivo processados (Parquet, Delta Lake, ORC, etc.) e arquivos de dados tabulares do SQL em Spark e SQL.
* Seja produtivo com recursos de criação aprimorados e visualização de dados interna.

Este artigo descreve como usar notebooks no Azure Synapse Studio.

## <a name="preview-of-the-new-notebook-experience"></a>Visualização da nova experiência de notebook
A equipe de Synapse colocou o novo componente de notebooks no Synapse Studio para fornecer uma experiência de notebook consistente para os clientes da Microsoft e maximizar a descoberta, a produtividade, o compartilhamento e a colaboração. A nova experiência do notebook está pronta para visualização. Verifique o botão **recursos de visualização** na barra de ferramentas do bloco de anotações para ativá-lo. A tabela a seguir captura a comparação de recursos entre o bloco de anotações existente (portanto, chamado de "Notebook clássico") com a nova visualização.  

|Recurso|Notebook clássico|Visualizar bloco de notas|
|--|--|--|
|% de execução| Sem suporte | &#9745;|
|Histórico de%| Sem suporte |&#9745;
|% de carga| Sem suporte |&#9745;|
|%% HTML| Sem suporte |&#9745;|
|Arrastar e soltar para mover uma célula| Sem suporte |&#9745;|
|Saída de exibição persistente ()|&#9745;| Não disponível |
|Formatar célula de texto com botões da barra de ferramentas|&#9745;| Não disponível |
|Desfazer operação de célula| &#9745;| Não disponível |


## <a name="create-a-notebook"></a>Criar um notebook

Há dois modos de criar um notebook. Você pode criar um notebook ou importar um existente para um workspace do Azure Synapse no **Pesquisador de Objetos**. Os notebooks do Azure Synapse Studio podem reconhecer arquivos IPYNB padrão do Jupyter Notebook.

![criar bloco de anotações de importação](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook-2.png)

## <a name="develop-notebooks"></a>Desenvolver notebooks

Os notebooks consistem em células, que são blocos individuais de código ou texto que podem ser executados de forma independente ou em grupo.

### <a name="add-a-cell"></a>Adicionar uma célula

Há várias maneiras de adicionar uma nova célula ao notebook.

# <a name="classical-notebook"></a>[Notebook clássico](#tab/classical)

1. Expanda o botão superior esquerdo **+ Célula** e selecione **Adicionar célula de código** ou **Adicionar célula de texto**.

    ![add-cell-with-cell-button](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. Passe o mouse sobre o espaço entre duas células e selecione **Adicionar código** ou **Adicionar texto**.

    ![add-cell-between-space](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. Use [teclas de atalho no modo de comando](#shortcut-keys-under-command-mode). Pressione **A** para inserir uma célula acima da célula atual. Pressione **B** para inserir uma célula abaixo da célula atual.


# <a name="preview-notebook"></a>[Visualizar bloco de notas](#tab/preview)

1. Expanda o botão superior esquerdo **+ célula** e selecione **célula de código** ou **célula de redução**.
    ![Add-Azure-Notebook-célula-com-botão-célula](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-add-cell-1.png)
2. Selecione o sinal de adição no início de uma célula e selecione **célula de código** ou **célula de redução**.

    ![Adicionar-Azure-Notebook-célula-entre-espaço](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-add-cell-2.png)

3. Use [as teclas de atalho aznb no modo de comando](#shortcut-keys-under-command-mode). Pressione **A** para inserir uma célula acima da célula atual. Pressione **B** para inserir uma célula abaixo da célula atual.

---

### <a name="set-a-primary-language"></a>Definir uma linguagem principal

Os notebooks do Azure Synapse Studio dão suporte a quatro linguagens do Apache Spark:

* pySpark (Python)
* Spark (Scala)
* SparkSQL
* .NET para Apache Spark (C#)

Você pode definir a linguagem principal para novas células adicionadas na lista suspensa na barra de comandos superior.

   ![default-synapse-language](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>Usar vários idiomas

Você pode usar várias linguagens em um notebook especificando o comando magic da linguagem correto no início de uma célula. A tabela a seguir lista os comandos magic para alternar entre linguagens de célula.

|Comandos magic |Linguagem | Descrição |  
|---|------|-----|
|%%pyspark| Python | Execute uma consulta **Python** no contexto do Spark.  |
|%%spark| Scala | Execute uma consulta **Scala** no contexto do Spark.  |  
|%%sql| SparkSQL | Execute uma consulta **SparkSQL** no contexto do Spark.  |
|%%csharp | .NET para Spark C# | Execute uma consulta **.NET para Spark C#** no contexto do Spark. |

A imagem a seguir é um exemplo de como você pode escrever uma consulta PySpark com o comando magic **%%pyspark** ou uma consulta SparkSQL com o comando magic **%%sql** em um notebook **Spark (Scala)** . Observe que a linguagem principal do notebook está definida como pySpark.

   ![Comandos mágicos do Synapse Spark](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages"></a>Usar tabelas temporárias para fazer referência a dados entre linguagens

Você não pode fazer referência a dados ou variáveis diretamente em linguagens diferentes em um notebook do Synapse Studio. No Spark, uma tabela temporária pode ser referenciada entre os idiomas. Aqui está um exemplo de como ler um dataframe `Scala` no `PySpark` e `SparkSQL` usando uma tabela temporária do Spark como solução alternativa.

1. Na célula 1, leia um dataframe de um conector de pool do SQL usando escalabilidade e crie uma tabela temporária.

   ```scala
   %%scala
   val scalaDataFrame = spark.read.sqlanalytics("mySQLPoolDatabase.dbo.mySQLPoolTable")
   scalaDataFrame.createOrReplaceTempView( "mydataframetable" )
   ```

2. Na célula 2, consulte os dados com o Spark SQL.
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. Na célula 3, use os dados no PySpark.

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>IntelliSense de estilo IDE

Os notebooks do Azure Synapse Studio são integrados ao editor Monaco para levar o IntelliSense de estilo IDE ao editor de célula. O realce de sintaxe, o marcador de erro e as conclusões automáticas de código ajudam você a escrever código e identificar problemas mais rapidamente.

Os recursos do IntelliSense estão em níveis diferentes de maturidade para linguagens diferentes. Use a tabela a seguir para ver o que tem suporte.

|Languages| Realce da sintaxe | Marcador de erro de sintaxe  | Conclusão de código de sintaxe | Conclusão de código de variável| Conclusão de código de função do sistema| Conclusão do código de função do usuário| Recuo Inteligente | Dobramento de código|
|--|--|--|--|--|--|--|--|--|
|PySpark (Python)|Sim|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Spark (Scala)|Sim|Sim|Sim|Sim|-|-|-|Sim|
|SparkSQL|Sim|Sim|-|-|-|-|-|-|
|.NET para Spark (C#)|Sim|-|-|-|-|-|-|-|

### <a name="format-text-cell-with-toolbar-buttons"></a>Formatar célula de texto com botões da barra de ferramentas

# <a name="classical-notebook"></a>[Notebook clássico](#tab/classical)

Você pode usar os botões de formatação da barra de ferramentas de células de texto para realizar ações de markdown comuns. Eles incluem texto em negrito, texto em itálico, inserção de trechos de código, inserção de lista não ordenada, inserção de lista ordenada e inserção de imagem da URL.

  ![Barra de ferramentas de célula de texto Synapse](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

# <a name="preview-notebook"></a>[Visualizar bloco de notas](#tab/preview)

A barra de ferramentas do botão Formatar ainda não está disponível para a experiência de visualização do bloco de anotações. 

---

### <a name="undo-cell-operations"></a>Desfazer operações de célula

# <a name="classical-notebook"></a>[Notebook clássico](#tab/classical)

Selecione o botão **desfazer** ou pressione **Ctrl + Z** para revogar a operação de célula mais recente. Agora você pode desfazer até as 20 últimas ações do histórico da célula. 

   ![Synapse desfazer células](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)
# <a name="preview-notebook"></a>[Visualizar bloco de notas](#tab/preview)

A operação de desfazer célula ainda não está disponível para a experiência de visualização do bloco de anotações. 

---

### <a name="move-a-cell"></a>Mover uma célula

# <a name="classical-notebook"></a>[Notebook clássico](#tab/classical)

Selecione as reticências (...) para acessar o menu de ações adicionais de célula na extrema direita. Em seguida, selecione **Mover célula para cima** ou **Mover célula para baixo** para mover a célula atual. 

Você também pode usar [teclas de atalho no modo de comando](#shortcut-keys-under-command-mode). Pressione **Ctrl+Alt+↑** a fim de mover a célula atual para cima. Pressione **Ctrl+Alt+↓** a fim de mover a célula atual para baixo.

   ![move-a-cell](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

# <a name="preview-notebook"></a>[Visualizar bloco de notas](#tab/preview)

Clique no lado esquerdo de uma célula e arraste-a para a posição desejada. 
    ![Synapse mover células](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-drag-drop-cell.gif)

---

### <a name="delete-a-cell"></a>Excluir uma célula

# <a name="classical-notebook"></a>[Notebook clássico](#tab/classical)

Para excluir uma célula, selecione as reticências (...), acesse o menu de ações de célula adicional na extrema direita e selecione **Excluir uma célula**. 

Você também pode usar [teclas de atalho no modo de comando](#shortcut-keys-under-command-mode). Pressione **D,D** para excluir a célula atual.
  
   ![delete-a-cell](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

# <a name="preview-notebook"></a>[Visualizar bloco de notas](#tab/preview)

Para excluir uma célula, selecione o botão excluir à direita da célula. 

Você também pode usar [teclas de atalho no modo de comando](#shortcut-keys-under-command-mode). Pressione **Shift + D** para excluir a célula atual. 

   ![Azure-Notebook-excluir-a-célula](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-delete-cell.png)

---

### <a name="collapse-a-cell-input"></a>Recolher uma entrada de célula

# <a name="classical-notebook"></a>[Notebook clássico](#tab/classical)

Selecione o botão de seta na parte inferior da célula atual para recolhê-lo. Para expandi-lo, selecione o botão de seta enquanto a célula está recolhida.

   ![collapse-cell-input](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

# <a name="preview-notebook"></a>[Visualizar bloco de notas](#tab/preview)

Selecione as reticências **mais comandos** (...) na barra de ferramentas da célula e **insira** uma entrada para recolher a entrada da célula atual. Para expandi-lo, selecione a **entrada oculta** enquanto a célula é recolhida.

   ![Azure-Notebook-Collapse-CÉL-Input](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-collapse-cell-input.gif)

---

### <a name="collapse-a-cell-output"></a>Recolher uma saída de célula

# <a name="classical-notebook"></a>[Notebook clássico](#tab/classical)

Selecione o botão **recolher saída** no canto superior esquerdo da saída da célula atual para recolhê-la. Para expandi-lo, selecione a **saída mostrar célula** enquanto a saída da célula estiver recolhida.

   ![collapse-cell-output](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

# <a name="preview-notebook"></a>[Visualizar bloco de notas](#tab/preview)

Selecione as reticências **mais comandos** (...) na barra de ferramentas da célula e **saída** para recolher a saída da célula atual. Para expandi-lo, selecione o mesmo botão enquanto a saída da célula estiver oculta.

   ![Azure-Notebook-Collapse-CÉL-output](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-collapse-cell-output.gif)


---

## <a name="run-notebooks"></a>Executar notebooks

Você pode executar as células de código em seu notebook individualmente ou todas de uma vez. O status e o progresso de cada célula são representados no notebook.

### <a name="run-a-cell"></a>Executar uma célula

Há várias maneiras de executar o código em uma célula.

1. Passe o mouse sobre a célula que você deseja executar e selecione o botão **Executar Célula** ou pressione **Ctrl+Enter**.

   ![run-cell-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)
  
2. Use [teclas de atalho no modo de comando](#shortcut-keys-under-command-mode). Pressione **Shift+Enter** para executar a célula atual e selecionar a célula abaixo. Pressione **Alt+Enter** para executar a célula atual e inserir uma célula abaixo.

---

### <a name="run-all-cells"></a>Executar todas as células
Selecione o botão **executar tudo** para executar todas as células no bloco de anotações atual em sequência.

   ![run-all-cells](./media/apache-spark-development-using-notebooks/synapse-run-all.png)


### <a name="run-all-cells-above-or-below"></a>Executar todas as células acima ou abaixo

# <a name="classical-notebook"></a>[Notebook clássico](#tab/classical)

Para acessar o menu de ações adicionais de célula na extrema direita, selecione as reticências ( **...** ). Em seguida, selecione **Executar células acima** para executar todas as células acima da atual em sequência. Selecione **Executar células abaixo** para executar todas as células abaixo da atual em sequência.

   ![run-cells-above-or-below](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)

# <a name="preview-notebook"></a>[Visualizar bloco de notas](#tab/preview)

Expanda a lista suspensa do botão **executar tudo** e selecione **executar células acima** para executar todas as células acima da atual em sequência. Selecione **Executar células abaixo** para executar todas as células abaixo da atual em sequência.

   ![Azure-Notebook-Run-Cells-Above-ou-abaixo](./media/apache-spark-development-using-notebooks/synapse-aznb-run-cells-above-or-below.png)

---

### <a name="cancel-all-running-cells"></a>Cancelar todas as células em execução

# <a name="classical-notebook"></a>[Notebook clássico](#tab/classical)
Selecione o botão **cancelar tudo** para cancelar as células ou células em execução aguardando na fila. 
   ![cancelar-todas as células](./media/apache-spark-development-using-notebooks/synapse-cancel-all.png) 

# <a name="preview-notebook"></a>[Visualizar bloco de notas](#tab/preview)

Selecione o botão **cancelar tudo** para cancelar as células ou células em execução aguardando na fila. 
   ![Azure-Notebook-cancelar-todas-células](./media/apache-spark-development-using-notebooks/synapse-aznb-cancel-all.png) 

---



### <a name="notebook-reference"></a>Referência do bloco de anotações

# <a name="classical-notebook"></a>[Notebook clássico](#tab/classical)

Não há suporte.

# <a name="preview-notebook"></a>[Visualizar bloco de notas](#tab/preview)

Você pode usar o ```%run <notebook path>``` comando mágico para fazer referência a outro bloco de anotações no contexto do bloco de anotações atual. Todas as variáveis definidas no bloco de anotações de referência estão disponíveis no bloco de anotações atual. ```%run``` o comando mágico dá suporte a chamadas aninhadas, mas não oferece suporte a chamadas recursivas. Você receberá uma exceção se a profundidade da instrução for maior que cinco. ```%run``` Atualmente, o comando só dá suporte para passar um caminho de bloco de anotações como parâmetro. 

Exemplo: ``` %run /path/notebookA ```.

> [!NOTE]
> Não há suporte para a referência do bloco de anotações no pipeline do Synapse.
>
>

---


### <a name="cell-status-indicator"></a>Indicador de status de célula

Um status de execução de célula passo a passo é exibido abaixo da célula para ajudá-lo a ver seu progresso atual. Depois que a execução da célula for concluída, um resumo de execução com a duração total e a hora de término serão mostrados e mantidos ali para referência futura.

![cell-status](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Indicador de progresso do Spark

O notebook do Azure Synapse Studio é baseado exclusivamente no Spark. As células de código são executadas no pool de Apache Spark sem servidor remotamente. Um indicador de progresso do trabalho do Spark é fornecido com uma barra de progresso em tempo real para ajudá-lo a entender o status de execução do trabalho.
O número de tarefas por cada trabalho ou estágio ajuda a identificar o nível paralelo do seu trabalho do Spark. Você também pode analisar mais detalhadamente a interface do usuário do Spark de um trabalho específico (ou estágio) por meio da seleção do link no nome do trabalho (ou estágio).


![spark-progress-indicator](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Configuração de sessão do Spark

Você pode especificar a duração do tempo limite, o número e o tamanho dos executores para dar à sessão atual do Spark em **Configurar sessão**. Reinicie a sessão do Spark para que as alterações de configuração entrem em vigor. Todas as variáveis do notebook em cache serão limpas.

[![gerenciamento de sessão](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-spark-session-management.png)](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-spark-session-management.png#lightbox)

#### <a name="spark-session-config-magic-command"></a>Comando mágico de configuração de sessão do Spark
Você também pode especificar as configurações de sessão do Spark por meio de um comando mágico **%% Configurar**. A sessão do Spark precisa ser reiniciada para que as configurações tenham efeito. Recomendamos que você execute a **configuração%%** no início do notebook. Aqui está um exemplo, consulte https://github.com/cloudera/livy#request-body para obter uma lista completa de parâmetros válidos 

```
%%configure -f
{
    to config the session.
    "driverMemory":"2g",
    "driverCores":3,
    "executorMemory":"2g",
    "executorCores":2,
    "jars":["myjar1.jar","myjar.jar"],
    "conf":{
        "spark.driver.maxResultSize":"10g"
    }
}
```
> [!NOTE]
> O comando mágico de configuração de sessão do Spark não tem suporte no pipeline Synapse.
>
>

## <a name="bring-data-to-a-notebook"></a>Trazer dados para um notebook

Você pode carregar dados do Armazenamento de Blobs do Azure, do Azure Data Lake Store Gen 2 e de pool do SQL, como mostram os exemplos de código abaixo.

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>Ler um CSV do Azure Data Lake Store Gen2 como um dataframe do Spark

```python
from pyspark.sql import SparkSession
from pyspark.sql.types import *
account_name = "Your account name"
container_name = "Your container name"
relative_path = "Your path"
adls_path = 'abfss://%s@%s.dfs.core.windows.net/%s' % (container_name, account_name, relative_path)

df1 = spark.read.option('header', 'true') \
                .option('delimiter', ',') \
                .csv(adls_path + '/Testfile.csv')

```

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>Ler um CSV do Armazenamento de Blobs do Azure como um dataframe do Spark

```python

from pyspark.sql import SparkSession

# Azure storage access info
blob_account_name = 'Your account name' # replace with your blob name
blob_container_name = 'Your container name' # replace with your container name
blob_relative_path = 'Your path' # replace with your relative folder path
linked_service_name = 'Your linked service name' # replace with your linked service name

blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linked_service_name)

# Allow SPARK to access from Blob remotely

wasb_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)

spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
print('Remote blob path: ' + wasb_path)

df = spark.read.option("header", "true") \
            .option("delimiter","|") \
            .schema(schema) \
            .csv(wasbs_path)
```

### <a name="read-data-from-the-primary-storage-account"></a>Ler dados da conta de armazenamento primária

Você pode acessar os dados na conta de armazenamento principal diretamente. Não é necessário fornecer as chaves secretas. No Data Explorer, clique com o botão direito do mouse em um arquivo e selecione **Novo notebook** para ver um novo notebook com o extrator de dados gerado automaticamente.

![data-to-cell](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="save-notebooks"></a>Salvar notebooks

Você pode salvar um único notebook ou todos eles em seu espaço de trabalho.

1. Para salvar as alterações feitas em um único notebook, selecione o botão **Publicar** na barra de comandos do notebook.

   ![publish-notebook](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. Para salvar todos os notebooks em seu espaço de trabalho, selecione o botão **Publicar tudo** na barra de comandos do espaço de trabalho. 

   ![publish-all](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

Nas propriedades do notebook, você pode configurar se deseja incluir a saída da célula ao salvar.

   ![notebook-properties](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>Comandos magic
Você pode usar comandos mágicos Jupyter familiares nos notebooks do Azure Synapse Studio. Examine a lista a seguir como os comandos mágicos disponíveis atuais. Conte-nos [seus casos de uso no GitHub](https://github.com/MicrosoftDocs/azure-docs/issues/new) para que possamos continuar a criar mais comandos mágicos para atender às suas necessidades.

> [!NOTE]
> Somente os comandos mágicos a seguir têm suporte no pipeline Synapse:%% pyspark,%% Spark,%% Csharp,%% SQL. 
>
>

# <a name="classical-notebook"></a>[Notebook clássico](#tab/classical)

Magics de linha disponíveis: [%lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [% timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

Mágicas de célula disponíveis: [%% time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%% timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%% Capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%% WriteFile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%% SQL](#use-multiple-languages), [%% pyspark](#use-multiple-languages), [%% Spark](#use-multiple-languages), [%% Csharp](#use-multiple-languages),[%% Configure](#spark-session-config-magic-command)



# <a name="preview-notebook"></a>[Visualizar bloco de notas](#tab/preview)

Mágicas de linha disponíveis: [% lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [% time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [% timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [% history](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-history), [% Execute](#notebook-reference), [% Load](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-load)

Mágicas de célula disponíveis: [%% time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%% timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%% Capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%% WriteFile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%% SQL](#use-multiple-languages), [%% pyspark](#use-multiple-languages), [%% Spark](#use-multiple-languages), [%% Csharp](#use-multiple-languages), [%% HTML](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-html), [%% Configure](#spark-session-config-magic-command)

--- 

## <a name="integrate-a-notebook"></a>Integrar um bloco de anotações

### <a name="add-a-notebook-to-a-pipeline"></a>Adicionar um bloco de anotações a um pipeline

Selecione o botão **Adicionar ao pipeline** no canto superior direito para adicionar um bloco de anotações a um pipeline existente ou criar um novo pipeline.

![Adicionar bloco de anotações ao pipeline](./media/apache-spark-development-using-notebooks/add-to-pipeline.png)

### <a name="designate-a-parameters-cell"></a>Designar uma célula de parâmetros

# <a name="classical-notebook"></a>[Notebook clássico](#tab/classical)

Para parametrizar o bloco de anotações, selecione as reticências (...) para acessar o menu de ações de célula adicional na extrema direita. Em seguida, selecione **alternar célula de parâmetro** para designar a célula como a célula de parâmetros.

![alternar parâmetro](./media/apache-spark-development-using-notebooks/toggle-parameter-cell.png)

# <a name="preview-notebook"></a>[Visualizar bloco de notas](#tab/preview)

Para parametrizar o bloco de anotações, selecione as reticências (...) para acessar **mais comandos** na barra de ferramentas da célula. Em seguida, selecione **alternar célula de parâmetro** para designar a célula como a célula de parâmetros.

![Azure-Notebook-alternância-parâmetro](./media/apache-spark-development-using-notebooks/azure-notebook-toggle-parameter-cell.png)

---

Azure Data Factory procura a célula Parameters e trata essa célula como padrão para os parâmetros passados no momento da execução. O mecanismo de execução adicionará uma nova célula abaixo da célula Parameters com parâmetros de entrada para substituir os valores padrão. Quando uma célula Parameters não for designada, a célula injetada será inserida na parte superior do bloco de anotações.


### <a name="assign-parameters-values-from-a-pipeline"></a>Atribuir valores de parâmetros de um pipeline

Depois de criar um bloco de anotações com parâmetros, você pode executá-lo de um pipeline com a atividade do Azure Synapse notebook. Depois de adicionar a atividade à tela do pipeline, você poderá definir os valores dos parâmetros na seção **parâmetros de base** na guia **configurações** . 

![Atribuir um parâmetro](./media/apache-spark-development-using-notebooks/assign-parameter.png)

Ao atribuir valores de parâmetro, você pode usar a [linguagem de expressão de pipeline](../../data-factory/control-flow-expression-language-functions.md) ou variáveis de [sistema](../../data-factory/control-flow-system-variables.md).



## <a name="shortcut-keys"></a>Teclas de atalho

Semelhantemente aos Jupyter Notebooks, os notebooks do Azure Synapse Studio têm uma interface de usuário modal. O teclado faz coisas diferentes, dependendo de em qual modo a célula do notebook está. Os notebooks do Synapse Studio dão suporte aos seguintes dois modos de uma célula de código: modo de comando e modo de edição.

1. Uma célula está no modo de comando quando não há um cursor de texto solicitando que você digite. Quando uma célula está no modo de comando, você pode editar o notebook como um todo, mas não pode digitar em células individuais. Insira o modo de comando pressionando `ESC` ou usando o mouse para selecionar fora da área do editor de uma célula.

   ![command-mode](./media/apache-spark-development-using-notebooks/synapse-command-mode-2.png)

2. O modo de edição é indicado por um cursor de texto solicitando que você digite na área do editor. Quando uma célula está no modo de edição, você pode digitar na célula. Insira o modo de edição pressionando `Enter` ou usando o mouse para selecionar na área do editor de uma célula.
   
   ![edit-mode](./media/apache-spark-development-using-notebooks/synapse-edit-mode-2.png)

### <a name="shortcut-keys-under-command-mode"></a>Teclas de atalho no modo de comando

# <a name="classical-notebook"></a>[Notebook clássico](#tab/classical)

Usando os atalhos de tecla a seguir, você pode navegar e executar o código com mais facilidade nos notebooks do Azure Synapse.

| Ação |Atalhos do notebook do Synapse Studio  |
|--|--|
|Executar a célula atual e selecionar abaixo | Shift+Enter |
|Executar a célula atual e inserir abaixo | Alt+Enter |
|Selecionar célula acima| Para cima |
|Selecionar célula abaixo| Para baixo |
|Inserir célula acima| Um |
|Inserir célula abaixo| B |
|Estender células selecionadas acima| Shift+Seta para Cima |
|Estender células selecionadas abaixo| Shift+Seta para Baixo|
|Mover a célula para cima| CTRL+ALT+↑ |
|Mover a célula para baixo| CTRL+ALT+↓ |
|Excluir células selecionadas| D, D |
|Mudar para o modo de edição| Digite |

# <a name="preview-notebook"></a>[Visualizar bloco de notas](#tab/preview)

| Ação |Atalhos do notebook do Synapse Studio  |
|--|--|
|Executar a célula atual e selecionar abaixo | Shift+Enter |
|Executar a célula atual e inserir abaixo | Alt+Enter |
|Executar célula atual| Ctrl+Enter |
|Selecionar célula acima| Para cima |
|Selecionar célula abaixo| Para baixo |
|Selecionar célula anterior| K |
|Selecionar próxima célula| J |
|Inserir célula acima| Um |
|Inserir célula abaixo| B |
|Excluir células selecionadas| Shift + D |
|Mudar para o modo de edição| Digite |

---

### <a name="shortcut-keys-under-edit-mode"></a>Teclas de atalho no modo de edição


Usando os atalhos de tecla a seguir, você pode navegar e executar o código com mais facilidade nos notebooks do Azure Synapse quando no modo de edição.

| Ação |Atalhos do notebook do Synapse Studio  |
|--|--|
|Mover cursor para cima | Para cima |
|Mover cursor para baixo|Para baixo|
|Desfazer|CTRL+Z|
|Refaz|Ctrl+Y|
|Inserir/remover comentário|Ctrl+/|
|Excluir palavra anterior|Ctrl+Backspace|
|Excluir palavra seguinte|Ctrl+Delete|
|Ir para o início da célula|Ctrl+Home|
|Ir para o final da célula |Ctrl+End|
|Ir uma palavra para a esquerda|CTRL+Seta para a esquerda|
|Ir uma palavra para a direita|CTRL+Seta para a direita|
|Selecionar tudo|CTRL+A|
|Recuar| CTRL +]|
|Desfazer recuo|Ctrl+[|
|Alternar para o modo de comando| Esc |

---

## <a name="next-steps"></a>Próximas etapas
- [Confira os notebooks de exemplo do Azure Synapse](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [Início Rápido: Criar um Pool do Apache Spark no Azure Synapse Analytics usando ferramentas da Web](../quickstart-apache-spark-notebook.md)
- [O que é Apache Spark no Azure Synapse Analytics](apache-spark-overview.md)
- [Use .NET para Apache Spark com Azure Synapse Analytics](spark-dotnet.md)
- [Documentação do .NET para Apache Spark](/dotnet/spark)
- [Azure Synapse Analytics](../index.yml)