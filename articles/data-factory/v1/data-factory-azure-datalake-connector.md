---
title: Copiar dados de/para o Azure Data Lake Storage Gen1 | Microsoft Docs
description: Saiba como copiar dados de e para o Data Lake Store usando o Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 25b1ff3c-b2fd-48e5-b759-bb2112122e30
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d8637a2711c0301d9e9f409e169ed04fb3d65783
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839537"
---
# <a name="copy-data-to-and-from-data-lake-storage-gen1-by-using-data-factory"></a>Copiar dados de/para o Data Lake Storage Gen1 usando o Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](data-factory-azure-datalake-connector.md)
> * [Versão 2 (versão atual)](../connector-azure-data-lake-store.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do Azure Data Factory, confira [Conector do Azure Data Lake Storage Gen1 na V2](../connector-azure-data-lake-store.md).

Este artigo explica como usar a atividade de cópia no Azure Data Factory para mover dados de/para o Azure Data Lake Storage Gen1 (conhecido anteriormente como Azure Data Lake Store). Ele se baseia no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md), uma visão geral da movimentação de dados com a Atividade de Cópia.

## <a name="supported-scenarios"></a>Cenários com suporte
Você pode copiar dados **do Azure Data Lake Store** para os seguintes armazenamentos de dados:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Você pode copiar dados dos seguintes armazenamentos de dados para o **Azure Data Lake Store**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Crie uma conta do Data Lake Store antes de criar um pipeline com a Atividade de Cópia. Para obter mais informações, consulte [Introdução ao Azure Data Lake Store](../../data-lake-store/data-lake-store-get-started-portal.md).

## <a name="supported-authentication-types"></a>Tipos de autenticação com suporte
O conector do Data Lake Store dá suporte a esses tipos de autenticação:
* Autenticação de entidade de serviço
* Autenticação de credencial do usuário (OAuth)

