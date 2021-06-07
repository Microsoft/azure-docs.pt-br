---
title: Examinar informações sobre seus dados no Azure alcance
description: Este guia de instruções descreve como exibir e usar os relatórios de verificação do alcance insights em seus dados.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: 7807659a30127f39bb79ad99bdb733c12eb1d25d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100548651"
---
# <a name="scan-insights-on-your-data-in-azure-purview"></a>Examinar informações sobre seus dados no Azure alcance

Este guia de instruções descreve como acessar, exibir e filtrar relatórios do Azure alcance Scan insights para seus dados.

Neste guia de instruções, você aprenderá a:

> [!div class="checklist"]
> * Exiba informações de sua conta do alcance.
> * Obtenha uma visão geral das suas verificações.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a usar o alcance insights, verifique se você concluiu as seguintes etapas:

* Configure os recursos do Azure e preencha a conta com os dados.
* Configure e conclua uma verificação na fonte de dados.

Para obter mais informações, consulte [gerenciar fontes de dados no Azure alcance (versão prévia)](manage-data-sources.md).

## <a name="use-purview-scan-insights"></a>Usar percepções de verificação do alcance

No Azure alcance, você pode registrar e verificar os tipos de origem. Você pode exibir o status da verificação ao longo do tempo em examinar insights. As informações informam quantas verificações falharam, tiveram êxito ou foram canceladas em um determinado período de tempo.

### <a name="view-scan-insights"></a>Exibir insights do exame

1. Vá para a tela instância **do Azure alcance** na portal do Azure e selecione sua conta do alcance.

1. Na página **visão geral** , **na seção Introdução** , selecione o bloco abrir o **alcance Studio** .

   :::image type="content" source="./media/scan-insights/portal-access.png" alt-text="Inicie o alcance no portal do Azure":::

1. Na página **inicial** do alcance, selecione o bloco **Exibir informações** para acessar sua área do **insights** :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: .

   :::image type="content" source="./media/scan-insights/view-insights.png" alt-text="Exibir suas ideias no portal do Azure":::

1. Na área **insights** :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: , selecione **verificações** para exibir o relatório de **informações de verificação** do alcance.

### <a name="view-high-level-kpis-to-show-count-of-scans-by-status-and-deep-dive-into-each-scan"></a>Exibir KPIs de alto nível para mostrar a contagem de verificações por status e aprofundar-se em cada verificação
 
1. Os KPIs de alto nível mostram o total de verificações executadas dentro de um período. O período de tempo é padronizado nos últimos 30 dias. No entanto, você pode selecionar últimos sete dias também. Com base no filtro de tempo, os valores de KPI refletem a contagem de verificações adequadamente.


1. Com base no valor de filtro de tempo selecionado, você pode ver a distribuição de verificações bem-sucedidas, com falha e canceladas por semana ou pelo dia no grafo.

1. Na parte inferior do grafo, há um link **Exibir mais** para você explorar mais. O link abre a página de  **status da verificação** na experiência do exame insights. Aqui você pode ver um nome de verificação e o número de vezes que ele teve êxito, falhou ou foi cancelado nos últimos 30 dias.

    :::image type="content" source="./media/scan-insights/main-graph.png" alt-text="Exibir o status da verificação ao longo do tempo":::

4. Você pode explorar ainda mais uma verificação específica clicando no nome da **verificação** que irá conectá-lo ao histórico de verificação dentro da experiência de **fontes** do Azure alcance. Na página Histórico de execuções, você pode obter a ID de execução que ajudará na investigação adicional de falhas.

    :::image type="content" source="./media/scan-insights/scan-status.png" alt-text="Exibir detalhes da verificação":::

5. Por fim, você pode voltar à página examinar status da **verificação** do insights seguindo as trilhas de pão no canto superior esquerdo da página de histórico de execuções.

    :::image type="content" source="./media/scan-insights/scan-history.png" alt-text="Exibir o histórico de verificação"::: 

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o Azure alcance **insights** com o [Data insights](./concept-insights.md)

* Saiba mais sobre a experiência de **fontes** do Azure alcance com [gerenciar fontes de dados](./manage-data-sources.md)