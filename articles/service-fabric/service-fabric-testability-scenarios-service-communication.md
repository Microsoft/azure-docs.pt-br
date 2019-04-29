---
title: 'Possibilidade de teste: Comunicação de serviço | Microsoft Docs'
description: As comunicação entre serviços é um ponto de integração essencial de um aplicativo da Malha do Serviço. Este artigo aborda as considerações de design e as técnicas de teste.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 017557df-fb59-4e4a-a65d-2732f29255b8
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 529c8d74b6e0a63a7969f31d5b5e8073ecb79411
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60543216"
---
# <a name="service-fabric-testability-scenarios-service-communication"></a>Cenários de possibilidade de teste do Service Fabric: Comunicação de serviço
Os estilos de arquitetura orientada a serviços e microsserviços surgem naturalmente no Azure Service Fabric. Nesses tipos de arquiteturas distribuídas, os aplicativos de microsserviço divididos em componentes são compostos normalmente por vários serviços que precisam se comunicar. Mesmo nos casos mais simples, há geralmente pelo menos um serviço Web sem estado e um serviço de armazenamento de dados com estado que precisam se comunicar.

A comunicação entre os serviços é um ponto de integração essencial de um aplicativo, já que cada serviço expõe uma API remota para outros serviços. Trabalhar com um conjunto de limites de API que envolve E/S geralmente exige algum cuidado e uma boa quantidade de teste e validação.

Há várias considerações a serem feitas quando esses limites de serviço são conectados em um sistema distribuído:

* *Protocolo de transporte*. Você usará HTTP para maior interoperabilidade, ou um protocolo binário personalizado para taxa de transferência máxima?
* *Manipulação de erros*. Como os erros transitórios e permanentes serão tratados? O que acontecerá quando um serviço for movido para um nó diferente?
* *Tempos limite e latência*. Em aplicativos com várias camadas, como cada camada de serviço lidará com a latência por meio da pilha e até o usuário?

Se você usa um dos componentes integrados de comunicação de serviço fornecidos pelo Service Fabric ou compila seu próprio, testar as interações entre os serviços é fundamental para garantir a resiliência em seu aplicativo.

## <a name="prepare-for-services-to-move"></a>Preparar-se para mover os serviços
As instâncias do serviço podem se movimentar com o tempo. Isso acontece especialmente quando são configuradas com métricas de carga para balanceamento personalizado e ideal de recursos. O Service Fabric move suas instâncias de serviço a fim de maximizar a disponibilidade até mesmo durante atualizações, failovers, expansão e outras situações que ocorrem durante o tempo de vida de um sistema distribuído.

À medida que os serviços se movimentam pelo cluster, seus clientes e outros serviços devem estar preparados para lidar com dois cenários ao conversar com um serviço:

* A instância do serviço ou réplica da partição foi movida desde a última vez que você conversou com ela. Essa é uma parte normal do ciclo de vida do serviço e deve acontecer durante o tempo de vida do seu aplicativo.
* A instância do serviço ou a réplica da partição está se movimentando. Embora o failover de um serviço de um nó para outro ocorra rapidamente na Malha do Serviço, pode haver um atraso na disponibilidade se o componente de comunicação do serviço iniciar de forma lenta.

A manipulação tranquila desses cenários é importante para um sistema em execução adequada. Para fazer isso, lembre-se que:

* Todos os serviços aos quais é possível se conectar têm um *endereço* no qual escutam (por exemplo, HTTP ou WebSockets). Quando uma instância de serviço ou partição se move, altera seu ponto de extremidade do endereço. (É movido para um nó diferente com um endereço IP diferente). Se você estiver usando os componentes integrados de comunicação, eles manipularão a nova resolução dos endereços de serviço para você.
* Pode haver um aumento temporário na latência do serviço à medida que a instância do serviço começa sua escuta novamente. Isso depende da rapidez com que serviço abre após a movimentação da instância do serviço.
* Quaisquer conexões existentes precisam ser fechadas e reabertas quando o serviço for aberto em um novo nó. Um desligamento ou reinicialização de nó proporciona tempo para o desligamento correto das conexões existentes.

### <a name="test-it-move-service-instances"></a>Testá-lo: Mover instâncias do serviço
Usando ferramentas de Possibilidade de Teste do Service Fabric, é possível criar um cenário de teste para testar essas situações de maneiras diferentes:

1. Mova a réplica primária de um serviço com estado.
   
    A réplica primária de uma partição de serviço com estado pode ser movida por vários motivos. Use isso para direcionar a réplica primária de uma partição específica para ver como os serviços reagem à movimentação de uma maneira muito controlada.
   
    ```powershell
   
    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService
   
    ```
2. Parar um nó.
   
    Quando um nó for interrompido, o Service Fabric moverá todas as instâncias ou partições de serviço que estavam nesse nó para um dos nós disponíveis no cluster. Use isso para testar uma situação na qual um nó é perdido em seu cluster e todas as instâncias e réplicas do serviço nesse nó precisam ser movidas.
   
    Pode-se interromper um nó usando o cmdlet **Stop-ServiceFabricNode** PowerShell:
   
    ```powershell
   
    PS > Stop-ServiceFabricNode -NodeName Node_1
   
    ```

## <a name="maintain-service-availability"></a>Manter a disponibilidade do serviço
O Service Fabric é uma plataforma projetada para fornecer alta disponibilidade para seus serviços. Em casos extremos, porém, os problemas de infraestrutura subjacentes ainda podem causar indisponibilidade. Também é importante fazer um teste para esses cenários.

Serviços com estado usam um sistema baseado em quorum para replicar o estado a fim de obter alta disponibilidade. Isso significa que um quorum de réplicas precisa estar disponível a fim de executar operações de gravação. Em casos raros, como em uma falha generalizada de hardware, talvez um quorum de réplicas não esteja disponível. Nesses casos, você não poderá executar operações de gravação, mas ainda conseguirá executar operações de leitura.

### <a name="test-it-write-operation-unavailability"></a>Testá-lo: Operação de gravação
Ao usar as ferramentas de possibilidade de teste no Service Fabric, pode-se injetar uma falha que induza a perda de quorum como teste. Embora tal cenário seja raro, é importante que os clientes e serviços que dependem do serviço com estado sejam preparados para lidar com situações nas quais não podem fazer solicitações de gravação para ele. Também é importante que o próprio serviço com estado reconheça essa possibilidade e possa comunicá-la adequadamente aos chamadores.

É possível induzir a perda de quorum usando o cmdlet **Invoke-ServiceFabricPartitionQuorumLoss** PowerShell:

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode QuorumReplicas -QuorumLossDurationInSeconds 20

```

Neste exemplo, definimos `QuorumLossMode` como `QuorumReplicas` para indicar que desejamos induzir perda de quórum sem interromper todas as réplicas. Dessa forma, as operações de leitura ainda são possíveis. Para testar um cenário no qual uma partição inteira não está disponível, você pode definir essa opção como `AllReplicas`.

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre as ações de possibilidade de teste](service-fabric-testability-actions.md)

[Saiba mais sobre os cenários de possibilidade de teste](service-fabric-testability-scenarios.md)

