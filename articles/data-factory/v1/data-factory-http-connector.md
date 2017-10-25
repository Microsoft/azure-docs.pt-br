---
title: "Mover dados de uma fonte HTTP – Azure | Microsoft Docs"
description: Saiba mais sobre como mover dados de uma origem HTTP local ou na nuvem usando o Azure Data Factory.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 6765939785f73b44d136fe4ed9d1a35e67fe4111
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="move-data-from-an-http-source-using-azure-data-factory"></a>Mover dados de uma origem HTTP usando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 – já disponível](data-factory-http-connector.md)
> * [Versão 2 – Versão prévia](../connector-http.md)

> [!NOTE]
> Este artigo se aplica à versão 1 do Data Factory, que está com GA (disponibilidade geral). Se você estiver usando a versão 2 do serviço do Data Factory, que está em versão prévia, consulte [HTTP connector in V2](../connector-http.md) (Conector do HTTP na V2).


Este artigo descreve como usar a Atividade de Cópia no Azure Data Factory para mover dados de um ponto de extremidade HTTP local/na nuvem para um armazenamento de dados do coletor com suporte. Este artigo se baseia no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral de movimentação de dados com a atividade de cópia e a lista de armazenamentos de dados com suporte, como fontes/coletores.

Atualmente, o data factory dá suporte apenas para a movimentação de dados de uma origem HTTP para outros armazenamentos de dados, mas não para a movimentação de dados de outros armazenamentos de dados para um destino HTTP.

## <a name="supported-scenarios-and-authentication-types"></a>Cenários com suporte e tipos de autenticação
Você pode usar esse conector HTTP para recuperar dados de **ponto de extremidade HTTP/s, seja ele na nuvem ou local**, usando o método HTTP **GET** ou **POST**. Os seguintes tipos de autenticação têm suporte: **Anônimo**, **Básico**, **Digest**, **Windows** e **ClientCertificate**. Observe que a diferença entre esse conector e o [Conector da tabela da Web](data-factory-web-table-connector.md) é: o segundo é usado para extrair o conteúdo de tabela da página HTML da Web.

Ao copiar dados de um ponto de extremidade HTTP local, você precisa instalar um gateway de gerenciamento de dados no ambiente local/VM do Azure. Consulte o artigo [movendo dados entre pontos locais e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) para saber mais sobre o Gateway de gerenciamento de dados e obter instruções passo a passo de como configurar o gateway.

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia que mova dados de uma origem HTTP usando ferramentas/APIs diferentes.

- A maneira mais fácil de criar um pipeline é usar o **Assistente de Cópia**. Confira [Tutorial: Criar um pipeline usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md) para ver um breve passo a passo sobre como criar um pipeline usando o Assistente de cópia de dados.

