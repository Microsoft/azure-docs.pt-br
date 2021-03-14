---
title: Informações de ativos sobre seus dados no Azure alcance (versão prévia)
description: Este guia de instruções descreve como exibir e usar relatórios de ativos do alcance insights em seus dados.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: b9a207ffa14a18a5f4421fd21cebed28290b5ea6
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183073"
---
# <a name="asset-insights-on-your-data-in-azure-purview"></a>Informações de ativos sobre seus dados no Azure alcance

Este guia de instruções descreve como acessar, exibir e filtrar relatórios do alcance Asset Insight para seus dados.

Neste guia de instruções, você aprenderá a:

> [!div class="checklist"]
> * Exiba informações de sua conta do alcance.
> * Obtenha uma visão geral dos seus dados.
> * Faça uma busca detalhada para obter mais detalhes de contagem de ativos.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a usar o alcance insights, verifique se você concluiu as seguintes etapas:

* Configure os recursos do Azure e preencha a conta com os dados.

* Configure e conclua uma verificação no tipo de origem.

Para obter mais informações, consulte [gerenciar fontes de dados no Azure alcance (versão prévia)](manage-data-sources.md).

## <a name="use-purview-asset-insights"></a>Usar insights de ativos do alcance

No Azure alcance, você pode registrar e verificar os tipos de origem. Depois que a verificação for concluída, você poderá exibir a distribuição de ativos no Asset insights, que informa o estado do seu espaço de dados por conjuntos de recursos e classificação. Ele também informa se há alguma alteração no tamanho dos dados.

> [!NOTE]
> Depois de ter examinado seus tipos de origem, forneça informações de ativos de 3-8 horas para refletir os novos ativos. O atraso pode ser devido ao alto tráfego na região de implantação ou tamanho da carga de trabalho. Para obter mais informações, entre em contato com a equipe de suporte do campo.

1. Navegue até o recurso alcance do Azure na portal do Azure.

1. Na página **visão geral** , **na seção Introdução** , selecione o bloco abrir o **alcance Studio** .

   :::image type="content" source="./media/asset-insights/portal-access.png" alt-text="Inicie o alcance no portal do Azure":::

1. Na página **inicial** do alcance, selecione o bloco **Exibir informações** para acessar sua área do **insights** :::image type="icon" source="media/asset-insights/ico-insights.png" border="false"::: .

   :::image type="content" source="./media/asset-insights/view-insights.png" alt-text="Exibir suas ideias no portal do Azure":::

1. Na área de **informações** :::image type="icon" source="media/asset-insights/ico-insights.png" border="false"::: , selecione **ativos** para exibir o relatório de **insights de ativos** do alcance.

### <a name="view-asset-insights"></a>Exibir informações de ativos

1. A página principal do **insights de ativos** exibe as seguintes áreas:

2. KPIs de alto nível para mostrar tipos de origem, ativos classificados e ativos descobertos
 
3. O primeiro gráfico mostra **ativos por tipo de origem**.

4. Exiba sua distribuição de ativos por tipo de origem. Escolha uma classificação ou uma categoria de classificação inteira para ver a distribuição de ativos por tipo de origem. 
 
5. Para exibir mais, selecione **Exibir mais**, que exibe uma forma tabular dos tipos de origem e contagens de ativos. Os filtros de classificação são transportados para esta página.

   :::image type="content" source="./media/asset-insights/highlight-kpis.png" alt-text="Exibir KPIs e grafo no insights de ativos":::
 
6. Selecione uma fonte específica para a qual você gostaria de ver as principais pastas com contagens de ativos. 

   :::image type="content" source="./media/asset-insights/select-data-source.png" alt-text="Selecionar tipo de origem":::
 
7. Selecione o total de ativos em relação à pasta superior dentro do tipo de origem selecionado acima.

   :::image type="content" source="./media/asset-insights/file-path.png" alt-text="Exibir caminhos de arquivo":::

8. Exiba a lista de arquivos dentro da pasta. Navegue de volta para informações usando as trilhas de pão.

   :::image type="content" source="./media/asset-insights/list-page.png" alt-text="Exibir lista de ativos":::  

### <a name="file-based-source-types"></a>Tipos de origem baseados em arquivo
Os próximos dois grafos do Asset insights mostram uma distribuição de tipos de origem baseados em arquivo. O primeiro grafo, chamado **tendência de tamanho (GB) do tipo de arquivo em tipos de origem**, mostra a tendência de tamanho de tipo de arquivo superior nos últimos 30 dias. 
 
1. Escolha o tipo de fonte para exibir o tipo de arquivo na origem. 
 
1. Selecione **Exibir mais** para ver o tamanho atual dos dados, alteração de tamanho, contagem de ativos atuais e alteração na contagem de ativos.
 
   > [!NOTE]
   > Se a verificação for executada apenas uma vez nos últimos 30 dias ou se qualquer alteração de catálogo, como adição/remoção de classificação, tiver ocorrido apenas uma vez em 30 dias, as informações de alteração acima aparecerão em branco.

1. Consulte as principais pastas com alterar contagens de ativos principais quando você clica no tipo de origem.

1. Selecione o caminho para ver a lista de ativos.

O segundo grafo em tipos de origem baseados em arquivo são ***arquivos não associados a um conjunto de recursos***. Se você espera que todos os arquivos sejam acumulados em um conjunto de recursos, esse grafo pode ajudá-lo a entender quais ativos não foram acumulados. Os ativos ausentes podem ser uma indicação do padrão de arquivo incorreto na pasta. Siga as mesmas etapas de outros grafos para exibir mais detalhes sobre os arquivos.

   :::image type="content" source="./media/asset-insights/file-based-assets.png" alt-text="Exibir ativos baseados em arquivo":::  

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os relatórios do Azure alcance Insight com as [análises de verificação](./scan-insights.md)
