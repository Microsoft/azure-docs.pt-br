---
title: Copiar e transformar dados no Armazenamento de Blobs do Azure
description: Saiba como copiar dados do e para o Armazenamento de Blobs e transformar dados nele usando o Data Factory.
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/08/2020
ms.openlocfilehash: 85600bbee15dadcce7315300ffde481cbfc2e255
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98034706"
---
# <a name="copy-and-transform-data-in-azure-blob-storage-by-using-azure-data-factory"></a>Copiar e transformar dados no Armazenamento de Blobs do Azure usando o Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-azure-blob-connector.md)
> * [Versão atual](connector-azure-blob-storage.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados de e para o armazenamento de BLOBs do Azure. Ele também descreve como usar a atividade de fluxo de dados para transformar dados no armazenamento de BLOBs do Azure. Para saber mais sobre o Azure Data Factory, leia as [artigo introdutório](introduction.md).

>[!TIP]
>Para saber mais sobre um cenário de migração para um data Lake ou uma data warehouse, consulte [usar Azure data Factory para migrar dados do data Lake ou data warehouse para o Azure](data-migration-guidance-overview.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector do armazenamento de BLOBs do Azure tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/coletor compatível](copy-activity-overview.md)
- [Fluxo de dados de mapeamento](concepts-data-flow-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)
- [Excluir atividade](delete-activity.md)

Para a atividade de cópia, esse conector de armazenamento de BLOBs dá suporte a:

- Copiar blobs de e para contas de Armazenamento do Azure de uso geral e para armazenamento de blobs Quente/Frio. 
- Copiar BLOBs usando uma chave de conta, uma assinatura de acesso compartilhado de serviço (SAS), uma entidade de serviço ou identidades gerenciadas para autenticações de recursos do Azure.
- Copiar blobs de blocos, acréscimos ou de páginas e copiar dados somente para blobs de blocos.
- Copiando BLOBs como está ou analisando ou gerando BLOBs com [formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md).
- [Preservando os metadados do arquivo durante a cópia](#preserving-metadata-during-copy).

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas para o Armazenamento de Blobs.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

Este conector de armazenamento de BLOBs dá suporte aos seguintes tipos de autenticação. Consulte as seções correspondentes para obter detalhes.

- [Autenticação de chave de conta](#account-key-authentication)
- [Autenticação de assinatura de acesso compartilhado](#shared-access-signature-authentication)
- [Autenticação de entidade de serviço](#service-principal-authentication)
- [Identidades gerenciadas para a autenticação de recursos do Azure](#managed-identity)

>[!NOTE]
>- Se quiser usar o tempo de execução de integração do Azure público para se conectar ao armazenamento de BLOBs, aproveitando a opção **permitir que os serviços confiáveis da Microsoft acessem essa conta de armazenamento** habilitada no firewall do armazenamento do Azure, você deve usar a [autenticação de identidade gerenciada](#managed-identity).
>- Ao usar o polybase ou a instrução de cópia para carregar dados no Azure Synapse Analytics, se o armazenamento de blob de origem ou de preparo estiver configurado com um ponto de extremidade de rede virtual do Azure, você deverá usar a autenticação de identidade gerenciada conforme exigido pelo Synapse. Consulte a seção [autenticação de identidade gerenciada](#managed-identity) para obter mais pré-requisitos de configuração.

>[!NOTE]
>As atividades do Azure HDInsight e Azure Machine Learning só dão suporte à autenticação que usa chaves de conta de armazenamento de BLOBs do Azure.

### <a name="account-key-authentication"></a>Autenticação de chave de conta

O Data Factory dá suporte às seguintes propriedades para autenticação de chave de conta de armazenamento:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade **Type** deve ser definida como **AzureBlobStorage** (sugerida) ou **AzureStorage** (consulte as observações a seguir). |Sim |
| connectionString | Especifique as informações necessárias para se conectar ao armazenamento para a propriedade **ConnectionString** . <br/> Você também pode colocar a chave de conta em Azure Key Vault e `accountKey` efetuar pull da configuração da cadeia de conexão. Para obter mais informações, consulte os exemplos a seguir e as [credenciais de armazenamento no artigo Azure Key Vault](store-credentials-in-key-vault.md) . |Sim |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o tempo de execução de integração do Azure ou o tempo de execução de integração auto-hospedado (se o armazenamento de dados estiver em uma rede privada). Se essa propriedade não for especificada, o serviço usará o tempo de execução de integração do Azure padrão. |Não |

>[!NOTE]
>Não há suporte para um ponto de extremidade de serviço blob secundário quando você está usando a autenticação de chave de conta. Você pode usar outros tipos de autenticação.

>[!NOTE]
>Se você estiver usando o serviço vinculado de tipo "AzureStorage", ele ainda terá suporte como está. Mas sugerimos que você use o novo tipo de serviço vinculado "AzureBlobStorage" no futuro.

**Exemplo:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: armazenar a chave de conta no Azure Key Vault**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;",
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }            
    }
}
```

### <a name="shared-access-signature-authentication"></a>Autenticação de assinatura de acesso compartilhado

Uma assinatura de acesso compartilhado fornece acesso delegado aos recursos da sua conta de armazenamento. Você pode usar uma assinatura de acesso compartilhado para conceder a um cliente permissões limitadas para objetos em sua conta de armazenamento por determinado tempo. 

Não é preciso compartilhar as chaves de acesso da conta. A assinatura de acesso compartilhado é um URI que engloba em seus parâmetros de consulta todas as informações necessárias para o acesso autenticado a um recurso de armazenamento. Para acessar recursos de armazenamento com a assinatura de acesso compartilhado, o cliente só precisa passar a assinatura de acesso compartilhado ao construtor ou método apropriado. 

Para obter mais informações sobre assinaturas de acesso compartilhado, consulte [Assinaturas de acesso compartilhado: Entender o modelo de assinatura de acesso compartilhado](../storage/common/storage-sas-overview.md).

> [!NOTE]
>- O Data Factory agora dá suporte para *assinaturas de acesso compartilhado de serviço* e *assinaturas de acesso compartilhado de conta*. Para obter mais informações sobre assinaturas de acesso compartilhado, consulte [conceder acesso limitado aos recursos de armazenamento do Azure usando assinaturas de acesso compartilhado](../storage/common/storage-sas-overview.md).
>- Nas configurações mais recentes do conjunto de controles, o caminho da pasta é o caminho absoluto a partir do nível de contêiner. Você precisa configurar um alinhado com o caminho em seu URI SAS.

O Data Factory dá suporte às seguintes propriedades para usar a autenticação de assinatura de acesso compartilhado:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade **Type** deve ser definida como **AzureBlobStorage** (sugerida) ou **AzureStorage** (consulte a observação a seguir). |Sim |
| sasUri | Especifique o URI da assinatura de acesso compartilhado para os recursos de armazenamento, como BLOB ou contêiner. <br/>Marque este campo como **SecureString** para armazená-lo com segurança em data Factory. Você também pode colocar o token SAS em Azure Key Vault para usar a rotação automática e remover a parte do token. Para obter mais informações, consulte os exemplos a seguir e [armazenar credenciais em Azure Key Vault](store-credentials-in-key-vault.md). |Sim |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o tempo de execução de integração do Azure ou o tempo de execução de integração auto-hospedado (se o armazenamento de dados estiver em uma rede privada). Se essa propriedade não for especificada, o serviço usará o tempo de execução de integração do Azure padrão. |Não |

>[!NOTE]
>Se você estiver usando o serviço vinculado de tipo "AzureStorage", ele ainda terá suporte como está. Mas sugerimos que você use o novo tipo de serviço vinculado "AzureBlobStorage" no futuro.

**Exemplo:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<accountname>.blob.core.windows.net/?sv=<storage version>&st=<start time>&se=<expire time>&sr=<resource>&sp=<permissions>&sip=<ip range>&spr=<protocol>&sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: armazenar a chave de conta no Azure Key Vault**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<accountname>.blob.core.windows.net/>"
            },
            "sasToken": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName with value of SAS token e.g. ?sv=<storage version>&st=<start time>&se=<expire time>&sr=<resource>&sp=<permissions>&sip=<ip range>&spr=<protocol>&sig=<signature>>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Ao criar um URI de assinatura de acesso compartilhado, considere os seguintes pontos:

- Defina as permissões apropriadas de leitura/gravação em objetos com base em como o serviço vinculado (leitura, gravação, leitura/gravação) será usado no data factory.
- Defina o **Tempo de expiração** adequadamente. Certifique-se de que o acesso aos objetos de Armazenamento não expira dentro do período ativo do pipeline.
- O URI deve ser criado no contêiner ou BLOB certo com base na necessidade. Um URI de assinatura de acesso compartilhado a um blob permite que o Data Factory acesse o blob específico. Um URI de assinatura de acesso compartilhado para um contêiner de armazenamento de Blob permite que o Data Factory se reitere pelos blobs naquele contêiner. Para fornecer acesso a mais/menos objetos posteriormente, ou para atualizar o URI de assinatura de acesso compartilhado, lembre-se de atualizar o serviço vinculado ao novo URI.

### <a name="service-principal-authentication"></a>Autenticação de entidade de serviço

Para obter informações gerais sobre a autenticação de entidade de serviço do armazenamento do Azure, consulte [autenticar o acesso ao armazenamento do Azure usando Azure Active Directory](../storage/common/storage-auth-aad.md).

Para usar a autenticação de entidade de serviço, siga estas etapas:

1. Registre uma entidade de aplicativo no Azure Active Directory (Azure AD) seguindo [Registre o aplicativo com um locatário do Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Anote esses valores, que você usa para definir o serviço vinculado:

    - ID do aplicativo
    - Chave do aplicativo
    - ID do locatário

2. Conceda a permissão apropriada à entidade de serviço no armazenamento de Blob do Azure. Para obter mais informações sobre as funções, consulte [use the portal do Azure to Assign a Azure Role for Access to blob and Queue data](../storage/common/storage-auth-aad-rbac-portal.md).

    - **Como fonte**, no **controle de acesso (iam)**, conceda pelo menos a função de **leitor de dados de blob de armazenamento** .
    - **Como coletor**, no **controle de acesso (iam)**, conceda pelo menos a função de **colaborador de dados de blob de armazenamento** .

Estas propriedades são suportadas por um serviço vinculado de armazenamento de Blob do Azure:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade **Type** deve ser definida como **AzureBlobStorage**. |Sim |
| serviceEndpoint | Especifique o ponto de extremidade do serviço de armazenamento de Blob do Azure com o padrão de `https://<accountName>.blob.core.windows.net/`. |Sim |
| accountKind | Especifique o tipo de sua conta de armazenamento. Os valores permitidos são: **armazenamento** (uso geral v1), **StorageV2** (uso geral v2), **BlobStorage** ou **BlockBlobStorage**. <br/> Ao usar o serviço vinculado de blob do Azure no fluxo de dados, a identidade gerenciada ou a autenticação da entidade de serviço não tem suporte quando o tipo de conta está vazio ou "armazenamento". Especifique o tipo de conta apropriado, escolha uma autenticação diferente ou atualize sua conta de armazenamento para uso geral v2. |Não |
| servicePrincipalId | Especifique a ID do cliente do aplicativo. | Sim |
| servicePrincipalKey | Especifique a chave do aplicativo. Marque este campo como **SecureString** para armazená-lo com segurança no data Factory ou [faça referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| locatário | Especifique as informações de locatário (domínio nome ou ID do Locatário) em que o aplicativo reside. Recupere-o passando o mouse sobre o canto superior direito do portal do Azure. | Sim |
| azureCloudType | Para autenticação de entidade de serviço, especifique o tipo de ambiente de nuvem do Azure ao qual seu aplicativo Azure Active Directory está registrado. <br/> Os valores permitidos são **AzurePublic**, **AzureChina**, **AzureUsGovernment** e **AzureGermany**. Por padrão, o ambiente de nuvem do data factory é usado. | Não |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o tempo de execução de integração do Azure ou o tempo de execução de integração auto-hospedado (se o armazenamento de dados estiver em uma rede privada). Se essa propriedade não for especificada, o serviço usará o tempo de execução de integração do Azure padrão. |Não |

>[!NOTE]
>Se sua conta de blob habilitar a [exclusão reversível](../storage/blobs/soft-delete-blob-overview.md), a autenticação da entidade de serviço não terá suporte no fluxo de dados.

>[!NOTE]
>A autenticação da entidade de serviço tem suporte apenas pelo serviço vinculado do tipo "AzureBlobStorage", não pelo serviço vinculado do tipo "AzureStorage" anterior.

**Exemplo:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
            "accountKind": "StorageV2",
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

### <a name="managed-identities-for-azure-resource-authentication"></a><a name="managed-identity"></a> Identidades gerenciadas para a autenticação de recursos do Azure

Um data factory pode ser associado a uma [identidade gerenciada para recursos do Azure](data-factory-service-identity.md), que representa esse data factory específico. Você pode usar essa identidade gerenciada diretamente para autenticação de armazenamento de BLOBs, que é semelhante a usar sua própria entidade de serviço. Ele permite que essa fábrica designada acesse e copie dados de ou para o armazenamento de BLOBs.

Para obter informações gerais sobre a autenticação de armazenamento do Azure, consulte [autenticar o acesso ao armazenamento do Azure usando Azure Active Directory](../storage/common/storage-auth-aad.md). Para usar identidades gerenciadas para a autenticação de recursos do Azure, siga estas etapas:

1. [Recupere data Factory informações de identidade gerenciadas](data-factory-service-identity.md#retrieve-managed-identity) copiando o valor da ID de objeto de identidade gerenciada gerada junto com sua fábrica.

2. Conceda a permissão de identidade gerenciada no armazenamento de BLOBs do Azure. Para obter mais informações sobre as funções, consulte [use the portal do Azure to Assign a Azure Role for Access to blob and Queue data](../storage/common/storage-auth-aad-rbac-portal.md).

    - **Como fonte**, no **controle de acesso (iam)**, conceda pelo menos a função de **leitor de dados de blob de armazenamento** .
    - **Como coletor**, no **controle de acesso (iam)**, conceda pelo menos a função de **colaborador de dados de blob de armazenamento** .

>[!IMPORTANT]
>Se você usar o polybase ou a instrução de cópia para carregar dados do armazenamento de BLOBs (como uma origem ou como um preparo) no Azure Synapse Analytics, ao usar a autenticação de identidade gerenciada para o armazenamento de BLOBs, certifique-se de também seguir as etapas 1 a 3 nesta [diretriz](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage). Essas etapas registrarão o servidor no Azure AD e atribuirão a função de colaborador de dados de blob de armazenamento ao servidor. Data Factory lida com o restante. Se você configurar o armazenamento de BLOBs com um ponto de extremidade de rede virtual do Azure, você também precisará **permitir que os serviços confiáveis da Microsoft acessem essa conta de armazenamento** ativada em firewalls da conta de armazenamento do Azure e no menu de configurações de **redes virtuais** , conforme exigido pelo Synapse.

Estas propriedades são suportadas por um serviço vinculado de armazenamento de Blob do Azure:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade **Type** deve ser definida como **AzureBlobStorage**. |Sim |
| serviceEndpoint | Especifique o ponto de extremidade do serviço de armazenamento de Blob do Azure com o padrão de `https://<accountName>.blob.core.windows.net/`. |Sim |
| accountKind | Especifique o tipo de sua conta de armazenamento. Os valores permitidos são: **armazenamento** (uso geral v1), **StorageV2** (uso geral v2), **BlobStorage** ou **BlockBlobStorage**. <br/> Ao usar o serviço vinculado de blob do Azure no fluxo de dados, a identidade gerenciada ou a autenticação da entidade de serviço não tem suporte quando o tipo de conta está vazio ou "armazenamento". Especifique o tipo de conta apropriado, escolha uma autenticação diferente ou atualize sua conta de armazenamento para uso geral v2. |Não |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o tempo de execução de integração do Azure ou o tempo de execução de integração auto-hospedado (se o armazenamento de dados estiver em uma rede privada). Se essa propriedade não for especificada, o serviço usará o tempo de execução de integração do Azure padrão. |Não |

> [!NOTE]
> Se a sua conta de blob habilitar a [exclusão reversível](../storage/blobs/soft-delete-blob-overview.md), não haverá suporte para a autenticação de identidade gerenciada no fluxo de dados.

> [!NOTE]
> As identidades gerenciadas para a autenticação de recursos do Azure têm suporte apenas pelo serviço vinculado do tipo "AzureBlobStorage", não pelo serviço vinculado do tipo "AzureStorage" anterior.

**Exemplo:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
            "accountKind": "StorageV2" 
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

As propriedades a seguir têm suporte para o armazenamento de BLOBs do Azure em `location` configurações em um conjunto de acordo com base em formato:

| Propriedade   | Descrição                                                  | Obrigatório |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | A propriedade **Type** do local no DataSet deve ser definida como **AzureBlobStorageLocation**. | Sim      |
| contêiner  | O contêiner de blob.                                          | Sim      |
| folderPath | O caminho para a pasta sob o contêiner fornecido. Se você quiser usar um curinga para filtrar a pasta, ignore essa configuração e especifique isso nas configurações de origem da atividade. | Não       |
| fileName   | O nome do arquivo no contêiner e no caminho da pasta fornecidos. Se você quiser usar curinga para filtrar arquivos, ignore essa configuração e especifique isso nas configurações de origem da atividade. | Não       |

**Exemplo:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
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

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte do coletor e da fonte de armazenamento de BLOBs.

### <a name="blob-storage-as-a-source-type"></a>Armazenamento de blob como um tipo de fonte

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As propriedades a seguir têm suporte para o armazenamento de BLOBs do Azure em `storeSettings` configurações em uma fonte de cópia baseada em formato:

| Propriedade                 | Descrição                                                  | Obrigatório                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | A propriedade **Type** em `storeSettings` deve ser definida como **AzureBlobStorageReadSettings**. | Sim                                           |
| **_Localize os arquivos a serem copiados:_* _ |  |  |
| OPÇÃO 1: caminho estático<br> | Copie do caminho do contêiner ou da pasta/do arquivo especificado no conjunto de dados. Se você quiser copiar todos os blobs de um contêiner ou pasta, especifique também `wildcardFileName` como `_` . |  |
| OPÇÃO 2: prefixo do blob<br>- prefix | Prefixo do nome do blob no contêiner fornecido configurado em um conjunto de dados para filtrar os blobs de origem. Os BLOBs cujos nomes começam com `container_in_dataset/this_prefix` são selecionados. Ele utiliza o filtro do lado do serviço para armazenamento de BLOBs, que fornece melhor desempenho do que um filtro curinga.<br><br>Quando você usa o prefixo e opta por copiar para o coletor baseado em arquivo com a hierarquia de preservação, observe que o subcaminho após o último "/" no prefixo será preservado. Por exemplo, você tem origem  `container/folder/subfolder/file.txt` e configura o prefixo como `folder/sub` , então o caminho do arquivo preservado é `subfolder/file.txt` . | Não                                                          |
| OPÇÃO 3: curinga<br>- wildcardFolderPath | O caminho da pasta com caracteres curinga no contêiner fornecido configurado em um conjunto de dados para filtrar as pastas de origem. <br>Os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único). Use `^` para escapar se o nome da pasta tiver curinga ou este caractere de escape dentro de. <br>Veja mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). | Não                                            |
| OPÇÃO 3: curinga<br>- wildcardFileName | O nome do arquivo com caracteres curinga no contêiner e caminho de pasta fornecidos (ou caminho da pasta curinga) para filtrar os arquivos de origem. <br>Os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único). Use `^` para escapar se o nome do arquivo tiver um curinga ou este caractere de escape dentro de. Veja mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). | Sim |
| OPÇÃO 4: uma lista de arquivos<br>- fileListPath | Indica a cópia de um determinado conjunto de arquivos. Aponte para um arquivo de texto que inclui uma lista de arquivos que você deseja copiar, um arquivo por linha, que é o caminho relativo para o caminho configurado no conjunto de um.<br/>Quando você estiver usando essa opção, não especifique um nome de arquivo no conjunto de um. Veja mais exemplos em [Exemplos de lista de arquivos](#file-list-examples). |Não |
| ***Configurações adicionais:** _ |  | |
| recursiva | Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. Observe que quando _ *recursivo** é definido como **true** e o coletor é um armazenamento baseado em arquivo, uma pasta ou subpasta vazia não é copiada ou criada no coletor. <br>Os valores permitidos são **true** (padrão) e **false**.<br>Essa propriedade não se aplica quando você configura `fileListPath`. |Não |
| deleteFilesAfterCompletion | Indica se os arquivos binários serão excluídos do repositório de origem após a movimentação com êxito para o repositório de destino. A exclusão do arquivo é por arquivo, portanto, quando a atividade de cópia falhar, você verá que alguns arquivos já foram copiados para o destino e excluídos da origem, enquanto outros ainda permanecem no repositório de origem. <br/>Esta propriedade só é válida no cenário de cópia de arquivos binários. O valor padrão: false. |Não |
| modifiedDatetimeStart    | Os arquivos são filtrados com base no atributo: última modificação. <br>Os arquivos serão escolhidos se a hora da última alteração estiver dentro do período entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. O tempo é aplicado a um fuso horário UTC no formato "2018-12-01T05:00:00Z". <br> As propriedades podem ser **nulas**, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de um.  Quando `modifiedDatetimeStart` tem um valor DateTime, mas `modifiedDatetimeEnd` é **nulo**, os arquivos cujo último atributo modificado é maior ou igual ao valor DateTime serão selecionados.  Quando `modifiedDatetimeEnd` tem um valor DateTime, mas `modifiedDatetimeStart` é **nulo**, os arquivos cujo último atributo modificado é menor que o valor DateTime será selecionado.<br/>Essa propriedade não se aplica quando você configura `fileListPath`. | Não                                            |
| modifiedDatetimeEnd      | Mesmo que acima.                                               | Não                                            |
| enablePartitionDiscovery | Para arquivos que são particionados, especifique se deseja analisar as partições do caminho do arquivo e adicioná-las como colunas de origem adicionais.<br/>Os valores permitidos são **false** (padrão) e **true**. | Não                                            |
| partitionRootPath | Quando a descoberta de partição estiver habilitada, especifique o caminho raiz absoluto para ler as pastas particionadas como colunas de dados.<br/><br/>Se não for especificado, por padrão,<br/>-Quando você usa o caminho do arquivo no conjunto de programas ou na lista de arquivos na origem, o caminho raiz da partição é o caminho configurado no conjunto de um.<br/>-Quando você usa o filtro de pasta curinga, o caminho raiz da partição é o subcaminho antes do primeiro caractere curinga.<br/>-Quando você usa o prefixo, o caminho raiz da partição é subcaminho antes do último "/". <br/><br/>Por exemplo, supondo que você configure o caminho no conjunto de um como "raiz/pasta/ano = 2020/mês = 08/dia = 27":<br/>-Se você especificar o caminho raiz da partição como "raiz/pasta/ano = 2020", a atividade de cópia irá gerar mais duas colunas `month` e `day` com o valor "08" e "27", respectivamente, além das colunas dentro dos arquivos.<br/>-Se o caminho raiz da partição não for especificado, nenhuma coluna extra será gerada. | Não                                            |
| maxConcurrentConnections | O número de conexões simultâneas com o armazenamento. Especifique somente quando desejar limitar as conexões simultâneas ao armazenamento de dados. | Não                                            |

> [!NOTE]
> Para o formato de texto parquet/delimitado, o tipo de **blobname** para a origem da atividade de cópia mencionada na próxima seção ainda tem suporte como é para compatibilidade com versões anteriores. Sugerimos que você use o novo modelo até que a interface do usuário de criação do Data Factory tenha mudado para gerar esses novos tipos.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
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
                    "type": "AzureBlobStorageReadSettings",
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

> [!NOTE]
> O `$logs` contêiner, que é criado automaticamente quando análise de armazenamento está habilitado para uma conta de armazenamento, não é mostrado quando uma operação de listagem de contêiner é executada por meio da interface do usuário do data Factory. O caminho do arquivo deve ser fornecido diretamente para Data Factory consumir arquivos do `$logs` contêiner.

### <a name="blob-storage-as-a-sink-type"></a>Armazenamento de blob como um tipo de coletor

[!INCLUDE [data-factory-v2-file-sink-formats](../../includes/data-factory-v2-file-sink-formats.md)] 

As propriedades a seguir têm suporte para o armazenamento de BLOBs do Azure em `storeSettings` configurações em um coletor de cópia com base em formato:

| Propriedade                 | Descrição                                                  | Obrigatório |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | A propriedade **Type** em `storeSettings` deve ser definida como **AzureBlobStorageWriteSettings**. | Sim      |
| copyBehavior             | Define o comportamento de cópia quando a fonte for de arquivos de um armazenamento de dados baseado em arquivo.<br/><br/>Valores permitidos são:<br/><b>– PreserveHierarchy (padrão)</b>: Preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino.<br/><b>– FlattenHierarchy</b>: Todos os arquivos da pasta de origem estão no primeiro nível da pasta de destino. Os arquivos de destino têm os nomes gerados automaticamente. <br/><b>– MergeFiles</b>: Mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo ou do blob for especificado, o nome do arquivo mesclado será o nome especificado. Caso contrário, ele será um nome de arquivo gerado automaticamente. | Não       |
| blockSizeInMB | Especifique o tamanho do bloco, em megabytes, usado para gravar dados em blobs de blocos. Saiba mais [sobre Blobs de Blocos](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs). <br/>O valor permitido é *entre 4 MB e 100 MB*. <br/>Por padrão, Data Factory determina automaticamente o tamanho do bloco com base no tipo de armazenamento de origem e nos dados. Para cópia não binária no armazenamento de BLOBs, o tamanho de bloco padrão é 100 MB para que ele possa caber (no máximo) 4,95 TB de dados. Ele pode não ser ideal quando os dados não são grandes, especialmente quando você usa o tempo de execução de integração auto-hospedado com conexões de rede inadequadas que resultam em problemas de desempenho ou tempo limite da operação. Você pode especificar explicitamente um tamanho de bloco, garantindo que `blockSizeInMB*50000` seja grande o suficiente para armazenar os dados. Caso contrário, a execução da atividade de cópia falhará. | Não |
| maxConcurrentConnections | O número de conexões simultâneas com o armazenamento. Especifique somente quando desejar limitar as conexões simultâneas ao armazenamento de dados. | Não       |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
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
                    "type": "AzureBlobStorageWriteSettings",
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
| `container/Folder*` | (vazio, usar padrão) | false | contêiner<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| `container/Folder*` | (vazio, usar padrão) | true | contêiner<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| `container/Folder*` | `*.csv` | false | contêiner<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| `container/Folder*` | `*.csv` | true | contêiner<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |

### <a name="file-list-examples"></a>Exemplos de lista de arquivos

Esta seção descreve o comportamento resultante do uso de um caminho de lista de arquivos na origem da atividade de cópia.

Suponha que você tenha a seguinte estrutura de pasta de origem e queira copiar os arquivos em negrito:

| Exemplo de estrutura de origem                                      | Conteúdo em FileListToCopy.txt                             | Configuração de Data Factory                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| contêiner<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadados<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **No conjunto de dados:**<br>- Contêiner: `container`<br>- Caminho da pasta: `FolderA`<br><br>**Na origem da atividade de cópia:**<br>- Caminho da lista de arquivos: `container/Metadata/FileListToCopy.txt` <br><br>O caminho da lista de arquivos aponta para um arquivo de texto no mesmo armazenamento de dados que inclui uma lista de arquivos que você deseja copiar, um arquivo por linha, com o caminho relativo para o caminho configurado no conjunto de dados. |

### <a name="some-recursive-and-copybehavior-examples"></a>Alguns exemplos de recursive e copyBehavior

Esta seção descreve o comportamento resultante da operação de cópia para diferentes combinações de valores **recursivos** e **copyBehavior** .

| recursiva | copyBehavior | Estrutura de pasta de origem | Destino resultante |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A pasta de destino, Pasta1, é criada com a mesma estrutura da origem:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 |
| true |flattenHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A pasta de destino, Pasta1, é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo5 |
| true |mergeFiles | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A pasta de destino, Pasta1, é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos de Arquivo1 + Arquivo2 + Arquivo3 + Arquivo4 + Arquivo5 são mesclados em um arquivo com um nome de arquivo gerado automaticamente. |
| false |preserveHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A pasta de destino, Pasta1, é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/><br/>A Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não é selecionada. |
| false |flattenHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A pasta de destino, Pasta1, é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo2<br/><br/>A Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não é selecionada. |
| false |mergeFiles | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A pasta de destino, Pasta1, é criada com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos de Arquivo1 + Arquivo2 são mesclados em um arquivo com um nome de arquivo gerado automaticamente. nome gerado automaticamente para o Arquivo1<br/><br/>A Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não é selecionada. |

## <a name="preserving-metadata-during-copy"></a>Preservando metadados durante a cópia

Ao copiar arquivos do Amazon S3, armazenamento de BLOBs do Azure ou Azure Data Lake Storage Gen2 para Azure Data Lake Storage Gen2 ou armazenamento de BLOBs do Azure, você pode optar por preservar os metadados do arquivo junto com os dados. Saiba mais em [Preservar metadados](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="mapping-data-flow-properties"></a>Mapeamento de propriedades de fluxo de dados

Quando estiver transformando dados no mapeamento de fluxos de dados, você poderá ler e gravar arquivos do armazenamento de BLOBs do Azure nos seguintes formatos:
* [Avro](format-avro.md#mapping-data-flow-properties)
* [Texto delimitado](format-delimited-text.md#mapping-data-flow-properties)
* [Trifásico](format-delta.md#mapping-data-flow-properties)
* [Excel](format-excel.md#mapping-data-flow-properties)
* [JSON](format-json.md#mapping-data-flow-properties)
* [Parquet](format-parquet.md#mapping-data-flow-properties)

As configurações de formato específico estão localizadas na documentação para esse formato. Para obter mais informações, consulte [transformação de origem no mapeamento de fluxo de dados](data-flow-source.md) e [transformação de coletor no fluxo de dados de mapeamento](data-flow-sink.md).

### <a name="source-transformation"></a>Transformação de origem

Em transformação de origem, você pode ler de um contêiner, pasta ou arquivo individual no armazenamento de BLOBs do Azure. Use a guia **Opções de origem** para gerenciar como os arquivos são lidos. 

![Opções de origem](media/data-flow/sourceOptions1.png "Opções de origem")

**Caminhos curinga:** Usar um padrão curinga instruirá Data Factory a executar um loop em cada pasta e arquivo correspondentes em uma única transformação de origem. Esse é um modo eficaz de processar vários arquivos dentro de um único fluxo. Adicione vários padrões de correspondência de curinga com o sinal de adição que aparece quando você passa o mouse sobre o padrão de curinga existente.

Em seu contêiner de origem, escolha uma série de arquivos que correspondem a um padrão. Somente um contêiner pode ser especificado no DataSet. O caminho curinga, portanto, também precisa incluir o caminho da pasta raiz.

Exemplos de caracteres curinga:

* ```*``` Representa qualquer conjunto de caracteres.
* ```**``` Representa o aninhamento de diretório recursivo.
* ```?``` Substitui um caractere.
* ```[]``` Corresponde a um ou mais caracteres entre colchetes.

* ```/data/sales/**/*.csv``` Obtém todos os arquivos. csv em/data/Sales.
* ```/data/sales/20??/**/``` Obtém todos os arquivos no século 20.
* ```/data/sales/*/*/*.csv``` Obtém os arquivos. csv dois níveis em/data/Sales.
* ```/data/sales/2004/*/12/[XY]1?.csv``` Obtém todos os arquivos. csv em dezembro de 2004, começando com X ou Y prefixados por um número de dois dígitos.

**Caminho raiz da partição:** Se você tiver pastas particionadas em sua fonte de arquivo com um ```key=value``` formato (por exemplo, `year=2019` ), poderá atribuir o nível superior dessa árvore de pastas de partição a um nome de coluna no fluxo de dados do fluxo de dados.

Primeiro, defina um curinga para incluir todos os caminhos que são as pastas particionadas mais os arquivos folha que você deseja ler.

![Configurações do arquivo de origem da partição](media/data-flow/partfile2.png "Configuração de arquivos da partição")

Use a configuração **caminho raiz da partição** para definir qual é o nível superior da estrutura de pastas. Ao exibir o conteúdo de seus dados por meio de uma visualização de dados, você verá que Data Factory adicionará as partições resolvidas encontradas em cada um dos níveis de pasta.

![Caminho raiz da partição](media/data-flow/partfile1.png "Visualização do caminho raiz da partição")

**Lista de arquivos:** é um conjunto de arquivos. Crie um arquivo de texto que inclui uma lista de arquivos do caminho relativo a serem processados. Aponte para este arquivo de texto.

**Coluna para armazenar o nome do arquivo:** armazene o nome do arquivo de origem em uma coluna em seus dados. Insira um novo nome de coluna para armazenar a cadeia de caracteres de nome de arquivo.

**Após a conclusão:** Opte por não fazer nada com o arquivo de origem após a execução de fluxo de dados, excluir o arquivo de origem ou mover o arquivo de origem. Os caminhos para movimentação são relativos.

Para mover os arquivos de origem para outro local após o processamento, primeiro selecione "Mover" para a operação de arquivo. Em seguida, defina o diretório "de". Se você não estiver usando curingas para o caminho, a configuração "de" será a mesma pasta que a de origem.

Se você tiver um caminho de origem com curinga, sua sintaxe terá a seguinte aparência:

```/data/sales/20??/**/*.csv```

Você pode especificar "de" como:

```/data/sales```

E você pode especificar "to" como:

```/backup/priorSales```

Nesse caso, todos os arquivos que foram originados em /data/sales são movidos para /backup/priorSales.

> [!NOTE]
> As operações do arquivo são executadas somente quando você inicia o fluxo de dados de uma execução de pipeline (depuração de pipeline ou realização da execução) que usa a atividade Executar Fluxo de Dados em um pipeline. As operações de arquivo *não são* executadas no modo de depuração do Fluxo de Dados.

**Filtrar por última modificação:** você pode filtrar quais arquivos são processados especificando um intervalo de datas de quando eles foram modificados pela última vez. Todos os DateTimes estão em UTC. 

### <a name="sink-properties"></a>Propriedades do coletor

Na transformação do coletor, você pode gravar em um contêiner ou em uma pasta no armazenamento de BLOBs do Azure. Use a guia **configurações** para gerenciar como os arquivos são gravados.

![Opções de coletor](media/data-flow/file-sink-settings.png "opções de coletor")

**Limpe a pasta:** determina se a pasta de destino é limpa ou não antes de os dados serem gravados.

**Opção do nome do arquivo:** determina como os arquivos de destino são nomeados na pasta de destino. As opções de nome de arquivo são:
   * **Padrão**: permitir que o Spark nomeie arquivos com base nos padrões de PART.
   * **Padrão**: insira um padrão que enumere os arquivos de saída por partição. Por exemplo, **loans[n].csv** criará loans1.csv, loans2.csv e assim por diante.
   * **Por partição**: insira um nome de arquivo por partição.
   * **Como dados na coluna**: defina o arquivo de saída para o valor de uma coluna. O caminho é relativo ao contêiner de conjunto de dados, não à pasta de destino. Se você tiver um caminho de pasta em seu conjunto de dados, ele será substituído.
   * **Saída para um único arquivo**: combine os arquivos de saída particionados em um único arquivo nomeado. O caminho é relativo à pasta do conjunto de dados. Lembre-se de que a operação de mesclagem possivelmente pode falhar com base no tamanho do nó. Não recomendamos essa opção para grandes conjuntos de altos.

**Cotar tudo:** Determina se todos os valores entre aspas devem ser demarcados.

## <a name="lookup-activity-properties"></a>Pesquisar propriedades de atividade

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
| type | A propriedade **Type** do conjunto de conjuntos deve ser definida como **AzureBlob**. |Sim |
| folderPath | Caminho para o contêiner e a pasta no armazenamento de BLOBs. <br/><br/>Há suporte para um filtro curinga para o caminho, excluindo o nome do contêiner. Os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único). Use `^` para escapar se o nome da pasta tiver um curinga ou este caractere de escape dentro de. <br/><br/>Um exemplo é: myblobcontainer/myblobfolder/. Veja mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). |Sim para a atividade de cópia ou pesquisa, não para a atividade GetMetadata |
| fileName | Filtro de nome ou curinga para os BLOBs sob o valor **FolderPath** especificado. Se você não especificar algum valor para essa propriedade, o conjunto de dados apontará para todos os blobs na pasta. <br/><br/>Para o filtro, os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou a um único caractere).<br/>– Exemplo 1: `"fileName": "*.csv"`<br/>– Exemplo 2: `"fileName": "???20180427.txt"`<br/>Use `^` para escapar se o nome do arquivo tiver um curinga ou este caractere de escape dentro de.<br/><br/>Quando **filename** não é especificado para um conjunto de dados de saída e **preserveHierarchy** não é especificado no coletor de atividade, a atividade de cópia gera automaticamente o nome do blob com o seguinte padrão: "*Data. [ GUID de ID de execução de atividade]. [GUID If FlattenHierarchy]. [Formatar se configurado]. [compactação se configurada]*". Por exemplo: "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. gz". <br/><br/>Se você copiar de uma fonte de tabela usando um nome de tabela em vez de uma consulta, o padrão de nome será "*[nome da tabela]. [ formato]. [compactação se configurada]*". Por exemplo: "MyTable.csv". |Não |
| modifiedDatetimeStart | Os arquivos são filtrados com base no atributo: última modificação. Os arquivos serão escolhidos se a hora da última alteração estiver dentro do período entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário de UTC no formato "2018-12-01T05:00:00Z". <br/><br/> Lembre-se de que a habilitação dessa configuração afetará o desempenho geral da movimentação de dados quando você quiser filtrar grandes quantidades de arquivos. <br/><br/> As propriedades podem ser **nulas**, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de um.  Quando `modifiedDatetimeStart` tem um valor DateTime, mas `modifiedDatetimeEnd` é **nulo**, os arquivos cujo último atributo modificado é maior ou igual ao valor DateTime serão selecionados.  Quando `modifiedDatetimeEnd` tem um valor DateTime, mas `modifiedDatetimeStart` é **nulo**, os arquivos cujo último atributo modificado é menor que o valor DateTime será selecionado.| Não |
| modifiedDatetimeEnd | Os arquivos são filtrados com base no atributo: última modificação. Os arquivos serão escolhidos se a hora da última alteração estiver dentro do período entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário de UTC no formato "2018-12-01T05:00:00Z". <br/><br/> Lembre-se de que a habilitação dessa configuração afetará o desempenho geral da movimentação de dados quando você quiser filtrar grandes quantidades de arquivos. <br/><br/> As propriedades podem ser **nulas**, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de um.  Quando `modifiedDatetimeStart` tem um valor DateTime, mas `modifiedDatetimeEnd` é **nulo**, os arquivos cujo último atributo modificado é maior ou igual ao valor DateTime serão selecionados.  Quando `modifiedDatetimeEnd` tem um valor DateTime, mas `modifiedDatetimeStart` é **nulo**, os arquivos cujo último atributo modificado é menor que o valor DateTime será selecionado.| Não |
| format | Se você quiser copiar arquivos no estado em que se encontram entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas duas definições de conjunto de dados de entrada e de saída.<br/><br/>Se você quer analisar ou gerar arquivos com um formato específico, os seguintes tipos de formato de arquivo são compatíveis: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Defina a propriedade **type** sob **format** para um desses valores. Para saber mais, veja as seções [Formato de texto](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Formato JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Formato Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Formato Orc](supported-file-formats-and-compression-codecs-legacy.md#orc-format) e [Formato Parquet](supported-file-formats-and-compression-codecs-legacy.md#parquet-format). |Não (somente para o cenário de cópia binária) |
| compactação | Especifique o tipo e o nível de compactação para os dados. Para obter mais informações, consulte [Formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Os tipos com suporte são: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>Os níveis de suporte são **Ideal** e **Mais rápido**. |Não |

>[!TIP]
>Para copiar todos os blobs em uma pasta, especifique **folderPath** somente.<br>Para copiar um único blob com um nome específico, especifique **FolderPath** para a parte da pasta e **filename** para o nome do arquivo.<br>Para copiar um subconjunto de BLOBs em uma pasta, especifique **FolderPath** para a parte da pasta e o **nome do arquivo** com um filtro curinga. 

**Exemplo:**

```json
{
    "name": "AzureBlobDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "<Azure Blob storage linked service name>",
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

### <a name="legacy-source-model-for-the-copy-activity"></a>Modelo de origem herdado para a atividade de cópia

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade **Type** da fonte da atividade de cópia deve ser definida como **blobname**. |Sim |
| recursiva | Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. Observe que quando **recursivo** é definido como **true** e o coletor é um armazenamento baseado em arquivo, uma pasta ou subpasta vazia não é copiada ou criada no coletor.<br/>Os valores permitidos são **true** (padrão) e **false**. | Não |
| maxConcurrentConnections | O número de conexões simultâneas com o armazenamento. Especifique somente quando desejar limitar as conexões simultâneas ao armazenamento de dados. | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Blob input dataset name>",
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
                "type": "BlobSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="legacy-sink-model-for-the-copy-activity"></a>Modelo de coletor herdado para a atividade de cópia

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade **Type** do coletor da atividade de cópia deve ser definida como **BlobSink**. |Sim |
| copyBehavior | Define o comportamento de cópia quando a fonte for de arquivos de um armazenamento de dados baseado em arquivo.<br/><br/>Valores permitidos são:<br/><b>– PreserveHierarchy (padrão)</b>: Preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino.<br/><b>– FlattenHierarchy</b>: Todos os arquivos da pasta de origem estão no primeiro nível da pasta de destino. Os arquivos de destino têm os nomes gerados automaticamente. <br/><b>– MergeFiles</b>: Mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo ou do blob for especificado, o nome do arquivo mesclado será o nome especificado. Caso contrário, ele será um nome de arquivo gerado automaticamente. | Não |
| maxConcurrentConnections | O número de conexões simultâneas com o armazenamento. Especifique somente quando desejar limitar as conexões simultâneas ao armazenamento de dados. | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Blob output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "BlobSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>Próximas etapas

Para obter uma lista de armazenamentos de dados que a atividade de cópia no Data Factory dá suporte como fontes e coletores, consulte [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
