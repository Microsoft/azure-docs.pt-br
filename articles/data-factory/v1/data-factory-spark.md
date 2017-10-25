---
title: Invocar programas Spark do Azure Data Factory | Microsoft Docs
description: Aprenda a invocar programas Spark de uma Azure Data Factory usando a atividade MapReduce.
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: fd98931c-cab5-4d66-97cb-4c947861255c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: 3690b5f62d8384d255d420946f6ac1cfd47b9317
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="invoke-spark-programs-from-azure-data-factory-pipelines"></a>Invocar programas Spark dos pipelines do Azure Data Factory

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Atividade de Hive](data-factory-hive-activity.md)
> * [Atividade Pig](data-factory-pig-activity.md)
> * [Atividade MapReduce](data-factory-map-reduce.md)
> * [Atividade de Transmissão do Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Atividade do Spark](data-factory-spark.md)
> * [Atividade de Execução em Lote do Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Atividade do Recurso de Atualização do Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md)
> * [Atividade do U-SQL do Data Lake Analytics](data-factory-usql-activity.md)
> * [Atividade Personalizada do .NET](data-factory-use-custom-activities.md)

## <a name="introduction"></a>Introdução
A atividade do Spark é uma das [atividades de transformação de dados](data-factory-data-transformation-activities.md) com suporte pelo Azure Data Factory. Esta atividade executa o programa do Spark especificado no cluster do Apache Spark no Azure HDInsight.    

> [!IMPORTANT]
> - A atividade do Spark não oferece suporte a clusters HDInsight Spark que usam um Azure Data Lake Store como armazenamento primário.
> - A atividade do Spark dá suporte apenas a clusters HDInsight Spark existentes (seus próprios). Ele não dá suporte a serviço vinculado ao HDInsight sob demanda.

## <a name="walkthrough-create-a-pipeline-with-spark-activity"></a>Passo a passo: criar um pipeline com atividade do Spark
Aqui estão as etapas típicas para criar um pipeline do Data Factory com uma Atividade do Spark.  

1. Criar uma fábrica de dados.
2. Crie um serviço vinculado ao Armazenamento do Azure para vincular seu armazenamento do Azure que está associado ao cluster HDInsight Spark para o data factory.     
2. Crie um serviço vinculado ao Azure HDInsight para vincular o cluster do Apache Spark no Azure HDInsight ao data factory.
3. Crie um conjunto de dados que se refere ao serviço vinculado do Armazenamento do Azure. No momento, você deve especificar um conjunto de dados de saída para uma atividade mesmo que não exista nenhuma saída sendo produzida.  
4. Crie um pipeline com atividade do Spark que se refere ao serviço vinculado ao HDInsight criado na etapa 2. A atividade está configurada com o conjunto de dados que você criou na etapa anterior como um conjunto de dados de saída. O conjunto de dados de saída é o que conduz a agenda (por hora, diariamente, etc.). Portanto, você deve especificar o conjunto de dados de saída mesmo que a atividade não produza efetivamente uma saída.

