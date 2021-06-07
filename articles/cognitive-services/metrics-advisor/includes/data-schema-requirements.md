---
title: Requisitos do esquema de dados
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 4c55c25621df1925b6ed6c374d8af88551eb1e46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96231422"
---
O Assistente de Métricas é um serviço para detecção de anomalias, diagnóstico e análise de séries temporais. Como um serviço da plataforma AI, ele usa os dados para treinar o modelo usado. O serviço aceita tabelas de dados agregados com as seguintes colunas:

* **Medida** (obrigatória): uma ou mais colunas contendo valores numéricos.
* **Carimbo de data/hora** (opcional): zero ou uma coluna com o tipo `DateTime` ou `String`. Quando essa coluna não for definida, o carimbo de data/hora será designado como a hora de início de cada período de ingestão. Formatar o carimbo de data/hora como: `yyyy-MM-ddTHH:mm:ssZ`. 
  * **O carimbo de data/hora deve corresponder à granularidade da métrica. Por exemplo, uma métrica diária deve garantir que a hora, o minuto e o segundo do carimbo de data/hora sejam rotulados como `00:00:00`** .
* **Dimensão** (opcional): as colunas podem ser de qualquer tipo de dados. Tenha cuidado ao trabalhar com grandes volumes de colunas e valores, para impedir que números excessivos de dimensões sejam processados.

> [!Note]
> Para cada métrica, deve haver apenas um carimbo de data/hora por medida, correspondendo a uma combinação de dimensão. Agregue os dados antes da integração ou use uma consulta para especificar os dados a serem ingeridos.