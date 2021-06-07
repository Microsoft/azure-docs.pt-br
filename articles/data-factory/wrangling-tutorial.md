---
title: Introdução ao fluxo de dados do Wrangling no Azure Data Factory
description: Um tutorial sobre como preparar dados no Azure Data Factory usando o fluxo de dados do Wrangling
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: 3d43a532f57feab361c6a3de79269991f46fc55d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98684015"
---
# <a name="prepare-data-with-data-wrangling"></a>Preparar dados com o Wrangling de dados

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Os Wrangling de dados no data factory permitem que você crie Power Query de mashups interativos nativamente no ADF e, em seguida, execute-os em escala dentro de um pipeline do ADF.

> [!NOTE]
> Power Query atividade no ADF está disponíveis no momento em visualização pública

## <a name="create-a-power-query-activity"></a>Criar uma atividade de Power Query

Há duas maneiras de criar uma Power Query no Azure Data Factory. Uma delas é clicar no ícone de adição e selecionar o **fluxo de dados** no painel recursos de fábrica.

> [!NOTE]
> Anteriormente, o recurso Data Wrangling estava localizado no fluxo de trabalho de fluxo de dados. Agora, você criará seu Wrangling de dados para combinar com ```New > Power query```

![Captura de tela que mostra Power Query no painel recursos de fábrica.](media/data-flow/power-query-wrangling.png)

O outro método está no painel atividades da tela do pipeline. Abra o **Power Query** acordeão e arraste a atividade de **Power Query** para a tela.

![Captura de tela que realça a opção de Wrangling de dados.](media/data-flow/power-query-activity.png)

## <a name="author-a-power-query-data-wrangling-activity"></a>Criar uma atividade do Power Query data Wrangling

Adicione um **conjunto de fontes de origem** para seu Power Query combinar. Você pode escolher um conjunto de um existente ou criar um novo. Você também pode selecionar um conjunto de um banco de uma coletor. Você pode escolher um ou mais conjuntos de data de origem, mas apenas um coletor é permitido neste momento. A escolha de um conjunto de coletas é opcional, mas pelo menos um conjunto de fonte de origem é necessário.

![Wrangling](media/wrangling-data-flow/tutorial4.png)

Clique em **criar** para abrir o Power Query editor de mashup online.

![Captura de tela que mostra o botão criar que abre o Power Query editor de mashup online.](media/wrangling-data-flow/tutorial5.png)

Crie seu Wrangling Power Query usando a preparação de dados sem código. Para obter a lista de funções disponíveis, consulte [funções de transformação](wrangling-functions.md). O ADF converte o script M em um script de fluxo de dados para que você possa executar o Power Query em escala usando o ambiente do Spark do fluxo de dados Azure Data Factory.

![Captura de tela que mostra o processo para criar seus dados Wrangling Power Query.](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-power-query-data-wrangling-activity"></a>Executando e monitorando uma atividade de Wrangling de dados de Power Query

Para executar uma execução de depuração de pipeline de uma atividade de Power Query, clique em **depurar** na tela do pipeline. Depois de publicar seu pipeline, o **gatilho agora** executa uma execução sob demanda do último pipeline publicado. Power Query pipelines podem ser agendados com todos os disparadores Azure Data Factory existentes.

![Captura de tela que mostra como adicionar uma atividade Power Query data Wrangling.](media/wrangling-data-flow/tutorial3.png)

Vá para a guia **Monitor** para visualizar a saída de uma execução de atividade de Power Query disparada.

![Captura de tela que mostra a saída de uma execução disparada de Wrangling Power Query atividade.](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>Próximas etapas

Saiba como [criar um fluxo de dados de mapeamento](tutorial-data-flow.md).
