---
title: Copiar dados do Google Cloud Storage usando Azure Data Factory
description: Saiba mais sobre como copiar dados do Google Cloud Storage para armazenamentos de dados de coletor com suporte por meio do Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/14/2020
ms.author: jingwang
ms.openlocfilehash: 4d8ddb89055d318aaea9ada7f38f908223ff5a57
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100388440"
---
# <a name="copy-data-from-google-cloud-storage-by-using-azure-data-factory"></a>Copiar dados do Google Cloud Storage usando Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como copiar dados a partir do Google Cloud Storage (GCS). Para saber mais sobre o Azure Data Factory, leia as [artigo introdutório](introduction.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector de armazenamento doo Google Cloud Storage oferece suporte às seguintes atividades:

- [atividade de cópia](copy-activity-overview.md) de com [matriz de fonte/coletor com suporte](copy-activity-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)
- [Excluir atividade](delete-activity.md)

Especificamente, esse conector do Google Cloud Storage dá suporte à cópia de arquivos como está ou à análise de arquivos com os [formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md). Ele aproveita a interoperabilidade compatível com S3 de GCS.

## <a name="prerequisites"></a>Pré-requisitos

A configuração a seguir é necessária em sua conta de armazenamento do Google Cloud:

1. Permitir a interoperabilidade para sua conta do Google Cloud Storage
2. Defina o projeto padrão que contém os dados que você deseja copiar do Bucket de GCS de destino.
3. Crie uma conta de serviço e defina os níveis de permissões certos usando a nuvem IAM em GCP. 
4. Gere as chaves de acesso para esta conta de serviço.

![Recuperar a chave de acesso para o Google Cloud Storage](media/connector-google-cloud-storage/google-storage-cloud-settings.png)

## <a name="required-permissions"></a>Permissões necessárias

Para copiar dados do Google Cloud Storage, verifique se você recebeu as seguintes permissões para operações de objeto: ` storage.objects.get` e ` storage.objects.list` .

Se você usar a interface do usuário do Data Factory para criar, ` storage.buckets.list` será necessária permissão adicional para operações como testar a conexão com o serviço vinculado e navegar da raiz. Se você não quiser conceder essa permissão, poderá escolher as opções "testar a conexão com o caminho do arquivo" ou "procurar a partir do caminho especificado" na interface do usuário.

