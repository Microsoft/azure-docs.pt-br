---
title: Copiar dados do servidor SFTP usando o Azure Data Factory | Microsoft Docs
description: Saiba mais sobre o conector do MySQL no Azure Data Factory que permite que você copie dados de um servidor SFTP para um armazenamento de dados que tem suporte como um coletor.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: jingwang
ms.openlocfilehash: 9540a82933337dab112119cc791fa12d98b30aff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61042927"
---
# <a name="copy-data-from-sftp-server-using-azure-data-factory"></a>Copiar dados de um servidor SFTP usando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-sftp-connector.md)
> * [Versão atual](connector-sftp.md)

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de um servidor SFTP. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados de um servidor SFTP para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector de SFTP dá suporte a:

- Cópia de arquivos usando a autenticação **Básica** ou **SshPublicKey**.
- Cópia de arquivos no estado em que se encontram ou análise de arquivos com os [formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md).

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções que se seguem fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao SFTP.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do SFTP:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **Sftp**. |Sim |
| host | Nome ou endereço IP do servidor SFTP. |Sim |
| porta | Porta na qual o servidor SFTP está escutando.<br/>Os valores permitidos são: inteiro, o valor padrão é **22**. |Não  |
| skipHostKeyValidation | Especifique se deseja ignorar a validação da chave de host.<br/>Os valores permitidos são: **true**, **false** (padrão).  | Não  |
| hostKeyFingerprint | Especifique a impressão digital da chave de host. | Sim se "skipHostKeyValidation" estiver definida como false.  |
| authenticationType | Especifique o tipo de autenticação.<br/>Valores permitidos são: **Básico**, **SshPublicKey**. Consulte as seções [Usando a autenticação Básica](#using-basic-authentication) e [Usando autenticação de chave pública SSH](#using-ssh-public-key-authentication) para ver mais propriedades e amostras do JSON, respectivamente. |Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime do Azure ou o Integration Runtime auto-hospedado (se o armazenamento de dados estiver localizado em uma rede privada). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não  |

### <a name="using-basic-authentication"></a>Usando a autenticação Básica

Para usar a autenticação Básica, defina a propriedade "authenticationType" como **Básica** e especifique as propriedades a seguir, além das que são genéricas do conector SFTP apresentadas na última seção:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| userName | Usuário que tem acesso ao servidor SFTP. |Sim |
| Senha | Senha do usuário (userName). Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |

**Exemplo:**

```json
{
    "name": "SftpLinkedService",
    "type": "linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "authenticationType": "Basic",
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

### <a name="using-ssh-public-key-authentication"></a>Usando a autenticação de chave pública SSH

Para usar a chave pública SSH, defina a propriedade "authenticationType" como **SshPublicKey** e especifique as propriedades a seguir, além das que são genéricas do conector SFTP apresentadas na última seção:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| userName | Usuário que tem acesso ao servidor SFTP |Sim |
| privateKeyPath | Especifica o caminho absoluto para o arquivo de chave privada que pode ser acessado pelo Integration Runtime. Aplica-se apenas quando o tipo auto-hospedado do Integration Runtime é especificado em "connectVia". | Especifique `privateKeyPath` ou `privateKeyContent`.  |
| privateKeyContent | Conteúdo da chave privada SSH codificada em Base64. A chave privada SSH deve estar no formato OpenSSH. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Especifique `privateKeyPath` ou `privateKeyContent`. |
| Senha | Especifique a senha/frase secreta para descriptografar a chave particular se o arquivo de chave for protegido por uma frase secreta. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim, se o arquivo de chave privada for protegido por uma frase secreta. |

> [!NOTE]
> O conector SFTP fornece suporte para a chave OpenSSH RSA/DSA. Certifique-se de que o conteúdo do arquivo da chave inicia com "-----BEGIN [RSA/DSA] PRIVATE KEY-----". Se o arquivo da chave privada for um arquivo ppk-format, utilize a ferramenta Putty para converter de .ppk para o formato OpenSSH. 

**Exemplo 1: Autenticação SshPublicKey usando o filePath da chave privada**

```json
{
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo 2: Autenticação SshPublicKey usando o conteúdo da chave privada**

```json
{
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "<username>",
            "privateKeyContent": {
                "type": "SecureString",
                "value": "<base64 string of the private key content>"
            },
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
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

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre conjuntos de dados. Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados de SFTP.

Para copiar dados do SFTP, defina a propriedade type do conjunto de dados como **FileShare**. Há suporte para as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como: **FileShare** |Sim |
| folderPath | Caminho para a pasta. O filtro curinga é permitido; os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único); use `^` para escape se o nome de arquivo real tiver curinga ou esse caractere interno de escape. <br/><br/>Exemplos: rootfolder/subfolder/; veja mais exemplos em [Exemplos de filtro de pasta e arquivo](#folder-and-file-filter-examples). |Sim |
| fileName |  **Filtro de nome ou curinga** para os arquivos em "folderPath" especificado. Se você não especificar um valor para essa propriedade, o conjunto de dados apontará para todos os arquivos na pasta. <br/><br/>Para filtro, os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único).<br/>– Exemplo 1: `"fileName": "*.csv"`<br/>– Exemplo 2: `"fileName": "???20180427.txt"`<br/>Use `^` como escape se o nome real da pasta tiver curingas ou esse caractere de escape. |Não  |
| modifiedDatetimeStart | Filtro de arquivos com base no atributo: Última Modificação. Os arquivos serão selecionados se a hora da última alteração estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário de UTC no formato "2018-12-01T05:00:00Z". <br/><br/> As propriedades podem ser NULL, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o valor de data e hora, mas `modifiedDatetimeEnd` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é maior ou igual ao valor de data e hora.  Quando `modifiedDatetimeEnd` tem o valor de data e hora, mas `modifiedDatetimeStart` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é menor que o valor de data e hora.| Não  |
| modifiedDatetimeEnd | Filtro de arquivos com base no atributo: Última Modificação. Os arquivos serão selecionados se a hora da última alteração estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário de UTC no formato "2018-12-01T05:00:00Z". <br/><br/> As propriedades podem ser NULL, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o valor de data e hora, mas `modifiedDatetimeEnd` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é maior ou igual ao valor de data e hora.  Quando `modifiedDatetimeEnd` tem o valor de data e hora, mas `modifiedDatetimeStart` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é menor que o valor de data e hora.| Não  |
| formato | Se você quiser **copiar arquivos no estado em que se encontram** entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas duas definições de conjunto de dados de entrada e de saída.<br/><br/>Se você quer analisar arquivos com um formato específico, os seguintes tipos de formato de arquivo têm suporte: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Defina a propriedade **type** sob formato como um desses valores. Para saber mais, veja as seções [Formato de texto](supported-file-formats-and-compression-codecs.md#text-format), [Formato Json](supported-file-formats-and-compression-codecs.md#json-format), [Formato Avro](supported-file-formats-and-compression-codecs.md#avro-format), [Formato Orc](supported-file-formats-and-compression-codecs.md#orc-format), e [Formato Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Não (somente para o cenário de cópia binária) |
| compactação | Especifique o tipo e o nível de compactação para os dados. Para obter mais informações, consulte [Formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Tipos compatíveis são: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>Níveis compatíveis são: **Ideal** e **Mais Rápido**. |Não  |

>[!TIP]
>Para copiar todos os arquivos em uma pasta, especifique **folderPath** somente.<br>Para copiar um único arquivo com um determinado nome, especifique **folderPath** com parte da pasta e **fileName** com nome de arquivo.<br>Para copiar um subconjunto de arquivos em uma pasta, especifique **folderPath** com parte da pasta e **fileName** com filtro curinga.

>[!NOTE]
>Se você estivesse usando a propriedade "fileFilter" para o filtro de arquivo, ele ainda tem suporte como-é, enquanto são sugeridos para usar o novo recurso de filtro adicionado ao "fileName" no futuro.

**Exemplo:**

```json
{
    "name": "SFTPDataset",
    "type": "Datasets",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<SFTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte SFTP.

### <a name="sftp-as-source"></a>SFTP como fonte

Para copiar dados do SFTP, defina o tipo de fonte na atividade de cópia como **FileSystemSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type da fonte da atividade de cópia deve ser definida como: **FileSystemSource** |Sim |
| recursiva | Indica se os dados são lidos recursivamente a partir das subpastas ou somente da pasta especificada. Observe que quando o recursivo estiver definido como verdadeiro e o coletor for um armazenamento baseado em arquivo, subpasta/pasta vazia não será copiada/criada no coletor.<br/>Os valores permitidos são: **true** (padrão), **false** | Não  |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SFTP input dataset name>",
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
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Exemplos de filtro de pasta e arquivo

Esta seção descreve o comportamento resultante do caminho da pasta e do nome de arquivo com filtros curinga.

| folderPath | fileName | recursiva | Estrutura da pasta de origem e resultado do filtro (os arquivos em **negrito** são recuperados)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (vazio, usar padrão) | falso | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5.csv<br/>OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| `Folder*` | (vazio, usar padrão) | verdadeiro | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| `Folder*` | `*.csv` | falso | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5.csv<br/>OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| `Folder*` | `*.csv` | verdadeiro | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).
