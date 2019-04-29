---
title: Transformar dados usando o script U-SQL - Azure | Microsoft Docs
description: Saiba como processar ou transformar dados executando scripts U-SQL no serviço de computação do Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: abnarain
ms.openlocfilehash: d5b074fcf182bcc9bf4dc17ba21215d27e13cbdd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60888428"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Transforme dados executando scripts U-SQL no serviço de computação do Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-usql-activity.md)
> * [Versão atual](transform-data-using-data-lake-analytics.md)

Um pipeline em uma fábrica de dados do Azure processa dados nos serviços de armazenamento vinculados utilizando serviços de computação vinculados. Ela contém uma sequência de atividades em que cada atividade executa uma operação de processamento específica. Este artigo descreve a **Atividade do U-SQL do Data Lake Analytics** que executa um script **U-SQL** em um serviço vinculado de computação do **Azure Data Lake Analytics**. 

Crie uma conta do Azure Data Lake Analytics antes de criar um pipeline com uma atividade do U-SQL do Data Lake Analytics. Para saber mais sobre o Azure Data Lake Analytics, veja [Introdução ao Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md).


## <a name="azure-data-lake-analytics-linked-service"></a>Serviço vinculado do Azure Data Lake Analytics
Você cria um serviço vinculado do **Azure Data Lake Analytics** para vincular um serviço de computação do Azure Data Lake Analytics a um Azure Data Factory. A atividade de U-SQL do Data Lake Analytics no pipeline se refere a esse serviço vinculado. 

A tabela a seguir apresenta as descrições das propriedades genéricas usadas na definição JSON. 

| Propriedade                 | DESCRIÇÃO                              | Obrigatório                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| **tipo**                 | A propriedade de tipo deve ser configurada como: **AzureDataLakeAnalytics**. | Sim                                      |
| **accountName**          | Nome da conta da Análise Azure Data Lake.  | Sim                                      |
| **dataLakeAnalyticsUri** | URI da Análise Azure Data Lake.           | Não                                        |
| **subscriptionId**       | ID de assinatura do Azure                    | Não                                        |
| **resourceGroupName**    | Nome do grupo de recursos do Azure                | Não                                        |

