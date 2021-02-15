---
title: Visão geral da limitação do barramento de serviço do Azure | Microsoft Docs
description: Visão geral da limitação do barramento de serviço – camadas Standard e Premium.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 436f9a40269f7eea4e31b55b9657d38849876eb4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85340947"
---
# <a name="throttling-operations-on-azure-service-bus"></a>Operações de limitação no barramento de serviço do Azure

As soluções nativas de nuvem fornecem uma noção de recursos ilimitados que podem ser dimensionados com sua carga de trabalho. Embora essa noção seja mais verdadeira na nuvem do que com os sistemas locais, ainda existem limitações que existem na nuvem.

Essas limitações podem causar a limitação de solicitações de aplicativos cliente nas camadas Standard e Premium, conforme discutido neste artigo. 

## <a name="throttling-in-azure-service-bus-standard-tier"></a>Limitação na camada standard do barramento de serviço do Azure

A camada standard do barramento de serviço do Azure funciona como uma configuração multilocatário com um modelo de preços pago conforme o uso. Aqui, vários namespaces no mesmo cluster compartilham os recursos alocados. A camada Standard é a opção recomendada para ambientes de desenvolvimento, teste e QA, juntamente com sistemas de produção de baixa taxa de transferência.

No passado, o barramento de serviço do Azure tinha limites de alta limitação estritamente dependentes da utilização de recursos. No entanto, há uma oportunidade de refinar a lógica de limitação e fornecer um comportamento de limitação previsível para todos os namespaces que estão compartilhando esses recursos.

Em uma tentativa de garantir o uso e a distribuição justas de recursos em todos os namespaces padrão do barramento de serviço do Azure que usam os mesmos recursos, a lógica de limitação foi modificada para ser baseada em crédito.

> [!NOTE]
> É importante observar que a limitação não é ***novidade*** no barramento de serviço do Azure ou em qualquer serviço de nuvem nativo.
>
> A limitação baseada em crédito é simplesmente refinar a maneira como vários namespaces compartilham recursos em um ambiente de camada padrão multilocatário e, portanto, habilitando o uso justo por todos os namespaces que compartilham os recursos.

### <a name="what-is-credit-based-throttling"></a>O que é a limitação baseada em crédito?

A limitação baseada em crédito limita o número de operações que podem ser executadas em um determinado namespace em um período de tempo específico. 

Abaixo está o fluxo de trabalho para a limitação baseada em crédito- 

  * No início de cada período de tempo, fornecemos um determinado número de créditos para cada namespace.
  * Todas as operações executadas pelos aplicativos cliente remetente ou destinatário serão contadas em relação a esses créditos (e subtraídos dos créditos disponíveis).
  * Se os créditos estiverem esgotados, as operações subsequentes serão limitadas até o início do próximo período de tempo.
  * Os créditos são reabastecidos no início do próximo período de tempo.

### <a name="what-are-the-credit-limits"></a>Quais são os limites de crédito?

Os limites de crédito estão atualmente definidos como ' 1000 ' créditos a cada segundo (por namespace).

Nem todas as operações são criadas de forma igual. Aqui estão os custos de crédito de cada uma das operações- 

| Operação | Custo de crédito|
|-----------|-----------|
| Operações de dados (Send, SendAsync, Receive, ReceiveAsync, Peek) |1 crédito por mensagem |
| Operações de gerenciamento (criar, ler, atualizar, excluir em filas, tópicos, assinaturas, filtros) | 10 créditos |

> [!NOTE]
> Observe que, ao enviar para um tópico, cada mensagem é avaliada em relação aos filtros antes de ser disponibilizada na assinatura.
> Cada avaliação de filtro também conta com relação ao limite de crédito (ou seja, 1 crédito por avaliação de filtro).
>

### <a name="how-will-i-know-that-im-being-throttled"></a>Como saber que estou sendo limitado?

Quando as solicitações do aplicativo cliente estiverem sendo limitadas, a resposta do servidor abaixo será recebida pelo seu aplicativo e registrada em log.

```
The request was terminated because the entity is being throttled. Error code: 50009. Please wait 2 seconds and try again.
```

### <a name="how-can-i-avoid-being-throttled"></a>Como posso evitar a limitação?

Com recursos compartilhados, é importante impor algum tipo de uso justo em vários namespaces do barramento de serviço que compartilham esses recursos. A limitação garante que qualquer pico em uma única carga de trabalho não faz com que outras cargas de trabalho nos mesmos recursos sejam limitadas.

Como mencionado posteriormente neste artigo, não há nenhum risco de ser limitado porque os SDKs do cliente (e outras ofertas de PaaS do Azure) têm a política de repetição padrão interna. Todas as solicitações limitadas serão repetidas com a retirada exponencial e, eventualmente, serão passadas quando os créditos forem reabastecidos.

É compreensível que alguns aplicativos possam ser sensíveis à limitação. Nesse caso, é recomendável [migrar seu namespace standard do barramento de serviço atual para Premium](service-bus-migrate-standard-premium.md). 

