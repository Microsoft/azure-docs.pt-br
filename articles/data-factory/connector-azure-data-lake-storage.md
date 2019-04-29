---
title: Copiar dados de ou para o Azure Data Lake Storage Gen2 usando o Data Factory | Microsoft Docs
description: Saiba como copiar dados para e do Azure Data Lake Storage Gen2 usando o Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: jingwang
ms.openlocfilehash: aba469081bf1f1aa265a55ffbd683ba19bc41b6e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60782083"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Copiar dados de/para o Azure Data Lake Storage Gen2 usando o Azure Data Factory

O Azure Data Lake Storage Gen2 é um conjunto de recursos dedicados à análise de Big Data, criado no [armazenamento de Blobs do Azure](../storage/blobs/storage-blobs-introduction.md). Ele permite que você faça interface com seus dados usando os paradigmas de sistema de arquivos e armazenamento de objetos.

Este artigo descreve como usar a Atividade de Cópia no Azure Data Factory para copiar dados de e para o Data Lake Store Gen2. Ele amplia o artigo [visão geral da Atividade de Cópia](copy-activity-overview.md) que apresenta uma visão geral da Atividade de Cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

É possível copiar dados de qualquer armazenamento de dados de origem com suporte para o Data Lake Store Gen2. Adicionalmente, é possível copiar dados do Data Lake Store Gen2 para qualquer repositório de dados de coletores com suporte. Para obter uma lista de armazenamentos de dados que têm suporte como fontes ou coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md).

Especificamente, esse conector dá suporte para:

- Copiando dados usando chave de conta, entidade de serviço ou identidades gerenciadas para autenticações de recursos do Azure.
- Copiar arquivos no estado em que se encontram ou análise ou geração de arquivos com [formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md).

>[!TIP]
>Se você habilitar o namespace hierárquico, atualmente não haverá nenhuma interoperabilidade das operações entre o Blob e ADLS Gen2 APIs. No caso de você atingir o erro de "ErrorCode=FilesystemNotFound" com a mensagem detalhada como "o sistema de arquivos especificado não existe.", ele é causado pelo coletor especificado sistema de arquivos foi criado por meio da API do Blob em vez da API de ADLS Gen2 em outro lugar. Para corrigir o problema, especifique um novo sistema de arquivos com um nome que não existe como o nome de um contêiner de Blob, e o ADF criará automaticamente esse sistema de arquivos durante a cópia de dados.

>[!NOTE]
>Se você habilitar a opção _"Permitir que serviços Microsoft confiáveis acessem esta conta de armazenamento"_ nas configurações do firewall do Azure Storage, o uso do Azure Integration Runtime para se conectar ao Data Lake Storage Gen2 falhará com erro proibido, pois o ADF não é tratado como serviço Microsoft confiável. Por favor, use o Microsoft Integração Runtime Auto-hospedado como conexão via.

## <a name="get-started"></a>Introdução