- Você também pode usar as seguintes ferramentas para criar um pipeline: **Portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo do Azure Resource Manager**, **API .NET** e **API REST**. Confira o [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo sobre a criação de um pipeline com uma atividade de cópia. Para obter amostras JSON copiar dados da origem HTTP para o Armazenamento de Blobs do Azure, consulte a seção [Exemplos de JSON](#json-examples) nestes artigos.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado
A tabela a seguir apresenta a descrição para elementos JSON específicos do serviço HTTP vinculado.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| type | A propriedade type deve ser definida como: `Http`. | Sim |
| url | URL base para o Servidor Web | Sim |
| authenticationType | Especifica o tipo de autenticação. Os valores permitidos são: **Anônimo**, **Básico**, **Digest**, **Windows** e **ClientCertificate**. <br><br> Consulte as seções abaixo desta tabela para mais propriedades e amostras JSON para esses tipos de autenticação, respectivamente. | Sim |
| enableServerCertificateValidation | Especifique se deseja habilitar a validação do certificado SSL do servidor se a origem for um servidor Web HTTPS | Não, o padrão é true |
| gatewayName | Nome do Gateway de Gerenciamento de Dados para se conectar a uma origem HTTP local. | Sim se estiver copiando dados de uma origem HTTP local. |
| encryptedCredential | Credencial criptografada para acessar o ponto de extremidade HTTP. Gerado automaticamente quando você configura as informações de autenticação no assistente de cópia ou a caixa de diálogo pop-up do ClickOnce. | Não. Aplique somente quando estiver copiando dados de um servidor HTTP local. |

Consulte [Mover dados entre fontes locais e a nuvem com o Gateway de Gerenciamento de Dados](data-factory-move-data-between-onprem-and-cloud.md) para obter detalhes sobre como configurar as credenciais para uma fonte de dados de conector HTTP local.

### <a name="using-basic-digest-or-windows-authentication"></a>Usando a autenticação Básica, Digest ou Windows

Defina `authenticationType` como `Basic`, `Digest` ou `Windows` e especifique as propriedades a seguir, além das genéricas do conector HTTP apresentadas acima:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| Nome de Usuário | Nome de usuário para acessar o ponto de extremidade HTTP. | Sim |
| Senha | Senha do usuário (nome de usuário). | Sim |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>Exemplo: usando a autenticação Básica, Digest ou Windows

```JSON
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "basic",
            "url" : "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

### <a name="using-clientcertificate-authentication"></a>Usando a autenticação ClientCertificate

Para usar a autenticação Básica, defina `authenticationType` como `ClientCertificate` e especifique as propriedades a seguir, além das genéricas do conector HTTP apresentadas acima:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| embeddedCertData | O conteúdo codificado na Base64 de dados binários do arquivo Personal Information Exchange (PFX). | Especifique `embeddedCertData` ou `certThumbprint`. |
| certThumbprint | A impressão digital do certificado que foi instalado no repositório de certificados do computador do gateway. Aplique somente ao copiar dados de uma origem HTTP local. | Especifique `embeddedCertData` ou `certThumbprint`. |
| Senha | Senha associada ao certificado. | Não |

Se você usar `certThumbprint` para autenticação e o certificado estiver instalado no armazenamento pessoal do computador local, você precisará conceder a permissão de leitura para o serviço de gateway:

1. Iniciar o MMC (Console de Gerenciamento Microsoft). Adicionar o snap-in **Certificados**, que tem como destino o **Computador Local**.
2. Expanda **Certificados**, **Pessoal** e clique em **Certificados**.
3. Clique com o botão direito do mouse no certificado do repositório pessoal e selecione **Todas as Tarefas**->**Gerenciar Chaves Particulares...**
3. Na guia **Segurança**, adicione a conta de usuário sob a qual o serviço de Host de Gateway de Gerenciamento de Dados está em execução com o acesso de leitura para o certificado.  

#### <a name="example-using-client-certificate"></a>Exemplo: usando o certificado do cliente
Esse serviço vinculado vincula seu data factory a um servidor Web HTTP local. Ele usa um certificado do cliente instalado no computador com o Gateway de Gerenciamento de Dados instalado.

```JSON
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"

        }
    }
}
```

#### <a name="example-using-client-certificate-in-a-file"></a>Exemplo: usando o certificado do cliente em um arquivo
Esse serviço vinculado vincula seu data factory a um servidor Web HTTP local. Ele usa um certificado do cliente no computador com o Gateway de Gerenciamento de Dados instalado.

```JSON
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "base64 encoded cert data",
            "password": "password of cert"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de dados, confira o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). As seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados (SQL Azure, Blob do Azure, Tabela do Azure etc.).

