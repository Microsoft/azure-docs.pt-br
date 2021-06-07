---
title: Enviar mensagens correlacionadas em ordem usando um comboio sequencial
description: Enviar mensagens relacionadas em ordem usando o padrão comboio sequencial no aplicativo lógico do Azure com o barramento de serviço do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: apseth, divswa, logicappspm
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: 8c00d2e4f622bcfad7b2468013336f0d936e318c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87048670"
---
# <a name="send-related-messages-in-order-by-using-a-sequential-convoy-in-azure-logic-apps-with-azure-service-bus"></a>Enviar mensagens relacionadas em ordem usando um comboio sequencial no aplicativo lógico do Azure com o barramento de serviço do Azure

Quando você precisa enviar mensagens correlacionadas em uma ordem específica, você pode seguir o padrão [ *comboio sequencial*](/azure/architecture/patterns/sequential-convoy) ao usar os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) usando o [conector do barramento de serviço do Azure](../connectors/connectors-create-api-servicebus.md). As mensagens correlacionadas têm uma propriedade que define a relação entre essas mensagens, como a ID da [sessão](../service-bus-messaging/message-sessions.md) no barramento de serviço.

Por exemplo, suponha que você tenha 10 mensagens para uma sessão denominada "sessão 1" e tenha 5 mensagens para uma sessão chamada "sessão 2" que são todas enviadas para a mesma [fila do barramento de serviço](../service-bus-messaging/service-bus-queues-topics-subscriptions.md). Você pode criar um aplicativo lógico que processa mensagens da fila para que todas as mensagens da "sessão 1" sejam manipuladas por uma única execução de gatilho e que todas as mensagens de "sessão 2" sejam tratadas pela próxima execução do gatilho.

![Padrão comboio sequencial geral](./media/send-related-messages-sequential-convoy/sequential-convoy-pattern-general.png)

Este artigo mostra como criar um aplicativo lógico que implementa esse padrão usando a **entrega em ordem correlacionada usando o modelo de sessões do barramento de serviço** . Este modelo define um fluxo de trabalho de aplicativo lógico que começa com o conector do barramento de serviço **quando uma mensagem é recebida em um gatilho de fila (Peek-Lock)** , que recebe mensagens de uma [fila do barramento de serviço](../service-bus-messaging/service-bus-queues-topics-subscriptions.md). Aqui estão as etapas de alto nível que este aplicativo lógico executa:

* Inicializar uma sessão com base em uma mensagem que o gatilho lê da fila do barramento de serviço.

* Ler e processar todas as mensagens da mesma sessão na fila durante a execução do fluxo de trabalho atual.

