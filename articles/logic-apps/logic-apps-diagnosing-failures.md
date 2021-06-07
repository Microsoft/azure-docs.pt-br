---
title: Solucionar problemas e diagnosticar falhas de fluxo de trabalho
description: Saiba como solucionar e diagnosticar problemas, erros e falhas em seus fluxos de trabalho em aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 1f83f13564a64a0d9d8a5e0144ca95af6a769d6c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95995039"
---
# <a name="troubleshoot-and-diagnose-workflow-failures-in-azure-logic-apps"></a>Solucionar problemas e diagnosticar falhas de fluxo de trabalho nos Aplicativos Lógicos do Azure

Seu aplicativo lógico gera informações que podem ajudar você a diagnosticar e depurar problemas nele. É possível diagnosticar um aplicativo lógico revisando cada etapa no fluxo de trabalho por meio do Portal do Azure. Ou então, você pode adicionar algumas etapas a um fluxo de trabalho para depuração em tempo de execução.

<a name="check-trigger-history"></a>

## <a name="check-trigger-history"></a>Verificar histórico de gatilho

Cada execução de aplicativo lógico começa com uma tentativa de gatilho, portanto, se o gatilho não disparar, siga estas etapas:

1. Verifique o status do gatilho [verificando o histórico do gatilho](../logic-apps/monitor-logic-apps.md#review-trigger-history). Para exibir mais informações sobre a tentativa do gatilho, selecione esse evento de gatilho, por exemplo:

   ![Exibir status e histórico do gatilho](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

1. Verifique as entradas do gatilho para confirmar que elas aparecem conforme o esperado. No **link entradas**, selecione o link, que mostra o painel **entradas** .

   As entradas do gatilho incluem os dados que o gatilho espera e exigem para iniciar o fluxo de trabalho. A revisão dessas entradas pode ajudá-lo a determinar se as entradas do gatilho estão corretas e se a condição foi atendida para que o fluxo de trabalho possa continuar.

   Por exemplo, a `feedUrl` Propriedade aqui tem um valor de RSS feed incorreto:

   ![Examinar entradas de gatilho para erros](./media/logic-apps-diagnosing-failures/review-trigger-inputs-for-errors.png)

1. Verifique as saídas de gatilhos, se houver, para confirmar que elas aparecem conforme o esperado. No **link de saídas**, selecione o link, que mostra o painel de **saídas** .

   As saídas de gatilho incluem os dados que o gatilho passa para a próxima etapa no fluxo de trabalho. A revisão dessas saídas pode ajudá-lo a determinar se os valores corretos ou esperados foram passados para a próxima etapa do fluxo de trabalho, por exemplo:

   ![Examinar saídas de gatilho de erros](./media/logic-apps-diagnosing-failures/review-trigger-outputs-for-errors.png)

   > [!TIP]
   > Se você encontrar algum conteúdo que não reconheça, saiba mais sobre [diferentes tipos de conteúdo](../logic-apps/logic-apps-content-type.md) em aplicativos lógicos do Azure.

<a name="check-runs-history"></a>

## <a name="check-runs-history"></a>Verificar histórico de execuções

Cada vez que o gatilho é acionado para um item ou evento, o mecanismo de aplicativos lógicos cria e executa uma instância de fluxo de trabalho separada para cada item ou evento. Se uma execução falhar, siga estas etapas para examinar o que aconteceu durante essa execução, incluindo o status de cada etapa no fluxo de trabalho, além das entradas e saídas de cada etapa.

1. Verifique o status de execução do fluxo de trabalho [verificando o histórico de execuções](../logic-apps/monitor-logic-apps.md#review-runs-history). Para exibir mais informações sobre uma execução com falha, incluindo todas as etapas em que são executadas em seu status, selecione a execução com falha.

   ![Exibir o histórico de execução e selecionar execução com falha](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

1. Depois que todas as etapas na execução forem exibidas, expanda a primeira etapa com falha.

   ![Expandir a primeira etapa com falha](./media/logic-apps-diagnosing-failures/logic-app-run-pane.png)

1. Verifique as entradas da etapa com falha para confirmar se elas aparecem conforme o esperado.

1. Examine os detalhes de cada etapa em uma execução específica. No **Histórico de execuções**, selecione a execução que você deseja examinar.

   ![Examinar o histórico de execuções](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

   ![Exibir os detalhes de uma execução de aplicativo lógico](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

1. Para examinar as entradas, saídas e as mensagens de erro para uma etapa específica, escolha essa etapa para que a forma se expanda e mostre os detalhes. Por exemplo:

   ![Exibir detalhes da etapa](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>Realizar depuração de runtime

Para ajudar na depuração, você pode adicionar etapas de diagnóstico a um fluxo de trabalho do aplicativo lógico, juntamente com a revisão do gatilho e do histórico de execuções. Por exemplo, você pode adicionar etapas que usam o serviço [Webhook Tester](https://webhook.site/) para poder inspecionar solicitações HTTP e determinar seu tamanho, forma e formato exatos.

1. Vá para o site do [testador do webhook](https://webhook.site/) e copie a URL exclusiva gerada.

1. Em seu aplicativo lógico, adicione uma ação HTTP POST e o conteúdo do corpo que você deseja testar, por exemplo, uma expressão ou outra saída de etapa.

1. Cole a URL do testador de webhook na ação HTTP POST.

1. Para examinar como uma solicitação é formada quando gerada no mecanismo de aplicativos lógicos, execute o aplicativo lógico e visite novamente o site do testador de webhook para obter mais detalhes.

## <a name="next-steps"></a>Próximas etapas

* [Monitorar seu aplicativo lógico](../logic-apps/monitor-logic-apps.md)
