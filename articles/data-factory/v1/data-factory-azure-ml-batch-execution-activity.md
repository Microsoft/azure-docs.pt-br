---
title: Criar pipelines de dados de previsão usando Azure Data Factory
description: Descreve como criar pipelines preditivas usando Azure Data Factory e Azure Machine Learning Studio (clássico)
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: c04c94ef2a73085b982fde3efefecea351b083af
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97608061"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-studio-classic-and-azure-data-factory"></a>Criar pipelines preditivas usando Azure Machine Learning Studio (clássico) e Azure Data Factory

> [!div class="op_single_selector" title1="Atividades de transformação"]
> * [Atividade do hive](data-factory-hive-activity.md)
> * [Atividade Pig](data-factory-pig-activity.md)
> * [Atividade MapReduce](data-factory-map-reduce.md)
> * [Atividade de streaming do Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Atividade do Spark](data-factory-spark.md)
> * [Atividade de execução em lotes do Azure Machine Learning Studio (clássico)](data-factory-azure-ml-batch-execution-activity.md)
> * [Atividade do recurso de atualização do Azure Machine Learning Studio (clássico)](data-factory-azure-ml-update-resource-activity.md)
> * [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md)
> * [Atividade do U-SQL da Análise Data Lake](data-factory-usql-activity.md)
> * [Atividade personalizada do .NET](data-factory-use-custom-activities.md)

## <a name="introduction"></a>Introdução
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [transformar dados usando aprendizado de máquina no Data Factory](../transform-data-using-machine-learning.md).

