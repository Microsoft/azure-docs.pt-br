---
title: Transformar dados usando o script U-SQL - Azure | Microsoft Docs
description: Saiba como processar ou transformar dados executando scripts U-SQL no serviço de computação do Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
ms.assetid: e17c1255-62c2-4e2e-bb60-d25274903e80
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/01/2017
author: nabhishek
ms.author: abnarain
manager: craigg
robots: noindex
ms.openlocfilehash: 5835c37363c7e9d2dd3253c08ab97f17852725f5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61248140"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Transforme dados executando scripts U-SQL no serviço de computação do Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](data-factory-usql-activity.md)
> * [Versão 2 (versão atual)](../transform-data-using-data-lake-analytics.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [Atividade U-SQL na V2](../transform-data-using-data-lake-analytics.md).

Um pipeline em uma fábrica de dados do Azure processa dados nos serviços de armazenamento vinculados utilizando serviços de computação vinculados. Ela contém uma sequência de atividades em que cada atividade executa uma operação de processamento específica. Este artigo descreve a **Atividade do U-SQL do Data Lake Analytics** que executa um script **U-SQL** em um serviço vinculado de computação do **Azure Data Lake Analytics**. 

Crie uma conta do Azure Data Lake Analytics antes de criar um pipeline com uma atividade do U-SQL do Data Lake Analytics. Para saber mais sobre o Azure Data Lake Analytics, veja [Introdução ao Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

Veja o tutorial [Criar seu primeiro pipeline](data-factory-build-your-first-pipeline.md) para obter etapas detalhadas de como criar um Data Factory, serviços vinculados, conjuntos de dados e um pipeline. Use os snippets de código JSON com o Editor do Data Factory, Visual Studio ou Azure PowerShell para criar as entidades do Data Factory.

## <a name="supported-authentication-types"></a>Tipos de autenticação com suporte
A atividade do U-SQL dá suporte aos tipos de autenticação abaixo no Data Lake Analytics:
* Autenticação de entidade de serviço
* Autenticação de credencial do usuário (OAuth) 

Recomendamos o uso da autenticação de entidade de serviço, especialmente para uma execução de U-SQL agendada. O comportamento de expiração do token pode ocorrer com autenticação de credenciais do usuário. Para obter os detalhes de configuração, consulte a seção [Propriedades do serviço vinculado](#azure-data-lake-analytics-linked-service).

## <a name="azure-data-lake-analytics-linked-service"></a>Serviço Vinculado da Análise Azure Data Lake
Você cria um serviço vinculado do **Azure Data Lake Analytics** para vincular um serviço de computação do Azure Data Lake Analytics a um Azure Data Factory. A atividade de U-SQL do Data Lake Analytics no pipeline se refere a esse serviço vinculado. 

A tabela a seguir apresenta as descrições das propriedades genéricas usadas na definição JSON. Você ainda pode escolher entre a autenticação de credencial do usuário e entidade de serviço.

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| **tipo** |A propriedade de tipo deve ser configurada como: **AzureDataLakeAnalytics**. |Sim |
| **accountName** |Nome da conta da Análise Azure Data Lake. |Sim |
| **dataLakeAnalyticsUri** |URI da Análise Azure Data Lake. |Não  |
| **subscriptionId** |Id de assinatura do Azure |Não (se não for especificado, a assinatura do Data Factory é usada). |
| **resourceGroupName** |Nome do grupo de recursos do Azure |Não (se não for especificado, o grupo de recursos do Data Factory é usado). |

### <a name="service-principal-authentication-recommended"></a>Autenticação de entidade de serviço (recomendada)
Para usar a autenticação de entidade de serviço, registre uma entidade de aplicativo no Azure AD (Azure Active Directory) e conceda a ela o acesso ao Data Lake Store. Para encontrar as etapas detalhadas, consulte [Autenticação de serviço a serviço](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Anote os seguintes valores, que são usados para definir o serviço vinculado:
* ID do aplicativo
* Chave do aplicativo 
* ID do locatário

Use a autenticação de entidade de serviço especificando as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| **servicePrincipalId** | Especifique a ID do cliente do aplicativo. | Sim |
| **servicePrincipalKey** | Especifique a chave do aplicativo. | Sim |
| **tenant** | Especifique as informações de locatário (domínio nome ou ID do Locatário) em que o aplicativo reside. É possível recuperá-las focalizando o mouse no canto superior direito do Portal do Azure. | Sim |

**Exemplo: Autenticação de entidade de serviço**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics.net",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

### <a name="user-credential-authentication"></a>Autenticação de credenciais de usuário
Como alternativa, você pode usar a autenticação de credenciais do usuário do Data Lake Analytics especificando as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| **authorization** | Clique no botão **Autorizar** no Editor do Data Factory e insira as suas credenciais, que atribuem a URL de autorização gerada automaticamente a essa propriedade. | Sim |
| **sessionId** | A ID de sessão OAuth da sessão de autorização OAuth. Cada ID da sessão é exclusiva e pode ser usada somente uma vez. Essa configuração é gerada automaticamente quando você usa o Editor do Data Factory. | Sim |

**Exemplo: Autenticação da credencial do usuário**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>", 
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="token-expiration"></a>Expiração do token
O código de autorização gerado usando o botão **Autorizar** expira após algum tempo. Confira a tabela a seguir para ver os tempos de expiração para os diferentes tipos de contas de usuário. Talvez você veja a seguinte mensagem de erro quando o **token de autenticação expirar**: Erro de operação de credencial: invalid_grant - AADSTS70002: Erro ao validar credenciais. AADSTS70008: A concessão de acesso fornecida expirou ou foi revogada. ID de Rastreamento: d18629e8-af88-43c5-88e3-d8419eb1fca1 ID de Correlação: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Carimbo de data/hora: 2015-12-15 21:09:31Z

| Tipo de usuário | Expira após |
|:--- |:--- |
| Contas de usuário NÃO gerenciadas pelo Azure Active Directory (@hotmail.com, @live.com etc.) |12 horas |
| Contas de usuários gerenciadas pelo AAD (Azure Active Directory) |14 dias após a última execução da fatia. <br/><br/>90 dias, se uma fatia com base em serviços vinculados do OAuth for executada pelo menos uma vez a cada 14 dias. |

Para evitar/resolver este erro, reautorize usando o botão **Autorizar** quando o **token expirar** e reimplante o serviço vinculado. Você também pode gerar valores para as propriedades **sessionId** e **authorization** de modo programático usando o código da seguinte maneira:

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```

Veja os tópicos [Classe AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [Classe AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx) e [Classe AuthorizationSessionGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) para obter detalhes sobre as classes do Data Factory usadas no código. Adicione uma referência a: Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll para a classe WindowsFormsWebAuthenticationDialog. 

## <a name="data-lake-analytics-u-sql-activity"></a>Atividade do U-SQL da Análise Data Lake
O seguinte snippet de código JSON define um pipeline com uma Atividade do U-SQL da Análise Data Lake. A definição de atividade tem uma referência para o serviço vinculado da Análise Azure Data Lake criado anteriormente.   

```json
{
    "name": "ComputeEventsByRegionPipeline",
    "properties": {
        "description": "This is a pipeline to compute events for en-gb locale and date less than 2012/02/19.",
        "activities": 
        [
            {
                "type": "DataLakeAnalyticsU-SQL",
                "typeProperties": {
                    "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                    "scriptLinkedService": "StorageLinkedService",
                    "degreeOfParallelism": 3,
                    "priority": 100,
                    "parameters": {
                        "in": "/datalake/input/SearchLog.tsv",
                        "out": "/datalake/output/Result.tsv"
                    }
                },
                "inputs": [
                    {
                        "name": "DataLakeTable"
                    }
                ],
                "outputs": 
                [
                    {
                        "name": "EventsByRegionTable"
                    }
                ],
                "policy": {
                    "timeout": "06:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "EventsByRegion",
                "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
            }
        ],
        "start": "2015-08-08T00:00:00Z",
        "end": "2015-08-08T01:00:00Z",
        "isPaused": false
    }
}
```

A tabela a seguir descreve os nomes e as descrições de propriedades que são específicas a esta atividade. 

| Propriedade            | DESCRIÇÃO                              | Obrigatório                                 |
| :------------------ | :--------------------------------------- | :--------------------------------------- |
| Tipo                | A propriedade type deve ser definida como **DataLakeAnalyticsU-SQL**. | Sim                                      |
| linkedServiceName   | Referência ao Azure Data Lake Analytics registrado como um serviço vinculado ao Data Factory | Sim                                      |
| scriptPath          | Caminho para a pasta que contém o script U-SQL. O nome do arquivo diferencia maiúsculas de minúsculas. | Não (se você usar o script)                   |
| scriptLinkedService | Serviço vinculado que vincula o armazenamento que contém o script para a fábrica de dados | Não (se você usar o script)                   |
| script              | Especificar script embutido em vez de especificar scriptPath e scriptLinkedService. Por exemplo: `"script": "CREATE DATABASE test"`. | Não (se você usar scriptPath e scriptLinkedService) |
| degreeOfParallelism | O número máximo de nós usados simultaneamente para executar o trabalho. | Não                                        |
| prioridade            | Determina quais trabalhos de todos os que estão na fila devem ser selecionados para serem executados primeiro. Quanto menor o número, maior a prioridade. | Não                                        |
| parâmetros          | Parâmetros do script U-SQL          | Não                                        |
| runtimeVersion      | Versão de tempo de execução do mecanismo U-SQL a ser usado | Não                                        |
| compilationMode     | <p>Modo de compilação do U-SQL. Deve ser um destes valores:</p> <ul><li>**Semantic:** execute apenas as verificações de semântica e as verificações de integridade necessárias.</li><li>**Full:** execute a compilação completa, incluindo verificação de sintaxe, otimização, geração de código, etc.</li><li>**SingleBox:** executa a compilação completa com a configuração TargetType como SingleBox.</li></ul><p>Se você não especificar um valor para essa propriedade, o servidor determinará o modo de compilação ideal. </p> | Não                                        |

Consulte [Definição do Script SearchLogProcessing.txt](#sample-u-sql-script) para ver a definição do script. 

## <a name="sample-input-and-output-datasets"></a>Conjuntos de dados de entrada e saída de exemplo
### <a name="input-dataset"></a>Conjunto de dados de entrada
Neste exemplo, os dados de entrada residem em um Repositório Azure Data Lake (arquivo SearchLog.tsv na pasta datalake/input). 

```json
{
    "name": "DataLakeTable",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
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

### <a name="output-dataset"></a>Conjunto de dados de saída
Neste exemplo, os dados de saída produzidos pelo script U-SQL são armazenados em um Repositório Azure Data Lake (pasta datalake/output). 

```json
{
    "name": "EventsByRegionTable",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="sample-data-lake-store-linked-service"></a>Exemplo de serviço vinculado do Data Lake Store
Veja a definição de exemplo de serviço vinculado do Azure Data Lake Store usado pelos conjuntos de dados de entrada/saída. 

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
        }
    }
}
```

Consulte o artigo [Mover dados de e para o Azure Data Lake Store](data-factory-azure-datalake-connector.md) para obter descrições das propriedades de JSON. 

## <a name="sample-u-sql-script"></a>Exemplo de script U-SQL

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM @in
    USING Extractors.Tsv(nullEscape:"#NULL#");

@rs1 =
    SELECT Start, Region, Duration
    FROM @searchlog
WHERE Region == "en-gb";

@rs1 =
    SELECT Start, Region, Duration
    FROM @rs1
    WHERE Start <= DateTime.Parse("2012/02/19");

OUTPUT @rs1   
    TO @out
      USING Outputters.Tsv(quoting:false, dateTimeFormat:null);
```

Os valores para  **\@na** e  **\@out** parâmetros no script U-SQL são passados dinamicamente pelo ADF usando a seção 'parameters'. Veja a seção "parâmetros" na definição do pipeline.

Você pode especificar outras propriedades, por exemplo, degreeOfParallelism e prioridade, bem como em sua definição de pipeline para os trabalhos executados no serviço Data Lake Analytics.

## <a name="dynamic-parameters"></a>Parâmetros dinâmicos
Na definição de pipeline de exemplo, os parâmetros in e out são atribuídos com valores embutidos em código. 

```json
"parameters": {
    "in": "/datalake/input/SearchLog.tsv",
    "out": "/datalake/output/Result.tsv"
}
```

É possível usar parâmetros dinâmicos em vez disso. Por exemplo:  

```json
"parameters": {
    "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
    "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
}
```

Nesse caso, os arquivos de entrada ainda são obtidos da pasta /datalake/input e os arquivos de saída são gerados na pasta /datalake/output. Os nomes de arquivo são dinâmicos com base na hora de início da fatia.  


