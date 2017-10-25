---
title: "Como criar um trabalho de processamento de análise de dados para Stream Analytics | Microsoft Docs"
description: "Criar um trabalho de processamento de análise de dados para o Stream Analytics | segmento de roteiro de aprendizagem."
keywords: "processamento de análise de dados"
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: e825fbcf-69e9-443f-b402-3b7a4568f415
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: 05fdf1e20efd129cdfc27e1d37bc9e124edf5dcd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-a-data-analytics-processing-job-for-stream-analytics"></a>Como criar um trabalho de processamento de análise de dados para o Stream Analytics
O recurso de nível superior no Stream Analytics do Azure é um Trabalho do Stream Analytics.  Ele consiste em uma ou mais fontes de dados de entrada, uma consulta que expressa a transformação de dados, e um ou mais destinos de saída nos quais os resultados são gravados. Juntos, eles permitem ao usuário executar processamento de análise de dados para cenários de dados de streaming.

Para começar a usar o Stream Analytics, crie um novo trabalho do Stream Analytics.  Observe que essa ação não terá implicações de cobrança até que o trabalho seja iniciado.

1. Entre no [Portal clássico do Azure](http://manage.windowsazure.com) online ou no [Portal do Azure](https://portal.azure.com/).
2. No portal: **Clique em Novo** e em **Serviços de Dados** ou **Análises de Dados**, dependendo do portal, e clique em **Stream Analytics do Azure** ou **Stream Analytics** e, em seguida, clique em **Criação Rápida**.
   
   ![Assistente de trabalho de processamento de análise de dados](./media/stream-analytics-create-a-job/1-stream-analytics-create-a-job.png)  
   
   ![Criar trabalho de processamento de análise de dados](./media/stream-analytics-create-a-job/4-stream-analytics-create-a-job.png)  
3. Especifique as configurações desejadas para o trabalho do Stream Analytics.
   
   * Na caixa **Nome do Trabalho** , digite um nome para identificar o trabalho do Stream Analytics. Quando o **Nome do Trabalho** for validado, uma marca de seleção verde será exibida na caixa Nome do Trabalho. O **Nome do Trabalho** pode conter apenas caracteres alfanuméricos e o caractere “-”, e deve ter entre 3 e 63 caracteres.
   * Use **Região** ou **Local** no portal do Azure para especificar a localização geográfica em que deseja executar o trabalho.
   * Se estiver usando o Portal do Azure, selecione ou crie uma conta de armazenamento para usar como a **Conta de Armazenamento de Monitoramento Regional**. Essa conta de armazenamento é usada para armazenar dados de monitoramento de todos os trabalhos do Stream Analytics nessa região.
   * Se estiver usando o portal do Azure, especifique um **Grupo de Recursos** novo ou existente para reter recursos relacionados para seu aplicativo.
4. Depois de configurar as novas opções de trabalho do Stream Analytics, clique em **Criar Trabalho do Stream Analytics**. Pode levar alguns minutos para que o trabalho do Stream Analytics seja criado. Para verificar o status, você pode monitorar o progresso no Hub de notificações.
   
   ![Hub de notificações de trabalho de processamento de análise de dados](./media/stream-analytics-create-a-job/2-stream-analytics-create-a-job.png)  
   
   ![Análise de dados do portal do Azure processando o trabalho Criar Trabalho](./media/stream-analytics-create-a-job/5-stream-analytics-create-a-job.png)  
5. O novo trabalho mostrará um status **Criado**. Observe que o botão **Iniciar** está desabilitado. Configure a entrada, a consulta e a saída do trabalho antes de iniciá-lo.
   
   ![Status do trabalho de processamento de análise de dados](./media/stream-analytics-create-a-job/3-stream-analytics-create-a-job.png)  
   
   ![Status do trabalho de processamento de análise de dados no portal do Azure](./media/stream-analytics-create-a-job/6-stream-analytics-create-a-job.png)  

## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente nosso [Fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

