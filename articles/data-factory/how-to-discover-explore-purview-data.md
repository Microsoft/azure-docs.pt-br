---
title: Descobrir e explorar dados no ADF usando o Purview
description: Saiba como descobrir, explorar dados em Azure Data Factory usando o alcance
ms.service: data-factory
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
ms.custom: seo-lt-2019
ms.date: 01/15/2021
ms.openlocfilehash: 031e7ceee739ffd666ad367bd6778865a297a25d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100386264"
---
# <a name="discover-and-explore-data-in-adf-using-purview"></a>Descobrir e explorar dados no ADF usando o Purview

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Neste artigo, você registrará uma conta do Azure alcance em um Data Factory. Essa conexão permite que você descubra ativos alcance do Azure e interaja com eles por meio de recursos do ADF. 

Você pode executar as seguintes tarefas no ADF: 
- Use a caixa de pesquisa na parte superior para localizar ativos do Purview com base em palavras-chave 
- Entender os dados com base em metadados, linhagem, anotações 
- Conecte esses dados à sua data factory com os serviços vinculados ou conjuntos de dados 

## <a name="prerequisites"></a>Pré-requisitos 
- [Conta do Azure Purview](../purview/create-catalog-portal.md) 
- [Data Factory](./quickstart-create-data-factory-portal.md) 
- [Conectar uma conta do Azure alcance ao Data Factory](./connect-data-factory-to-azure-purview.md) 

## <a name="using-azure-purview-in-data-factory"></a>Usando o alcance do Azure no Data Factory 

Use o Azure alcance no Data Factory exige que você tenha acesso a essa conta do alcance. Data Factory passa pela sua permissão alcance. Por exemplo, se você tiver uma função de permissão de curador, poderá editar metadados verificados pelo Azure alcance. 

### <a name="data-discovery-search-datasets"></a>Data Discovery: pesquisar conjuntos de dados 

Para descobrir os dados registrados e verificados pelo Azure alcance, você pode usar a barra de pesquisa no centro superior do Data Factory Portal. Certifique-se de selecionar alcance do Azure para pesquisar todos os dados da sua organização. 

:::image type="content" source="./media/data-factory-purview/search-dataset.png" alt-text="Captura de tela para executar em conjuntos de os.":::

### <a name="actions-that-you-can-perform-over-datasets-with-data-factory-resources"></a>Ações que você pode executar sobre conjuntos de os com Data Factory recursos 
Você pode criar diretamente um serviço vinculado, um conjunto de dados ou um Dataflow sobre aqueles pesquisados pelo Azure alcance.

:::image type="content" source="./media/data-factory-purview/actions-over-purview-data.png" alt-text="Captura de tela que mostra como você pode criar diretamente um serviço vinculado, um conjunto de dados ou um Dataflow por meio dos dados pesquisados pelo Azure alcance.":::

##  <a name="nextsteps"></a>Próximas etapas 

- [Registrar e verificar os ativos de Azure Data Factory no Azure alcance](../purview/register-scan-azure-synapse-analytics.md)
- [Como pesquisar dados no catálogo de dados do Azure alcance](../purview/how-to-search-catalog.md)