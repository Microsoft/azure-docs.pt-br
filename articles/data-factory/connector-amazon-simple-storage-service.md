---
title: Copiar dados do Amazon S3 (Simple Storage Service)
description: Saiba mais sobre como copiar dados do Amazon S3 (Simple Storage Service) para armazenamentos de dados de coletor com suporte por meio do Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/14/2021
ms.openlocfilehash: 82871a09916b2b64f74e25088f5e75ac60a40678
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202497"
---
# <a name="copy-data-from-amazon-simple-storage-service-by-using-azure-data-factory"></a>Copiar dados do Amazon Simple Storage Service usando Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
>
> * [Versão 1](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [Versão atual](connector-amazon-simple-storage-service.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como copiar dados do Amazon S3 (Simple Storage Service). Para saber mais sobre o Azure Data Factory, leia as [artigo introdutório](introduction.md).

>[!TIP]
>Para saber mais sobre o cenário de migração de dados do Amazon S3 para o armazenamento do Azure, confira [usar o Azure data Factory para migrar dados do Amazon S3 para o armazenamento do Azure](data-migration-guidance-s3-azure-storage.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector Amazon S3 tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/coletor compatível](copy-activity-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)
- [Excluir atividade](delete-activity.md)

Especificamente, esse conector Amazon S3 dá suporte à cópia de arquivos como está ou à análise de arquivos com os [formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md). Você também pode optar por [preservar os metadados do arquivo durante a cópia](#preserve-metadata-during-copy). O conector usa a [Assinatura da AWS versão 4](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) para autenticar solicitações para S3.

>[!TIP]
>Você pode usar esse conector Amazon S3 para copiar dados de *qualquer provedor de armazenamento compatível com S3*, como o [Google Cloud Storage](connector-google-cloud-storage.md). Especifique a URL do serviço correspondente na configuração do serviço vinculado.

## <a name="required-permissions"></a>Permissões necessárias

Para copiar dados do Amazon S3, verifique se você recebeu as seguintes permissões para operações de objeto do Amazon S3: `s3:GetObject` e `s3:GetObjectVersion` .

Se você usar a interface do usuário do data Factory para criar, `s3:ListAllMyBuckets` `s3:ListBucket` / `s3:GetBucketLocation` serão necessárias permissões adicionais para operações como testar a conexão com o serviço vinculado e navegar da raiz. Se você não quiser conceder essas permissões, poderá escolher as opções "testar a conexão com o caminho do arquivo" ou "procurar a partir do caminho especificado" na interface do usuário.

Para obter a lista completa de permissões do Amazon S3, consulte [especificando permissões em uma política](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html) no site do AWS.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

As seções seguintes fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao Amazon S3.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para um serviço vinculado do Amazon S3:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade **Type** deve ser definida como **AmazonS3**. | Sim |
| authenticationType | Especifique o tipo de autenticação usado para se conectar ao Amazon S3. Você pode optar por usar chaves de acesso para uma conta de IAM (gerenciamento de acesso e identidade) AWS ou [credenciais de segurança temporárias](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html).<br>Os valores permitidos são: `AccessKey` (padrão) e `TemporarySecurityCredentials` . |No |
| accessKeyId | ID da chave de acesso secreta. |Sim |
| secretAccessKey | A chave de acesso do secreta em si. Marque esse campo como **SecureString** para armazená-lo com segurança no Data Factory ou [referencie um segredo armazenado no Cofre de Chaves do Azure](store-credentials-in-key-vault.md). |Sim |
| sessionToken | Aplicável ao usar autenticação [temporária de credenciais de segurança](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) . Saiba como [solicitar credenciais de segurança temporárias](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_request.html#api_getsessiontoken) de AWS.<br>Observação a credencial temporária AWS expira entre 15 minutos e 36 horas com base nas configurações. Verifique se sua credencial é válida quando a atividade é executada, especialmente para carga de trabalho operacional, por exemplo, você pode atualizá-la periodicamente e armazená-la em Azure Key Vault.<br>Marque esse campo como **SecureString** para armazená-lo com segurança no Data Factory ou [referencie um segredo armazenado no Cofre de Chaves do Azure](store-credentials-in-key-vault.md). |No |
| serviceUrl | Especifique o ponto de extremidade S3 personalizado se você estiver copiando dados de um provedor de armazenamento compatível com S3 que não seja o serviço do Amazon S3 oficial. Por exemplo, para copiar dados do Google Cloud Storage, especifique `https://storage.googleapis.com`. | Não |
| forcePathStyle | Indica se o acesso ao [estilo de caminho](https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html#path-style-access) S3 deve ser usado em vez do acesso ao estilo hospedado virtual. Os valores permitidos são: **false** (padrão), **true**.<br>Se você estiver se conectando ao provedor de armazenamento compatível com S3 que não seja o serviço do Amazon S3 oficial e esse repositório de dados exigir acesso ao estilo de caminho (por exemplo, [armazenamento em nuvem Oracle](https://docs.oracle.com/iaas/Content/Object/Tasks/s3compatibleapi.htm)), defina essa propriedade como true. Verifique a documentação de cada repositório de dados sobre se o acesso ao estilo de caminho é necessário ou não. |Não |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o tempo de execução de integração do Azure ou o tempo de execução de integração auto-hospedado (se o armazenamento de dados estiver em uma rede privada). Se essa propriedade não for especificada, o serviço usará o tempo de execução de integração do Azure padrão. |No |

>[!TIP]
>Especifique a URL do serviço S3 personalizado se você estiver copiando dados de um armazenamento compatível com S3 que não seja o serviço do Amazon S3 oficial.

**Exemplo: usando a autenticação de chave de acesso**

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: usando autenticação de credencial de segurança temporária**

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "authenticationType": "TemporarySecurityCredentials",
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            },
            "sessionToken": {
                "type": "SecureString",
                "value": "<session token>"
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

As propriedades a seguir têm suporte para o Amazon S3 em `location` configurações em um conjunto de acordo com base em formato:

| Propriedade   | Descrição                                                  | Obrigatório |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | A propriedade **Type** em `location` em um DataSet deve ser definida como **AmazonS3Location**. | Sim      |
| bucketName | O nome do bucket S3.                                          | Sim      |
| folderPath | O caminho para a pasta no Bucket especificado. Se você quiser usar um curinga para filtrar a pasta, ignore essa configuração e especifique-a nas configurações de origem da atividade. | Não       |
| fileName   | O nome do arquivo no Bucket e no caminho da pasta fornecidos. Se você quiser usar um curinga para filtrar arquivos, ignore essa configuração e especifique isso nas configurações de origem da atividade. | Não       |
| version | A versão do objeto S3 se o controle de versão do S3 está habilitado. Se não for especificado, a versão mais recente será buscada. |Não |

**Exemplo:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AmazonS3Location",
                "bucketName": "bucketname",
                "folderPath": "folder/subfolder"
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

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades às quais a origem do Amazon S3 dá suporte.

### <a name="amazon-s3-as-a-source-type"></a>Amazon S3 como um tipo de fonte

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As propriedades a seguir têm suporte para o Amazon S3 em `storeSettings` configurações em uma fonte de cópia baseada em formato:

| Propriedade                 | Descrição                                                  | Obrigatório                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | A propriedade **Type** em `storeSettings` deve ser definida como **AmazonS3ReadSettings**. | Yes                                                         |
| **_Localize os arquivos a serem copiados:_* _ |  |  |
| OPÇÃO 1: caminho estático<br> | Copiar do caminho do bucket ou de pasta/arquivo especificado no conjunto de dados. Se você quiser copiar todos os arquivos de um Bucket ou pasta, especifique também `wildcardFileName` como `_` . |  |
| OPÇÃO DOIS: Prefixo S3<br>- prefixo | Prefixo do nome da chave S3 sob o Bucket especificado configurado em um conjunto de dados para filtrar os arquivos S3 de origem. As chaves S3 cujos nomes começam com `bucket_in_dataset/this_prefix` são selecionadas. Ele utiliza o filtro do lado do serviço S3's, que fornece melhor desempenho do que um filtro curinga.<br/><br/>Quando você usa o prefixo e opta por copiar para o coletor baseado em arquivo com a hierarquia de preservação, observe que o subcaminho após o último "/" no prefixo será preservado. Por exemplo, você tem origem  `bucket/folder/subfolder/file.txt` e configura o prefixo como `folder/sub` , então o caminho do arquivo preservado é `subfolder/file.txt` . | Não |
| OPÇÃO 3: curinga<br>- wildcardFolderPath | O caminho da pasta com caracteres curinga no Bucket especificado configurado em um conjunto de dados para filtrar as pastas de origem. <br>Os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único). Use `^` para escapar se o nome da pasta tiver um curinga ou este caractere de escape dentro de. <br>Veja mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). | Não                                            |
| OPÇÃO 3: curinga<br>- wildcardFileName | O nome do arquivo com caracteres curinga no Bucket e caminho da pasta fornecidos (ou caminho da pasta curinga) para filtrar os arquivos de origem. <br>Os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único). Use `^` para escapar se o nome do arquivo tiver um curinga ou este caractere de escape dentro de.  Veja mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). | Sim |
| OPÇÃO 4: uma lista de arquivos<br>- fileListPath | Indica a cópia de um determinado conjunto de arquivos. Aponte para um arquivo de texto que inclui uma lista de arquivos que você deseja copiar, um arquivo por linha, que é o caminho relativo para o caminho configurado no conjunto de um.<br/>Quando você estiver usando essa opção, não especifique um nome de arquivo no conjunto de um. Veja mais exemplos em [Exemplos de lista de arquivos](#file-list-examples). |Não |
| ***Configurações adicionais:** _ |  | |
| recursiva | Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. Observe que quando _ *recursivo** é definido como **true** e o coletor é um armazenamento baseado em arquivo, uma pasta ou subpasta vazia não é copiada ou criada no coletor. <br>Os valores permitidos são **true** (padrão) e **false**.<br>Essa propriedade não se aplica quando você configura `fileListPath`. |Não |
| deleteFilesAfterCompletion | Indica se os arquivos binários serão excluídos do repositório de origem após a movimentação com êxito para o repositório de destino. A exclusão do arquivo é por arquivo, portanto, quando a atividade de cópia falhar, você verá que alguns arquivos já foram copiados para o destino e excluídos da origem, enquanto outros ainda permanecem no repositório de origem. <br/>Esta propriedade só é válida no cenário de cópia de arquivos binários. O valor padrão: false. |Não |
| modifiedDatetimeStart    | Os arquivos são filtrados com base no atributo: última modificação. <br>Os arquivos serão escolhidos se a hora da última alteração estiver dentro do período entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. O tempo é aplicado a um fuso horário UTC no formato "2018-12-01T05:00:00Z". <br> As propriedades podem ser **nulas**, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de um.  Quando `modifiedDatetimeStart` tem um valor DateTime, mas `modifiedDatetimeEnd` é **nulo**, os arquivos cujo último atributo modificado é maior ou igual ao valor DateTime serão selecionados.  Quando `modifiedDatetimeEnd` tem um valor DateTime, mas `modifiedDatetimeStart` é **nulo**, os arquivos cujo último atributo modificado é menor que o valor DateTime será selecionado.<br/>Essa propriedade não se aplica quando você configura `fileListPath`. | Não                                            |
| modifiedDatetimeEnd      | Mesmo que acima.                                               | Não                                                          |
| enablePartitionDiscovery | Para arquivos que são particionados, especifique se deseja analisar as partições do caminho do arquivo e adicioná-las como colunas de origem adicionais.<br/>Os valores permitidos são **false** (padrão) e **true**. | No                                            |
| partitionRootPath | Quando a descoberta de partição estiver habilitada, especifique o caminho raiz absoluto para ler as pastas particionadas como colunas de dados.<br/><br/>Se não for especificado, por padrão,<br/>-Quando você usa o caminho do arquivo no conjunto de programas ou na lista de arquivos na origem, o caminho raiz da partição é o caminho configurado no conjunto de um.<br/>-Quando você usa o filtro de pasta curinga, o caminho raiz da partição é o subcaminho antes do primeiro caractere curinga.<br/>-Quando você usa o prefixo, o caminho raiz da partição é subcaminho antes do último "/". <br/><br/>Por exemplo, supondo que você configure o caminho no conjunto de um como "raiz/pasta/ano = 2020/mês = 08/dia = 27":<br/>-Se você especificar o caminho raiz da partição como "raiz/pasta/ano = 2020", a atividade de cópia irá gerar mais duas colunas `month` e `day` com o valor "08" e "27", respectivamente, além das colunas dentro dos arquivos.<br/>-Se o caminho raiz da partição não for especificado, nenhuma coluna extra será gerada. | Não                                            |
| maxConcurrentConnections | O número de conexões simultâneas com o armazenamento de dados. Especifique somente quando desejar limitar as conexões simultâneas ao armazenamento de dados. | Não                                                          |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
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
                    "type": "AmazonS3ReadSettings",
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

### <a name="folder-and-file-filter-examples"></a>Exemplos de filtro de pasta e arquivo

Esta seção descreve o comportamento resultante do caminho da pasta e do nome de arquivo com filtros curinga.

| bucket | chave | recursiva | Estrutura da pasta de origem e resultado do filtro (os arquivos em negrito são recuperados)|
|:--- |:--- |:--- |:--- |
| bucket | `Folder*/*` | false | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| bucket | `Folder*/*` | true | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| bucket | `Folder*/*.csv` | false | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| bucket | `Folder*/*.csv` | true | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |

### <a name="file-list-examples"></a>Exemplos de lista de arquivos

Esta seção descreve o comportamento resultante do uso de um caminho de lista de arquivos em uma origem de atividade de cópia.

Suponha que você tenha a seguinte estrutura de pasta de origem e queira copiar os arquivos em negrito:

| Exemplo de estrutura de origem                                      | Conteúdo em FileListToCopy.txt                             | Configuração de Data Factory                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadados<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **No conjunto de dados:**<br>– Bucket: `bucket`<br>- Caminho da pasta: `FolderA`<br><br>**Na origem da atividade de cópia:**<br>- Caminho da lista de arquivos: `bucket/Metadata/FileListToCopy.txt` <br><br>O caminho da lista de arquivos aponta para um arquivo de texto no mesmo armazenamento de dados que inclui uma lista de arquivos que você deseja copiar, um arquivo por linha, com o caminho relativo para o caminho configurado no conjunto de dados. |

## <a name="preserve-metadata-during-copy"></a>Preservar metadados durante a cópia

Ao copiar arquivos do Amazon S3 para o Azure Data Lake Storage Gen2 ou o armazenamento de BLOBs do Azure, você pode optar por preservar os metadados do arquivo junto com os dados. Saiba mais em [Preservar metadados](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="lookup-activity-properties"></a>Pesquisar propriedades de atividades

Para saber detalhes sobre as propriedades, verifique [Atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Propriedades de atividade GetMetadata

Para obter detalhes sobre as propriedades, verifique a [atividade GetMetadata](control-flow-get-metadata-activity.md). 

## <a name="delete-activity-properties"></a>Excluir propriedades da atividade

Para obter detalhes sobre as propriedades, marque a [atividade de exclusão](delete-activity.md).

## <a name="legacy-models"></a>Modelos herdados

>[!NOTE]
>Os modelos a seguir ainda têm suporte como são para compatibilidade com versões anteriores. Sugerimos que você use o novo modelo mencionado anteriormente. A interface do usuário de criação do Data Factory mudou para gerar o novo modelo.

### <a name="legacy-dataset-model"></a>Modelo de conjunto de dados herdado

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade **Type** do conjunto de conjuntos deve ser definida como **AmazonS3Object**. |Sim |
| bucketName | O nome do bucket S3. Não há suporte para o filtro curinga. |Sim para a atividade de cópia ou pesquisa, não para a atividade GetMetadata |
| chave | O nome ou filtro curinga da chave de objeto S3 no Bucket especificado. Aplica-se somente quando a propriedade **prefix** não é especificada. <br/><br/>O filtro curinga tem suporte para a parte da pasta e o nome do arquivo. Os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único).<br/>– Exemplo 1: `"key": "rootfolder/subfolder/*.csv"`<br/>– Exemplo 2: `"key": "rootfolder/subfolder/???20180427.txt"`<br/>Consulte mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). Use `^` para escapar se a pasta ou o nome de arquivo real tiver um curinga ou este caractere de escape dentro de. |Não |
| prefixo | Prefixo da chave do objeto S3. Objetos cujas chaves começam com esse prefixo serão selecionados. Aplica-se somente quando a propriedade de **chave** não é especificada. |Não |
| version | A versão do objeto S3 se o controle de versão do S3 está habilitado. Se uma versão não for especificada, a versão mais recente será buscada. |Não |
| modifiedDatetimeStart | Os arquivos são filtrados com base no atributo: última modificação. Os arquivos serão escolhidos se a hora da última alteração estiver dentro do período entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário de UTC no formato "2018-12-01T05:00:00Z". <br/><br/> Lembre-se de que a habilitação dessa configuração afetará o desempenho geral da movimentação de dados quando você quiser filtrar grandes quantidades de arquivos. <br/><br/> As propriedades podem ser **nulas**, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de um.  Quando `modifiedDatetimeStart` tem um valor DateTime, mas `modifiedDatetimeEnd` é **nulo**, os arquivos cujo último atributo modificado é maior ou igual ao valor DateTime serão selecionados.  Quando `modifiedDatetimeEnd` tem um valor DateTime, mas `modifiedDatetimeStart` é nulo, os arquivos cujo último atributo modificado é menor que o valor DateTime será selecionado.| Não |
| modifiedDatetimeEnd | Os arquivos são filtrados com base no atributo: última modificação. Os arquivos serão escolhidos se a hora da última alteração estiver dentro do período entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário de UTC no formato "2018-12-01T05:00:00Z". <br/><br/> Lembre-se de que a habilitação dessa configuração afetará o desempenho geral da movimentação de dados quando você quiser filtrar grandes quantidades de arquivos. <br/><br/> As propriedades podem ser **nulas**, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de um.  Quando `modifiedDatetimeStart` tem um valor DateTime, mas `modifiedDatetimeEnd` é **nulo**, os arquivos cujo último atributo modificado é maior ou igual ao valor DateTime serão selecionados.  Quando `modifiedDatetimeEnd` tem um valor DateTime, mas `modifiedDatetimeStart` é **nulo**, os arquivos cujo último atributo modificado é menor que o valor DateTime será selecionado.| Não |
| format | Se você quiser copiar arquivos no estado em que se encontram entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas definições de conjunto de dados de entrada e de saída.<br/><br/>Se você quer analisar ou gerar arquivos com um formato específico, os seguintes tipos de formato de arquivo são compatíveis: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Defina a propriedade **type** sob **format** para um desses valores. Para saber mais, veja as seções [Formato de texto](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Formato JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Formato Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Formato Orc](supported-file-formats-and-compression-codecs-legacy.md#orc-format) e [Formato Parquet](supported-file-formats-and-compression-codecs-legacy.md#parquet-format). |Não (somente para o cenário de cópia binária) |
| compactação | Especifique o tipo e o nível de compactação para os dados. Para obter mais informações, consulte [Formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Os tipos com suporte são: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>Os níveis de suporte são **Ideal** e **Mais rápido**. |Não |

>[!TIP]
>Para copiar todos os arquivos em uma pasta, especifique **bucketname** para o Bucket e o **prefixo** da parte da pasta.
>
>Para copiar um único arquivo com um nome específico, especifique **bucketname** para o Bucket e a **chave** para a parte da pasta mais o nome do arquivo.
>
>Para copiar um subconjunto de arquivos em uma pasta, especifique **bucketname** para o Bucket e a **chave** para a parte da pasta, além do filtro curinga.

**Exemplo: usando o prefixo**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "prefix": "testFolder/test",
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

**Exemplo: usando a chave e a versão (opcional)**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "key": "testFolder/testfile.csv.gz",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
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

### <a name="legacy-source-model-for-the-copy-activity"></a>Modelo de origem herdado para a atividade de cópia

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade **Type** da fonte da atividade de cópia deve ser definida como **FileSystemName**. |Sim |
| recursiva | Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. Observe que quando **recursivo** é definido como **true** e o coletor é um armazenamento baseado em arquivo, uma pasta ou subpasta vazia não será copiada ou criada no coletor.<br/>Os valores permitidos são **true** (padrão) e **false**. | Não |
| maxConcurrentConnections | O número das conexões para se conectar ao armazenamento de dados simultaneamente. Especifique somente quando desejar limitar as conexões simultâneas ao armazenamento de dados. | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon S3 input dataset name>",
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
Para obter uma lista de armazenamentos de dados que a atividade de cópia no Azure Data Factory dá suporte como fontes e coletores, consulte [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