Para obter a lista completa de funções de armazenamento em nuvem do Google e permissões associadas, consulte [funções iam para armazenamento em nuvem](https://cloud.google.com/storage/docs/access-control/iam-roles) no site do Google Cloud.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao conector do Google Cloud Storage.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para os serviços vinculados do Google Cloud Storage:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade **Type** deve ser definida como **GoogleCloudStorage**. | Sim |
| accessKeyId | ID da chave de acesso secreta. Para ver a chave de acesso e o segredo, confira [Prerrequisitos](#prerequisites). |Sim |
| secretAccessKey | A chave de acesso do secreta em si. Marque este campo como **SecureString** para armazená-lo com segurança no data Factory ou [faça referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). |Sim |
| serviceUrl | Especifique o ponto de extremidade GCS personalizado como `https://storage.googleapis.com` . | Sim |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o tempo de execução de integração do Azure ou o tempo de execução de integração auto-hospedado (se o armazenamento de dados estiver em uma rede privada). Se essa propriedade não for especificada, o serviço usará o tempo de execução de integração do Azure padrão. |Não |

Aqui está um exemplo:

```json
{
    "name": "GoogleCloudStorageLinkedService",
    "properties": {
        "type": "GoogleCloudStorage",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            },
            "serviceUrl": "https://storage.googleapis.com"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As propriedades a seguir têm suporte para o armazenamento em nuvem do Google em `location` configurações em um conjunto de acordo com base em formato:

| Propriedade   | Descrição                                                  | Obrigatório |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | A propriedade **Type** em `location` no DataSet deve ser definida como **GoogleCloudStorageLocation**. | Sim      |
| bucketName | O nome do bucket GCS.                                          | Sim      |
| folderPath | O caminho para a pasta sob o bucket fornecido. Se você quiser usar um curinga para filtrar a pasta, ignore essa configuração e especifique isso nas configurações de origem da atividade. | Não       |
| fileName   | O nome do arquivo no Bucket e no caminho da pasta fornecidos. Se você quiser usar um curinga para filtrar os arquivos, ignore essa configuração e especifique isso nas configurações de origem da atividade. | Não       |

**Exemplo:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Google Cloud Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "GoogleCloudStorageLocation",
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

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades que a fonte de armazenamento do Google Cloud suporta.

### <a name="google-cloud-storage-as-a-source-type"></a>Armazenamento em nuvem do Google como um tipo de origem

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As propriedades a seguir têm suporte para o armazenamento em nuvem do Google em `storeSettings` configurações em uma fonte de cópia baseada em formato:

| Propriedade                 | Descrição                                                  | Obrigatório                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | A propriedade **Type** em `storeSettings` deve ser definida como **GoogleCloudStorageReadSettings**. | Sim                                                         |
| ***Localize os arquivos a serem copiados:*** |  |  |
| OPÇÃO 1: caminho estático<br> | Copiar do caminho do bucket ou de pasta/arquivo especificado no conjunto de dados. Se você quiser copiar todos os arquivos de um Bucket ou pasta, especifique também `wildcardFileName` como `*` . |  |
| OPÇÃO DOIS: Prefixo GCS<br>– prefix | Prefixo do nome da chave de GCS no Bucket especificado configurado no conjunto de dados para filtrar os arquivos de GCS de origem. As chaves de GCS cujos nomes começam com `bucket_in_dataset/this_prefix` são selecionadas. Ele utiliza o filtro do lado do serviço de GCS, que fornece melhor desempenho do que um filtro curinga. | Não |
| OPÇÃO 3: curinga<br>- wildcardFolderPath | O caminho da pasta com caracteres curinga no Bucket especificado configurado em um conjunto de dados para filtrar as pastas de origem. <br>Os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único). Use `^` para escapar se o nome da pasta tiver um curinga ou este caractere de escape dentro de. <br>Veja mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). | Não                                            |
| OPÇÃO 3: curinga<br>- wildcardFileName | O nome do arquivo com caracteres curinga no Bucket e caminho da pasta fornecidos (ou caminho da pasta curinga) para filtrar os arquivos de origem. <br>Os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único). Use `^` para escapar se o nome do arquivo tiver um curinga ou este caractere de escape dentro de.  Veja mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). | Sim |
| OPÇÃO 3: uma lista de arquivos<br>- fileListPath | Indica a cópia de um determinado conjunto de arquivos. Aponte para um arquivo de texto que inclui uma lista de arquivos que você deseja copiar, um arquivo por linha, que é o caminho relativo para o caminho configurado no conjunto de um.<br/>Quando você estiver usando essa opção, não especifique o nome do arquivo no conjunto de conjuntos. Veja mais exemplos em [Exemplos de lista de arquivos](#file-list-examples). |Não |
| ***Configurações adicionais:*** |  | |
| recursiva | Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. Observe que quando **recursivo** é definido como **true** e o coletor é um armazenamento baseado em arquivo, uma pasta ou subpasta vazia não é copiada ou criada no coletor. <br>Os valores permitidos são **true** (padrão) e **false**.<br>Essa propriedade não se aplica quando você configura `fileListPath`. |Não |
| deleteFilesAfterCompletion | Indica se os arquivos binários serão excluídos do repositório de origem após a movimentação com êxito para o repositório de destino. A exclusão do arquivo é por arquivo, portanto, quando a atividade de cópia falhar, você verá que alguns arquivos já foram copiados para o destino e excluídos da origem, enquanto outros ainda permanecem no repositório de origem. <br/>Esta propriedade só é válida no cenário de cópia de arquivos binários. O valor padrão: false. |Não |
| modifiedDatetimeStart    | Os arquivos são filtrados com base no atributo: última modificação. <br>Os arquivos serão escolhidos se a hora da última alteração estiver dentro do período entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário de UTC no formato "2018-12-01T05:00:00Z". <br> As propriedades podem ser **nulas**, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de um.  Quando `modifiedDatetimeStart` tem um valor DateTime, mas `modifiedDatetimeEnd` é **nulo**, os arquivos cujo último atributo modificado é maior ou igual ao valor DateTime serão selecionados.  Quando `modifiedDatetimeEnd` tem um valor DateTime, mas `modifiedDatetimeStart` é **nulo**, os arquivos cujo último atributo modificado é menor que o valor DateTime será selecionado.<br/>Essa propriedade não se aplica quando você configura `fileListPath`. | Não                                            |
| modifiedDatetimeEnd      | Mesmo que acima.                                               | Não                                                          |
| enablePartitionDiscovery | Para arquivos que são particionados, especifique se deseja analisar as partições do caminho do arquivo e adicioná-las como colunas de origem adicionais.<br/>Os valores permitidos são **false** (padrão) e **true**. | Não                                            |
| partitionRootPath | Quando a descoberta de partição estiver habilitada, especifique o caminho raiz absoluto para ler as pastas particionadas como colunas de dados.<br/><br/>Se não for especificado, por padrão,<br/>-Quando você usa o caminho do arquivo no conjunto de programas ou na lista de arquivos na origem, o caminho raiz da partição é o caminho configurado no conjunto de um.<br/>-Quando você usa o filtro de pasta curinga, o caminho raiz da partição é o subcaminho antes do primeiro caractere curinga.<br/><br/>Por exemplo, supondo que você configure o caminho no conjunto de um como "raiz/pasta/ano = 2020/mês = 08/dia = 27":<br/>-Se você especificar o caminho raiz da partição como "raiz/pasta/ano = 2020", a atividade de cópia irá gerar mais duas colunas `month` e `day` com o valor "08" e "27", respectivamente, além das colunas dentro dos arquivos.<br/>-Se o caminho raiz da partição não for especificado, nenhuma coluna extra será gerada. | Não                                            |
| maxConcurrentConnections | O número de conexões simultâneas com o armazenamento. Especifique somente quando desejar limitar as conexões simultâneas ao armazenamento de dados. | Não                                                          |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromGoogleCloudStorage",
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
                    "type": "GoogleCloudStorageReadSettings",
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

Esta seção descreve o comportamento resultante do uso de um caminho de lista de arquivos na origem da atividade de cópia.

Suponha que você tenha a seguinte estrutura de pasta de origem e queira copiar os arquivos em negrito:

| Exemplo de estrutura de origem                                      | Conteúdo em FileListToCopy.txt                             | Configuração de Data Factory                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadados<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **No conjunto de dados:**<br>– Bucket: `bucket`<br>- Caminho da pasta: `FolderA`<br><br>**Na origem da atividade de cópia:**<br>- Caminho da lista de arquivos: `bucket/Metadata/FileListToCopy.txt` <br><br>O caminho da lista de arquivos aponta para um arquivo de texto no mesmo armazenamento de dados que inclui uma lista de arquivos que você deseja copiar, um arquivo por linha, com o caminho relativo para o caminho configurado no conjunto de dados. |

## <a name="lookup-activity-properties"></a>Pesquisar propriedades de atividade

Para saber detalhes sobre as propriedades, verifique [Atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Propriedades de atividade GetMetadata

Para obter detalhes sobre as propriedades, verifique a [atividade GetMetadata](control-flow-get-metadata-activity.md). 

## <a name="delete-activity-properties"></a>Excluir propriedades da atividade

Para obter detalhes sobre as propriedades, marque a [atividade de exclusão](delete-activity.md).

## <a name="legacy-models"></a>Modelos herdados

Se você estivesse usando um conector do Amazon S3 para copiar dados do Google Cloud Storage, ainda há suporte para compatibilidade com versões anteriores. Sugerimos que você use o novo modelo mencionado anteriormente. A interface do usuário de criação do Data Factory mudou para gerar o novo modelo.

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados que a atividade de cópia no Azure Data Factory dá suporte como fontes e coletores, consulte [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
