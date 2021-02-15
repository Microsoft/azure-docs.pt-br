---
title: Copiar dados da tabela da Web usando Azure Data Factory
description: Saiba mais sobre o conector de tabela da Web do Azure Data Factory permite que você copie dados de uma tabela da Web para os armazenamentos de dados com suporte pelo Data Factory como coletores.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: 1ab45868afd8d1b7ba1f61f5eaacca283817e6d6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367003"
---
# <a name="copy-data-from-web-table-by-using-azure-data-factory"></a>Copiar dados da tabela da Web usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-web-table-connector.md)
> * [Versão atual](connector-web-table.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de um banco de dados de tabela da Web. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

A diferença entre esse conector de tabela da Web, o [conector REST](connector-rest.md) e o [conector HTTP](connector-http.md) é:

- O **conector da tabela da Web** extrai o conteúdo da tabela de uma página da Web em HTML.
- O **conector REST** especificamente dá suporte à cópia de dados de APIs RESTful.
- O **conector HTTP** é genérico para recuperar dados de qualquer ponto de extremidade HTTP, por exemplo, para baixar o arquivo. 

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector de tabela da Web tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/coletor com suporte](copy-activity-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados de um banco de dados de tabela da Web para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, esse conector de tabela da Web dá suporte à **extração de conteúdo de tabela de uma página HTML**.

## <a name="prerequisites"></a>Pré-requisitos

Para usar este conector de tabela da Web, você precisa configurar um runtime de integração auto-hospedada. Confira o artigo de [Integration Runtime auto-hospedado](create-self-hosted-integration-runtime.md) para obter detalhes.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções que a seguir fornecem detalhes sobre as propriedades usadas para definir entidades do Data Factory específicas à tabela da Web.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado de tabela da Web:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **Web** |Sim |
| url | URL para a origem da Web |Sim |
| authenticationType | O valor permitido é: **Anônimo**. |Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. É necessário um Integration Runtime auto-hospedado, conforme mencionado nos [Pré-requisitos](#prerequisites). |Sim |

**Exemplo:**

```json
{
    "name": "WebLinkedService",
    "properties": {
        "type": "Web",
        "typeProperties": {
            "url" : "https://en.wikipedia.org/wiki/",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [conjuntos de dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados da tabela da Web.

Para copiar dados da tabela web, defina a propriedade type do conjunto de dados como **WebTable**. Há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como: **WebTable** | Sim |
| caminho |Uma URL relativa para o recurso que contém a tabela. |Não. Quando o caminho não for especificado, apenas a URL especificada na definição do serviço vinculado será usada. |
| índice |O índice da tabela no recurso. Confira a seção [Obter índice de uma tabela em uma página HTML](#get-index-of-a-table-in-an-html-page) a fim de ver as etapas para obter o índice de uma tabela em uma página HTML. |Sim |

**Exemplo:**

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Web linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela origem da tabela da Web.

### <a name="web-table-as-source"></a>Tabela da Web como origem

Para copiar dados da tabela da Web, defina o tipo de origem na atividade de cópia como **WebSource**, não há suporte para nenhuma propriedade adicional.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromWebTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Web table input dataset name>",
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
                "type": "WebSource"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="get-index-of-a-table-in-an-html-page"></a>Obter índice de uma tabela em uma página HTML

Para obter o índice de uma tabela que você precisa configurar em [propriedades do conjunto de dados](#dataset-properties) é possível usar, por exemplo, Excel 2016 como a ferramenta conforme a seguir:

1. Inicie o **Excel 2016** e alterne para a guia **Dados**.
2. Clique em **Nova Consulta** na barra de ferramentas, aponte para **De Outras Fontes** e clique em **Da Web**.

    ![Menu do Power Query](./media/copy-data-from-web-table/PowerQuery-Menu.png)
3. Na caixa de diálogo **Da Web**, insira a **URL** que você usaria no JSON de serviço vinculado (por exemplo: https://en.wikipedia.org/wiki/)) juntamente com o caminho que você especificaria para o conjunto de dados (por exemplo: AFI 27s_100_Years de %... 100_Movies) e clique em **OK**.

    ![Do diálogo da Web](./media/copy-data-from-web-table/FromWeb-DialogBox.png)

    URL usada neste exemplo: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Se você vir a caixa de diálogo **Acessar conteúdo da Web**, selecione a **autenticação** de **URL** correta e clique em **Conectar**.

   ![Acessar caixa de diálogo de conteúdo da Web](./media/copy-data-from-web-table/AccessWebContentDialog.png)
5. Clique em um item de **tabela** na exibição de árvore para ver o conteúdo da tabela e clique em **Editar** na parte inferior.  

   ![Diálogo de navegador](./media/copy-data-from-web-table/Navigator-DialogBox.png)
6. Na janela **Editor de Consultas**, clique no botão **Editor Avançado** na barra de ferramentas.

    ![Botão Editor Avançado](./media/copy-data-from-web-table/QueryEditor-AdvancedEditorButton.png)
7. Na caixa de diálogo Editor Avançado, o número ao lado de "Origem" é o índice.

    ![Editor Avançado – índice](./media/copy-data-from-web-table/AdvancedEditor-Index.png)

Se você estiver usando o Excel 2013, use o [Microsoft Power Query para Excel](https://www.microsoft.com/download/details.aspx?id=39379) a fim de obter o índice. Confira o artigo [Conectar-se a uma página da Web](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) para obter detalhes. As etapas são semelhantes se você estiver usando o [Microsoft Power BI para Desktop](https://powerbi.microsoft.com/desktop/).


## <a name="lookup-activity-properties"></a>Pesquisar propriedades de atividade

Para saber detalhes sobre as propriedades, verifique [Pesquisar atividade](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