### <a name="azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio (clássico)
[Azure Machine Learning Studio (clássico)](https://azure.microsoft.com/documentation/services/machine-learning/) permite que você crie, teste e implante soluções de análise preditiva. De um ponto de vista de alto nível, isso é feito em três etapas:

1. **Crie um teste de treinamento**. Você faz essa etapa usando Azure Machine Learning Studio (clássico). O estúdio (clássico) é um ambiente de desenvolvimento Visual colaborativo que você usa para treinar e testar um modelo de análise preditiva usando dados de treinamento.
2. **Convertê-lo em um teste preditivo**. Quando o modelo foi treinado com dados existentes e você estiver pronto para usá-lo para pontuar novos dados, você preparará e simplificará seu teste para a pontuação.
3. **Implantá-lo como um serviço da Web**. Você pode publicar seu experimento de pontuação como um serviço Web do Azure. Você pode enviar dados ao seu modelo por desse ponto de extremidade de serviço Web e receber previsões de resultado do modelo.

### <a name="azure-data-factory"></a>Fábrica de dados do Azure
Data Factory é um serviço de integração de dados baseado em nuvem que orquestra e automatiza a **movimentação** e a **transformação** de dados. Você pode criar soluções de integração de dados usando o Azure Data Factory que podem ingerir dados de vários repositórios de dados, transformar/processar os dados e publicar os dados resultantes nos repositórios de dados.

O serviço Data Factory permite criar pipelines de dados que movem e transformam dados e depois executar os pipelines em um agendamento especificado (por hora, diariamente, semanalmente, etc.). Ele também fornece visualizações avançadas para exibir a linhagem e as dependências entre os pipelines de dados e monitorar todos os seus pipelines de dados em uma única exibição unificada, a fim de identificar os problemas e configurar alertas de monitoramento facilmente.

Consulte os artigos [Introdução ao Azure Data Factory](data-factory-introduction.md) e [Criar seu primeiro pipeline](data-factory-build-your-first-pipeline.md) para começar a introdução ao serviço do Azure Data Factory.

### <a name="data-factory-and-machine-learning-studio-classic-together"></a>Data Factory e Machine Learning Studio (clássico) juntos
Azure Data Factory permite que você crie facilmente pipelines que usam um serviço Web publicado [Azure Machine Learning Studio (clássico)][azure-machine-learning] para análise preditiva. Usando a **atividade de execução em lote** em um pipeline Azure data Factory, você pode invocar um serviço Web Studio (clássico) para fazer previsões sobre os dados no lote. Consulte invocando um serviço Web Azure Machine Learning Studio (clássico) usando a seção atividade de execução em lote para obter detalhes.

Ao longo do tempo, os modelos de previsão nos experimentos de Pontuação do estúdio (clássico) precisam ser retreinados usando novos conjuntos de dados de entrada. Você pode treinar novamente um modelo de estúdio (clássico) de um pipeline Data Factory realizando as seguintes etapas:

1. Publique o experimento de treinamento (e não um experimento preditivo) como um serviço Web. Você faz essa etapa no estúdio (clássico) como fez para expor o experimento de previsão como um serviço Web no cenário anterior.
2. Use a atividade de execução em lote do Studio (clássico) para invocar o serviço Web para o teste de treinamento. Basicamente, você pode usar a atividade de execução em lote do Studio (clássico) para invocar o serviço Web de treinamento e o serviço Web de pontuação.

Depois de fazer o novo treinamento, atualize o serviço Web de Pontuação (teste de previsão exposto como um serviço Web) com o modelo treinado recentemente usando a **atividade de recurso de atualização Azure Machine Learning Studio (clássica)**. Veja o artigo [Atualização de modelos usando a Atividade do Recurso de Atualização](data-factory-azure-ml-update-resource-activity.md) para obter detalhes.

## <a name="invoking-a-web-service-using-batch-execution-activity"></a>Invocar um serviço Web usando a Atividade de Execução em Lote
Você usa Azure Data Factory para orquestrar a movimentação e o processamento de dados e, em seguida, executar a execução em lotes usando o Studio (clássico). Estas são as etapas de nível superior:

1. Crie um serviço vinculado Azure Machine Learning Studio (clássico). Você precisará dos seguintes valores:

   1. **URI de Solicitação** para a API de Execução do Lote. Encontre o URI da Solicitação clicando no link **EXECUÇÃO EM LOTE** na página de serviços Web.
   2. **Chave de API** para o serviço Web do Studio (clássico) publicado. Encontre a chave de API clicando no serviço Web que você publicou.
   3. Use a atividade **AzureMLBatchExecution** .

      ![Painel Machine Learning Studio (clássico)](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

      ![URI do lote](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)

### <a name="scenario-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Cenário: Experimentos usando entradas/saídas de serviço Web que se referem aos dados no armazenamento de Blob do Azure
Nesse cenário, o serviço Web Studio (clássico) faz previsões usando dados de um arquivo em um armazenamento de BLOBs do Azure e armazena os resultados da previsão no armazenamento de BLOBs. O JSON a seguir define um pipeline do Data Factory com uma atividade AzureMLBatchExecution. A atividade contém o conjunto de dados **DecisionTreeInputBlob** como entrada e **DecisionTreeResultBlob** como a saída. O **DecisionTreeInputBlob** é passado como uma entrada para o serviço Web usando a propriedade JSON **webServiceInput**. O **DecisionTreeResultBlob** é passado como uma saída para o serviço Web usando a propriedade JSON **webServiceOutputs**.

> [!IMPORTANT]
> Se o serviço Web receber várias entradas, use a propriedade **webServiceInputs** em vez de usar **webServiceInput**. Veja a seção [Serviço Web exige várias entradas](#web-service-requires-multiple-inputs) para obter um exemplo de como usar a propriedade webServiceInputs.
>
> Os conjuntos de resultados que são referenciados pelas propriedades **webServiceInput** / **webServiceInputs** e **webServiceOutputs** (em **typeproperties**) também devem ser incluídos nas **entradas** e **saídas** da atividade.
>
> No experimento do estúdio (clássico), as portas de entrada e saída do serviço Web e os parâmetros globais têm nomes padrão ("entrada1", "entrada2") que você pode personalizar. Os nomes que você usa para as configurações webServiceInputs, webserviceoutputs e globalParameters devem corresponder exatamente aos nomes nos testes. Você pode exibir o conteúdo de solicitação de exemplo na página de ajuda de execução do lote para seu ponto de extremidade do estúdio (clássico) para verificar o mapeamento esperado.
>
>

```json
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchExecution",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "DecisionTreeInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "DecisionTreeResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "typeProperties":
        {
            "webServiceInput": "DecisionTreeInputBlob",
            "webServiceOutputs": {
                "output1": "DecisionTreeResultBlob"
            }
        },
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

> [!NOTE]
> Apenas as entradas e saídas da atividade AzureMLBatchExecution podem ser passadas como parâmetros para o serviço Web. Por exemplo, no snippet JSON acima, DecisionTreeInputBlob é uma entrada para a atividade de AzureMLBatchExecution, que é passada como entrada para o serviço Web a través do parâmetro webServiceInput.
>
>

### <a name="example"></a>Exemplo
Este exemplo usa o armazenamento do Azure para conter tanto os dados de entrada quanto os de saída.

Recomendamos que você percorra o tutorial [Compilar seu primeiro pipeline com o Data Factory][adf-build-1st-pipeline] antes de usar este exemplo. Use o Editor do Data Factory para criar artefatos do Data Factory (serviços vinculados, conjuntos de dados, pipeline) neste exemplo.

1. Criar um **serviço vinculado** para o **Armazenamento do Azure**. Se os arquivos de entrada e saída estiverem em contas de armazenamento diferentes, você precisará de dois serviços vinculados. Aqui está um exemplo JSON:

   ```json
   {
     "name": "StorageLinkedService",
     "properties": {
       "type": "AzureStorage",
       "typeProperties": {
         "connectionString": "DefaultEndpointsProtocol=https;AccountName= [acctName];AccountKey=[acctKey]"
       }
     }
   }
   ```

2. Criar a **entrada** do **conjunto de dados** do Azure Data Factory. Ao contrário de alguns outros conjuntos de dados do Data Factory, esses conjuntos de dados devem conter os valores **folderPath** e **fileName**. Você pode usar o particionamento para fazer com que cada execução em lote (cada fatia de dados) processe ou produza arquivos de entrada e saída exclusiva. Você precisará incluir algumas atividades upstream para transformar a entrada no formato de arquivo CSV e colocá-lo na conta de armazenamento para cada fatia. Nesse caso, não inclua as configurações **external** e **externalData** mostradas no exemplo a seguir e seu DecisionTreeInputBlob seria o conjunto de dados de saída de uma Atividade diferente.

   ```json
   {
     "name": "DecisionTreeInputBlob",
     "properties": {
       "type": "AzureBlob",
       "linkedServiceName": "StorageLinkedService",
       "typeProperties": {
         "folderPath": "azuremltesting/input",
         "fileName": "in.csv",
         "format": {
           "type": "TextFormat",
           "columnDelimiter": ","
         }
       },
       "external": true,
       "availability": {
         "frequency": "Day",
         "interval": 1
       },
       "policy": {
         "externalData": {
           "retryInterval": "00:01:00",
           "retryTimeout": "00:10:00",
           "maximumRetry": 3
         }
       }
     }
   }
   ```

   O arquivo csv de entrada deve ter a linha de cabeçalho de coluna. Se você estiver usando a **Atividade de cópia** para criar/mover o csv para dentro do armazenamento de blobs, você deve definir a propriedade de coleta **blobWriterAddHeade** r como **true**. Por exemplo:

   ```json
   sink:
   {
     "type": "BlobSink",
     "blobWriterAddHeader": true
     }
   ```

   Se o arquivo csv não tem a linha de cabeçalho, você poderá ver o seguinte erro: **Erro na atividade: erro ao ler a cadeia de caracteres. Token inesperado: StartObject. Caminho '', linha 1, posição 1**.

3. Criar a **saída** do **conjunto de dados** da Azure Data Factory. Este exemplo usa o particionamento para criar um caminho de saída exclusivo para cada execução de divisão. Sem o particionamento, a atividade substituiria o arquivo.

   ```json
   {
     "name": "DecisionTreeResultBlob",
     "properties": {
       "type": "AzureBlob",
       "linkedServiceName": "StorageLinkedService",
       "typeProperties": {
         "folderPath": "azuremltesting/scored/{folderpart}/",
         "fileName": "{filepart}result.csv",
         "partitionedBy": [
           {
             "name": "folderpart",
             "value": {
               "type": "DateTime",
               "date": "SliceStart",
               "format": "yyyyMMdd"
             }
           },
           {
             "name": "filepart",
             "value": {
               "type": "DateTime",
               "date": "SliceStart",
               "format": "HHmmss"
             }
           }
         ],
         "format": {
           "type": "TextFormat",
           "columnDelimiter": ","
         }
       },
       "availability": {
         "frequency": "Day",
         "interval": 15
       }
     }
   }
   ```

4. Crie um **serviço vinculado** do tipo **AzureMLLinkedService**, fornecendo a chave de API e a URL de execução de lote do modelo.

   ```json
   {
     "name": "MyAzureMLLinkedService",
     "properties": {
       "type": "AzureML",
       "typeProperties": {
         "mlEndpoint": "https://[batch execution endpoint]/jobs",
         "apiKey": "[apikey]"
       }
     }
   }
   ```

5. Por fim, crie um pipeline que contenha uma atividade **AzureMLBatchScoring** . Em runtime, o pipeline executa as seguintes etapas:

   1. Obtém o local do arquivo de entrada de seus conjuntos de dados de entrada.
   2. Invoca a API de execução em lote do Studio (clássico)
   3. Copia a saída da execução em lote no blob especificado no conjunto de dados de saída.

      > [!NOTE]
      > A atividade AzureMLBatchExecution pode ter zero ou mais entradas e uma ou mais saídas.
      >
      >

      ```json
      {
        "name": "PredictivePipeline",
        "properties": {
          "description": "use AzureML model",
          "activities": [
            {
              "name": "MLActivity",
              "type": "AzureMLBatchExecution",
              "description": "prediction analysis on batch input",
              "inputs": [
                {
                  "name": "DecisionTreeInputBlob"
                }
                ],
              "outputs": [
                {
                  "name": "DecisionTreeResultBlob"
                }
                ],
              "linkedServiceName": "MyAzureMLLinkedService",
              "typeProperties":
                {
                "webServiceInput": "DecisionTreeInputBlob",
                "webServiceOutputs": {
                  "output1": "DecisionTreeResultBlob"
                }
                },
              "policy": {
                "concurrency": 3,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "02:00:00"
              }
            }
          ],
          "start": "2016-02-13T00:00:00Z",
          "end": "2016-02-14T00:00:00Z"
        }
      }
      ```

      Os DateTimes de **início** e **término** devem estar no [formato ISO](https://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. O tempo **final** é opcional. Se você não especificar o valor para a propriedade **end** , ele será calculado como "**Start + 48 hours".** Para executar o pipeline indefinidamente, especifique **9999-09-09** como o valor para a propriedade **end**. Consulte a [Referência de script JSON](/previous-versions/azure/dn835050(v=azure.100)) para obter detalhes sobre as propriedades JSON.

      > [!NOTE]
      > A especificação de entrada para a atividade AzureMLBatchExecution é opcional.
      >
      >

### <a name="scenario-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Cenário: Experimentos usando módulos Leitor/Gravador para fazer referência a dados em vários armazenamentos
Outro cenário comum ao criar experimentos de estúdio (clássico) é usar módulos de leitor e gravador. O módulo leitor é usado para carregar dados em um experimento e o módulo gravador é usado para salvar os dados dos experimentos. Para obter detalhes sobre os módulos de leitor e gravador, consulte os tópicos [Leitor](/azure/machine-learning/studio-module-reference/import-data) e [Gravador](/azure/machine-learning/studio-module-reference/export-data) na biblioteca MSDN.

Ao usar os módulos leitor e gravador, é recomendável usar um parâmetro de serviço Web para cada propriedade desses módulos leitor/gravador. Esses parâmetros da Web permitem que você configure os valores durante o runtime. Por exemplo, você poderia criar um experimento com um módulo leitor que usa um banco de dados SQL do Azure: XXX.database.windows.net. Depois que o serviço Web tiver sido implantado, você deverá habilitar os consumidores do serviço Web para especificar outro SQL Server lógico chamado YYY.database.windows.net. Você pode usar um parâmetro de serviço Web para permitir que esse valor seja configurado.

> [!NOTE]
> A saída e entrada de serviço Web são diferentes dos parâmetros de serviço Web. No primeiro cenário, você viu como uma entrada e saída podem ser especificadas para um serviço Web de estúdio (clássico). Nesse cenário, você pode passar parâmetros para um serviço Web que correspondem às propriedades dos módulos de leitor/gravador.
>
>

Vejamos um cenário para o uso de parâmetros de serviço Web. Você tem um serviço Web do Studio (clássico) implantado que usa um módulo leitor para ler dados de uma das fontes de dados com suporte do Studio (clássico) (por exemplo: banco de dado SQL do Azure). Após a execução do lote, os resultados são gravados usando um módulo Gravador (banco de dados SQL do Azure).  Não há entradas e saídas de serviço Web definidas nos experimentos. Nesse caso, recomendamos que você configure os parâmetros de serviço Web relevantes para os módulos de leitor e gravador. Essa configuração permite que os módulos de leitor/gravador sejam configurados ao usar a atividade AzureMLBatchExecution. Você especifica parâmetros de serviço Web na seção **globalParameters** no JSON da atividade da seguinte maneira:

```json
"typeProperties": {
    "globalParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```

Você também pode usar [Funções do Data Factory](data-factory-functions-variables.md) para passar valores para os parâmetros do serviço Web conforme mostrado no exemplo a seguir:

```json
"typeProperties": {
    "globalParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Os parâmetros de serviço Web diferenciam maiúsculas de minúsculas, portanto, garanta que os nomes que você especificar na atividade de JSON correspondam aos expostos pelo serviço Web.
>
>

### <a name="using-a-reader-module-to-read-data-from-multiple-files-in-azure-blob"></a>Uso de um módulo Leitor para ler dados de vários arquivos no Blob do Azure
Pipelines de Big Data com atividades como Pig e Hive podem produzir um ou mais arquivos de saída sem extensão. Por exemplo, quando você especifica uma tabela externa do Hive, os dados para a tabela externa do Hive podem ser armazenados no armazenamento de blob do Azure com o seguinte nome 000000_0. É possível usar o módulo leitor em um experimento para ler vários arquivos e usá-los para previsões.

Ao usar o módulo leitor em um experimento de estúdio (clássico), você pode especificar o blob do Azure como uma entrada. Os arquivos no Armazenamento de Blobs do Azure podem ser os arquivos de saída (Exemplo: 000000_0) que são produzidos por um script de Pig e Hive em execução no HDInsight. O módulo de leitor permite que você leia arquivos (sem extensões), configurando o **Caminho para o contêiner, blob/diretório**. O **Caminho para o contêiner** aponta para o contêiner e o **diretório/blob** aponta para a pasta que contém os arquivos, conforme mostra a imagem a seguir. O asterisco, \*), **especifica que todos os arquivos no contêiner/pasta (ou seja, data/aggregateddata/year=2014/month-6/\*)** são lidos como parte do teste.

![Propriedades de Blob do Azure](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)

### <a name="example"></a>Exemplo
#### <a name="pipeline-with-azuremlbatchexecution-activity-with-web-service-parameters"></a>Pipeline com a atividade AzureMLBatchExecution com parâmetros de serviço Web

```JSON
{
  "name": "MLWithSqlReaderSqlWriter",
  "properties": {
    "description": "Azure Machine Learning Studio (classic) model with sql azure reader/writer",
    "activities": [
      {
        "name": "MLSqlReaderSqlWriterActivity",
        "type": "AzureMLBatchExecution",
        "description": "test",
        "inputs": [
          {
            "name": "MLSqlInput"
          }
        ],
        "outputs": [
          {
            "name": "MLSqlOutput"
          }
        ],
        "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
        "typeProperties":
        {
            "webServiceInput": "MLSqlInput",
            "webServiceOutputs": {
                "output1": "MLSqlOutput"
            }
              "globalParameters": {
                "Database server name": "<myserver>.database.windows.net",
                "Database name": "<database>",
                "Server user account name": "<user name>",
                "Server user account password": "<password>"
              }
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        },
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

No exemplo JSON acima:

* O serviço Web do Studio (clássico) implantado usa um módulo leitor e gravador para ler/gravar dados de/para um banco de dado SQL do Azure. Este serviço Web expõe os seguintes quatro parâmetros: Nome do servidor de banco de dados, Nome do banco de dados, Nome de conta de usuário do servidor e Senha de conta de usuário do servidor.
* Os DateTimes de **início** e **término** devem estar no [formato ISO](https://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. O tempo **final** é opcional. Se você não especificar o valor para a propriedade **end** , ele será calculado como "**Start + 48 hours".** Para executar o pipeline indefinidamente, especifique **9999-09-09** como o valor para a propriedade **end**. Consulte a [Referência de script JSON](/previous-versions/azure/dn835050(v=azure.100)) para obter detalhes sobre as propriedades JSON.

### <a name="other-scenarios"></a>Outros cenários
#### <a name="web-service-requires-multiple-inputs"></a>Serviço Web exige várias entradas
Se o serviço Web receber várias entradas, use a propriedade **webServiceInputs** em vez de usar **webServiceInput**. Os conjuntos de dados referenciados por **webServiceInputs** também devem ser incluídos nas **entradas** da Atividade.

No experimento Azure Machine Learning Studio (clássico), as portas de entrada e saída do serviço Web e os parâmetros globais têm nomes padrão ("entrada1", "entrada2") que você pode personalizar. Os nomes que você usa para as configurações webServiceInputs, webserviceoutputs e globalParameters devem corresponder exatamente aos nomes nos testes. Você pode exibir o conteúdo de solicitação de exemplo na página de ajuda de execução do lote para seu ponto de extremidade do estúdio (clássico) para verificar o mapeamento esperado.

```JSON
{
    "name": "PredictivePipeline",
    "properties": {
        "description": "use AzureML model",
        "activities": [{
            "name": "MLActivity",
            "type": "AzureMLBatchExecution",
            "description": "prediction analysis on batch input",
            "inputs": [{
                "name": "inputDataset1"
            }, {
                "name": "inputDataset2"
            }],
            "outputs": [{
                "name": "outputDataset"
            }],
            "linkedServiceName": "MyAzureMLLinkedService",
            "typeProperties": {
                "webServiceInputs": {
                    "input1": "inputDataset1",
                    "input2": "inputDataset2"
                },
                "webServiceOutputs": {
                    "output1": "outputDataset"
                }
            },
            "policy": {
                "concurrency": 3,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "02:00:00"
            }
        }],
        "start": "2016-02-13T00:00:00Z",
        "end": "2016-02-14T00:00:00Z"
    }
}
```

#### <a name="web-service-does-not-require-an-input"></a>O serviço Web não requer uma entrada
Os serviços Web de execução em lote Azure Machine Learning Studio (clássico) podem ser usados para executar qualquer fluxo de trabalho, por exemplo, scripts R ou Python, que podem não exigir entradas. Ou o teste pode ser configurado com um módulo Leitor que não expõe GlobalParameters. Nesse caso, a atividade AzureMLBatchExecution seria configurada da seguinte maneira:

```JSON
{
    "name": "scoring service",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "myBlob"
        }
    ],
    "typeProperties": {
        "webServiceOutputs": {
            "output1": "myBlob"
        }
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-does-not-require-an-inputoutput"></a>O serviço Web não requer uma entrada/saída
O serviço Web de execução em lote Azure Machine Learning Studio (clássico) pode não ter nenhuma saída de serviço Web configurada. Neste exemplo, não há nenhum serviço Web de entrada ou saída, nem GlobalParameters configurados. Ainda há uma saída configurada na própria atividade, mas ela não é fornecida como um webServiceOutput.

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "placeholderOutputDataset"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-uses-readers-and-writers-and-the-activity-runs-only-when-other-activities-have-succeeded"></a>O serviço Web usa leitores e gravadores, e a atividade será executada somente quando outras atividades tiverem êxito.
Os módulos leitor e gravador do serviço Web Azure Machine Learning Studio (clássico) podem ser configurados para serem executados com ou sem Globalparameters. No entanto, convém inserir chamadas de serviço em um pipeline que usa as dependências do conjunto de dados para chamar o serviço apenas quando algum processamento upstream for concluído. Você também pode disparar alguma outra ação após a execução em lote usando esta abordagem. Nesse caso, você pode expressar as dependências usando as entradas e saídas de atividade sem nomeá-las como entradas ou saídas do serviço Web.

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "inputs": [
        {
            "name": "upstreamData1"
        },
        {
            "name": "upstreamData2"
        }
    ],
    "outputs": [
        {
            "name": "downstreamData"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

Os **aprendizados** são:

* Se o ponto de extremidade do teste usar um webServiceInput:, ele será representado por um conjunto de dados de blob e será incluído nas entradas da atividade e na propriedade webServiceInput. Caso contrário, a propriedade webServiceInput é omitida.
* Se o ponto de extremidade do teste usar um webServiceOutput(s):, ele será representado por um conjunto de dados de blob e será incluído nas saídas da atividade e na propriedade webServiceOutputs. A saída da atividade e webServiceOutputs são mapeados de acordo com o nome de cada saída no teste. Caso contrário, a propriedade webServiceOutputs é omitida.
* Se o ponto de extremidade do teste expor globalParameter(s), eles serão fornecidos na propriedade globalParameters da atividade como pares de valor e chave. Caso contrário, a propriedade globalParameters é omitida. As chaves diferenciam maiúsculas de minúsculas. [funções do Azure Data Factory](data-factory-functions-variables.md) podem ser usadas nos valores.
* Podem ser incluídos nas propriedades de entrada e saída da atividade conjuntos de dados adicionais, sem serem referenciados no typeProperties da atividade. Esses conjuntos de dados determinam a execução com dependências de fatia, mas são ignorados pela atividade AzureMLBatchExecution.


## <a name="updating-models-using-update-resource-activity"></a>Atualização dos modelos usando a Atividade de Recurso de Atualização
Depois de fazer o novo treinamento, atualize o serviço Web de Pontuação (teste de previsão exposto como um serviço Web) com o modelo treinado recentemente usando a **atividade de recurso de atualização Azure Machine Learning Studio (clássica)**. Veja o artigo [Atualização de modelos usando a Atividade do Recurso de Atualização](data-factory-azure-ml-update-resource-activity.md) para obter detalhes.

### <a name="reader-and-writer-modules"></a>Módulos de leitor e gravador
Um cenário comum de uso de parâmetros de serviço Web é o uso de leitores e gravadores do SQL do Azure. O módulo leitor é usado para carregar dados em um experimento de serviços de gerenciamento de dados fora do estúdio (clássico). O módulo gravador é para salvar dados de suas experiências em serviços de gerenciamento de dados fora do estúdio (clássico).

Para obter detalhes sobre leitor/gravador do SQL do Azure/Blob do Azure, consulte os tópicos [Leitor](/azure/machine-learning/studio-module-reference/import-data) e [Gravador](/azure/machine-learning/studio-module-reference/export-data) na biblioteca MSDN. O exemplo na seção anterior usou o leitor de blobs do Azure e o gravador de blobs do Azure. Esta seção discute usando o leitor do SQL do Azure e o gravador do SQL do Azure.

## <a name="frequently-asked-questions"></a>Perguntas frequentes
**P:** Tenho vários arquivos que são gerados pelos meus pipelines de Big Data. Posso usar a atividade AzureMLBatchExecution para trabalhar em todos os arquivos?

**R:** Sim. Confira a seção **Usando um módulo Leitor para ler dados de vários arquivos no Blob do Azure** para obter detalhes.

## <a name="azure-machine-learning-studio-classic-batch-scoring-activity"></a>Atividade de Pontuação de lote Azure Machine Learning Studio (clássica)
Se você estiver usando a atividade **AzureMLBatchScoring** para integrar com Azure Machine Learning Studio (clássico), recomendamos que você use a atividade de **AzureMLBatchExecution** mais recente.

A atividade AzureMLBatchExecution foi introduzida na versão de agosto de 2015 do Azure SDK e Azure PowerShell.

Se você quiser continuar usando a atividade AzureMLBatchScoring, continue lendo esta seção.

### <a name="azure-machine-learning-studio-classic-batch-scoring-activity-using-azure-storage-for-inputoutput"></a>Atividade de Pontuação de lote Azure Machine Learning Studio (clássica) usando o armazenamento do Azure para entrada/saída

```JSON
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchScoring",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "ScoringInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "ScoringResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

### <a name="web-service-parameters"></a>Parâmetros de serviço Web
Para especificar valores para parâmetros de serviço Web, adicione uma seção **typeProperties** à seção **AzureMLBatchScoringActivity** no JSON do pipeline, conforme mostra o exemplo a seguir:

```JSON
"typeProperties": {
    "webServiceParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```
Você também pode usar [Funções do Data Factory](data-factory-functions-variables.md) para passar valores para os parâmetros do serviço Web conforme mostrado no exemplo a seguir:

```JSON
"typeProperties": {
    "webServiceParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Os parâmetros de serviço Web diferenciam maiúsculas de minúsculas, portanto, garanta que os nomes que você especificar na atividade de JSON correspondam aos expostos pelo serviço Web.
>
>

## <a name="see-also"></a>Consulte Também
* [Postagem do blog do Azure: Introdução ao Azure Data Factory e Azure Machine Learning](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)

[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: https://azure.microsoft.com/services/machine-learning/