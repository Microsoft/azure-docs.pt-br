---
title: 'Tutorial: Criar uma definição de trabalho do Apache Spark no Synapse Studio'
description: Tutorial – Usar o Azure Synapse Analytics para criar definições de trabalho do Spark e enviá-las a um Apache Spark para um pool do Azure Synapse Analytics.
author: Jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 10/16/2020
ms.openlocfilehash: 15b67c969cb0464256caed58a2e7388eb7a76b9c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608726"
---
# <a name="tutorial-create-apache-spark-job-definition-in-synapse-studio"></a>Tutorial: Criar uma definição de trabalho do Apache Spark no Synapse Studio

Este tutorial demonstra como usar o Azure Synapse Studio para criar definições de trabalho do Apache Spark e depois enviá-las a um pool do Apache Spark sem servidor.

Este tutorial cobre as seguintes tarefas:
> [!div class="checklist"]
>
> - Criar uma definição de trabalho do Apache Spark para PySpark (Python)
> - Criar uma definição de trabalho do Apache Spark para Spark (Scala)
> - Criar uma definição de trabalho do Apache Spark para .NET Spark (C#/F#)
> - Criar uma definição de trabalho importando um arquivo JSON
> - Como exportar um arquivo de definição de trabalho do Apache Spark para o local
> - Enviar uma definição de trabalho do Apache Spark como um trabalho em lotes
> - Adicionar uma definição de trabalho do Apache Spark no pipeline


## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, verifique se você atende aos seguintes requisitos:

* Um workspace do Azure Synapse Analytics. Para obter instruções, confira [Criar um workspace do Azure Synapse Analytics](../../machine-learning/how-to-manage-workspace.md).
* Um Pool do Apache Spark sem servidor.
* Uma conta de armazenamento do ADLS Gen2. Você precisa ser o **Colaborador de Dados do Blob de Armazenamento** do sistema de arquivos ADLS Gen2 com o qual deseja trabalhar. Se não for, você precisará adicionar a permissão manualmente.
* Se você não quiser usar o armazenamento padrão do workspace, vincule a conta de armazenamento do ADLS Gen2 necessária no Synapse Studio. 


## <a name="create-an-apache-spark-job-definition-for-pyspark-python"></a>Criar uma definição de trabalho do Apache Spark para PySpark (Python)

Nesta seção, você cria uma definição de trabalho do Apache Spark para PySpark (Python).

1. Abra o [Azure Synapse Studio](https://web.azuresynapse.net/).

2. Você pode acessar [Arquivos de exemplo para criar definições de trabalho do Apache Spark](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Python) a fim de baixar **arquivos de exemplo para python.zip**, descompactar o pacote compactado e extrair os arquivos **wordcount.py** e **shakespeare.txt**. 

     ![arquivos de exemplo](./media/apache-spark-job-definitions/sample-files.png)

3. Selecione **Dados** -> **Vinculados** -> **Azure Data Lake Storage Gen2** e carregue **wordcount.py** e **shakespeare.txt** em seu sistema de arquivos do ADLS Gen2. 

     ![carregar arquivo do Python](./media/apache-spark-job-definitions/upload-python-file.png)

4. Selecione o hub **Desenvolver**, escolha o ícone " +" e selecione **Definição de trabalho do Spark** para criar essa definição de trabalho. 

     ![criar uma definição para Python](./media/apache-spark-job-definitions/create-new-definition.png)

5. Selecione **PySpark (Python)** na lista suspensa Linguagem na janela principal de definição de trabalho do Apache Spark.

     ![selecionar python](./media/apache-spark-job-definitions/select-python.png)

6. Preencha as informações para a definição de trabalho do Apache Spark. 

     |  Propriedade   | Descrição   |  
     | ----- | ----- |  
     |Nome da definição do trabalho| Insira um nome para a definição de trabalho do Apache Spark. Esse nome pode ser atualizado a qualquer momento até que seja publicado. <br> Exemplo: `job definition sample`|
     |Arquivo de definição principal| O arquivo principal usado para o trabalho. Selecione um arquivo PY do seu armazenamento. Você pode selecionar **Carregar arquivo** para carregar o arquivo em uma conta de armazenamento. <br> Exemplo: `abfss://…/path/to/wordcount.py`|
     |Argumentos de linha de comando| Argumentos opcionais para o trabalho. <br> Exemplo: `abfss://…/path/to/shakespeare.txt` `abfss://…/path/to/result` <br> *Observação: dois argumentos para a definição de trabalho de exemplo são separados por um espaço.*|
     |Arquivos de referência| Arquivos adicionais usados para referência no arquivo de definição principal. Você pode selecionar **Carregar arquivo** para carregar o arquivo em uma conta de armazenamento. |
     |Pool do Spark| O trabalho será enviado para o pool do Apache Spark selecionado.|
     |Versão do Spark| A versão do Apache Spark que o pool do Apache Spark está executando.|
     |Executores| Número de executores a serem fornecidos no pool do Apache Spark especificado para o trabalho.|
     |Tamanho do executor| Número de núcleos e memória a serem usados para os executores fornecidos no pool do Apache Spark especificado para o trabalho.|  
     |Tamanho do driver| Número de núcleos e memória a serem usados para o driver fornecido no pool do Apache Spark especificado para o trabalho.|

     ![Definir o valor da definição de trabalho do Spark para Python](./media/apache-spark-job-definitions/create-py-definition.png)

7. Selecione **Publicar** para salvar a definição de trabalho do Apache Spark.

     ![publicar definição de py](./media/apache-spark-job-definitions/publish-py-definition.png)

## <a name="create-an-apache-spark-job-definition-for-apache-sparkscala"></a>Criar uma definição de trabalho do Apache Spark para o Apache Spark (Scala)

Nesta seção, você cria uma definição de trabalho do Apache Spark para o Apache Spark (Scala).

 1. Abra o [Azure Synapse Studio](https://web.azuresynapse.net/).

 2. Você pode acessar [Arquivos de exemplo para criar definições de trabalho do Apache Spark](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Scala) a fim de baixar **arquivos de exemplo para scala.zip**, descompactar o pacote compactado e extrair os arquivos **wordcount.jar** e **shakespeare.txt**. 
 
     ![arquivos de exemplo para Scala](./media/apache-spark-job-definitions/sample-files-scala.png)

 3. Selecione **Dados** -> **Vinculados** -> **Azure Data Lake Storage Gen2** e carregue **wordcount.jar** e **shakespeare.txt** em seu sistema de arquivos do ADLS Gen2.
 
     ![preparar estrutura do Scala](./media/apache-spark-job-definitions/prepare-scala-structure.png)

 4. Selecione o hub **Desenvolver**, escolha o ícone " +" e selecione **Definição de trabalho do Spark** para criar essa definição de trabalho. (A imagem de exemplo é a mesma da etapa 4 de **Criar uma definição de trabalho do Apache Spark (Python) para PySpark**.)

 5. Selecione **Spark (Scala)** na lista suspensa Linguagem na janela principal de definição de trabalho do Apache Spark.

     ![selecionar Scala](./media/apache-spark-job-definitions/select-scala.png)

 6. Preencha as informações para a definição de trabalho do Apache Spark. Você pode copiar as informações de exemplo.

     |  Propriedade   | Descrição   |  
     | ----- | ----- |  
     |Nome da definição do trabalho| Insira um nome para a definição de trabalho do Apache Spark. Esse nome pode ser atualizado a qualquer momento até que seja publicado. <br> Exemplo: `scala`|
     |Arquivo de definição principal| O arquivo principal usado para o trabalho. Selecione um arquivo JAR do seu armazenamento. Você pode selecionar **Carregar arquivo** para carregar o arquivo em uma conta de armazenamento. <br> Exemplo: `abfss://…/path/to/wordcount.jar`|
     |Nome da classe principal| O identificador totalmente qualificado ou a classe principal que está no arquivo de definição principal. <br> Exemplo: `WordCount`|
     |Argumentos de linha de comando| Argumentos opcionais para o trabalho. <br> Exemplo: `abfss://…/path/to/shakespeare.txt` `abfss://…/path/to/result` <br> *Observação: dois argumentos para a definição de trabalho de exemplo são separados por um espaço.* |
     |Arquivos de referência| Arquivos adicionais usados para referência no arquivo de definição principal. Você pode selecionar **Carregar arquivo** para carregar o arquivo em uma conta de armazenamento.|
     |Pool do Spark| O trabalho será enviado para o pool do Apache Spark selecionado.|
     |Versão do Spark| A versão do Apache Spark que o pool do Apache Spark está executando.|
     |Executores| Número de executores a serem fornecidos no pool do Apache Spark especificado para o trabalho.|  
     |Tamanho do executor| Número de núcleos e memória a serem usados para os executores fornecidos no pool do Apache Spark especificado para o trabalho.|
     |Tamanho do driver| Número de núcleos e memória a serem usados para o driver fornecido no pool do Apache Spark especificado para o trabalho.|

     ![Definir o valor da definição de trabalho do Spark para Scala](./media/apache-spark-job-definitions/create-scala-definition.png)

 7. Selecione **Publicar** para salvar a definição de trabalho do Apache Spark.

      ![publicar definição do Scala](./media/apache-spark-job-definitions/publish-scala-definition.png)

## <a name="create-an-apache-spark-job-definition-for-net-sparkcf"></a>Criar uma definição de trabalho do Apache Spark para .NET Spark (C#/F#)

Nesta seção, você criará uma definição de trabalho do Apache Spark para .NET Spark (C#/F#).
 1. Abra o [Azure Synapse Studio](https://web.azuresynapse.net/).

 2. Você pode acessar [Arquivos de exemplo para criar definições de trabalho do Apache Spark](https://github.com/Azure-Samples/Synapse/tree/master/Spark/DotNET) a fim de baixar **arquivos de exemplo para dotnet.zip**, descompactar o pacote compactado e extrair os arquivos **wordcount.jar** e **shakespeare.txt**. 

     ![exemplo para dotnet](./media/apache-spark-job-definitions/sample-dotnet.png)

 3. Selecione **Dados** -> **Vinculados** -> **Azure Data Lake Storage Gen2** e carregue **wordcount.zip** e **shakespeare.txt** em seu sistema de arquivos do ADLS Gen2.
 
     ![preparar estrutura dotnet](./media/apache-spark-job-definitions/prepare-dotnet-structure.png)

 4. Selecione o hub **Desenvolver**, escolha o ícone " +" e selecione **Definição de trabalho do Spark** para criar essa definição de trabalho. (A imagem de exemplo é a mesma da etapa 4 de **Criar uma definição de trabalho do Apache Spark (Python) para PySpark**.)

 5. Selecione **.NET Spark(C#/F#)** na lista suspensa Linguagem na janela principal de definição de trabalho do Apache Spark.

     ![selecionar dotnet](./media/apache-spark-job-definitions/select-dotnet.png)

 6. Preencha as informações para a definição de trabalho do Apache Spark. Você pode copiar as informações de exemplo.
    
     |  Propriedade   | Descrição   |  
     | ----- | ----- |  
     |Nome da definição do trabalho| Insira um nome para a definição de trabalho do Apache Spark. Esse nome pode ser atualizado a qualquer momento até que seja publicado. <br> Exemplo: `dotnet`|
     |Arquivo de definição principal| O arquivo principal usado para o trabalho. Selecione um arquivo zip que contenha seu aplicativo .NET para Apache Spark (ou seja, o arquivo executável principal, DLLs que contenham funções definidas pelo usuário e outros arquivos necessários) do seu armazenamento. Você pode selecionar **Carregar arquivo** para carregar o arquivo em uma conta de armazenamento. <br> Exemplo: `abfss://…/path/to/wordcount.zip`|
     |Arquivo executável principal| O arquivo executável principal no arquivo zip de definição principal. <br> Exemplo: `WordCount`|
     |Argumentos de linha de comando| Argumentos opcionais para o trabalho. <br> Exemplo: `abfss://…/path/to/shakespeare.txt` `abfss://…/path/to/result` <br> *Observação: dois argumentos para a definição de trabalho de exemplo são separados por um espaço.* |
     |Arquivos de referência| Arquivos adicionais requeridos pelos nós de trabalho para execução do aplicativo .NET para Apache Spark que não estão incluídos no arquivo ZIP de definição principal (ou seja, jars dependentes, DLLs de funções definidas pelo usuário adicionais e outros arquivos de configuração). Você pode selecionar **Carregar arquivo** para carregar o arquivo em uma conta de armazenamento.|
     |Pool do Spark| O trabalho será enviado para o pool do Apache Spark selecionado.|
     |Versão do Spark| A versão do Apache Spark que o pool do Apache Spark está executando.|
     |Executores| Número de executores a serem fornecidos no pool do Apache Spark especificado para o trabalho.|  
     |Tamanho do executor| Número de núcleos e memória a serem usados para os executores fornecidos no pool do Apache Spark especificado para o trabalho.|
     |Tamanho do driver| Número de núcleos e memória a serem usados para o driver fornecido no pool do Apache Spark especificado para o trabalho.|

     ![Definir o valor da definição de trabalho do Spark para dotnet](./media/apache-spark-job-definitions/create-dotnet-definition.png)

 7. Selecione **Publicar** para salvar a definição de trabalho do Apache Spark.

      ![publicar definição de dotnet](./media/apache-spark-job-definitions/publish-dotnet-definition.png)

## <a name="create-apache-spark-job-definition-by-importing-a-json-file"></a>Criar uma definição de trabalho do Apache Spark importando um arquivo JSON

 Importe um arquivo JSON local existente para o workspace do Azure Synapse no menu **Ações** (...) do Explorador de definições de trabalho do Apache Spark para criar uma definição de trabalho do Apache Spark.

 ![criar uma definição de importação](./media/apache-spark-job-definitions/create-import-definition.png)

 
 A definição de trabalho do Spark é totalmente compatível com a API do Livy. Você pode adicionar mais parâmetros a outras propriedades do Livy [(Documentação do Livy – API REST – apache.org)](https://livy.incubator.apache.org/docs/latest/rest-api.html) no arquivo JSON local. Especifique também os parâmetros relacionados à configuração do Spark na propriedade config, conforme mostrado abaixo. Em seguida, importe o arquivo JSON novamente para criar uma definição de trabalho do Apache Spark para o trabalho em lotes. Exemplo de JSON para importação de definição do Spark:
 
```Scala
   {
  "targetBigDataPool": {
    "referenceName": "socdemolarge",
    "type": "BigDataPoolReference"
  },
  "requiredSparkVersion": "2.3",
  "language": "scala",
  "jobProperties": {
    "name": "robinSparkDefinitiontest",
    "file": "adl://socdemo-c14.azuredatalakestore.net/users/robinyao/wordcount.jar",
    "className": "WordCount",
    "args": [
      "adl://socdemo-c14.azuredatalakestore.net/users/robinyao/shakespeare.txt"
    ],
    "jars": [],
    "files": [],
    "conf": {
      "spark.dynamicAllocation.enabled": "false",
      "spark.dynamicAllocation.minExecutors": "2",
      "spark.dynamicAllocation.maxExecutors": "2"
    },
    "numExecutors": 2,
    "executorCores": 8,
    "executorMemory": "24g",
    "driverCores": 8,
    "driverMemory": "24g"
  }
}

```

![outras propriedades do Livy](./media/apache-spark-job-definitions/other-livy-properties.png)

## <a name="export-an-existing-apache-spark-job-definition-file"></a>Exportar um arquivo de definição de trabalho do Apache Spark existente

 Exporte os arquivos de definição de trabalho do Apache Spark existentes para o local no menu **Ações** (...) do Explorador de Arquivos. Você pode atualizar o arquivo JSON ainda mais em relação a propriedades adicionais do Livy e importá-lo novamente para criar uma definição de trabalho, se necessário.

 ![criar uma definição de exportação](./media/apache-spark-job-definitions/create-export-definition.png)

 ![criar uma definição de exportação 2](./media/apache-spark-job-definitions/create-export-definition-2.png)

## <a name="submit-an-apache-spark-job-definition-as-a-batch-job"></a>Enviar uma definição de trabalho do Apache Spark como um trabalho em lotes

Depois de criar uma definição de trabalho do Apache Spark, você pode enviá-la para um pool do Apache Spark. Verifique se você é o **Colaborador dos Dados do Blob de Armazenamento** do sistema de arquivos ADLS Gen2 com o qual deseja trabalhar. Se não for, você precisará adicionar a permissão manualmente.

### <a name="scenario-1-submit-apache-spark-job-definition"></a>Cenário 1: Enviar uma definição de trabalho do Apache Spark
 1. Abra uma janela de definição de trabalho do Apache Spark selecionando-a.

      ![Abrir a definição de trabalho do Spark a ser enviada ](./media/apache-spark-job-definitions/open-spark-definition.png)

 2. Selecione o botão **Enviar** para enviar o projeto para o Pool do Apache Spark selecionado. Selecione a guia **URL de monitoramento do Spark** para ver o LogQuery do aplicativo Apache Spark.

    ![Selecione o botão enviar para enviar a definição de trabalho do Spark](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![A caixa de diálogo Envio do Spark](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-apache-spark-job-running-progress"></a>Cenário 2: Exibir o progresso da execução do trabalho do Apache Spark

 1. Selecione **Monitorar** e escolha a opção **Aplicativos Apache Spark**. Você pode encontrar o aplicativo Apache Spark enviado.

     ![Exibir aplicativo Spark](./media/apache-spark-job-definitions/view-spark-application.png)

 2. Selecione um aplicativo Apache Spark e a janela de trabalho **SparkJobDefinition** será exibida. Você pode exibir o andamento da execução do trabalho aqui.
     
     ![Exibir LogQuery do aplicativo Spark](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>Cenário 3: verificar arquivo de saída

 1. Selecione **Dados** -> **Vinculados** -> **Azure Data Lake Storage Gen2** (hozhaobdbj) e abra a pasta **resultado** criada anteriormente. Você pode acessar essa pasta e verificar se a saída foi gerada.

     ![Exibir arquivo de saída](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="add-an-apache-spark-job-definition-into-pipeline"></a>Adicionar uma definição de trabalho do Apache Spark no pipeline

Nesta seção, você adiciona uma definição de trabalho do Apache Spark no pipeline.

 1. Abra uma definição de trabalho existente do Apache Spark.

 2. Selecione o ícone no canto superior direito da definição de trabalho do Apache Spark e escolha **Pipeline existente** ou **Novo pipeline**. Consulte a página do Pipeline para obter mais informações.

     ![adicionar ao pipeline1](./media/apache-spark-job-definitions/add-to-pipeline01.png)

     ![adicionar ao pipeline2](./media/apache-spark-job-definitions/add-to-pipeline02.png)


## <a name="next-steps"></a>Próximas etapas

Em seguida, você pode usar o Azure Synapse Studio para criar conjuntos de dados do Power BI e gerenciar dados do Power BI. Avance para o artigo [Vincular um workspace do Power BI a um workspace do Azure Synapse](../quickstart-power-bi.md) para saber mais. 