A seção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no armazenamento de dados. A seção typeProperties para o conjunto de dados do tipo **Http** tem as propriedades a seguir

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | O tipo do conjunto de dados foi especificado. deve ser definido como `Http`. | Sim |
| relativeUrl | Uma URL relativa para o recurso que contém os dados. Quando o caminho não for especificado, apenas a URL especificada na definição do serviço vinculado será usada. <br><br> Para construir uma URL dinâmica, você pode usar as [funções e variáveis de sistema do Data Factory](data-factory-functions-variables.md), por exemplo, “relativeUrl”: “$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)”. | Não |
| requestMethod | Método Http. Os valores permitidos são **GET** ou **POST**. | Não. O padrão é `GET`. |
| additionalHeaders | Cabeçalhos de solicitação HTTP adicionais. | Não |
| requestBody | O corpo da solicitação HTTP. | Não |
| formato | Se você quiser simplesmente **recuperar os dados do ponto de extremidade HTTP como estão** sem analisá-los, ignore estas configurações de formato. <br><br> Se você quiser analisar o conteúdo da resposta HTTP durante a cópia, há suporte para os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Para saber mais, veja as seções [Formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [Formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [Formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format), e [Formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). |Não |
| compactação | Especifique o tipo e o nível de compactação para os dados. Os tipos com suporte são: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**. Os níveis com suporte são **Ideal** e **O mais rápido**. Para saber mais, confira [File and compression formats in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support) (Formatos de arquivo e de compactação no Azure Data Factory). |Não |

### <a name="example-using-the-get-default-method"></a>Exemplo: usando o método GET (padrão)

```JSON
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "XXX/test.xml",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

### <a name="example-using-the-post-method"></a>Exemplo: usando o método POST

```JSON
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
           "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Criando pipelines](data-factory-create-pipelines.md). As propriedades, como nome, descrição, tabelas de entrada e saída, e política, estão disponíveis para todos os tipos de atividades.

As propriedades disponíveis na seção **typeProperties** da atividade, por outro lado, variam de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam de acordo com os tipos de fonte e coletor.

Atualmente, quando a origem na atividade de cópia é do tipo **HttpSource**, há suporte para as propriedades a seguir.

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| httpRequestTimeout | O tempo limite (TimeSpan) para a solicitação HTTP obter uma resposta. É o tempo limite para obter uma resposta e não o tempo limite para ler dados de resposta. | Não. Valor padrão: 00:01:40 |

## <a name="supported-file-and-compression-formats"></a>Formatos de arquivo e de compactação com suporte
Consulte o artigo [Formatos de arquivo e de compactação no Azure Data Factory](data-factory-supported-file-and-compression-formats.md) para obter detalhes.

## <a name="json-examples"></a>Exemplos de JSON
O exemplo a seguir fornece as definições de JSON de exemplo que você pode usar para criar um pipeline usando o [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados da origem HTTP para o Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das fontes para qualquer um dos coletores declarados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a Atividade de Cópia no Azure Data Factory.

### <a name="example-copy-data-from-http-source-to-azure-blob-storage"></a>Exemplo: copiar dados da origem HTTP para o Armazenamento de Blobs do Azure
A solução de Data Factory para este exemplo contém as seguintes entidades de Data Factory:

1. Um serviço vinculado do tipo [HTTP](#linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [Http](#dataset-properties).
4. Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com a Atividade de Cópia que usa [HttpSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados de uma origem HTTP para um blob do Azure a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

### <a name="http-linked-service"></a>Serviço vinculado HTTP
Esse exemplo usa o serviço vinculado HTTP com autenticação anônima. Confira a seção [Serviço vinculado HTTP](#linked-service-properties) para ver os diferentes tipos de autenticação que você pode usar.

```JSON
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Serviço vinculado de armazenamento do Azure

```JSON
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

### <a name="http-input-dataset"></a>Conjunto de dados de entrada HTTP
Configurar **external** como **true** informa ao serviço Data Factory que o conjunto de dados é externo ao Data Factory e não é produzido por uma atividade no Data Factory.

```JSON
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}

```

### <a name="azure-blob-output-dataset"></a>Conjunto de dados de saída de Blob do Azure

Os dados são gravados em um novo blob a cada hora (frequência: hora, intervalo: 1).

```JSON
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/Movies"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

### <a name="pipeline-with-copy-activity"></a>Pipeline com Atividade de cópia

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída e é agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo **source** está definido como **HttpSource** e o tipo **sink** está definido como **BlobSink**.

Consulte [HttpSource](#copy-activity-properties) para obter a lista de propriedades às quais HttpSource dá suporte.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "HttpSourceToAzureBlob",
        "description": "Copy from an HTTP source to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "HttpSourceDataInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "HttpSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
      ]
   }
}
```

> [!NOTE]
> Para mapear colunas de conjunto de dados de origem para colunas do conjunto de dados de coletor, confira [Mapeando colunas de conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Desempenho e Ajuste
Veja o [Guia de desempenho e ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (Atividade de Cópia) no Azure Data Factory, além de várias maneiras de otimizar esse processo.
