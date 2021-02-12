---
title: Trocar mensagens com o barramento de serviço do Azure
description: Criar tarefas e fluxos de trabalho automatizados que enviam e recebem mensagens usando o barramento de serviço do Azure nos aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 10/22/2020
tags: connectors
ms.openlocfilehash: b6276ff940d8b156a671cb5386ce53ede30dd879
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019695"
---
# <a name="exchange-messages-in-the-cloud-by-using-azure-logic-apps-and-azure-service-bus"></a>Trocar mensagens na nuvem usando os aplicativos lógicos do Azure e o barramento de serviço do Azure

Com os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) e o conector do [barramento de serviço do Azure](../service-bus-messaging/service-bus-messaging-overview.md) , você pode criar tarefas automatizadas e fluxos de trabalho que transferem dados, como ordens de venda e compra, diários e movimentações de estoque entre aplicativos para sua organização. Além de monitorar, enviar e gerenciar mensagens, o conector também realiza ações com filas, sessões, tópicos, assinaturas e assim por diante; por exemplo:

* Monitorar quando mensagens chegam (preenchimento automático) ou são recebidas (peek-lock) em filas, tópicos e assinaturas de tópicos.
* Enviar mensagens.
* Criar e excluir assinaturas de tópicos.
* Gerenciar mensagens em filas e assinaturas de tópicos; por exemplo, get, get deferred, complete, defer, abandon e dead-letter.
* Renovar bloqueios em mensagens e sessões nas filas e assinaturas de tópicos.
* Fechar sessões em filas e tópicos.

Você pode usar gatilhos para obter respostas do Barramento de Serviço e disponibilizar a saída para outras ações em seus aplicativos lógicos. Você também pode fazer com que outras ações usem a saída das ações do Barramento de Serviço. Se você for novo no barramento de serviço e nos aplicativos lógicos, examine [o que é o barramento de serviço do Azure?](../service-bus-messaging/service-bus-messaging-overview.md) e [o que são os aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md)?

