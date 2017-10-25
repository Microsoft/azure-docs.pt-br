---
title: "Como configurar saídas de dados para trabalhos do Stream Analytics | Microsoft Docs"
description: "Configurar saídas para trabalhos do Stream Analytics | segmento do roteiro de aprendizagem."
keywords: "dados de saída, movimentação de dados"
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 3bbea3da-bfce-4af1-a15e-d4b23874034f
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/26/2017
ms.author: samacha
ms.openlocfilehash: 1ffa517469da1a8d79917b9747abc97ca3bef463
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-data-outputs-for-stream-analytics-jobs"></a>Como configurar saídas de dados para trabalhos do Stream Analytics

Os trabalhos do Stream Analytics do Azure podem ser conectados a uma ou mais saídas de dados, o que define uma conexão com um coletor de dados existente. Conforme o trabalho do Stream Analytics processa e transforma dados de entrada, um fluxo de eventos de saída de dados é gravado na saída do trabalho.

As saídas de dados do Stream Analytics podem ser usadas para dar origem a painéis ou alertas em tempo real, disparar fluxos de trabalho de movimentação de dados ou simplesmente arquivar dados para processamento em lote posteriormente. O Stream Analytics integra-se perfeitamente a vários serviços do Azure, que são documentados em detalhes aqui.

Para adicionar uma saída ao trabalho do Stream Analytics:

1. No [Portal do Azure](https://portal.azure.com), abra o seu trabalho e clique em **Saídas** e, em seguida, clique em **Adicionar** na folha Saídas exibida.
   
    ![Adicionar saídas](./media/stream-analytics-add-outputs/1-stream-analytics-add-outputs.png)  
   
2. Forneça um nome amigável para essa saída na caixa **Alias de saída** . Esse nome pode ser usado na consulta do seu trabalho posteriormente para fazer referência à saída.  
   
    Preencha o restante das propriedades de conexão necessárias para se conectar à saída.  Esses campos variam de acordo com o tipo de saída e são definidos em detalhes aqui.  
   
    ![Escolher o tipo de movimentação de dados](./media/stream-analytics-add-outputs/2-stream-analytics-add-outputs.png)  
   
3. Dependendo do tipo de saída, talvez seja necessário especificar como os dados são serializados ou formatados. As configurações específicas de serialização para cada tipo de saída estão documentadas aqui.
   
    Preencha o restante das propriedades de conexão necessárias para se conectar à fonte de dados. Esses campos variam de acordo com o tipo de entrada e de fonte e são definidos detalhadamente no artigo [Criar trabalho](stream-analytics-create-a-job.md).  

> [!Note]
>
> Qualquer elemento de saída adicionado ao trabalho deve existir antes de o trabalho ser iniciado e de os eventos começarem a fluir. Por exemplo, se você usar o Armazenamento de Blobs como uma saída, o trabalho não criará uma conta de armazenamento automaticamente. Ele precisa ser criado pelo usuário antes de o trabalho ASA ser iniciado.
> 
 

## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente nosso [Fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

