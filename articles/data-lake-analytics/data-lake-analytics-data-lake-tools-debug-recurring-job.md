---
title: Depurar trabalhos recorrentes no Azure Data Lake Analytics
description: Saiba como usar as Ferramentas do Azure Data Lake para Visual Studio para depurar um trabalho recorrente anormal.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 05/20/2018
ms.openlocfilehash: 5a2935d559a967151c5bdc01c4b0806fe52179b4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60629689"
---
# <a name="troubleshoot-an-abnormal-recurring-job"></a>Solucionar problemas de um trabalho recorrente anormal

Este artigo apresenta como usar as [Ferramentas do Azure Data Lake para Visual Studio](https://aka.ms/adltoolsvs) para solucionar problemas com trabalho recorrente. Saiba mais sobre o pipeline e trabalhos recorrentes no [blog do Azure Data Lake e do Azure HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2017/09/19/managing-pipeline-recurring-jobs-in-azure-data-lake-analytics-made-easy/).

Os trabalhos recorrentes geralmente compartilham a mesma lógica de consulta e dados de entrada semelhantes. Por exemplo, imagine que você tem um trabalho recorrente em execução em todas as manhãs de segunda-feira às 8h para contar usuários ativos na semana anterior. Os scripts para esses trabalhos compartilham um modelo de script que contém a lógica de consulta. As entradas para esses trabalhos são os dados de uso para a última semana. O compartilhamento da mesma lógica de consulta e uma entrada semelhante geralmente significa que o desempenho desses trabalhos é semelhante e estável. Se um dos seus trabalhos recorrentes repentinamente falhar, funcionar de maneira anormal ou ficar muito mais lento, você talvez queira:

- Ver os relatórios de estatísticas das execuções anteriores do trabalho recorrente para ver o que aconteceu.
- Comparar o trabalho anormal com um normal para descobrir o que foi alterado.

A **Exibição de Trabalho Relacionado** nas Ferramentas do Azure Data Lake para Visual Studio ajuda a acelerar o andamento da solução de problemas em ambos os casos.

## <a name="step-1-find-recurring-jobs-and-open-related-job-view"></a>Etapa 1: Localizar trabalhos recorrentes e abrir a exibição de trabalhos relacionados

Para usar a Exibição de Trabalho Relacionado para solucionar um problema de trabalho recorrente, primeiro você precisa localizar o trabalho recorrente no Visual Studio e, em seguida, abrir a Exibição de Trabalho Relacionado.

### <a name="case-1-you-have-the-url-for-the-recurring-job"></a>Caso 1: Você tem a URL do trabalho recorrente

Por meio de **Ferramentas** > **Data Lake** > **Exibição Trabalho**, você pode colar a URL de trabalho para abrir a Exibição de Trabalho no Visual Studio. Selecione **Exibir trabalhos relacionados** para abrir a Exibição de Trabalhos Relacionados.

![Exibir link de Trabalhos Relacionados nas Ferramentas do Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/view-related-job.png)
 
### <a name="case-2-you-have-the-pipeline-for-the-recurring-job-but-not-the-url"></a>Caso 2: Você tem o pipeline para o trabalho recorrente, mas não a URL

No Visual Studio, você pode abrir o navegador de Pipeline por meio de Gerenciador de Servidores > sua conta do Azure Data Lake Analytics > **Pipelines**. (Se você não encontrar esse nó no Gerenciador de Servidores, [faça o download o plug-in mais recente](https://aka.ms/adltoolsvs).) 

![Selecionando o nó de Pipelines](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/pipeline-browser.png)

No navegador de Pipeline, todos os pipelines para a conta do Data Lake Analytics são listados à esquerda. Você pode expandir os pipelines para localizar todos os trabalhos recorrentes e, em seguida, selecionar aquele que tem problemas. A Exibição de Trabalhos Relacionados abre à direita.

![Selecionando um pipeline e abrindo a Exibição de Trabalhos Relacionados](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-view.png)

## <a name="step-2-analyze-a-statistics-report"></a>Etapa 2: Analisar um relatório de estatísticas

Um resumo e um relatório de estatísticas são mostrados na parte superior do modo de Exibição de Trabalhos Relacionados. Lá, você pode encontrar a possível causa raiz do problema. 

1.  No relatório, o eixo X mostra a hora de envio do trabalho. Use-o para localizar o trabalho anormal.
2.  Use o processo no diagrama a seguir para verificar as estatísticas e obter informações sobre o problema e as possíveis soluções.

![Diagrama do processo de verificação de estatísticas](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-metrics-debugging-flow.png)

## <a name="step-3-compare-the-abnormal-job-to-a-normal-job"></a>Etapa 3: Comparar o trabalho anormal com um trabalho normal

Você pode encontrar todos os trabalhos recorrentes enviados, por meio da lista de trabalhos na parte inferior da Exibição de Trabalho Relacionado. Para obter mais informações e as possíveis soluções, clique com o botão direito do mouse no trabalho anormal. Use o modo de exibição de Comparação de Trabalhos para comparar o trabalho anormal com um normal anterior.

![Menu de atalho para comparar trabalhos](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/compare-job.png)

Preste atenção às grandes diferenças entre esses dois trabalhos. Essas diferenças estão provavelmente causando problemas de desempenho. Para verificar, use as etapas no diagrama a seguir:

![Diagrama do processo de verificação de diferenças entre os trabalhos](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-diff-debugging-flow.png)

## <a name="next-steps"></a>Próximas etapas

* [Resolver problemas de distorções de dados](data-lake-analytics-data-lake-tools-data-skew-solutions.md)
* [Depurar um código C# definido pelo usuário em trabalhos com falha do U-SQL](data-lake-analytics-debug-u-sql-jobs.md)
