---
title: Visão geral do conector do Azure Data Factory
description: Conheça os conectores com suporte no Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: jingwang
ms.openlocfilehash: cfd3376174ec0f7789389988245f7377b9896a00
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103015906"
---
# <a name="azure-data-factory-connector-overview"></a>Visão geral do conector do Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

O Azure Data Factory dá suporte aos seguintes armazenamentos de dados e formatos por meio de cópia, fluxo de dados, pesquisa, obtenção de metadados e exclusão de atividades. Clique em cada armazenamento de dados para aprender os recursos com suporte e as configurações correspondentes em detalhes.

## <a name="supported-data-stores"></a>Armazenamento de dados com suporte

[!INCLUDE [Connector overview](../../includes/data-factory-v2-connector-overview.md)]

## <a name="integrate-with-more-data-stores"></a>Integre com mais armazenamentos de dados

Azure Data Factory pode alcançar um conjunto mais amplo de armazenamentos de dados do que a lista mencionada acima. Se você precisar mover dados de/para um repositório de dados que não esteja na lista de conectores internos Azure Data Factory, aqui estão algumas opções extensíveis:
- Para o banco de dados e data warehouse, geralmente você pode encontrar um driver ODBC correspondente, com o qual você pode usar o [conector ODBC genérico](connector-odbc.md).
- Para aplicativos SaaS:
    - Se ele fornecer APIs RESTful, você poderá usar o [conector REST genérico](connector-rest.md).
    - Se ele tiver um feed OData, você poderá usar o [conector OData genérico](connector-odata.md).
    - Se ele fornecer APIs SOAP, você poderá usar o [conector http genérico](connector-http.md).
    - Se ele tiver um driver ODBC, você poderá usar o [conector ODBC genérico](connector-odbc.md).
- Para outros, verifique se você pode carregar ou expor dados como qualquer armazenamento de dados com suporte do ADF, por exemplo, blob/arquivo/FTP/SFTP/etc. do Azure, deixe o ADF pegar a partir daí. Você pode invocar o mecanismo de carregamento de dados personalizado por meio [do Azure function](control-flow-azure-function-activity.md), [atividade personalizada](transform-data-using-dotnet-custom-activity.md), [databricks](transform-data-databricks-notebook.md) / [HDInsight](transform-data-using-hadoop-hive.md), [atividade da Web](control-flow-web-activity.md), etc.

## <a name="supported-file-formats"></a>Formatos de arquivo com suporte

O Azure Data Factory é compatível com os formatos de arquivo a seguir. Confira cada artigo para obter configurações baseadas em formato.

- [Formato Avro](format-avro.md)
- [Formato binário](format-binary.md)
- [Formato do Common Data Model](format-common-data-model.md)
- [Formato de texto delimitado](format-delimited-text.md)
- [Formato delta](format-delta.md)
- [Formato do Excel](format-excel.md)
- [Formato JSON](format-json.md)
- [Formato ORC](format-orc.md)
- [Formato Parquet](format-parquet.md)
- [Formato XML](format-xml.md)

## <a name="next-steps"></a>Próximas etapas

- [Atividade de cópia](copy-activity-overview.md)
- [Fluxo de dados de mapeamento](concepts-data-flow-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade obter metadados](control-flow-get-metadata-activity.md)
- [Excluir atividade](delete-activity.md)
