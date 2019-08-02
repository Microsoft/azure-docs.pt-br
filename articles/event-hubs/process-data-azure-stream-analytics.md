---
title: Processar dados de hubs de eventos do Azure usando Stream Analytics | Microsoft Docs
description: Este artigo mostra como processar dados do hub de eventos do Azure usando um trabalho Azure Stream Analytics.
services: event-hubs
author: spelluru
manager: ''
ms.author: spelluru
ms.date: 07/09/2019
ms.topic: article
ms.service: event-hubs
ms.openlocfilehash: 003e68b36ff71fb2991cf087ef33f72aba73a8be
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68233969"
---
# <a name="process-data-from-your-event-hub-using-azure-stream-analytics-preview"></a>Processar dados do seu hub de eventos usando Azure Stream Analytics (versão prévia)
O serviço de Azure Stream Analytics facilita a ingestão, o processamento e a análise de dados de streaming dos hubs de eventos do Azure, possibilitando ideias poderosas para impulsionar as ações em tempo real. Essa integração permite que você crie rapidamente um pipeline de análise de Hot-Path. Você pode usar o portal do Azure para visualizar dados de entrada e gravar uma consulta Stream Analytics. Quando a consulta estiver pronta, você poderá movê-la para a produção em apenas alguns cliques. 

> [!NOTE]
> Esse recurso está atualmente na visualização. 

## <a name="key-benefits"></a>Principais benefícios
Aqui estão os principais benefícios dos hubs de eventos do Azure e da integração de Azure Stream Analytics: 
- **Visualizar dados** – você pode visualizar dados de entrada de um hub de eventos no portal do Azure.
- **Testar sua consulta** – Prepare uma consulta de transformação e teste-a diretamente na portal do Azure. Para obter a sintaxe da linguagem de consulta, consulte Stream Analytics a documentação da [linguagem de consulta](/stream-analytics-query/built-in-functions-azure-stream-analytics) .
- **Implantar sua consulta em produção** – você pode implantar a consulta na produção criando e iniciando um trabalho de Azure Stream Analytics.

## <a name="end-to-end-flow"></a>Fluxo de ponta a ponta

1. Entre no [Portal do Azure](https://portal.azure.com). 
1. Navegue até o **namespace** de seus hubs de eventos e, em seguida, navegue até o **Hub de eventos**, que tem os dados de entrada. 
1. Selecione **processar dados** na página Hub de eventos.  

    ![Bloco de dados do processo](./media/process-data-azure-stream-analytics/process-data-tile.png)
1. Selecione **explorar** no bloco **Habilitar informações em tempo real de eventos** . 

    ![Selecionar Stream Analytics](./media/process-data-azure-stream-analytics/process-data-page-explore-stream-analytics.png)
1. Você verá uma página de consulta com valores já definidos para os seguintes campos:
    1. Seu **Hub de eventos** como uma entrada para a consulta.
    1. Exemplo de **consulta SQL** com instrução SELECT. 
    1. Um alias de **saída** para se referir aos resultados do teste de consulta. 

        ![Editor de consultas](./media/process-data-azure-stream-analytics/query-editor.png)
        
        > [!NOTE]
        >  Quando você usa esse recurso pela primeira vez, essa página solicita sua permissão para criar um grupo de consumidores e uma política para o Hub de eventos para visualizar os dados de entrada.
1. Selecione **criar** no painel de **visualização de entrada** , conforme mostrado na imagem anterior. 
1. Você verá imediatamente um instantâneo dos dados de entrada mais recentes nesta guia.
    - O tipo de serialização em seus dados é detectado automaticamente (JSON/CSV). Você pode alterá-lo manualmente também para JSON/CSV/AVRO.
    - Você pode visualizar os dados de entrada no formato de tabela ou no formato bruto. 
    - Se os dados mostrados não estiverem atuais, selecione **Atualizar** para ver os eventos mais recentes. 

        Aqui está um exemplo de dados no **formato de tabela**:   ![Resulta no formato de tabela](./media/process-data-azure-stream-analytics/snapshot-results.png)

        Aqui está um exemplo de dados no **formato bruto**: 

        ![Resulta no formato bruto](./media/process-data-azure-stream-analytics/snapshot-results-raw-format.png)
1. Selecione **testar consulta** para ver o instantâneo dos resultados de teste de sua consulta na guia **resultados do teste** . Você também pode baixar os resultados.

    ![Testar resultados da consulta](./media/process-data-azure-stream-analytics/test-results.png)
1. Escreva sua própria consulta para transformar os dados. Consulte [Stream Analytics referência de linguagem de consulta](/stream-analytics-query/stream-analytics-query-language-reference).
1. Depois de testar a consulta e desejar movê-la para produção, selecione **implantar consulta**. Para implantar a consulta, crie um trabalho de Azure Stream Analytics em que você pode definir uma saída para o trabalho e iniciar o trabalho. Para criar um trabalho de Stream Analytics, especifique um nome para o trabalho e selecione **criar**.

      ![Criar um trabalho do Azure Stream Analytics](./media/process-data-azure-stream-analytics/create-stream-analytics-job.png)

      > [!NOTE] 
      >  Recomendamos que você crie um grupo de consumidores e uma política para cada novo Azure Stream Analytics trabalho criado na página hubs de eventos. Os grupos de consumidores permitem apenas cinco leitores simultâneos, portanto, fornecer um grupo de consumidores dedicado para cada trabalho evitará erros que possam surgir de exceder esse limite. Uma política dedicada permite que você gire sua chave ou REVOKE permissões sem afetar outros recursos. 
1. Seu trabalho de Stream Analytics agora é criado onde sua consulta é a mesma que você testou e a entrada é seu hub de eventos. 

9.  Para concluir o pipeline, defina a **saída** da consulta e selecione **Iniciar** para iniciar o trabalho.

    > [!NOTE]
    > Antes de iniciar o trabalho, não se esqueça de substituir o outputalias pelo nome de saída que você criou em Azure Stream Analytics.

      ![Definir a saída e iniciar o trabalho](./media/process-data-azure-stream-analytics/set-output-start-job.png)


## <a name="known-limitations"></a>Limitações conhecidas
Durante o teste da consulta, os resultados do teste demoram aproximadamente 6 segundos para serem carregados. Estamos trabalhando para melhorar o desempenho do teste. No entanto, quando implantado em produção, Azure Stream Analytics terá latência de subsegundo.

## <a name="streaming-units"></a>Unidades de transmissão
Seu trabalho de Azure Stream Analytics usa como padrão três unidades de streaming (SUs). Para ajustar essa configuração, selecione **escala** no menu à esquerda na página **Stream Analytics trabalho** no portal do Azure. Para saber mais sobre unidades de streaming, confira [entender e ajustar unidades de streaming](../stream-analytics/stream-analytics-streaming-unit-consumption.md).

![Dimensionar unidades de streaming](./media/process-data-azure-stream-analytics/scale.png)

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre consultas de Stream Analytics, consulte [Stream Analytics linguagem de consulta](/stream-analytics-query/built-in-functions-azure-stream-analytics)