>[!TIP]
>Para obter um passo a passo de uso do conector Data Lake Store Gen2, consulte [Carregar dados no Azure Data Lake Store Gen2](load-azure-data-lake-storage-gen2.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre propriedades que são usadas para definir entidades do Data Factory específicas do Data Lake Store Gen2.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

O conector do Azure Data Lake Storage Gen2 suporta os seguintes tipos de autenticação, consulte a seção correspondente em detalhes:

- [Autenticação de chave de conta](#account-key-authentication)
- [Autenticação de entidade de serviço](#service-principal-authentication)
- [Identidades gerenciadas para autenticação de recursos do Azure](#managed-identity)

### <a name="account-key-authentication"></a>Autenticação de chave de conta

Para usar a autenticação de chave de conta de armazenamento, há suporte para as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type deve ser definida como **AzureBlobFS**. |Sim |
| url | Ponto de extremidade para o Data Lake Store Gen2 com o padrão de `https://<accountname>.dfs.core.windows.net`. | Sim | 
| accountKey | Chave de conta para o serviço do Data Lake Store Gen2. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime do Azure ou o Integration Runtime auto-hospedado (se o armazenamento de dados estiver em uma rede privada). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não  |

**Exemplo:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "accountkey": { 
                "type": "SecureString", 
                "value": "<accountkey>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>Autenticação de entidade de serviço

Para usar a autenticação de entidade de serviço, siga estas etapas:

1. Registre uma entidade de aplicativo no Azure Active Directory (Azure AD) seguindo [Registre o aplicativo com um locatário do Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Anote os seguintes valores, que são usados para definir o serviço vinculado:

    - ID do aplicativo
    - Chave do aplicativo
    - ID do locatário

2. Conceda a permissão apropriada à entidade serviço.

    - **Como o código-fonte**, no Gerenciador de armazenamento, conceda pelo menos **leitura + execução** permissão para listar e copiar os arquivos em pastas e subpastas ou conceder **leitura** permissão para copiar um único arquivo. Como alternativa, no controle de acesso (IAM), conceda pelo menos **leitor de dados de Blob de armazenamento** função.
    - **Como coletor**, no Gerenciador de armazenamento, conceda pelo menos **gravar + executar** permissão para criar itens filho na pasta. Como alternativa, no controle de acesso (IAM), conceda pelo menos **Colaborador de dados de Blob de armazenamento** função.

>[!NOTE]
>A lista de pastas a partir do nível da conta ou para testar a conexão, você precisa definir a permissão da entidade de serviço que está sendo concedida a **conta de armazenamento com a permissão de "Execute" no IAM**. Isso é verdadeiro quando você usa o:
>- **Ferramenta Copiar Dados** para criar o pipeline de cópia.
>- **Interface do Usuário do Data Factory** para testar a conexão e navegar por pastas durante a criação. 
>Se você tiver interesse na concessão de permissão no nível da conta, você pode ignorar manualmente conexão de teste e o caminho de entrada durante a criação. Atividade de cópia ainda funcionará desde que a entidade de serviço é concedida com a permissão adequada os arquivos a serem copiados.

Essas propriedades têm suporte no serviço vinculado:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade type deve ser definida como **AzureBlobFS**. |Sim |
| url | Ponto de extremidade para o Data Lake Store Gen2 com o padrão de `https://<accountname>.dfs.core.windows.net`. | Sim | 
| servicePrincipalId | Especifique a ID do cliente do aplicativo. | Sim |
| servicePrincipalKey | Especifique a chave do aplicativo. Marque esse campo como **SecureString** para armazená-lo com segurança no Data Factory ou [referencie um segredo armazenado no Cofre de Chaves do Azure](store-credentials-in-key-vault.md). | Sim |
| tenant | Especifique as informações de locatário (domínio nome ou ID do Locatário) em que o aplicativo reside. Para recuperá-lo, passe o mouse no canto superior direito do portal do Azure. | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime do Azure ou o Integration Runtime auto-hospedado (se o armazenamento de dados estiver em uma rede privada). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não  |

**Exemplo:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Identidades gerenciadas para autenticação de recursos do Azure

Um data factory pode ser associado a uma [identidade gerenciada para recursos do Azure](data-factory-service-identity.md), que representa esse data factory específico. Você pode usar essa identidade de gerenciado diretamente para a autenticação de armazenamento de Blob semelhante a usar sua própria entidade de serviço. Ele permite que este alocador designado acesse e copie dados de/para o seu armazenamento de Blob.

Para usar identidades gerenciadas para autenticação de recursos do Azure, siga estas etapas:

1. [Recuperar informações de identidade gerenciada de fábrica de dados](data-factory-service-identity.md#retrieve-managed-identity) copiando o valor de "Serviço de identidade ID do aplicativo" gerado junto com seu alocador.

2. Conceda a permissão apropriada à identidade gerenciada. 

    - **Como o código-fonte**, no Gerenciador de armazenamento, conceda pelo menos **leitura + execução** permissão para listar e copiar os arquivos em pastas e subpastas ou conceder **leitura** permissão para copiar um único arquivo. Como alternativa, no controle de acesso (IAM), conceda pelo menos **leitor de dados de Blob de armazenamento** função.
    - **Como coletor**, no Gerenciador de armazenamento, conceda pelo menos **gravar + executar** permissão para criar itens filho na pasta. Como alternativa, no controle de acesso (IAM), conceda pelo menos **Colaborador de dados de Blob de armazenamento** função.

>[!NOTE]
>A lista de pastas a partir do nível da conta ou para testar a conexão, você precisa definir a permissão de identidade gerenciada que está sendo concedida a **conta de armazenamento com a permissão de "Execute" no IAM**. Isso é verdadeiro quando você usa o:
>- **Ferramenta Copiar Dados** para criar o pipeline de cópia.
>- **Interface do Usuário do Data Factory** para testar a conexão e navegar por pastas durante a criação. 
>Se você tiver interesse na concessão de permissão no nível da conta, você pode ignorar manualmente conexão de teste e o caminho de entrada durante a criação. Atividade de cópia ainda funcionará desde que a identidade gerenciada é concedida com a permissão adequada os arquivos a serem copiados.

Essas propriedades têm suporte no serviço vinculado:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type deve ser definida como **AzureBlobFS**. |Sim |
| url | Ponto de extremidade para o Data Lake Store Gen2 com o padrão de `https://<accountname>.dfs.core.windows.net`. | Sim | 
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime do Azure ou o Integration Runtime auto-hospedado (se o armazenamento de dados estiver em uma rede privada). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não  |

**Exemplo:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](concepts-datasets-linked-services.md). As propriedades a seguir têm suporte no conjunto de dados do Azure Data Lake Store:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type do conjunto de dados deve ser definida como: **AzureBlobFSFile**. |Sim |
| folderPath | Caminho para a pasta no Data Lake Store Gen2. Se não especificado, apontará para a raiz. <br/><br/>O filtro curinga é permitido; os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único); use `^` para escape se o nome real da pasta tiver um curinga ou esse caractere interno de escape. <br/><br/>Exemplos: sistema de arquivos/pasta /, veja mais exemplos na [exemplos de filtro de arquivo e pasta](#folder-and-file-filter-examples). |Não  |
| fileName | **Filtro de nome ou curinga** para os arquivos em "folderPath" especificado. Se você não especificar um valor para essa propriedade, o conjunto de dados apontará para todos os arquivos na pasta. <br/><br/>Para filtro, os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único).<br/>– Exemplo 1: `"fileName": "*.csv"`<br/>– Exemplo 2: `"fileName": "???20180427.txt"`<br/>Use `^` para se seu nome de arquivo real curinga ou esse caractere de escape dentro de escape.<br/><br/>Quando fileName não for especificado para um conjunto de dados de saída e **preserveHierarchy** não for especificada no coletor de atividade, a atividade de cópia gerará automaticamente o nome do arquivo com o seguinte padrão: "*Data.[GUID da ID de execução da atividade].[GUID se FlattenHierarchy].[formato se configurado].[compactação se configurada]*", por exemplo: "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". Se você copiar da fonte de dados de tabela usando o nome da tabela em vez da consulta, o nome padrão será "*[nome da tabela].[formato].[compactação se configurada]*", por exemplo: "MinhaTabela.csv". |Não  |
| modifiedDatetimeStart | Filtro de arquivos com base no atributo: Última Modificação. Os arquivos serão selecionados se a hora da última alteração estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário de UTC no formato "2018-12-01T05:00:00Z". <br/><br/> As propriedades podem ser NULL, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o valor de data e hora, mas `modifiedDatetimeEnd` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é maior ou igual ao valor de data e hora.  Quando `modifiedDatetimeEnd` tem o valor de data e hora, mas `modifiedDatetimeStart` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é menor que o valor de data e hora.| Não  |
| modifiedDatetimeEnd | Filtro de arquivos com base no atributo: Última Modificação. Os arquivos serão selecionados se a hora da última alteração estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário de UTC no formato "2018-12-01T05:00:00Z". <br/><br/> As propriedades podem ser NULL, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o valor de data e hora, mas `modifiedDatetimeEnd` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é maior ou igual ao valor de data e hora.  Quando `modifiedDatetimeEnd` tem o valor de data e hora, mas `modifiedDatetimeStart` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é menor que o valor de data e hora.| Não  |
| formato | Se você quiser copiar arquivos no estado em que se encontram entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas duas definições de conjunto de dados de entrada e de saída.<br/><br/>Se você quer analisar ou gerar arquivos com um formato específico, os seguintes tipos de formato de arquivo são compatíveis: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Defina a propriedade **type** sob **format** para um desses valores. Para saber mais, veja as seções [Formato de texto](supported-file-formats-and-compression-codecs.md#text-format), [Formato JSON](supported-file-formats-and-compression-codecs.md#json-format), [Formato Avro](supported-file-formats-and-compression-codecs.md#avro-format), [Formato Orc](supported-file-formats-and-compression-codecs.md#orc-format) e [Formato Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Não (somente para o cenário de cópia binária) |
| compactação | Especifique o tipo e o nível de compactação para os dados. Para obter mais informações, consulte [Formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Os tipos com suporte são: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>Os níveis de suporte são **Ideal** e **Mais rápido**. |Não  |

>[!TIP]
>Para copiar todos os arquivos em uma pasta, especifique **folderPath** somente.<br>Para copiar um único arquivo com um determinado nome, especifique **folderPath** com parte da pasta e **fileName** com nome de arquivo.<br>Para copiar um subconjunto de arquivos em uma pasta, especifique **folderPath** com parte da pasta e **fileName** com filtro curinga. 

**Exemplo:**

```json
{
    "name": "AzureDataLakeStorageDataset",
    "properties": {
        "type": "AzureBlobFSFile",
        "linkedServiceName": {
            "referenceName": "<Azure Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
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

Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, consulte o artigo [Copiar configurações de atividade](copy-activity-overview.md#configuration) e [Pipelines e atividades](concepts-pipelines-activities.md). Esta seção fornece uma lista de propriedades com suporte pelo coletor e origem do Data Lake Store Gen2.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure Data Lake Store Gen2 como um tipo de origem

As propriedades a seguir têm suporte na seção **source** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type da origem da atividade de cópia deve ser definida como **AzureBlobFSSource**. |Sim |
| recursiva | Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. Observe que quando recursiva é definida como true e o coletor é um armazenamento baseado em arquivo, uma pasta vazia ou subpasta não é copiada ou criada no coletor.<br/>Os valores permitidos são **true** (padrão) e **false**. | Não  |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromAzureDataLakeStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Data Lake Storage input dataset name>",
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
                "type": "AzureBlobFSSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-data-lake-storage-gen2-as-a-sink-type"></a>Azure Data Lake Store Gen2 como um tipo de coletor

As propriedades a seguir têm suporte na seção **sink** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade do tipo do coletor de atividade de cópia deve ser definida como **AzureBlobFSSink**. |Sim |
| copyBehavior | Define o comportamento de cópia quando a fonte for de arquivos de um armazenamento de dados baseado em arquivo.<br/><br/>Valores permitidos são:<br/><b>– PreserveHierarchy (padrão)</b>: Preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino.<br/><b>– FlattenHierarchy</b>: Todos os arquivos da pasta de origem estão no primeiro nível da pasta de destino. Os arquivos de destino têm os nomes gerados automaticamente. <br/><b>– MergeFiles</b>: Mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo for especificado, o nome do arquivo mesclado será o nome especificado. Caso contrário, ele será um nome de arquivo gerado automaticamente. | Não  |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToAzureDataLakeStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Lake Storage Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
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

### <a name="some-recursive-and-copybehavior-examples"></a>Alguns exemplos de recursive e copyBehavior

Esta seção descreve o comportamento resultante da operação de cópia para diferentes combinações de valores recursive e copyBehavior.

| recursiva | copyBehavior | Estrutura de pasta de origem | Destino resultante |
|:--- |:--- |:--- |:--- |
| verdadeiro |preserveHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A pasta de destino Pasta1 é criada com a mesma estrutura da origem:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 |
| verdadeiro |flattenHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo5 |
| verdadeiro |mergeFiles | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos de Arquivo1 + Arquivo2 + Arquivo3 + Arquivo4 + Arquivo5 são mesclados em um arquivo com um nome de arquivo gerado automaticamente. |
| falso |preserveHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A pasta de destino Pasta1 é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/><br/>A Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não é selecionada. |
| falso |flattenHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A pasta de destino Pasta1 é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo2<br/><br/>A Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não é selecionada. |
| falso |mergeFiles | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos de Arquivo1 + Arquivo2 são mesclados em um arquivo com um nome de arquivo gerado automaticamente. nome gerado automaticamente para o Arquivo1<br/><br/>A Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não é selecionada. |

## <a name="next-steps"></a>Próximas etapas

Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).
