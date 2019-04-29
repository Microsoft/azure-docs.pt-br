---
title: Unidade testando serviços com estado no Azure Service Fabric | Microsoft Docs
description: Aprenda sobre os conceitos e práticas de testes unitários do Service Fabric Stateful Services.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/04/2018
ms.author: atsenthi
ms.openlocfilehash: ca473b9947a9b0df610a9c3dac66914b06cc9217
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60881446"
---
# <a name="unit-testing-stateful-services-in-service-fabric"></a>Unidade testando serviços com estado no Service Fabric

Este artigo aborda os conceitos e práticas de testes unitários do Service Fabric Stateful Services. O teste de unidade no Service Fabric merece suas próprias considerações, devido ao fato de que o código do aplicativo é executado ativamente em vários contextos diferentes. Este artigo descreve as práticas usadas para garantir que o código do aplicativo seja coberto em cada um dos diferentes contextos que ele pode executar.

## <a name="unit-testing-and-mocking"></a>Testes de unidade e zombaria
O teste de unidade no contexto deste artigo é um teste automatizado que pode ser executado no contexto de um executor de testes, como MSTest ou NUnit. Os testes de unidade contidos neste artigo não executam operações em um recurso remoto, como um banco de dados ou uma API RESTFul. Esses recursos remotos devem ser ridicularizados. Zombando no contexto deste artigo irá falsificar, registrar e controlar os valores de retorno para recursos remotos.

### <a name="service-fabric-considerations"></a>Considerações sobre o Service Fabric
Unidade testando um serviço stateful do Service Fabric tem várias considerações. Em primeiro lugar, o código de serviço é executado em vários nós, mas em diferentes funções. Os testes unitários devem avaliar o código em cada função para obter uma cobertura completa. Os diferentes papéis seriam Primário, Ativo Secundário, Inativo Secundário e Desconhecido. A função Nenhum normalmente não precisa de nenhuma cobertura especial, pois o Service Fabric considera essa função como nula ou nula. Em segundo lugar, cada nó irá mudar seu papel em qualquer ponto. Para obter cobertura completa, o caminho de execução de código deve ser testado com a ocorrência de alterações de papel.

## <a name="why-unit-test-stateful-services"></a>Por que a unidade testou os serviços com estado? 
Serviços de estado de teste de unidade podem ajudar a descobrir alguns erros comuns que são feitos que não necessariamente seriam capturados pelo aplicativo convencional ou teste de unidade específica de domínio. Por exemplo, se o serviço com estado tiver algum estado na memória, esse tipo de teste poderá verificar se esse estado na memória é mantido em sincronia entre cada réplica. Esse tipo de teste também pode verificar se um serviço com estado responde aos tokens de cancelamento transmitidos adequadamente pela orquestração do Service Fabric. Quando os cancelamentos são acionados, o serviço deve interromper qualquer operação longa e / ou assíncrona.  

## <a name="common-practices"></a>Práticas comuns

