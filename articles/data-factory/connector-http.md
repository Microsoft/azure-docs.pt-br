---
title: Copiar dados de uma fonte HTTP usando o Azure Data Factory | Microsoft Docs
description: Saiba como copiar dados de uma fonte HTTP local ou de nuvem para armazenamentos de dados de coletor com suporte, usando uma atividade de cópia em um pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: 87505081f16008dff7da1f567c1265c695f3f0ab
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60653762"
---
# <a name="copy-data-from-an-http-endpoint-by-using-azure-data-factory"></a>Copiar dados de um ponto de extremidade HTTP usando o Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-http-connector.md)
> * [Versão atual](connector-http.md)

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de um ponto de extremidade HTTP. O artigo baseia-se em [Atividade de Cópia no Azure Data Factory](copy-activity-overview.md), que apresenta uma visão geral da Atividade de Cópia.

A diferença entre esse conector HTTP, o [conector REST](connector-rest.md) e o [conector de tabela da Web](connector-web-table.md) é:

- **conector REST** dá suporte especificamente à cópia de dados de APIs RESTful; 
- O **conector HTTP** é genérico para recuperar dados de qualquer ponto de extremidade HTTP, por exemplo, para baixar o arquivo. Antes desse conector REST ser disponibilizado, talvez você use o conector HTTP para copiar dados da API RESTful, o que tem suporte, mas é menos funcional em comparação ao conector REST.
- O **conector da tabela da Web** extrai o conteúdo da tabela de uma página da Web em HTML.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados de uma origem HTTP para qualquer repositório de dados do coletor com suporte. Para obter uma lista de armazenamentos de dados que o Copy Activity suporta como fontes e coletores, consulte [Armazenamentos de dados e formatos compatíveis](copy-activity-overview.md#supported-data-stores-and-formats).

Você pode usar esse conector HTTP para:

- Recuperar dados de um endpoint HTTP / S usando os métodos HTTP **GET** ou **POST**.
- Recupere dados usando uma das seguintes autenticações: **Anônima**, **Básica**, **Digest**, **Windows** ou **ClientCertificate**.
- Copie a resposta HTTP como está ou analise-a usando [formatos de arquivo suportados e codecs de compactação](supported-file-formats-and-compression-codecs.md).

> [!TIP]
> Para testar uma solicitação HTTP para recuperação de dados antes de configurar o conector HTTP no Data Factory, saiba mais sobre a especificação da API para os requisitos de cabeçalho e corpo. Você pode usar ferramentas como o Postman ou um navegador da Web para validar.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre propriedades que você pode usar para definir entidades do Data Factory específicas do conector HTTP.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As seguintes propriedades são suportadas para o serviço vinculado HTTP:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | O **tipo** propriedade deve ser definida como **HttpServer**. | Sim |
| url | A URL base para o servidor web. | Sim |
| enableServerCertificateValidation | Especifique se deseja ativar a validação do certificado SSL do servidor ao se conectar a um terminal HTTP. Se seu servidor HTTPS usa um certificado autoassinado, defina essa propriedade como **falsos**. | Não <br /> (o padrão é **verdadeiro**) |
| authenticationType | Especifica o tipo de autenticação. Os valores permitidos são **Anonymous**, **Basic**, **Digest**, **Windows** e **ClientCertificate**. <br><br> Veja as seções que seguem esta tabela para mais propriedades e amostras JSON para esses tipos de autenticação. | Sim |
| connectVia | O [Tempo de Integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Tempo de Execução de Integração do Azure ou um Tempo de Execução de Integração auto-hospedado (se o seu armazenamento de dados estiver localizado em uma rede privada). Se não especificado, essa propriedade usará o tempo de execução de integração do Azure padrão. |Não  |

### <a name="using-basic-digest-or-windows-authentication"></a>Usando a autenticação Básica, Digest ou Windows

Defina a **authenticationType** propriedade **Básico**, **Digest**, ou **Windows**. Além das propriedades genéricas descritas na seção anterior, especifique as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| userName | O nome de usuário a ser usada para acessar o ponto de extremidade HTTP. | Sim |
| Senha | A senha do usuário (o **nome de usuário** valor). Marque esse campo como um tipo **SecureString** para armazená-lo com segurança no Data Factory. Você também pode [referenciar um segredo armazenado no Cofre de Chaves do Azure](store-credentials-in-key-vault.md). | Sim |

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

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| embeddedCertData | Dados de certificado codificados em Base64. | Especificar **embeddedCertData** ou **certThumbprint**. |
| certThumbprint | A impressão digital do certificado que está instalado no armazenamento de certificados da sua máquina de Automação do Runtime Integration. Aplica-se apenas quando o tipo de tempo de execução de integração auto-hospedado é especificado na propriedade **connectVia**. | Especificar **embeddedCertData** ou **certThumbprint**. |
| Senha | A senha associada com o certificado. Marque esse campo como um tipo **SecureString** para armazená-lo com segurança no Data Factory. Você também pode [referenciar um segredo armazenado no Cofre de Chaves do Azure](store-credentials-in-key-vault.md). | Não  |

Se você usar **certThumbprint** para autenticação e o certificado estiver instalado no armazenamento pessoal do computador local, conceda permissões de leitura ao Tempo de Integração auto-hospedado:

1. Abra o Console de gerenciamento Microsoft (MMC). Adicione a **certificados** snap-in que tem como alvo **computador Local**.
2. Expanda **Certificados** > **Pessoal** e, em seguida, selecione **Certificados**.
3. Clique com o botão direito do mouse no certificado do armazenamento pessoal e selecione **Todas as Tarefas** > **Gerenciar Chaves Particulares**.
3. Na guia **Segurança**, adicione a conta de usuário na qual o Serviço de Host do Tempo de Integração do Integration (DIAHostService) está em execução, com acesso de leitura ao certificado.

**Exemplo 1: Usar certThumbprint**

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

**Exemplo 2: Usar embeddedCertData**

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

Esta seção fornece uma lista de propriedades que o conjunto de dados HTTP suporta. 

Para obter uma lista completa de seções e propriedades disponíveis para definição de conjuntos de dados, consulte [Conjuntos de dados e serviços vinculados](concepts-datasets-linked-services.md). 

Para copiar dados do HTTP, defina a propriedade **tipo** do conjunto de dados para **HttpFile**. Há suporte para as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | O **tipo** propriedade do conjunto de dados deve ser definida como **HttpFile**. | Sim |
| relativeUrl | Uma URL relativa para o recurso que contém os dados. Quando essa propriedade não é especificada, somente o URL especificado na definição de serviço vinculada é usado. | Não  |
| requestMethod | O método HTTP. Valores permitidos são **Obtenha** (padrão) e **Post**. | Não  |
| additionalHeaders | Cabeçalhos de solicitação HTTP adicionais. | Não  |
| requestBody | O corpo da solicitação HTTP. | Não  |
| formato | Se você deseja recuperar dados do terminal HTTP como estão, sem analisá-los e, em seguida, copiar os dados para um armazenamento baseado em arquivo, ignore a seção **formato** nas definições de conjunto de dados de entrada e saída.<br/><br/>Se você quiser analisar o conteúdo da resposta HTTP durante a cópia, há suporte para os seguintes tipos de formato de arquivo: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. No **formato**, defina a propriedade **tipo** como um desses valores. Para obter mais informações, consulte [formato JSON](supported-file-formats-and-compression-codecs.md#json-format), [formato de texto](supported-file-formats-and-compression-codecs.md#text-format), [formato Avro](supported-file-formats-and-compression-codecs.md#avro-format), [formato Orc](supported-file-formats-and-compression-codecs.md#orc-format) e [formato Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Não  |
| compactação | Especifique o tipo e o nível de compactação para os dados. Para obter mais informações, consulte [Formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md#compression-support).<br/><br/>Tipos com suporte: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>Níveis compatíveis:  **Ideal** e **Mais Rápido**. |Não  |

> [!NOTE]
> O tamanho da carga útil do pedido HTTP suportado é de cerca de 500 KB. Se o tamanho da carga útil que você deseja passar para seu ponto de extremidade da web for maior que 500 KB, considere agrupar a carga útil em partes menores.

**Exemplo 1: Usar o método Get (padrão)**

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

**Exemplo 2: Usar o método Post**

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

## <a name="copy-activity-properties"></a>Propriedades da Atividade de Cópia

Esta seção fornece uma lista de propriedades que a fonte HTTP suporta.

Para obter uma lista completa de seções e propriedades que estão disponíveis para definir atividades, consulte [Pipelines](concepts-pipelines-activities.md). 

### <a name="http-as-source"></a>HTTP como fonte

Para copiar dados de HTTP, defina **tipo de fonte** na atividade de cópia como **HttpSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade **tipo** da origem da atividade de cópia deve ser configurada para **HttpSource**. | Sim |
| httpRequestTimeout | O tempo limite (o valor **TimeSpan**) para a solicitação HTTP para obter uma resposta. Esse valor é o tempo limite para obter uma resposta, não o tempo limite para ler os dados da resposta. O valor padrão é **01:00:40**.  | Não  |

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