Na migração, você pode alocar recursos dedicados ao namespace do barramento de serviço e dimensionar adequadamente os recursos se houver um pico na carga de trabalho e reduzir a probabilidade de ser limitado. Além disso, quando sua carga de trabalho reduz a níveis normais, você pode reduzir verticalmente os recursos alocados para seu namespace.

## <a name="throttling-in-azure-service-bus-premium-tier"></a>Limitação na camada Premium do barramento de serviço do Azure

A camada [Premium do barramento de serviço do Azure](service-bus-premium-messaging.md) aloca recursos dedicados, em termos de unidades de mensagens, a cada configuração de namespace pelo cliente. Esses recursos dedicados fornecem taxa de transferência e latência previsíveis e são recomendados para alta taxa de transferência ou sistemas de nível de produção confidenciais.

Além disso, a camada Premium também permite que os clientes aumentem sua capacidade de taxa de transferência quando experimentarem picos na carga de trabalho.

### <a name="how-does-throttling-work-in-service-bus-premium"></a>Como a limitação funciona no barramento de serviço Premium?

Com alocação de recursos exclusiva para o barramento de serviço Premium, a limitação é puramente controlada pelas limitações dos recursos alocados para o namespace.

Se o número de solicitações for maior do que os recursos atuais podem ser atendidos, as solicitações serão limitadas.

### <a name="how-will-i-know-that-im-being-throttled"></a>Como saber que estou sendo limitado?

Há várias maneiras de identificar a limitação no barramento de serviço Premium do Azure- 
  * **As solicitações limitadas** aparecem no [Azure monitor métricas de solicitação](service-bus-metrics-azure-monitor.md#request-metrics) para identificar quantas solicitações foram limitadas.
  * Alto **uso da CPU** indica que a alocação de recursos atual é alta e as solicitações podem ser limitadas se a carga de trabalho atual não for reduzida.
  * O **uso de memória** alta indica que a alocação de recursos atual é alta e as solicitações podem ser limitadas se a carga de trabalho atual não for reduzida.

### <a name="how-can-i-avoid-being-throttled"></a>Como posso evitar a limitação?

Como o namespace Premium do barramento de serviço já tem recursos dedicados, você pode reduzir a possibilidade de ficar limitado ao escalar verticalmente o número de unidades de mensagens alocadas para seu namespace no evento (ou antecipação) de um pico na carga de trabalho.

A expansão/redução pode ser obtida com a criação de [runbooks](../automation/automation-create-alert-triggered-runbook.md) que podem ser disparados por alterações nas métricas acima.

## <a name="faqs"></a>Perguntas frequentes

### <a name="how-does-throttling-affect-my-application"></a>Como a limitação afeta meu aplicativo?

Quando uma solicitação é limitada, ela indica que o serviço está ocupado porque está voltado a mais solicitações do que os recursos permitem. Se a mesma operação for tentada novamente após alguns instantes, depois que o serviço tiver trabalhado por meio de sua carga de trabalho atual, a solicitação poderá ser respeitada.

Como a limitação é o comportamento esperado de qualquer serviço nativo de nuvem, criamos a lógica de repetição no próprio SDK do barramento de serviço do Azure. O padrão é definido como repetição automática com um retirada exponencial para garantir que não tenhamos a mesma solicitação sendo limitada a cada vez.

A lógica de repetição padrão será aplicada a todas as operações.

### <a name="does-throttling-result-in-data-loss"></a>A limitação resulta em perda de dados?

O barramento de serviço do Azure é otimizado para persistência, garantimos que todos os dados enviados ao barramento de serviço estejam comprometidos com o armazenamento antes que o serviço reconheça o sucesso da solicitação.

Depois que a solicitação for bem-sucedida ' ACK ' (confirmada) pelo barramento de serviço, significa que o barramento de serviço processou a solicitação com êxito. Se o barramento de serviço retornar um ' NACK ' (falha), significa que o barramento de serviço não pôde processar a solicitação e o aplicativo cliente deve repetir a solicitação.

No entanto, quando uma solicitação é limitada, o serviço está implicando que ele não pode aceitar e processar a solicitação no momento devido a limitações de recursos. Isso **não** implica nenhum tipo de perda de dados porque o barramento de serviço simplesmente não examinou a solicitação. Nesse caso, depender da política de repetição padrão do SDK do barramento de serviço garante que a solicitação seja eventualmente processada.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações e exemplos de uso do sistema de mensagens do Barramento de Serviço, consulte os tópicos avançados a seguir:

* [Visão geral de mensagens do Barramento de Serviço](service-bus-messaging-overview.md)
* [Início rápido: Enviar e receber mensagens usando o portal do Azure e .NET](service-bus-quickstart-portal.md)
* [Tutorial: Atualizar estoque usando o portal do Azure e tópicos/assinaturas](service-bus-tutorial-topics-subscriptions-portal.md)

