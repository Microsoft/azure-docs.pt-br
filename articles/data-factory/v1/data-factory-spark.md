---
title: Invocar programas Spark do Azure Data Factory
description: Saiba como invocar programas Spark em um Azure Data Factory usando a atividade MapReduce.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 644a0d645abb186731ddb1f408cd36e5b9ba3c3f
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104780014"
---
# <a name="invoke-spark-programs-from-azure-data-factory-pipelines"></a>Invocar programas Spark dos pipelines do Azure Data Factory

> [!div class="op_single_selector" title1="Atividades de transformação"]
> * [Hive activity](data-factory-hive-activity.md) (Atividade do Hive)
> * [Pig activity](data-factory-pig-activity.md) (Atividade do Pig)
> * [MapReduce activity](data-factory-map-reduce.md) (Atividade do MapReduce)
> * [Hadoop Streaming activity](data-factory-hadoop-streaming-activity.md) (Atividade de streaming do Hadoop)
> * [Spark activity](data-factory-spark.md) (Atividade do Spark)
> * [Atividade de execução de lote Azure Machine Learning Studio (clássica)](data-factory-azure-ml-batch-execution-activity.md)
> * [Atividade de recurso de atualização Azure Machine Learning Studio (clássica)](data-factory-azure-ml-update-resource-activity.md)
> * [Stored procedure activity](data-factory-stored-proc-activity.md) (Atividade de procedimento armazenado)
> * [Data Lake Analytics atividade U-SQL](data-factory-usql-activity.md)
> * [Atividade personalizada do .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Este artigo se aplica à versão 1 do Azure Data Factory, que geralmente está disponível. Se você estiver usando a versão atual do serviço Data Factory, consulte [ transformar dados usando a atividade Apache Spark no Data Factory](../transform-data-using-spark.md).

## <a name="introduction"></a>Introdução
A atividade do Spark é uma das [atividades de transformação de dados](data-factory-data-transformation-activities.md) compatíveis com o Data Factory. Essa atividade executa o programa do Spark especificado no cluster Spark no Azure HDInsight.

> [!IMPORTANT]
> - A atividade do Spark não dá suporte a clusters HDInsight Spark que usam o Azure Data Lake Store como armazenamento primário.
> - A atividade do Spark é compatível apenas com clusters HDInsight Spark existentes (seus próprios). Ele não dá suporte a um serviço vinculado ao HDInsight sob demanda.

## <a name="walkthrough-create-a-pipeline-with-a-spark-activity"></a>Passo a passo: Criar um pipeline com uma atividade do Spark
Estas são as etapas típicas para criar um pipeline do data factory com uma atividade do Spark:

* Criar um data factory.
* Crie um serviço vinculado do Armazenamento do Azure para vincular o armazenamento associado ao cluster HDInsight Spark ao data factory.
* Crie um serviço vinculado do HDInsight para vincular o cluster Spark no HDInsight ao data factory.
* Crie um conjunto de dados que se refere ao serviço vinculado do Armazenamento. No momento, você deve especificar um conjunto de dados de saída para uma atividade mesmo que não exista nenhuma saída sendo produzida.
* Crie um pipeline com a atividade do Spark que se refere ao serviço vinculado do HDInsight criado. A atividade está configurada com o conjunto de dados que você criou na etapa anterior como um conjunto de dados de saída. O conjunto de dados de saída é o que aciona o agendamento (por hora, diariamente). Portanto, é necessário especificar o conjunto de dados de saída mesmo que a atividade não produza efetivamente uma saída.

### <a name="prerequisites"></a>Pré-requisitos
1. Crie uma conta de armazenamento de uso geral seguindo as instruções descritas em [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md).

1. Crie um cluster Spark no HDInsight seguindo as instruções descritas no tutorial [Criar um cluster Spark no HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Associe a conta de armazenamento criada na etapa 1 a esse cluster.

1. Baixe e revise o arquivo de script Python **test.py** localizado em [https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py](https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py).

1. Carregue **test.py** para a pasta **pyFiles** no contêiner **adfspark** em seu armazenamento de blobs. Crie o contêiner e a pasta, caso eles não existam.

### <a name="create-a-data-factory"></a>Criar um data factory
Para criar um data factory, siga estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Selecione **Novo** > **Dados + Análise** > **Data Factory**.

1. Na folha **Novo data factory**, em **Nome**, insira **SparkDF**.

   > [!IMPORTANT]
   > O nome da data factory do Azure deve ser globalmente exclusivo. Se você receber o erro “O nome do data factory SparkDF não está disponível”, altere o nome do data factory. Por exemplo, use yournameSparkDFdate e crie o data factory novamente. Para saber mais sobre as regras de nomenclatura, consulte [Data Factory: Regras de nomenclatura](data-factory-naming-rules.md).

1. Em **Assinatura**, escolha a assinatura do Azure onde você deseja que o data factory seja criado.

1. Selecione um grupo de recursos existente ou crie um grupo de recursos do Azure.

1. Marque a caixa de seleção **Fixar no painel**.

1. Selecione **Criar**.

   > [!IMPORTANT]
   > Para criar instâncias do Data Factory, você deve ser um membro da função [Colaborador do Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) no nível de assinatura/grupo de recursos.

1. Você vê o data factory sendo criado no painel do portal do Azure.

1. Após o data factory ter sido criado, a página do **Data factory** será exibida com seu conteúdo. Se a página **Data factory** não for exibida, selecione o bloco do data factory no painel.

    ![Folha Data Factory](./media/data-factory-spark/data-factory-blade.png)

### <a name="create-linked-services"></a>Criar serviços vinculados
Nesta etapa, você cria dois serviços vinculados. Um serviço vincula o cluster Spark ao data factory e o outro serviço vincula o armazenamento ao data factory.

#### <a name="create-a-storage-linked-service"></a>Criar um serviço vinculado do Armazenamento
Nesta etapa, você vincula sua conta de armazenamento ao data factory. Um conjunto de dados que você cria em uma etapa posterior neste passo a passo refere-se a esse serviço vinculado. O serviço vinculado HDInsight que você define na próxima etapa também se refere a esse serviço vinculado.

1. Na folha **Data factory**, selecione **Criar e implantar**. O Editor do Data Factory é exibido.

1. Clique em **Novo armazenamento de dados** e escolha **Armazenamento do Azure**.

   ![Novo armazenamento de dados](./media/data-factory-spark/new-data-store-azure-storage-menu.png)

1. O script JSON usado para criar um serviço vinculado do Armazenamento é exibido no editor.

   ![AzureStorageLinkedService](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

1. Substitua **nome da conta** e **chave de conta** pelo nome e pela chave de acesso de sua conta de armazenamento. Para saber como obter sua chave de acesso de armazenamento, confira [Gerenciar as chaves de acesso da conta de armazenamento](../../storage/common/storage-account-keys-manage.md).

1. Para implantar o serviço vinculado, selecione **Implantar** na barra de comandos. Depois que o serviço vinculado é implantado com êxito, a janela Rascunho 1 desaparece. Você verá **AzureStorageLinkedService** no modo de exibição de árvore à esquerda.

#### <a name="create-an-hdinsight-linked-service"></a>Como criar um serviço vinculado do HDInsight
Nesta etapa, você cria um serviço vinculado do HDInsight para vincular o cluster HDInsight Spark ao data factory. O cluster do HDInsight é usado para executar o programa especificado do Spark na atividade do Spark neste exemplo.

1. No editor de data Factory, selecione **mais**  >  **novo computação**  >  **cluster HDInsight**.

    ![Criar o serviço vinculado ao HDInsight](media/data-factory-spark/new-hdinsight-linked-service.png)

1. Copie e cole o snippet a seguir na janela de Rascunho-1. No editor de JSON, execute as seguintes etapas:

    1. Especifique o URI do cluster HDInsight Spark. Por exemplo: `https://<sparkclustername>.azurehdinsight.net/`.

    1. Especifique o nome do usuário que tem acesso ao cluster Spark.

    1. Especifique a senha para o usuário.

    1. Especifique o serviço vinculado do Armazenamento associado ao cluster HDInsight Spark. Neste exemplo, ele é AzureStorageLinkedService.

    ```json
    {
        "name": "HDInsightLinkedService",
        "properties": {
            "type": "HDInsight",
            "typeProperties": {
                "clusterUri": "https://<sparkclustername>.azurehdinsight.net/",
                "userName": "admin",
                "password": "**********",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }
    ```

    > [!IMPORTANT]
    > - A atividade do Spark não dá suporte a clusters HDInsight Spark que usam o Azure Data Lake Store como armazenamento primário.
    > - A atividade do Spark é compatível apenas com clusters HDInsight Spark existentes (seus próprios). Ele não dá suporte a um serviço vinculado ao HDInsight sob demanda.

    Para obter mais informações sobre o serviço vinculado do HDInsight, consulte [Serviço vinculado do HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).

1. Para implantar o serviço vinculado, selecione **Implantar** na barra de comandos.

### <a name="create-the-output-dataset"></a>Como criar um conjunto de dados de saída
O conjunto de dados de saída é o que aciona o agendamento (por hora, diariamente). Portanto, é necessário especificar um conjunto de dados de saída para a atividade do Spark no pipeline, embora a atividade não produza nenhuma saída. Especificar um conjunto de dados de entrada para a atividade é opcional.

1. No Data Factory Editor, selecione **Mais** > **Novo conjunto de dados** > **Armazenamento de Blobs do Azure**.

1. Copie e cole o snippet a seguir na janela de Rascunho-1. O snippet JSON define um conjunto de dados chamado **OutputDataset**. Além disso, você especifica que os resultados estão armazenados no contêiner de blobs denominado **adfspark** e na pasta denominada **pyFiles/output**. Conforme mencionado anteriormente, esse conjunto de dados é fictício. O programa do Spark neste exemplo não produz nenhuma saída. A seção **availability** especifica que o conjunto de dados de saída é produzido diariamente.

    ```json
    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "sparkoutput.txt",
                "folderPath": "adfspark/pyFiles/output",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": "\t"
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }
    ```
1. Para implantar o conjunto de dados, selecione **Implantar** na barra de comandos.


### <a name="create-a-pipeline"></a>Criar um pipeline
Nesta etapa, você cria um pipeline com a atividade HDInsightSpark. Atualmente, o conjunto de dados de saída é o que aciona a agenda. Assim, você deve criar um conjunto de dados de saída, mesmo que a atividade não produza qualquer saída. Se a atividade não receber entradas, ignore a criação de conjunto de dados de entrada. Portanto, nenhum conjunto de dados de entrada é especificado neste exemplo.

1. No Data Factory Editor, selecione **Mais** > **Novo pipeline**.

1. Substitua o script na janela de Rascunho-1 pelo seguinte script:

    ```json
    {
        "name": "SparkPipeline",
        "properties": {
            "activities": [
                {
                    "type": "HDInsightSpark",
                    "typeProperties": {
                        "rootPath": "adfspark\\pyFiles",
                        "entryFilePath": "test.py",
                        "getDebugInfo": "Always"
                    },
                    "outputs": [
                        {
                            "name": "OutputDataset"
                        }
                    ],
                    "name": "MySparkActivity",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2017-02-05T00:00:00Z",
            "end": "2017-02-06T00:00:00Z"
        }
    }
    ```

    Observe os seguintes pontos:

    1. A propriedade **Type** é definida como **HDInsightSpark**.

    1. A propriedade **rootPath** é definida como **adfspark\\pyFiles**, em que adfspark é o contêiner de blobs e pyFiles é a pasta de arquivos nesse contêiner. Neste exemplo, o armazenamento de blobs é aquele que está associado ao cluster Spark. Carregue o arquivo em outra conta de armazenamento. Se você fizer isso, crie um serviço vinculado do Armazenamento para vincular essa conta de armazenamento ao data factory. Em seguida, especifique o nome do serviço vinculado como um valor para a propriedade **sparkJobLinkedService** . Para obter mais informações sobre essa propriedade e outras propriedades compatíveis com a atividade do Spark, consulte [Propriedades da atividade do Spark](#spark-activity-properties).

    1. A propriedade **entryFilePath** é definida como **test.py**, que é o arquivo Python.

    1. A propriedade **getDebugInfo** é definida como **Always**, o que significa que os arquivos de log sempre são gerados (êxito ou falha).

       > [!IMPORTANT]
       > Recomendamos não definir essa propriedade como `Always` em um ambiente de produção, a menos que você esteja solucionando um problema.

    1. A seção de **saídas** tem um conjunto de uma saída. É necessário especificar um conjunto de dados de saída, mesmo que o programa do Spark não produza nenhuma saída. O conjunto de dados de saída aciona o agendamento para o pipeline (por hora, diariamente, etc.).

    Para obter mais informações sobre as propriedades compatíveis com a atividade do Spark, consulte a seção [Propriedades da atividade do Spark](#spark-activity-properties).

1. Para implantar o pipeline, selecione **Implantar** na barra de comandos.

### <a name="monitor-a-pipeline"></a>Como monitorar um pipeline
1. Na folha **Data factory**, selecione **Monitorar e gerenciar** para iniciar o aplicativo de monitoramento em outra guia.

    ![Bloco Monitorar e Gerenciar](media/data-factory-spark/monitor-and-manage-tile.png)

1. Altere o filtro **Hora de início** na parte superior para **1/2/2017** e selecione **Aplicar**.

1. Somente uma janela de atividade é exibida porque há apenas um dia entre as horas de início (2017-02-01) e de término (2017-02-02) do pipeline. Confirme se a fatia de dados está no estado **Pronto**.

    ![Monitorar o Pipeline](media/data-factory-spark/monitor-and-manage-app.png)

1. Na lista **Janelas de atividades**, selecione uma execução de atividade para ver detalhes sobre ela. Se houver um erro, você verá detalhes sobre ele no painel à direita.

### <a name="verify-the-results"></a>Verifique os resultados

1. Inicie o Jupyter Notebook para o cluster HDInsight Spark acessando `https://CLUSTERNAME.azurehdinsight.net/jupyter` . Também abra um painel do cluster para o cluster HDInsight Spark e, em seguida, inicie o Jupyter Notebook.

1. Selecione **novo**  >  **PySpark** para iniciar um novo bloco de anotações.

    ![Novo notebook Jupyter](media/data-factory-spark/jupyter-new-book.png)

1. Execute o seguinte comando copiando e colando o texto e pressionando Shift + Enter no final da segunda instrução:

    ```sql
    %%sql

    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
1. Confirme se os dados da tabela de HVAC são exibidos.

    ![Resultados da consulta do Jupyter](media/data-factory-spark/jupyter-notebook-results.png)

<!-- Removed bookmark #run-a-hive-query-using-spark-sql since it doesn't exist in the target article -->
Para obter instruções detalhadas, consulte a seção [Executar uma consulta SQL do Spark](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md).

### <a name="troubleshooting"></a>Solução de problemas
Como getDebugInfo é definido como **Sempre**, você vê uma subpasta log na pasta pyFiles no contêiner de blob. O arquivo de log na pasta de log fornece informações adicionais. Esse arquivo de log é especialmente útil quando há um erro. Em um ambiente de produção, recomendamos defini-lo como **Falha**.

Para obter uma solução de problemas adicional, execute as seguintes etapas:


1. Ir para `https://<CLUSTERNAME>.azurehdinsight.net/yarnui/hn/cluster`.

    ![Aplicativo de interface do usuário do YARN](media/data-factory-spark/yarnui-application.png)

1. Selecione **Logs** para uma das tentativas de execução.

    ![Página do aplicativo](media/data-factory-spark/yarn-applications.png)

1. As seguintes informações de erro adicionais são exibidas na página de log:

    ![Log de erros](media/data-factory-spark/yarnui-application-error.png)

As seções a seguir fornecem informações sobre as entidades do data factory para usar o cluster Spark e a atividade do Spark no data factory.

## <a name="spark-activity-properties"></a>Propriedades da Atividade do Spark
Esta é a definição JSON de exemplo de um pipeline com uma atividade do Spark:

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "arguments": [ "arg1", "arg2" ],
                    "sparkConfig": {
                        "spark.python.worker.memory": "512m"
                    },
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "description": "This activity invokes the Spark program",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-01T00:00:00Z",
        "end": "2017-02-02T00:00:00Z"
    }
}
```

A tabela a seguir descreve as propriedades JSON usadas na definição de JSON.

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| name | Nome da atividade no pipeline. | Sim |
| descrição | Texto que descreve o que a atividade faz. | Não |
| type | Essa propriedade deve ser definida como HDInsightSpark. | Sim |
| linkedServiceName | Nome do serviço vinculado do HDInsight no qual o programa Spark é executado. | Sim |
| rootPath | O contêiner de blobs e a pasta que contém o arquivo do Spark. O nome do arquivo diferencia maiúsculas de minúsculas. | Sim |
| entryFilePath | Caminho relativo à pasta raiz do código/pacote Spark. | Sim |
| className | Classe principal de Java/Spark do aplicativo. | Não |
| argumentos | Uma lista de argumentos de linha de comando para o programa Spark. | Não |
| proxyUser | A conta de usuário a ser representada para execução do programa do Spark. | Não |
| sparkConfig | Especifique valores para as propriedades de configuração do Spark listadas em [Configuração do Spark: propriedades do aplicativo](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Não |
| getDebugInfo | Especifica quando os arquivos de log do Spark são copiados para o armazenamento usado pelo cluster HDInsight (ou) especificado por sparkJobLinkedService. Os valores permitidos são Nenhum, Sempre ou Falha. O valor padrão é Nenhum. | Não |
| sparkJobLinkedService | O serviço vinculado do Armazenamento que contém o arquivo de trabalho, as dependências e os logs do Spark. Se você não especificar um valor para essa propriedade, o armazenamento associado ao cluster HDInsight será usado. | Não |

## <a name="folder-structure"></a>Estrutura de pastas
A atividade do Spark não é compatível com um script embutido, como as atividades do Pig e do Hive. Os trabalhos do Spark também são mais extensíveis do que trabalhos do Pig/Hive. Para trabalhos do Spark, você pode fornecer várias dependências, como pacotes jar (colocados em CLASSPATH do Java), arquivos do Python (colocados em PYTHONPATH) e outros arquivos.

Crie a estrutura de pastas a seguir no armazenamento de blobs referenciado pelo serviço vinculado do HDInsight. Em seguida, carregue os arquivos dependentes nas subpastas apropriadas na pasta raiz representada por **entryFilePath**. Por exemplo, carregue arquivos do Python na subpasta pyFiles e arquivos jar na subpasta jars da pasta raiz. Em runtime, o serviço Data Factory espera a seguinte estrutura de pastas no armazenamento de blobs:

| Caminho | Descrição | Obrigatório | Type |
| ---- | ----------- | -------- | ---- |
| . | O caminho raiz do trabalho do Spark no serviço vinculado do armazenamento. | Sim | Pasta |
| &lt;definido pelo usuário&gt; | O caminho que aponta para o arquivo de entrada do trabalho do Spark. | Sim | Arquivo |
| ./jars | Todos os arquivos nessa pasta são carregados e colocados no caminho de classe do Java do cluster. | Não | Pasta |
| ./pyFiles | Todos os arquivos nessa pasta são carregados e colocados no PYTHONPATH do cluster. | Não | Pasta |
| ./files | Todos os arquivos nessa pasta são carregados e colocados no diretório de trabalho executor. | Não | Pasta |
| ./archives | Todos os arquivos nessa pasta são descompactados. | Não | Pasta |
| ./logs | A pasta onde os logs do cluster Spark são armazenados.| Não | Pasta |

Este é um exemplo de um armazenamento que contém dois arquivos de trabalho do Spark no armazenamento de blobs referenciado pelo serviço vinculado do HDInsight:

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs
```