### <a name="service-principal-authentication"></a>Autenticação de entidade de serviço
O serviço vinculado do Azure Data Lake Analytics requer uma autenticação de entidade de serviço para conectar-se ao serviço do Azure Data Lake Analytics. Para usar a autenticação da entidade de serviço, registre uma entidade de aplicativo no Azure Active Directory (Azure AD) e conceda acesso ao Data Lake Analytics e ao Data Lake Store que ele usa. Para encontrar as etapas detalhadas, consulte [Autenticação de serviço a serviço](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Anote os seguintes valores, que são usados para definir o serviço vinculado:

* ID do aplicativo
* Chave do aplicativo 
* ID do locatário

Conceder permissão de entidade de serviço para o seu Azure Data Lake Anatlyics usando o [Assistente Adicionar Usuário](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#add-a-new-user).

Use a autenticação de entidade de serviço especificando as seguintes propriedades:

| Propriedade                | DESCRIÇÃO                              | Obrigatório |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Especifique a ID do cliente do aplicativo.     | Sim      |
| **servicePrincipalKey** | Especifique a chave do aplicativo.           | Sim      |
| **tenant**              | Especifique as informações de locatário (domínio nome ou ID do Locatário) em que o aplicativo reside. É possível recuperá-las focalizando o mouse no canto superior direito do Portal do Azure. | Sim      |

**Exemplo: Autenticação de entidade de serviço**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "<account name>",
            "dataLakeAnalyticsUri": "<azure data lake analytics URI>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }       
    }
}
```

Para saber mais sobre o serviço vinculado, consulte [Compute linked services](compute-linked-services.md) (Serviços de computação vinculados).

## <a name="data-lake-analytics-u-sql-activity"></a>Atividade do U-SQL da Análise Data Lake
O seguinte snippet de código JSON define um pipeline com uma Atividade do U-SQL da Análise Data Lake. A definição de atividade tem uma referência para o serviço vinculado da Análise Azure Data Lake criado anteriormente. Para executar um script U-SQL do Data Lake Analytics, o Data Factory envia o script especificado ao Data Lake Analytics e as entradas e saídas necessárias são definidas no script do Data Lake Analytics para efetuar fetch e emitir a saída. 

```json
{
    "name": "ADLA U-SQL Activity",
    "description": "description",
    "type": "DataLakeAnalyticsU-SQL",
    "linkedServiceName": {
        "referenceName": "<linked service name of Azure Data Lake Analytics>",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "<linked service name of Azure Data Lake Store or Azure Storage which contains the U-SQL script>",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
        "degreeOfParallelism": 3,
        "priority": 100,
        "parameters": {
            "in": "/datalake/input/SearchLog.tsv",
            "out": "/datalake/output/Result.tsv"
        }
    }   
}
```

A tabela a seguir descreve os nomes e as descrições de propriedades que são específicas a esta atividade. 

| Propriedade            | DESCRIÇÃO                              | Obrigatório |
| :------------------ | :--------------------------------------- | :------- |
| Nome                | Nome da atividade no pipeline     | Sim      |
| description         | Texto que descreve o que a atividade faz.  | Não        |
| tipo                | Para a atividade do U-SQL do Data Lake Analytics, o tipo de atividade é **DataLakeAnalyticsU-SQL**. | Sim      |
| linkedServiceName   | Serviço vinculado ao Azure Data Lake Analytics. Para saber mais sobre esse serviço vinculado, consulte o artigo [Compute linked services](compute-linked-services.md) (Serviços de computação vinculados).  |Sim       |
| scriptPath          | Caminho para a pasta que contém o script U-SQL. O nome do arquivo diferencia maiúsculas de minúsculas. | Sim      |
| scriptLinkedService | Serviço vinculado que vincula o **Azure Data Lake Store** ou **Armazenamento do Azure** que contém o script para o Data Factory | Sim      |
| degreeOfParallelism | O número máximo de nós usados simultaneamente para executar o trabalho. | Não        |
| prioridade            | Determina quais trabalhos de todos os que estão na fila devem ser selecionados para serem executados primeiro. Quanto menor o número, maior a prioridade. | Não        |
| parameters          | Parâmetros para passar para o script U-SQL.    | Não        |
| runtimeVersion      | Versão de tempo de execução do mecanismo U-SQL a ser usado. | Não        |
| compilationMode     | <p>Modo de compilação do U-SQL. Deve ser um destes valores: **Semantic:** realiza apenas as verificações de semântica e as verificações de integridade necessárias; **Full:** realiza a compilação completa, incluindo verificação de sintaxe, otimização, geração de código, etc.; **SingleBox:** executa a compilação completa com a configuração TargetType definida como SingleBox. Se você não especificar um valor para essa propriedade, o servidor determinará o modo de compilação ideal. | Não  |

Consulte [SearchLogProcessing.txt](#sample-u-sql-script) para ver a definição do script. 

## <a name="sample-u-sql-script"></a>Exemplo de script U-SQL

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int,
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

No exemplo de script acima a entrada e saída para o script é definido em  **\@na** e  **\@out** parâmetros. Os valores para  **\@na** e  **\@out** parâmetros no script U-SQL são passados dinamicamente pelo Data Factory usando a seção 'parameters'. 

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
    "in": "/datalake/input/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/data.tsv",
    "out": "/datalake/output/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/result.tsv"
}
```

Nesse caso, os arquivos de entrada ainda são obtidos da pasta /datalake/input e os arquivos de saída são gerados na pasta /datalake/output. Os nomes de arquivo são dinâmicos com base na hora de início de janela que está sendo passada quando o pipeline é disparado.  

## <a name="next-steps"></a>Próximas etapas
Consulte os seguintes artigos que explicam como transformar dados de outras maneiras: 

* [Hive activity](transform-data-using-hadoop-hive.md) (Atividade do Hive)
* [Pig activity](transform-data-using-hadoop-pig.md) (Atividade do Pig)
* [MapReduce activity](transform-data-using-hadoop-map-reduce.md) (Atividade do MapReduce)
* [Hadoop Streaming activity](transform-data-using-hadoop-streaming.md) (Atividade de streaming do Hadoop)
* [Spark activity](transform-data-using-spark.md) (Atividade do Spark)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Batch Execution activity](transform-data-using-machine-learning.md) (Atividade de execução em lotes do Machine Learning)
* [Stored procedure activity](transform-data-using-stored-procedure.md) (Atividade de procedimento armazenado)
