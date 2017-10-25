---
title: Como solucionar problemas de um trabalho recorrente anormal | Microsoft Docs
description: Saiba como usar as Ferramentas do Azure Data Lake para Visual Studio para depurar um trabalho recorrente anormal.
services: data-lake-analytics
documentationcenter: 
author: yanancai
manager: 
editor: 
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/27/2017
ms.author: yanacai
ms.openlocfilehash: a358f94b117c12511028a875e56b5c9dba8d3382
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-troubleshoot-an-abnormal-recurring-job"></a>Como solucionar problemas de um trabalho recorrente anormal

Neste documento, apresentaremos como usar as [Ferramentas do Azure Data Lake para Visual Studio](http://aka.ms/adltoolsvs) para solucionar problemas de trabalho recorrente. Saiba mais sobre pipeline e trabalhos recorrentes [aqui](https://blogs.msdn.microsoft.com/azuredatalake/2017/09/19/managing-pipeline-recurring-jobs-in-azure-data-lake-analytics-made-easy/).
Os trabalhos recorrentes geralmente compartilham a mesma lógica de consulta e dados de entrada semelhantes. Por exemplo, você tem um trabalho recorrente em execução em todas as manhãs de segunda-feira às 8h. para contar os usuários ativos da semana passada. Os scripts desses trabalhos compartilham um modelo de script que contém a lógica de consulta e as entradas para esses trabalhos são os dados de uso da última semana. O compartilhamento da mesma lógica de consulta e a entrada semelhante geralmente significam que o desempenho desses trabalhos é semelhante e estável. Caso um de seus trabalhos recorrentes, de repente, tenha um desempenho anormal, falhando ou ficando muito lento, você talvez queira:

1.  Ver os relatórios de estatísticas das execuções de versões prévias do trabalho recorrente para ver o que aconteceu.
2.  Comparar o trabalho anormal com um normal para descobrir o que foi alterado.

A **Exibição de Trabalho Relacionado** nas Ferramentas do Azure Data Lake para Visual Studio ajuda a acelerar o andamento da solução de problemas em ambos os casos.

## <a name="step-1-find-recurring-jobs-and-open-related-job-view"></a>Etapa 1: localizar os trabalhos recorrentes e abrir a Exibição de Trabalho Relacionado

Para usar a Exibição de Trabalho Relacionado para solucionar problemas de trabalho recorrente, primeiro você precisa localizar o trabalho recorrente no Visual Studio e, em seguida, abrir a Exibição de Trabalho Relacionado.

### <a name="case-1-you-have-the-url-for-the-recurring-job"></a>Caso 1: você tem a URL do trabalho recorrente

Por meio de **Ferramentas > Data Lake > Exibir Trabalho**, você pode colar a URL do trabalho para abrir a Exibição de Trabalho no Visual Studio e através de Exibir de Trabalhos Relacionados para abrir a Exibição de Trabalho Relacionado.

![Exibir trabalhos relacionados das Ferramentas do Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/view-related-job.png)
 
### <a name="case-2-you-have-the-pipeline-for-the-recurring-job-but-not-the-url"></a>Caso 2: você tem o pipeline do trabalho recorrente, mas não a URL

No Visual Studio, você pode abrir o Navegador de Pipeline por meio de **Gerenciador de Servidores > sua conta do Data Lake Analytics > Pipelines** (se você não conseguir encontrar esse nó no Gerenciador de Servidores, obtenha a ferramenta mais recente [aqui](http://aka.ms/adltoolsvs)). No Navegador de Pipeline (todos os pipelines da conta do ADLA estão listados à esquerda) você pode expandir os pipelines para localizar todos os trabalhos recorrentes. Clique no que tem problemas e a Exibição de Trabalho Relacionado é aberta à direita.

![Exibir trabalhos relacionados das Ferramentas do Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/pipeline-browser.png)

![Exibir trabalhos relacionados das Ferramentas do Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-view.png)

## <a name="step-2-analyze-statistics-report"></a>Etapa 2: analisar o relatório de estatísticas

Um resumo e um relatório de estatísticas são mostrados na parte superior da Exibição de Trabalho Relacionado, por meio dos quais você pode obter a possível causa raiz do problema. 

1.  Primeiro, você precisa localizar o trabalho anormal no relatório. O eixo X mostra a hora de envio do trabalho, por meio da qual você pode localizar o trabalho anormal.
2.  Siga o processo abaixo para verificar as estatísticas e obter as informações do trabalho anormal e as possíveis soluções.

![Exibir trabalhos relacionados das Ferramentas do Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-metrics-debugging-flow.png)

## <a name="step-3-compare-the-abnormal-recurring-job-to-a-normal-job"></a>Etapa 3: comparar o trabalho recorrente anormal com um trabalho normal

Você pode encontrar todos os trabalhos recorrentes enviados, por meio da lista de trabalhos na parte inferior da Exibição de Trabalho Relacionado. Ao clicar com o botão direito do mouse, você pode comparar o trabalho anormal com um anterior que tenha sido normal a fim de localizar mais informações e as possíveis soluções na exibição Comparação de Trabalho.

![Exibir trabalhos relacionados das Ferramentas do Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/compare-job.png)

Normalmente, você precisa prestar atenção às grandes diferenças entre esses dois trabalhos, pois elas são os prováveis motivos que estão causando problemas de desempenho e você também pode seguir as etapas abaixo para fazer uma verificação adicional.

![Exibir trabalhos relacionados das Ferramentas do Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-diff-debugging-flow.png)

## <a name="next-steps"></a>Próximas etapas

* [Como depurar e resolver problemas de distorção de dados](data-lake-analytics-data-lake-tools-data-skew-solutions.md)
* [Como depurar falha de trabalho U-SQL para um erro de código definido pelo usuário](data-lake-analytics-debug-u-sql-jobs.md)