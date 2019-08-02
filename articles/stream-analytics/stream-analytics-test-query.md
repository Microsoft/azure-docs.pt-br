---
title: Testar um trabalho do Azure Stream Analytics com dados de exemplo
description: Este artigo descreve como usar o portal do Azure para testar um trabalho do Azure Stream Analytics, uma entrada de exemplo e fazer o upload de dados de exemplo.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 6/21/2019
ms.custom: seodec18
ms.openlocfilehash: ad0e0ca75bf3d3a8d9d1029d42f8609b3c4c627b
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620822"
---
# <a name="test-a-stream-analytics-query-with-sample-data"></a>Testar uma consulta do Stream Analytics com dados de exemplo

Usando o Azure Stream Analytics, você pode amostras de dados de entradas ou carregar dados de exemplo para testar consultas no portal do Azure sem iniciar ou interromper um trabalho.

## <a name="upload-or-sample-data-from-a-live-source-to-test-the-query"></a>Carregar ou dados de exemplo de uma fonte ao vivo para testar a consulta

1. Entre no Portal do Azure. 

2. Localize o trabalho existente do Stream Analytics e selecione-o.

3. Na página de trabalho do Stream Analytics, sob o título **Topologia de Trabalho**, selecione **Consulta** para abrir a janela Editor de consultas. 

4. Para testar sua consulta você pode, em seguida, ou dados de exemplo de uma entrada ao vivo ou carregamento de um arquivo. Os dados devem ser serializados em JSON, CSV ou AVRO. A entrada de exemplo deve ser codificada em UTF-8 e não deve ser compactada. O delimitador somente de vírgula (,) dá suporte para teste da entrada do CSV no portal.

    1. Usando a entrada ao vivo: clique com botão direito em qualquer uma de suas entradas. Em seguida, selecione **dados de exemplo da entrada**. Na próxima tela, você pode definir a duração da amostra. Eventos de uma fonte ao vivo de amostragem recuperará até 1000 eventos ou 1 MB (o que vier primeiro), para que os dados amostrados podem não representar o intervalo de tempo integral especificado.

    1. Usando o arquivo: clique com botão direito em qualquer uma de suas entradas. Em seguida, selecione **Carregar dados de exemplo do arquivo**. 

    ![consulta de teste do editor de consultas do stream analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

5. Depois que a amostragem ou o upload for concluído, selecione **testar** para testar essa consulta em relação as dados de exemplo que você forneceu.

    ![dados de exemplo de teste do editor de consultas do stream analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

6. Se você precisar testar a saída do teste para uso posterior, a saída da consulta será exibida no navegador com um link para os resultados do download. 

7. Modifique iterativamente sua consulta e teste-a novamente para ver como a saída muda.

   ![A saída do exemplo do editor de consultas do Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

   Quando você usa várias saídas em uma consulta, os resultados são mostrados em guias separadas, e você pode alternar facilmente entre elas.

8. Depois de verificar os resultados mostrados no navegador, **Salve** sua consulta. Em seguida, **Inicie** o trabalho e permita que ele processe os eventos de entrada.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
