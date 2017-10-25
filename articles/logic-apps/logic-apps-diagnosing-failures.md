---
title: "Diagnosticar Falhas – Aplicativo Lógico do Azure | Microsoft Docs"
description: "Maneiras comuns de entender onde os aplicativos lógicos estão falhando"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: a6727ebd-39bd-4298-9e68-2ae98738576e
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 814e6f93088cdd96b0a663d2a7494b5a11470d99
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="diagnose-logic-app-failures"></a>Diagnosticar falhas nos aplicativos lógicos
Se você tiver problemas ou falhas com os aplicativos lógicos, há algumas abordagens que poderão ajudar a entender melhor a origem das falhas.  

## <a name="azure-portal-tools"></a>Ferramentas do portal do Azure
O Portal do Azure fornece várias ferramentas para diagnosticar cada aplicativo lógico em cada etapa.

### <a name="trigger-history"></a>Histórico de gatilho

Cada aplicativo lógico tem pelo menos um gatilho. Se você perceber que os aplicativos não estão sendo acionados, procure mais informações no histórico de gatilho. Você pode acessar o histórico de gatilhos na folha principal do aplicativo lógico.

![Exibir o histórico de gatilho][1]

O histórico de gatilho lista todas as tentativas de gatilho feitas pelo seu aplicativo lógico. Você pode clicar em cada tentativa de gatilho para ver os detalhes, mais especificamente, entradas ou saídas geradas pela tentativa de gatilho. Se você encontrar gatilhos com falha, selecione a tentativa de gatilho e escolha o link **Saídas** para examinar mensagens de erro geradas, por exemplo, para credenciais FTP que não são válidas.

Os diferentes status que você pode ver são:

* **Ignorado**. O ponto de extremidade foi sondado para procurar por dados e recebeu uma resposta de que nenhum dado estava disponível.
* **Êxito**. O gatilho recebeu uma resposta de que havia dados disponíveis. Esse status pode ser resultado de um gatilho manual, um gatilho de recorrência ou um gatilho de sondagem. Esse status geralmente está acompanhado pelo status **Disparado**, mas poderá não estar se você tiver uma condição ou um comando SplitOn no modo de exibição de código que não foi atendido.
* **Falha**. Um erro foi gerado.

#### <a name="start-a-trigger-manually"></a>Iniciar um gatilho manualmente

Se você quiser que o aplicativo lógico verifique imediatamente se há um gatilho disponível (sem aguardar a próxima recorrência), clique no botão **Selecionar Gatilho** na folha principal para forçar uma verificação. Por exemplo, clicar nesse link com um gatilho Dropbox fará com que o fluxo de trabalho sonde o Dropbox imediatamente em busca de novos arquivos.

### <a name="run-history"></a>Histórico da execução

Cada gatilho acionado resulta em uma execução. Você pode acessar informações de execução da folha principal, que contém muitos detalhes que podem ajudá-lo a entender o que aconteceu durante o fluxo de trabalho.

![Localizando o histórico da execução][2]

Uma execução exibe um dos seguintes status:

* **Êxito**. Todas as ações foram bem sucedidas. Se uma falha ocorreu, ela foi tratada por uma ação que ocorreu posteriormente no fluxo de trabalho. Ou seja, a falha foi tratada por uma ação definida para ser executada depois de uma ação com falha.
* **Falha**. Pelo menos uma ação teve uma falha que não foi tratada por uma ação posterior no fluxo de trabalho.
* **Cancelado**. O fluxo de trabalho estava em execução, mas recebeu uma solicitação de cancelamento.
* **Executando**. O fluxo de trabalho está em execução atualmente. Esse status pode ocorrer para fluxos de trabalho limitados ou por conta do plano de preços atual. Para ver mais detalhes, confira os [limites da ação na página de preços](https://azure.microsoft.com/pricing/details/app-service/plans/). Configurar o diagnóstico (os gráficos que aparecem embaixo do histórico da execução) também podem fornecer informações sobre quaisquer eventos de restrição ocorridos.

Quando você estiver observando um histórico da execução, você pode analisar para obter mais detalhes.  

#### <a name="trigger-outputs"></a>Saídas do gatilho

As saídas do gatilho mostrarão os dados recebidos do gatilho. Essas saídas podem ajudá-lo a determinar se todas as propriedades retornaram conforme o esperado.

> [!NOTE]
> Se você vir algum conteúdo que não consegue compreender, aprenda como o Aplicativo Lógico do Azure [lida com diferentes tipos de conteúdo](../logic-apps/logic-apps-content-type.md).
> 

![Exemplos de saída do gatilho][3]

#### <a name="action-inputs-and-outputs"></a>Entradas e saídas da ação

Você pode analisar as entradas e saídas que uma ação recebeu. Esses dados são úteis para entender o tamanho e a forma das saídas e para encontrar todas as mensagens de erro que possam ter sido geradas.

![Entradas e saídas da ação][4]

## <a name="debug-workflow-runtime"></a>Tempo de execução do fluxo de trabalho de depuração

Juntamente com o monitoramento de entradas, saídas e gatilhos de uma execução, você pode adicionar algumas etapas ao fluxo de trabalho para ajudar na depuração. 
[RequestBin](http://requestb.in) é uma ferramenta poderosa que você pode adicionar como uma etapa em um fluxo de trabalho. Usando o RequestBin, você pode configurar um inspetor de solicitação HTTP para determinar exatamente o tamanho, forma e formato de uma solicitação HTTP. Você pode criar um RequestBin e colar a URL em uma Ação HTTP POST do aplicativo lógico com qualquer conteúdo do corpo que você deseje testar, por exemplo, uma expressão ou outra saída da etapa. Depois de executar o aplicativo lógico, você poderá atualizar o RequestBin para ver como a solicitação foi formada quando gerada do mecanismo dos Aplicativos Lógicos.

<!-- image references -->
[1]: ./media/logic-apps-diagnosing-failures/triggerhistory.png
[2]: ./media/logic-apps-diagnosing-failures/runhistory.png
[3]: ./media/logic-apps-diagnosing-failures/triggeroutputslink.png
[4]: ./media/logic-apps-diagnosing-failures/actionoutputs.png
