---
title: Copiar dados de ou para o Azure Data Lake Storage Gen1 usando o Data Factory | Microsoft Docs
description: Saiba como copiar dados de armazenamentos de dados de origem com suporte para o Azure Data Lake Store ou do Data Lake Store para armazenamentos de coletor com suporte usando o Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: jingwang
ms.openlocfilehash: 433824c4e375cf1ce7d7a6fe16730044628ccab1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61001608"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-by-using-azure-data-factory"></a>Copiar dados de/para o Azure Data Lake Storage Gen1 usando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-azure-datalake-connector.md)
> * [Versão atual](connector-azure-data-lake-store.md)

Este artigo descreve como usar a Atividade de Cópia no Azure Data Factory para copiar dados de/para o Azure Data Lake Storage Gen1 (conhecido anteriormente como Azure Data Lake Store). Ele se baseia na [Visão geral da Atividade de Cópia](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados de qualquer armazenamento de dados de origem com suporte para o Azure Data Lake Store ou copiar dados do Azure Data Lake Store para qualquer armazenamento de dados de coletor com suporte. Veja a tabela [armazenamentos de dados compatíveis](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector do Azure Data Lake Store dá suporte à:

- Copiar arquivos usando um dos seguintes métodos de autenticação: **entidade de serviço** ou **identidades gerenciadas para recursos do Azure**.
- Cópia de arquivos no estado em que se encontram ou analisar ou gerar arquivos com os [formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md).

> [!IMPORTANT]
> Se você copiar dados usando o tempo de execução da integração auto-hospedada, configure o firewall corporativo para permitir o tráfego de saída para `<ADLS account name>.azuredatalakestore.net` e `login.microsoftonline.com/<tenant>/oauth2/token` na porta 443. O último é o Serviço de Token de Segurança do Azure com que o tempo de execução de integração precisa se comunicar para obter o token de acesso.

## <a name="get-started"></a>Introdução

> [!TIP]
> Para obter um passo a passo de como usar o conector do Azure Data Lake Store, veja [Carregar dados no Azure Data Lake Store](load-azure-data-lake-store.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções que se seguem fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao Azure Data Lake Store.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte no serviço vinculado do Azure Data Lake Store:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade `type` deve ser definida como: **AzureDataLakeStore**. | Sim |
| dataLakeStoreUri | Informações sobre a conta do Azure Data Lake Store. Essas informações usam um dos seguintes formatos: `https://[accountname].azuredatalakestore.net/webhdfs/v1` ou `adl://[accountname].azuredatalakestore.net/`. | Sim |
| subscriptionId | A ID de assinatura do Azure à qual a conta do Data Lake Store pertence. | Obrigatório para coletor |
| resourceGroupName | O nome do grupo de recursos do Azure ao qual a conta do Data Lake Store pertence. | Obrigatório para coletor |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Azure Integration Runtime ou o tempo de execução da integração auto-hospedada (se o armazenamento de dados está localizado em uma rede privada). Se essa propriedade não estiver especificada, ela usará o Azure Integration Runtime padrão. |Não  |

### <a name="use-service-principal-authentication"></a>Usar a autenticação de entidade de serviço

Para usar a autenticação de entidade de serviço, registre uma entidade de aplicativo no Azure Active Directory e conceda a ela o acesso ao Data Lake Store. Para encontrar as etapas detalhadas, consulte [Autenticação de serviço a serviço](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Anote os seguintes valores, que são usados para definir o serviço vinculado:

- ID do aplicativo
- Chave do aplicativo
- ID do locatário

>[!IMPORTANT]
> Verifique se você concedeu a permissão apropriada à entidade de serviço no Data Lake Store:
>- **Como origem**: Em **Data Explorer** > **Acesso**, dê pelo menos uma permissão de **Leitura + Execução** para listar e copiar os arquivos em pastas e subpastas. Outra opção é conceder permissão de **Leitura** para copiar um único arquivo. Você pode optar por adicionar a **Esta pasta e todos os filhos** para recursivo e adicionar como **uma permissão de acesso e uma entrada de permissão padrão**. Não há requisito de controle de acesso no nível da conta (IAM).
>- **Como coletor**: Em **Data Explorer** > **Acesso**, dê pelo menos uma permissão **Gravação + Execução** para criar itens filho na pasta. Você pode optar por adicionar a **Esta pasta e todos os filhos** para recursivo e adicionar como **uma permissão de acesso e uma entrada de permissão padrão**. Se você usar o Azure Integration Runtime para copiar (origem e coletor estão na nuvem), no IAM, dê pelo menos a função de **Leitor** para permitir ao Data Factory detectar a região do Data Lake Store. Se você quiser evitar essa função IAM, escolha explicitamente [criar um Azure Integration Runtime](create-azure-integration-runtime.md#create-azure-ir) com o local do Data Lake Store. Associe-os no serviço vinculado do Data Lake Store como o exemplo a seguir.

>[!NOTE]
>Para listar as pastas começando na raiz, você deve definir a permissão da entidade de serviço que está sendo concedida **no nível raiz com a permissão "Executar"**. Isso é verdadeiro quando você usa o:
>- **Ferramenta Copiar Dados** para criar o pipeline de cópia.
>- **Interface do Usuário do Data Factory** para testar a conexão e navegar por pastas durante a criação.
>Se você tiver interesse na concessão de permissão no nível raiz, você pode ignorar manualmente conexão de teste e o caminho de entrada durante a criação. Atividade de cópia ainda funcionará desde que a entidade de serviço é concedida com a permissão adequada os arquivos a serem copiados.

Há suporte para as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| servicePrincipalId | Especifique a ID do cliente do aplicativo. | Sim |
| servicePrincipalKey | Especifique a chave do aplicativo. Marque este campo como uma `SecureString` para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| tenant | Especifique as informações de locatário (domínio nome ou ID do Locatário) em que o aplicativo reside. É possível recuperá-las focalizando o mouse no canto superior direito do Portal do Azure. | Sim |

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

### <a name="managed-identity"></a> Usar identidades gerenciadas para autenticação de recursos do Azure

Um data factory pode ser associado a uma [identidade gerenciada para recursos do Azure](data-factory-service-identity.md), que representa esse data factory específico. Você pode usar essa identidade de gerenciado diretamente para a autenticação do Data Lake Store, semelhante a usar sua própria entidade de serviço. Ele permite que este alocador designado acesse e copie dados de ou para o seu Data Lake Store.

Para usar identidades gerenciadas para autenticação de recursos do Azure:

1. [Recuperar as informações de identidade do data factory gerenciado](data-factory-service-identity.md#retrieve-managed-identity) copiando o valor da "Serviço de identidade ID do aplicativo" gerado junto com seu alocador.
2. Conceda o acesso de identidade gerenciada para Store do Data Lake, da mesma maneira que faria para a entidade de serviço, estas notas a seguir.

>[!IMPORTANT]
> Verifique se que você conceder a data factory gerenciado permissão apropriada à identidade no Data Lake Store:
>- **Como origem**: Em **Data Explorer** > **Acesso**, dê pelo menos uma permissão de **Leitura + Execução** para listar e copiar os arquivos em pastas e subpastas. Outra opção é conceder permissão de **Leitura** para copiar um único arquivo. Você pode optar por adicionar a **Esta pasta e todos os filhos** para recursivo e adicionar como **uma permissão de acesso e uma entrada de permissão padrão**. Não há requisito de controle de acesso no nível da conta (IAM).
>- **Como coletor**: Em **Data Explorer** > **Acesso**, dê pelo menos uma permissão **Gravação + Execução** para criar itens filho na pasta. Você pode optar por adicionar a **Esta pasta e todos os filhos** para recursivo e adicionar como **uma permissão de acesso e uma entrada de permissão padrão**. Se você usar o Azure Integration Runtime para copiar (origem e coletor estão na nuvem), no IAM, dê pelo menos a função de **Leitor** para permitir ao Data Factory detectar a região do Data Lake Store. Se você quiser evitar essa função IAM, escolha explicitamente [criar um Azure Integration Runtime](create-azure-integration-runtime.md#create-azure-ir) com o local do Data Lake Store. Associe-os no serviço vinculado do Data Lake Store como o exemplo a seguir.

>[!NOTE]
>A lista de pastas começando na raiz, você deve definir a permissão de identidade gerenciada que está sendo concedida a **no nível raiz com a permissão "Execute"**. Isso é verdadeiro quando você usa o:
>- **Ferramenta Copiar Dados** para criar o pipeline de cópia.
>- **Interface do Usuário do Data Factory** para testar a conexão e navegar por pastas durante a criação.
>Se você tiver interesse na concessão de permissão no nível raiz, você pode ignorar manualmente conexão de teste e o caminho de entrada durante a criação. Atividade de cópia ainda funcionará desde que a identidade gerenciada é concedida com a permissão adequada os arquivos a serem copiados.

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

Para copiar dados para e do Azure Data Lake Store, defina a propriedade `type` do conjunto de dados como **AzureDataLakeStoreFile**. Há suporte para as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como: **AzureDataLakeStoreFile** |Sim |
| folderPath | Caminho para a pasta no Data Lake Store. Se não especificado, apontará para a raiz. <br/><br/>O filtro curinga é permitido; os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único); use `^` para escape se o nome real da pasta tiver um curinga ou esse caractere interno de escape. <br/><br/>Exemplos: rootfolder/subfolder/; veja mais exemplos em [Exemplos de filtro de pasta e arquivo](#folder-and-file-filter-examples). |Não  |
| fileName | **Filtro de nome ou curinga** para os arquivos em "folderPath" especificado. Se você não especificar um valor para essa propriedade, o conjunto de dados apontará para todos os arquivos na pasta. <br/><br/>Para filtro, os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único).<br/>– Exemplo 1: `"fileName": "*.csv"`<br/>– Exemplo 2: `"fileName": "???20180427.txt"`<br/>Use `^` para se seu nome de arquivo real curinga ou esse caractere de escape dentro de escape.<br/><br/>Quando o fileName não for especificado para um conjunto de dados de saída e a **preserveHierarchy** não for especificada no coletor de atividade, a atividade de cópia gerará automaticamente o nome do arquivo com o seguinte padrão: "*Data.[GUID da ID de execução de atividade].[GUID se FlattenHierarchy].[formato se configurado].[compactação se configurada]*". Por exemplo, "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". Se você copiar da fonte tabular usando o nome da tabela, em vez da consulta, o nome padrão será "*[nome da tabela].[formato].[compactação se configurada]*". Por exemplo, "MyTable.csv". |Não  |
| modifiedDatetimeStart | Filtro de arquivos com base no atributo: Última Modificação. Os arquivos serão selecionados se a hora da última alteração estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário de UTC no formato "2018-12-01T05:00:00Z". <br/><br/> As propriedades podem ser NULL, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o valor de data e hora, mas `modifiedDatetimeEnd` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é maior ou igual ao valor de data e hora.  Quando `modifiedDatetimeEnd` tem o valor de data e hora, mas `modifiedDatetimeStart` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é menor que o valor de data e hora.| Não  |
| modifiedDatetimeEnd | Filtro de arquivos com base no atributo: Última Modificação. Os arquivos serão selecionados se a hora da última alteração estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário de UTC no formato "2018-12-01T05:00:00Z". <br/><br/> As propriedades podem ser NULL, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o valor de data e hora, mas `modifiedDatetimeEnd` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é maior ou igual ao valor de data e hora.  Quando `modifiedDatetimeEnd` tem o valor de data e hora, mas `modifiedDatetimeStart` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é menor que o valor de data e hora.| Não  |
| formato | Se você quiser **copiar arquivos no estado em que se encontram** entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas duas definições de conjunto de dados de entrada e de saída.<br/><br/>Se você quer analisar ou gerar arquivos com um formato específico, os seguintes tipos de formato de arquivo são compatíveis: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Defina a propriedade **type** sob formato como um desses valores. Para saber mais, veja as seções [Formato de texto](supported-file-formats-and-compression-codecs.md#text-format), [Formato Json](supported-file-formats-and-compression-codecs.md#json-format), [Formato Avro](supported-file-formats-and-compression-codecs.md#avro-format), [Formato Orc](supported-file-formats-and-compression-codecs.md#orc-format), e [Formato Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Não (somente para o cenário de cópia binária) |
| compactação | Especifique o tipo e o nível de compactação para os dados. Para obter mais informações, consulte [Formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Tipos compatíveis são: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>Níveis compatíveis são: **Ideal** e **Mais Rápido**. |Não  |


>[!TIP]
>Para copiar todos os arquivos em uma pasta, especifique **folderPath** somente.<br>Para copiar um único arquivo com um nome em particular, especifique **folderPath** com parte da pasta e **fileName** com nome de arquivo.<br>Para copiar um subconjunto de arquivos em uma pasta, especifique **folderPath** com parte da pasta e **fileName** com filtro curinga. 

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

## <a name="copy-activity-properties"></a>Propriedades da Atividade de Cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir as atividades, veja [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela origem e pelo coletor do Azure Data Lake Store.

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store como fonte

Para copiar dados do Data Lake Store, defina o tipo de origem na Atividade de Cópia como **AzureDataLakeStoreSource**. As seguintes propriedades são suportadas na seção **source** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade `type` da origem da Atividade de Cópia deve ser definida como: **AzureDataLakeStoreSource**. |Sim |
| recursiva | Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. Observe que quando `recursive` é definida como true e o coletor é um repositório baseado em arquivo, uma pasta vazia ou subpasta não é copiada ou criada no coletor. Os valores permitidos são: **true** (padrão) e **false**. | Não  |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromADLS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS input dataset name>",
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

### <a name="azure-data-lake-store-as-sink"></a>Azure Data Lake Store como coletor

Para copiar dados para o Data Lake Store, defina o tipo de coletor na Atividade de Cópia como **AzureDataLakeStoreSink**. As propriedades a seguir têm suporte na seção **coletor**:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade `type` do coletor de Atividade de Cópia deve ser definida como: **AzureDataLakeStoreSink**. |Sim |
| copyBehavior | Define o comportamento de cópia quando a fonte for de arquivos de um armazenamento de dados baseado em arquivo.<br/><br/>Valores permitidos são:<br/><b>- PreserveHierarchy (padrão)</b>: preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino.<br/><b>- FlattenHierarchy</b>: todos os arquivos da pasta de origem estão no primeiro nível da pasta de destino. Os arquivos de destino têm nomes gerados automaticamente. <br/><b>- MergeFiles</b>: mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo/blob for especificado, o nome do arquivo mesclado será o nome especificado. Caso contrário, o nome do arquivo será gerado automaticamente. | Não  |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToADLS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS output dataset name>",
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

### <a name="folder-and-file-filter-examples"></a>Exemplos de filtro de pasta e arquivo

Esta seção descreve o comportamento resultante do caminho da pasta e do nome de arquivo com filtros curinga.

| folderPath | fileName | recursiva | Estrutura da pasta de origem e resultado do filtro (os arquivos em **negrito** são recuperados)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (vazio, usar padrão) | falso | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5.csv<br/>OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| `Folder*` | (vazio, usar padrão) | verdadeiro | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| `Folder*` | `*.csv` | falso | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5.csv<br/>OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| `Folder*` | `*.csv` | verdadeiro | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |

### <a name="examples-of-behavior-of-the-copy-operation"></a>Exemplos de comportamento da operação de cópia

Esta seção descreve o comportamento resultante da operação de Cópia para diferentes combinações de valores `recursive` e `copyBehavior`.

| recursiva | copyBehavior | Estrutura de pasta de origem | Destino resultante |
|:--- |:--- |:--- |:--- |
| verdadeiro |preserveHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a mesma estrutura da origem:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5. |
| verdadeiro |flattenHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome gerado automaticamente para o Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo5 |
| verdadeiro |mergeFiles | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos de Arquivo1 + Arquivo2 + Arquivo3 + Arquivo4 + Arquivo5 são mesclados em um arquivo com um nome de arquivo gerado automaticamente. |
| falso |preserveHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/><br/>Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não são selecionados. |
| falso |flattenHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome gerado automaticamente para o Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo2<br/><br/>Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não são selecionados. |
| falso |mergeFiles | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos dos Arquivo1 + Arquivo2 são mesclados em um único arquivo com um nome de arquivo gerado automaticamente. Nome gerado automaticamente para o Arquivo1<br/><br/>Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não são selecionados. |

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela Atividade de Cópia no Azure Data Factory, veja [Armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).
