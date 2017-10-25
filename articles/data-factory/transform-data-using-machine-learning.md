---
title: "Criar pipelines de dados de previsão usando o Azure Data Factory | Microsoft Docs"
description: "Saiba como criar um pipeline de previsão usando Azure Machine Learning – atividade de execução em lotes do Azure Data Factory."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: shengc
ms.openlocfilehash: 413f12d301a0e2c47048d23b2d4fb7de6423256d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Criar pipelines de previsão usando Azure Machine Learning e o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 – já disponível](v1/data-factory-azure-ml-batch-execution-activity.md)
> * [Versão 2 – Versão prévia](transform-data-using-machine-learning.md)

O [Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) permite compilar, testar e implantar soluções de análise preditiva. De um ponto de vista de alto nível, isso é feito em três etapas:

1. **Crie um teste de treinamento**. Conclua esta etapa usando o Estúdio AM do Azure. O Estúdio AM do Azure é um ambiente de desenvolvimento visual colaborativo usado para treinar e testar um modelo de análise preditiva usando dados de treinamento.
2. **Convertê-lo em um teste preditivo**. Quando o modelo foi treinado com dados existentes e você estiver pronto para usá-lo para pontuar novos dados, você preparará e simplificará seu teste para a pontuação.
3. **Implantá-lo como um serviço da Web**. Você pode publicar seu experimento de pontuação como um serviço Web do Azure. Você pode enviar dados ao seu modelo por desse ponto de extremidade de serviço Web e receber previsões de resultado do modelo.  

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que já está disponível, consulte a [Atividade de execução em lotes do Machine Learning na V1](v1/data-factory-azure-ml-batch-execution-activity.md).


### <a name="data-factory-and-machine-learning-together"></a>Data Factory e Machine Learning juntos
O Azure Data Factory permite que você crie facilmente pipelines que usam um serviço Web do [Azure Machine Learning][azure-machine-learning] publicado para análise preditiva. Usando a **Atividade de Execução em Lote** em um pipeline do Azure Data Factory, você pode invocar um serviço Web de AM do Azure para fazer previsões sobre dados em lote. 

Ao longo do tempo, os modelos de previsão nos experimentos de pontuação do AM do Azure precisam ser treinados novamente usando novos conjuntos de dados de entrada. Você pode treinar novamente um modelo do AM do Azure de um pipeline do Data Factory executando as seguintes etapas:

1. Publique o experimento de treinamento (e não um experimento preditivo) como um serviço Web. Essa etapa é feita no Estúdio AM do Azure como você fez para expor o experimento preditivo como um serviço Web no cenário anterior.
2. Use a Atividade de Execução de Lote do AM do Azure para chamar o serviço Web para o experimento de treinamento. Basicamente, você pode usar a atividade de Execução de Lote do AM do Azure para invocar o serviço Web de treinamento e o serviço Web de pontuação.

Depois de concluir o treinamento, atualize o serviço Web de pontuação (experimento preditivo exposto como um serviço Web) com o modelo recém-treinado usando a **Atividade de recurso de Atualização de AM do Azure**. Veja o artigo [Atualização de modelos usando a Atividade do Recurso de Atualização](update-machine-learning-models.md) para obter detalhes.

## <a name="azure-machine-learning-linked-service"></a>Serviço vinculado do Azure Machine Learning

Você cria um serviço vinculado do **Azure Machine Learning** para vincular um serviço Web do Azure Machine Learning a um Azure Data Factory. O serviço vinculado é usado pela atividade de execução em lotes do Azure Machine Learning e pela [atividade de atualização de recursos](update-machine-learning-models.md). 