Para examinar o arquivo JSON deste modelo, consulte [github: service-bus-sessions.jsem](https://github.com/Azure/logicapps/blob/master/templates/service-bus-sessions.json).

Para obter mais informações, consulte [padrão comboio sequencial – padrões de design de nuvem da arquitetura do Azure](/azure/architecture/patterns/sequential-convoy).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tem uma assinatura, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Um namespace do barramento de serviço e uma [fila do barramento de serviço](../service-bus-messaging/service-bus-queues-topics-subscriptions.md), que é uma entidade de mensagens que você usará em seu aplicativo lógico. Esses itens e seu aplicativo lógico precisam usar a mesma assinatura do Azure. Certifique-se de selecionar **habilitar sessões** ao criar a fila. Se você não tiver esses itens, saiba [como criar seu namespace e uma fila do barramento de serviço](../service-bus-messaging/service-bus-create-namespace-portal.md).

  [!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

* Conhecimento básico sobre como criar aplicativos lógicos. Se você for novo no aplicativo lógico do Azure, experimente o início rápido, [crie seu primeiro fluxo de trabalho automatizado](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="permissions-connection-string"></a>

## <a name="check-access-to-service-bus-namespace"></a>Verificar o acesso ao namespace do barramento de serviço

Se você não tiver certeza se seu aplicativo lógico tem permissões para acessar o namespace do barramento de serviço, confirme essas permissões.

1. Entre no [portal do Azure](https://portal.azure.com). Localize e selecione o *namespace* do barramento de serviço.

1. No menu namespace, em **configurações**, selecione **políticas de acesso compartilhado**. Em **declarações**, verifique se você tem permissões de **Gerenciamento** para esse namespace.

   ![Gerenciar permissões para namespace do barramento de serviço](./media/send-related-messages-sequential-convoy/check-service-bus-permissions.png)

1. Agora, obtenha a cadeia de conexão para o namespace do barramento de serviço. Você pode usar essa cadeia de caracteres mais tarde quando criar uma conexão com o namespace do seu aplicativo lógico.

   1. No painel **políticas de acesso compartilhado** , em **política**, selecione **RootManageSharedAccessKey**.
   
   1. Ao lado de sua cadeia de conexão primária, selecione o botão Copiar. Salve a cadeia de conexão para uso posterior.

      ![Copiar a cadeia de conexão do namespace do Barramento de Serviço](./media/send-related-messages-sequential-convoy/copy-service-bus-connection-string.png)

   > [!TIP]
   > Para confirmar se a cadeia de conexão está associada ao namespace do Barramento de Serviço ou a uma entidade de sistema de mensagens, como uma fila, pesquise a cadeia de conexão para o parâmetro `EntityPath`. Se encontrar esse parâmetro, a cadeia de conexão servirá para uma entidade específica e não será a cadeia correta a ser usada com seu aplicativo lógico.

## <a name="create-logic-app"></a>Criar aplicativo lógico

Nesta seção, você cria um aplicativo lógico usando a **entrega em ordem correlacionada usando** o modelo sessões do barramento de serviço, que inclui o gatilho e as ações para implementar esse padrão de fluxo de trabalho. Você também cria uma conexão com o namespace do barramento de serviço e especifica o nome da fila do barramento de serviço que você deseja usar.

1. No [portal do Azure](https://portal.azure.com), crie um aplicativo lógico em branco. No Home Page do Azure, selecione **criar um recurso**  >    >  **aplicativo lógico** de integração.

1. Depois que a Galeria de modelos for exibida, role além do vídeo e as seções de gatilhos comuns. Na seção **modelos** , selecione o modelo, **entrega em ordem correlacionada usando sessões do barramento de serviço**.

   ![Selecione o modelo "entrega em ordem correlacionada usando sessões do barramento de serviço"](./media/send-related-messages-sequential-convoy/select-correlated-in-order-delivery-template.png)

1. Quando a caixa de confirmação for exibida, selecione **usar este modelo**.

1. No designer do aplicativo lógico, na forma **barramento de serviço** , selecione **continuar** e, em seguida, selecione o sinal de adição ( **+** ) que aparece na forma.

   ![Selecione "continuar" para se conectar ao barramento de serviço do Azure](./media/send-related-messages-sequential-convoy/connect-to-service-bus.png)

1. Agora, crie uma conexão do barramento de serviço escolhendo uma das opções:

   * Para usar a cadeia de conexão que você copiou anteriormente do namespace do barramento de serviço, siga estas etapas:

     1. Selecione **inserir manualmente as informações de conexão**.

     1. Para **nome da conexão**, forneça um nome para a conexão. Para **cadeia de conexão**, Cole a cadeia de conexão do namespace e selecione **criar**, por exemplo:

        ![Insira o nome da conexão e a cadeia de conexão do barramento de serviço](./media/send-related-messages-sequential-convoy/provide-service-bus-connection-string.png)

        > [!TIP]
        > Se você não tiver essa cadeia de conexão, saiba como [Localizar e copiar a cadeia de conexão do namespace do barramento de serviço](#permissions-connection-string).

   * Para selecionar um namespace do barramento de serviço de sua assinatura atual do Azure, siga estas etapas:

     1. Para **nome da conexão**, forneça um nome para a conexão. Para **namespace do barramento de serviço**, selecione o namespace do barramento de serviço, por exemplo:

        ![Insira o nome da conexão e selecione namespace do barramento de serviço](./media/send-related-messages-sequential-convoy/create-service-bus-connection.png)

     1. Quando o próximo painel for exibido, selecione sua política de barramento de serviço e selecione **criar**.

        ![Selecione a política do barramento de serviço e, em seguida, "criar"](./media/send-related-messages-sequential-convoy/create-service-bus-connection-2.png)

1. Quando terminar, selecione **continuar**.

   O designer do aplicativo lógico agora mostra a **entrega em ordem correlacionada usando o modelo sessões do barramento de serviço** , que contém um fluxo de trabalho previamente preenchido com um gatilho e ações, incluindo dois escopos que implementam o tratamento de erros que seguem o `Try-Catch` padrão.

Agora você pode saber mais sobre o gatilho e as ações no modelo, ou pular para [fornecer os valores para o modelo de aplicativo lógico](#complete-template).

<a name="template-summary"></a>

## <a name="template-summary"></a>Resumo do modelo

Aqui está o fluxo de trabalho de nível superior na **entrega em ordem correlacionada usando** o modelo de sessões do barramento de serviço quando os detalhes são recolhidos:

![Fluxo de trabalho de nível superior do modelo](./media/send-related-messages-sequential-convoy/template-top-level-flow.png)

| Nome | Descrição |
|------|-------------|
| **`When a message is received in a queue (peek-lock)`** | Com base na recorrência especificada, esse gatilho do barramento de serviço verifica a fila do barramento de serviço especificada em busca de qualquer mensagem. Se uma mensagem existir na fila, o gatilho será acionado, o que criará e executará uma instância de fluxo de trabalho. <p><p>O termo *Peek-Lock* significa que o gatilho envia uma solicitação para recuperar uma mensagem da fila. Se uma mensagem existir, o gatilho recuperará e bloqueará a mensagem para que nenhum outro processamento ocorra nessa mensagem até que o período de bloqueio expire. Para obter detalhes, [inicialize a sessão](#initialize-session). |
| **`Init isDone`** | Essa [ação de **variável de inicialização**](../logic-apps/logic-apps-create-variables-store-values.md#initialize-variable) cria uma variável booliana que é definida como `false` e indica quando as seguintes condições são verdadeiras: <p><p>-Não há mais mensagens na sessão disponíveis para leitura. <br>-O bloqueio de sessão não precisa mais ser renovado para que a instância de fluxo de trabalho atual possa ser concluída. <p><p>Para obter detalhes, consulte [inicializar a sessão](#initialize-session). |
| **`Try`** | Essa [ação de **escopo**](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) contém as ações que são executadas para processar uma mensagem. Se um problema ocorrer no `Try` escopo, a ação de `Catch` **escopo** subsequente tratará desse problema. Para obter mais informações, consulte o [escopo "try"](#try-scope). |
| **`Catch`**| Essa [ação de **escopo**](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) contém as ações que são executadas se um problema ocorre no `Try` escopo anterior. Para obter mais informações, consulte [escopo de "catch"](#catch-scope). |
|||

<a name="try-scope"></a>

### <a name="try-scope"></a>Escopo "try"

Aqui está o fluxo de nível superior na `Try` [ação de escopo](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) quando os detalhes são recolhidos:

![Fluxo de trabalho de ação de escopo "try"](./media/send-related-messages-sequential-convoy/try-scope-action.png)

| Nome | Descrição |
|------|-------------|
| **`Send initial message to topic`** | Você pode substituir essa ação por qualquer ação que deseja manipular a primeira mensagem da sessão na fila. A ID da sessão especifica a sessão. <p><p>Para este modelo, uma ação do barramento de serviço envia a primeira mensagem a um tópico do barramento de serviço. Para obter detalhes, consulte [manipular a mensagem inicial](#handle-initial-message). |
| (ramificação paralela) | Esta [ação de ramificação paralela](../logic-apps/logic-apps-control-flow-branches.md) cria dois caminhos: <p><p>-Branch #1: continuar processando a mensagem. Para obter mais informações, consulte [Branch #1: concluir a mensagem inicial na fila](#complete-initial-message). <p><p>-Branch #2: abandone a mensagem se algo der errado e libere para retirada por outra execução de gatilho. Para obter mais informações, consulte [Branch #2: abandonar a mensagem inicial da fila](#abandon-initial-message). <p><p>Ambos os caminhos se unem posteriormente na **sessão de fechamento em uma fila e uma** ação de sucesso, descrita na próxima linha. |
| **`Close a session in a queue and succeed`** | Essa ação do barramento de serviço une os branches descritos anteriormente e fecha a sessão na fila após a ocorrência de um dos seguintes eventos: <p><p>-O fluxo de trabalho conclui o processamento de mensagens disponíveis na fila. <br>-O fluxo de trabalho abandona a mensagem inicial porque algo deu errado. <p><p>Para obter detalhes, consulte [fechar uma sessão em uma fila e ter sucesso](#close-session-succeed). |
|||

<a name="complete-initial-message"></a>

#### <a name="branch-1-complete-initial-message-in-queue"></a>#1 de ramificação: concluir a mensagem inicial na fila

| Nome | Descrição |
|------|-------------|
| `Complete initial message in queue` | Esta ação do barramento de serviço marca uma mensagem recuperada com êxito como concluída e remove a mensagem da fila para evitar o reprocessamento. Para obter detalhes, consulte [manipular a mensagem inicial](#handle-initial-message). |
| `While there are more messages for the session in the queue` | Esse [loop **until**](../logic-apps/logic-apps-control-flow-loops.md#until-loop) continua a obter mensagens enquanto as mensagens existem ou até uma hora ser passada. Para obter mais informações sobre as ações nesse loop, consulte [enquanto há mais mensagens para a sessão na fila](#while-more-messages-for-session). |
| **`Set isDone = true`** | Quando não houver mais mensagens, essa [ação **definir variável será definida**](../logic-apps/logic-apps-create-variables-store-values.md#set-variable) `isDone` como `true` . |
| **`Renew session lock until cancelled`** | Esse [loop **until**](../logic-apps/logic-apps-control-flow-loops.md#until-loop) garante que o bloqueio de sessão seja mantido por esse aplicativo lógico enquanto as mensagens existirem ou até que uma hora passe. Para obter mais informações sobre as ações nesse loop, consulte [renovar bloqueio de sessão até cancelado](#renew-session-while-messages-exist). |
|||

<a name="abandon-initial-message"></a>

#### <a name="branch-2-abandon-initial-message-from-the-queue"></a>#2 de ramificação: abandone a mensagem inicial da fila

Se a ação que manipula a primeira mensagem falhar, a ação do barramento de serviço, **abandonar a mensagem inicial da fila**, liberará a mensagem para outra instância de fluxo de trabalho ser executada e processada. Para obter detalhes, consulte [manipular a mensagem inicial](#handle-initial-message).

<a name="catch-scope"></a>

### <a name="catch-scope"></a>Escopo "catch"

Se as ações no `Try` escopo falharem, o aplicativo lógico ainda deverá fechar a sessão. A `Catch` [ação de escopo](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) é executada quando a `Try` ação de escopo resulta no status, `Failed` , `Skipped` ou `TimedOut` . O escopo retorna uma mensagem de erro que inclui a ID da sessão em que o problema ocorreu e encerra o aplicativo lógico.

Aqui está o fluxo de nível superior na `Catch` ação de escopo quando os detalhes são recolhidos:

![Fluxo de trabalho de ação de escopo "catch"](./media/send-related-messages-sequential-convoy/catch-scope-action.png)

| Nome | Descrição |
|------|-------------|
| **`Close a session in a queue and fail`** | Essa ação do barramento de serviço fecha a sessão na fila para que o bloqueio da sessão não permaneça aberto. Para obter detalhes, consulte [fechar uma sessão em uma fila e falhar](#close-session-fail). |
| **`Find failure msg from 'Try' block`** | Essa [ação de **matriz de filtro**](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action) cria uma matriz das entradas e saídas de todas as ações dentro do `Try` escopo com base nos critérios especificados. Nesse caso, essa ação retorna as saídas das ações que resultaram no `Failed` status. Para obter detalhes, consulte [Localizar mensagem de falha do bloco ' Try '](#find-failure-message). |
| **`Select error details`** | Essa [ação **Select**](../logic-apps/logic-apps-perform-data-operations.md#select-action) cria uma matriz que contém objetos JSON com base nos critérios especificados. Esses objetos JSON são criados a partir dos valores na matriz criada pela ação anterior, `Find failure msg from 'Try' block` . Nesse caso, essa ação retorna uma matriz que contém um objeto JSON criado com base nos detalhes do erro retornados da ação anterior. Para obter detalhes, consulte [selecionar detalhes do erro](#select-error-details). |
| **`Terminate`** | Essa [ação de **término**](../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action) interrompe a execução do fluxo de trabalho, cancela as ações em andamento, ignora as ações restantes e retorna o status especificado, a ID da sessão e o resultado do erro da `Select error details` ação. Para obter detalhes, consulte [encerrar aplicativo lógico](#terminate-logic-app). |
|||

<a name="complete-template"></a>

## <a name="complete-the-template"></a>Concluir o modelo

Para fornecer os valores para o gatilho e as ações no modelo **entrega em ordem correlacionada usando sessões do barramento de serviço** , siga estas etapas. Você precisa fornecer todos os valores necessários, que são marcados por um asterisco ( **\*** ), antes de poder salvar seu aplicativo lógico.

<a name="initialize-session"></a>

### <a name="initialize-the-session"></a>Inicializar a sessão

* Para o gatilho **quando uma mensagem é recebida em uma fila (Peek-Lock)** , forneça essas informações para que o modelo possa inicializar uma sessão usando a propriedade **ID da sessão** , por exemplo:

  ![Detalhes do gatilho do barramento de serviço para "quando uma mensagem é recebida em uma fila (Peek-Lock)"](./media/send-related-messages-sequential-convoy/service-bus-check-message-peek-lock-trigger.png)

  > [!NOTE]
  > Inicialmente, o intervalo de sondagem é definido como três minutos para que o aplicativo lógico não seja executado com mais frequência do que o esperado e resulte em encargos de cobrança inesperados. Idealmente, defina o intervalo e a frequência como 30 segundos para que o aplicativo lógico seja disparado imediatamente quando uma mensagem chegar.

  | Propriedade | Necessário para este cenário | Valor | Descrição |
  |----------|----------------------------|-------|-------------|
  | **Nome da fila** | Sim | <*nome da fila*> | O nome da fila do barramento de serviço criada anteriormente. Este exemplo usa "fabrikam-Service-Bus-Queue". |
  | **Tipo de fila** | Sim | **Principal** | Sua fila principal do barramento de serviço |
  | **ID da sessão** | Sim | **Próximo disponível** | Essa opção Obtém uma sessão para cada execução de gatilho, com base na ID de sessão da mensagem na fila do barramento de serviço. A sessão também é bloqueada para que nenhum outro aplicativo lógico ou outro cliente possa processar mensagens relacionadas a essa sessão. As ações subsequentes do fluxo de trabalho processam todas as mensagens associadas a essa sessão, conforme descrito posteriormente neste artigo. <p><p>Aqui está mais informações sobre as outras opções de **ID de sessão** : <p>- **Nenhum**: a opção padrão, que resulta em nenhuma sessão e não pode ser usada para implementar o padrão comboio sequencial. <p>- **Inserir valor personalizado**: Use essa opção quando você souber a ID de sessão que deseja usar e sempre quiser executar o gatilho para essa ID de sessão. <p>**Observação**: o conector do barramento de serviço pode economizar um número limitado de sessões exclusivas por vez do barramento de serviço do Azure para o cache do conector. Se a contagem de sessões exceder esse limite, as sessões antigas serão removidas do cache. Para obter mais informações, consulte [trocar mensagens na nuvem com os aplicativos lógicos do Azure e o barramento de serviço do Azure](../connectors/connectors-create-api-servicebus.md#connector-reference). |
  | **Intervalo** | Sim | <*número de intervalos*> | O número de unidades de tempo entre as recorrências antes de verificar uma mensagem. |
  | **Frequência** | Sim | **Segundo**, **Minuto**, **Hora**, **Dia**, **Semana** ou **Mês** | A unidade de tempo da recorrência a ser usada ao verificar uma mensagem. <p>**Dica**: para adicionar um **fuso horário** ou uma **hora de início**, selecione essas propriedades na lista **Adicionar novo parâmetro** . |
  |||||

  Para obter mais informações de gatilho, consulte [barramento de serviço – quando uma mensagem é recebida em uma fila (Peek-Lock)](/connectors/servicebus/#when-a-message-is-received-in-a-queue-(peek-lock)). O gatilho gera um [ServiceBusMessage](/connectors/servicebus/#servicebusmessage).

Depois de inicializar a sessão, o fluxo de trabalho usa a ação **inicializar variável** para criar uma variável booliana que inicialmente definida como `false` e indica quando as seguintes condições são verdadeiras: 

* Não há mais mensagens na sessão disponíveis para leitura.

* O bloqueio de sessão não precisa mais ser renovado para que a instância de fluxo de trabalho atual possa ser concluída.

![Detalhes da ação "inicializar variável" para "init isdone"](./media/send-related-messages-sequential-convoy/init-is-done-variable.png)

Em seguida, no bloco **try** , o fluxo de trabalho executa ações na primeira mensagem que é lida.

<a name="handle-initial-message"></a>

### <a name="handle-the-initial-message"></a>Manipular a mensagem inicial

A primeira ação é uma ação do barramento de serviço de espaço reservado, **Enviar mensagem inicial para o tópico**, que você pode substituir por qualquer outra ação que você deseja tratar da primeira mensagem da sessão na fila. A ID da sessão especifica a sessão de onde a mensagem foi originada.

A ação do barramento de serviço de espaço reservado envia a primeira mensagem a um tópico do barramento de serviço que é especificado pela propriedade **ID da sessão** . Dessa forma, todas as mensagens associadas a uma sessão específica vão para o mesmo tópico. Todas as propriedades de **ID de sessão** para ações subsequentes neste modelo usam o mesmo valor de ID de sessão.

![Detalhes da ação do barramento de serviço para "Enviar mensagem inicial ao tópico"](./media/send-related-messages-sequential-convoy/send-initial-message-to-topic-action.png)

1. Na ação do barramento de serviço, **conclua a mensagem inicial na fila**, forneça o nome da sua fila do barramento de serviço e mantenha todos os outros valores de propriedade padrão na ação.

   ![Detalhes da ação do barramento de serviço para "Concluir mensagem inicial na fila"](./media/send-related-messages-sequential-convoy/complete-initial-message-queue.png)

1. Na ação do barramento de serviço, **abandone a mensagem inicial da fila**, forneça o nome da fila do barramento de serviço e mantenha todos os outros valores de propriedade padrão na ação.

   ![Detalhes da ação do barramento de serviço para "abandonar a mensagem inicial da fila"](./media/send-related-messages-sequential-convoy/abandon-initial-message-from-queue.png)

Em seguida, você fornecerá as informações necessárias para as ações que seguem a **mensagem inicial completa na ação da fila** . Você começará com as ações no **enquanto houver mais mensagens para a sessão no** loop de fila.

<a name="while-more-messages-for-session"></a>

### <a name="while-there-are-more-messages-for-the-session-in-the-queue"></a>Embora haja mais mensagens para a sessão na fila

Esse [loop **until**](../logic-apps/logic-apps-control-flow-loops.md#until-loop) executa essas ações enquanto as mensagens existem na fila ou até uma hora ser passada. Para alterar o limite de tempo do loop, edite o valor da propriedade **Timeout** do loop.

* Obter mensagens adicionais da fila enquanto houver mensagens.

* Verifique o número de mensagens restantes. Se as mensagens ainda existirem, continue processando as mensagens. Se não houver mais mensagens, o fluxo de trabalho definirá a `isDone` variável como `true` e sairá do loop.

![Loop Until – processar mensagens enquanto estiver na fila](./media/send-related-messages-sequential-convoy/while-more-messages-for-session-in-queue.png)

1. Na ação do barramento de serviço, **obtenha mensagens adicionais da sessão**, forneça o nome da sua fila do barramento de serviço. Caso contrário, mantenha todos os outros valores de propriedade padrão na ação.

   > [!NOTE]
   > Por padrão, o número máximo de mensagens é definido como `175` , mas esse limite é afetado pelo tamanho da mensagem e pela propriedade tamanho máximo da mensagem no barramento de serviço. Para obter mais informações, consulte [tamanho da mensagem para uma fila](../service-bus-messaging/service-bus-quotas.md).

   ![Ação do barramento de serviço-"obter mensagens adicionais da sessão"](./media/send-related-messages-sequential-convoy/get-additional-messages-from-session.png)

   Em seguida, o fluxo de trabalho se divide nesses branches paralelos:

   * Se ocorrer um erro ou falha durante a verificação de mensagens adicionais, defina a `isDone` variável como `true` .

   * O **processo mensagens se obtivermos qualquer** condição verifica se o número de mensagens restantes é zero. Se for falso e houver mais mensagens, continue o processamento. Se for true e não houver mais mensagens, o fluxo de trabalho definirá a `isDone` variável como `true` .

   ![Condição-processar mensagens, se houver](./media/send-related-messages-sequential-convoy/process-messages-if-any.png)

   Na seção **If false** , um loop **for each** processa cada mensagem na ordem de primeiro a entrar, primeiro a sair (FIFO). Nas **configurações** do loop, a configuração de **controle de simultaneidade** é definida como `1` , portanto, apenas uma única mensagem é processada de cada vez.

   ![Loop "for each"-processa cada mensagem uma de cada vez](./media/send-related-messages-sequential-convoy/for-each-additional-message.png)

1. Para as ações do barramento de serviço, **conclua a mensagem em uma fila** e **abandone a mensagem em uma fila**, forneça o nome da sua fila do barramento de serviço.

   ![Ações do barramento de serviço-"concluir a mensagem em uma fila" e "abandonar a mensagem em uma fila"](./media/send-related-messages-sequential-convoy/abandon-or-complete-message-in-queue.png)

   Após a conclusão de **mais mensagens para a sessão na fila** , o fluxo de trabalho define a `isDone` variável como `true` .

Em seguida, você fornecerá as informações necessárias para as ações no loop **renovar sessão do bloqueio até o cancelado** .

<a name="renew-session-while-messages-exist"></a>

### <a name="renew-session-lock-until-cancelled"></a>Renovar bloqueio de sessão até cancelado

Esse [loop **until**](../logic-apps/logic-apps-control-flow-loops.md#until-loop) garante que o bloqueio de sessão seja mantido por esse aplicativo lógico enquanto as mensagens existem na fila ou até uma hora passada executando essas ações. Para alterar o limite de tempo do loop, edite o valor da propriedade **Timeout** do loop.

* Atraso por 25 segundos ou uma quantidade de tempo que é menor que a duração do tempo limite de bloqueio para a fila que está sendo processada. A menor duração de bloqueio é de 30 segundos, portanto, o valor padrão é suficiente. No entanto, você pode otimizar o número de vezes que o loop é executado ajustando-se adequadamente.

* Verifique se a `isDone` variável está definida como `true` .

  * Se `isDone` não estiver definido como `true` , o fluxo de trabalho ainda está processando mensagens, portanto, o fluxo de trabalho renova o bloqueio na sessão na fila e verifica a condição de loop novamente.

    Você precisa fornecer o nome para a fila do barramento de serviço na ação do barramento de serviço, [**renovar o bloqueio na sessão em uma fila**](#renew-lock-on-session).

  * Se `isDone` é definido como `true` , o fluxo de trabalho não renova o bloqueio na sessão na fila e sai do loop.

  ![Until loop – "renovar bloqueio de sessão até cancelado"](./media/send-related-messages-sequential-convoy/renew-lock-until-session-cancelled.png)

<a name="renew-lock-on-session"></a>

#### <a name="renew-lock-on-the-session-in-a-queue"></a>Renovar bloqueio na sessão em uma fila

Essa ação do barramento de serviço renova o bloqueio na sessão na fila enquanto o fluxo de trabalho ainda está processando mensagens.

* Na ação do barramento de serviço, **renove o bloqueio na sessão em uma fila**, forneça o nome da sua fila do barramento de serviço.

  ![Ação do barramento de serviço-"renovar bloqueio na sessão na fila"](./media/send-related-messages-sequential-convoy/renew-lock-on-session-in-queue.png)

Em seguida, você fornecerá as informações necessárias para a ação do barramento de serviço, **fechará uma sessão em uma fila e terá sucesso**.

<a name="close-session-succeed"></a>

### <a name="close-a-session-in-a-queue-and-succeed"></a>Fechar uma sessão em uma fila e ter sucesso

Essa ação do barramento de serviço fecha a sessão na fila depois que o fluxo de trabalho termina de processar todas as mensagens disponíveis na fila, ou o fluxo de trabalho abandona a mensagem inicial.

* Na ação do barramento de serviço, **feche uma sessão em uma fila e tenha sucesso**, forneça o nome da sua fila do barramento de serviço.

  ![Ação do barramento de serviço-"fechar uma sessão em uma fila e bem sucedido"](./media/send-related-messages-sequential-convoy/close-session-in-queue-succeed.png)

As seções a seguir descrevem as ações na `Catch` seção, que tratam erros e exceções que ocorrem em seu fluxo de trabalho.

<a name="close-session-fail"></a>

### <a name="close-a-session-in-a-queue-and-fail"></a>Fechar uma sessão em uma fila e falhar

Essa ação do barramento de serviço sempre é executada como a primeira ação no `Catch` escopo e fecha a sessão na fila.

* Na ação do barramento de serviço, **feche uma sessão em uma fila e falhe**, forneça o nome da sua fila do barramento de serviço.

  ![Ação do barramento de serviço-"fechar uma sessão em uma fila e falhar"](./media/send-related-messages-sequential-convoy/close-session-in-queue-fail.png)

Em seguida, o fluxo de trabalho cria uma matriz que tem as entradas e saídas de todas as ações no `Try` escopo para que o aplicativo lógico possa acessar informações sobre o erro ou a falha ocorrida.

<a name="find-failure-message"></a>

### <a name="find-failure-msg-from-try-block"></a>Localizar mensagem de falha do bloco ' Try '

Essa [ação de **matriz de filtro**](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action) cria uma matriz que tem as entradas e saídas de todas as ações dentro do `Try` escopo com base nos critérios especificados usando a [ `result()` função](../logic-apps/workflow-definition-language-functions-reference.md#result). Nesse caso, essa ação retorna as saídas das ações que têm `Failed` status usando a [ `equals()` função](../logic-apps/workflow-definition-language-functions-reference.md#equals) e a [ `item()` função](../logic-apps/workflow-definition-language-functions-reference.md#item).

![Ação filtrar matriz-"Localizar mensagem de falha do bloco" try "](./media/send-related-messages-sequential-convoy/find-failure-message.png)

Aqui está a definição de JSON para esta ação:

```json
"Find_failure_msg_from_'Try'_block": {
   "inputs": {
      "from": "@Result('Try')",
      "where": "@equals(item()['status'], 'Failed')"
   },
   "runAfter": {
      "Close_the_session_in_the_queue_and_fail": [
         "Succeeded"
      ]
   },
   "type": "Query"
},
```

Em seguida, o fluxo de trabalho cria uma matriz com um objeto JSON que contém as informações de erro na matriz retornada da `Find failure msg from 'Try' block` ação.

<a name="select-error-details"></a>

### <a name="select-error-details"></a>Selecionar detalhes do erro

Essa [ação **Select**](../logic-apps/logic-apps-perform-data-operations.md#select-action) cria uma matriz que contém objetos JSON com base na matriz de entrada que faz a saída da ação anterior, `Find failure msg from 'Try' block` . Especificamente, essa ação retorna uma matriz que tem apenas as propriedades especificadas para cada objeto na matriz. Nesse caso, a matriz contém as propriedades nome da ação e resultado do erro.

![Selecione ação-"selecionar detalhes do erro"](./media/send-related-messages-sequential-convoy/select-error-details.png)

Aqui está a definição de JSON para esta ação:

```json
"Select_error_details": {
   "inputs": {
      "from": "@body('Find_failure_msg_from_''Try''_block')[0]['outputs']",
      "select": {
         "action": "@item()['name']",
         "errorResult": "@item()"
      }
   },
   "runAfter": {
      "Find_failure_msg_from_'Try'_block": [
         "Succeeded"
      ]
   },
   "type": "Select"
},
```

Em seguida, o fluxo de trabalho interrompe a execução do aplicativo lógico e retorna o status da execução junto com mais informações sobre o erro ou a falha que aconteceu.

<a name="terminate-logic-app"></a>

### <a name="terminate-logic-app-run"></a>Encerrar execução do aplicativo lógico

Essa [ação de **término**](../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action) interrompe a execução do aplicativo lógico e retorna `Failed` como o status da execução do aplicativo lógico junto com a ID da sessão e o resultado do erro da `Select error details` ação.

![Ação terminar para parar a execução do aplicativo lógico](./media/send-related-messages-sequential-convoy/terminate-logic-app-run.png)

Aqui está a definição de JSON para esta ação:

```json
"Terminate": {
   "description": "This Failure Termination only runs if the Close Session upon Failure action runs - otherwise the LA will be terminated as Success",
   "inputs": {
      "runError": {
         "code": "",
         "message": "There was an error processing messages for Session ID @{triggerBody()?['SessionId']}. The following error(s) occurred: @{body('Select_error_details')['errorResult']}"
         },
         "runStatus": "Failed"
      },
      "runAfter": {
         "Select_error_details": [
            "Succeeded"
         ]
      },
      "type": "Terminate"
   }
},
```

## <a name="save-and-run-logic-app"></a>Salvar e executar o aplicativo lógico

Depois de concluir o modelo, agora você pode salvar seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

Para testar seu aplicativo lógico, envie mensagens para a fila do barramento de serviço. 

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre os [gatilhos e as ações do conector do barramento de serviço](/connectors/servicebus/)
