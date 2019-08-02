---
title: Como adicionar um conjunto de dados de referência ao seu ambiente Azure Time Series Insights | Microsoft Docs
description: Este artigo descreve como adicionar um conjunto de dados de referência para ampliar dados no seu ambiente Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/26/2019
ms.custom: seodec18
ms.openlocfilehash: 99933fa36cc822598ec9c173a470f90264d06d54
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461198"
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Criar um conjunto de dados de referência para o seu ambiente Time Series Insights usando o Portal do Azure

Este artigo descreve como adicionar um conjunto de dados de referência ao seu ambiente Azure Time Series Insights. É útil adicionar dados de referência em sua fonte de dados para aumentar os valores.

Um conjunto de dados de referência é uma coleção de itens que aumentam os eventos da fonte de evento. O mecanismo de entrada da Análise de Séries Temporais une cada evento da fonte de evento à linha de dados correspondentes em seu conjunto de dados de referência. Esse evento aumentado é disponibilizado para consulta. Essa junção baseia-se na(s) coluna(s) de chave primária definidas no conjunto de dados de referência.

Os dados de referência não estão unidos retroativamente. Assim, os dados de entrada apenas atuais e futuros são correspondidos e associados ao conjunto de referência de data, depois que ele foi configurado e carregado.

## <a name="video"></a>Vídeo

### <a name="learn-about-time-series-insights-reference-data-modelbr"></a>Saiba mais sobre o modelo de dados de referência do Time Series Insight.</br>

> [!VIDEO https://www.youtube.com/embed/Z0NuWQUMv1o]

## <a name="add-a-reference-data-set"></a>Adicionar um conjunto de dados de referência

1. Entre no [Portal do Azure](https://portal.azure.com).

1. Localize seu ambiente Time Series Insights existente. Selecione **Todos os recursos** no menu à esquerda do portal do Azure. Selecione o seu ambiente de Análise de Séries Temporais.

1. Selecione a página **Visão geral**. Localize o **URL do Gerenciador de Time Series Insights** e abra o link.  

   Exiba o gerenciador para o seu ambiente TSI.

1. Expanda o seletor de ambiente no gerenciador TSI. Escolha o ambiente ativo. Selecione o ícone de dados de referência no canto superior direito na página do Gerenciador.

   [![Adicionar dados de referência](media/add-reference-data-set/add-reference-data.png)](media/add-reference-data-set/add-reference-data.png#lightbox)

1. Selecione o botão **+ Adicionar um conjunto de dados** para começar a adicionar um novo conjunto de dados.

   [![Adicionar conjunto de dados](media/add-reference-data-set/add-data-set.png)](media/add-reference-data-set/add-data-set.png#lightbox)

1. Na página **Novo conjunto de dados de referência**, escolha o formato dos dados:
   - Escolha **CSV** para dados delimitados por vírgula. A primeira linha é tratada como uma linha de cabeçalho.
   - Escolha **matriz JSON** para dados formatados de JSON (javascript object notation).

   [![Escolha o formato de dados.](media/add-reference-data-set/add-data.png)](media/add-reference-data-set/add-data.png#lightbox)

1. Forneça os dados, usando um dos dois métodos:
   - Cole os dados no editor de texto. Em seguida, selecione o botão **Analisar dados de referência**.
   - Selecione o botão **Escolher arquivo** para adicionar dados de um arquivo de texto local.

   Por exemplo, cole dados CSV: [![Dados CSV colados](media/add-reference-data-set/csv-data-pasted.png)](media/add-reference-data-set/csv-data-pasted.png#lightbox)

   Por exemplo, cole os dados da matriz JSON: [![Cole os dados JSON](media/add-reference-data-set/json-data-pasted.png)](media/add-reference-data-set/json-data-pasted.png#lightbox)

   Se houver um erro ao analisar os valores de dados, o erro aparecerá em vermelho na parte inferior da página, como `CSV parsing error, no rows extracted`.

1. Depois que os dados são analisados com êxito, uma grade de dados é mostrada exibindo as colunas e linhas que representam os dados.  Examine a grade de dados para garantir a exatidão.

   [![Adicionar dados de referência](media/add-reference-data-set/parse-data.png)](media/add-reference-data-set/parse-data.png#lightbox)

1. Examine cada coluna para ver o tipo de dados assumido e alterar o tipo de dados, se necessário.  Selecione o símbolo de tipo de dados no cabeçalho da coluna: **#** para duplo (dados numéricos), **T|F** para booliano, ou **Abc** para cadeia de caracteres.

   [![Escolha os tipos de dados nos cabeçalhos de coluna.](media/add-reference-data-set/choose-datatypes.png)](media/add-reference-data-set/choose-datatypes.png#lightbox)

1. Renomeie os cabeçalhos de coluna, se necessário. O nome da coluna de chave é necessário para associar à propriedade correspondente na fonte de evento. Certifique-se de que os nomes de coluna de chave dos dados de referência correspondem exatamente ao nome do evento para seus dados de entrada, incluindo diferenciar maiúsculas de minúsculas. Os nomes de coluna não chave são usados para ampliar os dados de entrada com os valores de dados de referência correspondentes.

1. Selecione **adicionar uma linha** ou **adicionar uma coluna** para adicionar mais valores de dados de referência, conforme necessário.

1. Digite um valor no campo **Filtrar as linhas...**  para examinar linhas específicas, conforme necessário. O filtro é útil para análise de dados, mas não é aplicado ao carregar os dados.

1. Nomeie o conjunto de dados, preenchendo o campo **Nome do conjunto de dados** acima da grade de dados.

    [![O nome do conjunto de dados.](media/add-reference-data-set/name-reference-dataset.png)](media/add-reference-data-set/name-reference-dataset.png#lightbox)

1. Forneça a coluna de **chave primária** no conjunto de dados, selecionando na lista suspensa acima da grade de dados.

    [![Selecione as colunas de chave.](media/add-reference-data-set/set-primary-key.png)](media/add-reference-data-set/set-primary-key.png#lightbox)

    Opcionalmente, selecione o botão **+** para adicionar uma coluna de chave secundária, como uma chave primária composta. Se você precisar desfazer a seleção, escolha o valor vazio da lista suspensa para remover a chave secundária.

1. Para carregar os dados, selecione o botão **Carregar linhas**.

    [![carregar](media/add-reference-data-set/upload-rows.png)](media/add-reference-data-set/upload-rows.png#lightbox)

    A página confirma o carregamento concluído e exibe a mensagem **Conjunto de dados carregado com êxito**.

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar dados de referência](time-series-insights-manage-reference-data-csharp.md) programaticamente.

* Para obter a referência completa de API, confira o documento [API de Dados de Referência](/rest/api/time-series-insights/ga-reference-data-api).
