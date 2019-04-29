---
title: Como adicionar um conjunto de dados de referência ao seu ambiente Azure Time Series Insights | Microsoft Docs
description: Este artigo descreve como adicionar um conjunto de dados de referência para ampliar dados no seu ambiente Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/15/2018
ms.custom: seodec18
ms.openlocfilehash: 17ba15ea12efc80dcc830f6338a837d1abb77a71
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63764449"
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Criar um conjunto de dados de referência para o seu ambiente Time Series Insights usando o Portal do Azure

Este artigo descreve como adicionar um conjunto de dados de referência ao seu ambiente Azure Time Series Insights. É útil adicionar dados de referência em sua fonte de dados para aumentar os valores.

Um conjunto de dados de referência é uma coleção de itens que aumentam os eventos da fonte de evento. O mecanismo de entrada da Análise de Séries Temporais une cada evento da fonte de evento à linha de dados correspondentes em seu conjunto de dados de referência. Esse evento aumentado é disponibilizado para consulta. Essa junção baseia-se na(s) coluna(s) de chave primária definidas no conjunto de dados de referência.

Os dados de referência não estão unidos retroativamente. Isso significa que somente os dados de entrada atuais e futuros são correspondidos e associados ao conjunto de dados de referência, depois que foi configurado e carregado.

## <a name="video"></a>Vídeo: 

### <a name="in-this-video-we-cover-time-series-insights-reference-data-modelbr"></a>Neste vídeo, abordaremos o modelo de dados de referência do Time Series Insight.</br>

> [!VIDEO https://www.youtube.com/embed/Z0NuWQUMv1o]

## <a name="add-a-reference-data-set"></a>Adicionar um conjunto de dados de referência

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Localize seu ambiente Time Series Insights existente. Clique em **Todos os recursos** no menu à esquerda do portal do Azure. Selecione o seu ambiente de Análise de Séries Temporais.

3. Selecione a página **Visão geral**. Localize o **URL do Gerenciador de Time Series Insights** e abra o link.  

   Exiba o gerenciador para o seu ambiente TSI.

4. Expanda o seletor de ambiente no gerenciador TSI. Escolha o ambiente ativo. Selecione o ícone de dados de referência no canto superior direito na página do Gerenciador.

   ![Adicione dados de referência](media/add-reference-data-set/add_reference_data.png)

5. Selecione o botão **+ Adicionar um conjunto de dados** para começar a adicionar um novo conjunto de dados.

   ![Adicione o conjunto de dados](media/add-reference-data-set/add_data_set.png)

6. Na página **Novo conjunto de dados de referência**, escolha o formato dos dados: 
   - Escolha **CSV** para dados delimitados por vírgula. A primeira linha é tratada como uma linha de cabeçalho. 
   - Escolha **matriz JSON** para dados formatados de JSON (javascript object notation).

   ![Escolha a fonte de dados.](media/add-reference-data-set/add_data.png)

7. Forneça os dados, usando um dos dois métodos:
   - Cole os dados no editor de texto. Em seguida, selecione o botão **Analisar dados de referência**.
   - Selecione o botão **Escolher arquivo** para adicionar dados de um arquivo de texto local. 

   Por exemplo, cole dados CSV: ![Dados CSV colados](media/add-reference-data-set/csv_data_pasted.png)

   Por exemplo, cole os dados da matriz JSON: ![Colar dados JSON](media/add-reference-data-set/json_data_pasted.png)

   Se houver um erro ao analisar os valores de dados, o erro aparecerá em vermelho na parte inferior da página, como `CSV parsing error, no rows extracted`.

8. Depois que os dados são analisados com êxito, uma grade de dados é mostrada exibindo as colunas e linhas que representam os dados.  Examine a grade de dados para garantir a exatidão.

   ![Adicione dados de referência](media/add-reference-data-set/parse_data.png)

9. Examine cada coluna para ver o tipo de dados assumido e alterar o tipo de dados, se necessário.  Selecione o símbolo de tipo de dados no cabeçalho da coluna: **#** para duplo (dados numéricos), **T|F** para booliano, ou **Abc** para cadeia de caracteres.

   ![Escolha os tipos de dados nos cabeçalhos de coluna.](media/add-reference-data-set/choose_datatypes.png)

10. Renomeie os cabeçalhos de coluna, se necessário. O nome da coluna de chave é necessário para associar à propriedade correspondente na fonte de evento. Certifique-se de que os nomes de coluna de chave dos dados de referência correspondem exatamente ao nome do evento para seus dados de entrada, incluindo diferenciar maiúsculas de minúsculas. Os nomes de coluna não chave são usados para ampliar os dados de entrada com os valores de dados de referência correspondentes.

11. Clique em **Adicionar uma linha** ou **Adicionar uma coluna** para adicionar mais valores de dados de referência, conforme necessário.

12. Digite um valor no campo **Filtrar as linhas...**  para examinar linhas específicas, conforme necessário. O filtro é útil para análise de dados, mas não é aplicado ao carregar os dados.
 
13. Nomeie o conjunto de dados, preenchendo o campo **Nome do conjunto de dados** acima da grade de dados.

    ![Nomeie o conjunto de dados.](media/add-reference-data-set/name_reference_dataset.png)

14. Forneça a coluna de **chave primária** no conjunto de dados, selecionando na lista suspensa acima da grade de dados.

    ![Selecione a(s) coluna(s) de chave.](media/add-reference-data-set/set_primary_key.png)

    Opcionalmente, selecione o botão **+** para adicionar uma coluna de chave secundária, como uma chave primária composta. Se você precisar desfazer a seleção, escolha o valor vazio da lista suspensa para remover a chave secundária.

15. Para carregar os dados, selecione o botão **Carregar linhas**.

    ![Carregar](media/add-reference-data-set/upload_rows.png)

    A página confirma o carregamento concluído e exibe a mensagem **Conjunto de dados carregado com êxito**.

## <a name="next-steps"></a>Próximos passos
* [Gerenciar dados de referência](time-series-insights-manage-reference-data-csharp.md) programaticamente.
* Para obter a referência completa de API, confira o documento [API de Dados de Referência](/rest/api/time-series-insights/ga-reference-data-api).
