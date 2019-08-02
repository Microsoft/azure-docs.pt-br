---
title: Mover dados do servidor FTP usando o Azure Data Factory | Microsoft Docs
description: Saiba como mover dados de um servidor FTP usando o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: eea3bab0-a6e4-4045-ad44-9ce06229c718
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 5d043072244ede5b1d7bd28d4628ffe3cf4961d8
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836333"
---
# <a name="move-data-from-an-ftp-server-by-using-azure-data-factory"></a>Mover dados de um servidor FTP usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](data-factory-ftp-connector.md)
> * [Versão 2 (versão atual)](../connector-ftp.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [Conector FTP na V2 ](../connector-ftp.md).

Este artigo explica como usar a atividade de cópia no Azure Data Factory para mover dados de um servidor FTP. Ele se baseia no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral da movimentação de dados com a atividade de cópia.

Você pode copiar dados de um servidor FTP para qualquer repositório de dados de coletor com suporte. Para obter uma lista de repositórios de dados com suporte como coletores da atividade de cópia, confira a tabela [Repositórios de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Atualmente, o data factory dá suporte apenas à movimentação de dados de um servidor FTP para outros repositórios de dados, mas não para a movimentação de dados de outros repositórios de dados para um servidor FTP. Ele dá suporte a servidores FTP locais e em nuvem.

> [!NOTE]
> A atividade de cópia não exclui o arquivo de origem depois que ele é copiado com êxito para o destino. Se precisar excluir o arquivo de origem após uma cópia bem-sucedida, crie uma atividade personalizada para excluir o arquivo e use a atividade no pipeline.

## <a name="enable-connectivity"></a>Habilitar a conectividade
Se você estiver movendo dados de um servidor FTP **local** para um armazenamento de dados em nuvem (por exemplo, para o Armazenamento de Blobs do Azure), instale e use o Gateway de Gerenciamento de Dados. O Gateway de Gerenciamento de Dados é um agente cliente instalado em seu computador local que permite aos serviços de nuvem conectarem-se a recursos locais. Para ver os detalhes, consulte [Gateway de Gerenciamento de Dados](data-factory-data-management-gateway.md). Para ver instruções passo a passo sobre como configurar o gateway e usá-lo, consulte [Mover dados entre pontos locais e na nuvem](data-factory-move-data-between-onprem-and-cloud.md). Use o gateway para conectar-se a um servidor FTP mesmo que o servidor esteja em uma VM (máquina virtual) de IaaS (infraestrutura como serviço) do Azure.

É possível instalar o gateway no mesmo computador local ou na VM IaaS que o servidor FTP. No entanto, recomendamos que você instale o gateway em uma máquina separada/VM IaaS para evitar a contenção de recursos e para melhorar o desempenho. Ao instalar o gateway em um computador separado, o computador deverá ser capaz de acessar o servidor FTP.

## <a name="get-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia que move dados de uma origem FTP usando diferentes ferramentas ou APIs.

A maneira mais fácil de criar um pipeline é usar o **Assistente de Cópia do Data Factory**. Consulte [Tutorial: Criar um pipeline usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md) para obter um passo a passo rápido.

Você também pode usar as ferramentas abaixo para criar um pipeline: **Visual Studio**, **PowerShell**, **modelo do Resource Manager**, **.NET API**, e **API REST**. Confira o [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo sobre a criação de um pipeline com uma atividade de cópia.

Ao usar as ferramentas ou APIs, você executa as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Criar **serviços vinculados** para vincular repositórios de dados de entrada e saída ao seu data factory.
2. Criar **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia.
3. Criar um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto de dados como uma saída.

Ao usar o assistente, as definições de JSON para essas entidades do Data Factory (serviços vinculados, conjuntos de dados e o pipeline) são automaticamente criadas para você. Ao usar ferramentas ou APIs (exceto a API .NET), você define essas entidades do Data Factory usando o formato JSON. Para obter um exemplo com definições de JSON para entidades do Data Factory que são usadas para copiar dados do armazenamento de dados do FTP, confira a seção [Exemplo de JSON: Copiar dados do servidor FTP para o blob do Azure](#json-example-copy-data-from-ftp-server-to-azure-blob) deste artigo.

> [!NOTE]
> Para obter detalhes sobre os formatos de arquivo e de compactação com suporte para uso, consulte [Formatos de arquivo e de compactação no Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

As seções que se seguem fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades do Data Factory específicas ao FTP.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado
A tabela a seguir descreve elementos JSON específicos para um serviço FTP vinculado.

| Propriedade | DESCRIÇÃO | Obrigatório | Padrão |
| --- | --- | --- | --- |
| type |Defina isso para FtpServer. |Sim |&nbsp; |
| host |Especifique o nome ou endereço IP do servidor FTP. |Sim |&nbsp; |
| authenticationType |Especifique o tipo de autenticação. |Sim |Básica, Anônima |
| username |Especifique o usuário que tem acesso ao servidor FTP. |Não |&nbsp; |
| password |Especifique a senha para o usuário (nome de usuário). |Não |&nbsp; |
| encryptedCredential |Especifique a credencial criptografada para acessar o servidor FTP. |Não |&nbsp; |
| gatewayName |Especifique o nome do gateway no Gateway de Gerenciamento de Dados para se conectar a um servidor FTP local. |Não |&nbsp; |
| port |Especifique a porta ouvida pelo servidor FTP. |Não |21 |
| enableSsl |Especifique se o canal FTP sobre SSL/TLS deve ser usado. |Não |true |
| enableServerCertificateValidation |Especifique se deseja habilitar a validação do certificado SSL do servidor ao usar o canal FTP sobre SSL/TLS. |Não |true |

>[!NOTE]
>O conector FTP oferece suporte ao acessar o servidor FTP sem criptografia ou a criptografia de SSL/TLS explícita; ele não oferece suporte a criptografia SSL/TLS implícita.

### <a name="use-anonymous-authentication"></a>Usar autenticação anônima

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "authenticationType": "Anonymous",
              "host": "myftpserver.com"
        }
    }
}
```

### <a name="use-username-and-password-in-plain-text-for-basic-authentication"></a>Usar nome de usuário e senha em texto sem formatação para autenticação básica

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
    "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```

### <a name="use-port-enablessl-enableservercertificatevalidation"></a>Usar a porta, enableSsl, enableServerCertificateValidation

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

### <a name="use-encryptedcredential-for-authentication-and-gateway"></a>Usar encryptedCredential para autenticação e gateway

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "mygateway"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira [Criando conjuntos de dados](data-factory-create-datasets.md). As seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados.

A seção **typeProperties** é diferente para cada tipo de conjunto de dados. Ela fornece informações específicas ao tipo de conjunto de dados. A seção **typeProperties** para o conjunto de dados do tipo **FileShare** tem as propriedades a seguir:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| folderPath |Subcaminho para a pasta. Use o caractere de escape ' \ ' para caracteres especiais na cadeia de caracteres. Para obter exemplos, confira Definições de conjunto de dados e serviço vinculado de exemplo.<br/><br/>Você pode combinar essa propriedade com **partitionBy** para ter caminhos de pastas com base na fatia de data/hora de início e término. |Sim |
| fileName |Especifique o nome do arquivo no **folderPath** se quiser que a tabela se refira a um arquivo específico na pasta. Se você não especificar algum valor para essa propriedade, a tabela apontará para todos os arquivos na pasta.<br/><br/>Quando o **fileName** não for especificado para um conjunto de dados de saída, o nome do arquivo gerado será no seguinte formato: <br/><br/>`Data.<Guid>.txt` (Exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Não |
| fileFilter |Especifique um filtro a ser usado para selecionar um subconjunto de arquivos no **folderPath** em vez de todos os arquivos.<br/><br/>Os valores permitidos são: `*` (vários caracteres) e `?` (um único caractere).<br/><br/>Exemplo 1: `"fileFilter": "*.log"`<br/>Exemplo 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> **fileFilter** é aplicável a um conjunto de dados FileShare de entrada. Essa propriedade não tem suporte no HDFS (Sistema de Arquivos Distribuído Hadoop). |Não |
| partitionedBy |Usado especificar um **folderPath** dinâmico e o **fileName** para dados de série temporal. Por exemplo, você pode especificar um **folderPath** que é parametrizada para cada hora dos dados. |Não |
| format | Há suporte para os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Defina a propriedade **type** sob formato como um desses valores. Para saber mais, veja as seções [Formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [Formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [Formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format) e [Formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Se você quiser copiar arquivos no estado em que se encontram entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas duas definições de conjunto de dados de entrada e de saída. |Não |
| compression | Especifique o tipo e o nível de compactação para os dados. Os tipos com suporte são: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**; e os níveis com suporte são: **Ideal** e **Mais rápido**. Para saber mais, confira [File and compression formats in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support) (Formatos de arquivo e de compactação no Azure Data Factory). |Não |
| useBinaryTransfer |Especifique se o modo de transferência binário deve ser usado. Os valores são true para o modo binário (esse é o valor padrão) e false para ASCII. Essa propriedade só pode ser usada quando o tipo de serviço vinculado associado for do tipo: FtpServer. |Não |

> [!NOTE]
> **fileName** e **fileFilter** não podem ser usados simultaneamente.

### <a name="use-the-partionedby-property"></a>Usar a propriedade partionedBy
Conforme mencionado na seção anterior, você pode especificar um **folderPath** e um **fileName** dinâmicos para dados de série temporal com a propriedade **partitionedBy**.

Consulte [Criando conjuntos de dados](data-factory-create-datasets.md), [Agendamento e execução](data-factory-scheduling-and-execution.md) e [Criando pipelines](data-factory-create-pipelines.md) para saber mais sobre conjuntos de dados de série temporal, agendamentos e fatias.

#### <a name="sample-1"></a>Exemplo 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
Nesse exemplo, {Slice} é substituído pelo valor da variável de sistema SliceStart do Data Factory no formato (AAAAMMDDHH) especificado. O SliceStart refere-se à hora de início da fatia. O caminho da pasta é diferente para cada fatia. (Por exemplo, wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104.)

#### <a name="sample-2"></a>Exemplo 2

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
[
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
Neste exemplo, ano, mês, dia e hora do SliceStart são extraídos em variáveis separadas que são usadas pelas propriedades **folderPath** e **fileName**.

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para obter uma lista completa das seções e propriedades disponíveis para definir as atividades, consulte [Criando pipelines](data-factory-create-pipelines.md). As propriedades, como nome, descrição, tabelas de entrada e saída, e políticas, estão disponíveis para todos os tipos de atividade.

As propriedades disponíveis na seção **typeProperties** da atividade, por outro lado, variam de acordo com cada tipo de atividade. Para a atividade de cópia, as propriedades de tipo variam conforme os tipos de fonte e coletor.

Na atividade de cópia quando a fonte for do tipo **FileSystemSource**, as propriedades a seguir estarão disponíveis na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| recursive |Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. |True, False (padrão) |Não |

## <a name="json-example-copy-data-from-ftp-server-to-azure-blob"></a>Exemplo JSON: Copiar dados do servidor FTP para o blob do Azure
Este exemplo mostra como copiar dados de um servidor FTP para um Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados diretamente para qualquer um dos coletores declarados em [formatos e repositórios de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia no Data Factory.

Os exemplos a seguir fornecem as definições de JSON de exemplo que você pode usar para criar um pipeline usando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), ou [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md):

* Um serviço vinculado do tipo [FtpServer](#linked-service-properties)
* Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [FileShare](#dataset-properties)
* Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* Um [pipeline](data-factory-create-pipelines.md) com a atividade de cópia que usa [FileSystemSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

O exemplo copia dados de um servidor FTP para um blob do Azure a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

### <a name="ftp-linked-service"></a>Serviço vinculado de FTP

Este exemplo usa autenticação Básica com nome de usuário e senha em texto sem formatação. Você também pode usar uma das seguintes maneiras:

* Autenticação anônima
* Autenticação básica com credenciais criptografadas
* FTPS (FTP sobre SSL/TLS)

Confira a seção [Serviço FTP vinculado](#linked-service-properties) para ver os diferentes tipos de autenticação que você pode usar.

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
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
### <a name="ftp-input-dataset"></a>Conjunto de dados de entrada de FTP

Esse conjunto de dados refere-se à pasta FTP `mysharedfolder` e ao arquivo `test.csv`. O pipeline copia o arquivo para o destino.

Configurar **external**: **true** informa ao serviço Data Factory que o conjunto de dados é externo ao data factory e não é produzido por uma atividade no data factory.

```JSON
{
  "name": "FTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "FTPLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv",
      "useBinaryTransfer": true
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

### <a name="azure-blob-output-dataset"></a>Conjunto de dados de saída de Blob do Azure

Dados são gravados em um novo blob a cada hora (frequência: horas, intervalo: 1). O caminho de pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e hora da hora de início.

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/ftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```


### <a name="a-copy-activity-in-a-pipeline-with-file-system-source-and-blob-sink"></a>Uma atividade de cópia em um pipeline com origem no sistema de arquivos e coletor de blobs

O pipeline contém uma atividade de cópia que está configurada para usar os conjuntos de dados de entrada e saída e é agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo **source** está definido como **FileSystemSource** e o tipo **sink** está definido como **BlobSink**.

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
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
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-08-24T18:00:00Z",
        "end": "2016-08-24T19:00:00Z"
    }
}
```
> [!NOTE]
> Para mapear colunas de conjunto de dados de origem para colunas do conjunto de dados de coletor, confira [Mapeando colunas de conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="next-steps"></a>Próximas etapas
Confira os seguintes artigos:

* Para saber mais sobre os principais fatores que afetam o desempenho e a movimentação dos dados (atividade de cópia) no Azure Data Factory, além de várias maneiras de otimizá-la, consulte o [Guia de desempenho e ajuste da atividade de cópia](data-factory-copy-activity-performance.md).

* Para obter instruções passo a passo para criar um pipeline com uma atividade de cópia, consulte o [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
