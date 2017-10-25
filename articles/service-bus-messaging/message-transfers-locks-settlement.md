---
title: "Transferência de mensagem, bloqueios e liquidação do Barramento de Serviço do Azure | Microsoft Docs"
description: "Visão geral das transferências de mensagens do Barramento de Serviço e das operações de liquidação"
services: service-bus-messaging
documentationcenter: 
author: clemensv
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2017
ms.author: sethm
ms.openlocfilehash: a4d14aef8d24f1754db6370505e3f5f8e54d0278
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="message-transfers-locks-and-settlement"></a>Transferências de mensagem, bloqueios e liquidação

A funcionalidade mais central de um agente de mensagens, como o Barramento de Serviço, é aceitar mensagens em uma fila ou tópico e mantê-las disponíveis para recuperação posterior. *Enviar* é o termo normalmente usado para a transferência de uma mensagem para o agente de mensagens. *Receber* é o termo normalmente usado para a transferência de uma mensagem para um cliente que vai recuperá-la.

Quando um cliente envia uma mensagem, ele normalmente quer saber se a mensagem foi transferida para o agente e aceita por ele ou se ocorreu algum tipo de erro. Essa confirmação positiva ou negativa define o entendimento entre o cliente e o agente sobre o estado de transferência da mensagem e, portanto, é conhecida como *liquidação*.

Da mesma forma, quando o agente transfere uma mensagem para um cliente, os dois desejam definir um entendimento sobre o êxito do processamento da mensagem e se ela pode ser removida, ou se a entrega ou o processamento da mensagem falhou e talvez precise ser entregue novamente.

## <a name="settling-send-operations"></a>Liquidando operações de envio

Usando um dos clientes de API do Barramento de Serviço com suporte, as operações de envio para o Barramento de Serviço são sempre explicitamente liquidadas, o que significa que a operação da API aguarda a chegada de um resultado de aceitação do Barramento de Serviço para concluir a operação de envio.

Se a mensagem foi rejeitada pelo Barramento de Serviço, a rejeição contém um indicador de erro e um texto com uma "id-de-rastreamento" dentro dele. O motivo da rejeição também inclui informações sobre a possibilidade de repetição da operação com algum grau de êxito. No cliente, essas informações são transformadas em uma exceção e geradas para o chamador da operação de envio. Se a mensagem foi aceita, a operação é concluída silenciosamente.

Ao usar o protocolo AMQP, que é o protocolo exclusivo para os clientes .NET Standard e Java e [que é uma opção para o cliente do .NET Framework](service-bus-amqp-dotnet.md), as transferências de mensagem e liquidações são completamente assíncronas e colocadas no pipeline, e é recomendável que você use as variantes de API de modelo de programação assíncrona.

Um remetente pode enviar várias mensagens durante a transmissão em sucessão rápida sem ter que esperar cada mensagem ser confirmada, como seria o caso com o protocolo SBMP ou o HTTP 1.1. Essas operações de envio assíncrono são concluídas conforme as respectivas mensagens são aceitas e armazenadas em entidades particionadas ou quando as operações de envio para entidades diferentes se sobrepõem. As conclusões também podem ocorrer fora da ordem de envio original.

A estratégia para lidar com o resultado das operações de envio pode ter impacto imediato e significativo no desempenho do seu aplicativo. Os exemplos nesta seção foram escritos em C# e se aplicam equivalentemente ao Java Futures.

Se o aplicativo produz picos de mensagens, como ilustrado aqui com um loop simples, e tem que aguardar a conclusão de cada operação de envio antes de enviar a mensagem seguinte, seja em forma síncrona ou assíncrona da API, o envio de 10 mensagens só é concluído após 10 ciclos de liquidação completos sequenciais.

Com uma distância de latência de viagem de ida e volta no protocolo TCP de 70 milissegundos de um site local até o Barramento de Serviço, e com apenas 10 ms para que o Barramento de Serviço aceite e armazene cada mensagem, o loop abaixo demora pelo menos 8 segundos, sem contar o tempo de transferência de carga ou os possíveis efeitos de congestionamento de rota:

```csharp
for (int i = 0; i < 100; i++)
{
  // creating the message omitted for brevity
  await client.SendAsync(…);
}
```

