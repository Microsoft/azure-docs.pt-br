---
title: Copiar dados para ou do armazenamento do Azure Data Lake Gen2 usando o Data Factory | Microsoft Docs
description: Saiba como copiar dados para e do armazenamento do Azure Data Lake Gen2 usando o Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: 9f60c6258da77c0aaa99d16e178f4b3531ce90d9
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509243"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Copiar dados de/para o Azure Data Lake Storage Gen2 usando o Azure Data Factory

Gen2 de armazenamento do Azure Data Lake é um conjunto de recursos dedicado para análise de big data incorporado [armazenamento de BLOBs do Azure](../storage/blobs/storage-blobs-introduction.md). Você pode usá-lo para fazer interface com seus dados usando ambos os paradigmas de armazenamento de sistema e o objeto de arquivo.

Este artigo descreve como copiar dados para e do armazenamento do Azure Data Lake Gen2. Para saber mais sobre o Azure Data Factory, leia as [artigo introdutório](introduction.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector do armazenamento do Azure Data Lake Gen2 tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [suporte para a matriz de origem ou coletor](copy-activity-overview.md)
- [Mapeamento de fluxo de dados](concepts-data-flow-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)

Especificamente, com esse conector, que você pode:

- Copie dados usando a chave de conta, entidade de serviço ou identidades gerenciadas para as autenticações de recursos do Azure.
- Copiar arquivos como estão ou analisar ou gerar arquivos com [suporte para formatos de arquivo e codecs de compactação](supported-file-formats-and-compression-codecs.md).

>[!TIP]
>Se você habilitar o namespace hierárquico, atualmente não há nenhuma interoperabilidade das operações entre o Blob e APIs de Gen2 de armazenamento do Data Lake. Se você atingir o erro "ErrorCode = FilesystemNotFound" com a mensagem "o sistema de arquivos especificado não existe", ele é causado pelo sistema de arquivos do coletor especificado que foi criado por meio da API do Blob em vez da API do Data Lake armazenamento Gen2 em outro lugar. Para corrigir o problema, especifique um novo sistema de arquivos com um nome que não existe como o nome de um contêiner de Blob. Em seguida, o Data Factory cria automaticamente esse sistema de arquivos durante a cópia de dados.

>[!NOTE]
>Se você habilitar a **permitir que os serviços da Microsoft para acessar essa conta de armazenamento confiáveis** opção nas configurações de firewall de armazenamento do Azure, o tempo de execução de integração do Azure não se conectar ao Data Lake armazenamento Gen2 e mostra um erro "proibido". A mensagem de erro é exibida porque a fábrica de dados não é tratada como um serviço confiável da Microsoft. Use o tempo de execução de integração auto-hospedado para conectar-se em vez disso.

## <a name="get-started"></a>Introdução

>[!TIP]
>Para obter uma explicação de como usar o conector do Data Lake armazenamento Gen2, consulte [carregar dados no armazenamento do Azure Data Lake Gen2](load-azure-data-lake-storage-gen2.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem informações sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao Data Lake armazenamento Gen2.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

O conector do armazenamento do Azure Data Lake Gen2 suporta os seguintes tipos de autenticação. Consulte as seções correspondentes para obter detalhes:

- [Autenticação de chave de conta](#account-key-authentication)
- [Autenticação de entidade de serviço](#service-principal-authentication)
- [Identidades gerenciadas para autenticação de recursos do Azure](#managed-identity)

>[!NOTE]
>Ao usar o PolyBase para carregar dados no SQL Data Warehouse, se sua fonte de dados Lake armazenamento Gen2 estiver configurado com o ponto de extremidade de rede Virtual, você deve usar a autenticação de identidade gerenciada conforme exigido pelo PolyBase. Consulte a [autenticação de identidade gerenciada](#managed-identity) seção com mais pré-requisitos de configuração.

### <a name="account-key-authentication"></a>Autenticação de chave de conta

Para usar a autenticação de chave de conta de armazenamento, há suporte para as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como **AzureBlobFS**. |Sim |
| url | Ponto de extremidade para o Data Lake armazenamento Gen2 com o padrão de `https://<accountname>.dfs.core.windows.net`. | Sim |
| accountKey | Chave de conta do Data Lake armazenamento Gen2. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o tempo de execução de integração do Azure ou um tempo de execução de integração auto-hospedado se o armazenamento de dados estiver em uma rede privada. Se essa propriedade não for especificada, o tempo de execução de integração do Azure padrão será usado. |Não |

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

Para usar a autenticação de entidade de serviço, siga estas etapas.

1. Registre uma entidade de aplicativo no Azure Active Directory (Azure AD), seguindo as etapas em [registrar seu aplicativo com um locatário Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Anote os seguintes valores, que são usados para definir o serviço vinculado:

    - ID do aplicativo
    - Chave do aplicativo
    - ID do locatário

2. Conceda a permissão apropriada à entidade serviço. Saiba mais sobre como funciona a permissão no Data Lake armazenamento Gen2 de [listas de controle de acesso nos arquivos e diretórios](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)

    - **Como origem**: No Gerenciador de armazenamento, conceda pelo menos **Execute** a partir do sistema de arquivos de origem, ao longo de permissão **leitura** permissão para os arquivos a serem copiados. Como alternativa, no controle de acesso (IAM), conceda pelo menos o **leitor de dados de Blob de armazenamento** função.
    - **Como coletor**: No Gerenciador de armazenamento, conceda pelo menos **Execute** a partir do sistema de arquivos do coletor, ao longo de permissão **gravar** permissão para a pasta de coletor. Como alternativa, no controle de acesso (IAM), conceda pelo menos o **Colaborador de dados de Blob de armazenamento** função.

>[!NOTE]
>A lista de pastas a partir do nível da conta ou para testar a conexão, você precisa definir a permissão da entidade de serviço que está sendo concedida a **conta de armazenamento com permissão de "Leitor de dados de Blob de armazenamento" no IAM**. Isso é verdadeiro quando você usa o:
>- **A ferramenta Copiar dados** para pipeline de cópia do autor.
>- **Interface do Usuário do Data Factory** para testar a conexão e navegar por pastas durante a criação. 
>Se você tiver dúvidas sobre como conceder permissão no nível da conta, durante a criação, ignore a conexão e teste um caminho pai com permissão concedida, em seguida, optar por navegar a partir de entrada que especificou o caminho. Copie atividade funciona como a entidade de serviço é concedida com a permissão adequada os arquivos a serem copiados.

Essas propriedades têm suporte para o serviço vinculado:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como **AzureBlobFS**. |Sim |
| url | Ponto de extremidade para o Data Lake armazenamento Gen2 com o padrão de `https://<accountname>.dfs.core.windows.net`. | Sim |
| servicePrincipalId | Especifique a ID do cliente do aplicativo. | Sim |
| servicePrincipalKey | Especifique a chave do aplicativo. Marcar este campo como um `SecureString` para armazená-la com segurança no Data Factory. Ou, você pode [fazer referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| tenant | Especifique as informações de locatário (domínio nome ou ID do Locatário) em que o aplicativo reside. Recuperá-lo passando o mouse no canto superior direito do portal do Azure. | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o tempo de execução de integração do Azure ou um tempo de execução de integração auto-hospedado se o armazenamento de dados estiver em uma rede privada. Se não especificado, o tempo de execução de integração do Azure padrão será usado. |Não |

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

Um data factory pode ser associado a uma [identidade gerenciada para recursos do Azure](data-factory-service-identity.md), que representa esse data factory específico. Você pode usar essa identidade de gerenciado diretamente para a autenticação do Data Lake armazenamento Gen2, semelhante a usar sua própria entidade de serviço. Ele permite que esta factory designada acesse e copie dados para ou de seu Data Lake armazenamento Gen2.

Para usar identidades gerenciadas para autenticação de recursos do Azure, siga estas etapas.

1. [Recuperar as informações de identidade gerenciada do Data Factory](data-factory-service-identity.md#retrieve-managed-identity) copiando o valor da **ID do aplicativo de identidade de serviço** gerado junto com seu alocador.

2. Conceda a permissão apropriada à identidade gerenciada. Saiba mais sobre como funciona a permissão no Data Lake armazenamento Gen2 da [listas de controle de acesso nos arquivos e diretórios](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

    - **Como origem**: No Gerenciador de armazenamento, conceda pelo menos **Execute** a partir do sistema de arquivos de origem, ao longo de permissão **leitura** permissão para os arquivos a serem copiados. Como alternativa, no controle de acesso (IAM), conceda pelo menos o **leitor de dados de Blob de armazenamento** função.
    - **Como coletor**: No Gerenciador de armazenamento, conceda pelo menos **Execute** a partir do sistema de arquivos do coletor, ao longo de permissão **gravar** permissão para a pasta de coletor. Como alternativa, no controle de acesso (IAM), conceda pelo menos o **Colaborador de dados de Blob de armazenamento** função.

>[!NOTE]
>A lista de pastas a partir do nível da conta ou para testar a conexão, você precisa definir a permissão de identidade gerenciada que está sendo concedida a **conta de armazenamento com permissão de "Leitor de dados de Blob de armazenamento" no IAM**. Isso é verdadeiro quando você usa o:
>- **A ferramenta Copiar dados** para pipeline de cópia do autor.
>- **Interface do Usuário do Data Factory** para testar a conexão e navegar por pastas durante a criação. 
>Se você tiver dúvidas sobre como conceder permissão no nível da conta, durante a criação, ignore a conexão e teste um caminho pai com permissão concedida, em seguida, optar por navegar a partir de entrada que especificou o caminho. Copie atividade funciona como a entidade de serviço é concedida com a permissão adequada os arquivos a serem copiados.

>[!IMPORTANT]
>Se você usar o PolyBase para carregar dados do Data Lake armazenamento Gen2 no SQL Data Warehouse, ao usar a autenticação de identidade gerenciada para o Data Lake armazenamento Gen2, certifique-se de também você seguir as etapas 1 e 2 na [neste guia](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) como 1) registre seu SQL Servidor de banco de dados com o Azure Active Directory (Azure AD) e 2) atribuir a função de Colaborador de dados de Blob de armazenamento ao seu servidor de banco de dados SQL; o restante são manipuladas pelo Data Factory. Se seu Data Lake armazenamento Gen2 é configurado com um ponto de extremidade de rede Virtual do Azure, para usar o PolyBase para carregar dados do, você deve usar a autenticação de identidade gerenciada conforme exigido pelo PolyBase.

Essas propriedades têm suporte para o serviço vinculado:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como **AzureBlobFS**. |Sim |
| url | Ponto de extremidade para o Data Lake armazenamento Gen2 com o padrão de `https://<accountname>.dfs.core.windows.net`. | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o tempo de execução de integração do Azure ou um tempo de execução de integração auto-hospedado se o armazenamento de dados estiver em uma rede privada. Se não especificado, o tempo de execução de integração do Azure padrão será usado. |Não |

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

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, consulte [conjuntos de dados](concepts-datasets-linked-services.md).

- Para o parquet e o formato de texto delimitado, consulte o [conjunto de dados de formato de Parquet e texto delimitado](#parquet-and-delimited-text-format-dataset) seção.
- Para outros formatos, como ORC, Avro, JSON ou formato binário, consulte o [outro conjunto de dados do formato](#other-format-dataset) seção.

### <a name="parquet-and-delimited-text-format-dataset"></a>Conjunto de dados de formato de parquet e texto delimitado

Para copiar dados para e do Data Lake armazenamento Gen2 parquet ou o formato de texto delimitado, consulte o [formato Parquet](format-parquet.md) e [formato de texto delimitado](format-delimited-text.md) artigos sobre o conjunto de dados com base no formato e configurações com suporte. As propriedades a seguir têm suporte para o Data Lake armazenamento Gen2 sob `location` configurações no conjunto de dados com base no formato:

| Propriedade   | DESCRIÇÃO                                                  | Obrigatório |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | A propriedade type sob `location` no conjunto de dados deve ser definida como **AzureBlobFSLocation**. | Sim      |
| fileSystem | O nome de sistema de arquivos do Data Lake armazenamento Gen2.                              | Não       |
| folderPath | O caminho para uma pasta no sistema de arquivos especificado. Se você quiser usar um caractere curinga para pastas de filtro, ignore essa configuração e especificá-lo nas configurações de fonte da atividade. | Não       |
| fileName   | O nome do arquivo sob o sistema de arquivos de determinado + folderPath. Se você quiser usar um caractere curinga para filtrar arquivos, ignore essa configuração e especificá-lo nas configurações de fonte da atividade. | Não       |

> [!NOTE]
> O **AzureBlobFSFile** tipo de conjunto de dados com formato parquet ou texto mencionado na seção a seguir ainda tem suporte como está para cópia, pesquisa ou atividade GetMetadata para compatibilidade com versões anteriores. Mas ele não funciona com o recurso de fluxo de dados de mapeamento. É recomendável que você use esse novo modelo no futuro. A criação da interface do usuário do Data Factory gera esses novos tipos.

**Exemplo:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobFSLocation",
                "fileSystem": "filesystemname",
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

### <a name="other-format-dataset"></a>Outro conjunto de dados de formato

Para copiar dados para e do Data Lake armazenamento Gen2 em ORC, Avro, JSON ou formato binário, há suporte para as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como: **AzureBlobFSFile**. |Sim |
| folderPath | Caminho para a pasta no Data Lake armazenamento Gen2. Se não especificado, apontará para a raiz. <br/><br/>Há suporte para o filtro curinga. Curingas permitidos são `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou um único caractere). Use `^` para escapar se seu nome real da pasta tem um caractere curinga ou esse caractere de escape é dentro. <br/><br/>Exemplos: sistema de arquivos/pasta /. Veja mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). |Não |
| fileName | Filtro de nome ou curinga para os arquivos sob o "folderPath" especificado. Se você não especificar um valor para essa propriedade, o conjunto de dados apontará para todos os arquivos na pasta. <br/><br/>Para filtro, os curingas permitidos são `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou um único caractere).<br/>– Exemplo 1: `"fileName": "*.csv"`<br/>– Exemplo 2: `"fileName": "???20180427.txt"`<br/>Use `^` para escapar se seu nome de arquivo real tem um caractere curinga ou esse caractere de escape é dentro.<br/><br/>Quando fileName não for especificado para um conjunto de dados de saída e **preserveHierarchy** não for especificada no coletor de atividade, a atividade de cópia gerará automaticamente o nome do arquivo com o seguinte padrão: "*Dados. [GUID da ID de execução de atividade]. [GUID se FlattenHierarchy]. [formato se configurado]. [compactação se configurado]* ", por exemplo,"Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". Se você copiar de uma fonte de tabela usando um nome de tabela em vez de uma consulta, o nome padrão é " *[nome da tabela]. [ Format]. [compactação se configurado]* ", por exemplo,"MyTable.csv". |Não |
| modifiedDatetimeStart | Filtro de arquivos com base no atributo da última modificação. Os arquivos estão selecionados se sua hora da última modificação está dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário UTC no formato de "2018-12-01T05:00:00Z". <br/><br/> O desempenho geral da movimentação de dados é afetado ao habilitar essa configuração quando você deseja que o filtro de arquivo com grandes quantidades de arquivos. <br/><br/> As propriedades podem ser NULL, o que significa que nenhum filtro de atributo de arquivo é aplicado ao conjunto de dados. Quando `modifiedDatetimeStart` tem um valor de data e hora, mas `modifiedDatetimeEnd` for NULL, isso significa que os arquivos cujo último atributo modificado é maior que ou igual ao valor de data e hora são selecionados. Quando `modifiedDatetimeEnd` tem um valor de data e hora, mas `modifiedDatetimeStart` for NULL, isso significa que os arquivos cujo último atributo modificado é menor que o valor de data e hora estão selecionados.| Não |
| modifiedDatetimeEnd | Filtro de arquivos com base no atributo da última modificação. Os arquivos estão selecionados se sua hora da última modificação está dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário UTC no formato de "2018-12-01T05:00:00Z". <br/><br/> O desempenho geral da movimentação de dados é afetado ao habilitar essa configuração quando você deseja que o filtro de arquivo com grandes quantidades de arquivos. <br/><br/> As propriedades podem ser NULL, o que significa que nenhum filtro de atributo de arquivo é aplicado ao conjunto de dados. Quando `modifiedDatetimeStart` tem um valor de data e hora, mas `modifiedDatetimeEnd` for NULL, isso significa que os arquivos cujo último atributo modificado é maior que ou igual ao valor de data e hora são selecionados. Quando `modifiedDatetimeEnd` tem um valor de data e hora, mas `modifiedDatetimeStart` for NULL, isso significa que os arquivos cujo último atributo modificado é menor que o valor de data e hora estão selecionados.| Não |
| format | Se você quiser copiar arquivos no estado em que se encontram entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas duas definições de conjunto de dados de entrada e de saída.<br/><br/>Se você quer analisar ou gerar arquivos com um formato específico, os seguintes tipos de formato de arquivo são compatíveis: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Defina a propriedade **type** sob **format** para um desses valores. Para obter mais informações, consulte o [formato de texto](supported-file-formats-and-compression-codecs.md#text-format), [formato JSON](supported-file-formats-and-compression-codecs.md#json-format), [formato Avro](supported-file-formats-and-compression-codecs.md#avro-format), [formato ORC](supported-file-formats-and-compression-codecs.md#orc-format), e [formato Parquet ](supported-file-formats-and-compression-codecs.md#parquet-format) seções. |Não (somente para o cenário de cópia binária) |
| compression | Especifique o tipo e o nível de compactação para os dados. Para obter mais informações, consulte [Formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Os tipos com suporte são: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>Os níveis de suporte são **Ideal** e **Mais rápido**. |Não |

>[!TIP]
>Para copiar todos os arquivos em uma pasta, especifique **folderPath** somente.<br>Para copiar um único arquivo com um determinado nome, especifique **folderPath** com uma parte da pasta e **fileName** com um nome de arquivo.<br>Para copiar um subconjunto de arquivos em uma pasta, especifique **folderPath** com uma parte da pasta e **fileName** com um filtro curinga. 

**Exemplo:**

```json
{
    "name": "ADLSGen2Dataset",
    "properties": {
        "type": "AzureBlobFSFile",
        "linkedServiceName": {
            "referenceName": "<Azure Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "myfilesystem/myfolder",
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

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, consulte [copiar configurações de atividade](copy-activity-overview.md#configuration) e [Pipelines e atividades](concepts-pipelines-activities.md). Esta seção fornece uma lista de propriedades com suporte pelo coletor e origem do Data Lake Store Gen2.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure Data Lake armazenamento Gen2 como um tipo de fonte

- Para copiar do parquet ou formato de texto delimitado, consulte o [Parquet e a origem do formato de texto delimitado](#parquet-and-delimited-text-format-source) seção.
- Para copiar dos outros formatos como ORC, Avro, JSON ou formato binário, consulte o [outra fonte de formato](#other-format-source) seção.

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet e a origem do formato de texto delimitado

Para copiar dados do Data Lake armazenamento Gen2 parquet ou o formato de texto delimitado, consulte o [formato Parquet](format-parquet.md) e [formato de texto delimitado](format-delimited-text.md) artigo sobre a fonte da atividade de cópia baseada em formato e configurações com suporte. As propriedades a seguir têm suporte para o Data Lake armazenamento Gen2 sob `storeSettings` as configurações na fonte de cópia com base no formato:

| Propriedade                 | DESCRIÇÃO                                                  | Obrigatório                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | A propriedade type sob `storeSettings` deve ser definida como **AzureBlobFSReadSetting**. | Sim                                           |
| recursive                | Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. Quando o recursivo estiver definido como true e o coletor é um armazenamento baseado em arquivo, uma pasta vazia ou subpasta não é copiada ou criada no coletor. Os valores permitidos são **true** (padrão) e **false**. | Não                                            |
| wildcardFolderPath       | O caminho da pasta com caracteres curinga em determinado sistema de arquivos configurado no conjunto de dados para pastas de código-fonte do filtro. <br>Curingas permitidos são `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou um único caractere). Use `^` para escapar se seu nome real da pasta tem um curinga ou esse caractere de escape dentro. <br>Veja mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). | Não                                            |
| wildcardFileName         | O nome de arquivo com caracteres curinga no sistema de arquivos especificado + folderPath/wildcardFolderPath para filtrar arquivos de origem. <br>Curingas permitidos são `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou um único caractere). Use `^` para escapar se seu nome real da pasta tem um curinga ou esse caractere de escape dentro. Veja mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). | Sim se `fileName` não for especificado no conjunto de dados |
| modifiedDatetimeStart    | Filtro de arquivos com base no atributo da última modificação. Os arquivos estão selecionados se sua hora da última modificação está dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário UTC no formato de "2018-12-01T05:00:00Z". <br> As propriedades podem ser NULL, o que significa que nenhum filtro de atributo de arquivo é aplicado ao conjunto de dados. Quando `modifiedDatetimeStart` tem um valor de data e hora, mas `modifiedDatetimeEnd` for NULL, isso significa que os arquivos cuja última modificação atributo é maior que ou igual ao valor de data e hora são selecionados. Quando `modifiedDatetimeEnd` tem um valor de data e hora, mas `modifiedDatetimeStart` for NULL, isso significa que os arquivos cujo último atributo modificado é menor que o valor de data e hora estão selecionados. | Não                                            |
| modifiedDatetimeEnd      | Mesmo que acima.                                               | Não                                            |
| maxConcurrentConnections | O número de conexões para se conectar ao repositório de armazenamento simultaneamente. Especifique somente quando você quiser limitar a conexão simultâneo ao armazenamento de dados. | Não                                            |

> [!NOTE]
> Para o parquet ou o formato de texto delimitado, o **AzureBlobFSSource** origem de atividade de cópia de tipo mencionada na seção a seguir ainda tem suporte, conforme é para compatibilidade com versões anteriores. É recomendável que você use esse novo modelo no futuro. A criação da interface do usuário do Data Factory gera esses novos tipos.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
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
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureBlobFSReadSetting",
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

#### <a name="other-format-source"></a>Outra fonte de formato

Para copiar dados do Data Lake armazenamento Gen2 em ORC, Avro, JSON ou formato binário, as propriedades a seguir têm suporte na atividade de cópia **origem** seção:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type da origem da atividade de cópia deve ser definida como **AzureBlobFSSource**. |Sim |
| recursive | Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. Quando o recursivo estiver definido como true e o coletor é um armazenamento baseado em arquivo, uma pasta vazia ou subpasta não é copiada ou criada no coletor.<br/>Os valores permitidos são **true** (padrão) e **false**. | Não |
| maxConcurrentConnections | O número de conexões para conectar-se ao armazenamento de dados simultaneamente. Especifique somente quando você quiser limitar a conexão simultâneo ao armazenamento de dados. | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen2 input dataset name>",
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

- Para copiar para o parquet ou o formato de texto delimitado, consulte o [Parquet e o coletor de formato de texto delimitado](#parquet-and-delimited-text-format-sink) seção.
- Para copiar para outros formatos, como ORC, Avro, JSON ou formato binário, consulte o [outro coletor do formato](#other-format-sink) seção.

#### <a name="parquet-and-delimited-text-format-sink"></a>Parquet e o coletor de formato de texto delimitado

Para copiar dados para o Data Lake armazenamento Gen2 parquet ou o formato de texto delimitado, consulte o [formato Parquet](format-parquet.md) e [formato de texto delimitado](format-delimited-text.md) artigos sobre o coletor de atividade de cópia baseada em formato e configurações com suporte. As propriedades a seguir têm suporte para o Data Lake armazenamento Gen2 sob `storeSettings` configurações no coletor de cópia com base no formato:

| Propriedade                 | DESCRIÇÃO                                                  | Obrigatório |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | A propriedade type sob `storeSettings` deve ser definida como **AzureBlobFSWriteSetting**. | Sim      |
| copyBehavior             | Define o comportamento de cópia quando a fonte for de arquivos de um armazenamento de dados baseado em arquivo.<br/><br/>Valores permitidos são:<br/><b>– PreserveHierarchy (padrão)</b>: Preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino.<br/><b>– FlattenHierarchy</b>: Todos os arquivos da pasta de origem estão no primeiro nível da pasta de destino. Os arquivos de destino têm os nomes gerados automaticamente. <br/><b>– MergeFiles</b>: Mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo for especificado, o nome do arquivo mesclado será o nome especificado. Caso contrário, ele será um nome de arquivo gerado automaticamente. | Não       |
| maxConcurrentConnections | O número de conexões para conectar-se ao armazenamento de dados simultaneamente. Especifique somente quando você quiser limitar a conexão simultâneo ao armazenamento de dados. | Não       |

> [!NOTE]
> Para o parquet ou o formato de texto delimitado, o **AzureBlobFSSink** coletor de atividade de cópia do tipo mencionado na seção a seguir ainda tem suporte, conforme é para compatibilidade com versões anteriores. É recomendável que você use esse novo modelo no futuro. A criação da interface do usuário do Data Factory gera esses novos tipos.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "AzureBlobFSWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="other-format-sink"></a>Outro coletor do formato

Para copiar dados para o Data Lake armazenamento Gen2 em ORC, Avro, JSON ou formato binário, as propriedades a seguir têm suporte nas **coletor** seção:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade do tipo do coletor de atividade de cópia deve ser definida como **AzureBlobFSSink**. |Sim |
| copyBehavior | Define o comportamento de cópia quando a fonte for de arquivos de um armazenamento de dados baseado em arquivo.<br/><br/>Valores permitidos são:<br/><b>– PreserveHierarchy (padrão)</b>: Preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino.<br/><b>– FlattenHierarchy</b>: Todos os arquivos da pasta de origem estão no primeiro nível da pasta de destino. Os arquivos de destino têm os nomes gerados automaticamente. <br/><b>– MergeFiles</b>: Mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo for especificado, o nome do arquivo mesclado será o nome especificado. Caso contrário, ele será um nome de arquivo gerado automaticamente. | Não |
| maxConcurrentConnections | O número de conexões para conectar-se ao armazenamento de dados simultaneamente. Especifique somente quando você quiser limitar a conexão simultâneo ao armazenamento de dados. | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen2 output dataset name>",
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

| folderPath | fileName | recursive | Estrutura da pasta de origem e resultado do filtro (os arquivos em **negrito** são recuperados)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Vazio, use o padrão) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5.csv<br/>OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| `Folder*` | (Vazio, use o padrão) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5.csv<br/>OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Alguns exemplos de recursive e copyBehavior

Esta seção descreve o comportamento resultante da operação de cópia para diferentes combinações de valores recursive e copyBehavior.

| recursive | copyBehavior | Estrutura de pasta de origem | Destino resultante |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a mesma estrutura da origem:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 |
| true |flattenHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo5 |
| true |mergeFiles | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos de Arquivo1 + Arquivo2 + Arquivo3 + Arquivo4 + Arquivo5 são mesclados em um arquivo com um nome de arquivo gerado automaticamente. |
| false |preserveHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/><br/>Subpasta1 com arquivo3, arquivo4 e arquivo5 não está selecionada. |
| false |flattenHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo2<br/><br/>Subpasta1 com arquivo3, arquivo4 e arquivo5 não está selecionada. |
| false |mergeFiles | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos de Arquivo1 + Arquivo2 são mesclados em um arquivo com um nome de arquivo gerado automaticamente. nome gerado automaticamente para o Arquivo1<br/><br/>Subpasta1 com arquivo3, arquivo4 e arquivo5 não está selecionada. |

## <a name="preserve-acls-from-data-lake-storage-gen1"></a>Preservar as ACLs do Data Lake Storage Gen1

>[!TIP]
>Para copiar dados do Azure Data Lake armazenamento Gen1 em Gen2 em geral, consulte [copiar dados do Azure Data Lake armazenamento Gen1 para Gen2 com o Azure Data Factory](load-azure-data-lake-storage-gen2-from-gen1.md) para um passo a passo e práticas recomendadas.

Quando você copia arquivos do Azure Data Lake armazenamento Gen1 Gen2, você pode optar por preservar as listas de controle de acesso (ACLs) POSIX junto com dados. Para obter mais informações sobre o controle de acesso, consulte [controle de acesso no Azure Data Lake armazenamento Gen1](../data-lake-store/data-lake-store-access-control.md) e [controle de acesso no armazenamento do Azure Data Lake Gen2](../storage/blobs/data-lake-storage-access-control.md).

Os seguintes tipos de ACLs podem ser preservados ao usar a atividade de cópia do Azure Data Factory. Você pode selecionar um ou mais tipos:

- **ACL**: Copie e preservar as listas de controle de acesso do POSIX em arquivos e diretórios. Ele copia as ACLs existentes completas da origem para o coletor. 
- **Proprietário**: Copie e preservar o usuário proprietário de arquivos e diretórios. É necessário acesso de superusuário para o coletor Data Lake armazenamento Gen2.
- **Grupo**: Copie e preservar o grupo proprietário de arquivos e diretórios. É necessário acesso de superusuário para o coletor Data Lake armazenamento Gen2 ou o usuário proprietário (se o usuário proprietário também for um membro do grupo de destino).

Se você especificar para copiar de uma pasta, o Data Factory replica as ACLs de determinada pasta, arquivos e diretórios sob ele, se `recursive` é definido como true. Se você especificar para copiar de um único arquivo, as ACLs no arquivo serão copiadas.

>[!IMPORTANT]
>Quando você optar por preservar as ACLs, certifique-se de que alto, você concede permissões suficientes para o Data Factory operar em relação a sua conta do Data Lake armazenamento Gen2 do coletor. Por exemplo, usar a autenticação de chave de conta ou atribua a função de proprietário de dados de Blob de armazenamento para a identidade do serviço principal ou gerenciado.

Quando você configura fonte como Gen1 de armazenamento do Data Lake com a opção de cópia binária ou formato binário e o coletor como Data Lake armazenamento Gen2 com a opção de cópia binária ou um formato binário, você pode encontrar o **preservar** opção o **cópia As configurações de ferramentas de dados** página ou na **atividade de cópia** > **configurações** guia para a criação de atividade.

![Data Lake Storage Gen1 como preservar Gen2 ACL](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

Aqui está um exemplo de configuração JSON (consulte `preserve`): 

```json
"activities":[
    {
        "name": "CopyFromGen1ToGen2",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataLakeStoreSource",
                "recursive": true
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
            },
            "preserve": [
                "ACL",
                "Owner",
                "Group"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Lake Storage Gen1 input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Lake Storage Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="mapping-data-flow-properties"></a>Mapeamento de propriedades de fluxo de dados

Saiba mais sobre [transformação de origem](data-flow-source.md) e [transformação do coletor](data-flow-sink.md) no recurso de fluxo de dados de mapeamento.

## <a name="next-steps"></a>Próximas etapas

Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).
