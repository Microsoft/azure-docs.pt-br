---
title: "Uso avançado dos Reliable Services | Microsoft Docs"
description: "Saiba mais sobre o uso avançado do Reliable Services do Service Fabric para obter maior flexibilidade em seus serviços."
services: Service-Fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: masnider
ms.assetid: f2942871-863d-47c3-b14a-7cdad9a742c7
ms.service: Service-Fabric
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: vturecek
ms.openlocfilehash: a87924faaf5c6c43716b06b6d70ab5100c61f097
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="advanced-usage-of-the-reliable-services-programming-model"></a>Uso avançado do modelo de programação de Serviços Confiáveis
O Service Fabric do Azure simplifica o desenvolvimento e o gerenciamento de serviços confiáveis com e sem estado. Este guia trata dos usos avançados dos Reliable Services para obter mais controle e flexibilidade sobre seus serviços. Antes de ler este guia, familiarize-se com [o do modelo de programação de Serviços Confiáveis](service-fabric-reliable-services-introduction.md).

Os serviços com e sem estado têm dois pontos de entrada principais para o código de usuário:

* `RunAsync(C#) / runAsync(Java)` é um ponto de entrada de finalidade geral para seu código de serviço.
* `CreateServiceReplicaListeners(C#)` e `CreateServiceInstanceListeners(C#) / createServiceInstanceListeners(Java)` servem para abrir ouvintes de comunicação para solicitações de cliente.

Para a maioria dos serviços, esses dois pontos de entrada são suficientes. Para casos raros, quando é necessário ter mais controle sobre o ciclo de vida do serviço, existem eventos de ciclo de vida adicionais disponíveis.

## <a name="stateless-service-instance-lifecycle"></a>Ciclo de vida de instância de serviço sem estado
O ciclo de vida de um serviço sem estado é muito simples. Um serviço sem estado só pode ser aberto, fechado ou anulado. `RunAsync` em um serviço sem estado é executado quando uma instância de serviço é aberta e cancelado quando uma instância de serviço é fechada ou anulada.

Embora `RunAsync` deva ser suficiente em quase todos os casos, os eventos abrir, fechar e anular em um serviço sem estado também estão disponíveis:

* `Task OnOpenAsync(IStatelessServicePartition, CancellationToken) - C# / CompletableFuture<String> onOpenAsync(CancellationToken) - Java` OnOpenAsync é chamado quando a instância do serviço sem estado está prestes a ser usada. As tarefas de inicialização do serviço estendido podem ser iniciadas nesse momento.
* `Task OnCloseAsync(CancellationToken) - C# / CompletableFuture onCloseAsync(CancellationToken) - Java` OnCloseAsync é chamado quando a instância do serviço sem estado está prestes a ser desligada de modo normal. Isso pode ocorrer quando o código de serviço estiver sendo atualizado, quando a instância do serviço estiver sendo movida devido ao balanceamento de carga ou quando for detectada uma falha temporária. OnCloseAsync pode ser usado para fechar todos os recursos com segurança, interromper todos os processamentos em segundo plano, terminar de salvar o estado externo ou fechar conexões existentes.
* `void OnAbort() - C# / void onAbort() - Java` OnAbort é chamado quando a instância do serviço sem estado está sendo desligada de modo forçado. Ele geralmente é chamado quando é detectada uma falha permanente no nó ou quando a malha de serviço não pode gerenciar confiavelmente o ciclo de vida da instância do serviço devido a falhas internas.

## <a name="stateful-service-replica-lifecycle"></a>Ciclo de vida de réplica de serviço com estado

> [!NOTE]
> Ainda não há suporte para Reliable Services com monitoração de estado em Java.
>
>

Um ciclo de vida de uma réplica de serviço com estado é muito mais complexo do que uma instância de serviço sem estado. Além dos eventos abrir, fechar e anular, uma réplica de serviço com estado sofre mudanças de função durante seu ciclo de vida. Quando uma réplica de serviço com estado muda de função, o evento `OnChangeRoleAsync` é disparado:

* `Task OnChangeRoleAsync(ReplicaRole, CancellationToken)` OnChangeRoleAsync é chamado quando a réplica de serviço com estado está alterando funções, por exemplo para primário ou secundário. Réplicas primárias recebem status de gravação (têm permissão para criar as Coleções Confiáveis e gravar nelas). Réplicas secundárias recebem status de leitura (podem somente ler das coleções confiáveis existentes). A maior parte do trabalho em um serviço com estado é executado na réplica primária. Réplicas secundárias podem realizar validação somente leitura, geração de relatórios, mineração de dados ou outros trabalhos somente leitura.

Em um serviço com estado, somente a réplica primária tem acesso de gravação para o estado e isso ocorre geralmente quando o serviço está executando o trabalho real. O método `RunAsync` em um serviço com estado é executado somente quando a réplica de serviço com estado é primária. O método `RunAsync` é cancelado quando a função de uma réplica primária é alterada para algo diferente de primária e também durante os eventos close e abort.

O uso do evento `OnChangeRoleAsync` permite que você execute o trabalho dependendo da função da réplica, bem como em resposta à mudança de função.

Um serviço com estado também fornece os mesmos quatro eventos do ciclo de vida que o serviço sem estado, com a mesma semântica e casos de uso:

```csharp
* Task OnOpenAsync(IStatefulServicePartition, CancellationToken)
* Task OnCloseAsync(CancellationToken)
* void OnAbort()
```

## <a name="next-steps"></a>Próximas etapas
Para tópicos mais avançados relacionados ao Service Fabric, consulte os artigos a seguir:

* [Configurando Reliable Services com estado](service-fabric-reliable-services-configuration.md)
* [Introdução à integridade da Malha do Serviço](service-fabric-health-introduction.md)
* [Usando relatórios de integridade do sistema para solução de problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [Configurando serviços com o Gerenciador de Recursos de Cluster do Service Fabric](service-fabric-cluster-resource-manager-configure-services.md)
