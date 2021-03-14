---
title: Copiar dados de uma origem HTTP usando Azure Data Factory
description: Saiba como copiar dados de uma fonte HTTP local ou de nuvem para armazenamentos de dados de coletor com suporte, usando uma atividade de cópia em um pipeline do Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 0462dac12d41fff667212902152b420d1460186d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383629"
---
# <a name="copy-data-from-an-http-endpoint-by-using-azure-data-factory"></a>Copiar dados de um ponto de extremidade HTTP usando o Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-http-connector.md)
> * [Versão atual](connector-http.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de um ponto de extremidade HTTP. O artigo baseia-se em [Atividade de Cópia no Azure Data Factory](copy-activity-overview.md), que apresenta uma visão geral da Atividade de Cópia.

A diferença entre esse conector HTTP, o [conector REST](connector-rest.md) e o [conector de tabela da Web](connector-web-table.md) é:

- **conector REST** dá suporte especificamente à cópia de dados de APIs RESTful; 
- O **conector HTTP** é genérico para recuperar dados de qualquer ponto de extremidade HTTP, por exemplo, para baixar o arquivo. Antes desse conector REST ser disponibilizado, talvez você use o conector HTTP para copiar dados da API RESTful, o que tem suporte, mas é menos funcional em comparação ao conector REST.
- O **conector da tabela da Web** extrai o conteúdo da tabela de uma página da Web em HTML.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector HTTP tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/coletor com suporte](copy-activity-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados de uma origem HTTP para qualquer repositório de dados do coletor com suporte. Para obter uma lista de armazenamentos de dados que o Copy Activity suporta como fontes e coletores, consulte [Armazenamentos de dados e formatos compatíveis](copy-activity-overview.md#supported-data-stores-and-formats).

Você pode usar esse conector HTTP para:

- Recuperar dados de um endpoint HTTP / S usando os métodos HTTP **GET** ou **POST**.
- Recupere dados usando uma das seguintes autenticações: **Anônimo**, **Básico**, **Resumo**, **Windows** ou **ClientCertificate**.
- Copie a resposta HTTP como está ou analise-a usando [formatos de arquivo suportados e codecs de compactação](supported-file-formats-and-compression-codecs.md).

> [!TIP]
> Para testar uma solicitação HTTP para recuperação de dados antes de configurar o conector HTTP no Data Factory, saiba mais sobre a especificação da API para os requisitos de cabeçalho e corpo. Você pode usar ferramentas como o Postman ou um navegador da Web para validar.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre propriedades que você pode usar para definir entidades do Data Factory específicas do conector HTTP.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As seguintes propriedades são suportadas para o serviço vinculado HTTP:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade **Type** deve ser definida como **HttpServer**. | Sim |
| url | A URL base para o servidor web. | Sim |
| enableServerCertificateValidation | Especifique se a validação do certificado TLS/SSL do servidor deve ser habilitada quando você se conectar a um ponto de extremidade HTTP. Se seu servidor HTTPS usa um certificado autoassinado, defina essa propriedade como **falsos**. | Não<br /> (o padrão é **true**) |
| authenticationType | Especifica o tipo de autenticação. Os valores permitidos são **Anonymous**, **Basic**, **Digest**, **Windows** e **ClientCertificate**. <br><br> Veja as seções que seguem esta tabela para mais propriedades e amostras JSON para esses tipos de autenticação. | Sim |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Saiba mais na seção [Pré-requisitos](#prerequisites). Se não especificado, o Azure Integration Runtime padrão será usado. |Não |

### <a name="using-basic-digest-or-windows-authentication"></a>Usando a autenticação Básica, Digest ou Windows

Defina a **authenticationType** propriedade **Básico**, **Digest**, ou **Windows**. Além das propriedades genéricas descritas na seção anterior, especifique as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| userName | O nome de usuário a ser usada para acessar o ponto de extremidade HTTP. | Sim |
| password | A senha do usuário (o **nome de usuário** valor). Marque esse campo como um tipo **SecureString** para armazená-lo com segurança no Data Factory. Você também pode [referenciar um segredo armazenado no Cofre de Chaves do Azure](store-credentials-in-key-vault.md). | Sim |

**Exemplo**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<HTTP endpoint>",
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

### <a name="using-clientcertificate-authentication"></a>Usando a autenticação ClientCertificate

Para usar a autenticação ClientCertificate, defina a propriedade **authenticationType** como **ClientCertificate**. Além das propriedades genéricas descritas na seção anterior, especifique as seguintes propriedades:

| Propriedade | Descrição | Necessária |
|:--- |:--- |:--- |
| embeddedCertData | Dados de certificado codificados em Base64. | Especificar **embeddedCertData** ou **certThumbprint**. |
| certThumbprint | A impressão digital do certificado que está instalado no armazenamento de certificados da sua máquina de Automação do Runtime Integration. Aplica-se apenas quando o tipo de runtime de integração auto-hospedada é especificado na propriedade **connectVia**. | Especificar **embeddedCertData** ou **certThumbprint**. |
| password | A senha associada com o certificado. Marque esse campo como um tipo **SecureString** para armazená-lo com segurança no Data Factory. Você também pode [referenciar um segredo armazenado no Cofre de Chaves do Azure](store-credentials-in-key-vault.md). | Não |

Se você usar **certThumbprint** para autenticação e o certificado estiver instalado no armazenamento pessoal do computador local, conceda permissões de leitura ao runtime de integração auto-hospedada:

1. Abra o console de gerenciamento Microsoft (MMC). Adicione a **certificados** snap-in que tem como alvo **computador Local**.
2. Expanda **certificados**  >  **pessoal** e selecione **certificados**.
3. Clique com o botão direito do mouse no certificado do repositório pessoal e selecione **todas as tarefas**  >  **gerenciar chaves privadas**.
3. Na guia **Segurança**, adicione a conta de usuário na qual o Serviço de Host do Integration Runtime (DIAHostService) está em execução, com acesso de leitura ao certificado.

**Exemplo 1: usando certThumbprint**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "certThumbprint": "<thumbprint of certificate>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo 2: usando embeddedCertData**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "embeddedCertData": "<Base64-encoded cert data>",
            "password": {
                "type": "SecureString",
                "value": "password of cert"
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

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](concepts-datasets-linked-services.md). 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As propriedades a seguir têm suporte para HTTP em `location` configurações no conjunto de conjuntos baseado em formato:

| Propriedade    | Descrição                                                  | Obrigatório |
| ----------- | ------------------------------------------------------------ | -------- |
| type        | A propriedade Type em `location` DataSet deve ser definida como **HttpServerLocation**. | Sim      |
| relativeUrl | Uma URL relativa para o recurso que contém os dados. O conector HTTP copia dados da URL combinada: `[URL specified in linked service][relative URL specified in dataset]` .   | Não       |

> [!NOTE]
> O tamanho da carga útil do pedido HTTP suportado é de cerca de 500 KB. Se o tamanho da carga útil que você deseja passar para seu ponto de extremidade da web for maior que 500 KB, considere agrupar a carga útil em partes menores.

**Exemplo:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HttpServerLocation",
                "relativeUrl": "<relative url>"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da Atividade de Cópia

Esta seção fornece uma lista de propriedades que a fonte HTTP suporta.

Para obter uma lista completa de seções e propriedades que estão disponíveis para definir atividades, consulte [Pipelines](concepts-pipelines-activities.md). 

### <a name="http-as-source"></a>HTTP como fonte

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As propriedades a seguir têm suporte para HTTP em `storeSettings` configurações na fonte de cópia baseada em formato:

| Propriedade                 | Descrição                                                  | Obrigatório |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | A propriedade Type em `storeSettings` deve ser definida como **HttpReadSettings**. | Sim      |
| requestMethod            | O método HTTP. <br>Valores permitidos são **Obtenha** (padrão) e **Post**. | Não       |
| addtionalHeaders         | Cabeçalhos de solicitação HTTP adicionais.                             | Não       |
| requestBody              | O corpo da solicitação HTTP.                               | Não       |
| httpRequestTimeout           | O tempo limite (o valor **TimeSpan**) para a solicitação HTTP para obter uma resposta. Esse valor é o tempo limite para obter uma resposta, não o tempo limite para ler os dados da resposta. O valor padrão é **01:00:40**. | Não       |
| maxConcurrentConnections | O número das conexões para se conectar ao repositório de armazenamento simultaneamente. Especifique somente quando quiser limitar a conexão simultânea com o armazenamento de dados. | Não       |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "HttpReadSettings",
                    "requestMethod": "Post",
                    "additionalHeaders": "<header key: header value>\n<header key: header value>\n",
                    "requestBody": "<body for POST HTTP request>"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Pesquisar propriedades de atividade

Para saber detalhes sobre as propriedades, verifique [Pesquisar atividade](control-flow-lookup-activity.md).

## <a name="legacy-models"></a>Modelos herdados

>[!NOTE]
>Os modelos a seguir ainda têm suporte no estado em que se encontram, para compatibilidade com versões anteriores. É recomendável usar o novo modelo mencionado nas seções acima no futuro, e a interface do usuário de criação do ADF mudou para gerar o novo modelo.

### <a name="legacy-dataset-model"></a>Modelo de conjunto de dados herdado

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade **Type** do conjunto de conjuntos deve ser definida como **httpfile**. | Sim |
| relativeUrl | Uma URL relativa para o recurso que contém os dados. Quando essa propriedade não é especificada, somente o URL especificado na definição de serviço vinculada é usado. | Não |
| requestMethod | O método HTTP. Valores permitidos são **Obtenha** (padrão) e **Post**. | Não |
| additionalHeaders | Cabeçalhos de solicitação HTTP adicionais. | Não |
| requestBody | O corpo da solicitação HTTP. | Não |
| format | Se você deseja recuperar dados do terminal HTTP como estão, sem analisá-los e, em seguida, copiar os dados para um armazenamento baseado em arquivo, ignore a seção **formato** nas definições de conjunto de dados de entrada e saída.<br/><br/>Se você desejar analisar o conteúdo da resposta HTTP durante a cópia, os seguintes tipos de formato de arquivo serão suportados: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. No **formato**, defina a propriedade **tipo** como um desses valores. Para obter mais informações, consulte [formato JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [formato de texto](supported-file-formats-and-compression-codecs-legacy.md#text-format), [formato Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [formato Orc](supported-file-formats-and-compression-codecs-legacy.md#orc-format) e [formato Parquet](supported-file-formats-and-compression-codecs-legacy.md#parquet-format). |Não |
| compactação | Especifique o tipo e o nível de compactação para os dados. Para obter mais informações, consulte [Formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/><br/>Tipos com suporte: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**.<br/>Níveis com suporte:  **ideal** e **mais rápido**. |Não |

> [!NOTE]
> O tamanho da carga útil do pedido HTTP suportado é de cerca de 500 KB. Se o tamanho da carga útil que você deseja passar para seu ponto de extremidade da web for maior que 500 KB, considere agrupar a carga útil em partes menores.

**Exemplo 1: Usando o método Get (padrão)**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        }
    }
}
```

**Exemplo 2: Usando o método Post**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "requestMethod": "Post",
            "requestBody": "<body for POST HTTP request>"
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>Modelo de origem de atividade de cópia herdado

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade **Type** da fonte da atividade de cópia deve ser definida como **httpname**. | Sim |
| httpRequestTimeout | O tempo limite (o valor **TimeSpan**) para a solicitação HTTP para obter uma resposta. Esse valor é o tempo limite para obter uma resposta, não o tempo limite para ler os dados da resposta. O valor padrão é **01:00:40**.  | Não |

**Exemplo**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<HTTP input dataset name>",
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
                "type": "HttpSource",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Próximas etapas

Para obter uma lista de armazenamentos de dados que o Copy Activity suporta como fontes e coletores no Azure Data Factory, consulte [Armazenamentos e formatos de dados compatíveis](copy-activity-overview.md#supported-data-stores-and-formats).
