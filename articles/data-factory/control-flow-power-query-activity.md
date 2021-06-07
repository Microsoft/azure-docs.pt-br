---
title: Atividade de Power Query no Azure Data Factory
description: Saiba como usar a atividade de Power Query para recursos de Wrangling de dados em um pipeline de Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/18/2021
ms.openlocfilehash: fc4f20db55f8e7e0b2f92cb8309c1c128b235089
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100385448"
---
# <a name="power-query-activity-in-data-factory"></a>Atividade do Power Query no data factory

A atividade Power Query permite que você crie e execute Power Query combinações para executar Wrangling de dados em escala em um pipeline Data Factory. Você pode criar um novo Power Query combinar na opção de menu novos recursos ou adicionando uma atividade de energia ao seu pipeline.

![Captura de tela que mostra Power Query no painel recursos de fábrica.](media/data-flow/power-query-wrangling.png)

Anteriormente, os dados Wrangling em Azure Data Factory foram criados a partir da opção de menu fluxo de dados. Isso foi alterado para a criação de uma nova atividade de Power Query. Você pode trabalhar diretamente dentro do Power Query editor de mashup para executar a exploração interativa de dados e, em seguida, salvar seu trabalho. Uma vez concluído, você pode pegar sua atividade de Power Query e adicioná-la a um pipeline. Azure Data Factory o escalará automaticamente e colocará em operação o Wrangling de dados usando o ambiente do Spark de fluxo de dados do Azure Data Factory.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MFYn]

## <a name="translation-to-data-flow-script"></a>Tradução para o script de fluxo de dados

Para alcançar a escala com sua atividade de Power Query, Azure Data Factory converte o ```M``` script em um script de fluxo de dados para que você possa executar o Power Query em escala usando o ambiente do Spark do fluxo de dados Azure data Factory. Crie seu fluxo de dados do Wrangling usando a preparação de dados sem código. Para obter a lista de funções disponíveis, consulte [funções de transformação](wrangling-functions.md).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os conceitos de Wrangling [de dados usando Power Query em Azure data Factory](wrangling-tutorial.md)
