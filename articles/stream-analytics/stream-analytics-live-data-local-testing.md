---
title: Testar dados dinâmicos com o Azure Stream Analytics para Visual Studio
description: Saiba como testar seu trabalho do Azure Stream Analytics localmente usando dados de streaming ao vivo.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: ea55d2f96a87503d43a69d288ce85dcff32a39ce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61479727"
---
# <a name="test-live-data-locally-using-azure-stream-analytics-tools-for-visual-studio-preview"></a>Testar dados dinâmicos localmente usando as ferramentas do Azure Stream Analytics para Visual Studio (Versão Prévia)

Ferramentas do Azure Stream Analytics para Visual Studio permitem que você teste o trabalhos localmente do IDE usando fluxos de eventos dinâmicos do Hub de Eventos, do Hub IoT e do Armazenamento de Blobs do Azure. Testes locais de dados dinâmicos não podem substituir o [teste de desempenho e escalabilidade](stream-analytics-streaming-unit-consumption.md) que você pode executar na nuvem, mas você pode economizar tempo durante o teste funcional ao não precisar enviar para a nuvem cada vez que deseja testar seu trabalho do Stream Analytics. Esse recurso está em versão prévia e não deve ser usado para cargas de trabalho de produção.

## <a name="testing-options"></a>Opções de teste

Há suporte para as seguintes opções de testes locais:

|**Input**  |**Saída**  |**Tipo de Trabalho**  |
|---------|---------|---------|
|Dados estáticos locais   |  Dados estáticos locais   |   Nuvem/Borda |
|Dados de entrada dinâmicos   |  Dados estáticos locais   |   Nuvem |
|Dados de entrada dinâmicos   |  Dados de saída dinâmicos   |   Nuvem |

## <a name="local-testing-with-live-data"></a>O teste local usando dados dinâmicos

1. Depois de criar um [projeto de nuvem do Azure Stream Analytics no Visual Studio](stream-analytics-quick-create-vs.md), abra **script.asaql**. O teste local usa entrada e saída locais por padrão.

   ![Entrada e saída local do Visual Studio no Azure Stream Analytics](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-local-input-output.png)

2. Para testar dados dinâmicos, escolha **Usar Entrada de Nuvem** na caixa suspensa.

   ![Entrada de nuvem dinâmica do Visual Studio no Azure Stream Analytics](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input.png)


3. Defina **Hora de Início** para definir quando o trabalho começará a processar dados de entrada. O trabalho pode precisar ler dados de entrada antecipadamente para garantir resultados precisos. O tempo padrão é definido como 30 minutos depois da hora atual.

   ![Hora de início de dados dinâmicos do Visual Studio no Azure Stream Analytics](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-start-time.png)

4. Clique em **Executar Localmente**. Uma janela do console será exibida com as métricas de trabalho e progresso em execução. Se você quiser interromper o processo, poderá fazer isso manualmente. 

   ![Janela de processo de dados dinâmicos do Visual Studio no Azure Stream Analytics](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-process-window.png)

   Os resultados de saída são atualizados a cada três segundos com as primeiras 500 linhas de saída na janela de resultados da execução local e os arquivos de saída são colocados na pasta **ASALocalRun** do caminho do projeto. Você também pode abrir os arquivos de saída clicando no botão **Abrir Pasta de Resultados** na janela de resultados da execução local.

   ![Pasta de resultados aberta de dados dinâmicos do Visual Studio no Azure Stream Analytics](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-open-results-folder.png)

5. Se você quiser enviar os resultados para seus Coletores de saída de nuvem, escolha **Saída para a nuvem** na segunda caixa suspensa. O Power BI e o Azure Data Lake Storage não são coletores de saída com suporte.

   ![Saída de dados dinâmicos para a nuvem do Visual Studio no Azure Stream Analytics](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-output.png)
 
## <a name="limitations"></a>Limitações

* O Power BI e Azure Data Lake Storage não são coletores de saída com suporte devido a limitações do modelo de autenticação.

* Somente opções de entrada de nuvem têm suporte para [políticas de tempo](stream-analytics-out-of-order-and-late-events.md), as opções de entrada locais, não.

## <a name="next-steps"></a>Próximas etapas

* [Criar um trabalho do Stream Analytics usando ferramentas do Azure Stream Analytics para Visual Studio](stream-analytics-quick-create-vs.md)
* [Instalar o Azure Stream Analytics Tools para o Microsoft Visual Studio](stream-analytics-tools-for-visual-studio-install.md)
* [Testar as consultas do Stream Analytics localmente com o Microsoft Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Use o Microsoft Visual Studio para visualizar os trabalhos do Azure Stream Analytics](stream-analytics-vs-tools.md)