### <a name="prerequisites"></a>Pré-requisitos
1. Criar uma **conta do Armazenamento do Azure para fins gerais** seguindo as instruções no passo a passo: [Criar uma conta de armazenamento](../../storage/common/storage-create-storage-account.md#create-a-storage-account).  
2. Crie um **cluster do Apache Spark no Azure HDInsight** seguindo as instruções no tutorial: [Criar cluster do Apache Spark no Azure HDInsight](../../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md). Associe a conta de armazenamento do Azure que você criou na etapa 1 a este cluster.  
3. Baixe e leia o arquivo de script de python **test.py** localizado em: [https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py](https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py).  
3.  Carregue **test.py** para a pasta **pyFiles** no contêiner **adfspark** em seu armazenamento de Blobs do Azure. Crie o contêiner e a pasta se eles não existirem.

### <a name="create-data-factory"></a>Criar um data factory
Vamos começar com a criação do data factory nesta etapa.

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. Clique em **NOVO** no menu à esquerda, clique em **Dados + Analytics** e clique em **Data Factory**.
3. Na folha **Novo data factory**, insira **SparkDF** como o Nome.

   > [!IMPORTANT]
   > O nome do Azure Data Factory deve ser **globalmente exclusivo**. Se você vir o erro: **O nome do data factory "SparkDF" não está disponível**. Altere o nome do data factory (por exemplo, seunomeSparkDF) e tente criar novamente. Consulte o tópico [Data Factory - regras de nomenclatura](data-factory-naming-rules.md) para ver as regras de nomenclatura para artefatos de Data Factory.   
4. Escolha a **assinatura do Azure** onde você deseja que o data factory seja criado.
5. Selecione um **grupo de recursos** existente ou crie um grupo de recursos do Azure.
6. Selecione a opção **Fixar no painel**.  
6. Clique em **Criar** na folha **Novo data factory**.

   > [!IMPORTANT]
   > Para criar instâncias de Data Factory, você deve ser um membro da função [Colaborador de Data Factory](../../active-directory/role-based-access-built-in-roles.md#data-factory-contributor) no nível de assinatura/grupo de recursos.
7. Você verá o data factory que está sendo criado no **painel** do portal do Azure da seguinte maneira:   
8. Após o data factory ter sido criado com êxito, você verá a página do data factory, que exibe seu conteúdo. Se você não vir a página do data factory, clique no bloco da sua data factory no painel.

    ![Folha Data Factory](./media/data-factory-spark/data-factory-blade.png)

### <a name="create-linked-services"></a>Criar serviços vinculados
Nesta etapa, você deve criar dois serviços vinculados, um para vincular seu cluster do Spark a sua data factory e outro para vincular seu armazenamento do Azure a sua data factory.  

#### <a name="create-azure-storage-linked-service"></a>Criar o serviço vinculado do armazenamento do Azure
Nesta etapa, você vincula a conta do Armazenamento do Azure ao data factory. Um conjunto de dados que você cria em uma etapa posterior neste passo a passo refere-se a esse serviço vinculado. O serviço vinculado HDInsight que você define na próxima etapa também se refere a esse serviço vinculado.  

1. Clique em **Criar e implantar** na folha **Data Factory** para seu data factory. Você deverá ver o Data Factory Editor.
2. Clique em **Novo repositório de dados** e escolha **Armazenamento do Azure**.

   ![Novo armazenamento de dados - Armazenamento do Azure - menu](./media/data-factory-spark/new-data-store-azure-storage-menu.png)
3. Você deve ver o **script JSON** para criar um serviço de armazenamento vinculado do Azure no editor.

   ![Serviço vinculado de armazenamento do Azure](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)
4. Substitua **accountname** e **accountkey** pelo nome e pela chave de acesso da sua conta de armazenamento do Azure. Para saber como conseguir sua chave de acesso de armazenamento, consulte as informações sobre como exibir, copiar e regenerar chaves de acesso de armazenamento em [Gerenciar sua conta de armazenamento](../../storage/common/storage-create-storage-account.md#manage-your-storage-account).
5. Clique em **Implantar** na barra de comandos para implantar o serviço vinculado. Depois que o serviço vinculado for implantado com êxito, a janela **Rascunho-1** desaparecerá e você verá **AzureStorageLinkedService** no modo de exibição de árvore à esquerda.

#### <a name="create-hdinsight-linked-service"></a>Criar o serviço vinculado ao HDInsight
Nesta etapa, você cria um serviço vinculado ao Azure HDInsight para vincular seu cluster do HDInsight Spark ao data factory. O cluster do HDInsight é usado para executar o programa especificado do Spark na atividade do Spark neste exemplo.  

1. Clique em **... Mais** na barra de ferramentas, clique em **Nova computação** e, em seguida, clique em **Cluster do HDInsight**.

    ![Criar o serviço vinculado ao HDInsight](media/data-factory-spark/new-hdinsight-linked-service.png)
2. Copie e cole o trecho a seguir na janela de **Rascunho-1** . No Editor JSON, siga estas etapas:
    1. Especifique o **URI** para o cluster do HDInsight Spark. Por exemplo: `https://<sparkclustername>.azurehdinsight.net/`.
    2. Especifique o nome do **usuário** que tem acesso ao cluster do Spark.
    3. Especifique a **senha** para o usuário.
    4. Especifique o **serviço vinculado ao Armazenamento do Azure** que está associado ao cluster do HDInsight Spark. Neste exemplo, é: **AzureStorageLinkedService**.

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
    > - A atividade do Spark não oferece suporte a clusters HDInsight Spark que usam um Azure Data Lake Store como armazenamento primário.
    > - A atividade do Spark dá suporte apenas ao cluster HDInsight Spark existente (seu próprio). Ele não dá suporte a serviço vinculado ao HDInsight sob demanda.

    Consulte [Serviço vinculado ao HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) para obter detalhes sobre o serviço vinculado ao HDInsight.
3.  Clique em **Implantar** na barra de comandos para implantar o serviço vinculado.  

### <a name="create-output-dataset"></a>Criar conjunto de dados de saída
O conjunto de dados de saída é o que conduz a agenda (por hora, diariamente, etc.). Portanto, você deve especificar um conjunto de dados de saída para a atividade do Spark embora a atividade não produza efetivamente uma saída. Especificar um conjunto de dados de entrada para a atividade é opcional.

1. No **Editor de Data Factory**, clique em **... Mais** na barra de comandos, clique em **Novo conjunto de dados** e selecione **Armazenamento de Blobs do Azure**.  
2. Copie e cole o trecho a seguir na janela de Rascunho-1. O trecho JSON define um conjunto de dados chamado **OutputDataset**. Além disso, você especifica que os resultados estão armazenados no contêiner de blobs denominado **adfspark** e na pasta denominada **pyFiles/output**. Como mencionado anteriormente, esse conjunto de dados é fictício. O programa Spark neste exemplo não produz saída. A seção **availability** especifica que o conjunto de dados de saída é produzido diariamente.  

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
3. Clique em **Implantar** na barra de comandos para implantar o conjunto de dados.


### <a name="create-pipeline"></a>Criar um pipeline
Nesta etapa, você cria um pipeline com a atividade **HDInsightSpark**. Atualmente, o conjunto de dados de saída é o que aciona a agenda, então você deve criar um conjunto de dados de saída, mesmo que a atividade não produza qualquer saída. Se a atividade não receber entradas, ignore a criação de conjunto de dados de entrada. Portanto, nenhum conjunto de dados de entrada é especificado neste exemplo.

1. No **Editor de Data Factory**, clique em **... Mais** na barra de comandos e, em seguida, clique em **Novo pipeline**.
2. Substitua o script na janela de Rascunho-1 pelo seguinte script:

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
    - A propriedade **type** é definida como **HDInsightSpark**.
    - O **rootPath** é definido como **adfspark\\pyFiles**, onde adfspark é o contêiner de Blob do Azure e pyFiles é a pasta de arquivos nesse contêiner. Neste exemplo, o Armazenamento de Blobs do Azure é aquele que está associado ao cluster Spark. Você pode carregar o arquivo em um Armazenamento do Azure diferente. Se você fizer isso, crie um serviço vinculado do Armazenamento do Azure para vincular essa conta de armazenamento ao data factory. Em seguida, especifique o nome do serviço vinculado como um valor para a propriedade **sparkJobLinkedService**. Consulte [Propriedades de Atividade Spark](#spark-activity-properties) para obter detalhes sobre essa propriedade e outras propriedades às quais a atividade Spark dá suporte.  
    - O **entryFilePath** é definido como **test.py**, que é o arquivo Python.
    - A propriedade **getDebugInfo** é definida como **Always**, o que significa que os arquivos de log são gerados sempre (êxito ou falha).

        > [!IMPORTANT]
        > É recomendável que você não defina essa propriedade como `Always` em um ambiente de produção a menos que você esteja solucionando um problema.
    - A seção de **saídas** tem um conjunto de dados de saída. Você deve especificar um conjunto de dados de saída mesmo que o programa spark não produza nenhuma saída. O conjunto de dados de saída conduz a agenda para o pipeline (por hora, diariamente, etc.).  

        Para obter detalhes sobre as propriedades com suporte pela atividade do Spark, veja a seção [Propriedades da atividade do Spark](#spark-activity-properties).
3. Para implantar o pipeline, clique em **Implantar** na barra de comando.

### <a name="monitor-pipeline"></a>Monitorar o pipeline
1. Clique em **X** para fechar as folhas do Editor da Fábrica de Dados e para navegar de volta à página inicial da Fábrica de Dados. Clique em **Monitorar e Gerenciar** para iniciar o aplicativo de monitoramento em outra guia.

    ![Bloco Monitorar e Gerenciar](media/data-factory-spark/monitor-and-manage-tile.png)
2. Altere o filtro **Hora de início** na parte superior para **1/2/2017** e clique em **Aplicar**.
3. Você verá apenas uma janela de atividade porque há apenas um dia entre a hora de início (2017-02-01) e a final (2017-02-02) do pipeline. Confirme se a fatia de dados está no estado **Pronto**.

    ![Monitorar o Pipeline](media/data-factory-spark/monitor-and-manage-app.png)    
4. Selecione a **janela de atividade** para ver detalhes sobre a execução da atividade. Se houver um erro, você verá detalhes sobre ele no painel à direita.

### <a name="verify-the-results"></a>Verifique os resultados

1. Inicie o **Notebook Jupyter** para seu cluster do HDInsight Spark, navegando até: https://NOMEDOCLUSTER.azurehdinsight.net/jupyter. Você pode também iniciar o painel do cluster para o cluster do HDInsight Spark e, em seguida, iniciar **Notebook Jupyter**.
2. Clique em **Novo** -> **PySpark** para iniciar um novo notebook.

    ![Novo notebook Jupyter](media/data-factory-spark/jupyter-new-book.png)
3. Execute o seguinte comando copiando/colando o texto e pressionando **SHIFT + ENTER** no final da segunda instrução.  

    ```sql
    %%sql

    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
4. Confirme que você vê os dados da tabela de hvac:  

    ![Resultados da consulta do Jupyter](media/data-factory-spark/jupyter-notebook-results.png)

<!-- Removed bookmark #run-a-hive-query-using-spark-sql since it doesn't exist in the target article -->
Veja a seção [Executar uma consulta SQL do Spark](../../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md) para obter instruções detalhadas. 

### <a name="troubleshooting"></a>Solucionar problemas
Como você define **getDebugInfo** para **Sempre**, verá uma subpasta **log** na pasta **pyFiles** em seu contêiner de Blob do Azure. O arquivo de log na pasta de log fornece detalhes adicionais. Esse arquivo de log é especialmente útil quando há um erro. Em um ambiente de produção, convém defini-lo como **Falha**.

Para solução de problemas adicionais, siga as etapas a seguir:


1. Navegue até `https://<CLUSTERNAME>.azurehdinsight.net/yarnui/hn/cluster`.

    ![Aplicativo de interface do usuário do YARN](media/data-factory-spark/yarnui-application.png)  
2. Clique em **Logs** para uma das tentativas de execução.

    ![Página do aplicativo](media/data-factory-spark/yarn-applications.png)
3. Você deve ver as informações de erro adicionais na página de logs.

    ![Log de erros](media/data-factory-spark/yarnui-application-error.png)

As seções a seguir fornecem informações sobre entidades de Data Factory para usar o cluster do Apache Spark e atividade do Spark na data factory.

## <a name="spark-activity-properties"></a>Propriedades da Atividade do Spark
Esta é a definição JSON de exemplo de um pipeline com atividade do Spark:    

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
                    }
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

A tabela a seguir descreve as propriedades JSON usadas na definição de JSON:

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| name | Nome da atividade no pipeline. | Sim |
| description | Texto que descreve o que a atividade faz. | Não |
| type | Essa propriedade deve ser definida como HDInsightSpark. | Sim |
| linkedServiceName | Nome do serviço vinculado do HDInsight no qual o programa Spark é executado. | Sim |
| rootPath | O contêiner de Blob do Azure e a pasta que contém o arquivo Spark. O nome do arquivo diferencia maiúsculas de minúsculas. | Sim |
| entryFilePath | Caminho relativo à pasta raiz do código/pacote Spark. | Sim |
| className | Classe principal de Java/Spark do aplicativo | Não |
| argumentos | Uma lista de argumentos de linha de comando para o programa Spark. | Não |
| proxyUser | A conta de usuário a ser representada para execução do programa Spark | Não |
| sparkConfig | Especifique valores para propriedades de configuração do Spark listadas no tópico: [Configuração do Spark – Propriedades de aplicativo](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Não |
| getDebugInfo | Especifica quando os arquivos de log do Spark são copiados no armazenamento do Azure usado pelo cluster HDInsight (ou) especificado por sparkJobLinkedService. Valores permitidos: Nenhum, Sempre ou Falha. Valor padrão: Nenhum. | Não |
| sparkJobLinkedService | O serviço vinculado ao Armazenamento do Azure que contém o arquivo de trabalho, dependências e os logs do Spark.  Se você não especificar um valor para essa propriedade, o armazenamento associado ao cluster HDInsight será usado. | Não |

## <a name="folder-structure"></a>Estrutura de pastas
A atividade do Spark não oferece suporte a um script em linha, como o fazem as atividades do Pig e do Hive. Os trabalhos do Spark também são mais extensíveis do que trabalhos do Pig/Hive. Para trabalhos do Spark, você pode fornecer várias dependências, como pacotes jar (colocados no CLASSPATH de java), arquivos do python (colocados no PYTHONPATH) e outros arquivos.

Crie a seguinte estrutura de pastas no armazenamento de Blobs do Azure referenciado pelo serviço vinculado ao HDInsight. Depois, carregue os arquivos dependentes nas subpastas apropriadas na pasta raiz, representada por **entryFilePath**. Por exemplo, carregue arquivos do python na subpasta pyFiles e os arquivos jar na subpasta jars da pasta raiz. Em tempo de execução, o serviço Data Factory espera a seguinte estrutura de pastas no Armazenamento de Blobs do Azure:     

| Caminho | Descrição | Obrigatório | Tipo |
| ---- | ----------- | -------- | ---- |
| . | O caminho raiz do trabalho do Spark no serviço vinculado ao armazenamento  | Sim | Pasta |
| &lt;definido pelo usuário&gt; | O caminho que aponta para o arquivo de entrada do trabalho do Spark | Sim | Arquivo |
| ./jars | Todos os arquivos nessa pasta são carregados e colocados no classpath de java do cluster | Não | Pasta |
| ./pyFiles | Todos os arquivos nessa pasta são carregados e colocados no PYTHONPATH do cluster | Não | Pasta |
| ./files | Todos os arquivos nessa pasta são carregados e colocados no diretório de trabalho executor | Não | Pasta |
| ./archives | Todos os arquivos nessa pasta são descompactados | Não | Pasta |
| ./logs | A pasta onde os logs do cluster Spark são armazenados.| Não | Pasta |

Veja um exemplo de um armazenamento que contém dois arquivos de trabalho do Spark no Armazenamento de Blobs do Azure referenciado pelo serviço vinculado ao HDInsight.

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
