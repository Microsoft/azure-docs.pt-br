---
title: Copiar dados do Salesforce usando o Azure Data Factory | Microsoft Docs
description: "Saiba como copiar dados do Salesforce para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2017
ms.author: jingwang
ms.openlocfilehash: c819b3e3e715427632e793ce77d31554914d248e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-from-salesforce-using-azure-data-factory"></a>Copiar dados do Salesforce usando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 – já disponível](v1/data-factory-salesforce-connector.md)
> * [Versão 2 – Versão prévia](connector-salesforce.md)

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de um banco de dados Salesforce. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que está em GA (disponibilidade geral), consulte [Conector do Salesforce na V1](v1/data-factory-salesforce-connector.md).

## <a name="supported-scenarios"></a>Cenários com suporte

Você pode copiar dados de um banco de dados Salesforce para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, esse conector do Salesforce dá suporte para as seguintes edições do Salesforce: **Developer Edition, Professional Edition, Enterprise Edition ou Unlimited Edition**. E ele dá suporte à cópia de dados da **produção, da área restrita e do domínio personalizado** do Salesforce.

## <a name="prerequisites"></a>Pré-requisitos

* A permissão de API deve estar habilitada no Salesforce. Consulte [Como habilito o acesso à API no Salesforce por conjunto de permissões?](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)

## <a name="salesforce-request-limits"></a>Limites da solicitação Salesforce

O Salesforce tem limites para o total de solicitações de API e as solicitações simultâneas de API. Observe os seguintes pontos:

- Se o número de solicitações simultâneas exceder o limite, a limitação será atingida e você verá falhas aleatórias.
- Se o número total de solicitações exceder o limite, a conta do Salesforce será bloqueada por 24 horas.

Você também pode receber o erro "REQUEST_LIMIT_EXCEEDED" em ambos os cenários. Veja a seção "Limites de Solicitações da API" no artigo [Salesforce Developer Limits](http://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) (Limites do Desenvolvedor Salesforce) para obter detalhes.

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com atividade de cópia usando o SDK do .NET, o SDK do Python, o Azure PowerShell, a API REST ou o modelo do Azure Resource Manager. Confira o [Tutorial de atividade de cópia](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo sobre a criação de um pipeline com uma atividade de cópia.

As seções que se seguem fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao conector do Salesforce.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do Salesforce:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type |A propriedade type deve ser definida para: **Salesforce**. |Sim |
| environmentUrl | Especifica a URL da instância do Salesforce. <br><br> – O padrão é `"https://login.salesforce.com"`. <br> – Para copiar dados da área restrita, especifique `"https://test.salesforce.com"`. <br> – Para copiar dados do domínio personalizado, especifique, por exemplo, `"https://[domain].my.salesforce.com"`. |Não |
| Nome de Usuário |Especifique um nome de usuário para a conta de usuário. |Sim |
| Senha |Especifique um senha para a conta de usuário. |Sim |
| securityToken |Especifique um token de segurança para a conta de usuário. Veja [Obter token de segurança](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) para ver instruções sobre como redefinir/obter o token de segurança. Para saber mais sobre os tokens de segurança em geral, veja [Security and the API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm) (Segurança e a API). |Sim |

**Exemplo:**

```json
{
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            },
            "securityToken": {
                "type": "SecureString",
                "value": "<security token>"
            }
        }
    },
    "name": "SalesforceLinkedService"
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre conjuntos de dados. Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do Salesforce.

Para copiar dados do Salesforce, defina a propriedade type do conjunto de dados como **RelationalTable**. Há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como: **RelationalTable** | Sim |
| tableName | Nome da tabela no banco de dados do Salesforce. | Não (se "query" na fonte da atividade for especificada) |

> [!IMPORTANT]
> A parte "__c" do Nome da API é necessária para qualquer objeto personalizado.

![Data Factory — conexão Salesforce — nome da API](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Exemplo:**

```json
{
    "name": "SalesforceDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Salesforce linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable__c"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte do Salesforce.

### <a name="salesforce-as-source"></a>Salesforce como fonte

Para copiar dados do Salesforce, defina o tipo de origem na atividade de cópia como **RelationalSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type da fonte da atividade de cópia deve ser definida como: **RelationalSource** | Sim |
| query |Utiliza a consulta personalizada para ler os dados. Você pode usar uma consulta SQL-92 ou uma consulta [SOQL (Salesforce Object Query Language)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm). Por exemplo: `select * from MyTable__c`. | Não (se a "tableName" no conjunto de dados for especificada) |

> [!IMPORTANT]
> A parte "__c" do Nome da API é necessária para qualquer objeto personalizado.

![Data Factory — conexão Salesforce — nome da API](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "RelationalSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="query-tips"></a>Dicas de consulta

### <a name="retrieving-data-from-salesforce-report"></a>Recuperando dados do relatório do Salesforce

Você pode recuperar dados de relatórios do Salesforce especificando a consulta como `{call "<report name>"}. Example: `"query": "{call \"TestReport\"}"`.

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>Recuperar registros excluídos da lixeira do Salesforce

Para consultar os registros excluídos pelo software da lixeira do Salesforce, você poderá especificar **"IsDeleted = 1"** em sua consulta. Por exemplo,

* Para consultar apenas os registros excluídos, especifique "select * from MyTable__c **where IsDeleted= 1**"
* Para consultar todos os registros, incluindo existentes e excluídos, especifique "select * from MyTable__c **m que IsDeleted = 0 ou IsDeleted = 1**"

### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>Recuperando dados usando a cláusula where na coluna DateTime

Ao especificar a consulta SQL ou SOQL, preste atenção à diferença de formato DateTime. Por exemplo:

* **Exemplo de SOQL**:`$$Text.Format('SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', <datetime parameter>, <datetime parameter>)`
* **Exemplo de SQL**: `$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\\'{0:yyyy-MM-dd HH:mm:ss}\\'}} AND LastModifiedDate < {{ts\\'{1:yyyy-MM-dd HH:mm:ss}\\'}}', <datetime parameter>, <datetime parameter>)`

## <a name="data-type-mapping-for-salesforce"></a>Mapeamento de tipo de dados para Salesforce

Ao copiar dados do Salesforce, os seguintes mapeamentos são usados de tipos de dados do Salesforce para tipos de dados provisórios do Azure Data Factory. Consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md) para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor.

| Tipo de dados do Salesforce | Tipo de dados provisório do Data Factory |
|:--- |:--- |
| Numeração automática |Cadeia de caracteres |
| Caixa de seleção |Booliano |
| Moeda |Duplo |
| Data |DateTime |
| Data/hora |DateTime |
| Email |Cadeia de caracteres |
| ID |Cadeia de caracteres |
| Relação de pesquisa |Cadeia de caracteres |
| Lista de seleção múltipla |Cadeia de caracteres |
| Número |Duplo |
| Porcentagem |Duplo |
| Telefone |Cadeia de caracteres |
| Lista de seleção |Cadeia de caracteres |
| Texto |Cadeia de caracteres |
| Área de texto |Cadeia de caracteres |
| Área de texto (longo) |Cadeia de caracteres |
| Área de texto (Rich) |Cadeia de caracteres |
| Texto (criptografado) |Cadeia de caracteres |
| URL |Cadeia de caracteres |


## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).