```JSON
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "URL to Azure ML Predictive Web Service",
            "apiKey": {
                "type": "SecureString",
                "value": "api key"
            }
        }
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Consulte o artigo [Compute linked services](compute-linked-services.md) (Serviços de computação vinculados) para obter descrições sobre as propriedades na definição JSON. 

O Azure Machine Learning dá suporte a serviços Web clássicos e a novos serviços Web para sua experiência de previsão. Você pode escolher aquele que é mais adequado no Data Factory. Para obter as informações necessárias para criar o serviço vinculado do Azure Machine Learning, acesse https://services.azureml.net, no qual todos os (novos) serviços Web e os serviços Web clássicos estão listados. Clique no serviço Web que deseja acessar e clique na página **Consumir**. Copie a **Chave Primária** da propriedade **apiKey** e **Solicitações de Lote** da propriedade **mlEndpoint**. 

![Serviços Web do Azure Machine Learning](./media/transform-data-using-machine-learning/web-services.png)

##<a name="azure-machine-learning-batch-execution-activity"></a>Atividade de execução em lotes do Azure Machine Learning

O trecho JSON a seguir define uma atividade de execução em lotes do Azure Machine Learning. A definição da atividade tem uma referência ao serviço vinculado do Azure Machine Learning que você criou anteriormente. 

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "<web service input name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                }, 
                "FilePath":"path1"
            }, 
            "<web service input name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference" 
                }, 
                "FilePath":"path2"
            }        
        },
        "webServiceOutputs": {
            "<web service output name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"   
                }, 
                "FilePath":"path3"
            }, 
            "<web service output name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"   
                }, 
                "FilePath":"path4"
            }         
        },
        "globalParameters": {
            "<Parameter 1 Name>": "<parameter value>",
            "<parameter 2 name>": "<parameter 2 value>"
        }
    }
}
```



| Propriedade          | Descrição                              | Obrigatório |
| :---------------- | :--------------------------------------- | :------- |
| name              | Nome da atividade no pipeline     | Sim      |
| description       | Texto que descreve o que a atividade faz.  | Não       |
| type              | Para a atividade do U-SQL do Data Lake Analytics, o tipo de atividade é **AzureMLBatchExecution**. | Sim      |
| linkedServiceName | Serviços vinculados ao serviço vinculado do Azure Machine Learning. Para saber mais sobre esse serviço vinculado, consulte o artigo [Compute linked services](compute-linked-services.md) (Serviços de computação vinculados). | Sim      |
| webServiceInputs  | Pares chave-valor, mapeando os nomes das entradas do serviço Web do Azure Machine Learning. A chave deve corresponder aos parâmetros de entrada definidos no serviço Web do Azure Machine Learning publicado. Valor é um par de propriedades FilePath e serviços vinculados do Armazenamento do Azure especificando os locais de Blob de entrada. | Não       |
| webServiceOutputs | Pares chave-valor, mapeando os nomes das saídas do serviço Web do Azure Machine Learning. A chave deve corresponder aos parâmetros de saída definidos no serviço Web do Azure Machine Learning publicado. Valor é um par de propriedades FilePath e serviços vinculados do Armazenamento do Azure especificando os locais de Blob de saída. | Não       |
| globalParameters  | Pares chave-valor a serem passados para o ponto de extremidade de serviço de execução em lotes do Azure ML. As chaves devem corresponder aos nomes dos parâmetros do serviço Web definidos no serviço Web do Azure ML publicado. Os valores são passados na propriedade GlobalParameters da solicitação de execução em lotes do Azure ML | Não       |

### <a name="scenario-1-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Cenário 1: testes usando entradas/saídas de serviço Web que se referem ao Armazenamento de Blobs do Azure

Nesse cenário, o serviço Web de Azure Machine Learning faz previsões usando dados de um arquivo em um armazenamento de blob do Azure e armazena os resultados de previsão no armazenamento de blob. O JSON a seguir define um pipeline do Data Factory com uma atividade AzureMLBatchExecution. Os dados de entrada e saída no Armazenamento de Blobs do Azure são referenciados usando um par de LinkedName e FilePath. No exemplo, os serviços vinculados de entradas e saídas são diferentes, você pode usar serviços vinculados diferentes para cada uma das entradas/saídas do Data Factory para poder selecionar os arquivos certos e enviá-los ao serviço Web do Azure ML. 

