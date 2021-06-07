---
title: Copiar dados no ou do Azure Data Lake Storage Gen1
description: Saiba como copiar dados de armazenamentos de dados de origem com suporte para o Azure Data Lake Store ou do Data Lake Store para armazenamentos de coletor com suporte usando o Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: 2b064412fab7b81a3e0cd164456e8fc5c3b35ae2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104597548"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>Copiar dados no/do Azure Data Lake Storage Gen1 usando o Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do Azure Data Factory que você está usando:"]
>
> * [Versão 1](v1/data-factory-azure-datalake-connector.md)
> * [Versão atual](connector-azure-data-lake-store.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como copiar dados de e para o Azure Data Lake Storage Gen1. Para saber mais sobre o Azure Data Factory, leia as [artigo introdutório](introduction.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector do Azure Data Lake Storage Gen1 é compatível com as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/coletor compatível](copy-activity-overview.md) 
- [Fluxo de dados de mapeamento](concepts-data-flow-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)
- [Excluir atividade](delete-activity.md)

Especificamente, com esse conector, você pode:

- Copiar arquivos usando um dos seguintes métodos de autenticação: entidade de serviço ou identidades gerenciadas para recursos do Azure.
- Copiar arquivos no estado em que se encontram ou analisar ou gerar arquivos com [formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md).
- [Preserve as ACLs](#preserve-acls-to-data-lake-storage-gen2) ao copiar no Azure Data Lake Storage Gen2.

> [!IMPORTANT]
> Se você copiar dados usando o runtime de integração auto-hospedada, configure o firewall corporativo para permitir o tráfego de saída para `<ADLS account name>.azuredatalakestore.net` e `login.microsoftonline.com/<tenant>/oauth2/token` na porta 443. O último é o Serviço de Token de Segurança do Azure com que o runtime de integração precisa se comunicar para obter o token de acesso.

## <a name="get-started"></a>Introdução

> [!TIP]
> Para obter um passo a passo de como usar o conector do Azure Data Lake Storage, veja [Carregar dados no Azure Data Lake Storage](load-azure-data-lake-store.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem informações sobre propriedades que são usadas para definir entidades do Data Factory específicas do Azure Data Lake Storage.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte no serviço vinculado do Azure Data Lake Store:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade `type` deve ser definida como: **AzureDataLakeStore**. | Sim |
| dataLakeStoreUri | Informações sobre a conta do Azure Data Lake Store. Essas informações usam um dos seguintes formatos: `https://[accountname].azuredatalakestore.net/webhdfs/v1` ou `adl://[accountname].azuredatalakestore.net/`. | Sim |
| subscriptionId | A ID de assinatura do Azure à qual a conta do Data Lake Store pertence. | Obrigatório para coletor |
| resourceGroupName | O nome do grupo de recursos do Azure ao qual a conta do Data Lake Store pertence. | Obrigatório para coletor |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você poderá usar o runtime de integração do Azure ou um runtime de integração auto-hospedada se o seu armazenamento de dados estiver localizado em uma rede privada. Se essa propriedade não for especificada, o runtime de integração do Azure padrão será usado. |Não |

### <a name="use-service-principal-authentication"></a>Usar a autenticação de entidade de serviço

Para usar a autenticação de entidade de serviço, siga estas etapas.

1. Registre uma entidade de aplicativo no Azure Active Directory e conceda a ela o acesso ao Data Lake Storage. Para encontrar as etapas detalhadas, consulte [Autenticação de serviço a serviço](../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md). Anote os seguintes valores, que são usados para definir o serviço vinculado:

    - ID do aplicativo
    - Chave do aplicativo
    - ID do locatário

2. Conceda a permissão adequada da entidade de serviço. Veja exemplos de como a permissão funciona no Data Lake Storage Gen1 em [Controle de acesso no Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions).

    - **Como origem**: em **Data Explorer** > **Acesso**, conceda pelo menos a permissão **Execução** para TODAS as pastas acima, incluindo a raiz, junto com a permissão **Leitura** para os arquivos que serão copiados. Você pode optar por adicionar a **Esta pasta e todos os filhos** para recursivo e adicionar como **uma permissão de acesso e uma entrada de permissão padrão**. Não há requisito de controle de acesso no nível da conta (IAM).
    - **Como coletor**: em **Data Explorer** > **Acesso**, conceda pelo menos a permissão **Execução** para TODAS as pastas acima, incluindo a raiz, junto com a permissão **Gravação** para a pasta coletora. Você pode optar por adicionar a **Esta pasta e todos os filhos** para recursivo e adicionar como **uma permissão de acesso e uma entrada de permissão padrão**.

Há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| servicePrincipalId | Especifique a ID do cliente do aplicativo. | Sim |
| servicePrincipalKey | Especifique a chave do aplicativo. Marque este campo como uma `SecureString` para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| locatário | Especifique as informações de locatário, como nome de domínio ou ID do locatário, em que o aplicativo reside. É possível recuperá-las focalizando o mouse no canto superior direito do Portal do Azure. | Sim |
| azureCloudType | Para autenticação de entidade de serviço, especifique o tipo de ambiente de nuvem do Azure no qual seu aplicativo Azure Active Directory está registrado. <br/> Os valores permitidos são **AzurePublic**, **AzureChina**, **AzureUsGovernment** e **AzureGermany**. Por padrão, o ambiente de nuvem do data factory é usado. | Não |

**Exemplo:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Usar identidades gerenciadas para autenticação de recursos do Azure

Um data factory pode ser associado a uma [identidade gerenciada para recursos do Azure](data-factory-service-identity.md), que representa esse data factory específico. Você pode usar essa identidade gerenciada diretamente para a autenticação do Data Lake Storage da mesma maneira que no uso de sua própria entidade de serviço. Ele permite que este alocador designado acesse e copie dados de ou para o seu Data Lake Store.

Para usar identidades gerenciadas para autenticação de recursos do Azure, siga estas etapas.

1. [Recuperar a identidade gerenciada do data factory](data-factory-service-identity.md#retrieve-managed-identity) copiando o valor de "ID do Aplicativo de Identidade do Serviço" gerado junto com seu alocador.

2. Conceda à identidade gerenciada acesso ao Data Lake Storage. Veja exemplos de como a permissão funciona no Data Lake Storage Gen1 em [Controle de acesso no Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions).

    - **Como origem**: em **Data Explorer** > **Acesso**, conceda pelo menos a permissão **Execução** para TODAS as pastas acima, incluindo a raiz, junto com a permissão **Leitura** para os arquivos que serão copiados. Você pode optar por adicionar a **Esta pasta e todos os filhos** para recursivo e adicionar como **uma permissão de acesso e uma entrada de permissão padrão**. Não há requisito de controle de acesso no nível da conta (IAM).
    - **Como coletor**: em **Data Explorer** > **Acesso**, conceda pelo menos a permissão **Execução** para TODAS as pastas acima, incluindo a raiz, junto com a permissão **Gravação** para a pasta coletora. Você pode optar por adicionar a **Esta pasta e todos os filhos** para recursivo e adicionar como **uma permissão de acesso e uma entrada de permissão padrão**.

No Azure Data Factory, você não precisa especificar nenhuma propriedade além das informações do Data Lake Store gerais no serviço vinculado.

**Exemplo:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
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

As seguintes propriedades são compatíveis com o Azure Data Lake Storage Gen1 em configurações de `location` no conjunto de dados baseado em formato:

| Propriedade   | Descrição                                                  | Obrigatório |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | A propriedade type em `location` no conjunto de dados deve ser definida como **AzureDataLakeStoreLocation**. | Sim      |
| folderPath | O caminho para uma pasta. Se você quiser usar um caractere curinga para filtrar a pasta, ignore essa configuração e especifique-o nas configurações de origem da atividade. | Não       |
| fileName   | O nome do arquivo sob o folderPath fornecido. Se você quiser usar um caractere curinga para filtrar os arquivos, ignore essa configuração e especifique-o nas configurações de origem da atividade. | Não       |

**Exemplo:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<ADLS Gen1 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureDataLakeStoreLocation",
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

Para obter uma lista completa das seções e propriedades disponíveis para definir as atividades, veja [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela origem e pelo coletor do Azure Data Lake Store.

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store como fonte

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As seguintes propriedades são compatíveis com o Azure Data Lake Storage Gen1 em configurações de `storeSettings` na fonte de dados baseada em formato:

| Propriedade                 | Descrição                                                  | Obrigatório                                     |
| ------------------------ | ------------------------------------------------------------ | -------------------------------------------- |
| type                     | A propriedade type em `storeSettings` deve ser configurada com **AzureDataLakeStoreReadSettings**. | Sim                                          |
| ***Localize os arquivos a serem copiados:*** |  |  |
| OPÇÃO 1: caminho estático<br> | Copiar do caminho de pasta/arquivo especificado no conjunto de dados. Se quiser copiar todos os arquivos de uma pasta, especifique também `wildcardFileName` como `*`. |  |
| OPÇÃO 2: intervalo de nomes<br>- listAfter | Recupere as pastas/arquivos cujo nome é após esse valor em ordem alfabética (exclusivo). Ele utiliza o filtro do lado do serviço para ADLS Gen1, que fornece melhor desempenho do que um filtro curinga. <br/>O data Factory aplica esse filtro ao caminho definido no conjunto de dados e há suporte para apenas um nível de entidade. Veja mais exemplos em [exemplos de filtro de intervalo de nomes](#name-range-filter-examples). | Não |
| OPÇÃO 2: intervalo de nomes<br/>- listBefore | Recupere as pastas/arquivos cujo nome é antes desse valor em ordem alfabética (inclusivo). Ele utiliza o filtro do lado do serviço para ADLS Gen1, que fornece melhor desempenho do que um filtro curinga.<br>O data Factory aplica esse filtro ao caminho definido no conjunto de dados e há suporte para apenas um nível de entidade. Veja mais exemplos em [exemplos de filtro de intervalo de nomes](#name-range-filter-examples). | Não |
| OPÇÃO 3: curinga<br>- wildcardFolderPath | O caminho da pasta com caracteres curinga para filtrar as pastas de origem. <br>Os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único); use `^` para escape se o nome de pasta atual tiver curinga ou esse caractere interno de escape. <br>Veja mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). | Não                                            |
| OPÇÃO 3: curinga<br>- wildcardFileName | O nome do arquivo com caracteres curinga sob o folderPath/wildcardFolderPath fornecido para filtrar os arquivos de origem. <br>Os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único); use `^` para escape se o nome de arquivo real tiver curinga ou esse caractere interno de escape.  Veja mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). | Sim |
| OPÇÃO 4: uma lista de arquivos<br>- fileListPath | Indica a cópia de um determinado conjunto de arquivos. Aponte para um arquivo de texto que inclui uma lista de arquivos que você deseja copiar, um arquivo por linha, que é o caminho relativo para o caminho configurado no conjunto de um.<br/>Ao usar essa opção, não especifique o nome do arquivo no conjunto de dados. Veja mais exemplos em [Exemplos de lista de arquivos](#file-list-examples). |Não |
| ***Configurações adicionais:*** |  | |
| recursiva | Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. Observe que quando recursiva é definida como true e o coletor é um armazenamento baseado em arquivo, uma pasta vazia ou subpasta não é copiada ou criada no coletor. <br>Os valores permitidos são **true** (padrão) e **false**.<br>Essa propriedade não se aplica quando você configura `fileListPath`. |Não |
| deleteFilesAfterCompletion | Indica se os arquivos binários serão excluídos do repositório de origem após a movimentação com êxito para o repositório de destino. A exclusão do arquivo é por arquivo, portanto, quando a atividade de cópia falhar, você verá que alguns arquivos já foram copiados para o destino e excluídos da origem, enquanto outros ainda permanecem no repositório de origem. <br/>Esta propriedade só é válida no cenário de cópia de arquivos binários. O valor padrão: false. |Não |
| modifiedDatetimeStart    | Filtro de arquivos com base no atributo: Última Modificação. <br>Os arquivos serão escolhidos se a hora da última alteração estiver dentro do período entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário de UTC no formato "2018-12-01T05:00:00Z". <br> As propriedades podem ser nulas, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de um.  Quando `modifiedDatetimeStart` tem o valor de data e hora, mas `modifiedDatetimeEnd` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é maior ou igual ao valor de data e hora.  Quando `modifiedDatetimeEnd` tem o valor de data e hora, mas `modifiedDatetimeStart` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é menor que o valor de data e hora.<br/>Essa propriedade não se aplica quando você configura `fileListPath`. | Não                                            |
| modifiedDatetimeEnd      | Mesmo que acima.                                               | Não                                           |
| enablePartitionDiscovery | Para arquivos que são particionados, especifique se deseja analisar as partições do caminho do arquivo e adicioná-las como colunas de origem adicionais.<br/>Os valores permitidos são **false** (padrão) e **true**. | Não                                            |
| partitionRootPath | Quando a descoberta de partição estiver habilitada, especifique o caminho raiz absoluto para ler as pastas particionadas como colunas de dados.<br/><br/>Se não for especificado, por padrão,<br/>-Quando você usa o caminho do arquivo no conjunto de programas ou na lista de arquivos na origem, o caminho raiz da partição é o caminho configurado no conjunto de um.<br/>-Quando você usa o filtro de pasta curinga, o caminho raiz da partição é o subcaminho antes do primeiro caractere curinga.<br/><br/>Por exemplo, supondo que você configure o caminho no conjunto de um como "raiz/pasta/ano = 2020/mês = 08/dia = 27":<br/>-Se você especificar o caminho raiz da partição como "raiz/pasta/ano = 2020", a atividade de cópia irá gerar mais duas colunas `month` e `day` com o valor "08" e "27", respectivamente, além das colunas dentro dos arquivos.<br/>-Se o caminho raiz da partição não for especificado, nenhuma coluna extra será gerada. | Não                                            |
| maxConcurrentConnections | O limite superior de conexões simultâneas estabelecidas com o armazenamento de dados durante a execução da atividade. Especifique um valor somente quando desejar limitar as conexões simultâneas.| Não                                           |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
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
                    "type": "AzureDataLakeStoreReadSettings",
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

### <a name="azure-data-lake-store-as-sink"></a>Azure Data Lake Store como coletor

[!INCLUDE [data-factory-v2-file-sink-formats](../../includes/data-factory-v2-file-sink-formats.md)]

As seguintes propriedades são compatíveis com o Azure Data Lake Storage Gen1 em configurações de `storeSettings` no coletor de dados baseado em formato:

| Propriedade                 | Descrição                                                  | Obrigatório |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | A propriedade type em `storeSettings` deve ser configurada com **AzureDataLakeStoreWriteSettings**. | Sim      |
| copyBehavior             | Define o comportamento de cópia quando a fonte for de arquivos de um armazenamento de dados baseado em arquivo.<br/><br/>Valores permitidos são:<br/><b>– PreserveHierarchy (padrão)</b>: Preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino.<br/><b>– FlattenHierarchy</b>: Todos os arquivos da pasta de origem estão no primeiro nível da pasta de destino. Os arquivos de destino têm os nomes gerados automaticamente. <br/><b>– MergeFiles</b>: Mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo for especificado, o nome do arquivo mesclado será o nome especificado. Caso contrário, ele será um nome de arquivo gerado automaticamente. | Não       |
| expiryDateTime | Especifica a hora de expiração dos arquivos gravados. A hora é aplicada ao fuso horário de UTC no formato "2020-03-01T08:00:00Z". Por padrão, ela é NULL, o que significa que os arquivos gravados nunca expiram. | Não |
| maxConcurrentConnections |O limite superior de conexões simultâneas estabelecidas com o armazenamento de dados durante a execução da atividade. Especifique um valor somente quando desejar limitar as conexões simultâneas.| Não       |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
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
                    "type": "AzureDataLakeStoreWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```
### <a name="name-range-filter-examples"></a>Exemplos de filtro de intervalo de nomes

Esta seção descreve o comportamento resultante de filtros de intervalo de nome.

| Exemplo de estrutura de origem | Configuração ADF | Resultado |
|:--- |:--- |:--- |
|root<br/>&nbsp;&nbsp;&nbsp;&nbsp;um<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AX<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file2.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;ax.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;b<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;bx.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;cx.csv| **No conjunto de dados:**<br>- Caminho da pasta: `root`<br><br>**Na origem da atividade de cópia:**<br>-Listar após: `a`<br>-Listar antes de: `b`| Em seguida, os seguintes arquivos serão copiados:<br><br>root<br/>&nbsp;&nbsp;&nbsp;&nbsp;AX<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file2.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;ax.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;b<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3.csv |

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
| root<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadados<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **No conjunto de dados:**<br>- Caminho da pasta: `root/FolderA`<br><br>**Na origem da atividade de cópia:**<br>- Caminho da lista de arquivos: `root/Metadata/FileListToCopy.txt` <br><br>O caminho da lista de arquivos aponta para um arquivo de texto no mesmo armazenamento de dados que inclui a lista de arquivos que você deseja copiar, um arquivo por linha, com o caminho relativo do caminho configurado no conjunto de dados. |

### <a name="examples-of-behavior-of-the-copy-operation"></a>Exemplos de comportamento da operação de cópia

Esta seção descreve o comportamento resultante da operação de Cópia para diferentes combinações de valores `recursive` e `copyBehavior`.

| recursiva | copyBehavior | Estrutura de pasta de origem | Destino resultante |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a mesma estrutura da origem:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5. |
| true |flattenHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo5 |
| true |mergeFiles | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos de Arquivo1 + Arquivo2 + Arquivo3 + Arquivo4 + Arquivo5 são mesclados em um arquivo com um nome de arquivo gerado automaticamente. |
| false |preserveHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/><br/>Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não selecionada. |
| false |flattenHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo2<br/><br/>Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não selecionada. |
| false |mergeFiles | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos de Arquivo1 + Arquivo2 são mesclados em um arquivo com um nome de arquivo gerado automaticamente. nome gerado automaticamente para o Arquivo1<br/><br/>Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não selecionada. |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>Preservar ACLs do Data Lake Storage Gen2

>[!TIP]
>Para copiar dados do Azure Data Lake Storage Gen1 no Gen2 em geral, confira [Copiar dados de Azure Data Lake Storage Gen1 no Gen2 com o Azure Data Factory](load-azure-data-lake-storage-gen2-from-gen1.md) para obter instruções detalhadas e melhores práticas.

Se você quiser replicar as ACLs (listas de controle de acesso) junto com os arquivos de dados ao atualizar do Data Lake Storage Gen1 para o Data Lake Storage Gen2, consulte [Preservar ACLs do Data Lake Storage Gen1](copy-activity-preserve-metadata.md#preserve-acls).

## <a name="mapping-data-flow-properties"></a>Propriedades do fluxo de dados de mapeamento

Quando estiver transformando dados no mapeamento de fluxos de dados, você pode ler e gravar arquivos de Azure Data Lake Storage Gen1 nos seguintes formatos:
* [Avro](format-avro.md#mapping-data-flow-properties)
* [Texto delimitado](format-delimited-text.md#mapping-data-flow-properties)
* [Excel](format-excel.md#mapping-data-flow-properties)
* [JSON](format-json.md#mapping-data-flow-properties)
* [Parquet](format-parquet.md#mapping-data-flow-properties)

As configurações específicas de formato estão localizadas na documentação para esse formato. Para obter mais informações, consulte [transformação de origem no mapeamento de fluxo de dados](data-flow-source.md) e [transformação de coletor no fluxo de dados de mapeamento](data-flow-sink.md).

### <a name="source-transformation"></a>Transformação de origem

Na transformação origem, você pode ler um contêiner, uma pasta ou um arquivo individual em Azure Data Lake Storage Gen1. A guia **Opções de origem** permite que você gerencie como os arquivos são lidos. 

![Opções de origem](media/data-flow/sourceOptions1.png "Opções de origem")

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

Na transformação do coletor, você pode gravar em um contêiner ou pasta no Azure Data Lake Storage Gen1. A guia **Configurações** permite que você gerencie como os arquivos são gravados.

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
| type | A propriedade type do conjunto de dados deve ser definida como **AzureDataLakeStoreFile**. |Sim |
| folderPath | Caminho para a pasta no Data Lake Store. Se não especificado, apontará para a raiz. <br/><br/>O filtro curinga tem suporte. Os curingas permitidos são `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único). Use `^` como escape se o nome real da pasta tiver curingas ou esse caractere de escape. <br/><br/>Por exemplo: rootfolder/subfolder/. Veja mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). |Não |
| fileName | Filtro de nome ou curinga para os arquivos em "folderPath" especificado. Se você não especificar um valor para essa propriedade, o conjunto de dados apontará para todos os arquivos na pasta. <br/><br/>Para filtro, os curingas permitidos são `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único).<br/>– Exemplo 1: `"fileName": "*.csv"`<br/>– Exemplo 2: `"fileName": "???20180427.txt"`<br/>Use `^` como escape se seu nome de arquivo real tiver um curinga ou esse caractere de escape.<br/><br/>Quando fileName não for especificado para um conjunto de dados de saída e **preserveHierarchy** não for especificada no coletor de atividade, a atividade de cópia gerará automaticamente o nome do arquivo com o seguinte padrão: "*Data.[GUID da ID de execução da atividade].[GUID se FlattenHierarchy].[formato se configurado].[compactação se configurada]* ", por exemplo: "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". Se você copiar da fonte tabular usando o nome da tabela em vez da consulta, o nome padrão será " *[nome da tabela].[formato].[compactação se configurada]* ", por exemplo, "MyTable.csv". |Não |
| modifiedDatetimeStart | Filtro de arquivos com base no atributo Última Modificação. Os arquivos são selecionados se a hora da última modificação está dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário de UTC no formato "2018-12-01T05:00:00Z". <br/><br/> O desempenho geral da movimentação de dados será afetado ao habilitar essa configuração quando você desejar filtrar grandes quantidades de arquivos. <br/><br/> As propriedades podem ser NULL, o que significa que nenhum filtro de atributo de arquivo é aplicado ao conjunto de dados. Quando `modifiedDatetimeStart` tiver o valor de datetime, mas `modifiedDatetimeEnd` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é maior ou igual ao valor de datetime. Quando `modifiedDatetimeEnd` tiver o valor de datetime, mas `modifiedDatetimeStart` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é menor que o valor de datetime.| Não |
| modifiedDatetimeEnd | Filtro de arquivos com base no atributo Última Modificação. Os arquivos são selecionados se a hora da última modificação está dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário de UTC no formato "2018-12-01T05:00:00Z". <br/><br/> O desempenho geral da movimentação de dados será afetado ao habilitar essa configuração quando você desejar filtrar grandes quantidades de arquivos. <br/><br/> As propriedades podem ser NULL, o que significa que nenhum filtro de atributo de arquivo é aplicado ao conjunto de dados. Quando `modifiedDatetimeStart` tiver o valor de datetime, mas `modifiedDatetimeEnd` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é maior ou igual ao valor de datetime. Quando `modifiedDatetimeEnd` tiver o valor de datetime, mas `modifiedDatetimeStart` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é menor que o valor de datetime.| Não |
| format | Se você quiser copiar arquivos no estado em que se encontram entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas definições de conjunto de dados de entrada e de saída.<br/><br/>Se você quer analisar ou gerar arquivos com um formato específico, os seguintes tipos de formato de arquivo são compatíveis: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Defina a propriedade **type** sob **format** para um desses valores. Para saber mais, veja as seções [Formato de texto](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Formato JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Formato Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Formato Orc](supported-file-formats-and-compression-codecs-legacy.md#orc-format) e [Formato Parquet](supported-file-formats-and-compression-codecs-legacy.md#parquet-format). |Não (somente para o cenário de cópia binária) |
| compactação | Especifique o tipo e o nível de compactação para os dados. Para obter mais informações, consulte [Formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Os tipos com suporte são: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>Os níveis de suporte são **Ideal** e **Mais rápido**. |Não |

>[!TIP]
>Para copiar todos os arquivos em uma pasta, especifique **folderPath** somente.<br>Para copiar um único arquivo com um nome em particular, especifique **folderPath** com uma parte da pasta e **fileName** com um nome de arquivo.<br>Para copiar um subconjunto de arquivos em uma pasta, especifique **folderPath** com uma parte da pasta e **fileName** com um filtro curinga. 

**Exemplo:**

```json
{
    "name": "ADLSDataset",
    "properties": {
        "type": "AzureDataLakeStoreFile",
        "linkedServiceName":{
            "referenceName": "<ADLS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "datalake/myfolder/",
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
| type | A propriedade `type` da fonte da atividade de cópia deve ser definida como **AzureDataLakeStoreSource**. |Sim |
| recursiva | Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. Quando `recursive` é definido como true e o coletor é um repositório baseado em arquivo, uma pasta vazia ou subpasta não é copiada ou criada no coletor. Os valores permitidos são **true** (padrão) e **false**. | Não |
| maxConcurrentConnections |O limite superior de conexões simultâneas estabelecidas com o armazenamento de dados durante a execução da atividade. Especifique um valor somente quando desejar limitar as conexões simultâneas.| Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen1 input dataset name>",
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
                "type": "AzureDataLakeStoreSource",
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
| type | A propriedade `type` do coletor da atividade de cópia deve ser definida como **AzureDataLakeStoreSink**. |Sim |
| copyBehavior | Define o comportamento de cópia quando a fonte for de arquivos de um armazenamento de dados baseado em arquivo.<br/><br/>Valores permitidos são:<br/><b>– PreserveHierarchy (padrão)</b>: Preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino.<br/><b>– FlattenHierarchy</b>: Todos os arquivos da pasta de origem estão no primeiro nível da pasta de destino. Os arquivos de destino têm os nomes gerados automaticamente. <br/><b>– MergeFiles</b>: Mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo for especificado, o nome do arquivo mesclado será o nome especificado. Caso contrário, o nome do arquivo será gerado automaticamente. | Não |
| maxConcurrentConnections |O limite superior de conexões simultâneas estabelecidas com o armazenamento de dados durante a execução da atividade. Especifique um valor somente quando desejar limitar as conexões simultâneas.| Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen1 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataLakeStoreSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>Próximas etapas

Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).