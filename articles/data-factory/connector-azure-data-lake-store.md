---
title: Copiar dados para ou de Gen1 de armazenamento do Azure Data Lake usando o Data Factory | Microsoft Docs
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
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: df88c3e2e07165182c917eaf30a5f37451fbd073
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509589"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>Copiar dados para ou de Gen1 de armazenamento do Azure Data Lake usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do Azure Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-azure-datalake-connector.md)
> * [Versão atual](connector-azure-data-lake-store.md)

Este artigo descreve como copiar dados para e do Azure Data Lake armazenamento Gen1. Para saber mais sobre o Azure Data Factory, leia as [artigo introdutório](introduction.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Esse conector Gen1 de armazenamento do Azure Data Lake é compatível com as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [suporte para a matriz de origem ou coletor](copy-activity-overview.md)
- [Mapeamento de fluxo de dados](concepts-data-flow-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)

Especificamente, com esse conector, que você pode:

- Copiar arquivos usando um dos seguintes métodos de autenticação: identidades de entidade de segurança ou gerenciadas para recursos do Azure de serviço.
- Copiar arquivos como estão ou analisar ou gerar arquivos com o [suporte para formatos de arquivo e codecs de compactação](supported-file-formats-and-compression-codecs.md).

> [!IMPORTANT]
> Se você copiar dados usando o tempo de execução de integração auto-hospedado, configure o firewall corporativo para permitir o tráfego de saída `<ADLS account name>.azuredatalakestore.net` e `login.microsoftonline.com/<tenant>/oauth2/token` na porta 443. O último é o Serviço de Token de Segurança do Azure com que o tempo de execução de integração precisa se comunicar para obter o token de acesso.

## <a name="get-started"></a>Introdução

> [!TIP]
> Para obter uma explicação de como usar o conector do Azure Data Lake Store, consulte [carregar dados no Azure Data Lake Store](load-azure-data-lake-store.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem informações sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao Azure Data Lake Store.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte no serviço vinculado do Azure Data Lake Store:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade `type` deve ser definida como: **AzureDataLakeStore**. | Sim |
| dataLakeStoreUri | Informações sobre a conta do Azure Data Lake Store. Essas informações usam um dos seguintes formatos: `https://[accountname].azuredatalakestore.net/webhdfs/v1` ou `adl://[accountname].azuredatalakestore.net/`. | Sim |
| subscriptionId | A ID de assinatura do Azure à qual a conta do Data Lake Store pertence. | Obrigatório para coletor |
| resourceGroupName | O nome do grupo de recursos do Azure ao qual a conta do Data Lake Store pertence. | Obrigatório para coletor |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o tempo de execução de integração do Azure ou um tempo de execução de integração auto-hospedado se o armazenamento de dados está localizado em uma rede privada. Se essa propriedade não for especificada, o tempo de execução de integração do Azure padrão será usado. |Não |

### <a name="use-service-principal-authentication"></a>Usar a autenticação de entidade de serviço

Para usar a autenticação de entidade de serviço, registre uma entidade de aplicativo no Azure Active Directory e conceda a ela o acesso ao Data Lake Store. Para encontrar as etapas detalhadas, consulte [Autenticação de serviço a serviço](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Anote os seguintes valores, que são usados para definir o serviço vinculado:

- ID do aplicativo
- Chave do aplicativo
- ID do locatário

>[!IMPORTANT]
> Conceda a permissão apropriada entidade de serviço no Data Lake Store:
>- **Como origem**: Em **Data Explorer** > **Acesso**, dê pelo menos uma permissão de **Leitura + Execução** para listar e copiar os arquivos em pastas e subpastas. Outra opção é conceder permissão de **Leitura** para copiar um único arquivo. Você pode optar por adicionar a **Esta pasta e todos os filhos** para recursivo e adicionar como **uma permissão de acesso e uma entrada de permissão padrão**. Não há nenhum requisito de controle de acesso de nível de conta (IAM).
>- **Como coletor**: Em **Data Explorer** > **Acesso**, dê pelo menos uma permissão **Gravação + Execução** para criar itens filho na pasta. Você pode optar por adicionar a **Esta pasta e todos os filhos** para recursivo e adicionar como **uma permissão de acesso e uma entrada de permissão padrão**. Se você usar um tempo de execução de integração do Azure para copiar (origem e coletor estão na nuvem), no IAM, conceda pelo menos o **leitor** função para permitir ao Data Factory detectar a região do Data Lake Store. Se você quiser evitar essa função IAM, escolha explicitamente [criar um Azure Integration Runtime](create-azure-integration-runtime.md#create-azure-ir) com o local do Data Lake Store. Por exemplo, se seu Store lago de dados estiver na Europa Ocidental, criar um tempo de execução de integração do Azure com o local definido para "Europa Ocidental". Associá-los no serviço do Data Lake Store vinculado, conforme mostrado no exemplo a seguir.

>[!NOTE]
>Para listar as pastas começando na raiz, você deve definir a permissão da entidade de serviço que está sendo concedida **no nível raiz com a permissão "Executar"** . Isso é verdadeiro quando você usa o:
>- **A ferramenta Copiar dados** para pipeline de cópia do autor.
>- **Interface do Usuário do Data Factory** para testar a conexão e navegar por pastas durante a criação.
>Se você tiver dúvidas sobre como conceder permissão no nível raiz, durante a criação, ignore o teste conexão e um caminho de paraent com permissão concedida, em seguida, optar por navegar a partir de entrada que especificou o caminho. Copie atividade funciona como a entidade de serviço é concedida com a permissão adequada os arquivos a serem copiados.

Há suporte para as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| servicePrincipalId | Especifique a ID do cliente do aplicativo. | Sim |
| servicePrincipalKey | Especifique a chave do aplicativo. Marque este campo como uma `SecureString` para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| tenant | Especifique as informações de locatário, como nome de domínio ou ID, em que reside o aplicativo de locatário. É possível recuperá-las focalizando o mouse no canto superior direito do Portal do Azure. | Sim |

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
>- **Como origem**: Em **Data Explorer** > **Acesso**, dê pelo menos uma permissão de **Leitura + Execução** para listar e copiar os arquivos em pastas e subpastas. Outra opção é conceder permissão de **Leitura** para copiar um único arquivo. Você pode optar por adicionar a **Esta pasta e todos os filhos** para recursivo e adicionar como **uma permissão de acesso e uma entrada de permissão padrão**. Não há nenhum requisito de controle de acesso de nível de conta (IAM).
>- **Como coletor**: Em **Data Explorer** > **Acesso**, dê pelo menos uma permissão **Gravação + Execução** para criar itens filho na pasta. Você pode optar por adicionar a **Esta pasta e todos os filhos** para recursivo e adicionar como **uma permissão de acesso e uma entrada de permissão padrão**. Se você usar um tempo de execução de integração do Azure para copiar (origem e coletor estão na nuvem), no IAM, conceda pelo menos o **leitor** função para permitir ao Data Factory detectar a região do Data Lake Store. Se você quiser evitar essa função IAM, escolha explicitamente [criar um Azure Integration Runtime](create-azure-integration-runtime.md#create-azure-ir) com o local do Data Lake Store. Associá-los no serviço do Data Lake Store vinculado, conforme mostrado no exemplo a seguir.

>[!NOTE]
>A lista de pastas começando na raiz, você deve definir a permissão de identidade gerenciada que está sendo concedida a **no nível raiz com a permissão "Execute"** . Isso é verdadeiro quando você usa o:
>- **A ferramenta Copiar dados** para pipeline de cópia do autor.
>- **Interface do Usuário do Data Factory** para testar a conexão e navegar por pastas durante a criação.
>Se você tiver dúvidas sobre como conceder permissão no nível raiz, durante a criação, ignore a conexão e teste um caminho pai com permissão concedida, em seguida, optar por navegar a partir de entrada que especificou o caminho. Copie atividade funciona como a entidade de serviço é concedida com a permissão adequada os arquivos a serem copiados.

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

- Para o parquet e o formato de texto delimitado, consulte o [conjunto de dados de formato de Parquet e texto delimitado](#parquet-and-delimited-text-format-dataset) seção.
- Para outros formatos, como ORC, Avro, JSON ou formato binário, consulte o [outro conjunto de dados do formato](#other-format-dataset) seção.

### <a name="parquet-and-delimited-text-format-dataset"></a>Conjunto de dados de formato de parquet e texto delimitado

Para copiar dados para e do Azure Data Lake Store Gen1 parquet ou o formato de texto delimitado, consulte o [formato Parquet](format-parquet.md) e [formato de texto delimitado](format-delimited-text.md) artigos sobre o conjunto de dados com base no formato e configurações com suporte. As propriedades a seguir têm suporte para o Azure Data Lake Store Gen1 sob `location` configurações no conjunto de dados com base no formato:

| Propriedade   | DESCRIÇÃO                                                  | Obrigatório |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | A propriedade type sob `location` no conjunto de dados deve ser definida como **AzureDataLakeStoreLocation**. | Sim      |
| folderPath | O caminho para uma pasta. Se você quiser usar um caractere curinga para pastas de filtro, ignore essa configuração e especificá-lo nas configurações de fonte da atividade. | Não       |
| fileName   | Nome do arquivo em determinado folderPath. Se você quiser usar um caractere curinga para filtrar arquivos, ignore essa configuração e especificá-lo nas configurações de fonte da atividade. | Não       |

> [!NOTE]
>
> O **AzureDataLakeStoreFile** tipo de conjunto de dados com formato parquet ou texto mencionado na seção a seguir ainda tem suporte como está para cópia, pesquisa e atividade GetMetadata para compatibilidade com versões anteriores. Mas ele não funciona com o recurso de fluxo de dados de mapeamento. É recomendável que você use esse novo modelo no futuro. A criação da interface do usuário do Data Factory gera esses novos tipos.

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

### <a name="other-format-dataset"></a>Outro conjunto de dados de formato

Para copiar dados para e do Azure Data Lake Store Gen1 em ORC, Avro, JSON ou formato binário, há suporte para as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como **AzureDataLakeStoreFile**. |Sim |
| folderPath | Caminho para a pasta no Data Lake Store. Se não especificado, apontará para a raiz. <br/><br/>Há suporte para o filtro curinga. Curingas permitidos são `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou um único caractere). Use `^` para escapar se seu nome real da pasta tem um curinga ou esse caractere de escape dentro. <br/><br/>Por exemplo: rootfolder/subfolder /. Veja mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). |Não |
| fileName | Filtro de nome ou curinga para os arquivos sob o "folderPath" especificado. Se você não especificar um valor para essa propriedade, o conjunto de dados apontará para todos os arquivos na pasta. <br/><br/>Para filtro, os curingas permitidos são `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou um único caractere).<br/>– Exemplo 1: `"fileName": "*.csv"`<br/>– Exemplo 2: `"fileName": "???20180427.txt"`<br/>Use `^` para escapar se seu nome de arquivo real tem um curinga ou esse caractere de escape dentro.<br/><br/>Quando fileName não for especificado para um conjunto de dados de saída e **preserveHierarchy** não for especificada no coletor de atividade, a atividade de cópia gerará automaticamente o nome do arquivo com o seguinte padrão: "*Dados. [GUID da ID de execução de atividade]. [GUID se FlattenHierarchy]. [formato se configurado]. [compactação se configurado]* ", por exemplo,"Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". Se você copiar de uma fonte de tabela usando um nome de tabela em vez de uma consulta, o nome padrão é " *[nome da tabela]. [ Format]. [compactação se configurado]* ", por exemplo,"MyTable.csv". |Não |
| modifiedDatetimeStart | Filtro de arquivos com base no atributo da última modificação. Os arquivos estão selecionados se sua hora da última modificação está dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário UTC no formato de "2018-12-01T05:00:00Z". <br/><br/> O desempenho geral da movimentação de dados é afetado ao habilitar essa configuração quando você deseja que o filtro de arquivo com grandes quantidades de arquivos. <br/><br/> As propriedades podem ser NULL, o que significa que nenhum filtro de atributo de arquivo é aplicado ao conjunto de dados. Quando `modifiedDatetimeStart` tem um valor de data e hora, mas `modifiedDatetimeEnd` for NULL, isso significa que os arquivos cujo último atributo modificado é maior que ou igual ao valor de data e hora são selecionados. Quando `modifiedDatetimeEnd` tem um valor de data e hora, mas `modifiedDatetimeStart` for NULL, isso significa que os arquivos cujo último atributo modificado é menor que o valor de data e hora estão selecionados.| Não |
| modifiedDatetimeEnd | Filtro de arquivos com base no atributo da última modificação. Os arquivos estão selecionados se sua hora da última modificação está dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário UTC no formato de "2018-12-01T05:00:00Z". <br/><br/> O desempenho geral da movimentação de dados é afetado ao habilitar essa configuração quando você deseja que o filtro de arquivo com grandes quantidades de arquivos. <br/><br/> As propriedades podem ser NULL, o que significa que nenhum filtro de atributo de arquivo é aplicado ao conjunto de dados. Quando `modifiedDatetimeStart` tem um valor de data e hora, mas `modifiedDatetimeEnd` for NULL, isso significa que os arquivos cujo último atributo modificado é maior que ou igual ao valor de data e hora são selecionados. Quando `modifiedDatetimeEnd` tem um valor de data e hora, mas `modifiedDatetimeStart` for NULL, isso significa que os arquivos cujo último atributo modificado é menor que o valor de data e hora estão selecionados.| Não |
| format | Se você quiser copiar arquivos como estão entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas duas definições de conjunto de dados de entrada e saída.<br/><br/>Se você quer analisar ou gerar arquivos com um formato específico, os seguintes tipos de formato de arquivo são compatíveis: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Defina a propriedade **type** sob **format** para um desses valores. Para saber mais, veja as seções [Formato de texto](supported-file-formats-and-compression-codecs.md#text-format), [Formato JSON](supported-file-formats-and-compression-codecs.md#json-format), [Formato Avro](supported-file-formats-and-compression-codecs.md#avro-format), [Formato Orc](supported-file-formats-and-compression-codecs.md#orc-format) e [Formato Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Não (somente para o cenário de cópia binária) |
| compression | Especifique o tipo e o nível de compactação para os dados. Para obter mais informações, consulte [Formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Os tipos com suporte são: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>Os níveis de suporte são **Ideal** e **Mais rápido**. |Não |


>[!TIP]
>Para copiar todos os arquivos em uma pasta, especifique **folderPath** somente.<br>Para copiar um único arquivo com um nome específico, especifique **folderPath** com uma parte da pasta e **fileName** com um nome de arquivo.<br>Para copiar um subconjunto de arquivos em uma pasta, especifique **folderPath** com uma parte da pasta e **fileName** com um filtro curinga. 

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

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir as atividades, veja [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela origem e pelo coletor do Azure Data Lake Store.

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store como fonte

- Para copiar do parquet ou o formato de texto delimitado, consulte o [Parquet e a origem do formato de texto delimitado](#parquet-and-delimited-text-format-source) seção.
- Para copiar dos outros formatos como ORC, Avro, JSON ou formato binário, consulte o [outra fonte de formato](#other-format-source) seção.

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet e a origem do formato de texto delimitado

Para copiar dados do Azure Data Lake Store Gen1 parquet ou o formato de texto delimitado, consulte o [formato Parquet](format-parquet.md) e [formato de texto delimitado](format-delimited-text.md) artigos sobre a fonte da atividade de cópia baseada em formato e configurações com suporte. As propriedades a seguir têm suporte para o Azure Data Lake Store Gen1 sob `storeSettings` configurações na fonte de cópia com base no formato:

| Propriedade                 | DESCRIÇÃO                                                  | Obrigatório                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | A propriedade type sob `storeSettings` deve ser definida como **AzureDataLakeStoreReadSetting**. | Sim                                           |
| recursive                | Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. Quando o recursivo estiver definido como true e o coletor é um armazenamento baseado em arquivo, uma pasta vazia ou subpasta não é copiada ou criada no coletor. Os valores permitidos são **true** (padrão) e **false**. | Não                                            |
| wildcardFolderPath       | O caminho da pasta com caracteres curinga para filtrar as pastas de origem. <br>Curingas permitidos são `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou um único caractere). Use `^` para escapar se seu nome real da pasta tem um curinga ou esse caractere de escape dentro. <br>Veja mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). | Não                                            |
| wildcardFileName         | O nome de arquivo com caracteres curinga em determinado folderPath/wildcardFolderPath para filtrar arquivos de origem. <br>Curingas permitidos são `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou um único caractere). Use `^` para escapar se seu nome real da pasta tem um curinga ou esse caractere de escape dentro. Veja mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). | Sim se `fileName` não for especificado no conjunto de dados |
| modifiedDatetimeStart    | Filtro de arquivos com base no atributo da última modificação. Os arquivos estão selecionados se sua hora da última modificação está dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário UTC no formato de "2018-12-01T05:00:00Z". <br> As propriedades podem ser NULL, o que significa que nenhum filtro de atributo de arquivo é aplicado ao conjunto de dados. Quando `modifiedDatetimeStart` tem um valor de data e hora, mas `modifiedDatetimeEnd` for NULL, isso significa que os arquivos cujo último atributo modificado é maior que ou igual ao valor de data e hora são selecionados. Quando `modifiedDatetimeEnd` tem um valor de data e hora, mas `modifiedDatetimeStart` for NULL, isso significa que os arquivos cujo último atributo modificado é menor que o valor de data e hora estão selecionados. | Não                                            |
| modifiedDatetimeEnd      | Mesmo que acima.                                               | Não                                            |
| maxConcurrentConnections | O número de conexões para se conectar ao repositório de armazenamento simultaneamente. Especifique somente quando você quiser limitar a conexão simultâneo ao armazenamento de dados. | Não                                            |

> [!NOTE]
> Para o parquet ou o formato de texto delimitado, o **AzureDataLakeStoreSource** origem de atividade de cópia de tipo mencionada na seção a seguir ainda tem suporte, conforme é para compatibilidade com versões anteriores. É recomendável que você use esse novo modelo no futuro. A criação da interface do usuário do Data Factory gera esses novos tipos.

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
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureDataLakeStoreReadSetting",
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

Para copiar dados do Azure Data Lake Store Gen1 em ORC, Avro, JSON ou formato binário, as propriedades a seguir têm suporte na atividade de cópia **origem** seção:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | O `type` propriedade da fonte da atividade de cópia deve ser definida como **AzureDataLakeStoreSource**. |Sim |
| recursive | Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. Quando `recursive` é definido como true e o coletor é um armazenamento baseado em arquivo, uma pasta vazia ou subpasta não é copiada ou criada no coletor. Os valores permitidos são **true** (padrão) e **false**. | Não |
| maxConcurrentConnections | O número de conexões para conectar-se ao armazenamento de dados simultaneamente. Especifique somente quando você quiser limitar a conexão simultâneo ao armazenamento de dados. | Não |

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

### <a name="azure-data-lake-store-as-sink"></a>Azure Data Lake Store como coletor

- Para copiar para parquet e formato de texto delimitado, consulte o [Parquet e o coletor de formato de texto delimitado](#parquet-and-delimited-text-format-sink) seção.
- Para copiar para outros formatos, como ORC, Avro, JSON ou formato binário, consulte o [outro coletor do formato](#other-format-sink) seção.

#### <a name="parquet-and-delimited-text-format-sink"></a>Parquet e o coletor de formato de texto delimitado

Para copiar dados para o Azure Data Lake Store Gen1 parquet ou o formato de texto delimitado, consulte o [formato Parquet](format-parquet.md) e [formato de texto delimitado](format-delimited-text.md) artigos sobre o coletor de atividade de cópia baseada em formato e configurações com suporte. As propriedades a seguir têm suporte para o Azure Data Lake Store Gen1 sob `storeSettings` configurações no coletor de cópia com base no formato:

| Propriedade                 | DESCRIÇÃO                                                  | Obrigatório |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | A propriedade type sob `storeSettings` deve ser definida como **AzureDataLakeStoreWriteSetting**. | Sim      |
| copyBehavior             | Define o comportamento de cópia quando a fonte for de arquivos de um armazenamento de dados baseado em arquivo.<br/><br/>Valores permitidos são:<br/><b>– PreserveHierarchy (padrão)</b>: Preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino.<br/><b>– FlattenHierarchy</b>: Todos os arquivos da pasta de origem estão no primeiro nível da pasta de destino. Os arquivos de destino têm os nomes gerados automaticamente. <br/><b>– MergeFiles</b>: Mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo for especificado, o nome do arquivo mesclado será o nome especificado. Caso contrário, ele será um nome de arquivo gerado automaticamente. | Não       |
| maxConcurrentConnections | O número de conexões para conectar-se ao armazenamento de dados simultaneamente. Especifique somente quando você quiser limitar a conexão simultâneo ao armazenamento de dados. | Não       |

> [!NOTE]
> Para o parquet ou o formato de texto delimitado, o **AzureDataLakeStoreSink** coletor de atividade de cópia do tipo mencionado na seção a seguir ainda tem suporte, conforme é para compatibilidade com versões anteriores. É recomendável que você use esse novo modelo no futuro. A criação da interface do usuário do Data Factory gera esses novos tipos.

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
                    "type": "AzureDataLakeStoreWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="other-format-sink"></a>Outro coletor do formato

Para copiar dados para o Azure Data Lake Store Gen1 em ORC, Avro, JSON ou formato binário, as propriedades a seguir têm suporte nas **coletor** seção:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | O `type` propriedade do coletor de atividade de cópia deve ser definida como **AzureDataLakeStoreSink**. |Sim |
| copyBehavior | Define o comportamento de cópia quando a fonte for de arquivos de um armazenamento de dados baseado em arquivo.<br/><br/>Valores permitidos são:<br/><b>– PreserveHierarchy (padrão)</b>: Preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino.<br/><b>– FlattenHierarchy</b>: Todos os arquivos da pasta de origem estão no primeiro nível da pasta de destino. Os arquivos de destino têm os nomes gerados automaticamente. <br/><b>– MergeFiles</b>: Mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo for especificado, o nome do arquivo mesclado será o nome especificado. Caso contrário, o nome do arquivo será gerado automaticamente. | Não |
| maxConcurrentConnections | O número de conexões para conectar-se ao armazenamento de dados simultaneamente. Especifique somente quando você quiser limitar a conexão simultâneo ao armazenamento de dados. | Não |

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

### <a name="folder-and-file-filter-examples"></a>Exemplos de filtro de pasta e arquivo

Esta seção descreve o comportamento resultante do caminho da pasta e do nome de arquivo com filtros curinga.

| folderPath | fileName | recursive | Estrutura da pasta de origem e resultado do filtro (os arquivos em **negrito** são recuperados)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Vazio, use o padrão) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5.csv<br/>OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| `Folder*` | (Vazio, use o padrão) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5.csv<br/>OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |

### <a name="examples-of-behavior-of-the-copy-operation"></a>Exemplos de comportamento da operação de cópia

Esta seção descreve o comportamento resultante da operação de Cópia para diferentes combinações de valores `recursive` e `copyBehavior`.

| recursive | copyBehavior | Estrutura de pasta de origem | Destino resultante |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a mesma estrutura da origem:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5. |
| true |flattenHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo5 |
| true |mergeFiles | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1 + arquivo2 + arquivo3 + arquivo4 + arquivo5 arquivo5 são mesclados em um arquivo, com um nome de arquivo gerado automaticamente. |
| false |preserveHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/><br/>Subpasta1 com arquivo3, arquivo4 e arquivo5 não são selecionadas. |
| false |flattenHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo2<br/><br/>Subpasta1 com arquivo3, arquivo4 e arquivo5 não são selecionadas. |
| false |mergeFiles | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Conteúdos de arquivo1 + arquivo2 é mesclados em um arquivo com nome de arquivo gerado automaticamente. nome gerado automaticamente para o Arquivo1<br/><br/>Subpasta1 com arquivo3, arquivo4 e arquivo5 não são selecionadas. |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>Preservar as ACLs para o Data Lake Storage Gen2

Se você quiser replicar o controle de acesso ACLs (listas) junto com os arquivos de dados quando você atualiza do Data Lake armazenamento Gen1 para Gen2 de armazenamento do Data Lake, consulte [preservar ACLs do Data Lake armazenamento Gen1](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1).

## <a name="mapping-data-flow-properties"></a>Mapeamento de propriedades de fluxo de dados

Saiba mais sobre [transformação de origem](data-flow-source.md) e [transformação do coletor](data-flow-sink.md) no recurso de fluxo de dados de mapeamento.

## <a name="next-steps"></a>Próximas etapas

Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).
