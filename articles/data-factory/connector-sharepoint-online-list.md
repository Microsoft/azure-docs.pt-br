---
title: Copiar dados da Lista do SharePoint Online usando o Azure Data Factory
description: Saiba como copiar dados da Lista do SharePoint Online em armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: jingwang
ms.openlocfilehash: f8074b69b97a6ef96837e73a1082d2deb67084d9
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102177854"
---
# <a name="copy-data-from-sharepoint-online-list-by-using-azure-data-factory"></a>Copiar dados da Lista do SharePoint Online usando o Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a Atividade de Cópia no Azure Data Factory para copiar dados da Lista do SharePoint Online. O artigo baseia-se em [Atividade de Cópia no Azure Data Factory](copy-activity-overview.md), que apresenta uma visão geral da Atividade de Cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector da Lista do SharePoint Online é compatível com as seguintes atividades:

- [atividade de cópia](copy-activity-overview.md) de com [matriz de fonte/coletor com suporte](copy-activity-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados da Lista do SharePoint Online para qualquer armazenamento de dados do coletor com suporte. Para obter uma lista de armazenamentos de dados que o Copy Activity suporta como fontes e coletores, consulte [Armazenamentos de dados e formatos compatíveis](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, esse conector da Lista do SharePoint Online usa a autenticação da entidade de serviço e recupera dados por meio do protocolo OData.

> [!TIP]
> Este conector suporta a cópia de dados da **Lista** do SharePoint Online, mas não do arquivo. Saiba como copiar arquivo na seção [Copiar arquivo do SharePoint Online](#copy-file-from-sharepoint-online).

## <a name="prerequisites"></a>Pré-requisitos

Esse conector da Lista do SharePoint Online usa a autenticação da entidade de serviço para se conectar ao SharePoint. Siga estas etapas para configurá-lo:

1. Registre uma entidade de aplicativo no Azure Active Directory (Azure AD) seguindo [Registre o aplicativo com um locatário do Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Anote os seguintes valores, que são usados para definir o serviço vinculado:

    - ID do aplicativo
    - Chave do aplicativo
    - ID do locatário

2. Conceda permissão de site do SharePoint Online para o aplicativo registrado: 

    > [!NOTE]
    > Esta operação requer a permissão do proprietário do site do SharePoint Online. Você pode encontrar o proprietário acessando a home page do site -> clique no “X membros” no canto direito -> verifique quem tem a função de “Proprietário”.

    1. Abra o link do site do SharePoint Online; por exemplo, `https://[your_site_url]/_layouts/15/appinv.aspx` (substitua a URL do site).
    2. Pesquise a ID do aplicativo que você registrou, preencha os campos vazios e clique em “Criar”.

        - Domínio do aplicativo: `localhost.com`
        - URL de redirecionamento: `https://www.localhost.com`
        - XML de solicitação de permissão:

        ```xml
        <AppPermissionRequests AllowAppOnlyPolicy="true">
            <AppPermissionRequest Scope="http://sharepoint/content/sitecollection/web" Right="Read"/>
        </AppPermissionRequests>
        ```

        ![permissão de concessão do sharepoint](media/connector-sharepoint-online-list/sharepoint-online-grant-permission.png)

    3. Clique em “Confie” para este aplicativo.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre propriedades que você pode usar para definir entidades do Data Factory específicas ao conector da Lista do SharePoint Online.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir são compatíveis com o serviço vinculado da Lista do SharePoint Online:

| **Propriedade**        | **Descrição**                                              | **Necessário** |
| ------------------- | ------------------------------------------------------------ | ------------ |
| type                | A propriedade de tipo deve ser definida como:  **SharePointOnlineList**.  | Sim          |
| siteUrl             | A URL do site do SharePoint Online: por exemplo, `https://contoso.sharepoint.com/sites/siteName`. | Sim          |
| servicePrincipalId  | A ID do Aplicativo (cliente) do aplicativo registrado no Azure Active Directory. | Sim          |
| servicePrincipalKey | A chave do aplicativo. Marque esse campo como **SecureString** para armazená-lo com segurança no Data Factory ou [referencie um segredo armazenado no Cofre de Chaves do Azure](store-credentials-in-key-vault.md). | Sim          |
| tenantId            | O ID do locatário em que o aplicativo reside.          | Sim          |
| connectVia          | O [runtime de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Saiba mais em [Pré-requisitos](#prerequisites), anteriormente neste artigo. Se não especificado, o Azure Integration Runtime padrão será usado. | Não           |

**Exemplo:**

```json
{
    "name": "SharePointOnlineList",
    "properties": {
        "type": "SharePointOnlineList",
        "typeProperties": {
            "siteUrl": "<site URL>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenantId": "<tenant ID>"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa de seções e propriedades disponíveis para definição de conjuntos de dados, consulte [Conjuntos de dados e serviços vinculados](concepts-datasets-linked-services.md). A seção a seguir fornece uma lista das propriedades compatíveis pelo conjunto de dados da tabela do SAP.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade **tipo** do conjunto de dados deve ser definida como **SharePointOnlineLResource**. | Sim |
| listName | O nome da Lista do SharePoint Online. | Sim |

**Exemplo**

```json
{
    "name": "SharePointOnlineListDataset",
    "properties":
    {
        "type": "SharePointOnlineListResource",
        "linkedServiceName": {
            "referenceName": "<SharePoint Online List linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties":
        {
            "listName": "<name of the list>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da Atividade de Cópia

Para obter uma lista completa de seções e propriedades que estão disponíveis para definir atividades, consulte [Pipelines](concepts-pipelines-activities.md).  A seção a seguir fornece uma lista das propriedades compatíveis pela origem da Lista do SharePoint Online.

### <a name="sharepoint-online-list-as-source"></a>Lista do SharePoint Online como origem

Para a cópia de dados da Lista do SharePoint Online, as propriedades a seguir na seção de **origem** da Atividade de Cópia são compatíveis:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade **tipo** da origem da Atividade de Cópia deve ser definida como **SharePointOnlineListSource**. | Sim |
| Consulta | Opções personalizadas de consulta do OData para filtrar dados. Exemplo: `"$top=10&$select=Title,Number"`. | Não |
| httpRequestTimeout | O tempo limite (em segundos) para a solicitação HTTP obter uma resposta. O padrão é 300 (5 minutos). | Não |

**Exemplo**

```json
"activities":[
    {
        "name": "CopyFromSharePointOnlineList",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SharePoint Online List input dataset name>",
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
            "source": {
                "type": "SharePointOnlineListSource",
                "query": "<OData query e.g. $top=10&$select=Title,Number>"
            }, 
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

> [!NOTE]
> No Azure Data Factory, não é possível selecionar mais de um tipo de dados de *escolha* para uma origem de lista do SharePoint Online.

## <a name="data-type-mapping-for-sharepoint-online-list"></a>Mapeamento do tipo de dados para a Lista do SharePoint Online

Ao copiar dados da Lista do SharePoint Online, os seguintes mapeamentos são usados entre o tipo de dados da Lista do SharePoint Online e o tipo de dados provisórios do Azure Data Factory. 

| **Tipo de dados do SharePoint Online**                 | **Tipo de dados do OData**                                  | **Tipo de dados provisório do Azure Data Factory** |
| ----------------------------------------------- | ---------------------------------------------------- | ---------------------------------------- |
| Linha única de texto                             | Edm.String                                           | String                                   |
| Várias linhas de texto                          | Edm.String                                           | String                                   |
| Escolha (menu para escolher)                    | Edm.String                                           | String                                   |
| Número (1, 1,0, 100)                            | Edm.Double                                           | Double                                   |
| Moeda ($, ¥, &euro; )                              | Edm.Double                                           | Double                                   |
| Data e hora                                   | Edm.DateTime                                         | Datetime                                 |
| Pesquisa (informações já presentes nesse site)       | Edm.Int32                                            | Int32                                    |
| Sim/Não (caixa de seleção)                              | Edm.Boolean                                          | Boolean                                  |
| Pessoa ou Grupo                                 | Edm.Int32                                            | Int32                                    |
| Hiperlink ou Imagem                            | Edm.String                                           | String                                   |
| Calculado (cálculo baseado em outras colunas) | Edm.String/Edm.Double/Edm.DateTime/Edm.Boolean | String/Double/DateTime/Boolean     |
| Anexo                                      | Sem suporte                                        |                                          |
| Resultado da tarefa                                    | Sem suporte                                        |                                          |
| Dados externos                                   | Sem suporte                                        |                                          |
| Metadados gerenciado                                | Sem suporte                                        |                                          |

## <a name="copy-file-from-sharepoint-online"></a>Copiar arquivo do SharePoint Online

Você pode copiar o arquivo do SharePoint Online usando **atividade da Web** para autenticar e obter o token de acesso do SPO, passando para a **Atividade de cópia** subsequente para copiar dados com o **conector HTTP como origem**.

![fluxo de arquivos de cópia do sharepoint](media/connector-sharepoint-online-list/sharepoint-online-copy-file-flow.png)

1. Siga a seção de [Pré-requisitos](#prerequisites) para criar o aplicativo do AAD e conceder permissão ao SharePoint Online. 

2. Crie um **Atividades da Web** para obter o token de acesso do SharePoint Online:

    - **URL**: `https://accounts.accesscontrol.windows.net/[Tenant-ID]/tokens/OAuth/2`. Substitua a ID do locatário.
    - **Método**: POST
    - **Cabeçalhos**:
        - Tipo de conteúdo: application/x-www-form-urlencoded
    - **Corpo**:  `grant_type=client_credentials&client_id=[Client-ID]@[Tenant-ID]&client_secret=[Client-Secret]&resource=00000003-0000-0ff1-ce00-000000000000/[Tenant-Name].sharepoint.com@[Tenant-ID]`. Substitua a ID do cliente, o segredo do cliente, o ID do locatário e o nome do locatário.

    > [!CAUTION]
    > Defina a opção Saída segura como verdadeira na atividade da Web para impedir que o valor do token seja registrado em texto sem formatação. Qualquer outra atividade que consumir esse valor deve ter a opção de Entrada segura definida como verdadeira.

3. Cadeia com uma **Atividade de cópia** com o conector HTTP como origem para copiar o conteúdo do arquivo do SharePoint Online:

    - Serviço vinculado HTTP:
        - **URL base**: `https://[site-url]/_api/web/GetFileByServerRelativeUrl('[relative-path-to-file]')/$value`. Substitua a URL do site e o caminho relativo para o arquivo. Amostra de caminho relativo ao arquivo como `/sites/site2/Shared Documents/TestBook.xlsx`.
        - **Tipo de autenticação:** Anônimo *(para usar o token de Portador configurado na origem da atividade de cópia posteriormente)*
    - Conjunto de dados: escolha o formato desejado. Para copiar o arquivo como ele está, selecione o tipo “Binário”.
    - Origem da atividade de cópia:
        - **Método de solicitação**: GET
        - **Cabeçalho adicional**: use a expressão a seguir`@{concat('Authorization: Bearer ', activity('<Web-activity-name>').output.access_token)}`, que usa o token de Portador gerado pela atividade da Web upstream como cabeçalho de autorização. Substitua o nome da atividade da Web.
    - Configure o coletor de atividades de cópia como de costume.

> [!NOTE]
> Mesmo que um aplicativo do Azure AD tenha `FullControl` permissões no SharePoint Online, você não pode copiar arquivos de bibliotecas de documentos com IRM habilitado.

## <a name="lookup-activity-properties"></a>Pesquisar propriedades de atividade

Para saber detalhes sobre as propriedades, verifique [Pesquisar atividade](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Próximas etapas

Para obter uma lista de armazenamentos de dados que o Copy Activity suporta como fontes e coletores no Azure Data Factory, consulte [Armazenamentos e formatos de dados compatíveis](copy-activity-overview.md#supported-data-stores-and-formats).
