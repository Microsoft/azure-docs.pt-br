---
title: Como dimensionar seu ambiente-Azure Time Series Insights | Microsoft Docs
description: Saiba como dimensionar seu ambiente de Azure Time Series Insights usando o portal do Azure.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 0dadf523c5d17ffb91f4fefa71b52d1d1855c978
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91570224"
---
# <a name="how-to-scale-your-azure-time-series-insights-gen1-environment"></a>Como dimensionar seu ambiente de Azure Time Series Insights Gen1

> [!CAUTION]
> Esse é um artigo do Gen1.

Este artigo descreve como alterar a capacidade de seu ambiente de Azure Time Series Insights usando o [portal do Azure](https://portal.azure.com). A capacidade é o multiplicador aplicado à taxa de ingresso, à capacidade de armazenamento e ao custo associado com a SKU selecionada.

Você pode usar o Portal do Azure para aumentar ou diminuir a capacidade de um determinada SKU de preços.

No entanto, não é permitido alterar a SKU da camada de preços. Por exemplo, um ambiente com uma SKU de preço S1 não pode ser convertido em um S2 ou vice-versa.

## <a name="ga-limits"></a>Limites de GA

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

## <a name="change-the-capacity-of-your-environment"></a>Alterar a capacidade do seu ambiente

1. Na portal do Azure, localize e selecione seu ambiente de Azure Time Series Insights.

1. No menu de seu ambiente de Azure Time Series Insights, selecione **configuração de armazenamento**.

   [![Configurar sua capacidade de Azure Time Series Insights](media/scale-your-environment/scale-your-environment-configure.png)](media/scale-your-environment/scale-your-environment-configure.png#lightbox)

1. Ajuste o controle deslizante **Capacidade** para selecionar a capacidade que atenda aos requisitos de suas taxas de entrada e capacidade de armazenamento. Observe que a **Taxa de entrada**, a **Capacidade de armazenamento** e o **Custo estimado** são atualizadas dinamicamente para mostrar o impacto da alteração.

   [![Configurar seu ambiente usando o controle deslizante capacidade](media/scale-your-environment/scale-your-environment-slider.png)](media/scale-your-environment/scale-your-environment-slider.png#lightbox)

   Como alternativa, você pode digitar o número do multiplicador de capacidade na caixa de texto à direita do controle deslizante.

1. Selecione **Salvar** para dimensionar o ambiente. O indicador de progresso é exibido momentaneamente até que a alteração seja confirmada.

1. Verifique se a nova capacidade é [suficiente para evitar a limitação](time-series-insights-diagnose-and-solve-problems.md).

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações, consulte [noções básicas sobre retenção em Azure Time Series insights](time-series-insights-concepts-retention.md).

- Saiba mais sobre como [Configurar a retenção de dados no Azure Azure Time Series insights](time-series-insights-how-to-configure-retention.md).

- Saiba mais sobre como [planejar o seu ambiente](time-series-insights-environment-planning.md).
