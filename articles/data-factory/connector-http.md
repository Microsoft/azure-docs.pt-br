---
title: Copiar dados de fontes HTTP usando o Azure Data Factory | Microsoft Docs
description: "Saiba como copiar dados de uma fonte HTTP local ou de nuvem para armazenamentos de dados de coletor com suporte, usando uma atividade de cópia em um pipeline do Azure Data Factory."
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
ms.date: 10/12/2017
ms.author: jingwang
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: b27e852b70651cec34b200bde362ff0efae30226
ms.contentlocale: pt-br
ms.lasthandoff: 09/25/2017

---
# <a name="copy-data-from-http-endpoint-using-azure-data-factory"></a>Copiar dados de ponto de extremidade HTTP usando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 – já disponível](v1/data-factory-http-connector.md)
> * [Versão 2 – Versão prévia](connector-http.md)

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de um ponto de extremidade HTTP. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que está em GA (disponibilidade geral), consulte [Conector de HTTP na V1](v1/data-factory-http-connector.md).

## <a name="supported-scenarios"></a>Cenários com suporte

Você pode copiar dados de uma fonte HTTP para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector de HTTP dá suporte à:

- Recuperação de dados do ponto de extremidade HTTP/S usando o método HTTP **GET** ou **POST**.
- Recuperação de dados usando as seguintes autenticações: **Anônima**, **Básica**, **Digest**, **Windows** e **ClientCertificate**.
- Cópia da resposta de HTTP no estado em que se encontra ou a análise dela com os [formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md).

A diferença entre esse conector e o [Conector de tabela da Web](connector-web-table.md) é que o segundo é usado para extrair o conteúdo de tabela da página HTML da Web.

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com atividade de cópia usando o SDK do .NET, o SDK do Python, o Azure PowerShell, a API REST ou o modelo do Azure Resource Manager. Confira o [Tutorial de atividade de cópia](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo sobre a criação de um pipeline com uma atividade de cópia.

As seções a seguir fornecem detalhes sobre as propriedades usadas para definir entidades do Data Factory específicas ao conector de HTTP.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado de HTTP:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **HttpServer**. | Sim |
| url | URL base para o Servidor Web | Sim |
| enableServerCertificateValidation | Especifique se deseja habilitar a validação do certificado SSL do servidor ao se conectar ao ponto de extremidade HTTP. | Não, o padrão é true |
| authenticationType | Especifica o tipo de autenticação. Os valores permitidos são: **Anônimo**, **Básico**, **Digest**, **Windows** e **ClientCertificate**. <br><br> Consulte as seções abaixo desta tabela para mais propriedades e amostras JSON para esses tipos de autenticação, respectivamente. | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime do Azure ou o Integration Runtime auto-hospedado (se o armazenamento de dados estiver localizado em uma rede privada). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não |

### <a name="using-basic-digest-or-windows-authentication"></a>Usando a autenticação Básica, Digest ou Windows

Defina a propriedade "authenticationType" como **Básica**, **Digest** ou **Windows** e especifique as seguintes propriedades junto com as propriedades genéricas descritas na seção anterior:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| userName | Nome de usuário para acessar o ponto de extremidade HTTP. | Sim |
| Senha | Senha do usuário (userName). Marque esse campo como SecureString. | Sim |

**Exemplo**

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "HttpServer",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "url" : "<HTTP endpoint>",
            "userName": "<username>",
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

Para usar a autenticação ClientCertificate, defina a propriedade "authenticationType" como **ClientCertificate** e especifique as propriedades a seguir junto com as propriedades genéricas descritas na seção anterior:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| embeddedCertData | Dados de certificado codificado em Base64. | Especifique `embeddedCertData` ou `certThumbprint`. |
| certThumbprint | A impressão digital do certificado que está instalado no repositório de certificados do Integration Runtime auto-hospedado do seu computador. Aplica-se apenas quando o tipo auto-hospedado do Integration Runtime é especificado em connectVia. | Especifique `embeddedCertData` ou `certThumbprint`. |
| Senha | Senha associada ao certificado. Marque esse campo como SecureString. | Não |

Se você usar "certThumbprint" para autenticação e o certificado estiver instalado no repositório pessoal do computador local, você precisará conceder a permissão de leitura para o Integration Runtime auto-hospedado:

1. Iniciar o MMC (Console de Gerenciamento Microsoft). Adicionar o snap-in **Certificados**, que tem como destino o **Computador Local**.
2. Expanda **Certificados**, **Pessoal** e clique em **Certificados**.
3. Clique com o botão direito do mouse no certificado do repositório pessoal e selecione **Todas as Tarefas** -> **Gerenciar Chaves Particulares...**
3. Na guia **Segurança**, adicione a conta de usuário, sob a qual o serviço de host de Integration Runtime (DIAHostService) está em execução, com o acesso de leitura para o certificado.

**Exemplo 1: usando certThumbprint**

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "HttpServer",
        "typeProperties":
        {
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
    "properties":
    {
        "type": "HttpServer",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "embeddedCertData": "<base64 encoded cert data>",
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

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre conjuntos de dados. Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados de HTTP.

Para copiar dados de HTTP, defina a propriedade type do conjunto de dados como **HttpFile**. Há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como: **HttpFile** | Sim |
| relativeUrl | Uma URL relativa para o recurso que contém os dados. Quando essa propriedade não é especificada, apenas a URL especificada na definição do serviço vinculado é usada. | Não |
| requestMethod | Método Http.<br/>Os valores permitidos são **Get** (padrão) ou **Post**. | Não |
| additionalHeaders | Cabeçalhos de solicitação HTTP adicionais. | Não |
| requestBody | O corpo da solicitação HTTP. | Não |
| formato | Se você quiser **recuperar dados de ponto de extremidade HTTP no estado em que se encontram**, sem analisá-los e copiá-los em um repositório baseado em arquivo, ignore a seção de formato nas definições de conjunto de dados de entrada e de saída.<br/><br/>Se você quiser analisar o conteúdo da resposta HTTP durante a cópia, há suporte para os seguintes tipos de formato de arquivo: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Defina a propriedade **type** sob formato como um desses valores. Para saber mais, veja as seções [Formato JSON](supported-file-formats-and-compression-codecs.md#json-format), [Formato de texto](supported-file-formats-and-compression-codecs.md#text-format), [Formato Avro](supported-file-formats-and-compression-codecs.md#avro-format), [Formato Orc](supported-file-formats-and-compression-codecs.md#orc-format) e [Formato Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Não |
| compactação | Especifique o tipo e o nível de compactação para os dados. Para obter mais informações, consulte [Formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Os tipos com suporte são: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>Os níveis com suporte são **Ideal** e **O mais rápido**. |Não |

**Exemplo 1: usando o método Get (padrão)**

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

**Exemplo 2: usando o método Post**

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

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte HTTP.

### <a name="http-as-source"></a>HTTP como fonte

Para copiar dados de HTTP, defina o tipo de fonte na atividade de cópia como **HttpSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type da fonte da atividade de cópia deve ser definida como: **HttpSource** | Sim |
| httpRequestTimeout | O tempo limite (TimeSpan) para a solicitação HTTP obter uma resposta. É o tempo limite para obter uma resposta e não o tempo limite para ler dados de resposta.<br/> O valor padrão é: 00:01:40  | Não |

**Exemplo:**

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
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).
