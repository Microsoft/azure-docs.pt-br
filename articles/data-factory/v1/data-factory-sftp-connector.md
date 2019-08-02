---
title: Mover dados do servidor SFTP usando o Azure Data Factory | Microsoft Docs
description: Saiba mais sobre como mover dados de um servidor SFTP local ou na nuvem usando o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/12/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d35c4f410c29bba7848dde53d206cdd2ccd980ca
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836162"
---
# <a name="move-data-from-an-sftp-server-using-azure-data-factory"></a>Mover dados de um servidor SFTP usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](data-factory-sftp-connector.md)
> * [Versão 2 (versão atual)](../connector-sftp.md)

> [!NOTE]
> Este artigo se aplica à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [ Conector SFTP em V2 ](../connector-sftp.md).

Este artigo descreve como usar a Atividade de Cópia no Azure Data Factory para mover dados de um servidor SFTP local/na nuvem para um armazenamento de dados do coletor com suporte. Este artigo se baseia no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral de movimentação de dados com a atividade de cópia e a lista de armazenamentos de dados com suporte, como fontes/coletores.

Atualmente, o data factory dá suporte apenas à movimentação de dados de um servidor SFTP para outros armazenamentos de dados, mas não para a movimentação de dados de outros armazenamentos de dados para um servidor SFTP. Ele dá suporte a servidores SFTP tanto locais quanto na nuvem.

> [!NOTE]
> A Atividade de Cópia não exclui o arquivo de origem depois que ele é copiado com êxito para o destino. Se precisar excluir o arquivo de origem após uma cópia bem-sucedida, crie uma atividade personalizada para excluir o arquivo e use a atividade no pipeline.

## <a name="supported-scenarios-and-authentication-types"></a>Cenários com suporte e tipos de autenticação
Você pode usar esse conector SFTP para copiar dados tanto de **servidores SFTP locais quanto na nuvem**. Os tipos de autenticação **Básica** e **SshPublicKey** têm suporte ao se conectar ao servidor SFTP.

Ao copiar dados de um servidor SFTP local, você precisa instalar um gateway de gerenciamento de dados no ambiente local/VM do Azure. Consulte [Gateway de Gerenciamento de Dados](data-factory-data-management-gateway.md) para obter detalhes sobre o gateway. Consulte o artigo [Moving data between on-premises locations and cloud](data-factory-move-data-between-onprem-and-cloud.md) (Mover dados entre pontos locais e na nuvem) para obter instruções detalhadas sobre como configurar o gateway e usá-lo.

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia que mova dados de uma origem SFTP usando diferentes ferramentas/APIs.

- A maneira mais fácil de criar um pipeline é usar o **Assistente de Cópia**. Consulte [Tutorial: criar um pipeline usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md) para ver um breve passo a passo sobre como criar um pipeline usando o Assistente de cópia de dados.