> [!IMPORTANT]
> Em seu teste do Azure ML, as portas de entrada e saída do serviço Web e os parâmetros globais têm nomes padrão ("input1", "input2") que você pode personalizar. Os nomes que você usa para as configurações webServiceInputs, webserviceoutputs e globalParameters devem corresponder exatamente aos nomes nos testes. Você pode exibir a carga de solicitação de exemplo na página de Ajuda de Execução do Lote no ponto de extremidade de Azure ML para verificar o mapeamento esperado.
>
> 

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "input1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                }, 
                "FilePath":"amltest/input/in1.csv"
            }, 
            "input2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference" 
                }, 
                "FilePath":"amltest/input/in2.csv"
            }        
        },
        "webServiceOutputs": {
            "outputName1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"   
                }, 
                "FilePath":"amltest2/output/out1.csv"
            }, 
            "outputName2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"   
                }, 
                "FilePath":"amltest2/output/out2.csv"
            }         
        }
    }
}
```
### <a name="scenario-2-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Cenário 2: testes usando módulos de leitura/gravação para referenciar dados em vários armazenamentos
Outro cenário comum ao criar testes do Azure ML é usar módulos Importar Dados e Exportar Dados. O módulo de Importar Dados é usado para carregar dados em um teste e o módulo Exportar Dados é usado para salvar os dados dos testes. Para obter detalhes sobre os módulos Importar Dados e Exportar Dados, consulte os tópicos [Importar Dados](https://msdn.microsoft.com/library/azure/dn905997.aspx) e [Exportar Dados](https://msdn.microsoft.com/library/azure/dn905984.aspx) na biblioteca MSDN.     

Ao usar os módulos Importar Dados e Exportar Dados, recomenda-se usar um parâmetro de serviço Web para cada propriedade desses módulos. Esses parâmetros da Web permitem que você configure os valores durante o tempo de execução. Por exemplo, você pode criar um teste com um módulo Importar Dados que use um Banco de Dados SQL do Azure: XXX.database.windows.net. Depois que o serviço Web for implantado, você precisará habilitar os consumidores do serviço Web para especificar outro SQL Server do Azure chamado `YYY.database.windows.net`. Você pode usar um parâmetro de serviço Web para permitir que esse valor seja configurado.

> [!NOTE]
> A saída e entrada de serviço Web são diferentes dos parâmetros de serviço Web. No primeiro cenário, você viu como uma entrada e saída podem ser especificados para um serviço Web do AM do Azure. Nesse cenário, você pode passar parâmetros para um serviço Web que correspondam às propriedades dos módulos Importar Dados e Exportar Dados.
>
> 

Vejamos um cenário para o uso de parâmetros de serviço Web. Você tem um serviço Web implantado de Azure Machine Learning que usa um módulo de leitor para ler dados de uma das fontes de dados compatíveis com o Azure Machine Learning (por exemplo: Banco de Dados SQL do Azure). Após a execução do lote, os resultados são gravados usando um módulo Gravador (banco de dados SQL do Azure).  Não há entradas e saídas de serviço Web definidas nos experimentos. Nesse caso, recomendamos que você configure os parâmetros de serviço Web relevantes para os módulos de leitor e gravador. Essa configuração permite que os módulos de leitor/gravador sejam configurados ao usar a atividade AzureMLBatchExecution. Você especifica parâmetros de serviço Web na seção **globalParameters** no JSON da atividade da seguinte maneira:

```JSON
"typeProperties": {
    "globalParameters": {
        "Database server name": "<myserver>.database.windows.net",
        "Database name": "<database>",
        "Server user account name": "<user name>",
        "Server user account password": "<password>"
    }
}
```


> [!NOTE]
> Os parâmetros de serviço Web diferenciam maiúsculas de minúsculas, portanto, garanta que os nomes que você especificar na atividade de JSON correspondam aos expostos pelo serviço Web.
>

Depois de concluir o treinamento, atualize o serviço Web de pontuação (experimento preditivo exposto como um serviço Web) com o modelo recém-treinado usando a **Atividade de recurso de Atualização de AM do Azure**. Veja o artigo [Atualização de modelos usando a Atividade do Recurso de Atualização](update-machine-learning-models.md) para obter detalhes.



## <a name="next-steps"></a>Próximas etapas
Consulte os seguintes artigos que explicam como transformar dados de outras maneiras: 

* [U-SQL activity](transform-data-using-data-lake-analytics.md) (Atividade do U-SQL)
* [Hive activity](transform-data-using-hadoop-hive.md) (Atividade do Hive)
* [Pig activity](transform-data-using-hadoop-pig.md) (Atividade do Pig)
* [MapReduce activity](transform-data-using-hadoop-map-reduce.md) (Atividade do MapReduce)
* [Hadoop Streaming activity](transform-data-using-hadoop-streaming.md) (Atividade de streaming do Hadoop)
* [Spark activity](transform-data-using-spark.md) (Atividade do Spark)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Stored procedure activity](transform-data-using-stored-procedure.md) (Atividade de procedimento armazenado)
