---
title: Copiar dados de e para um ponto de extremidade REST usando Azure Data Factory
description: Saiba como copiar dados de uma nuvem ou fonte REST local para armazenamentos de dados de coletor com suporte ou de um armazenamento de dados de origem com suporte para um coletor REST usando uma atividade de cópia em um pipeline de Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/08/2020
ms.author: jingwang
ms.openlocfilehash: 3fc567b7d4b2efab03e5d93adda62839d47f7522
ms.sourcegitcommit: 8c8c71a38b6ab2e8622698d4df60cb8a77aa9685
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2021
ms.locfileid: "99223080"
---
# <a name="copy-data-from-and-to-a-rest-endpoint-by-using-azure-data-factory"></a>Copiar dados de e para um ponto de extremidade REST usando Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados de e para um ponto de extremidade REST. O artigo baseia-se em [Atividade de Cópia no Azure Data Factory](copy-activity-overview.md), que apresenta uma visão geral da Atividade de Cópia.

A diferença entre esse conector REST, o [conector http](connector-http.md)e o [conector de tabela da Web](connector-web-table.md) são:

- O **conector REST** oferece suporte especificamente à cópia de dados de APIs RESTful; 
- O **conector http** é genérico para recuperar dados de qualquer ponto de extremidade http, por exemplo, para baixar o arquivo. Antes desse conector REST, você pode usar o conector HTTP para copiar dados da API RESTful, que tem suporte, mas a comparação menos funcional com o conector REST.
- O **conector da tabela da Web** extrai o conteúdo da tabela de uma página da Web em HTML.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados de uma origem REST para qualquer armazenamento de dados de coletor com suporte. Você também pode copiar dados de qualquer armazenamento de dados de origem com suporte para um coletor REST. Para obter uma lista de armazenamentos de dados que o Copy Activity suporta como fontes e coletores, consulte [Armazenamentos de dados e formatos compatíveis](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, esse conector REST genérico dá suporte para:

- Copiar dados de um ponto de extremidade REST usando os métodos **Get** ou **post** e copiando dados para um ponto de extremidade REST usando os métodos **post**, **Put** ou **patch** .
- Copiar dados usando uma das seguintes autenticações: **anônima**, **básica**, entidade de **serviço do AAD** e **identidades gerenciadas para recursos do Azure**.
- **[Paginação](#pagination-support)** no APIs REST.
- Para REST como fonte, copiar a resposta JSON REST [como está](#export-json-response-as-is) ou analisá-la usando o [mapeamento de esquema](copy-activity-schema-and-type-mapping.md#schema-mapping). Somente o conteúdo de resposta na **JSON** tem suporte.

> [!TIP]
> Para testar uma solicitação para recuperação de dados antes de configurar o conector REST no Data Factory, saiba mais sobre a especificação da API para os requisitos de cabeçalho e corpo. Você pode usar ferramentas como o Postman ou um navegador da Web para validar.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre propriedades que você pode usar para definir entidades do Data Factory específicas do conector REST.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As seguintes propriedades são suportadas para o serviço vinculado REST:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade **Type** deve ser definida como **RestService**. | Sim |
| url | A URL base do serviço REST. | Sim |
| enableServerCertificateValidation | Se o certificado TLS/SSL do lado do servidor deve ser validado ao se conectar ao ponto de extremidade. | Não<br /> (o padrão é **true**) |
| authenticationType | Tipo de autenticação usado para se conectar ao serviço REST. Os valores permitidos são **Anonymous**, **Basic**, **AadServicePrincipal** e **ManagedServiceIdentity**. Consulte respectivamente as seções correspondentes abaixo em mais propriedades e exemplos. | Sim |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Saiba mais na seção [Pré-requisitos](#prerequisites). Se não especificado, essa propriedade usará o Azure Integration Runtime padrão. |Não |

### <a name="use-basic-authentication"></a>Usar autenticação básica

Defina a **authenticationType** na propriedade **Básico**. Além das propriedades genéricas descritas na seção anterior, especifique as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| userName | O nome de usuário a ser usado para acessar o ponto de extremidade REST. | Sim |
| password | A senha do usuário (o **nome de usuário** valor). Marque esse campo como um tipo **SecureString** para armazená-lo com segurança no Data Factory. Você também pode [referenciar um segredo armazenado no Cofre de Chaves do Azure](store-credentials-in-key-vault.md). | Sim |

**Exemplo**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<REST endpoint>",
            "userName": "<user name>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-aad-service-principal-authentication"></a>Usar a autenticação de entidade de serviço da Microsoft Azure Active Directory

Defina a **authenticationType** na propriedade **AadServicePrincipal**. Além das propriedades genéricas descritas na seção anterior, especifique as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| servicePrincipalId | Especifique a ID do cliente do aplicativo do Azure Active Directory. | Sim |
| servicePrincipalKey | Especifique a chave do aplicativo do Azure Active Directory. Marque esse campo como **SecureString** para armazená-lo com segurança no Data Factory ou [referencie um segredo armazenado no Cofre de Chaves do Azure](store-credentials-in-key-vault.md). | Sim |
| locatário | Especifique as informações de locatário (domínio nome ou ID do Locatário) em que o aplicativo reside. Para recuperá-lo, passe o mouse no canto superior direito do portal do Azure. | Sim |
| aadResourceId | Especifique o recurso do AAD que você está solicitando para autorização, por exemplo, `https://management.core.windows.net` .| Sim |
| azureCloudType | Para autenticação de entidade de serviço, especifique o tipo de ambiente de nuvem do Azure no qual seu aplicativo do AAD está registrado. <br/> Os valores permitidos são **AzurePublic**, **AzureChina**, **AzureUsGovernment** e **AzureGermany**. Por padrão, o ambiente de nuvem do data factory é usado. | Não |

**Exemplo**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Usar identidades gerenciadas para autenticação de recursos do Azure

Defina a **authenticationType** na propriedade **ManagedServiceIdentity**. Além das propriedades genéricas descritas na seção anterior, especifique as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| aadResourceId | Especifique o recurso do AAD que você está solicitando para autorização, por exemplo, `https://management.core.windows.net` .| Sim |

**Exemplo**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "ManagedServiceIdentity",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Esta seção fornece uma lista de propriedades que o conjunto de dados REST suporta. 

Para obter uma lista completa de seções e propriedades disponíveis para definição de conjuntos de dados, consulte [Conjuntos de dados e serviços vinculados](concepts-datasets-linked-services.md). 

Para copiar dados do REST, há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade **tipo** do conjunto de dados deve ser definida como **RestResource**. | Sim |
| relativeUrl | Uma URL relativa para o recurso que contém os dados. Quando essa propriedade não é especificada, somente o URL especificado na definição de serviço vinculada é usado. O conector HTTP copia dados da URL combinada: `[URL specified in linked service]/[relative URL specified in dataset]` . | Não |

Se você definiu `requestMethod` , `additionalHeaders` `requestBody` e `paginationRules` no DataSet, ainda terá suporte como está, enquanto você é sugerido para usar o novo modelo na atividade no futuro.

**Exemplo:**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "typeProperties": {
            "relativeUrl": "<relative url>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da Atividade de Cópia

Esta seção fornece uma lista das propriedades com suporte pela fonte REST e pelo coletor.

Para obter uma lista completa de seções e propriedades que estão disponíveis para definir atividades, consulte [Pipelines](concepts-pipelines-activities.md). 

### <a name="rest-as-source"></a>REST como fonte

As propriedades a seguir têm suporte na seção **source** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | O **tipo** de propriedade da fonte da atividade de cópia deve ser definida como: **RestSource**. | Sim |
| requestMethod | O método HTTP. Os valores permitidos são **Get** (padrão) e **post**. | Não |
| additionalHeaders | Cabeçalhos de solicitação HTTP adicionais. | Não |
| requestBody | O corpo da solicitação HTTP. | Não |
| paginationRules | As regras de paginação para compor as próximas solicitações de página. Consulte a seção [suporte à paginação](#pagination-support) em detalhes. | Não |
| httpRequestTimeout | O tempo limite (o valor **TimeSpan**) para a solicitação HTTP para obter uma resposta. Esse valor é o tempo limite para obter uma resposta, não o tempo limite para ler os dados da resposta. O valor padrão é **01:00:40**.  | Não |
| requestInterval | O tempo de espera antes de enviar a solicitação para a próxima página. O valor padrão é **00:00:01** |  Não |

>[!NOTE]
>O conector REST ignora qualquer cabeçalho "Accept" especificado em `additionalHeaders` . Como o conector REST só dá suporte à resposta em JSON, ele gerará automaticamente um cabeçalho de `Accept: application/json` .

**Exemplo 1: usando o método Get com paginação**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
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
                "type": "RestSource",
                "additionalHeaders": {
                    "x-user-defined": "helloworld"
                },
                "paginationRules": {
                    "AbsoluteUrl": "$.paging.next"
                },
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Exemplo 2: Usando o método Post**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
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
                "type": "RestSource",
                "requestMethod": "Post",
                "requestBody": "<body for POST REST request>",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="rest-as-sink"></a>REST como coletor

As propriedades a seguir têm suporte na seção **sink** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade **Type** do coletor da atividade de cópia deve ser definida como **RestSink**. | Sim |
| requestMethod | O método HTTP. Os valores permitidos são **post** (padrão), **Put** e **patch**. | Não |
| additionalHeaders | Cabeçalhos de solicitação HTTP adicionais. | Não |
| httpRequestTimeout | O tempo limite (o valor **TimeSpan**) para a solicitação HTTP para obter uma resposta. Esse valor é o tempo limite para obter uma resposta, não o tempo limite para gravar os dados. O valor padrão é **01:00:40**.  | Não |
| requestInterval | O tempo de intervalo entre solicitações diferentes em milissegundos. O valor do intervalo de solicitação deve ser um número entre [10, 60000]. |  Não |
| httpCompressionType | Tipo de compactação HTTP a ser usado ao enviar dados com o nível de compactação ideal. Os valores permitidos são **None** e **gzip**. | Não |
| writeBatchSize | Número de registros a serem gravados no coletor REST por lote. O valor padrão é 10000. | Não |

O conector REST como coletor funciona com as APIs REST que aceitam JSON. Os dados serão enviados em JSON com o padrão a seguir. Conforme necessário, você pode usar o mapeamento de [esquema](copy-activity-schema-and-type-mapping.md#schema-mapping) de atividade de cópia para remodelar os dados de origem para estar de acordo com a carga esperada pela API REST.

```json
[
    { <data object> },
    { <data object> },
    ...
]
```

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<REST output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "RestSink",
                "requestMethod": "POST",
                "httpRequestTimeout": "00:01:40",
                "requestInterval": 10,
                "writeBatchSize": 10000,
                "httpCompressionType": "none",
            },
        }
    }
]
```

## <a name="pagination-support"></a>Suporte à paginação

Ao copiar dados de APIs REST, normalmente, a API REST limita seu tamanho de carga de resposta de uma única solicitação em um número razoável; Embora seja possível retornar uma grande quantidade de dados, ele divide o resultado em várias páginas e exige que os chamadores enviem solicitações consecutivas para obter a próxima página do resultado. Geralmente, a solicitação para uma página é dinâmica e composta por informações retornadas da resposta de página anterior.

Esse conector genérico REST suporta os seguintes padrões de paginação: 

* Valor da propriedade da URL absoluta ou relativa da próxima solicitação no corpo da resposta atual
* URL absoluta ou relativa da próxima solicitação = valor do cabeçalho nos cabeçalhos de resposta atuais
* Próxima solicitação de consulta de parâmetro = valor de propriedade no corpo de resposta atual
* Próxima solicitação de consulta de parâmetro = valor de cabeçalho nos cabeçalhos de resposta atuais
* Próxima solicitação de cabeçalho = valor de propriedade no corpo de resposta atual
* Próxima solicitação de cabeçalho = valor de cabeçalho nos cabeçalhos de resposta atuais

**As regras de paginação** são definidas como um dicionário no conjunto de dados, que contém um ou mais pares chave-valor que diferenciam maiúsculas de minúsculas. A configuração será usada para gerar a solicitação a partir da segunda página. O conector deixará de iteração quando obter o código de status HTTP 204 (sem conteúdo) ou qualquer uma das expressões JSONPath em "paginationRules" retornar NULL.

O **Suporte para chaves** nas regras de paginação:

| Chave | Descrição |
|:--- |:--- |
| AbsoluteUrl | Indica a URL para emitir a próxima solicitação. Ele pode ser **uma URL absoluta ou relativa**. |
| QueryParameters.*request_query_parameter* OU QueryParameters['request_query_parameter'] | "request_query_parameter" é definido pelo usuário, que faz referência a um nome de parâmetro de consulta na próxima URL de solicitação HTTP. |
| Headers.*request_header* OU Headers['request_header'] | "request_header" é definido pelo usuário, que faz referência a um nome de cabeçalho na próxima solicitação HTTP. |

Os **Valores com suporte** nas regras de paginação:

| Valor | Descrição |
|:--- |:--- |
| Headers.*response_header* OU Headers['response_header'] | "response_header" é definido pelo usuário, que faz referência a um nome de cabeçalho na resposta HTTP atual, o valor que será usado para emitir a próxima solicitação. |
| Uma expressão JSONPath começando com "$" (que representa a raiz do corpo da resposta) | O corpo da resposta deve conter apenas um objeto JSON. A expressão JSONPath deve retornar um único valor primitivo, que será usado para emitir a próxima solicitação. |

**Exemplo:**

O Facebook API do Graph retorna a resposta na estrutura a seguir, caso em que a URL da próxima página é representada em **_paginação. avançar_* _:

```json
{
    "data": [
        {
            "created_time": "2017-12-12T14:12:20+0000",
            "name": "album1",
            "id": "1809938745705498_1809939942372045"
        },
        {
            "created_time": "2017-12-12T14:14:03+0000",
            "name": "album2",
            "id": "1809938745705498_1809941802371859"
        },
        {
            "created_time": "2017-12-12T14:14:11+0000",
            "name": "album3",
            "id": "1809938745705498_1809941879038518"
        }
    ],
    "paging": {
        "cursors": {
            "after": "MTAxNTExOTQ1MjAwNzI5NDE=",
            "before": "NDMyNzQyODI3OTQw"
        },
        "previous": "https://graph.facebook.com/me/albums?limit=25&before=NDMyNzQyODI3OTQw",
        "next": "https://graph.facebook.com/me/albums?limit=25&after=MTAxNTExOTQ1MjAwNzI5NDE="
    }
}
```

A configuração de origem da atividade de cópia REST correspondente `paginationRules` , especialmente, é a seguinte:

```json
"typeProperties": {
    "source": {
        "type": "RestSource",
        "paginationRules": {
            "AbsoluteUrl": "$.paging.next"
        },
        ...
    },
    "sink": {
        "type": "<sink type>"
    }
}
```

## <a name="use-oauth"></a>Usar o OAuth
Esta seção descreve como usar um modelo de solução para copiar dados do conector REST para Azure Data Lake Storage no formato JSON usando o OAuth. 

### <a name="about-the-solution-template"></a>Sobre o modelo de solução

O modelo contém duas atividades:
- _ A atividade *Web** recupera o token de portador e, em seguida, passa-o para a atividade de cópia subsequente como autorização.
- A atividade de **cópia** copia dados do REST para o Azure data Lake Storage.

O modelo define dois parâmetros:
- **SinkContainer** é o caminho da pasta raiz para onde os dados são copiados em seu Azure data Lake Storage. 
- **SinkDirectory** é o caminho do diretório sob a raiz onde os dados são copiados em seu Azure data Lake Storage. 

### <a name="how-to-use-this-solution-template"></a>Como usar este modelo de solução

1. Vá para a **cópia do REST ou http usando** o modelo OAuth. Crie uma nova conexão para a conexão de origem. 
    ![Criar novas conexões](media/solution-template-copy-from-rest-or-http-using-oauth/source-connection.png)

    Abaixo estão as principais etapas para as novas configurações de serviço vinculado (REST):
    
     1. Em **URL base**, especifique o parâmetro de URL para seu próprio serviço REST de origem. 
     2. Para **tipo de autenticação**, escolha *anônimo*.
        ![Nova conexão REST](media/solution-template-copy-from-rest-or-http-using-oauth/new-rest-connection.png)

2. Crie uma nova conexão para a conexão de destino.  
    ![Nova conexão Gen2](media/solution-template-copy-from-rest-or-http-using-oauth/destination-connection.png)

3. Selecione **Usar este modelo**.
    ![Usar este modelo](media/solution-template-copy-from-rest-or-http-using-oauth/use-this-template.png)

4. Você verá o pipeline criado conforme mostrado no exemplo a seguir:  ![ captura de tela mostra o pipeline criado a partir do modelo.](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline.png)

5. Selecione atividade **da Web** . Em **configurações**, especifique a **URL**, o **método**, os **cabeçalhos** e o **corpo** correspondentes para recuperar o token de portador OAuth da API de logon do serviço do qual você deseja copiar dados. O espaço reservado no modelo demonstra um exemplo de OAuth Azure Active Directory (AAD). Observe que a autenticação do AAD tem suporte nativo do conector REST, aqui está apenas um exemplo para o fluxo OAuth. 

    | Propriedade | Descrição |
    |:--- |:--- |:--- |
    | URL |Especifique a URL da qual recuperar o token de portador OAuth. por exemplo, no exemplo, ele é https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/token |. 
    | Método | O método HTTP. Os valores permitidos são **post** e **Get**. | 
    | Cabeçalhos | O cabeçalho é definido pelo usuário, que faz referência a um nome de cabeçalho na solicitação HTTP. | 
    | Corpo | O corpo da solicitação HTTP. | 

    ![Pipeline](media/solution-template-copy-from-rest-or-http-using-oauth/web-settings.png)

6. Em **copiar dados** atividade, selecione a guia *origem* , você pode ver que o token de portador (access_token) recuperado da etapa anterior seria passado para a atividade copiar dados como **autorização** em cabeçalhos adicionais. Confirme as configurações das propriedades a seguir antes de iniciar uma execução de pipeline.

    | Propriedade | Descrição |
    |:--- |:--- |:--- | 
    | Método de solicitação | O método HTTP. Valores permitidos são **Obtenha** (padrão) e **Post**. | 
    | Cabeçalhos adicionais | Cabeçalhos de solicitação HTTP adicionais.| 

   ![Autenticação de origem de cópia](media/solution-template-copy-from-rest-or-http-using-oauth/copy-data-settings.png)

7. Selecione **Depurar**, insira os **Parâmetros** e, em seguida, selecione **Concluir**.
   ![Execução de pipeline](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline-run.png) 

8. Quando a execução do pipeline for concluída com êxito, você verá o resultado semelhante ao exemplo a seguir: ![ resultado da execução do pipeline](media/solution-template-copy-from-rest-or-http-using-oauth/run-result.png) 

9. Clique no ícone de "saída" do webactivity na coluna **ações** , você verá o access_token retornado pelo serviço.

   ![Saída de token](media/solution-template-copy-from-rest-or-http-using-oauth/token-output.png) 

10. Clique no ícone de "entrada" do CopyActivity na coluna **ações** , você verá que o access_token recuperado pelo webactivity é passado para CopyActivity para autenticação. 

    ![Entrada de token](media/solution-template-copy-from-rest-or-http-using-oauth/token-input.png)
        
    >[!CAUTION] 
    >Para evitar que o token seja registrado em texto sem formatação, habilite "saída segura" na atividade da Web e "entrada segura" na atividade de cópia.


## <a name="export-json-response-as-is"></a>Exportar a resposta JSON como ela aparece

Você pode usar esse conector REST para exportar a resposta JSON de API REST como ela aparece para vários armazenamentos baseados em arquivo. Para efetuar essa cópia independente de esquema, ignore a seção da "estrutura" (também chamada de *esquema*) no conjunto de dados e mapeamento de esquema na atividade de cópia.

## <a name="schema-mapping"></a>Mapeamento de esquema

Para copiar dados do ponto de extremidade REST para o coletor de tabela, confira o [mapeamento do esquema](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Próximas etapas

Para obter uma lista de armazenamentos de dados que o Copy Activity suporta como fontes e coletores no Azure Data Factory, consulte [Armazenamentos e formatos de dados compatíveis](copy-activity-overview.md#supported-data-stores-and-formats).