- Você também pode usar as ferramentas abaixo para criar um pipeline: **Visual Studio**, **Azure PowerShell**, **modelo do Resource Manager**, **.NET API**, e **API REST**. Confira o [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo sobre a criação de um pipeline com uma atividade de cópia. Para obter amostras JSON que copiam dados do servidor SFTP para o Armazenamento de Blobs do Azure, consulte a seção [Exemplo de JSON: copiar dados do servidor SFTP para o blob do Azure](#json-example-copy-data-from-sftp-server-to-azure-blob) deste artigo.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado
A tabela a seguir apresenta a descrição para elementos JSON específicos do serviço FTP vinculado.

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| type | A propriedade type deve ser definida como `Sftp`. |Sim |
| host | Nome ou endereço IP do servidor SFTP. |Sim |
| port |Porta na qual o servidor SFTP está escutando. O valor padrão é: 21 |Não |
| authenticationType |Especifique o tipo de autenticação. Valores permitidos: **Básico**, **SshPublicKey**. <br><br> Consulte as seções [Usando a autenticação Básica](#using-basic-authentication) e [Usando autenticação de chave pública SSH](#using-ssh-public-key-authentication) para ver mais propriedades e amostras do JSON, respectivamente. |Sim |
| skipHostKeyValidation | Especifique se deseja ignorar a validação da chave de host. | Nº O valor padrão: false |
| hostKeyFingerprint | Especifique a impressão digital da chave de host. | Sim se o `skipHostKeyValidation` estiver definido como false.  |
| gatewayName |Nome do Gateway de Gerenciamento de Dados para se conectar a um servidor SFTP local. | Sim se estiver copiando dados de um servidor SFTP local. |
| encryptedCredential | Credencial criptografada para acessar o servidor SFTP. Gerado automaticamente quando você especifica a autenticação Básica (nome de usuário + senha) ou autenticação SshPublicKey (nome de usuário + conteúdo ou caminho da chave privada) no assistente de cópia ou na caixa de diálogo de pop-up do ClickOnce. | Nº Aplique somente quando estiver copiando dados de um servidor SFTP local. |

### <a name="using-basic-authentication"></a>Usando a autenticação Básica

Para usar a autenticação Básica, defina `authenticationType` como `Basic` e especifique as propriedades a seguir, além das genéricas do conector SFTP apresentadas na última seção:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| Nome de Usuário | Usuário que tem acesso ao servidor SFTP. |Sim |
| password | Senha do usuário (nome de usuário). | Sim |

#### <a name="example-basic-authentication"></a>Exemplo: Autenticação básica
```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "password": "xxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
    }
}
```

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Exemplo: Autenticação básica com credencial criptografada

```JSON
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
      }
}
```

### <a name="using-ssh-public-key-authentication"></a>Usando a autenticação de chave pública SSH

Para usar a autenticação de chave pública SSH, defina `authenticationType` como `SshPublicKey` e especifique as propriedades a seguir, além das genéricas do conector SFTP apresentadas na última seção:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| Nome de Usuário |Usuário que tem acesso ao servidor SFTP |Sim |
| privateKeyPath | Especifique, para o arquivo de chave privada, um caminho absoluto que esse gateway possa acessar. | Especifique `privateKeyPath` ou `privateKeyContent`. <br><br> Aplique somente quando estiver copiando dados de um servidor SFTP local. |
| privateKeyContent | Uma cadeia de caracteres serializada do conteúdo da chave privada. O Assistente de Cópia pode ler o arquivo de chave privada e extrair o conteúdo da chave privada automaticamente. Se você estiver usando qualquer outra ferramenta/SDK, use a propriedade privateKeyPath em seu lugar. | Especifique `privateKeyPath` ou `privateKeyContent`. |
| passPhrase | Especifique a senha/frase secreta para descriptografar a chave particular se o arquivo de chave for protegido por uma frase secreta. | Sim, se o arquivo de chave privada for protegido por uma frase secreta. |

> [!NOTE]
> O conector SFTP fornece suporte para a chave OpenSSH RSA/DSA. Certifique-se de que o conteúdo do arquivo da chave inicia com "-----BEGIN [RSA/DSA] PRIVATE KEY-----". Se o arquivo da chave privada for um arquivo ppk-format, utilize a ferramenta Putty para converter de .ppk para o formato OpenSSH.

#### <a name="example-sshpublickey-authentication-using-private-key-filepath"></a>Exemplo: Autenticação SshPublicKey usando o filePath da chave privada

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyPath",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true,
            "gatewayName": "mygateway"
        }
    }
}
```

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Exemplo: Autenticação SshPublicKey usando o conteúdo da chave privada

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyContent",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver.westus.cloudapp.azure.com",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyContent": "<base64 string of the private key content>",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de dados, confira o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). As seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados.

A seção **typeProperties** é diferente para cada tipo de conjunto de dados. Ela fornece informações específicas ao tipo de conjunto de dados. A seção typeProperties para o conjunto de dados do tipo **FileShare** tem as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| folderPath |Subcaminho para a pasta. Use o caractere de escape ' \ ' para caracteres especiais na cadeia de caracteres. Para obter exemplos, confira Definições de conjunto de dados e serviço vinculado de exemplo.<br/><br/>Você pode combinar essa propriedade com **partitionBy** para ter caminhos de pastas com base na fatia de data/hora de início/término. |Sim |
| fileName |Especifique o nome do arquivo no **folderPath** se quiser que a tabela se refira a um arquivo específico na pasta. Se você não especificar algum valor para essa propriedade, a tabela apontará para todos os arquivos na pasta.<br/><br/>Quando o fileName não for especificado para um conjunto de dados de saída, o nome do arquivo gerado será no seguinte formato: <br/><br/>`Data.<Guid>.txt` (Exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Não |
| fileFilter |Especifique um filtro a ser usado para selecionar um subconjunto de arquivos no folderPath em vez de todos os arquivos.<br/><br/>Os valores permitidos são: `*` (vários caracteres) e `?` (um único caractere).<br/><br/>Exemplo 1: `"fileFilter": "*.log"`<br/>Exemplo 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> fileFilter é aplicável a um conjunto de dados FileShare de entrada. Essa propriedade não tem suporte com HDFS. |Não |
| partitionedBy |partitionedBy pode usado para especificar um filename, folderPath dinâmico para dados de série temporal. Por exemplo, folderPath parametrizado para cada hora dos dados. |Não |
| format | Há suporte para os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Defina a propriedade **type** sob formato como um desses valores. Para saber mais, veja as seções [Formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [Formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [Formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format), e [Formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Se você quiser **copiar arquivos no estado em que se encontram** entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas duas definições de conjunto de dados de entrada e de saída. |Não |
| compression | Especifique o tipo e o nível de compactação para os dados. Tipos compatíveis são: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**. Níveis compatíveis são: **Ideal** e **Mais Rápido**. Para saber mais, confira [File and compression formats in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support) (Formatos de arquivo e de compactação no Azure Data Factory). |Não |
| useBinaryTransfer |Especifique se deve usar o modo de transferência Binário. True para o modo binário e ASCII false. Valor padrão: True. Essa propriedade só pode ser usada quando o tipo de serviço vinculado associado é do tipo: FtpServer. |Não |

> [!NOTE]
> filename e fileFilter não podem ser usados simultaneamente.

### <a name="using-partionedby-property"></a>Usando a propriedade partionedBy
Conforme mencionado na seção anterior, você pode especificar um filename, folderPath dinâmico para dados de série temporal com partitionedBy. Você pode fazer isso com as macros de Data Factory e as variáveis de sistema SliceStart e SliceEnd que indicam o período de tempo lógico para uma determinada fatia de dados.

Confira os artigos [Criando conjuntos de dados](data-factory-create-datasets.md), [Agendamento e execução](data-factory-scheduling-and-execution.md) e [Criando pipelines](data-factory-create-pipelines.md) para saber mais sobre conjuntos de dados de série temporal, agendamentos e fatias.

#### <a name="sample-1"></a>Exemplo 1:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
Nesse exemplo, {Slice} é substituído pelo valor da variável de sistema SliceStart do Data Factory no formato (AAAAMMDDHH) especificado. O SliceStart refere-se à hora de início da fatia. O folderPath é diferente para cada fatia. Por exemplo: wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Exemplo 2:

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
Neste exemplo, ano, mês, dia e hora do SliceStart são extraídos em variáveis separadas que são usadas pelas propriedades folderPath e fileName.

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Criando pipelines](data-factory-create-pipelines.md). As propriedades, como nome, descrição, tabelas de entrada e saída, e políticas, estão disponíveis para todos os tipos de atividade.

Por outro lado, as propriedades disponíveis na seção typeProperties da atividade variam de acordo com cada tipo de atividade. Para a atividade de Cópia, as propriedades de tipo variam conforme os tipos de fonte e coletor.

[!INCLUDE [data-factory-file-system-source](../../../includes/data-factory-file-system-source.md)]

## <a name="supported-file-and-compression-formats"></a>Formatos de arquivo e de compactação com suporte
Consulte o artigo [Formatos de arquivo e de compactação no Azure Data Factory](data-factory-supported-file-and-compression-formats.md) para obter detalhes.

## <a name="json-example-copy-data-from-sftp-server-to-azure-blob"></a>Exemplo de JSON: copiar dados do servidor SFTP para o blob do Azure
O exemplo a seguir fornece as definições de JSON de exemplo que você pode usar para criar um pipeline usando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [do Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados da origem SFTP para o Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das fontes para qualquer um dos coletores declarados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a Atividade de Cópia no Azure Data Factory.

> [!IMPORTANT]
> Este exemplo fornece snippets de JSON. Ele não inclui instruções passo a passo para criar o data factory. Confira o artigo [Mover dados entre fontes locais e a nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter instruções passo a passo.

O exemplo tem as seguintes entidades de data factory:

* Um serviço vinculado do tipo [sftp](#linked-service-properties).
* Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [FileShare](#dataset-properties).
* Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Um [pipeline](data-factory-create-pipelines.md) com a Atividade de Cópia que usa [FileSystemSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados de um servidor SFTP para um blob do Azure a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

**Serviço vinculado SFTP**

Este exemplo usa autenticação Básica com nome de usuário e senha em texto sem formatação. Você também pode usar uma das seguintes maneiras:

* Autenticação básica com credenciais criptografadas
* Autenticação de chave pública SSH

Confira a seção [Serviço FTP vinculado](#linked-service-properties) para ver os diferentes tipos de autenticação que você pode usar.

```JSON

