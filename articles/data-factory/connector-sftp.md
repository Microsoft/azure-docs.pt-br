---
title: Copiar dados de e para o servidor SFTP
description: Saiba como copiar dados de e para o servidor SFTP usando Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: 19b32bed15a4d292a7427d8401e777c7761e45a3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592023"
---
# <a name="copy-data-from-and-to-the-sftp-server-by-using-azure-data-factory"></a>Copiar dados de e para o servidor SFTP usando Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-sftp-connector.md)
> * [Versão atual](connector-sftp.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como copiar dados de e para o servidor FTP seguro (SFTP). Para saber mais sobre o Azure Data Factory, leia as [artigo introdutório](introduction.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

O conector SFTP tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/coletor com suporte](copy-activity-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)
- [Excluir atividade](delete-activity.md)

Especificamente, o conector SFTP dá suporte a:

- Copiar arquivos de e para o servidor SFTP usando a **chave pública SSH** **básica** ou a autenticação **multifator** .
- Copiar arquivos como está ou analisando ou gerando arquivos com os [formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções que se seguem fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao SFTP.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do SFTP:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade Type deve ser definida como *SFTP*. |Sim |
| host | O nome ou endereço IP do servidor SFTP. |Sim |
| porta | A porta na qual o servidor SFTP está escutando.<br/>O valor permitido é um inteiro e o valor padrão é *22*. |Não |
| skipHostKeyValidation | Especifique se deseja ignorar a validação da chave de host.<br/>Os valores permitidos são *true* e *false* (padrão).  | Não |
| hostKeyFingerprint | Especifique a impressão digital da chave do host. | Sim, se "skipHostKeyValidation" for definido como false.  |
| authenticationType | Especifique o tipo de autenticação.<br/>Os valores permitidos são *Basic*, *SshPublicKey* e *Multifactor*. Para obter mais propriedades, consulte a seção [usar autenticação básica](#use-basic-authentication) . Para obter exemplos de JSON, consulte a seção [usar autenticação de chave pública SSH](#use-ssh-public-key-authentication) . |Sim |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Para saber mais, consulte a seção [pré-requisitos](#prerequisites) . Se o Integration Runtime não for especificado, o serviço usará o Azure Integration Runtime padrão. |Não |

### <a name="use-basic-authentication"></a>Usar autenticação básica

Para usar a autenticação básica, defina a propriedade *authenticationType* como *básica* e especifique as propriedades a seguir, além das propriedades genéricas do conector SFTP que foram introduzidas na seção anterior:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| userName | O usuário que tem acesso ao servidor SFTP. |Sim |
| password | A senha do usuário (userName). Marque este campo como uma SecureString para armazená-lo com segurança em seu data factory ou [faça referência a um segredo armazenado em um cofre de chaves do Azure](store-credentials-in-key-vault.md). | Sim |

**Exemplo:**

```json
{
    "name": "SftpLinkedService",
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
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-ssh-public-key-authentication"></a>Usar autenticação de chave pública SSH

Para usar a chave pública SSH, defina a propriedade "authenticationType" como **SshPublicKey** e especifique as propriedades a seguir, além das que são genéricas do conector SFTP apresentadas na última seção:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| userName | O usuário que tem acesso ao servidor SFTP. |Sim |
| privateKeyPath | Especifique o caminho absoluto para o arquivo de chave privada que o Integration Runtime pode acessar. Isso se aplica somente quando o tipo auto-hospedado do tempo de execução de integração é especificado em "connectVia". | Especifique `privateKeyPath` ou `privateKeyContent`.  |
| privateKeyContent | Conteúdo da chave privada SSH codificada em Base64. A chave privada SSH deve estar no formato OpenSSH. Marque este campo como uma SecureString para armazená-lo com segurança em seu data factory ou [faça referência a um segredo armazenado em um cofre de chaves do Azure](store-credentials-in-key-vault.md). | Especifique `privateKeyPath` ou `privateKeyContent`. |
| Senha | Especifique a frase secreta ou a senha para descriptografar a chave privada se o arquivo de chave ou o conteúdo da chave for protegido por uma frase secreta. Marque este campo como uma SecureString para armazená-lo com segurança em seu data factory ou [faça referência a um segredo armazenado em um cofre de chaves do Azure](store-credentials-in-key-vault.md). | Sim, se o arquivo de chave privada ou o conteúdo da chave for protegido por uma frase secreta. |

> [!NOTE]
> O conector SFTP dá suporte a uma chave RSA/DSA OpenSSH. Verifique se o conteúdo do arquivo de chave começa com "-----BEGIN [RSA/DSA]-----chave privada". Se o arquivo de chave privada for um arquivo de formato PPK, use a ferramenta de saída para converter de PPK para o formato OpenSSH. 

**Exemplo 1: Autenticação SshPublicKey usando o filePath da chave privada**

```json
{
    "name": "SftpLinkedService",
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
            "referenceName": "<name of integration runtime>",
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
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-multi-factor-authentication"></a>Usar autenticação multifator

Para usar a autenticação multifator, que é uma combinação de autenticações de chave pública básica e SSH, especifique as informações de nome de usuário, senha e chave privada descritas nas seções acima.

**Exemplo: autenticação multifator**

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<host>",
            "port": 22,
            "authenticationType": "MultiFactor",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            },
            "privateKeyContent": {
                "type": "SecureString",
                "value": "<base64 encoded private key content>"
            },
            "passPhrase": {
                "type": "SecureString",
                "value": "<passphrase for private key>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades que estão disponíveis para definir os conjuntos de valores, consulte o artigo [conjuntos de valores](concepts-datasets-linked-services.md) . 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As propriedades a seguir têm suporte para SFTP em `location` configurações no conjunto de entrada baseado em formato:

| Propriedade   | Descrição                                                  | Obrigatório |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | A propriedade *Type* em `location` DataSet deve ser definida como *SftpLocation*. | Sim      |
| folderPath | O caminho para a pasta. Se você quiser usar um curinga para filtrar a pasta, ignore essa configuração e especifique o caminho nas configurações de origem da atividade. | Não       |
| fileName   | O nome do arquivo sob o folderPath especificado. Se você quiser usar um curinga para filtrar arquivos, ignore essa configuração e especifique o nome do arquivo nas configurações de origem da atividade. | Não       |

**Exemplo:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<SFTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "SftpLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa de seções e propriedades que estão disponíveis para definir atividades, consulte o artigo [pipelines](concepts-pipelines-activities.md) . Esta seção fornece uma lista das propriedades com suporte pela fonte SFTP.

### <a name="sftp-as-source"></a>SFTP como fonte

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As propriedades a seguir têm suporte para SFTP sob as `storeSettings` configurações na fonte de cópia baseada em formato:

| Propriedade                 | Descrição                                                  | Obrigatório                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | A propriedade *Type* em `storeSettings` deve ser definida como *SftpReadSettings*. | Sim                                           |
| ***Localize os arquivos a serem copiados*** |  |  |
| OPÇÃO 1: caminho estático<br> | Copiar do caminho de pasta/arquivo especificado no conjunto de um. Se quiser copiar todos os arquivos de uma pasta, especifique também `wildcardFileName` como `*`. |  |
| OPÇÃO 2: curinga<br>- wildcardFolderPath | O caminho da pasta com caracteres curinga para filtrar as pastas de origem. <br>Curingas permitidos são `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou um único caractere); use `^` para escapar se o nome real da pasta tiver um caractere curinga ou esse caractere de escape dentro. <br>Para obter mais exemplos, consulte [exemplos de filtro de pasta e arquivo](#folder-and-file-filter-examples). | Não                                            |
| OPÇÃO 2: curinga<br>- wildcardFileName | O nome do arquivo com caracteres curinga no folderPath/wildcardFolderPath especificado para filtrar os arquivos de origem. <br>Curingas permitidos são `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou um único caractere); use `^` para escapar se o nome de arquivo real tiver curinga ou este caractere de escape dentro.  Para obter mais exemplos, consulte [exemplos de filtro de pasta e arquivo](#folder-and-file-filter-examples). | Sim |
| OPÇÃO 3: uma lista de arquivos<br>- fileListPath | Indica a cópia de um conjunto de arquivos especificado. Aponte para um arquivo de texto que inclui uma lista de arquivos que você deseja copiar (um arquivo por linha, com o caminho relativo para o caminho configurado no conjunto de um).<br/>Ao usar essa opção, não especifique o nome do arquivo no conjunto de um. Para obter mais exemplos, consulte [exemplos de lista de arquivos](#file-list-examples). |Não |
| ***Configurações adicionais*** |  | |
| recursiva | Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. Quando recursiva é definida como true e o coletor é um armazenamento baseado em arquivo, uma pasta vazia ou subpasta não é copiada ou criada no coletor. <br>Os valores permitidos são *true* (padrão) e *false*.<br>Essa propriedade não se aplica quando você configura `fileListPath`. |Não |
| deleteFilesAfterCompletion | Indica se os arquivos binários serão excluídos do repositório de origem após a movimentação com êxito para o repositório de destino. A exclusão do arquivo é por arquivo, portanto, quando a atividade de cópia falhar, você verá que alguns arquivos já foram copiados para o destino e excluídos da origem, enquanto outros ainda permanecem no repositório de origem. <br/>Esta propriedade só é válida no cenário de cópia de arquivos binários. O valor padrão: false. |Não |
| modifiedDatetimeStart    | Os arquivos são filtrados com base no atributo *modificado pela última vez*. <br>Os arquivos serão selecionados se a hora da última modificação estiver dentro do intervalo de `modifiedDatetimeStart` a `modifiedDatetimeEnd` . A hora é aplicada ao fuso horário UTC no formato *2018-12-01T05:00:00Z*. <br> As propriedades podem ser nulas, o que significa que nenhum filtro de atributo de arquivo é aplicado ao conjunto de valores.  Quando `modifiedDatetimeStart` tem um valor de DateTime, mas `modifiedDatetimeEnd` é nulo, significa que os arquivos cujo último atributo modificado é maior ou igual ao valor de DateTime são selecionados.  Quando `modifiedDatetimeEnd` tem um valor de DateTime, mas `modifiedDatetimeStart` é nulo, significa que os arquivos cujo último atributo modificado é menor que o valor de data e hora são selecionados.<br/>Essa propriedade não se aplica quando você configura `fileListPath`. | Não                                            |
| modifiedDatetimeEnd      | Mesmo que acima.                                               | Não                                            |
| enablePartitionDiscovery | Para arquivos que são particionados, especifique se deseja analisar as partições do caminho do arquivo e adicioná-las como colunas de origem adicionais.<br/>Os valores permitidos são **false** (padrão) e **true**. | Não                                            |
| partitionRootPath | Quando a descoberta de partição estiver habilitada, especifique o caminho raiz absoluto para ler as pastas particionadas como colunas de dados.<br/><br/>Se não for especificado, por padrão,<br/>-Quando você usa o caminho do arquivo no conjunto de programas ou na lista de arquivos na origem, o caminho raiz da partição é o caminho configurado no conjunto de um.<br/>-Quando você usa o filtro de pasta curinga, o caminho raiz da partição é o subcaminho antes do primeiro caractere curinga.<br/><br/>Por exemplo, supondo que você configure o caminho no conjunto de um como "raiz/pasta/ano = 2020/mês = 08/dia = 27":<br/>-Se você especificar o caminho raiz da partição como "raiz/pasta/ano = 2020", a atividade de cópia irá gerar mais duas colunas `month` e `day` com o valor "08" e "27", respectivamente, além das colunas dentro dos arquivos.<br/>-Se o caminho raiz da partição não for especificado, nenhuma coluna extra será gerada. | Não                                            |
| maxConcurrentConnections | O limite superior de conexões simultâneas estabelecidas com o armazenamento de dados durante a execução da atividade. Especifique um valor somente quando desejar limitar as conexões simultâneas.| Não                                            |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
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
                    "type": "SftpReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sftp-as-a-sink"></a>SFTP como um coletor

[!INCLUDE [data-factory-v2-file-sink-formats](../../includes/data-factory-v2-file-sink-formats.md)]

As propriedades a seguir têm suporte para SFTP em `storeSettings` configurações em um coletor de cópia com base em formato:

| Propriedade                 | Descrição                                                  | Obrigatório |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | A propriedade *Type* em `storeSettings` deve ser definida como *SftpWriteSettings*. | Sim      |
| copyBehavior             | Define o comportamento de cópia quando a fonte for de arquivos de um armazenamento de dados baseado em arquivo.<br/><br/>Valores permitidos são:<br/><b>– PreserveHierarchy (padrão)</b>: Preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino.<br/><b>– FlattenHierarchy</b>: Todos os arquivos da pasta de origem estão no primeiro nível da pasta de destino. Os arquivos de destino têm os nomes gerados automaticamente. <br/><b>– MergeFiles</b>: Mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo for especificado, o nome do arquivo mesclado será o nome especificado. Caso contrário, ele será um nome de arquivo gerado automaticamente. | Não       |
| maxConcurrentConnections | O limite superior de conexões simultâneas estabelecidas com o armazenamento de dados durante a execução da atividade. Especifique um valor somente quando desejar limitar as conexões simultâneas. | Não       |
| useTempFileRename | Indique se deseja carregar arquivos temporários e renomeá-los ou gravar diretamente na pasta de destino ou no local do arquivo. Por padrão, Azure Data Factory primeiro grava em arquivos temporários e, em seguida, os renomeia quando o carregamento é concluído. Essa sequência ajuda a (1) evitar conflitos que podem resultar em um arquivo corrompido se você tiver outros processos gravando no mesmo arquivo e (2) verificar se a versão original do arquivo existe durante a transferência. Se o seu servidor SFTP não oferecer suporte a uma operação de renomeação, desabilite essa opção e verifique se você não tem uma gravação simultânea no arquivo de destino. Para obter mais informações, consulte a dica de solução de problemas no final desta tabela. | Não. O valor padrão é *true*. |
| operationTimeout | O tempo de espera antes de expirar cada solicitação de gravação para o servidor SFTP. O valor padrão é 60 minutos (01:00:00).|Não |

>[!TIP]
>Se você receber o erro "UserErrorSftpPathNotFound", "UserErrorSftpPermissionDenied" ou "SftpOperationFail" quando estiver gravando dados em SFTP, e o usuário *SFTP usado tiver* as permissões corretas, verifique se a operação de renomeação do arquivo de suporte do servidor SFTP está funcionando. Se não estiver, desabilite a opção **carregar com o arquivo temporário** ( `useTempFileRename` ) e tente novamente. Para saber mais sobre essa propriedade, consulte a tabela anterior. Se você usar um tempo de execução de integração auto-hospedado para a atividade de cópia, certifique-se de usar a versão 4,6 ou posterior.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
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
                "type": "<source type>"
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings":{
                    "type": "SftpWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Exemplos de filtro de pasta e arquivo

Esta seção descreve o comportamento que resulta do uso de filtros curinga com caminhos de pasta e nomes de arquivo.

| folderPath | fileName | recursiva | Estrutura da pasta de origem e resultado do filtro (os arquivos em **negrito** são recuperados)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (vazio, usar padrão) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5.csv<br/>OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| `Folder*` | (vazio, usar padrão) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5.csv<br/>OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |

### <a name="file-list-examples"></a>Exemplos de lista de arquivos

Esta tabela descreve o comportamento resultante do uso de um caminho de lista de arquivos na origem da atividade de cópia. Ele pressupõe que você tem a seguinte estrutura de pasta de origem e deseja copiar os arquivos que estão em negrito:

| Exemplo de estrutura de origem                                      | Conteúdo em FileListToCopy.txt                             | Configuração de Azure Data Factory                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| root<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadados<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **No conjunto de os:**<br>- Caminho da pasta: `root/FolderA`<br><br>**Na fonte da atividade de cópia:**<br>- Caminho da lista de arquivos: `root/Metadata/FileListToCopy.txt` <br><br>O caminho da lista de arquivos aponta para um arquivo de texto no mesmo armazenamento de dados que inclui uma lista de arquivos que você deseja copiar (um arquivo por linha, com o caminho relativo para o caminho configurado no conjunto de dados). |

## <a name="lookup-activity-properties"></a>Pesquisar propriedades de atividade

Para obter informações sobre propriedades de atividade de pesquisa, consulte [atividade de pesquisa em Azure data Factory](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Propriedades de atividade GetMetadata

Para obter informações sobre as propriedades da atividade GetMetadata, consulte [atividade GetMetadata em Azure data Factory](control-flow-get-metadata-activity.md). 

## <a name="delete-activity-properties"></a>Excluir propriedades da atividade

Para obter informações sobre as propriedades de atividade de exclusão, consulte [excluir atividade no Azure data Factory](delete-activity.md).

## <a name="legacy-models"></a>Modelos herdados

>[!NOTE]
>Os modelos a seguir ainda têm suporte como são para compatibilidade com versões anteriores. É recomendável que você use o novo modelo abordado anteriormente, porque a interface do usuário de criação do Azure Data Factory mudou para gerar o novo modelo.

### <a name="legacy-dataset-model"></a>Modelo de conjunto de dados herdado

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade *Type* do conjunto de conjuntos deve ser definida como *FileShare*. |Sim |
| folderPath | O caminho para a pasta. Há suporte para um filtro curinga. Curingas permitidos são `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou um único caractere); use `^` para escapar se o nome real do arquivo tiver um caractere curinga ou esse caractere de escape dentro. <br/><br/>Exemplos: rootfolder/subfolder/; veja mais exemplos em [Exemplos de filtro de pasta e arquivo](#folder-and-file-filter-examples). |Sim |
| fileName |  **Filtro de nome ou curinga** para os arquivos sob o "FolderPath" especificado. Se você não especificar um valor para essa propriedade, o conjunto de dados apontará para todos os arquivos na pasta. <br/><br/>Para o filtro, os curingas permitidos são `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou a um único caractere).<br/>– Exemplo 1: `"fileName": "*.csv"`<br/>– Exemplo 2: `"fileName": "???20180427.txt"`<br/>Use `^` como escape se o nome real da pasta tiver curingas ou esse caractere de escape. |Não |
| modifiedDatetimeStart | Os arquivos são filtrados com base no atributo *modificado pela última vez*. Os arquivos serão selecionados se a hora da última modificação estiver dentro do intervalo de `modifiedDatetimeStart` a `modifiedDatetimeEnd` . A hora é aplicada ao fuso horário UTC no formato *2018-12-01T05:00:00Z*. <br/><br/> O desempenho geral da movimentação de dados será afetado com a habilitação dessa configuração quando você desejar fazer o filtro de arquivo de um grande número de arquivos. <br/><br/> As propriedades podem ser nulas, o que significa que nenhum filtro de atributo de arquivo é aplicado ao conjunto de valores.  Quando `modifiedDatetimeStart` tem um valor de DateTime, mas `modifiedDatetimeEnd` é nulo, significa que os arquivos cujo último atributo modificado é maior ou igual ao valor de DateTime são selecionados.  Quando `modifiedDatetimeEnd` tem um valor de DateTime, mas `modifiedDatetimeStart` é nulo, significa que os arquivos cujo último atributo modificado é menor que o valor de data e hora são selecionados.| Não |
| modifiedDatetimeEnd | Os arquivos são filtrados com base no atributo *modificado pela última vez*. Os arquivos serão selecionados se a hora da última modificação estiver dentro do intervalo de `modifiedDatetimeStart` a `modifiedDatetimeEnd` . A hora é aplicada ao fuso horário UTC no formato *2018-12-01T05:00:00Z*. <br/><br/> O desempenho geral da movimentação de dados será afetado com a habilitação dessa configuração quando você desejar fazer o filtro de arquivo de um grande número de arquivos. <br/><br/> As propriedades podem ser nulas, o que significa que nenhum filtro de atributo de arquivo é aplicado ao conjunto de valores.  Quando `modifiedDatetimeStart` tem um valor de DateTime, mas `modifiedDatetimeEnd` é nulo, significa que os arquivos cujo último atributo modificado é maior ou igual ao valor de DateTime são selecionados.  Quando `modifiedDatetimeEnd` tem um valor de DateTime, mas `modifiedDatetimeStart` é nulo, significa que os arquivos cujo último atributo modificado é menor que o valor de data e hora são selecionados.| Não |
| format | Se você quiser copiar arquivos no estado em que se encontram entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas definições de conjunto de dados de entrada e de saída.<br/><br/>Se você quiser analisar arquivos com um formato específico, há suporte para os seguintes tipos de formato de arquivo: *TextFormat*, *JsonFormat*, *AvroFormat*, *OrcFormat* e *ParquetFormat*. Defina a propriedade *type* sob formato como um desses valores. Para obter mais informações, consulte [formato de texto](supported-file-formats-and-compression-codecs-legacy.md#text-format), [formato JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [formato Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [formato Orc](supported-file-formats-and-compression-codecs-legacy.md#orc-format)e seções de [formato parquet](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) . |Não (somente para o cenário de cópia binária) |
| compactação | Especifique o tipo e o nível de compactação para os dados. Para obter mais informações, consulte [Formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Os tipos com suporte são: *GZip*, *Deflate*, *BZip2* e *ZipDeflate*.<br/>Os níveis de suporte são *Ideal* e *Mais rápido*. |Não |

>[!TIP]
>Para copiar todos os arquivos em uma pasta, especifique *folderPath* somente.<br>Para copiar um único arquivo com um nome especificado, especifique *FolderPath* com a pasta Part e *filename* com o nome do arquivo.<br>Para copiar um subconjunto de arquivos em uma pasta, especifique *FolderPath* com a parte da pasta e o *nome do arquivo* com o filtro curinga.

>[!NOTE]
>Se você estiver usando a propriedade *FileFilter* para o filtro de arquivo, ele ainda terá suporte como está, mas recomendamos que você use o novo recurso de filtro adicionado ao *nome de arquivo* de Now on.

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

### <a name="legacy-copy-activity-source-model"></a>Modelo de origem da atividade de cópia herdada

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade *Type* da fonte da atividade de cópia deve ser definida como *FileSystemName* |Sim |
| recursiva | Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. Quando recursivo é definido como *true* e o coletor é um armazenamento baseado em arquivo, pastas e subpastas vazias não serão copiadas ou criadas no coletor.<br/>Os valores permitidos são *true* (padrão) e *false* | Não |
| maxConcurrentConnections |O limite superior de conexões simultâneas estabelecidas com o armazenamento de dados durante a execução da atividade. Especifique um valor somente quando desejar limitar as conexões simultâneas.| Não |

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

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como fontes e coletores pela atividade de cópia no Azure Data Factory, consulte [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
