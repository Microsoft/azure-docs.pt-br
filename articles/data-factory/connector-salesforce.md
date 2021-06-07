---
title: Copiar dados de e para o Salesforce
description: Saiba como copiar dados do Salesforce para armazenamentos de dados de coletor com suporte ou de armazenamentos de dados de fonte com suporte para Salesforce usando uma atividade de cópia em um pipeline do Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: 5b49e62330c789d6d5cbe2af2edb28a2c3e1238f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104583081"
---
# <a name="copy-data-from-and-to-salesforce-by-using-azure-data-factory"></a>Copiar dados de e para Salesforce usando o Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-salesforce-connector.md)
> * [Versão atual](connector-salesforce.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de e para o Salesforce. Ele amplia o artigo [Visão geral da Atividade de Cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector do Salesforce tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/coletor com suporte](copy-activity-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados do Salesforce para qualquer armazenamento de dados de coletor com suporte. Você também pode copiar dados de qualquer repositório de dados de fonte com suporte para um banco de dados Salesforce. Para obter uma lista de armazenamentos de dados com suporte como fontes ou coletores pela atividade de cópia, consulte a tabela [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats) .

Especificamente, este conector Salesforce dá suporte à:

- Edições de Desenvolvedor, Professional, Enterprise ou Ilimitada do Salesforce.
- Copiar dados de e para a produção, da área restrita e do domínio personalizado do Salesforce.

O conector do Salesforce é criado sobre a API REST do Salesforce/em massa. Por padrão, ao copiar dados do Salesforce, o conector usa [V45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) e escolhe automaticamente entre as APIs REST e Bulk com base no tamanho dos dados – quando o conjunto de resultados é grande, a API em massa é usada para melhorar o desempenho; ao gravar dados no Salesforce, o conector usa [v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) da API em massa. Você também pode definir explicitamente a versão da API usada para ler/gravar dados por meio da [ `apiVersion` Propriedade](#linked-service-properties) no serviço vinculado.

## <a name="prerequisites"></a>Pré-requisitos

A permissão de API deve estar habilitada no Salesforce. Para mais informações, consulte [Habilitar o acesso à API no Salesforce por conjunto de permissões](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)

## <a name="salesforce-request-limits"></a>Limites da solicitação Salesforce

O Salesforce tem limites para o total de solicitações de API e as solicitações simultâneas de API. Observe os seguintes pontos:

- Se o número de solicitações simultâneas exceder o limite, a limitação será atingida e você verá falhas aleatórias.
- Se o número total de solicitações exceder o limite, a conta do Salesforce será bloqueada por 24 horas.

Você também pode receber a mensagem de erro "REQUEST_LIMIT_EXCEEDED" em ambos os cenários. Para obter mais informações, veja a seção "Limites de Solicitações da API" na seção [Salesforce Developer Limits](https://developer.salesforce.com/docs/atlas.en-us.218.0.salesforce_app_limits_cheatsheet.meta/salesforce_app_limits_cheatsheet/salesforce_app_limits_platform_api.htm) (Limites do Desenvolvedor Salesforce).

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções que se seguem fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao conector do Salesforce.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do Salesforce.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type |A propriedade type deve ser definida para **Salesforce**. |Sim |
| environmentUrl | Especifique a URL da instância do Salesforce. <br> – O padrão é `"https://login.salesforce.com"`. <br> – Para copiar dados da área restrita, especifique `"https://test.salesforce.com"`. <br> – Para copiar dados do domínio personalizado, especifique, por exemplo, `"https://[domain].my.salesforce.com"`. |Não |
| Nome de Usuário |Especifique um nome de usuário para a conta de usuário. |Sim |
| password |Especifique um senha para a conta de usuário.<br/><br/>Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |Sim |
| securityToken |Especifique um token de segurança para a conta de usuário. <br/><br/>Para saber mais sobre os tokens de segurança em geral, veja [Security and the API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm) (Segurança e a API). O token de segurança só poderá ser ignorado se você adicionar o IP do Integration Runtime à [lista de endereços IP confiáveis](https://developer.salesforce.com/docs/atlas.en-us.securityImplGuide.meta/securityImplGuide/security_networkaccess.htm) no Salesforce. Ao usar Azure IR, consulte [Azure Integration Runtime endereços IP](azure-integration-runtime-ip-addresses.md).<br/><br/>Para obter instruções sobre como obter e redefinir um token de segurança, consulte [obter um token de segurança](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm). Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |Não |
| apiVersion | Especifique a versão de API em massa/REST do Salesforce a ser usada, por exemplo, `48.0` . Por padrão, o conector usa [V45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) para copiar dados do Salesforce e usa o [v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) para copiar dados para o Salesforce. | Não |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Se não for especificado, ele usa o Integration Runtime padrão do Azure. | Não |

**Exemplo: Credenciais de armazenamento no Data Factory**

```json
{
    "name": "SalesforceLinkedService",
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
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: Credenciais de armazenamento no Key Vault**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of password in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            },
            "securityToken": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of security token in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do Salesforce.

Para copiar dados do e para o Salesforce, defina a propriedade tipo do conjunto de dados como **SalesforceObject**. Há suporte para as seguintes propriedades.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade tipo deve ser definida para **SalesforceObject**.  | Sim |
| objectApiName | O nome do objeto de Salesforce para recuperar dados. | Não para fonte, Sim para o coletor |

> [!IMPORTANT]
> A parte "__c" do **Nome da API** é necessária para qualquer objeto personalizado.

![Nome da API de conexão do Salesforce ao Data Factory](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Exemplo:**

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "type": "SalesforceObject",
        "typeProperties": {
            "objectApiName": "MyTable__c"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Salesforce linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

>[!NOTE]
>Para retrocompatibilidade: ao copiar dados de Salesforce, se você usar o conjunto de dados de tipo "RelationalTable" anterior continuará funcionando, enquanto você vê uma sugestão para alternar para o novo tipo de "SalesforceObject".

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como **RelationalTable**. | Sim |
| tableName | Nome da tabela no Salesforce. | Não (se "query" na fonte da atividade for especificada) |

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte e coletor do Salesforce.

### <a name="salesforce-as-a-source-type"></a>Salesforce como um tipo de fonte

Para copiar dados do Salesforce, defina o tipo de origem na atividade de cópia como **SalesforceSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade tipo da fonte da atividade de cópia deve ser definida como: **SalesforceSource**. | Sim |
| Consulta |Utiliza a consulta personalizada para ler os dados. É possível usar a consulta [SOQL (Salesforce Object Query Language)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) ou a consulta SQL-92. Veja mais dicas na seção [dicas de consulta](#query-tips). Se a consulta não for especificada, todos os dados do objeto Salesforce especificado em "objectApiName" no conjunto de dados serão recuperados. | Não (se "objectApiName" no conjunto de dados for especificado) |
| readBehavior | Indica se deve consultar os registros existentes, ou consultar todos os registros, incluindo o que foi excluído. Se não for especificado, o comportamento padrão é o primeiro. <br>Valores permitidos: **query** (padrão), **queryAll**.  | Não |

> [!IMPORTANT]
> A parte "__c" do **Nome da API** é necessária para qualquer objeto personalizado.

![Lista Nome da API de conexão do Salesforce ao Data Factory](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

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
                "type": "SalesforceSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

>[!NOTE]
>Para retrocompatibilidade: ao copiar dados de Salesforce, se você usar a cópia de tipo "RelationalSource" anterior, a fonte continuará funcionando, enquanto você vê uma sugestão para alternar para o novo tipo de "SalesforceSource".

### <a name="salesforce-as-a-sink-type"></a>Salesforce como um tipo de coletor

Para copiar dados do Salesforce, defina o tipo de coletor na atividade de cópia como **SalesforceSink**. As propriedades a seguir têm suporte na seção **sink** da atividade de cópia.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do coletor da atividade de cópia deve ser definida como **SalesforceSink**. | Sim |
| writeBehavior | O comportamento da operação de gravação.<br/>Valores permitidos são **Insert** e **Upsert**. | Não (o padrão é Insert) |
| externalIdFieldName | O nome do campo de ID externo para a operação upsert. O campo especificado deve ser definido como "campo de ID externa" no objeto Salesforce. Ele não pode ter valores nulos nos dados de entrada correspondentes. | Sim para "Upsert" |
| writeBatchSize | A contagem de linhas de dados gravados no Salesforce em cada lote. | Não (o padrão é 5.000) |
| ignoreNullValues | Indica se deve ignorar valores NULL de dados de entrada durante a operação de gravação.<br/>Os valores permitidos são **True** e **False**.<br>- **True**: Deixa os dados no objeto de destino inalterados quando você faz uma operação upsert ou atualização. Insira um valor padrão definido quando você faz uma operação insert.<br/>- **False**: atualiza os dados no objeto de destino como NULL quando você faz uma operação upsert ou atualização. Insira um valor NULL quando você faz uma operação insert. | Não (padrão é falso) |
| maxConcurrentConnections |O limite superior de conexões simultâneas estabelecidas com o armazenamento de dados durante a execução da atividade. Especifique um valor somente quando desejar limitar as conexões simultâneas.| Não |

**Exemplo: coletor Salesforce em uma atividade de cópia**

```json
"activities":[
    {
        "name": "CopyToSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Salesforce output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SalesforceSink",
                "writeBehavior": "Upsert",
                "externalIdFieldName": "CustomerId__c",
                "writeBatchSize": 10000,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="query-tips"></a>Dicas de consulta

### <a name="retrieve-data-from-a-salesforce-report"></a>Recupere dados do relatório do Salesforce

Você pode recuperar dados de relatórios do Salesforce especificando uma consulta como `{call "<report name>"}`. Um exemplo é `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-recycle-bin"></a>Recupere registros excluídos da lixeira do Salesforce

Para consultar os registros excluídos de maneira reversível da lixeira do Salesforce, você pode especificar `readBehavior` como `queryAll`. 

### <a name="difference-between-soql-and-sql-query-syntax"></a>Diferença entre a sintaxe de consulta SOQL e SQL

Ao copiar dados do Salesforce, é possível usar a consulta SOQL ou a consulta SQL. Note que as duas têm suporte diferente de sintaxe e funcionalidade, não combine-as. É recomendável usar a consulta SOQL, que tem suporte nativo do Salesforce. A tabela a seguir lista as principais diferenças:

| Sintaxe | Modo SOQL | Modo SQL |
|:--- |:--- |:--- |
| Seleção de coluna | É necessário enumerar os campos a serem copiados na consulta, por exemplo, `SELECT field1, filed2 FROM objectname` | `SELECT *` tem suporte além da seleção de colunas. |
| Aspas | Nomes de campos/objetos não podem ser entre aspas. | Nomes de campos/objetos podem ser entre aspas, p. ex. `SELECT "id" FROM "Account"` |
| Formato de data/hora |  Consulte os detalhes [aqui](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm) e exemplos na próxima seção. | Consulte os detalhes [aqui](/sql/odbc/reference/develop-app/date-time-and-timestamp-literals) e exemplos na próxima seção. |
| Valores boolianos | Representado como `False` e `True`, p. ex. `SELECT … WHERE IsDeleted=True`. | Representado como 0 ou 1, p. ex. `SELECT … WHERE IsDeleted=1`. |
| Renomeação de coluna | Sem suporte. | Com suporte, p. ex.: `SELECT a AS b FROM …`. |
| Relação | Com suporte, p. ex. `Account_vod__r.nvs_Country__c`. | Sem suporte. |

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Recuperar dados usando um onde cláusula na coluna DateTime

Ao especificar a consulta SQL ou SOQL, preste atenção à diferença de formato DateTime. Por exemplo:

* **Exemplo de SOQL**: `SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **Exemplo de SQL**: `SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

### <a name="error-of-malformed_query-truncated"></a>Erro de MALFORMED_QUERY: truncado

Se você encontrar o erro de "MALFORMED_QUERY: TRUNCATE", normalmente, é devido a você ter uma coluna de tipo JunctionIdList nos dados e o Salesforce tem a limitação de dar suporte a esses dados com um grande número de linhas. Para mitigar, tente excluir a coluna JunctionIdList ou limitar o número de linhas a serem copiadas (você pode particionar várias execuções da atividade de cópia).

## <a name="data-type-mapping-for-salesforce"></a>Mapeamento de tipo de dados para Salesforce

Ao copiar dados do Salesforce, os seguintes mapeamentos são usados de tipos de dados do Salesforce para tipos de dados provisórios do Data Factory. Para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor, consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md).

| Tipo de dados do Salesforce | Tipo de dados provisório do Data Factory |
|:--- |:--- |
| Numeração automática |String |
| Caixa de seleção |Boolean |
| Moeda |Decimal |
| Data |Datetime |
| Data/Hora |DateTime |
| Email |String |
| ID |String |
| Relação de pesquisa |String |
| Lista de seleção múltipla |String |
| Número |Decimal |
| Porcentagem |Decimal |
| Telefone |String |
| Lista de seleção |String |
| Texto |Cadeia de caracteres |
| Área de texto |String |
| Área de texto (longo) |String |
| Área de texto (Rich) |String |
| Texto (criptografado) |String |
| URL |String |

## <a name="lookup-activity-properties"></a>Pesquisar propriedades de atividade

Para saber detalhes sobre as propriedades, verifique [Pesquisar atividade](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).