{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "myuser",
            "password": "mypassword",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
    }
}
```
**Serviço vinculado de armazenamento do Azure**

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
**Conjunto de dados de entrada do SFTP**

Esse conjunto de dados refere-se à pasta `mysharedfolder` e ao arquivo `test.csv` do SFTP. O pipeline copia o arquivo para o destino.

Configurar “external”: “true” informa ao serviço Data Factory que o conjunto de dados é externo ao data factory e não é produzido por uma atividade no data factory.

```JSON
{
  "name": "SFTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "SftpLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Conjunto de dados de saída de Blob do Azure**

Os dados são gravados em um novo blob a cada hora (frequência: horas, intervalo: 1). O caminho de pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Pipeline com Atividade de cópia**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída e é agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo **source** está definido como **FileSystemSource** e o tipo **sink** está definido como **BlobSink**.

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "SFTPToBlobCopy",
            "inputs": [{
                "name": "SFTPFileInput"
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
        "start": "2017-02-20T18:00:00Z",
        "end": "2017-02-20T19:00:00Z"
    }
}
```

## <a name="performance-and-tuning"></a>Desempenho e Ajuste
Veja o [Guia de desempenho e ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (Atividade de Cópia) no Azure Data Factory, além de várias maneiras de otimizar esse processo.

## <a name="next-steps"></a>Próximas etapas
Confira os seguintes artigos:

* [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criação de um pipeline com uma Atividade de cópia.