A seção a seguir informa sobre as práticas mais comuns para testes unitários de um serviço com monitoração de estado. Ele também aconselha o que uma camada de simulação deve ter para alinhar de perto a orquestração do Service Fabric e o gerenciamento de estado. Bibliotecas de simulação existem bibliotecas que fornecem essa funcionalidade. [ServiceFabric.Mocks](https://www.nuget.org/packages/ServiceFabric.Mocks/) a partir de 3.3.0 ou posterior é uma dessas bibliotecas que fornece a funcionalidade de simulação recomendada e segue as práticas descritas abaixo.

### <a name="arrangement"></a>Disposição

#### <a name="use-multiple-service-instances"></a>Usar várias instâncias do serviço
Testes de unidade devem executar várias instâncias de um serviço com estado. Isso simula o que realmente acontece no cluster em que o Service Fabric fornece várias réplicas que executam seu serviço em diferentes nós. Cada uma dessas instâncias estará executando sob um contexto diferente, no entanto. Ao executar o teste, cada instância deve ser preparada com a configuração de função esperada no cluster. Por exemplo, se for esperado que o serviço tenha um tamanho de réplica de destino de 3, o Service Fabric provisionaria três réplicas em nós diferentes. Um dos quais é o primário e os outros dois são secundários ativos.

Na maioria dos casos, o caminho de execução do serviço variará um pouco para cada uma dessas funções. Por exemplo, se o serviço não deve aceitar solicitações de um Active Secondary, o serviço pode ter uma verificação para este caso para lançar de volta uma exceção informativa que indica que uma solicitação foi tentada em um secundário. Ter várias instâncias permitirá que essa situação seja testada.

Além disso, ter várias instâncias permite que os testes mudem as funções de cada uma dessas instâncias para verificar se as respostas são consistentes, apesar das alterações na função.

#### <a name="mock-the-state-manager"></a>O Gerenciador de estado de simulação
O Gerente de Estado deve ser tratado como um recurso remoto e, portanto, ridicularizado. Ao ridicularizar o gerente de estado, é preciso haver algum armazenamento na memória subjacente para rastrear o que é salvo no gerenciador de estado, para que ele possa ser lido e verificado. Uma maneira simples de conseguir isso é criar instâncias de simulação de cada um dos tipos de coleções confiáveis. Dentro desses mocks, use um tipo de dados que se alinha de perto com as operações executadas nessa coleção. A seguir estão alguns tipos de dados sugeridos para cada coleta confiável

- IReliableDictionary<TKey, TValue> -> System.Collections.Concurrent.ConcurrentDictionary<TKey, TValue>
- IReliableQueue<T> -> System.Collections.Generic.Queue<T>
- IReliableConcurrentQueue<T> -> System.Collections.Concurrent.ConcurrentQueue<T>

#### <a name="many-state-manager-instances-single-storage"></a>Muitas instâncias do gerenciador de estado, armazenamento único
Como mencionado anteriormente, o Gerenciador de Estado e as Coleções Confiáveis devem ser tratados como um recurso remoto. Portanto, esses recursos devem e serão ridicularizados nos testes de unidade. No entanto, ao executar várias instâncias de um serviço com estado, será um desafio manter cada gerenciador de estado ridicularizado em sincronia entre diferentes instâncias de serviço com estado. Quando o serviço com estado está sendo executado no cluster, o Service Fabric cuida de manter o gerenciador de estado de cada réplica secundária consistente com a réplica primária. Portanto, os testes devem se comportar da mesma forma que possam simular mudanças de função.

Uma maneira simples de conseguir essa sincronização é usar um padrão singleton para o objeto subjacente que armazena os dados gravados em cada Coleção Confiável. Por exemplo, se um serviço com estado está usando um `IReliableDictionary<string, string>`. O gerente de estado simulado deve retornar uma simulação de `IReliableDictionary<string, string>`. Essa simulação pode usar um `ConcurrentDictionary<string, string>` para acompanhar os pares de chave/valor gravados. O `ConcurrentDictionary<string, string>` deve ser um singleton usado por todas as instâncias dos gerentes de estado passadas para o serviço.

#### <a name="keep-track-of-cancellation-tokens"></a>Manter o controle de tokens de cancelamento
Os tokens de cancelamento são um aspecto importante, porém comumente negligenciado, dos serviços com estado. Quando o Service Fabric inicializa uma réplica primária para um serviço com estado, um token de cancelamento é fornecido. Este token de cancelamento destina-se a sinalizar para o serviço quando é removido ou rebaixado para uma função diferente. O serviço com estado deve parar qualquer operação longa ou assíncrona para que o Service Fabric possa concluir o fluxo de trabalho de mudança de função.

Ao executar os testes de unidade, todos os tokens de cancelamento fornecidos para RunAsync, ChangeRoleAsync, OpenAsync e CloseAsync devem ser mantidos durante a execução do teste. Manter esses tokens permitirá que o teste simule um desligamento ou rebaixamento do serviço e verifique se o serviço responde adequadamente.

#### <a name="test-end-to-end-with-mocked-remote-resources"></a>Teste de ponta a ponta com recursos remotos simulados
Os testes de unidade devem executar o máximo possível do código do aplicativo que pode modificar o estado do serviço com informações de estado. É recomendado que os testes sejam mais completos de ponta a ponta. As únicas zombarias que existem são para registrar, simular e / ou verificar interações de recursos remotos. Isso inclui interações com o gerente de estado e coleções confiáveis. O snippet a seguir é um exemplo de pepino para um teste que demonstra os testes de ponta a ponta:

```
    Given stateful service named "fabric:/MyApp/MyService" is created
    And a new replica is created as "Primary" with id "111"
    And a new replica is created as "IdleSecondary" with id "222"
    And a new replica is created as "IdleSecondary" with id "333"
    And all idle secondary replicas are promoted to active secondary
    When a request is made to add the an employee "John Smith"
    And the active secondary replica "222" is promoted to primary
    And a request is made to get all employees
    Then the request should should return the "John Smith" employee
```

Este teste afirma que os dados que estão sendo capturados em uma réplica estão disponíveis para uma réplica secundária quando ela é promovida para primária. Supondo que uma coleção confiável seja a loja de apoio para os dados do funcionário, uma possível falha que poderia ser detectada com esse teste seria se o código do aplicativo não fosse executado `CommitAsync` na transação para salvar o novo funcionário. Nesse caso, a segunda solicitação para obter funcionários não retornaria o funcionário adicionado pela primeira solicitação.

### <a name="acting"></a>Agindo
#### <a name="mimic-service-fabric-replica-orchestration"></a>Imitar a orquestração de réplica do Service Fabric
Ao gerenciar várias instâncias de serviço, os testes devem inicializar e desmontar esses serviços da mesma maneira que a orquestração do Service Fabric. Por exemplo, quando um serviço é criado em uma nova réplica primária, o Service Fabric invoca CreateServiceReplicaListener, OpenAsync, ChangeRoleAsync e RunAsync. Os eventos do ciclo de vida estão documentados nos seguintes artigos:

- [Stateful Service Startup](service-fabric-reliable-services-lifecycle.md#stateful-service-startup)
- [Stateful Service Shutdown](service-fabric-reliable-services-lifecycle.md#stateful-service-shutdown)
- [Stateful Service Primary Swaps](service-fabric-reliable-services-lifecycle.md#stateful-service-primary-swaps)

#### <a name="run-replica-role-changes"></a>Executar alterações de função de réplica
Os testes de unidade devem alterar as funções das instâncias de serviço da mesma maneira que a orquestração do Service Fabric. A máquina de estado da função está documentada no seguinte artigo:

[Replica Role State Machine](service-fabric-concepts-replica-lifecycle.md#replica-role)

A simulação de alterações de funções é um dos aspectos mais críticos do teste e pode revelar problemas em que o estado da réplica não é consistente entre si. O estado de réplica inconsistente pode ocorrer devido ao armazenamento no estado de memória em variáveis de instância estáticas ou de nível de classe. Exemplos disso podem ser tokens de cancelamento, enums e objetos / valores de configuração. Isso também garantirá que o serviço esteja respeitando os tokens de cancelamento fornecidos durante o RunAsync para permitir que a alteração de função ocorra. A simulação de alterações de função também pode revelar problemas que podem surgir se o código não for gravado para permitir uma invocação do RunAsync várias vezes.

#### <a name="cancel-cancellation-tokens"></a>Cancelar tokens de cancelamento
Deverão existir testes de unidade em que o token de cancelamento fornecido ao RunAsync seja cancelado. Isso permitirá que o teste verifique se o serviço é encerrado normalmente. Durante esse desligamento, qualquer operação longa ou assíncrona deve ser interrompida. Exemplo de um processo de longa execução que pode existir em um serviço é aquele que ouve mensagens em uma fila confiável. Isso pode existir diretamente no RunAsync ou em um thread de segundo plano. A implementação deve incluir lógica para sair da operação se esse token de cancelamento for cancelado.

Se os serviços com estado fizerem uso de qualquer cache ou estado na memória que deve existir apenas no primário, ele deve ser descartado no momento. Isso é para garantir que esse estado seja consistente se o nó se tornar um primário novamente mais tarde. O teste de cancelamento permitirá que o teste verifique se esse estado foi descartado corretamente.

#### <a name="execute-requests-against-multiple-replicas"></a>Executar solicitações contra várias réplicas
Os testes de declaração devem executar o mesmo pedido em relação a réplicas diferentes. Quando emparelhado com alterações de função, problemas de consistência podem ser descobertos. Um exemplo de teste pode executar as seguintes etapas:
1. Executar uma solicitação de gravação contra o primário atual
2. Execute uma solicitação de leitura que retorne os dados gravados na etapa 1 em relação à
3. Promova um secundário para primário. Isso também deve rebaixar o primário atual para secundário
4. Execute a mesma solicitação de leitura da etapa 2 em relação ao novo secundário.

Na última etapa, o teste pode afirmar que os dados retornados são consistentes. Um possível problema que isso poderia descobrir é que os dados que estão sendo retornados pelo serviço podem estar na memória, mas suportados, no final, por uma coleta confiável. Que os dados na memória podem não ser mantidos sincronizados adequadamente com o que existe na coleção confiável.

Os dados na memória são normalmente usados para criar índices secundários ou agregações de dados que existem em uma coleção confiável.

### <a name="asserting"></a>Declarando
#### <a name="ensure-responses-match-across-replicas"></a>Garantir que as respostas correspondam às réplicas
Testes de unidade devem afirmar que uma resposta para uma determinada solicitação é consistente em várias réplicas após a transição para a primária. Isso pode revelar possíveis problemas em que os dados fornecidos na resposta não são protegidos por uma coleta confiável ou mantidos na memória sem um mecanismo para sincronizar esses dados entre as réplicas. Isso garantirá que o serviço retorne respostas consistentes após o Service Fabric reequilibrar ou fazer failover para uma nova réplica primária.

#### <a name="verify-service-respects-cancellation"></a>Verifique se o cancelamento de aspectos do serviço
Processos longos ou assíncronos que devem ser encerrados quando um token de cancelamento é cancelado devem ser verificados que eles realmente terminaram após o cancelamento. Isso garantirá que, apesar das funções de alteração de réplica, os processos que não são destinados a continuar em execução na réplica não primária parem antes da conclusão da transição. Isso também pode revelar problemas em que um processo desse tipo impede que uma alteração de função ou uma solicitação de desligamento do Service Fabric seja concluída.

#### <a name="verify-which-replicas-should-serve-requests"></a>Verificar quais réplicas devem atender a solicitações
Os testes devem declarar o comportamento esperado se uma solicitação for roteada para uma réplica não primária. O Service Fabric fornece a capacidade de ter réplicas secundárias atendendo a solicitações. No entanto, as gravações em coleções confiáveis podem ocorrer apenas a partir da réplica primária. Se o seu aplicativo pretender que apenas as réplicas primárias atendam a solicitações ou que apenas um subconjunto de solicitações possa ser tratado por um secundário, os testes deverão confirmar o comportamento esperado para os casos positivos e negativos. O caso negativo sendo um pedido é roteado para uma réplica que não deve manipular a solicitação e, o positivo sendo o oposto.

## <a name="next-steps"></a>Próximas etapas
Saiba como [serviços com monitoração de estado do teste de unidade](service-fabric-how-to-unit-test-stateful-services.md).