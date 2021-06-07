---
title: Práticas recomendadas para o Azure Functions
description: Aprenda as práticas recomendadas e padrões para o Azure Functions.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 12/17/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5783f8092a6435b43ab8720df18cc5200e390d46
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100378240"
---
# <a name="best-practices-for-performance-and-reliability-of-azure-functions"></a>Práticas recomendadas para desempenho e confiabilidade de Azure Functions

Este artigo fornece orientações para melhorar o desempenho e confiabilidade de seus aplicativos de funções [sem servidor](https://azure.microsoft.com/solutions/serverless/).  

A seguir, estão as melhores práticas para criar e projetar soluções sem servidor usando o Azure Functions.

## <a name="avoid-long-running-functions"></a>Evite funções grandes de longa duração

As funções grandes de longa duração podem causar problemas de tempo limite inesperados. Para saber mais sobre os tempos limite de um determinado plano de hospedagem, confira [duração do tempo limite do aplicativo de funções](functions-scale.md#timeout).

Uma função pode se tornar grande devido a muitas dependências de Node.js. A importação dessas dependências pode causar aumento no tempo de carregamento resultando em tempos limite inesperados. As dependências são carregadas explícita e implicitamente. Um único módulo carregado pelo seu código pode carregar seus próprios módulos adicionais.

Sempre que possível, refatore funções grandes em conjuntos menores de funções que funcionem juntos e retornem respostas rápidas. Por exemplo, um webhook ou uma função de gatilho HTTP pode exigir uma resposta de confirmação dentro de um determinado limite de tempo; é comum que WebHooks exijam uma resposta imediata. Você pode passar o conteúdo do gatilho HTTP para uma fila para ser processado por uma função de gatilho de fila. Essa abordagem permite que você adie o trabalho real e retorne uma resposta imediata.

## <a name="cross-function-communication"></a>Comunicação entre funções

As [Durable Functions](durable/durable-functions-overview.md) e os [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) são projetados para gerenciar transições de estado e comunicação entre diversas funções.

Se não estiver usando Durable Functions ou aplicativos lógicos para integrar com várias funções, é melhor usar filas de armazenamento para comunicação entre funções. O principal motivo é que as filas de armazenamento são mais baratas e muito mais fáceis de provisionar do que outras opções de armazenamento.

Mensagens individuais em uma fila de armazenamento estão limitadas ao tamanho de 64 KB. Se você precisar passar mensagens maiores entre as funções, uma fila do Barramento de Serviço do Azure poderá ser usada para dar suporte a mensagens com tamanhos de até 256 KB na camada Standard e até 1 MB na camada Premium.

Tópicos de barramento de serviço são úteis se você precisar de filtragem de mensagens antes do processamento.

Hubs de eventos são úteis para oferecer suporte a comunicações de alto volume.

## <a name="write-functions-to-be-stateless"></a>Grave funções para serem sem estado

As funções devem ser sem estado e idempotentes se possível. Associe informações de estado necessárias aos seus dados. Por exemplo, um pedido sendo processado provavelmente teria um membro `state` associado. Uma função pode processar um pedido com base no estado enquanto a função em si permanece sem estado.

Funções de idempotentes são recomendadas especialmente com gatilhos de timer. Por exemplo, se você tiver algo que absolutamente deve ser executado uma vez por dia, grave-o para que ele possa ser executado a qualquer momento durante o dia com os mesmos resultados. A função pode sair quando não há trabalho para um dia específico. Também se uma execução anterior tiver falhado ao concluir, a próxima execução deve continuar de onde a anterior parou.

## <a name="write-defensive-functions"></a>Grave funções defensivas

Suponha que sua função pode encontrar uma exceção a qualquer momento. Projete suas funções com a capacidade de continuar de um ponto de falha anterior durante a próxima execução. Considere um cenário que requeira as seguintes ações:

1. Consulta de 10.000 linhas em um banco de dados.
2. Crie uma mensagem de fila para cada uma das linhas para processar ainda mais adiante na linha.

Dependendo de quão complexo for o seu sistema, você pode ter: serviços de downstream envolvidos com problemas incorretos, interrupções de rede ou limites de cota atingidos, etc. Todos eles podem afetar sua função a qualquer momento. Você precisa para projetar suas funções para estarem preparadas para isso.

Como o seu código reage se ocorrer uma falha após a inserção de 5.000 desses itens em uma fila para processamento? Controle itens em um conjunto concluído. Caso contrário, você pode inseri-los de novo posteriormente. Essa inserção dupla pode ter um impacto sério em seu fluxo de trabalho, portanto, [torne suas funções idempotentes](functions-idempotent.md). 

Se um item da fila já tiver sido processado, permita que sua função seja no-op.

Tire proveito de medidas defensivas já fornecidas para componentes usados na plataforma Azure Functions. Por exemplo, consulte **Tratamento de mensagens suspeitas na fila** na documentação de [gatilhos e associações de fila de Armazenamento do Microsoft Azure](functions-bindings-storage-queue-trigger.md#poison-messages).

## <a name="function-organization-best-practices"></a>Práticas recomendadas da organização de funções

Como parte de sua solução, você pode desenvolver e publicar várias funções. Essas funções geralmente são combinadas em um único aplicativo de funções, mas também podem ser executadas em aplicativos de funções separados. Nos planos de hospedagem Premium e dedicado (serviço de aplicativo), vários aplicativos de funções também podem compartilhar os mesmos recursos executando no mesmo plano. A forma como você agrupa suas funções e seus aplicativos de função pode afetar o desempenho, o dimensionamento, a configuração, a implantação e a segurança de sua solução geral. Não há regras que se apliquem a todos os cenários, portanto, considere as informações nesta seção ao planejar e desenvolver suas funções.

### <a name="organize-functions-for-performance-and-scaling"></a>Organizar funções de desempenho e dimensionamento

Cada função que você cria tem uma superfície de memória. Embora essa superfície seja geralmente pequena, ter muitas funções em um aplicativo de funções pode levar à inicialização mais lenta do seu aplicativo em novas instâncias. Isso também significa que o uso de memória geral do seu aplicativo de funções pode ser maior. É difícil dizer quantas funções devem estar em um único aplicativo, o que depende de sua carga de trabalho específica. No entanto, se sua função armazena muitos dados na memória, considere ter menos funções em um único aplicativo.

Se você executar vários aplicativos de funções em um único plano Premium ou plano dedicado (serviço de aplicativo), esses aplicativos serão todos dimensionados juntos. Se você tiver um aplicativo de funções que tenha um requisito de memória muito maior do que os outros, ele usará uma quantidade desproporcional de recursos de memória em cada instância na qual o aplicativo é implantado. Como isso pode deixar menos memória disponível para os outros aplicativos em cada instância, talvez você queira executar um aplicativo de função de alta memória usando a mesma forma em seu próprio plano de hospedagem separado.

> [!NOTE]
> Ao usar o [plano de consumo](./functions-scale.md), recomendamos sempre colocar cada aplicativo em seu próprio plano, já que os aplicativos são dimensionados de forma independente, de qualquer maneira.

Considere se você deseja agrupar funções com perfis de carga diferentes. Por exemplo, se você tiver uma função que processa muitas milhares de mensagens da fila e outra que seja chamada apenas ocasionalmente, mas tiver requisitos de memória alta, talvez você queira implantá-las em aplicativos de funções separados para que eles obtenham seus próprios conjuntos de recursos e sejam dimensionados de forma independente.

### <a name="organize-functions-for-configuration-and-deployment"></a>Organizar funções para configuração e implantação

Os aplicativos de funções têm um `host.json` arquivo, que é usado para configurar o comportamento avançado de gatilhos de função e o tempo de execução de Azure functions. As alterações no `host.json` arquivo se aplicam a todas as funções no aplicativo. Se você tiver algumas funções que precisam de configurações personalizadas, considere movê-las para seu próprio aplicativo de funções.

Todas as funções em seu projeto local são implantadas juntas como um conjunto de arquivos para seu aplicativo de funções no Azure. Talvez seja necessário implantar funções individuais separadamente ou usar recursos como [Slots de implantação](./functions-deployment-slots.md) para algumas funções e não para outras. Nesses casos, você deve implantar essas funções (em projetos de código separados) em diferentes aplicativos de funções.

### <a name="organize-functions-by-privilege"></a>Organizar funções por privilégio

As cadeias de conexão e outras credenciais armazenadas nas configurações do aplicativo dão a todas as funções no aplicativo de funções o mesmo conjunto de permissões no recurso associado. Considere minimizar o número de funções com acesso a credenciais específicas movendo funções que não usam essas credenciais para um aplicativo de funções separado. Você sempre pode usar técnicas como [encadeamento de funções](/learn/modules/chain-azure-functions-data-using-bindings/) para passar dados entre funções em diferentes aplicativos de funções.  

## <a name="scalability-best-practices"></a>Melhores práticas de escalabilidade

Há vários fatores que afetam a escala de instâncias do seu aplicativo de funções. Os detalhes são fornecidos na documentação de [dimensionamento de função](functions-scale.md).  A seguir, estão algumas das melhores práticas para garantir a escalabilidade ideal para um aplicativo de funções.

### <a name="share-and-manage-connections"></a>Gerenciar e compartilhar conexões

Reutilize conexões a recursos externos sempre que possível. Veja [como gerenciar conexões no Azure Functions](./manage-connections.md).

### <a name="avoid-sharing-storage-accounts"></a>Evite compartilhar contas de armazenamento

Ao criar um aplicativo de funções, você deve associá-lo a uma conta de armazenamento. A conexão da conta de armazenamento é mantida na [configuração de aplicativo AzureWebJobsStorage](./functions-app-settings.md#azurewebjobsstorage).

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>Não misture códigos de teste e de produção no mesmo aplicativo de funções

As funções em um aplicativo de funções compartilham recursos. Por exemplo, a memória é compartilhada. Se você estiver usando um aplicativo de funções em produção, não adicione recursos e funções de teste nele. Ele pode causar sobrecarga inesperada durante a execução de código de produção.

Cuidado com o que você carrega em seus aplicativos de funções de produção. A memória é dividida igualmente entre cada função no aplicativo.

Se você tiver um assembly compartilhado referenciado em várias funções .NET, coloque-o em uma pasta compartilhada comum. Caso contrário, você pode implantar acidentalmente várias versões do mesmo binário que se comportam de forma diferente entre as funções.

Não use o log detalhado no código de produção, que tem um impacto negativo no desempenho.

### <a name="use-async-code-but-avoid-blocking-calls"></a>Usar o código assíncrono, mas evitar chamadas de bloqueio

A programação assíncrona é uma prática recomendada, especialmente ao bloquear operações de e/s envolvidas.

No C#, sempre Evite referenciar a `Result` propriedade ou o `Wait` método de chamada em uma `Task` instância. Essa abordagem pode levar ao esgotamento de thread.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="use-multiple-worker-processes"></a>Usar vários processos de trabalho

Por padrão, qualquer instância de host para o Functions usa um único processo de trabalho. Para melhorar o desempenho, especialmente com tempos de execução de thread único como Python, use o [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) para aumentar o número de processos de trabalho por host (até 10). O Azure Functions tenta distribuir uniformemente invocações de função simultâneas entre esses trabalhos.

O FUNCTIONS_WORKER_PROCESS_COUNT se aplica a cada host que o Functions cria quando escala horizontalmente seu aplicativo para atender à demanda.

### <a name="receive-messages-in-batch-whenever-possible"></a>Receber mensagens em lote sempre que possível

Alguns gatilhos, como o Hub de Eventos, habilitam o recebimento de um lote de mensagens em uma única invocação.  As mensagens em lote têm um desempenho melhor.  É possível configurar o tamanho máximo do lote no arquivo `host.json`, conforme detalhado na [documentação de referência do host.json](functions-host-json.md)

Para funções C#, você pode alterar o tipo para uma matriz fortemente tipada.  Por exemplo, em vez de `EventData sensorEvent`, a assinatura do método pode ser `EventData[] sensorEvent`.  Para outros idiomas, você precisará definir explicitamente a propriedade cardinalidade em seu para para `function.json` `many` habilitar o envio em lote, [conforme mostrado aqui](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10).

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>Configurar comportamentos de host para lidar melhor com a simultaneidade

O arquivo `host.json` no aplicativo de funções permite configurar os comportamentos do runtime e do gatilho do host.  Além do envio em lote de comportamentos, é possível gerenciar a simultaneidade de diversos gatilhos. Geralmente, ajustar os valores nessas opções pode ajudar a escalar cada instância adequadamente para as demandas das funções invocadas.

As configurações na host.jsno arquivo se aplicam a todas as funções dentro do aplicativo, dentro de uma *única instância* da função. Por exemplo, se você tivesse um aplicativo de funções com duas funções e [`maxConcurrentRequests`](functions-bindings-http-webhook-output.md#hostjson-settings) solicitações HTTP definidas como 25, uma solicitação para o gatilho http contaria para as 25 solicitações simultâneas compartilhadas.  Quando esse aplicativo de funções é dimensionado para 10 instâncias, as dez funções permitem efetivamente 250 solicitações simultâneas (10 instâncias * 25 solicitações simultâneas por instância). 

Outras opções de configuração de host são encontradas no [ artigohost.jsno Configuration](functions-host-json.md).

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte os recursos a seguir:

* [Como gerenciar conexões no Azure Functions](manage-connections.md)
* [Práticas recomendadas do Serviço de Aplicativo do Azure](../app-service/app-service-best-practices.md)
