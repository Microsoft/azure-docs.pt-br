---
title: Como escrever consultas em Stream Analytics| Microsoft Docs
description: Escrever consultas no Stream Analytics e consultar dados | segmento do roteiro de aprendizagem.
keywords: como escrever consultas, dados de consulta, escrever uma consulta, escrever consultas
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 0e9cdadd-0ee0-4bee-b65b-4a06fb863c95
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: 215b774c20d80a67b1cefa2634131bd44860c692
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-write-queries-in-stream-analytics"></a>Como escrever consultas em Stream Analytics
Escrevendo consulta para lógica de processamento de fluxo no Stream Analytics do Azure é implementada como uma "consulta permanente" que é definida antes de um trabalho ser iniciado e executado em dados assim que ela atinge o trabalho. A transformação de dados é expressa em uma linguagem de consulta do tipo SQL, que é basicamente um subconjunto de T-SQL com algumas extensões de linguagem adicionadas como [Janelas](https://msdn.microsoft.com/library/azure/dn835019.aspx) usadas para expressar semânticas temporais.

## <a name="writing-queries"></a>Escrevendo consultas:
1. Em seu trabalho do Stream Analytics no Portal do Azure, clique em **Consulta**.
   
    ![Selecionar Consulta](./media/stream-analytics-write-queries/1-stream-analytics-write-queries.png)  
   
    No Portal do Azure, clique em **Consulta**.
   
    ![Selecionar Visualização da Consulta](./media/stream-analytics-write-queries/query-preview-portal.png)  
2. Os novos trabalhos têm um modelo de consulta que ajuda você a começar. O modelo de consulta executa uma consulta de "passagem" que projeta todos os campos dos eventos de entrada na saída.  
   
   * Se tiver definido pelo menos uma entrada e saída para seu trabalho, você poderá substituir os campos de espaço reservado "[YourOutputAlias]" e "[YourInputAlias]" pelos aliases da entrada e saída que deseja usar primeiro. Além disso, você ainda pode criar e testar sua consulta no portal clássico do Azure sem definir entradas e saídas no trabalho.
   * Se quiser executar mais processamento do que uma simples passagem, você pode editar a definição da consulta. Para começar a criar uma consulta, veja alguns padrões comuns de consulta capturados [aqui](stream-analytics-stream-analytics-query-patterns.md).  
   
   ![Janela de Consulta de dados](./media/stream-analytics-write-queries/2-stream-analytics-write-queries.png)  

## <a name="to-validate-query-data-is-working"></a>Para validar que os dados da consulta estão funcionando:
Você pode testar se sua consulta se comporta conforme esperado executando-a no navegador em um ou mais arquivos JSON locais contendo dados de teste. Isso não iniciará o trabalho nem terá nenhuma implicação de faturamento.

> [!NOTE]
> Atualmente, não há suporte para o teste de consulta no navegador no Portal do Azure.  
> 
> 

1. Verifique se não há erros na consulta (caso contrário, o botão Testar será desabilitado) e clique no botão Testar.  
   
   ![Teste de Consultar de dados](./media/stream-analytics-write-queries/3-stream-analytics-write-queries.png)  
2. Será solicitado que você especifique arquivos para cada uma das entradas referenciadas na consulta. Neste exemplo, a consulta do modelo é deixada como está, de modo que a caixa de diálogo está solicitando uma entrada denominada "yourinputalias".
   
   ![Testar Consulta de dados](./media/stream-analytics-write-queries/4-stream-analytics-write-queries.png)  
3. Procure um arquivo de teste. Vários exemplos de arquivos estão disponíveis no [github](https://github.com/Azure/azure-stream-analytics/tree/master/Sample Data) e você também pode recuperar exemplos de dados de suas próprias entradas de transmissão de dados por meio da função Dados de Exemplo na guia de entradas.
   
   ![Entrada de Consulta](./media/stream-analytics-write-queries/5-stream-analytics-write-queries.png)  
4. Depois de fechar a caixa de diálogo, sua consulta será executada sobre os dados de teste e você verá os resultados na parte inferior da página Consulta.
   
   ![Resumo da Consulta](./media/stream-analytics-write-queries/6-stream-analytics-write-queries.png)  

## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente nosso [Fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