É recomendável que você use a autenticação de entidade de serviço, especialmente para uma cópia de dados agendada. O comportamento de expiração do token pode ocorrer com autenticação de credenciais do usuário. Para obter os detalhes de configuração, consulte a seção [Propriedades do serviço vinculado](#linked-service-properties).

## <a name="get-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia que mova dados bidirecionalmente em um Azure Data Lake Store usando diferentes ferramentas/APIs.

A maneira mais fácil de criar um pipeline para copiar dados é usar o **Assistente de Cópia**. Para obter um tutorial sobre como criar um pipeline usando o Assistente de Cópia, consulte [Tutorial: Criar um pipeline usando Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md).

Você também pode usar as ferramentas abaixo para criar um pipeline: **Visual Studio**, **Azure PowerShell**, **modelo do Resource Manager**, **.NET API**, e **API REST**. Confira o [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo sobre a criação de um pipeline com uma atividade de cópia.

Ao usar as ferramentas ou APIs, você executa as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Criar uma **data factory**. Um data factory pode conter um ou mais pipelines.
2. Criar **serviços vinculados** para vincular repositórios de dados de entrada e saída ao seu data factory. Por exemplo, se você está copiando dados de um Armazenamento de Blobs do Azure para um Azure Data Lake Store, crie dois serviços vinculados para vincular sua Conta de Armazenamento do Azure e o Azure Data Lake Store ao data factory. Para propriedades do serviço vinculado que são específicas do Azure Data Lake Store, consulte a seção de [propriedades do serviço vinculado](#linked-service-properties).
2. Criar **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia. No exemplo mencionado na última etapa, você cria um conjunto de dados para especificar o contêiner de blobs e a pasta que contém os dados de entrada. Em seguida, você cria outro conjunto de dados para o caminho de pasta e arquivo no Data Lake Store que contém os dados copiados do Armazenamento de Blobs. Para propriedades do conjunto de dados que são específicas do Azure Data Lake Store, consulte a seção [propriedades do conjunto de dados](#dataset-properties).
3. Criar um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto de dados como uma saída. No exemplo mencionado anteriormente, você usa BlobSource como fonte e AzureDataLakeStoreSink como coletor para a atividade de cópia. De modo similar, se você estiver copiando do Azure Data Lake Storage para o Armazenamento de Blobs do Azure, use AzureDataLakeStoreSource e BlobSink na atividade de cópia. Para propriedades da atividade de cópia que são específicas do Azure Data Lake Store, consulte a seção de [propriedades da atividade de cópia](#copy-activity-properties). Para obter detalhes sobre como usar um armazenamento de dados como uma origem ou um coletor, clique no link na seção anterior para o seu armazenamento de dados.

Ao usar o assistente, as definições de JSON para essas entidades do Data Factory (serviços vinculados, conjuntos de dados e o pipeline) são automaticamente criadas para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades do Data Factory usando o formato JSON. Para obter exemplos com definições de JSON das entidades do Data Factory usadas para copiar dados bidirecionalmente em um Azure Data Lake Store, confira a seção [Exemplos de JSON](#json-examples-for-copying-data-to-and-from-data-lake-store) neste artigo.

As seções que a seguir fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades do Data Factory específicas ao Data Lake Store.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado
Um serviço vinculado vincula um armazenamento de dados a um data factory. Crie um serviço vinculado do tipo **AzureDataLakeStore** para vincular os dados do Data Lake Store ao data factory. A tabela a seguir descreve elementos JSON específicos para serviços vinculados do Data Lake Store. Você pode escolher entre a entidade de serviço e a autenticação de credenciais de usuário.

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| **type** | A propriedade type deve ser definida como **AzureDataLakeStore**. | Sim |
| **dataLakeStoreUri** | Informações sobre a conta do Azure Data Lake Store. Essas informações usam um dos seguintes formatos: `https://[accountname].azuredatalakestore.net/webhdfs/v1` ou `adl://[accountname].azuredatalakestore.net/`. | Sim |
| **subscriptionId** | ID de assinatura do Azure à qual a conta do Data Lake Store pertence. | Obrigatório para coletor |
| **resourceGroupName** | Nome do grupo de recursos do Azure ao qual a conta do Data Lake Store pertence. | Obrigatório para coletor |

### <a name="service-principal-authentication-recommended"></a>Autenticação de entidade de serviço (recomendada)
Para usar a autenticação de entidade de serviço, registre uma entidade de aplicativo no Azure AD (Azure Active Directory) e conceda a ela o acesso ao Data Lake Store. Para encontrar as etapas detalhadas, consulte [Autenticação de serviço a serviço](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Anote os seguintes valores, que são usados para definir o serviço vinculado:
* ID do aplicativo
* Chave do aplicativo
* ID do locatário

> [!IMPORTANT]
> Verifique se você concedeu a permissão apropriada à entidade de serviço no Azure Data Lake Store:
>- **Para usar o Data Lake Store como origem**, conceda pelo menos a permissão de acesso a dados **Leitura + Execução** para listar e copiar o conteúdo de uma pasta ou de **Leitura** para copiar um único arquivo. Nenhum requisito de controle de acesso no nível da conta.
>- **Para usar o Data Lake Store como coletor**, conceda pelo menos a permissão de acesso a dados **Gravação + Execução** para criar itens filho na pasta. E, se você usar o Azure IR para capacitar a cópia (tanto a origem quanto o coletor estão na nuvem), para permitir ao Data Factory detectar a região do Data Lake Store, conceda pelo menos a função **Leitor** no controle de acesso da conta (IAM). Se você quiser evitar essa função IAM, [especifique executionLocation](data-factory-data-movement-activities.md#global) com o local do Data Lake Store na atividade de cópia.
>- Se você **usar o Assistente de Cópia para criar pipelines**, conceda pelo menos a função **Leitor** no IAM (controle de acesso da conta). Além disso, conceda pelo menos a permissão de **Leitura + Execução** para sua raiz do Data Lake Store (“/”) e seus filhos. Caso contrário, você poderá ver a mensagem "As credenciais fornecidas são inválidas".

Use a autenticação de entidade de serviço especificando as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| **servicePrincipalId** | Especifique a ID do cliente do aplicativo. | Sim |
| **servicePrincipalKey** | Especifique a chave do aplicativo. | Sim |
| **tenant** | Especifique as informações de locatário (domínio nome ou ID do Locatário) em que o aplicativo reside. É possível recuperá-las focalizando o mouse no canto superior direito do Portal do Azure. | Sim |

**Exemplo: Autenticação de entidade de serviço**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

### <a name="user-credential-authentication"></a>Autenticação de credenciais de usuário
Como alternativa, você pode usar a autenticação de credenciais do usuário para copiar de ou para o Data Lake Store especificando as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| **authorization** | Clique no botão **Autorizar** no Editor do Data Factory e insira as suas credenciais, que atribuem a URL de autorização gerada automaticamente a essa propriedade. | Sim |
| **sessionId** | A ID de sessão OAuth da sessão de autorização OAuth. Cada ID da sessão é exclusiva e pode ser usada somente uma vez. Essa configuração é gerada automaticamente quando você usa o Editor do Data Factory. | Sim |

> [!IMPORTANT]
> Verifique se você concedeu a permissão apropriada ao usuário no Azure Data Lake Store:
>- **Para usar o Data Lake Store como origem**, conceda pelo menos a permissão de acesso a dados **Leitura + Execução** para listar e copiar o conteúdo de uma pasta ou de **Leitura** para copiar um único arquivo. Nenhum requisito de controle de acesso no nível da conta.
>- **Para usar o Data Lake Store como coletor**, conceda pelo menos a permissão de acesso a dados **Gravação + Execução** para criar itens filho na pasta. E, se você usar o Azure IR para capacitar a cópia (tanto a origem quanto o coletor estão na nuvem), para permitir ao Data Factory detectar a região do Data Lake Store, conceda pelo menos a função **Leitor** no controle de acesso da conta (IAM). Se você quiser evitar essa função IAM, [especifique executionLocation](data-factory-data-movement-activities.md#global) com o local do Data Lake Store na atividade de cópia.
>- Se você **usar o Assistente de Cópia para criar pipelines**, conceda pelo menos a função **Leitor** no IAM (controle de acesso da conta). Além disso, conceda pelo menos a permissão de **Leitura + Execução** para sua raiz do Data Lake Store (“/”) e seus filhos. Caso contrário, você poderá ver a mensagem "As credenciais fornecidas são inválidas".

**Exemplo: Autenticação da credencial do usuário**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

#### <a name="token-expiration"></a>Expiração do token
O código de autorização gerado usando o botão **Autorizar** expira após um determinado período. A mensagem a seguir significa que o token de autenticação expirou:

Erro de operação de credencial: invalid_grant - AADSTS70002: Erro ao validar credenciais. AADSTS70008: A concessão de acesso fornecida expirou ou foi revogada. ID de Rastreamento: d18629e8-af88-43c5-88e3-d8419eb1fca1 ID de Correlação: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Carimbo de data/hora: 2015-12-15 21-09-31Z.

A tabela a seguir mostra os tempos de expiração de diferentes tipos de contas de usuário:

| Tipo de usuário | Expira após |
|:--- |:--- |
| Contas de usuário *não* gerenciadas pelo Azure Active Directory (por exemplo, @hotmail.com ou @live.com) |12 horas |
| Contas de usuários gerenciadas pelo Azure Active Directory |14 dias após a última execução da fatia <br/><br/>90 dias, se uma fatia com base em serviços vinculados do OAuth for executada pelo menos uma vez a cada 14 dias |

Se você alterar sua senha antes do momento de expiração do token, o token expirará imediatamente. Você verá a mensagem mencionada anteriormente nesta seção.

Você pode reautorizar a conta usando o botão **Autorizar** quando o token expirar para reimplantar o serviço vinculado. Você também pode gerar valores para as propriedades **sessionId** e **authorization** programaticamente usando o código a seguir:


```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```
Para encontrar detalhes sobre as classes do Data Factory usadas no código, consulte os tópicos [Classe AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [Classe AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx) e [Classe AuthorizationSessionGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx). Adicione uma referência à versão `2.9.10826.1824` de `Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll` para a classe `WindowsFormsWebAuthenticationDialog` usada no código.

## <a name="troubleshooting-tips"></a>Dicas de solução de problemas

**Sintoma:** Ao copiar dados **no** Azure Data Lake Store, se a atividade de cópia falhar com o seguinte erro:

  ```
  Failed to detect the region for Azure Data Lake account {your account name}. Please make sure that the Resource Group name: {resource group name} and subscription ID: {subscription ID} of this Azure Data Lake Store resource are correct.
  ```

**Causa raiz:** Há 2 motivos possíveis:

1. O `resourceGroupName` e/ou `subscriptionId` especificado no serviço vinculado do Azure Data Lake Store está incorreto;
2. O usuário ou a entidade de serviço não tem a permissão necessária.

**Resolução:**

1. Verifique se o `subscriptionId` e `resourceGroupName` especificado no serviço vinculado `typeProperties` realmente são aqueles aos quais sua conta do Data Lake pertence.

2. Certifique-se de conceder pelo menos a função de **Leitor** ao usuário ou entidade de serviço na conta do Data Lake. Veja como fazer isso:

    1. Vá para o portal do Azure -> sua conta do Data Lake Store
    2. Clique em **Controle de acesso (IAM)** na folha do Data Lake Store
    3. Clique em **Adicionar atribuição de função**
    4. Defina **Função** como **Leitor** e selecione o usuário ou a entidade de serviço que você usa para copiar para conceder acesso

3. Se você não quiser conceder **Reader** a função ao usuário ou entidade de serviço alternativa é [especificar explicitamente um local de execução](data-factory-data-movement-activities.md#global) na atividade de cópia com o local do seu Store lago de dados. Exemplo:

    ```json
    {
      "name": "CopyToADLS",
      "type": "Copy",
      ......
      "typeProperties": {
        "source": {
          "type": "<source type>"
        },
        "sink": {
          "type": "AzureDataLakeStoreSink"
        },
        "exeuctionLocation": "West US"
      }
    }
    ```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para especificar um conjunto de dados para representar os dados de entrada em um Data Lake Store, defina a propriedade **type** do conjunto de dados para **AzureDataLakeStore**. Defina a propriedade **linkedServiceName** do conjunto de dados para o nome do serviço vinculado do Data Lake Store. Para obter uma lista completa das seções e propriedades JSON disponíveis para definir conjuntos de dados, consulte o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). As seções de um conjunto de dados no JSON, como **structure**, **availability** e **policy** são similares para todos os tipos de conjunto de dados (Banco de Dados SQL do Azure, Blob do Azure e tabela do Azure, por exemplo). A seção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização e o formato dos dados no armazenamento de dados.

A seção **typeProperties** do conjunto de um conjunto de dados do tipo **AzureDataLakeStore** contém a seguinte propriedade:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| **folderPath** |Caminho para o contêiner e a pasta no Data Lake Store. |Sim |
| **fileName** |O nome do arquivo no Azure Data Lake Store. A propriedade **fileName** é opcional e diferencia maiúsculas de minúsculas. <br/><br/>Caso você especifique um **fileName**, a atividade (incluindo Cópia) funcionará no arquivo específico.<br/><br/>Quando **fileName** não for especificado, a Cópia incluirá todos os arquivos do **folderPath** para o conjunto de dados de entrada.<br/><br/>Quando **fileName** não for especificado para um conjunto de dados de saída e **preserveHierarchy** não for especificada no coletor de atividade, o nome do arquivo gerado está no formato `Data._Guid_.txt`. Por exemplo:  Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. |Não |
| **partitionedBy** |A propriedade **partitionedBy** é opcional. Você pode usá-la para especificar um caminho de pasta dinâmico e o nome de arquivo para dados de série temporal. Por exemplo, **folderPath** pode ser parametrizado para cada hora dos dados. Para encontrar detalhes e exemplos, confira A propriedade partitionedBy. |Não |
| **format** | Há suporte para os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Defina a propriedade **type** sob **format** para um desses valores. Para obter mais informações, consulte as seções [Formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [Formato JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formato ORC](data-factory-supported-file-and-compression-formats.md#orc-format) e [Formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) no artigo [Formatos de arquivo aos quais o Azure Data Factory dá suporte](data-factory-supported-file-and-compression-formats.md). <br><br> Se você quiser copiar arquivos no estado em que se encontram entre repositórios com base em arquivo (cópia binária), ignore a seção `format` nas duas definições de conjunto de dados de entrada e de saída. |Não |
| **compression** | Especifique o tipo e o nível de compactação para os dados. Os tipos com suporte são: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**. Os níveis de suporte são **Ideal** e **Mais rápido**. Para saber mais, confira [Formatos de arquivo aos quais o Azure Data Factory dá suporte](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

### <a name="the-partitionedby-property"></a>A propriedade partitionedBy
Você pode especificar propriedades **folderPath** e **fileName** dinâmicas para dados de série temporal com a propriedade **partitionedBy**, funções do Data Factory e variáveis do sistema. Para encontrar detalhes, consulte o artigo [Azure Data Factory – Funções e Variáveis do Sistema](data-factory-functions-variables.md).


No exemplo a seguir, `{Slice}` é substituído pelo valor da variável do sistema do Data Factory `SliceStart` no formato especificado (`yyyyMMddHH`). O nome `SliceStart` se refere à hora de início da fatia. A propriedade `folderPath` é diferente para cada fatia, como em `wikidatagateway/wikisampledataout/2014100103` ou `wikidatagateway/wikisampledataout/2014100104`.

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

No exemplo a seguir, o ano, o mês, o dia e a hora de `SliceStart` são extraídos em variáveis separadas usadas pelas propriedades `folderPath` e `fileName`:
```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
[
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
Para obter mais detalhes sobre conjuntos de dados de série temporal, agendamento e fatias, consulte os artigos [Conjuntos de dados no Azure Data Factory](data-factory-create-datasets.md) e [Agendamento e execução com o Data Factory](data-factory-scheduling-and-execution.md).


## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Criando pipelines](data-factory-create-pipelines.md). As propriedades, como nome, descrição, tabelas de entrada e saída, e política, estão disponíveis para todos os tipos de atividades.

As propriedades disponíveis na seção **typeProperties** de uma atividade variam de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam de acordo com os tipos de fonte e coletor.

**AzureDataLakeStoreSource** dá suporte à seguinte propriedade na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| **recursive** |Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. |True (valor padrão), False |Não |

**AzureDataLakeStoreSink** dá suporte às seguintes propriedades na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| **copyBehavior** |Especifica o comportamento da cópia. |<b>PreserveHierarchy</b>: Preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino.<br/><br/><b>FlattenHierarchy</b>: Todos os arquivos da pasta de origem são criados no primeiro nível da pasta de destino. Os arquivos de destino são criados com nomes gerados automaticamente.<br/><br/><b>MergeFiles</b>: Mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo ou do blob for especificado, o nome do arquivo mesclado será o nome especificado. Caso contrário, o nome do arquivo será gerado automaticamente. |Não |

### <a name="recursive-and-copybehavior-examples"></a>exemplos de recursive e copyBehavior
Esta seção descreve o comportamento resultante da operação de cópia para diferentes combinações de valores recursive e copyBehavior.

| recursive | copyBehavior | Comportamento resultante |
| --- | --- | --- |
| true |preserveHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta de destino Pasta1 é criada com a mesma estrutura da origem<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5. |
| true |flattenHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome gerado automaticamente para o Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo5 |
| true |mergeFiles |Para uma pasta de origem Pasta1 com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos dos Arquivo1 + Arquivo2 + Arquivo3 + Arquivo4 + Arquivo5 são mesclados em um único arquivo com um nome de arquivo gerado automaticamente |
| false |preserveHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/><br/><br/>Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não são selecionados. |
| false |flattenHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome gerado automaticamente para o Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo2<br/><br/><br/>Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não são selecionados. |
| false |mergeFiles |Para uma pasta de origem Pasta1 com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos dos Arquivo1 + Arquivo2 são mesclados em um único arquivo com um nome de arquivo gerado automaticamente. Nome gerado automaticamente para o Arquivo1<br/><br/>Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não são selecionados. |

## <a name="supported-file-and-compression-formats"></a>Formatos de arquivo e de compactação com suporte
Para obter detalhes, consulte o artigo [Formatos de arquivo e de compactação no Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

## <a name="json-examples-for-copying-data-to-and-from-data-lake-store"></a>Exemplos JSON para cópia de dados de e para o Data Lake Store
Os exemplos a seguir fornecem as definições de JSON de exemplo. Você pode usar essas definições de exemplo para criar um pipeline usando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [do Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Os exemplos mostram como copiar dados de e para o Data Lake Store e o Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados _diretamente_ de qualquer uma das fontes para qualquer um dos coletores com suporte. Para obter mais informações, consulte a seção "Fontes de dados e formatos com suporte" no artigo [Mover dados usando a Atividade de Cópia](data-factory-data-movement-activities.md).

### <a name="example-copy-data-from-azure-blob-storage-to-azure-data-lake-store"></a>Exemplo: Copiar dados do Armazenamento de Blobs do Azure para o Azure Data Lake Store
O código de exemplo nessa seção mostra:

* Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Um serviço vinculado do tipo [AzureDataLakeStore](#linked-service-properties).
* Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureDataLakeStore](#dataset-properties).
* Um [pipeline](data-factory-create-pipelines.md) com atividade de cópia que usa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) e [AzureDataLakeStoreSink](#copy-activity-properties).

O exemplo mostra como os dados de série temporal do Armazenamento de Blobs do Azure são copiados para o Data Lake Store de hora em hora.

**Serviço vinculado de armazenamento do Azure**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**Serviço vinculado do Azure Data Lake Store**

```JSON
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

> [!NOTE]
> Para obter os detalhes de configuração, consulte a seção [Propriedades do serviço vinculado](#linked-service-properties).
>

**Conjunto de dados de entrada de Blob do Azure**

No exemplo a seguir, os dados são coletados de um novo blob de hora em hora (`"frequency": "Hour", "interval": 1`). O caminho de pasta e nome de arquivo para o blob são avaliados dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes do ano, mês e dia da hora de início. O nome de arquivo usa a parte de hora da hora de início. A configuração `"external": true` informa ao serviço Data Factory que a tabela é externa ao Data Factory e não é produzida por uma atividade no Data Factory.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ]
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Conjunto de dados de saída do Azure Data Lake Store**

O exemplo a seguir copia dados para o Data Lake Store. Os novos dados são copiados para o Data Lake Store de hora em hora.

```JSON
{
    "name": "AzureDataLakeStoreOutput",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Atividade de cópia em um pipeline com uma origem de blob e um coletor Data Lake Store**

No exemplo a seguir, o pipeline contém uma atividade de cópia configurada para usar os conjuntos de dados de entrada e saída. A atividade de cópia está programada para ser executado a cada hora. Na definição de JSON do pipeline, o tipo `source` é definido como `BlobSource` e o tipo `sink` é definido como `AzureDataLakeStoreSink`.

```json
{
    "name":"SamplePipeline",
    "properties":
    {
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":
        [
            {
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureDataLakeStoreOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "AzureDataLakeStoreSink"
                    }
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

### <a name="example-copy-data-from-azure-data-lake-store-to-an-azure-blob"></a>Exemplo: Copiar dados do Azure Data Lake Store para um blob do Azure
O código de exemplo nessa seção mostra:

* Um serviço vinculado do tipo [AzureDataLakeStore](#linked-service-properties).
* Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [AzureDataLakeStore](#dataset-properties).
* Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Um [pipeline](data-factory-create-pipelines.md) com uma atividade de cópia que usa [AzureDataLakeStoreSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O código copia dados da série temporal do Data Lake Store para um Blob do Azure de hora em hora.

**Serviço vinculado do Azure Data Lake Store**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        }
    }
}
```

> [!NOTE]
> Para obter os detalhes de configuração, consulte a seção [Propriedades do serviço vinculado](#linked-service-properties).
>

**Serviço vinculado de armazenamento do Azure**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Conjunto de dados de entrada do Azure Data Lake**

Nesse exemplo, configurar `"external"` e `true` informa ao serviço Data Factory que a tabela é externa ao Data Factory e não é produzida por uma atividade no Data Factory.

```json
{
    "name": "AzureDataLakeStoreInput",
    "properties":
    {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
**Conjunto de dados de saída do blob do Azure**

No exemplo a seguir, os dados são gravados em um novo blob de hora em hora (`"frequency": "Hour", "interval": 1`). O caminho de pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Uma atividade de cópia em um pipeline com uma origem do Azure Data Lake Store e um coletor de blob**

No exemplo a seguir, o pipeline contém uma atividade de cópia configurada para usar os conjuntos de dados de entrada e saída. A atividade de cópia está programada para ser executado a cada hora. Na definição de JSON do pipeline, o tipo `source` é definido como `AzureDataLakeStoreSource` e o tipo `sink` é definido como `BlobSink`.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[
            {
                "name": "AzureDakeLaketoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureDataLakeStoreInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "AzureDataLakeStoreSource",
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

Na definição da atividade de cópia, você também pode mapear colunas do conjunto de dados de origem para colunas no conjunto de dados do coletor. Para obter detalhes, confira [Mapping dataset columns in Azure Data Factory](data-factory-map-columns.md) (Mapear colunas de conjunto de dados no Azure Data Factory).

## <a name="performance-and-tuning"></a>Desempenho e ajuste
Para saber mais sobre os fatores que afetam o desempenho da Atividade de Cópia e como otimizá-lo, veja o artigo [Guia de desempenho e ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md).
