---
title: Mover dados de uma fonte HTTP – Azure | Microsoft Docs
description: Saiba como mover dados de uma fonte HTTP local ou na nuvem usando o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 22d6999b2a69aceb4421cea070d784f693bdf9c4
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839290"
---
# <a name="move-data-from-an-http-source-by-using-azure-data-factory"></a>Mover dados de uma fonte HTTP usando o Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](data-factory-http-connector.md)
> * [Versão 2 (versão atual)](../connector-http.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço do Azure Data Factory, consulte [Conector HTTP em V2](../connector-http.md).


Este artigo descreve como usar a atividade de cópia no Azure Data Factory para mover dados de um ponto de extremidade HTTP local ou na nuvem para um repositório de dados de coletor com suporte. Este artigo baseia-se em [ Mover dados usando a Atividade de Cópia ](data-factory-data-movement-activities.md), que apresenta uma visão geral da movimentação de dados usando a Atividade de Cópia. O artigo também lista os armazenamentos de dados que a Atividade de Cópia suporta como fontes e coletores.

Atualmente, o Data Factory suporta apenas dados em movimento de uma origem HTTP para outros armazenamentos de dados. Atualmente, o Data Factory suporta apenas dados em movimento de uma origem HTTP para outros armazenamentos de dados.

## <a name="supported-scenarios-and-authentication-types"></a>Cenários com suporte e tipos de autenticação

Você pode usar esse conector HTTP para recuperar dados de *uma nuvem e um ponto de extremidade HTTP/S local* usando os métodos HTTP **GET** ou **POST**. Os seguintes tipos de autenticação são compatíveis: **Anônima**, **Básica**, **Digest**, **Windows** e **ClientCertificate**. Observe a diferença entre esse conector e o [conector de tabela da Web](data-factory-web-table-connector.md). O conector da tabela da Web extrai o conteúdo da tabela de uma página da Web em HTML.

Ao copiar dados de um ponto de extremidade HTTP local, você deve instalar o Data Management Gateway no ambiente local ou em uma VM do Azure. Para saber mais sobre o Data Management Gateway e obter instruções passo a passo sobre como configurar o gateway, consulte [Mover dados entre locais e a nuvem](data-factory-move-data-between-onprem-and-cloud.md).

## <a name="get-started"></a>Introdução

Você pode criar um pipeline que tenha uma atividade de cópia para mover dados de uma origem HTTP usando diferentes ferramentas ou APIs:

- A maneira mais fácil de criar um pipeline é usar o assistente Copiar Dados. Confira um rápido passo a passo sobre como criar um pipeline usando o Assistente de Cópia de Dados no [Tutorial: Criar um pipeline usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md).

- Você também pode usar as ferramentas a seguir para criar um pipeline: o **Visual Studio**, **do Azure PowerShell**, um **modelo do Resource Manager**, o **API .NET** , ou o **API REST**. Para obter instruções passo a passo sobre como criar um pipeline com atividade de cópia, consulte o [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). Para amostras JSON que copiam dados de uma origem HTTP para o Armazenamento de Blobs do Azure, consulte [Exemplos JSON](#json-examples).

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

A tabela a seguir descreve elementos JSON que são específicos para o serviço vinculado HTTP:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| type | O **tipo** propriedade deve ser definida como **Http**. | Sim |
| url | A URL base para o servidor web. | Sim |
| authenticationType | Especifica o tipo de autenticação. Os valores permitidos são **Anonymous**, **Basic**, **Digest**, **Windows** e **ClientCertificate**. <br><br> Consulte as seções posteriores deste artigo para mais propriedades e amostras JSON para esses tipos de autenticação. | Sim |
| enableServerCertificateValidation | Especifica se deve ativar a validação do certificado SSL do servidor se a origem for um servidor da Web HTTPS. Quando seu servidor HTTPS usa um certificado autoassinado, configure isso para **falso**. | Não<br /> (o padrão é **verdadeiro**) |
| gatewayName | O nome da instância do Data Management Gateway a ser usada para se conectar a uma origem HTTP local. | Sim, se você estiver copiando dados de uma fonte HTTP local |
| encryptedCredential | A credencial criptografada para acessar o terminal HTTP. O valor é gerado automaticamente quando você configura as informações de autenticação no Assistente de Cópia ou usando a caixa de diálogo **ClickOnce**. | Não<br /> (aplica-se somente quando você copia dados de um servidor HTTP local) |

Para obter detalhes sobre a configuração de credenciais para uma fonte de dados de conector HTTP local, consulte [Mover dados entre fontes locais e a nuvem usando o Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md).

### <a name="using-basic-digest-or-windows-authentication"></a>Usando a autenticação Básica, Digest ou Windows

Definir **authenticationType** à **básica**, **Digest**, ou **Windows**. Além da HTTP conector as propriedades genéricas descritas nas seções anteriores, defina as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| userName | O nome de usuário a ser usada para acessar o ponto de extremidade HTTP. | Sim |
| password | A senha do usuário (**nome de usuário**). | Sim |

**Exemplo: Usando a autenticação Básica, Digest ou Windows**

```json
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

Para usar a autenticação básica, defina **authenticationType** como  **ClientCertificate**. Além da HTTP conector as propriedades genéricas descritas nas seções anteriores, defina as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| embeddedCertData | O conteúdo codificado em Base64 de dados binários do arquivo PFX. | Especificar **embeddedCertData** ou **certThumbprint** |
| certThumbprint | A impressão digital do certificado que foi instalado no repositório de certificados do computador do gateway. Aplique somente quando você copiar dados de uma origem HTTP local. | Especificar **embeddedCertData** ou **certThumbprint** |
| password | A senha associada com o certificado. | Não |

Se você usar **certThumbprint** para autenticação e o certificado estiver instalado no armazenamento pessoal do computador local, conceda permissões de leitura ao serviço de gateway:

1. Abra o console de gerenciamento Microsoft (MMC). Adicione a **certificados** snap-in que tem como alvo **computador Local**.
2. Expanda **Certificados** > **Pessoal** e, em seguida, selecione **Certificados**.
3. Clique com o botão direito do mouse no certificado do armazenamento pessoal e selecione **Todas as Tarefas** >**Gerenciar Chaves Particulares**.
3. Na guia **Segurança**, adicione a conta de usuário na qual o Serviço do Host do Data Management Gateway está em execução, com acesso de leitura ao certificado.  

**Exemplo: Usando um certificado de cliente**

Esse serviço vinculado vincula seu data factory a um servidor Web HTTP local. Ele usa um certificado de cliente instalado na máquina que possui o Data Management Gateway instalado.

```json
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

**Exemplo: Usando um certificado de cliente em um arquivo**

Esse serviço vinculado vincula seu data factory a um servidor Web HTTP local. Ele usa um arquivo de certificado de cliente no computador que tem um Gateway de gerenciamento de dados instalado.

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "Base64-encoded cert data",
            "password": "password of cert"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Algumas seções de um arquivo JSON do conjunto de dados, como estrutura, disponibilidade e política, são similares para todos os tipos de conjunto de dados (banco de dados SQL do Azure, armazenamento de BLOBs do Azure, armazenamento de tabelas do Azure).

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira [Criando conjuntos de dados](data-factory-create-datasets.md).

A seção **typeProperties** é diferente para cada tipo de conjunto de dados. A seção **typeProperties** fornece informações sobre a localização dos dados no armazenamento de dados. A seção **typeProperties** para um conjunto de dados do tipo **Http** possui as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | O **tipo** do conjunto de dados deve ser definida como **Http**. | Sim |
| relativeUrl | Uma URL relativa para o recurso que contém os dados. Quando o caminho não é especificado, somente o URL especificado na definição de serviço vinculada é usado. <br><br> Para construir um URL dinâmico, você pode usar [funções do Data Factory e variáveis do sistema](data-factory-functions-variables.md). Exemplo: **relativeUrl**: **$$ Text.Format ('/ my / report? Month = {0: aaaa - - {0: MM} & fmt = csv', SliceStart)** . | Não |
| requestMethod | O método HTTP. Valores permitidos são **Obtenha** e **POST**. | Não <br />(o padrão é **obter**) |
| additionalHeaders | Cabeçalhos de solicitação HTTP adicionais. | Não |
| requestBody | O corpo da solicitação HTTP. | Não |
| format | Se você deseja *recuperar os dados de um endpoint HTTP como está* sem analisá-los, ignore a configuração **formato**. <br><br> Se quiser analisar o conteúdo da resposta HTTP durante a cópia, os seguintes tipos de formato são compatíveis: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Para obter mais informações, consulte [Formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [Formato JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format) e [Formato de parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). |Não |
| compression | Especifique o tipo e o nível de compactação para os dados. Tipos com suporte: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**. Níveis compatíveis: **Ideal** e **Mais Rápido**. Para saber mais, confira [File and compression formats in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support) (Formatos de arquivo e de compactação no Azure Data Factory). |Não |

**Exemplo: usando o método GET (padrão)**

```json
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

**Exemplo: usando o método POST**

```json
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

## <a name="copy-activity-properties"></a>Propriedades da Atividade de Cópia

Propriedades como nome, descrição, tabelas de entrada e saída e política estão disponíveis para todos os tipos de atividades.

Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, consulte [Creating pipelines](data-factory-create-pipelines.md). 

As propriedades que estão disponíveis na seção **typeProperties** da atividade variam de acordo com cada tipo de atividade. Para uma atividade de cópia, as propriedades variam dependendo dos tipos de fontes e coletores.

Atualmente, quando a origem em Copy Activity é do tipo **HttpSource**, as seguintes propriedades são suportadas:

| Propriedade | DESCRIÇÃO | Obrigatório |
| -------- | ----------- | -------- |
| httpRequestTimeout | O tempo limite (o valor **TimeSpan**) para a solicitação HTTP para obter uma resposta. É o tempo limite para obter uma resposta, não o tempo limite para ler os dados de resposta. | Não<br />(valor padrão: **00:01:40**) |

## <a name="supported-file-and-compression-formats"></a>Formatos de arquivo e de compactação com suporte

Consulte [Formatos de arquivo e compactação no Azure Data Factory](data-factory-supported-file-and-compression-formats.md) para saber mais.

## <a name="json-examples"></a>Exemplos de JSON

Os exemplos a seguir fornecem as definições de JSON de exemplo que você pode usar para criar um pipeline usando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [do Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Os exemplos mostram como copiar dados de uma origem HTTP para o armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados *diretamente* de qualquer uma das origens para qualquer um dos coletores [suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a Atividade de Cópia no Azure Data Factory.

**Exemplo: copiar dados de uma origem HTTP para o armazenamento Azure Blob**

A solução de Data Factory para este exemplo contém as seguintes entidades de Data Factory:

*   Um serviço vinculado do tipo [HTTP](#linked-service-properties).
*   Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
*   Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [Http](#dataset-properties).
*   Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
*   Um [pipeline](data-factory-create-pipelines.md) que tem uma atividade de cópia que usa [HttpSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados de uma origem HTTP para um blob do Azure a cada hora. As propriedades JSON usadas nessas amostras são descritas nas seções que seguem as amostras.

### <a name="http-linked-service"></a>Serviço vinculado HTTP

Esse exemplo usa o serviço vinculado HTTP com autenticação anônima. Veja [serviço vinculado HTTP](#linked-service-properties) para diferentes tipos de autenticação que você pode usar.

```json
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

### <a name="azure-storage-linked-service"></a>Serviço vinculado do armazenamento do Azure

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
    }
  }
}
```

### <a name="http-input-dataset"></a>Conjunto de dados de entrada HTTP

A definição **externa** para **verdadeiro** informa ao serviço Data Factory que o conjunto de dados é externo ao data factory e não é produzido por uma atividade no data factory.

```json
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

### <a name="azure-blob-output-dataset"></a>Conjunto de dados de saída de blob do Azure

Os dados são gravados em um novo blob a cada hora (**frequência**: **hora**, **intervalo**: **1**).

```json
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

### <a name="pipeline-that-uses-a-copy-activity"></a>Pipeline que usa uma atividade de cópia

O pipeline contém uma atividade de cópia configurada para usar os conjuntos de dados de entrada e saída. A atividade de cópia está programada para ser executado a cada hora. Na definição JSON do pipeline, o tipo **source** é definido como **HttpSource** e o tipo **sink** é definido como **BlobSink**.

Para obter a lista de propriedades que **HttpSource** dá suporte, consulte [HttpSource](#copy-activity-properties).

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with a copy activity",
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
> Para mapear colunas de um conjunto de dados de origem para colunas de um conjunto de dados do coletor, consulte [mapeando colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Desempenho e ajuste

Para conhecer os principais fatores que afetam o desempenho da movimentação de dados (Atividade de Cópia) no Azure Data Factory e várias maneiras de otimizá-la, consulte o [Guia de desempenho e ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md).
