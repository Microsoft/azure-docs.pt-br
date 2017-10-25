---
title: Visualizar e solucionar problemas com trabalhos do Stream Analytics | Microsoft Docs
description: "Saiba como visualizar um pipeline de trabalho do Stream Analytics para solução de problemas de autoatendimento usando o recurso de diagrama de diagnóstico."
keywords: 
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: d87841cd-c59f-4a46-b46e-8b904fdc12e9
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: 820b73a5dbf9bb108e189313cf6ee2b924ab04c7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="visualize-and-troubleshoot-stream-analytics-jobs"></a>Visualizar e solucionar problemas com trabalhos do Stream Analytics
No Stream Analytics, assim como em outras tecnologias baseadas em nuvem, a solução de problemas às vezes é necessária para examinar por que um trabalho não produz a saída esperada (ou até mesmo nenhuma saída). Com esse conceito em mente, o Stream Analytics oferece a capacidade de visualizar um trabalho de streaming. Isso também é útil como uma ferramenta de modelagem e traz um benefício adicional para os que exigem documentação de seu trabalho.

No painel de visualização, as entradas são visíveis, bem como a consulta que estão sendo executadas, e então todas as saídas são configuradas. Problemas de conectividade ou a configuração podem se tornar mais aparentes e também pode ser útil ver uma representação visual da sua configuração.

## <a name="using-the-diagnosis-diagram-tool"></a>Usando a ferramenta de diagrama de diagnóstico
Para acessar esse visualizador, basta clicar no botão "Diagrama de diagnóstico" na área "Configurações" do trabalho do Stream Analytics.

![stream-analytics-troubleshoot-visualization-diagnosis-diagram](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-diagnosis-diagram1.png)

Cada entrada e saída é codificadas por cores para indicar o estado atual desse componente, conforme mostrado a seguir.

![stream-analytics-troubleshoot-visualization-input-map](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-input-map.png)

Quando o usuário desejar examinar as etapas de consulta intermediárias para entender os padrões de fluxo de dados dentro de um trabalho, a ferramenta de visualização fornece uma exibição da separação da consulta em suas etapas componentes e a sequência do fluxo. Clique em cada etapa de consulta para mostrar a seção correspondente em um painel de edição de consulta conforme ilustrado. 

![stream-analytics-troubleshoot-visualization-intermediate-steps](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-intermediate-steps.png)

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

