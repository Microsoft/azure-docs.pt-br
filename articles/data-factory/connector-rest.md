---
title: Copiar dados de uma fonte REST usando o Azure Data Factory | Microsoft Docs
description: Saiba como copiar dados de uma nuvem ou fonte REST local para armazenamentos de dados de coletor com suporte, usando uma atividade de cópia em um pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: jingwang
ms.openlocfilehash: ee47f464c59bd9deed98671f19cfcc6d2c3c1b39
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60546617"
---
# <a name="copy-data-from-a-rest-endpoint-by-using-azure-data-factory"></a>Copiar dados de um ponto de extremidade REST usando o Azure Data Factory

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de um ponto de extremidade REST. O artigo baseia-se em [Atividade de Cópia no Azure Data Factory](copy-activity-overview.md), que apresenta uma visão geral da Atividade de Cópia.

A diferença entre esse conector REST, o [conector HTTP](connector-http.md) e o [conector da tabela da Web](connector-web-table.md) é:

- o **conector REST** especificamente dá suporte à cópia de dados do APIs RESTful; 
- O **conector HTTP** é genérico para recuperar dados de qualquer ponto de extremidade HTTP como, por exemplo, para fazer o download. Antes desse conector REST se tornar disponível, você pode usar o conector HTTP para copiar dados do API RESTful, que é um suporte menos funcional em comparação ao conector REST.
- O **conector da tabela da Web** extrai o conteúdo da tabela de uma página da Web em HTML.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados de uma origem REST para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados que o Copy Activity suporta como fontes e coletores, consulte [Armazenamentos de dados e formatos compatíveis](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, esse conector REST genérico dá suporte para:

- Recuperação de dados do ponto de extremidade REST usando os métodos **GET** ou **POST**.
- Recuperação de dados usando uma das seguintes autenticações: **Anônimo**, **Básico**, **Entidade de serviço da Microsoft Azure Active Directory** e  **identidades gerenciadas para recursos do Azure**.
- **[Paginação](#pagination-support)** no APIs REST.
- Copiar a resposta JSON do REST [como aparece](#export-json-response-as-is) ou analisá-la usando um [mapeamento de esquema](copy-activity-schema-and-type-mapping.md#schema-mapping). Somente o conteúdo de resposta na **JSON** tem suporte.

> [!TIP]
> Para testar uma solicitação para recuperação de dados antes de configurar o conector REST no Data Factory, saiba mais sobre a especificação da API para os requisitos de cabeçalho e corpo. Você pode usar ferramentas como o Postman ou um navegador da Web para validar.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre propriedades que você pode usar para definir entidades do Data Factory específicas do conector REST.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As seguintes propriedades são suportadas para o serviço vinculado REST:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | O **tipo** propriedade deve ser definida como **RestService**. | Sim |
| url | A URL base do serviço REST. | Sim |
| enableServerCertificateValidation | Se for necessário validar o certificado SSL no lado do servidor ao se conectar ao ponto de extremidade. | Não <br /> (o padrão é **verdadeiro**) |
| authenticationType | Tipo de autenticação usado para se conectar ao serviço REST. Os valores permitidos são **Anônimo**, **Básico**, **Windows** e **ManagedServiceIdentity**. Consulte respectivamente as seções correspondentes abaixo em mais propriedades e exemplos. | Sim |
| connectVia | O [Tempo de Integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Tempo de Execução de Integração do Azure ou um Tempo de Execução de Integração auto-hospedado (se o seu armazenamento de dados estiver localizado em uma rede privada). Se não especificado, essa propriedade usará o tempo de execução de integração do Azure padrão. |Não  |

### <a name="use-basic-authentication"></a>Usar autenticação básica

Defina a **authenticationType** na propriedade **Básico**. Além das propriedades genéricas descritas na seção anterior, especifique as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| userName | O nome de usuário a ser usado para acessar o ponto de extremidade REST. | Sim |
| Senha | A senha do usuário (o **nome de usuário** valor). Marque esse campo como um tipo **SecureString** para armazená-lo com segurança no Data Factory. Você também pode [referenciar um segredo armazenado no Cofre de Chaves do Azure](store-credentials-in-key-vault.md). | Sim |

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

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| servicePrincipalId | Especifique a ID do cliente do aplicativo do Azure Active Directory. | Sim |
| servicePrincipalKey | Especifique a chave do aplicativo do Azure Active Directory. Marque esse campo como **SecureString** para armazená-lo com segurança no Data Factory ou [referencie um segredo armazenado no Cofre de Chaves do Azure](store-credentials-in-key-vault.md). | Sim |
| tenant | Especifique as informações de locatário (domínio nome ou ID do Locatário) em que o aplicativo reside. Para recuperá-lo, passe o mouse no canto superior direito do portal do Azure. | Sim |
| aadResourceId | Especifique o recurso do AAD ao qual você está solicitando autorização, exemplo `https://management.core.windows.net`.| Sim |

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

### <a name="managed-identity"></a> Usar identidades gerenciadas para autenticação de recursos do Azure

Defina a **authenticationType** na propriedade **ManagedServiceIdentity**. Além das propriedades genéricas descritas na seção anterior, especifique as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| aadResourceId | Especifique o recurso do AAD ao qual você está solicitando autorização, exemplo `https://management.core.windows.net`.| Sim |

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

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade **tipo** do conjunto de dados deve ser definida como**RestResource**. | Sim |
| relativeUrl | Uma URL relativa para o recurso que contém os dados. Quando essa propriedade não é especificada, somente o URL especificado na definição de serviço vinculada é usado. | Não  |
| requestMethod | O método HTTP. Valores permitidos são **Obtenha** (padrão) e **Post**. | Não  |
| additionalHeaders | Cabeçalhos de solicitação HTTP adicionais. | Não  |
| requestBody | O corpo da solicitação HTTP. | Não  |
| paginationRules | As regras de paginação para compor as próximas solicitações de página. Consulte a seção [suporte à paginação](#pagination-support) em detalhes. | Não  |

**Exemplo 1: Usando o método Get com a paginação**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "additionalHeaders": {
                "x-user-defined": "helloworld"
            },
            "paginationRules": {
                "AbsoluteUrl": "$.paging.next"
            }
        }
    }
}
```

**Exemplo 2: Usar o método Post**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "requestMethod": "Post",
            "requestBody": "<body for POST REST request>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da Atividade de Cópia

Esta seção fornece uma lista de propriedades que a fonte REST suporta.

Para obter uma lista completa de seções e propriedades que estão disponíveis para definir atividades, consulte [Pipelines](concepts-pipelines-activities.md). 

### <a name="rest-as-source"></a>REST como fonte

As propriedades a seguir têm suporte na seção **source** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | O **tipo** de propriedade da fonte da atividade de cópia deve ser definida como: **RestSource**. | Sim |
| httpRequestTimeout | O tempo limite (o valor **TimeSpan**) para a solicitação HTTP para obter uma resposta. Esse valor é o tempo limite para obter uma resposta, não o tempo limite para ler os dados da resposta. O valor padrão é **01:00:40**.  | Não  |
| requestInterval | O tempo de espera antes de enviar a solicitação para a próxima página. O valor padrão é **00:00:01** |  Não  |

**Exemplo**

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
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="pagination-support"></a>Suporte à paginação

Normalmente, a API REST limita o seu tamanho de conteúdo de resposta de uma única solicitação em um número razoável; enquanto para retornar uma grande quantidade de dados, ele divide o resultado em várias páginas e exige que os clientes enviem solicitações consecutivas para obter a próxima página do resultado. Geralmente, a solicitação para uma página é dinâmica e composta por informações retornadas da resposta de página anterior.

Esse conector genérico REST suporta os seguintes padrões de paginação: 

* URL absoluta ou relativa da próxima solicitação = valor da propriedade no corpo de resposta atual
* URL absoluta ou relativa da próxima solicitação = valor de cabeçalho em cabeçalhos de resposta atual
* Próxima solicitação de consulta de parâmetro = valor de propriedade no corpo de resposta atual
* Próxima solicitação de consulta de parâmetro = valor de cabeçalho nos cabeçalhos de resposta atuais
* Próxima solicitação de cabeçalho = valor de propriedade no corpo de resposta atual
* Próxima solicitação de cabeçalho = valor de cabeçalho nos cabeçalhos de resposta atuais

As **Regras de paginação** são definidos como um dicionário no conjunto de dados que contêm um ou mais pares de chave-valor com distinção entre maiúsculas de minúsculas. A configuração será usada para gerar a solicitação a partir da segunda página. O conector irá interromper a iteração quando obter o código de status HTTP 204 (sem conteúdo) ou qualquer uma das expressões JSONPath em "paginationRules" retornar nula.

O **Suporte para chaves** nas regras de paginação:

| Chave | DESCRIÇÃO |
|:--- |:--- |
| AbsoluteUrl | Indica a URL para emitir a próxima solicitação. Ele pode ser **URL absoluta ou relativa URL**. |
| QueryParameters.*request_query_parameter* OU QueryParameters['request_query_parameter'] | O "request_query_parameter" é definido pelo usuário e faz referência a um nome de parâmetro de consulta na URL da próxima solicitação HTTP. |
| Headers.*request_header* OU Headers['request_header'] | O "request_query_parameter" é definido pelo usuário e faz referência a um nome de parâmetro de cabeçalho da próxima solicitação HTTP. |

Os **Valores com suporte** nas regras de paginação:

| Value | DESCRIÇÃO |
|:--- |:--- |
| Headers.*response_header* OU Headers['response_header'] | O "response_header" é definido pelo usuário que faz referência a um nome de cabeçalho na resposta HTTP atual, o valor que será usado para emitir a próxima solicitação. |
| Uma expressão JSONPath começando com "$" (que representa a raiz do corpo da resposta) | O corpo da resposta deve conter apenas um objeto JSON. A expressão JSONPath deve retornar um único valor primitivo, que será usado para emitir a próxima solicitação. |

**Exemplo:**

O API do Graph do Facebook retorna a resposta na próxima estrutura, em cada casa a URL da próxima página é representada por ***paging.next***:

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

A configuração do conjunto de dados REST correspondente, especialmente o `paginationRules`, é da seguinte maneira:

```json
{
    "name": "MyFacebookAlbums",
    "properties": {
            "type": "RestResource",
            "typeProperties": {
                "relativeUrl": "albums",
                "paginationRules": {
                    "AbsoluteUrl": "$.paging.next"
                }
            },
            "linkedServiceName": {
                "referenceName": "MyRestService",
                "type": "LinkedServiceReference"
            }
    }
}
```

## <a name="export-json-response-as-is"></a>Exportar a resposta JSON como ela aparece

Você pode usar esse conector REST para exportar a resposta JSON de API REST como ela aparece para vários armazenamentos baseados em arquivo. Para atingir essa cópia independente de esquema, ignore a seção da "estrutura" (também chamada de *esquema*) no conjunto de dados e o mapeamento de esquema na atividade de cópia.

## <a name="schema-mapping"></a>Mapeamento de esquema

Para copiar dados do ponto de extremidade REST para o coletor de tabela, confira o [mapeamento do esquema](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Próximas etapas

Para obter uma lista de armazenamentos de dados que o Copy Activity suporta como fontes e coletores no Azure Data Factory, consulte [Armazenamentos e formatos de dados compatíveis](copy-activity-overview.md#supported-data-stores-and-formats).