Se o aplicativo inicia as dez operações de envio assíncrono em sucessão imediata e aguarda suas respectivas conclusões separadamente, os tempos de ida e volta para as dez operações de envio se sobrepõem. As dez mensagens são transferidas em sucessão imediata, podendo até mesmo compartilhar quadros TCP, e a duração total da transferência depende bastante do tempo relativo à rede que leva para transferir as mensagens para o agente.

Fazendo as mesmas suposições do loop anterior, o tempo total de execução sobreposto para o seguinte loop pode ficar abaixo de um segundo:

```csharp
var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  tasks.Add(client.SendAsync(…));
}
await Task.WhenAll(tasks.ToArray());
```

É importante observar que todos os modelos de programação assíncrona usam alguma forma de fila de trabalho oculta e baseada na memória que contém todas as operações pendentes. Quando [SendAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.sendasync#Microsoft_Azure_ServiceBus_QueueClient_SendAsync_Microsoft_Azure_ServiceBus_Message_) (C#) ou **Enviar** (Java) retornam, a tarefa de envio é enfileirada na fila de trabalho, mas protocolo só começa quando é a vez da tarefa ser executada. Em caso de código que tende a enviar picos de mensagens por push, e quando a questão da confiabilidade for importante, é preciso ter cuidado para não colocar todas as mensagens “em trânsito” ao mesmo tempo, já que todas as mensagens enviadas ocupam memória até serem efetivamente enviadas.

Semáforos, conforme mostrado no trecho de código em C# a seguir, são objetos de sincronização que permitem essa limitação no nível do aplicativo quando necessário. Esse uso do semáforo permite no máximo 10 mensagens em trânsito ao mesmo tempo. Um dos 10 bloqueios de semáforo disponíveis é usado antes do envio e liberado quando o envio é concluído. A 11ª passagem pelo loop aguarda até que um dos envios anteriores seja concluído para liberar seu bloqueio:

```csharp
var semaphore = new SemaphoreSlim(10);

var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  await semaphore.WaitAsync();

  tasks.Add(client.SendAsync(…).ContinueWith((t)=>semaphore.Release()));
}
await Task.WhenAll(tasks.ToArray());
```

Os aplicativos **nunca** devem iniciar uma operação de envio assíncrono de forma a "disparar e esquecer", sem recuperar o resultado da operação. Isso pode sobrecarregar a memória da fila interna e invisível ao ponto de esgotamento e impedir que o aplicativo detecte erros de envio:

```csharp
for (int i = 0; i < 100; i++)
{

  client.SendAsync(message); // DON’T DO THIS
}
```

Com um cliente AMQP de baixo nível, o Barramento de Serviço também aceita transferências "previamente liquidadas". Uma transferência previamente liquidada é uma operação de disparar e esquecer cujo resultado não é relatado para o cliente, e a mensagem é considerada liquidada quando enviada. A falta de comentários para o cliente também indica que nenhum dado acionável está disponível para o diagnóstico, o que significa que esse modo não está qualificado para obter ajuda por meio do suporte do Azure.

## <a name="settling-receive-operations"></a>Liquidando operações de recebimento

Para operações de recebimento, os clientes da API do Barramento de Serviço habilitam dois modos explícitos diferentes: *Receive-and-Delete* e *Peek-Lock*.

O modo [Receive-and-Delete](/dotnet/api/microsoft.servicebus.messaging.receivemode) diz ao agente para considerar todas as mensagens que ele envia para o cliente destinatário como liquidadas quando enviadas. Isso significa que a mensagem é considerada consumida assim que o agente a envia. Se a transferência de mensagem falhar, a mensagem será perdida.

A vantagem desse modo é que o destinatário não precisa realizar outra ação em relação à mensagem e também não sofre atraso por causa do resultado da liquidação. Se os dados contidos nas mensagens individuais têm valor baixo e/ou só são úteis por um período muito curto, esse modo é uma opção razoável.

O modo [Peek-Lock](/dotnet/api/microsoft.servicebus.messaging.receivemode) diz ao agente que o cliente destinatário deseja liquidar as mensagens recebidas explicitamente. A mensagem é disponibilizada para o destinatário para ser processada enquanto é mantida em um bloqueio exclusivo no serviço, de forma que outros destinatários concorrentes não possam vê-la. A duração do bloqueio é inicialmente definida no nível da assinatura ou da fila e pode ser estendida pelo cliente proprietário do bloqueio por meio da operação [RenewLock](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_).

Quando uma mensagem é bloqueada, outros clientes destinatários da mesma fila ou assinatura podem usar bloqueios e recuperar as mensagens disponíveis seguintes que não estejam em bloqueio ativo. Quando o bloqueio de uma mensagem é liberado explicitamente ou expira, a mensagem é exibida novamente na frente da ordem de recuperação para nova entrega ou próxima a ela.

Quando a mensagem é liberada repetidamente por destinatários ou eles deixam o bloqueio expirar determinada quantidade de vezes ([maxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount#Microsoft_ServiceBus_Messaging_QueueDescription_MaxDeliveryCount)), a mensagem é removida da fila ou assinatura e colocada na fila de mensagens mortas automaticamente.

O cliente destinatário inicia a liquidação de uma mensagem recebida com uma confirmação positiva quando chama [Complete](/dotnet/api/microsoft.servicebus.messaging.queueclient.complete#Microsoft_ServiceBus_Messaging_QueueClient_Complete_System_Guid_) no nível da API. Isso indica ao agente que a mensagem foi processada com êxito e ela é removida da fila ou assinatura. O agente responde à intenção de liquidação do destinatário com uma resposta que indica se a liquidação pode ser feita.

Quando o cliente destinatário falha ao processar uma mensagem, mas quer que a mensagem seja entregue novamente, ele pode solicitar explicitamente a liberação e o desbloqueio imediatos da mensagem chamando [Abandon](/dotnet/api/microsoft.servicebus.messaging.queueclient.abandon) ou pode não fazer nada e permitir que o bloqueio expire.

Se um cliente destinatário falha ao processar uma mensagem e sabe que a nova entrega e tentativa da operação não será útil, ele pode rejeitar a mensagem, que será movida para a fila de mensagens mortas chamando [DeadLetter](/dotnet/api/microsoft.servicebus.messaging.queueclient.deadletter) e também permite definir uma propriedade personalizada, incluindo um código de motivo que pode ser recuperado com a mensagem da fila de mensagens mortas.

Um caso especial de liquidação é o adiamento, que é abordado em outro artigo.

As operações **Complete** ou **DeadLetter**, bem como a operação **RenewLock** podem falhar devido a problemas de rede se o bloqueio mantido expirou ou em caso de outras condições no lado do serviço que impeçam a liquidação. Em um destes casos, o serviço envia uma confirmação negativa que aparece como uma exceção nos clientes da API. Se o motivo for uma conexão de rede interrompida, o bloqueio será descartado porque o Barramento de Serviço não dá suporte à recuperação de links AMQP existentes em uma conexão diferente.

Se **Complete** falhar, o que ocorre normalmente no final do tratamento das mensagens e, em alguns casos, após alguns minutos de processamento, o aplicativo destinatário poderá decidir se preserva o estado do trabalho e ignora a mesma mensagem quando ela é entregue pela segunda vez, ou se ele descarta o resultado do trabalho e tenta novamente quando a mensagem é entregue da segunda vez.

O mecanismo típico para identificar as entregas de mensagem duplicada é verificando a identificação da mensagem, que pode e deve ser definida pelo remetente como um valor exclusivo, possivelmente alinhado com um identificador do processo de origem. Um agendador provavelmente definiria a ID de mensagem do identificador da tarefa que ele está tentando atribuir a um trabalho, e o trabalho ignoraria a segunda ocorrência da atribuição de tarefa se ela já estiver concluída.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as mensagens do Barramento de Serviço, consulte os seguintes tópicos:

* [Conceitos fundamentais do barramento de serviço](service-bus-fundamentals-hybrid-solutions.md)
* [Filas, tópicos e assinaturas do Barramento de Serviço](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
* [Como usar tópicos e assinaturas do Barramento de Serviço](service-bus-dotnet-how-to-use-topics-subscriptions.md)