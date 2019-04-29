---
title: Testar um trabalho do Azure Stream Analytics com dados de exemplo
description: Este artigo descreve como usar o portal do Azure para testar um trabalho do Azure Stream Analytics, uma entrada de exemplo e fazer o upload de dados de exemplo.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: fca76b632e9bcc27ed762886eaea696a5696ad3f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60761823"
---
# <a name="test-a-stream-analytics-query-with-sample-data"></a>Testar uma consulta do Stream Analytics com dados de exemplo

Usando o Azure Stream Analytics, é possível carregar dados de exemplo e testar consultas no portal do Azure sem iniciar ou interromper um trabalho.

## <a name="upload-sample-data-and-test-the-query"></a>Carregar dados de exemplo e testar a consulta

1. Entre no Portal do Azure. 

2. Localize o trabalho existente do Stream Analytics e selecione-o.

3. Na página de trabalho do Stream Analytics, sob o título **Topologia de Trabalho**, selecione **Consulta** para abrir a janela Editor de consultas. 

4. Para testar a consulta com dados de entrada de exemplo, clique com o botão direito do mouse em qualquer uma das entradas.  Em seguida, selecione **Carregar dados de exemplo do arquivo**. Os dados devem ser serializados em JSON, CSV ou AVRO. A entrada de exemplo deve ser codificada em UTF-8 e não deve ser compactada. O delimitador somente de vírgula (,) dá suporte para teste da entrada do CSV no portal.

    ![consulta de teste do editor de consultas do stream analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

5. Depois que o upload for concluído, selecione **Teste** para testar essa consulta em relação aos dados de exemplo que você forneceu.

    ![dados de exemplo de teste do editor de consultas do stream analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

6. Se você precisar testar a saída do teste para uso posterior, a saída da consulta será exibida no navegador com um link para os resultados do download. 

7. Modifique iterativamente sua consulta e teste-a novamente para ver como a saída muda.

   ![A saída do exemplo do editor de consultas do Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

   Quando você usa várias saídas em uma consulta, os resultados são mostrados em guias separadas, e você pode alternar facilmente entre elas.

8. Depois de verificar os resultados mostrados no navegador, **Salve** sua consulta. Em seguida, **Inicie** o trabalho e permita que ele processe os eventos de entrada.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
