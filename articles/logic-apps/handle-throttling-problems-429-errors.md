---
title: Lidar com problemas de limitação ou ' 429-erros em excesso de solicitações
description: Como solucionar problemas de limitação ou "HTTP 429 número excessivo de erros de solicitações, em aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: ea153b1927a337be29c2eb69e2417cc250abf5e8
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2020
ms.locfileid: "94366042"
---
# <a name="handle-throttling-problems-429---too-many-requests-errors-in-azure-logic-apps"></a>Lidar com problemas de limitação (429-erros de "excesso de solicitações") nos aplicativos lógicos do Azure

Em [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md), seu aplicativo lógico retorna um [erro "http 429 número excessivo de solicitações"](https://developer.mozilla.org/docs/Web/HTTP/Status/429) ao passar pela limitação, o que acontece quando o número de solicitações excede a taxa na qual o destino pode lidar com um período de tempo específico. A limitação pode criar problemas como processamento de dados atrasado, velocidade de desempenho reduzida e erros, como exceder a política de repetição especificada.

![Limitação no conector de SQL Server](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-error.png)

Aqui estão alguns tipos comuns de limitação que seu aplicativo lógico pode ter:

* [Aplicativo lógico](#logic-app-throttling)
* [Connector](#connector-throttling)
* [Serviço ou sistema de destino](#destination-throttling)

<a name="logic-app-throttling"></a>

## <a name="logic-app-throttling"></a>Limitação do aplicativo lógico

O serviço de aplicativos lógicos do Azure tem seus próprios [limites de taxa de transferência](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Portanto, se seu aplicativo lógico exceder esses limites, o recurso de aplicativo lógico será limitado, não apenas uma instância ou execução específica.

Para localizar eventos de limitação neste nível, verifique o painel de **métricas** do aplicativo lógico no portal do Azure.

1. No [portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer do aplicativo lógico.

1. No menu do aplicativo lógico, em **monitoramento** , selecione **métricas**.

1. Em **título do gráfico** , selecione **Adicionar métrica** para adicionar outra métrica ao existente.

1. Na primeira barra de métricas, na lista **métrica** , selecione **eventos com restrição de ação**. Na segunda barra de métricas, na lista **métrica** , selecione **disparar eventos restringidos**.

Para lidar com a limitação nesse nível, você tem estas opções:

* Limite o número de instâncias do aplicativo lógico que podem ser executadas ao mesmo tempo.

  Por padrão, se a condição de gatilho do aplicativo lógico for atendida mais de uma vez ao mesmo tempo, várias instâncias de gatilho para seu aplicativo lógico serão executadas simultaneamente ou *em paralelo*. Esse comportamento significa que cada instância de gatilho é acionada antes da conclusão da execução da instância do fluxo de trabalho anterior.

  Embora o número padrão de instâncias de gatilho que podem ser executadas simultaneamente seja [ilimitado](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits), você pode limitar esse número [ativando a configuração de simultaneidade do gatilho](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency)e, se necessário, selecionar um limite diferente do valor padrão.

* Habilite o modo de alta taxa de transferência.

  Um aplicativo lógico tem um [limite padrão para o número de ações que podem ser executadas em um intervalo de interrupção de 5 minutos](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Para aumentar esse limite para o número máximo de ações, ative o [modo de alta taxa de transferência](../logic-apps/logic-apps-limits-and-config.md#run-high-throughput-mode) em seu aplicativo lógico.

* Desabilitar o comportamento de deslote de matriz ("split on") em gatilhos.

  Se um gatilho retornar uma matriz para que as ações de fluxo de trabalho restantes sejam processadas, a [configuração **dividir**](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) do gatilho dividirá os itens da matriz e iniciará uma instância de fluxo de trabalho para cada item da matriz, disparando efetivamente várias execuções simultâneas até o limite de [ **divisão**](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits). Para controlar a limitação, desative a **divisão no** comportamento e faça com que seu aplicativo lógico processe toda a matriz com uma única chamada, em vez de lidar com um único item por chamada.

* Refatorar ações em aplicativos lógicos menores.

  Como mencionado anteriormente, um aplicativo lógico é limitado a um [número padrão de ações que podem ser executadas em um período de 5 minutos](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Embora seja possível aumentar esse limite habilitando o [modo de alta taxa de transferência](../logic-apps/logic-apps-limits-and-config.md#run-high-throughput-mode), você também pode considerar se deseja dividir as ações do seu aplicativo lógico em aplicativos lógicos menores para que o número de ações executadas em cada aplicativo lógico permaneça sob o limite. Dessa forma, você reduzirá a carga em um único recurso de aplicativo lógico e distribuirá a carga entre vários aplicativos lógicos. Essa solução funciona melhor para ações que lidam com grandes conjuntos de dados ou que muitas ações em execução simultânea, iterações de loop ou ações dentro de cada iteração de loop que excedem o limite de execução da ação.

  Por exemplo, esse aplicativo lógico faz todo o trabalho para obter tabelas de um banco de dados SQL Server e obtém as linhas de cada tabela. O loop **for each** itera simultaneamente em cada tabela para que a ação **obter linhas** retorne as linhas de cada tabela. Com base nas quantidades de dados nessas tabelas, essas ações podem exceder o limite de execuções de ação.

  ![Refatoração "antes" do aplicativo lógico](./media/handle-throttling-problems-429-errors/refactor-logic-app-before-version.png)

  Após a refatoração, o aplicativo lógico agora é um aplicativo lógico pai e filho. O pai Obtém as tabelas de SQL Server e, em seguida, chama um aplicativo lógico filho para cada tabela para obter as linhas:

  ![Criar um aplicativo lógico para uma ação](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-1.png)

  Aqui está o aplicativo lógico filho que é chamado pelo aplicativo lógico pai para obter as linhas para cada tabela:

  ![Criar outro aplicativo lógico para uma segunda ação](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-2.png)

<a name="connector-throttling"></a>

## <a name="connector-throttling"></a>Limitação do conector

Cada conector tem seus próprios limites de limitação, que podem ser encontrados na página de referência técnica do conector. Por exemplo, o [conector do barramento de serviço do Azure](/connectors/servicebus/) tem um limite de limitação que permite até 6.000 chamadas por minuto, enquanto o conector de SQL Server tem [limites de limitação que variam de acordo com o tipo de operação](/connectors/sql/).

Alguns gatilhos e ações, como HTTP, têm uma ["política de repetição"](../logic-apps/logic-apps-exception-handling.md#retry-policies) que você pode personalizar com base nos [limites de política de repetição](../logic-apps/logic-apps-limits-and-config.md#retry-policy-limits) para implementar a manipulação de exceção. Essa política especifica se e com que frequência um gatilho ou uma ação tenta novamente uma solicitação quando a solicitação original falha ou atinge o tempo limite e resulta em uma resposta 408, 429 ou 5xx. Assim, quando a limitação inicia e retorna um erro 429, os aplicativos lógicos seguem a política de repetição quando houver suporte.

Para saber se um gatilho ou uma ação dá suporte a políticas de repetição, verifique as configurações do gatilho ou da ação. Para exibir as tentativas de repetição de um gatilho ou ação, vá para o histórico de execuções do seu aplicativo lógico, selecione a execução que você deseja examinar e expanda esse gatilho ou ação para exibir detalhes sobre entradas, saídas e quaisquer novas tentativas, por exemplo:

![Exibir o histórico de execução da ação, novas tentativas, entradas e saídas](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-retries.png)

Embora o histórico de repetição forneça informações de erro, você pode ter problemas para diferenciar entre limitação de conector e [limitação de destino](#destination-throttling). Nesse caso, talvez seja necessário revisar os detalhes da resposta ou executar alguns cálculos de intervalo de limitação para identificar a origem.

Para aplicativos lógicos no serviço de aplicativos lógicos do Azure de vários locatários global, a limitação ocorre no nível de *conexão* . Portanto, por exemplo, para aplicativos lógicos executados em um [ambiente do serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), a limitação ainda ocorre para conexões não-ISE porque elas são executadas no serviço de aplicativos lógicos de vários locatários globais. No entanto, as conexões do ISE, que são criadas por conectores do ISE, não são limitadas porque são executadas em seu ISE.

Para lidar com a limitação nesse nível, você tem estas opções:

* Configure várias conexões para uma única ação, de modo que o aplicativo lógico particiona os dados para processamento.

  Para essa opção, considere se você pode distribuir a carga de trabalho dividindo as solicitações de uma ação entre várias conexões com o mesmo destino usando as mesmas credenciais.

  Por exemplo, suponha que seu aplicativo lógico obtenha tabelas de um banco de dados SQL Server e, em seguida, obtenha as linhas de cada tabela. Com base no número de linhas que você precisa processar, você pode usar várias conexões e vários loops **for each** para dividir o número total de linhas em conjuntos menores para processamento. Esse cenário usa dois **para cada** loop para dividir o número total de linhas na metade. O primeiro loop **for each** usa uma expressão que obtém a primeira metade. O outro loop **for each** usa uma expressão diferente que obtém a segunda metade, por exemplo:<p>

    * Expressão 1: a `take()` função obtém a frente de uma coleção. Para obter mais informações, consulte a [ **`take()`** função](workflow-definition-language-functions-reference.md#take).

      `@take(collection-or-array-name, div(length(collection-or-array-name), 2))`

    * Expressão 2: a `skip()` função remove a frente de uma coleção e retorna todos os outros itens. Para obter mais informações, consulte a [ **`skip()`** função](workflow-definition-language-functions-reference.md#skip).

      `@skip(collection-or-array-name, div(length(collection-or-array-name), 2))`

    Aqui está um exemplo de Visual que mostra como você pode usar essas expressões:

    ![Criar e usar várias conexões para uma única ação](./media/handle-throttling-problems-429-errors/create-multiple-connections-per-action.png)

* Configure uma conexão diferente para cada ação.

  Para essa opção, considere se você pode distribuir a carga de trabalho distribuindo as solicitações de cada ação em sua própria conexão, mesmo quando as ações se conectam ao mesmo serviço ou sistema e usam as mesmas credenciais.

  Por exemplo, suponha que seu aplicativo lógico obtenha as tabelas de um banco de dados SQL Server e obtenha cada linha em cada tabela. Você pode usar conexões separadas para que o que obtém as tabelas use uma conexão, enquanto a obtenção de cada linha usa outra conexão.

  ![Criar e usar conexões diferentes para cada ação](./media/handle-throttling-problems-429-errors/create-connection-per-action.png)

* Altere a simultaneidade ou o paralelismo em um [loop "for each"](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop).

  Por padrão, as iterações de loop "for each" são executadas ao mesmo tempo até o [limite de simultaneidade](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Se você tiver um conector que está ficando limitado dentro de um loop "for each", poderá reduzir o número de iterações de loop executadas em paralelo. Para saber mais, consulte esses tópicos:
  
  * [Loops "for each" – alterar a simultaneidade ou executar sequencialmente](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop)

  * [Esquema de linguagem de definição de fluxo de trabalho – para cada loop](../logic-apps/logic-apps-workflow-actions-triggers.md#foreach-action)

  * [Esquema de linguagem de definição de fluxo de trabalho – alterar a simultaneidade de loop "for each"](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency)

  * [Esquema de linguagem de definição de fluxo de trabalho – executar loops "for each" sequencialmente](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each)

<a name="destination-throttling"></a>

## <a name="destination-service-or-system-throttling"></a>Serviço de destino ou limitação do sistema

Embora um conector tenha seus próprios limites de limitação, o serviço de destino ou o sistema que é chamado pelo conector também pode ter limites de limitação. Por exemplo, algumas APIs no Microsoft Exchange Server têm limites de limitação mais rígidos do que o conector do Outlook do Office 365.

Por padrão, as instâncias de um aplicativo lógico e quaisquer loops ou ramificações dentro dessas instâncias são executadas *em paralelo*. Esse comportamento significa que várias instâncias podem chamar o mesmo ponto de extremidade ao mesmo tempo. Cada instância não sabe sobre a existência do outro, portanto, tentativas de repetição de ações com falha podem criar [condições de corrida](https://en.wikipedia.org/wiki/Race_condition) em que várias chamadas tentam ser executadas ao mesmo tempo, mas para obter êxito, essas chamadas devem chegar ao serviço ou sistema de destino antes que a limitação comece a ocorrer.

Por exemplo, suponha que você tenha uma matriz que tenha 100 itens. Você usa um loop "for each" para iterar pela matriz e ativar o controle de simultaneidade do loop para que você possa restringir o número de iterações paralelas a 20 ou o [limite padrão atual](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits). Dentro desse loop, uma ação insere um item da matriz em um banco de dados SQL Server, que permite apenas 15 chamadas por segundo. Esse cenário resulta em um problema de limitação porque uma pendência de novas tentativas é criada e nunca é executada.

Esta tabela descreve a linha do tempo para o que acontece no loop quando o intervalo de repetição da ação é de 1 segundo:

| Point-in-time | Número de ações executadas | Número de ações que falharam | Número de repetições aguardando |
|---------------|----------------------------|-----------------------------|---------------------------|
| T + 0 segundos | 20 inserções | 5 falha, devido ao limite de SQL | 5 novas tentativas |
| T + 0,5 segundos | 15 inserções, devido às 5 repetições anteriores aguardando | Todos os 15 falham, devido ao limite de SQL anterior ainda em vigor por outros 0,5 segundos | 20 tentativas <br>(anteriores 5 + 15 novas) |
| T + 1 segundo | 20 inserções | 5 falha mais para 20 tentativas anteriores, devido ao limite de SQL | 25 repetições (últimas 20 + 5 novas)
|||||

Para lidar com a limitação nesse nível, você tem estas opções:

* Crie aplicativos lógicos para que cada um manipule uma única operação.

  * Continuando com o cenário de SQL Server de exemplo nesta seção, você pode criar um aplicativo lógico que coloca itens de matriz em uma fila, como uma [fila do barramento de serviço do Azure](../connectors/connectors-create-api-servicebus.md). Em seguida, você cria outro aplicativo lógico que executa apenas a operação de inserção para cada item nessa fila. Dessa forma, apenas uma instância de aplicativo lógico é executada em qualquer momento específico, o que conclui a operação de inserção e passa para o próximo item na fila, ou a instância recebe erros de 429, mas não tenta repetições impróprias.

  * Crie um aplicativo lógico pai que chama um aplicativo lógico filho ou aninhado para cada ação. Se o pai precisar chamar diferentes aplicativos filho com base no resultado do pai, você poderá usar uma ação de condição ou uma ação de comutador que determina qual aplicativo filho chamar. Esse padrão pode ajudá-lo a reduzir o número de chamadas ou operações.

    Por exemplo, suponha que você tenha dois aplicativos lógicos, cada um com um gatilho de sondagem que verifica sua conta de email a cada minuto para um assunto específico, como "êxito" ou "falha". Essa configuração resulta em 120 chamadas por hora. Em vez disso, se você criar um único aplicativo lógico pai que seja sondado a cada minuto, mas chama um aplicativo lógico filho que é executado com base em "sucesso" ou "falha", você corta o número de verificações de sondagem para metade ou 60 nesse caso.

* Configure o processamento em lotes.

  Se o serviço de destino oferecer suporte a operações em lote, você poderá abordar a limitação processando itens em grupos ou lotes, em vez de individualmente. Para implementar a solução de processamento em lotes, você cria um aplicativo lógico "receptor do lote" e um aplicativo lógico "remetente do lote". O remetente do lote coleta mensagens ou itens até que os critérios especificados sejam atendidos e, em seguida, envia essas mensagens ou itens em um único grupo. O destinatário do lote aceita esse grupo e processa essas mensagens ou itens. Para obter mais informações, consulte [processar mensagens em lote em grupos](../logic-apps/logic-apps-batch-process-send-receive-messages.md).

* Use as versões de webhook para gatilhos e ações, em vez de versões de sondagem.

  Por quê? Um gatilho de sondagem continua a verificar o serviço de destino ou o sistema em intervalos específicos. Um intervalo muito frequente, como a cada segundo, pode criar problemas de limitação. No entanto, um gatilho ou ação de webhook, como [webhook http](../connectors/connectors-native-webhook.md), cria apenas uma única chamada para o serviço ou sistema de destino, que ocorre no momento da assinatura e solicita que o destino notifique o gatilho ou a ação somente quando ocorrer um evento. Dessa forma, o gatilho ou a ação não precisa verificar continuamente o destino.
  
  Portanto, se o serviço ou o sistema de destino oferecer suporte a WebHooks ou fornecer um conector que tenha uma versão de webhook, essa opção será melhor do que usar a versão de sondagem. Para identificar gatilhos e ações de webhook, confirme se eles têm o `ApiConnectionWebhook` tipo ou se eles não exigem que você especifique uma recorrência. Para obter mais informações, consulte [gatilho APIConnectionWebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) e [ação APIConnectionWebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-action).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [os limites e a configuração dos aplicativos lógicos](../logic-apps/logic-apps-limits-and-config.md)
