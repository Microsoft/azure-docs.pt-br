---
title: Copiar e transformar dados no Azure Data Lake Storage Gen2
description: Saiba como copiar dados para e do Azure Data Lake Storage Gen2 e transformar dados no Azure Data Lake Storage Gen2 usando o Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: 7a501a86f979bb508052c8957627ebfa7950fd63
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104597568"
---
# <a name="copy-and-transform-data-in-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Copiar e transformar dados no Azure Data Lake Storage Gen2 usando o Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

O Azure Data Lake Storage Gen2 (ADLS Gen2) é um conjunto de recursos dedicados à análise de Big Data, criado no [armazenamento de Blobs do Azure](../storage/blobs/storage-blobs-introduction.md). Você pode usá-lo para fazer interface com seus dados usando os paradigmas de sistema de arquivos e armazenamento de objetos.

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de e para o Azure Data Lake Storage Gen2 e usar o Fluxo de Dados para transformar dados no Azure Data Lake Storage Gen2. Para saber mais sobre o Azure Data Factory, leia as [artigo introdutório](introduction.md).

>[!TIP]
>Para o cenário de migração do data lake ou do data warehouse, saiba mais em [Usar o Azure Data Factory para migrar dados do seu data lake ou data warehouse para o Azure](data-migration-guidance-overview.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector do Azure Data Lake Storage Gen2 oferece suporte para as seguintes atividades:

- [atividade de cópia](copy-activity-overview.md) de com [matriz de fonte/coletor com suporte](copy-activity-overview.md)
- [Fluxo de dados de mapeamento](concepts-data-flow-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)
- [Excluir atividade](delete-activity.md)

Para atividade de cópia, com esse conector, você pode:

- Copiar dados de/para o Azure Data Lake Storage Gen2 usando chave de conta, entidade de serviço ou identidades gerenciadas para autenticações de recursos do Azure.
- Copiar arquivos no estado em que se encontram ou analisar ou gerar arquivos com [formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md).
- [Preserve os metadados do arquivo durante a cópia](#preserve-metadata-during-copy).
- [Preserve ACLs](#preserve-acls) ao copiar de Azure Data Lake Storage Gen1/Gen2.

## <a name="get-started"></a>Introdução

>[!TIP]
>Para obter um passo a passo sobre como usar o conector Data Lake Storage Gen2, confira [Carregar dados no Azure Data Lake Storage Gen2](load-azure-data-lake-storage-gen2.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem informações sobre propriedades que são usadas para definir entidades do Data Factory específicas do Data Lake Storage Gen2.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

O conector do Azure Data Lake Storage Gen2 dá suporte aos seguintes tipos de autenticação. Consulte as seções correspondentes para obter detalhes:

- [Autenticação de chave de conta](#account-key-authentication)
- [Autenticação de entidade de serviço](#service-principal-authentication)
- [Identidades gerenciadas para autenticação de recursos do Azure](#managed-identity)

>[!NOTE]
>- Se quiser usar o tempo de execução de integração do Azure público para se conectar ao Data Lake Storage Gen2 aproveitando a opção **permitir que os serviços confiáveis da Microsoft acessem essa conta de armazenamento** habilitada no firewall do armazenamento do Azure, você deve usar a [autenticação de identidade gerenciada](#managed-identity).
>- Ao usar o polybase ou a instrução de cópia para carregar dados no Azure Synapse Analytics, se o Data Lake Storage Gen2 de origem ou de preparo estiver configurado com um ponto de extremidade de rede virtual do Azure, você deverá usar a autenticação de identidade gerenciada conforme exigido pelo Synapse. Confira a seção [autenticação de identidade gerenciada](#managed-identity) com mais pré-requisitos de configuração.

### <a name="account-key-authentication"></a>Autenticação de chave de conta

Para usar a autenticação de chave de conta de armazenamento, há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como **AzureBlobFS**. |Sim |
| url | Ponto de extremidade para Data Lake Storage Gen2 com o padrão de `https://<accountname>.dfs.core.windows.net`. | Sim |
| accountKey | Chave de conta para o Data Lake Storage Gen2. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |Sim |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Azure Integration Runtime ou um runtime de integração auto-hospedada se o seu armazenamento de dados estiver em uma rede privada. Se essa propriedade não for especificada, o Azure Integration Runtime padrão será usado. |Não |

>[!NOTE]
>Não há suporte para o ponto de extremidade do sistema de arquivos ADLS secundário ao usar a autenticação de chave de conta. Você pode usar outros tipos de autenticação.

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

1. Registre uma entidade de aplicativo no Azure Active Directory (Azure AD) seguindo as etapas em [Registre o aplicativo com um locatário do Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Anote os seguintes valores, que são usados para definir o serviço vinculado:

    - ID do aplicativo
    - Chave do aplicativo
    - ID do locatário

2. Conceda a permissão adequada do principal de serviço. Veja exemplos de como a permissão funciona em Data Lake Storage Gen2 de [Listas de controle de acesso em arquivos e diretórios](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)

    - **Como origem**: No Gerenciador de Armazenamento, conceda pelo menos a permissão de **Executar** para todas as pastas acima e o arquivo de sistema, junto com a permissão **Leitura** para os arquivos a serem copiados. Como alternativa, em Controle de acesso (IAM), conceda, pelo menos, a função de **Leitor de Dados do Storage Blob**.
    - **Como coletor**: no Gerenciador de Armazenamento, conceda pelo menos a permissão de **Executar** para todas as pastas acima e o arquivo de sistema, junto com a permissão **Gravar** para a pasta do coletor. Como alternativa, em Controle de acesso (IAM), conceda, pelo menos, a função de **Colaborador de Dados do Storage Blob**.

>[!NOTE]
>Se você usar a interface do usuário do Data Factory para criar e a entidade de serviço não estiver definida com a função "Leitor de dados de blob de armazenamento/colaborador" no IAM, ao fazer a conexão de teste ou procurar/navegar por pastas, escolha "Testar a conexão com o caminho do arquivo" ou "Procurar do caminho especificado" e especifique um caminho com a permissão **Ler + Executar**.

Estas propriedades têm suporte para o serviço vinculado:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como **AzureBlobFS**. |Sim |
| url | Ponto de extremidade para Data Lake Storage Gen2 com o padrão de `https://<accountname>.dfs.core.windows.net`. | Sim |
| servicePrincipalId | Especifique a ID do cliente do aplicativo. | Sim |
| servicePrincipalCredentialType | O tipo de credencial a ser usado para autenticação de entidade de serviço. Os valores permitidos são **ServicePrincipalKey** e **ServicePrincipalCert**. | Sim |
| servicePrincipalCredential | A credencial da entidade de serviço. <br/> Ao usar **ServicePrincipalKey** como o tipo de credencial, especifique a chave do aplicativo. Marque este campo como **SecureString** para armazená-lo com segurança no data Factory ou [faça referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). <br/> Quando você usa **ServicePrincipalCert** como a credencial, faça referência a um certificado em Azure Key Vault. | Sim |
| servicePrincipalKey | Especifique a chave do aplicativo. Marque este campo como **SecureString** para armazená-lo com segurança no data Factory ou [faça referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). <br/> Essa propriedade ainda tem suporte no estado em que se encontra para o `servicePrincipalId`  +  `servicePrincipalKey` . À medida que o ADF adiciona nova autenticação de certificado de entidade de serviço, o novo modelo para autenticação de entidade de serviço é `servicePrincipalId`  +  `servicePrincipalCredentialType`  +  `servicePrincipalCredential` . | Não |
| locatário | Especifique as informações de locatário (domínio nome ou ID do Locatário) em que o aplicativo reside. Recupere-as passando o mouse no canto superior direito do Portal do Azure. | Sim |
| azureCloudType | Para autenticação de entidade de serviço, especifique o tipo de ambiente de nuvem do Azure no qual seu aplicativo Azure Active Directory está registrado. <br/> Os valores permitidos são **AzurePublic**, **AzureChina**, **AzureUsGovernment** e **AzureGermany**. Por padrão, o ambiente de nuvem do data factory é usado. | Não |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Azure Integration Runtime ou um runtime de integração auto-hospedada se o seu armazenamento de dados estiver em uma rede privada. Se não especificado, o Azure Integration Runtime padrão será usado. |Não |

**Exemplo: usando a autenticação de chave de entidade de serviço**

Você também pode armazenar a chave de entidade de serviço em Azure Key Vault.

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalCredentialType": "ServicePrincipalKey",
            "servicePrincipalCredential": {
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

**Exemplo: usando a autenticação de certificado da entidade de serviço**
```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalCredentialType": "ServicePrincipalCert",
            "servicePrincipalCredential": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<AKV reference>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<certificate name in AKV>" 
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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Identidades gerenciadas para autenticação de recursos do Azure

Um data factory pode ser associado a uma [identidade gerenciada para recursos do Azure](data-factory-service-identity.md), que representa esse data factory específico. Você pode usar essa identidade gerenciada diretamente para a autenticação do Data Lake Storage Gen2 da mesma maneira que no uso de sua própria entidade de serviço. Ele permite que este alocador designado acesse e copie dados de ou para o seu Data Lake Storage Gen2.

Para usar identidades gerenciadas para autenticação de recursos do Azure, siga estas etapas.

1. [Recuperar as informações de identidade gerenciada do Data Factory](data-factory-service-identity.md#retrieve-managed-identity) copiando o valor de **ID do objeto de identidade gerenciada** gerado junto com seu alocador.

2. Conceda a permissão adequada de identidade gerenciada. Veja exemplos de como a permissão funciona em Data Lake Storage Gen2 de [Listas de controle de acesso em arquivos e diretórios](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

    - **Como origem**: no Gerenciador de Armazenamento, conceda pelo menos a permissão de **Executar** para todas as pastas acima e o arquivo de sistema, junto com a permissão **Leitura** para os arquivos a serem copiados. Como alternativa, em Controle de acesso (IAM), conceda, pelo menos, a função de **Leitor de Dados do Storage Blob**.
    - **Como coletor**: no Gerenciador de Armazenamento, conceda pelo menos a permissão de **Executar** para todas as pastas acima e o arquivo de sistema, junto com a permissão **Gravar** para a pasta do coletor. Como alternativa, em Controle de acesso (IAM), conceda, pelo menos, a função de **Colaborador de Dados do Storage Blob**.

>[!NOTE]
>Se você usar a interface do usuário do Data Factory para criar e identidade gerenciada não estiver definida com a função "Leitor de dados de blob de armazenamento/colaborador" no IAM, ao fazer a conexão de teste ou procurar/navegar por pastas, escolha "Testar a conexão com o caminho do arquivo" ou "Procurar do caminho especificado" e especifique um caminho com a permissão **Ler + Executar**.

>[!IMPORTANT]
>Se você usar o polybase ou a instrução de cópia para carregar dados de Data Lake Storage Gen2 no Azure Synapse Analytics, ao usar a autenticação de identidade gerenciada para o Data Lake Storage Gen2, certifique-se de também seguir as etapas 1 a 3 nesta [diretriz](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage). Essas etapas registrarão o servidor no Azure AD e atribuirão a função de colaborador de dados de blob de armazenamento ao servidor. Data Factory lida com o restante. Se você configurar o armazenamento de BLOBs com um ponto de extremidade de rede virtual do Azure, você também precisará **permitir que os serviços confiáveis da Microsoft acessem essa conta de armazenamento** ativada em firewalls da conta de armazenamento do Azure e no menu de configurações de **redes virtuais** , conforme exigido pelo Synapse.

Estas propriedades têm suporte para o serviço vinculado:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como **AzureBlobFS**. |Sim |
| url | Ponto de extremidade para Data Lake Storage Gen2 com o padrão de `https://<accountname>.dfs.core.windows.net`. | Sim |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Azure Integration Runtime ou um runtime de integração auto-hospedada se o seu armazenamento de dados estiver em uma rede privada. Se não especificado, o Azure Integration Runtime padrão será usado. |Não |

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

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira [Conjuntos de dados](concepts-datasets-linked-services.md).

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As propriedades a seguir têm suporte para Data Lake Storage Gen2 em configurações de `location` no conjunto de dados baseado em formato:

| Propriedade   | Descrição                                                  | Obrigatório |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | A propriedade type em `location` no conjunto de dados deve ser definida como **AzureBlobFSLocation**. | Sim      |
| fileSystem | Nome do sistema de arquivos do Data Lake Storage Gen2.                              | Não       |
| folderPath | O caminho para uma pasta sob o sistema de arquivos fornecido. Se você quiser usar um caractere curinga para filtrar a pasta, ignore essa configuração e especifique-o nas configurações de origem da atividade. | Não       |
| fileName   | O nome do arquivo sob o fileSystem + folderPath fornecido. Se você quiser usar um caractere curinga para filtrar os arquivos, ignore essa configuração e especifique-o nas configurações de origem da atividade. | Não       |

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

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, confira [Copiar configurações de atividade](copy-activity-overview.md#configuration) e [Pipelines e atividades](concepts-pipelines-activities.md). Esta seção fornece uma lista de propriedades com suporte pelo coletor e origem do Data Lake Store Gen2.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure Data Lake Storage Gen2 como um tipo de origem

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Você tem várias opções para copiar dados de ADLS Gen2:

- Copiar do caminho especificado no conjunto de dados.
- Para filtrar usando caracteres curinga em relação ao caminho da pasta ou nome do arquivo, confira `wildcardFolderPath` e `wildcardFileName`.
- Copie os arquivos definidos em um determinado arquivo de texto como conjunto de arquivos, confira `fileListPath`.

As propriedades a seguir têm suporte para Data Lake Storage Gen2 em configurações de `storeSettings` no fonte de cópia baseado em formato:

| Propriedade                 | Descrição                                                  | Obrigatório                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | A propriedade de tipo em `storeSettings` deve ser configurada com **AzureBlobFSReadSettings**. | Sim                                           |
| ***Localize os arquivos a serem copiados:*** |  |  |
| OPÇÃO 1: caminho estático<br> | Copiar do sistema de arquivos ou caminho de pasta/arquivo especificado no conjunto de dados. Se você quiser copiar todos os arquivos de um sistema de arquivos/pasta, especifique também `wildcardFileName` como `*`. |  |
| OPÇÃO 2: curinga<br>- wildcardFolderPath | O caminho da pasta com caracteres curinga no sistema de arquivos fornecido configurado no conjunto de dados para filtrar as pastas de origem. <br>Os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único); use `^` para escape se o nome de pasta atual tiver curinga ou esse caractere interno de escape. <br>Veja mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). | Não                                            |
| OPÇÃO 2: curinga<br>- wildcardFileName | O nome do arquivo com caracteres curinga sob sistema de arquivos + folderPath/wildcardFolderPath fornecido para filtrar os arquivos de origem. <br>Os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único); use `^` para escape se o nome de arquivo real tiver curinga ou esse caractere interno de escape.  Veja mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). | Sim |
| OPÇÃO 3: uma lista de arquivos<br>- fileListPath | Indica a cópia de um determinado conjunto de arquivos. Aponte para um arquivo de texto que inclui uma lista de arquivos que você deseja copiar, um arquivo por linha, que é o caminho relativo para o caminho configurado no conjunto de um.<br/>Ao usar essa opção, não especifique o nome do arquivo no conjunto de dados. Veja mais exemplos em [Exemplos de lista de arquivos](#file-list-examples). |Não |
| ***Configurações adicionais:*** |  | |
| recursiva | Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. Observe que quando recursiva é definida como true e o coletor é um armazenamento baseado em arquivo, uma pasta vazia ou subpasta não é copiada ou criada no coletor. <br>Os valores permitidos são **true** (padrão) e **false**.<br>Essa propriedade não se aplica quando você configura `fileListPath`. |Não |
| deleteFilesAfterCompletion | Indica se os arquivos binários serão excluídos do repositório de origem após a movimentação com êxito para o repositório de destino. A exclusão do arquivo é por arquivo, portanto, quando a atividade de cópia falhar, você verá que alguns arquivos já foram copiados para o destino e excluídos da origem, enquanto outros ainda permanecem no repositório de origem. <br/>Esta propriedade só é válida no cenário de cópia de arquivos binários. O valor padrão: false. |Não |
| modifiedDatetimeStart    | Filtro de arquivos com base no atributo: Última Modificação. <br>Os arquivos serão escolhidos se a hora da última alteração estiver dentro do período entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário de UTC no formato "2018-12-01T05:00:00Z". <br> As propriedades podem ser nulas, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de um.  Quando `modifiedDatetimeStart` tem o valor de data e hora, mas `modifiedDatetimeEnd` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é maior ou igual ao valor de data e hora.  Quando `modifiedDatetimeEnd` tem o valor de data e hora, mas `modifiedDatetimeStart` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é menor que o valor de data e hora.<br/>Essa propriedade não se aplica quando você configura `fileListPath`. | Não                                            |
| modifiedDatetimeEnd      | Mesmo que acima.                                               | Não                                            |
| enablePartitionDiscovery | Para arquivos que são particionados, especifique se deseja analisar as partições do caminho do arquivo e adicioná-las como colunas de origem adicionais.<br/>Os valores permitidos são **false** (padrão) e **true**. | Não                                            |
| partitionRootPath | Quando a descoberta de partição estiver habilitada, especifique o caminho raiz absoluto para ler as pastas particionadas como colunas de dados.<br/><br/>Se não for especificado, por padrão,<br/>-Quando você usa o caminho do arquivo no conjunto de programas ou na lista de arquivos na origem, o caminho raiz da partição é o caminho configurado no conjunto de um.<br/>-Quando você usa o filtro de pasta curinga, o caminho raiz da partição é o subcaminho antes do primeiro caractere curinga.<br/><br/>Por exemplo, supondo que você configure o caminho no conjunto de um como "raiz/pasta/ano = 2020/mês = 08/dia = 27":<br/>-Se você especificar o caminho raiz da partição como "raiz/pasta/ano = 2020", a atividade de cópia irá gerar mais duas colunas `month` e `day` com o valor "08" e "27", respectivamente, além das colunas dentro dos arquivos.<br/>-Se o caminho raiz da partição não for especificado, nenhuma coluna extra será gerada. | Não                                            |
| maxConcurrentConnections | O limite superior de conexões simultâneas estabelecidas com o armazenamento de dados durante a execução da atividade. Especifique um valor somente quando desejar limitar as conexões simultâneas.| Não                                            |

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
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureBlobFSReadSettings",
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

### <a name="azure-data-lake-storage-gen2-as-a-sink-type"></a>Azure Data Lake Store Gen2 como um tipo de coletor

[!INCLUDE [data-factory-v2-file-sink-formats](../../includes/data-factory-v2-file-sink-formats.md)]

As propriedades a seguir têm suporte para Data Lake Storage Gen2 em configurações de `storeSettings` no coletor de cópia baseado em formato:

| Propriedade                 | Descrição                                                  | Obrigatório |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | A propriedade de tipo em `storeSettings` deve ser configurada com **AzureBlobFSWriteSettings**. | Sim      |
| copyBehavior             | Define o comportamento de cópia quando a fonte for de arquivos de um armazenamento de dados baseado em arquivo.<br/><br/>Valores permitidos são:<br/><b>– PreserveHierarchy (padrão)</b>: Preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino.<br/><b>– FlattenHierarchy</b>: Todos os arquivos da pasta de origem estão no primeiro nível da pasta de destino. Os arquivos de destino têm os nomes gerados automaticamente. <br/><b>– MergeFiles</b>: Mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo for especificado, o nome do arquivo mesclado será o nome especificado. Caso contrário, ele será um nome de arquivo gerado automaticamente. | Não       |
| blockSizeInMB | Especifique o tamanho do bloco em MB usado para gravar dados no ADLS Gen2. Saiba mais [sobre Blobs de blocos](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs). <br/>O valor permitido é **entre 4 MB e 100 MB**. <br/>Por padrão, o ADF determina automaticamente o tamanho do bloco com base no tipo de armazenamento de origem e nos dados. Para cópia não binária em ADLS Gen2, o tamanho de bloco padrão é 100 MB para que caiba no máximo de 4,95 TB de dados. Talvez não seja ideal quando os dados não forem grandes, especialmente quando você usar o runtime de integração auto-hospedada com rede deficiente, resultando em tempo limite de operação ou problema de desempenho. Você pode especificar explicitamente um tamanho de bloco, mas verifique se blockSizeInMB*50000 é grande o suficiente para armazenar os dados; caso contrário, haverá falha na execução da atividade de cópia. | Não |
| maxConcurrentConnections | O limite superior de conexões simultâneas estabelecidas com o armazenamento de dados durante a execução da atividade. Especifique um valor somente quando desejar limitar as conexões simultâneas.| Não       |

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
                    "type": "AzureBlobFSWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Exemplos de filtro de pasta e arquivo

Esta seção descreve o comportamento resultante do caminho da pasta e do nome de arquivo com filtros curinga.

| folderPath | fileName | recursiva | Estrutura da pasta de origem e resultado do filtro (os arquivos em **negrito** são recuperados)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Vazio, usar padrão) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5.csv<br/>OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| `Folder*` | (Vazio, usar padrão) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5.csv<br/>OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |

### <a name="file-list-examples"></a>Exemplos de lista de arquivos

Esta seção descreve o comportamento resultante do uso do caminho da lista de arquivos na origem da atividade de cópia.

Supondo que você tenha a seguinte estrutura de pasta de origem e queira copiar os arquivos em negrito:

| Exemplo de estrutura de origem                                      | Conteúdo em FileListToCopy.txt                             | Configuração ADF                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| filesystem<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadados<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **No conjunto de dados:**<br>- Sistema de arquivos: `filesystem`<br>- Caminho da pasta: `FolderA`<br><br>**Na origem da atividade de cópia:**<br>- Caminho da lista de arquivos: `filesystem/Metadata/FileListToCopy.txt` <br><br>O caminho da lista de arquivos aponta para um arquivo de texto no mesmo armazenamento de dados que inclui a lista de arquivos que você deseja copiar, um arquivo por linha, com o caminho relativo do caminho configurado no conjunto de dados. |


### <a name="some-recursive-and-copybehavior-examples"></a>Alguns exemplos de recursive e copyBehavior

Esta seção descreve o comportamento resultante da operação de cópia para diferentes combinações de valores recursive e copyBehavior.

| recursiva | copyBehavior | Estrutura de pasta de origem | Destino resultante |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a mesma estrutura da origem:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 |
| true |flattenHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo5 |
| true |mergeFiles | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos de Arquivo1 + Arquivo2 + Arquivo3 + Arquivo4 + Arquivo5 são mesclados em um arquivo com um nome de arquivo gerado automaticamente. |
| false |preserveHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/><br/>A Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não é selecionada. |
| false |flattenHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo2<br/><br/>A Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não é selecionada. |
| false |mergeFiles | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos de Arquivo1 + Arquivo2 são mesclados em um arquivo com um nome de arquivo gerado automaticamente. nome gerado automaticamente para o Arquivo1<br/><br/>A Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não é selecionada. |

## <a name="preserve-metadata-during-copy"></a>Preservar metadados durante a cópia

Ao copiar arquivos do Amazon S3/blob do Azure/Azure Data Lake Storage Gen2 para o Azure Data Lake Storage Gen2/blob do Azure, você poderá optar por preservar os metadados do arquivo junto com os dados. Saiba mais em [Preservar metadados](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="preserve-acls-from-data-lake-storage-gen1gen2"></a><a name="preserve-acls"></a> Preservar ACLs do Data Lake Storage Gen1/Gen2

Ao copiar arquivos do Azure Data Lake Storage Gen1/Gen2 para Gen2, você pode optar por preservar as ACLs (listas de controle de acesso) do POSIX junto com os dados. Saiba mais sobre [Preservar ACLs do Data Lake Storage Gen1/Gen2 para o Gen2](copy-activity-preserve-metadata.md#preserve-acls).

>[!TIP]
>Para copiar dados do Azure Data Lake Storage Gen1 para o Gen2 em geral, confira [Copiar dados de Azure Data Lake Storage Gen1 para o Gen2 com Azure Data Factory](load-azure-data-lake-storage-gen2-from-gen1.md) para obter instruções detalhadas e práticas recomendadas.

## <a name="mapping-data-flow-properties"></a>Mapeamento de propriedades de fluxo de dados

Quando estiver transformando dados no mapeamento de fluxos de dados, você pode ler e gravar arquivos de Azure Data Lake Storage Gen2 nos seguintes formatos:
* [Avro](format-avro.md#mapping-data-flow-properties)
* [Modelo de dados comuns (visualização)](format-common-data-model.md#mapping-data-flow-properties)
* [Texto delimitado](format-delimited-text.md#mapping-data-flow-properties)
* [Delta](format-delta.md#mapping-data-flow-properties)
* [Excel](format-excel.md#mapping-data-flow-properties)
* [JSON](format-json.md#mapping-data-flow-properties)
* [Parquet](format-parquet.md#mapping-data-flow-properties)

As configurações de formato específico estão localizadas na documentação para esse formato. Para obter mais informações, consulte [transformação de origem no mapeamento de fluxo de dados](data-flow-source.md) e [transformação de coletor no fluxo de dados de mapeamento](data-flow-sink.md).

### <a name="source-transformation"></a>Transformação de origem

Na transformação de origem, você pode ler de um contêiner, pasta ou arquivo individual no Azure Data Lake Storage Gen2. A guia **Opções de origem** permite que você gerencie como os arquivos são lidos. 

![Opções de origem](media/data-flow/sourceOptions1.png "Opções de fonte")

**Caminho curinga:** O uso de um padrão curinga instruirá o ADF a executar um loop em cada pasta e arquivo correspondentes em uma única transformação de origem. Esse é um modo eficaz de processar vários arquivos dentro de um único fluxo. Adicione vários padrões de correspondência de curingas com o sinal de + que aparece ao passar o mouse sobre o padrão de curinga existente.

Em seu contêiner de origem, escolha uma série de arquivos que correspondem a um padrão. Somente o contêiner pode ser especificado no conjunto de dados. O caminho curinga, portanto, também precisa incluir o caminho da pasta raiz.

Exemplos de caracteres curinga:

* ```*``` Representa qualquer conjunto de caracteres
* ```**``` Representa o aninhamento de diretório recursivo
* ```?``` Substitui um caractere
* ```[]``` Corresponde a um ou mais caracteres entre colchetes

* ```/data/sales/**/*.csv``` Obtém todos os arquivos CSV em /data/sales
* ```/data/sales/20??/**/``` Obtém todos os arquivos no século 20
* ```/data/sales/*/*/*.csv``` Obtém arquivos CSV dois níveis em /data/sales
* ```/data/sales/2004/*/12/[XY]1?.csv``` Obtém todos os arquivos CSV de dezembro de 2004 que começam com X ou Y prefixado por um número de dois dígitos

**Caminho raiz da partição:** se você tiver pastas particionadas em sua fonte de arquivo com um formato ```key=value``` (por exemplo, year=2019), poderá atribuir o nível superior da árvore de pastas dessa partição a um nome de coluna no fluxo de dados.

Primeiro, defina um curinga para incluir todos os caminhos que são as pastas particionadas mais os arquivos folha que você deseja ler.

![Configurações do arquivo de origem da partição](media/data-flow/partfile2.png "Configuração de arquivos da partição")

Use a configuração do caminho raiz da partição para definir qual é o nível superior da estrutura de pastas. Ao exibir o conteúdo de seus dados por meio de uma visualização de dados, você verá que o ADF adicionará as partições resolvidas encontradas em cada um dos níveis de pasta.

![Caminho raiz da partição](media/data-flow/partfile1.png "Visualização do caminho raiz da partição")

**Lista de arquivos:** é um conjunto de arquivos. Crie um arquivo de texto que inclui uma lista de arquivos do caminho relativo a serem processados. Aponte para este arquivo de texto.

**Coluna para armazenar o nome do arquivo:** armazene o nome do arquivo de origem em uma coluna em seus dados. Insira um novo nome de coluna para armazenar a cadeia de caracteres de nome de arquivo.

**Após a conclusão:** Opte por não fazer nada com o arquivo de origem após a execução de fluxo de dados, excluir o arquivo de origem ou mover o arquivo de origem. Os caminhos para movimentação são relativos.

Para mover os arquivos de origem para outro local após o processamento, primeiro selecione "Mover" para a operação de arquivo. Em seguida, defina o diretório "de". Se você não estiver usando curingas para o caminho, a configuração "de" será a mesma pasta que a de origem.

Se você tiver um caminho de origem com curinga, sua sintaxe terá a seguinte aparência:

```/data/sales/20??/**/*.csv```

Você pode especificar "de" como

```/data/sales```

E "para" como

```/backup/priorSales```

Nesse caso, todos os arquivos que foram originados em /data/sales são movidos para /backup/priorSales.

> [!NOTE]
> As operações do arquivo são executadas somente quando você inicia o fluxo de dados de uma execução de pipeline (depuração de pipeline ou realização da execução) que usa a atividade Executar Fluxo de Dados em um pipeline. As operações de arquivo *não são* executadas no modo de depuração do Fluxo de Dados.

**Filtrar por última modificação:** você pode filtrar quais arquivos são processados especificando um intervalo de datas de quando eles foram modificados pela última vez. Todos os datetimes estão em UTC. 

### <a name="sink-properties"></a>Propriedades do coletor

Na transformação do coletor, você pode gravar em um contêiner ou pasta no Azure Data Lake Storage Gen2. A guia **Configurações** permite que você gerencie como os arquivos são gravados.

![opções de coletor](media/data-flow/file-sink-settings.png "opções de coletor")

**Limpe a pasta:** determina se a pasta de destino é limpa ou não antes de os dados serem gravados.

**Opção do nome do arquivo:** determina como os arquivos de destino são nomeados na pasta de destino. As opções de nome de arquivo são:
   * **Padrão**: permitir que o Spark nomeie arquivos com base nos padrões de PART.
   * **Padrão**: insira um padrão que enumere os arquivos de saída por partição. Por exemplo, **loans[n].csv** criará loans1.csv, loans2.csv e assim por diante.
   * **Por partição**: insira um nome de arquivo por partição.
   * **Como dados na coluna**: defina o arquivo de saída para o valor de uma coluna. O caminho é relativo ao contêiner de conjunto de dados, não à pasta de destino. Se você tiver um caminho de pasta em seu conjunto de dados, ele será substituído.
   * **Saída para um único arquivo**: combine os arquivos de saída particionados em um único arquivo nomeado. O caminho é relativo à pasta do conjunto de dados. Lembre-se de que a operação de mesclagem pode falhar com base no tamanho do nó. Essa opção não é recomendada para conjuntos de dados grandes.

**Citar tudo:** determina se todos os valores devem ser delimitados por aspas

## <a name="lookup-activity-properties"></a>Pesquisar propriedades de atividades

Para saber detalhes sobre as propriedades, verifique [Atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Propriedades de atividade GetMetadata

Para saber detalhes sobre as propriedades, verifique [Atividade GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Excluir propriedades da atividade

Para saber mais detalhes sobre as propriedades, marque [Excluir atividade](delete-activity.md)

## <a name="legacy-models"></a>Modelos herdados

>[!NOTE]
>Os modelos a seguir ainda têm suporte no estado em que se encontram, para compatibilidade com versões anteriores. É recomendável usar o novo modelo mencionado nas seções acima no futuro, e a interface do usuário de criação do ADF mudou para gerar o novo modelo.

### <a name="legacy-dataset-model"></a>Modelo de conjunto de dados herdado

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como: **AzureBlobFSFile**. |Sim |
| folderPath | Caminho para a pasta em Data Lake Storage Gen2. Se não especificado, apontará para a raiz. <br/><br/>O filtro curinga tem suporte. Os curingas permitidos são `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único). Use `^` como escape se o nome real da pasta tiver curingas ou esse caractere de escape. <br/><br/>Exemplos: filesystem/folder/. Veja mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). |Não |
| fileName | Filtro de nome ou curinga para os arquivos em "folderPath" especificado. Se você não especificar um valor para essa propriedade, o conjunto de dados apontará para todos os arquivos na pasta. <br/><br/>Para filtro, os curingas permitidos são `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único).<br/>– Exemplo 1: `"fileName": "*.csv"`<br/>– Exemplo 2: `"fileName": "???20180427.txt"`<br/>Use `^` para se seu nome de arquivo real curinga ou esse caractere de escape dentro de escape.<br/><br/>Quando fileName não for especificado para um conjunto de dados de saída e **preserveHierarchy** não for especificada no coletor de atividade, a atividade de cópia gerará automaticamente o nome do arquivo com o seguinte padrão: "*Data.[GUID da ID de execução da atividade].[GUID se FlattenHierarchy].[formato se configurado].[compactação se configurada]* ", por exemplo: "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". Se você copiar da fonte tabular usando o nome da tabela, em vez da consulta, o nome padrão será " *[nome da tabela].[formato].[compactação se configurada]* ", por exemplo, "MyTable.csv". |Não |
| modifiedDatetimeStart | Filtro de arquivos com base no atributo Última Modificação. Os arquivos são selecionados se a hora da última modificação está dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário de UTC no formato "2018-12-01T05:00:00Z". <br/><br/> O desempenho geral da movimentação de dados será afetado ao habilitar essa configuração quando você desejar filtrar grandes quantidades de arquivos. <br/><br/> As propriedades podem ser NULL, o que significa que nenhum filtro de atributo de arquivo é aplicado ao conjunto de dados. Quando `modifiedDatetimeStart` tiver o valor de datetime, mas `modifiedDatetimeEnd` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é maior ou igual ao valor de datetime. Quando `modifiedDatetimeEnd` tiver o valor de datetime, mas `modifiedDatetimeStart` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é menor que o valor de datetime.| Não |
| modifiedDatetimeEnd | Filtro de arquivos com base no atributo Última Modificação. Os arquivos são selecionados se a hora da última modificação está dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário de UTC no formato "2018-12-01T05:00:00Z". <br/><br/> O desempenho geral da movimentação de dados será afetado ao habilitar essa configuração quando você desejar filtrar grandes quantidades de arquivos. <br/><br/> As propriedades podem ser NULL, o que significa que nenhum filtro de atributo de arquivo é aplicado ao conjunto de dados. Quando `modifiedDatetimeStart` tiver o valor de datetime, mas `modifiedDatetimeEnd` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é maior ou igual ao valor de datetime. Quando `modifiedDatetimeEnd` tiver o valor de datetime, mas `modifiedDatetimeStart` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é menor que o valor de datetime.| Não |
| format | Se você quiser copiar arquivos no estado em que se encontram entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas duas definições de conjunto de dados de entrada e de saída.<br/><br/>Se você quer analisar ou gerar arquivos com um formato específico, os seguintes tipos de formato de arquivo são compatíveis: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Defina a propriedade **type** sob **format** para um desses valores. Para saber mais, veja as seções [Formato de texto](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Formato JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Formato Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Formato ORC](supported-file-formats-and-compression-codecs-legacy.md#orc-format) e [Formato Parquet](supported-file-formats-and-compression-codecs-legacy.md#parquet-format). |Não (somente para o cenário de cópia binária) |
| compactação | Especifique o tipo e o nível de compactação para os dados. Para obter mais informações, consulte [Formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Os tipos com suporte são: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>Os níveis de suporte são **Ideal** e **Mais rápido**. |Não |

>[!TIP]
>Para copiar todos os arquivos em uma pasta, especifique **folderPath** somente.<br>Para copiar um único arquivo com um determinado nome, especifique **folderPath** com parte da pasta e **fileName** com nome de arquivo.<br>Para copiar um subconjunto de arquivos em uma pasta, especifique **folderPath** com parte da pasta e **fileName** com filtro curinga. 

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

### <a name="legacy-copy-activity-source-model"></a>Modelo de origem de atividade de cópia herdado

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type da origem da atividade de cópia deve ser definida como **AzureBlobFSSource**. |Sim |
| recursiva | Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. Quando recursiva é definida como true e o coletor é um armazenamento baseado em arquivo, uma pasta vazia ou subpasta não é copiada ou criada no coletor.<br/>Os valores permitidos são **true** (padrão) e **false**. | Não |
| maxConcurrentConnections | O limite superior de conexões simultâneas estabelecidas com o armazenamento de dados durante a execução da atividade. Especifique um valor somente quando desejar limitar as conexões simultâneas.| Não |

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

### <a name="legacy-copy-activity-sink-model"></a>Modelo do coletor de atividade de cópia herdado

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade do tipo do coletor de atividade de cópia deve ser definida como **AzureBlobFSSink**. |Sim |
| copyBehavior | Define o comportamento de cópia quando a fonte for de arquivos de um armazenamento de dados baseado em arquivo.<br/><br/>Valores permitidos são:<br/><b>– PreserveHierarchy (padrão)</b>: Preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino.<br/><b>– FlattenHierarchy</b>: Todos os arquivos da pasta de origem estão no primeiro nível da pasta de destino. Os arquivos de destino têm os nomes gerados automaticamente. <br/><b>– MergeFiles</b>: Mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo for especificado, o nome do arquivo mesclado será o nome especificado. Caso contrário, ele será um nome de arquivo gerado automaticamente. | Não |
| maxConcurrentConnections | O limite superior de conexões simultâneas estabelecidas com o armazenamento de dados durante a execução da atividade. Especifique um valor somente quando desejar limitar as conexões simultâneas.| Não |

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

## <a name="next-steps"></a>Próximas etapas

Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).