[!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta e uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Um namespace de Barramento de Serviço e a entidade de mensagens, como uma fila. Esses itens e seu aplicativo lógico precisam usar a mesma assinatura do Azure. Se você não tiver esses itens, aprenda a [criar seu namespace do Barramento de Serviço e uma fila](../service-bus-messaging/service-bus-create-namespace-portal.md).

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico em que você usa o namespace do barramento de serviço e a entidade de mensagens. Seu aplicativo lógico e o barramento de serviço precisam usar a mesma assinatura do Azure. Para iniciar o fluxo de trabalho com um gatilho do barramento de serviço, [crie um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar uma ação do barramento de serviço em seu fluxo de trabalho, inicie seu aplicativo lógico com outro gatilho, por exemplo, o [gatilho de recorrência](../connectors/connectors-native-recurrence.md).

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>Verificar permissões

Confirme se seu aplicativo lógico tem permissões para acessar o namespace do Barramento de Serviço.

1. Na [portal do Azure](https://portal.azure.com), entre com sua conta do Azure.

1. Acesse o *namespace* do seu Barramento de Serviço. Na página do namespace, em **Configurações**, selecione **Políticas de acesso compartilhado**. Em **declarações**, verifique se você tem permissões de **Gerenciamento** para esse namespace.

   ![Gerenciar permissões para namespace do barramento de serviço](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

1. Obtenha a cadeia de conexão para o namespace do Barramento de Serviço. Você precisa dessa cadeia de caracteres ao fornecer as informações de conexão em seu aplicativo lógico.

   1. No painel **políticas de acesso compartilhado** , selecione **RootManageSharedAccessKey**.

   1. Ao lado de sua cadeia de conexão primária, selecione o botão Copiar. Salve a cadeia de conexão para uso posterior.

      ![Copiar a cadeia de conexão do namespace do Barramento de Serviço](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > Para confirmar se a cadeia de conexão está associada ao namespace do Barramento de Serviço ou a uma entidade de sistema de mensagens, como uma fila, pesquise a cadeia de conexão para o parâmetro `EntityPath`. Se encontrar esse parâmetro, a cadeia de conexão servirá para uma entidade específica e não será a cadeia correta a ser usada com seu aplicativo lógico.

## <a name="add-service-bus-trigger"></a>Adicionar gatilho do barramento de serviço

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com)e abra seu aplicativo lógico em branco no designer do aplicativo lógico.

1. Na caixa de pesquisa do portal, digite `azure service bus` . Na lista de gatilhos que aparece, selecione o gatilho desejado.

   Por exemplo, para disparar seu aplicativo lógico quando um novo item é enviado para uma fila do barramento de serviço, selecione o gatilho **quando uma mensagem é recebida em uma fila (preenchimento automático)** .

   ![Selecionar um gatilho do Barramento de Serviço](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   Aqui estão algumas considerações sobre quando você usa um gatilho do barramento de serviço:

   * Todos os gatilhos do barramento de serviço são gatilhos *de sondagem longa* . Essa descrição significa que, quando o gatilho é acionado, o gatilho processa todas as mensagens e aguarda 30 segundos para que mais mensagens apareçam na fila ou na assinatura do tópico. Se nenhuma mensagem aparecer em 30 segundos, a execução do gatilho será ignorada. Caso contrário, o gatilho continuará lendo as mensagens até que a fila ou a assinatura do tópico esteja vazia. A próxima sondagem de gatilho é baseada no intervalo de recorrência especificado nas propriedades do gatilho.

   * Alguns gatilhos, como **quando uma ou mais mensagens chegam em um gatilho de fila (conclusão automática)** , podem retornar uma ou mais mensagens. Quando esses gatilhos são acionados, eles retornam entre um e o número de mensagens que é especificado pela propriedade **contagem máxima de mensagens** do gatilho.

     > [!NOTE]
     > O gatilho de preenchimento automático conclui automaticamente uma mensagem, mas a conclusão ocorre apenas na próxima chamada para o barramento de serviço. Esse comportamento pode afetar o design do seu aplicativo lógico. Por exemplo, evite alterar a simultaneidade no gatilho de preenchimento automático porque essa alteração pode resultar em mensagens duplicadas se o aplicativo lógico entrar em um estado limitado. A alteração do controle de simultaneidade cria estas condições: os gatilhos limitados são ignorados com o `WorkflowRunInProgress` código, a operação de conclusão não acontecerá e a próxima execução do gatilho ocorrerá após o intervalo de sondagem. Você precisa definir a duração do bloqueio do barramento de serviço para um valor maior que o intervalo de sondagem. No entanto, apesar dessa configuração, a mensagem ainda pode não ser concluída se seu aplicativo lógico permanecer em um estado limitado no próximo intervalo de sondagem.

   * Se você [ativar a configuração de simultaneidade](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) para um gatilho do barramento de serviço, o valor padrão da `maximumWaitingRuns` propriedade será 10. Com base na configuração de duração de bloqueio da entidade do barramento de serviço e na duração da execução da instância do aplicativo lógico, esse valor padrão pode ser muito grande e pode causar uma exceção de "bloqueio perdido". Para encontrar o valor ideal para seu cenário, comece a testar com um valor de 1 ou 2 para a `maximumWaitingRuns` propriedade. Para alterar o valor máximo de execuções em espera, consulte [alterar limite de execuções em espera](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs).

1. Se o seu gatilho estiver se conectando ao seu namespace do barramento de serviço pela primeira vez, siga estas etapas quando o designer do aplicativo lógico solicitar informações de conexão.

   1. Forneça um nome para sua conexão e selecione o namespace do seu Barramento de Serviço.

      ![Captura de tela que mostra fornecer o nome da conexão e selecionar o namespace do barramento de serviço](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-1.png)

      Para inserir manualmente a cadeia de conexão, selecione **inserir manualmente as informações de conexão**. Caso não tenha uma cadeia de conexão, aprenda [como localizar sua cadeia de conexão](#permissions-connection-string).

   1. Selecione a política do barramento de serviço e selecione **criar**.

      ![Captura de tela que mostra a seleção da política do barramento de serviço](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-2.png)

   1. Selecione a entidade de mensagens que você deseja, como uma fila ou um tópico. Para este exemplo, selecione a fila do barramento de serviço.
   
      ![Captura de tela que mostra a seleção da fila do barramento de serviço](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-trigger.png)

1. Forneça as informações necessárias para o gatilho selecionado. Para adicionar outras propriedades disponíveis à ação, abra a lista **Adicionar novo parâmetro** e selecione as propriedades desejadas.

   Para o gatilho deste exemplo, selecione o intervalo de sondagem e a frequência para verificar a fila.

   ![Captura de tela que mostra a configuração do intervalo de sondagem no gatilho do barramento de serviço](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

   Para obter mais informações sobre gatilhos e propriedades disponíveis, consulte a [página de referência](/connectors/servicebus/)do conector.

1. Continue criando seu aplicativo lógico adicionando as ações desejadas.

   Por exemplo, pode adicionar uma ação que envia um email quando uma nova mensagem chega. Quando o gatilho verifica sua fila e localiza uma nova mensagem, seu aplicativo lógico executa as ações selecionadas para a mensagem encontrada.

## <a name="add-service-bus-action"></a>Adicionar ação do barramento de serviço

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. No [portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer do aplicativo lógico.

1. Na etapa em que você deseja adicionar uma ação, selecione **nova etapa**.

   Ou, para adicionar uma ação entre etapas, mova o ponteiro sobre a seta entre essas etapas. Selecione o sinal de adição ( **+** ) que aparece e selecione **Adicionar uma ação**.

1. Em **Escolher uma ação**, na caixa de pesquisa, insira `azure service bus`. Na lista de ações exibida, selecione a ação desejada. 

   Para este exemplo, selecione a ação **Enviar mensagem** .

   ![Captura de tela que mostra a seleção da ação do barramento de serviço](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. Se sua ação estiver se conectando ao namespace do barramento de serviço pela primeira vez, siga estas etapas quando o designer do aplicativo lógico solicitar informações de conexão.

   1. Forneça um nome para sua conexão e selecione o namespace do seu Barramento de Serviço.

      ![Captura de tela que mostra o fornecimento de um nome de conexão e seleção de um namespace do barramento de serviço](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-1.png)

      Para inserir manualmente a cadeia de conexão, selecione **inserir manualmente as informações de conexão**. Caso não tenha uma cadeia de conexão, aprenda [como localizar sua cadeia de conexão](#permissions-connection-string).

   1. Selecione a política do barramento de serviço e selecione **criar**.

      ![Captura de tela que mostra a seleção de uma política do barramento de serviço e a seleção do botão criar](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-2.png)

   1. Selecione a entidade de mensagens que você deseja, como uma fila ou um tópico. Para este exemplo, selecione a fila do barramento de serviço.

      ![Captura de tela que mostra a seleção de uma fila do barramento de serviço](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-action.png)

1. Forneça os detalhes necessários para a ação selecionada. Para adicionar outras propriedades disponíveis à ação, abra a lista **Adicionar novo parâmetro** e selecione as propriedades desejadas.

   Por exemplo, selecione as propriedades **conteúdo** e **tipo de conteúdo** para adicioná-las à ação. Em seguida, especifique o conteúdo para a mensagem que você deseja enviar.

   ![Captura de tela que mostra como fornecer o tipo de conteúdo e os detalhes da mensagem](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

   Para obter mais informações sobre as ações disponíveis e suas propriedades, consulte a [página de referência](/connectors/servicebus/)do conector.

1. Continue criando seu aplicativo lógico adicionando outras ações que desejar.

   Por exemplo, você pode adicionar uma ação que envia email para confirmar que sua mensagem foi enviada.

1. Salve seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

<a name="sequential-convoy"></a>

## <a name="send-correlated-messages-in-order"></a>Enviar mensagens correlacionadas na ordem

Quando você precisar enviar mensagens relacionadas em uma ordem específica, poderá usar o [padrão *comboio sequencial*](/azure/architecture/patterns/sequential-convoy) usando o conector do [barramento de serviço do Azure](../connectors/connectors-create-api-servicebus.md). As mensagens correlacionadas têm uma propriedade que define a relação entre essas mensagens, como a ID da [sessão](../service-bus-messaging/message-sessions.md) no barramento de serviço.

Ao criar um aplicativo lógico, você pode selecionar o modelo **entrega em ordem correlacionada usando sessões do barramento de serviço** , que implementa o padrão comboio sequencial. Para obter mais informações, consulte [enviar mensagens relacionadas na ordem](../logic-apps/send-related-messages-sequential-convoy.md).

## <a name="delays-in-updates-to-your-logic-app-taking-effect"></a>Atrasos em atualizações para seu aplicativo lógico entrar em vigor

Se o intervalo de sondagem de um gatilho do barramento de serviço for pequeno, como 10 segundos, as atualizações para seu aplicativo lógico poderão não entrar em vigor por até 10 minutos. Para contornar esse problema, você pode aumentar temporariamente o intervalo de sondagem para um valor maior, como 30 segundos ou 1 minuto, antes de atualizar seu aplicativo lógico. Depois de fazer a atualização, você pode redefinir o intervalo de sondagem para o valor original. 

<a name="connector-reference"></a>

## <a name="connector-reference"></a>Referência de conector

De um barramento de serviço, o conector do barramento de serviço pode economizar até 1.500 sessões exclusivas de cada vez no cache do conector, por [entidade de mensagens do barramento de serviço, como uma assinatura ou um tópico](../service-bus-messaging/service-bus-queues-topics-subscriptions.md). Se a contagem de sessões exceder esse limite, as sessões antigas serão removidas do cache. Para obter mais informações, consulte [sessões de mensagens](../service-bus-messaging/message-sessions.md).

Para obter outros detalhes técnicos sobre gatilhos, ações e limites, que são descritos pela descrição do Swagger do conector, examine a [página de referência do conector](/connectors/servicebus/). Para saber mais sobre as mensagens do barramento de serviço do Azure, consulte [o que é o barramento de serviço do Azure](../service-bus-messaging/service-bus-messaging-overview.md)?

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
