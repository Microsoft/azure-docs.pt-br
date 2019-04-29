---
title: ReliableConcurrentQueue no Azure Service Fabric
description: ReliableConcurrentQueue é uma fila de alta taxa de transferência que permite enfileirar e remover da fila de modo paralelo.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: raja,tyadam,masnider,vturecek
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: aljo
ms.openlocfilehash: dbdfa4686c047fa7cf5d74cd9aca768447f9db93
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60774005"
---
# <a name="introduction-to-reliableconcurrentqueue-in-azure-service-fabric"></a>Introdução a ReliableConcurrentQueue no Azure Service Fabric
Fila Simultânea Confiável é uma fila assíncrona, transacional e replicada quais apresenta alta simultaneidade para operações de enfileirar e remover da fila. Ele é projetado para oferecer alta taxa de transferência e baixa latência flexibilizando a rígida ordenação de PEPS fornecida pela [Fila Confiável](https://msdn.microsoft.com/library/azure/dn971527.aspx) e, em vez disso, fornece uma ordenação de melhor esforço.

## <a name="apis"></a>APIs

|Fila Simultânea                |Fila Simultânea Confiável                                         |
|--------------------------------|------------------------------------------------------------------|
| void Enqueue(T item)           | Task EnqueueAsync(ITransaction tx, T item)                       |
| bool TryDequeue(out T result)  | Task< ConditionalValue < T > > TryDequeueAsync(ITransaction tx)  |
| int Count()                    | long Count()                                                     |

## <a name="comparison-with-reliable-queuehttpsmsdnmicrosoftcomlibraryazuredn971527aspx"></a>Comparação com [Fila Confiável](https://msdn.microsoft.com/library/azure/dn971527.aspx)

A Fila Simultâneas Confiável é oferecida como uma alternativa à [Fila Confiável](https://msdn.microsoft.com/library/azure/dn971527.aspx). Ela deve ser usada em casos em que ordenação PEPS estrita não seja necessária, como garantir que PEPS exija uma compensação com simultaneidade.  [Fila Confiável](https://msdn.microsoft.com/library/azure/dn971527.aspx) usa bloqueios para impor a ordenação PEPS, com no máximo uma transação com permissão para enfileirar e no máximo uma transação com permissão para remover da fila por vez. Em comparação, Fila Simultâneos Confiável flexibiliza a restrição de ordenação e permite que qualquer número de transações simultâneas intercale suas operações de enfileirar e remover da fila. Ordenação de melhor esforço é fornecido, porém, a ordenação relativa de dois valores em uma Fila Simultânea Confiável nunca pode ser garantida.

Fila Simultâneas Confiáveis fornecem maior taxa de transferência e menor latência que [Fila Confiável](https://msdn.microsoft.com/library/azure/dn971527.aspx) sempre que há várias transações simultâneas executando ações de enfileirar e/ou remover da fila.

Um exemplo de caso de uso para o ReliableConcurrentQueue é o cenário [Fila de Mensagens](https://en.wikipedia.org/wiki/Message_queue). Nesse cenário, um ou mais produtores de mensagem criam e adicionam itens à fila, e um ou mais consumidores de mensagens capturam mensagens da fila e as processam. Vários produtores e consumidores podem trabalhar de modo independente, usando transações simultâneas para processar a fila.

## <a name="usage-guidelines"></a>Diretrizes de uso
* A fila espera que os itens na fila tenham um baixo período de retenção. Ou seja, os itens não permanecem na fila por um longo período.
* A fila não assegura a ordenação PEPS estrita.
* A fila não lê suas próprias gravações. Se um item for enfileirado em uma transação, ele não será visível para uma operação de remover da fila na mesma transação.
* As operações de remover da fila não são isoladas umas das outras. Se o item *A* for removido da fila na transação *txnA*, embora *txnA* não esteja confirmado, o item *A* não ficará visível a uma transação simultânea *txnB*.  Se *txnA* for anulada, *A* ficará visível a *txnB* imediatamente.
* O comportamento *TryPeekAsync* pode ser implementado usando um *TryDequeueAsync* e, em seguida, anulando a transação. Um exemplo disso pode ser encontrado na seção de Padrões de Programação.
* A contagem é não transacional. Você pode usá-la para ter uma ideia do número de elementos na fila, mas representa um ponto no tempo e não é confiável.
* Processamento dispendioso nos itens de remoção da fila não deve ser executado enquanto a transação estiver ativa para evitar transações de execução longa que podem afetar o desempenho do sistema.

## <a name="code-snippets"></a>Snippets de código
Vamos analisar alguns snippets de código e suas saídas esperadas. O tratamento de exceção é ignorado nesta seção.

### <a name="enqueueasync"></a>EnqueueAsync
Aqui estão alguns snippets de código para usar EnqueueAsync seguidos por suas saídas esperadas.

- *Caso 1: Tarefa única de enfileiramento*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}
```

Suponha que a tarefa tenha sido concluída com êxito e que não tenham ocorrido transações simultâneas que modificassem a fila. O usuário pode esperar que a fila contenha os itens em qualquer uma destas ordens:

> 10, 20
> 
> 20, 10


- *Caso 2: Tarefa enfileiramento paralelo*

```
// Parallel Task 1
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}

// Parallel Task 2
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 30, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 40, cancellationToken);

    await txn.CommitAsync();
}
```

Suponha que as tarefas tenham sido concluídas com êxito, que as tarefas foram executadas em paralelo e que não tenham ocorrido outras transações simultâneas que modificassem a fila. Não é possível inferir a ordem dos itens na fila. Para esse snippet de código, os itens podem aparecer em qualquer uma das 4! ordenações possíveis.  A fila tentará manter os itens na ordem original (enfileirados), mas poderá ser forçada a reordená-los devido a falhas ou operações simultâneas.


### <a name="dequeueasync"></a>DequeueAsync
Aqui estão alguns snippets de código para usar TryDequeueAsync seguidos pelas suas saídas esperadas. Suponha que a fila já está preenchida com os seguintes itens na fila:
> 10, 20, 30, 40, 50, 60

- *Caso 1: Tarefa única de remoção da fila*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    await txn.CommitAsync();
}
```

Suponha que a tarefa tenha sido concluída com êxito e que não tenham ocorrido transações simultâneas que modificassem a fila. Uma vez que não se pode fazer nenhuma inferência sobre a ordem dos itens na fila, qualquer um dos três itens pode ser removido da fila em qualquer ordem. A fila tentará manter os itens na ordem original (enfileirados), mas poderá ser forçada a reordená-los devido a falhas ou operações simultâneas.  

- *Caso 2: Tarefa paralela de remoção da fila*

```
// Parallel Task 1
List<int> dequeue1;
using (var txn = this.StateManager.CreateTransaction())
{
    dequeue1.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;
    dequeue1.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;

    await txn.CommitAsync();
}

// Parallel Task 2
List<int> dequeue2;
using (var txn = this.StateManager.CreateTransaction())
{
    dequeue2.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;
    dequeue2.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;

    await txn.CommitAsync();
}
```

Suponha que as tarefas tenham sido concluídas com êxito, que as tarefas foram executadas em paralelo e que não tenham ocorrido outras transações simultâneas que modificassem a fila. Uma vez que não se pode fazer nenhuma inferência sobre a ordem dos itens na fila, as listas *dequeue1* e *dequeue2* conterão, cada uma, qualquer um dos dois itens em qualquer ordem.

O mesmo item *não* aparecerá em ambas as listas. Assim, dequeue1 tiver *10*, *30*, então dequeue2 terá *20*, *40*.

- *Caso 3: Ordenação de remoção da fila com anulação da transação*

Anular uma transação com remoções de fila em andamento coloca que os itens de volta no topo da fila. A ordem em que os itens são colocados de volta no topo da fila não é garantida. Vamos examine o código a seguir:

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    // Abort the transaction
    await txn.AbortAsync();
}
```
Suponha que os itens foram removidos da fila na seguinte ordem:
> 10, 20

Quando anulamos a transação, os itens são adicionados de volta ao topo da fila em qualquer uma das ordens a seguir:
> 10, 20
> 
> 20, 10

O mesmo é verdadeiro para todos os casos em que a transação não foi *Confirmada* com sucesso.

## <a name="programming-patterns"></a>Padrões de programação
Nesta seção, vamos analisar alguns padrões de programação que podem ser úteis no uso de ReliableConcurrentQueue.

### <a name="batch-dequeues"></a>Remoções de fila em lote
O padrão de programação recomendado é a tarefa de consumidor realizar remoções da fila em lote, em vez de executar uma remoção da fila por vez. O usuário pode optar por restringir atrasos entre cada lote ou o tamanho do lote. O snippet de código a seguir mostra esse modelo de programação.  Observe que, neste exemplo, o processamento é feito depois que a transação é confirmada, portanto, se ocorrer uma falha durante o processamento, os itens não processados serão perdidos sem terem sido processados.  Como alternativa, o processamento pode ser feito no escopo da transação, no entanto, isso pode ter um impacto negativo no desempenho e requer tratamento dos itens já processados.

```
int batchSize = 5;
long delayMs = 100;

while(!cancellationToken.IsCancellationRequested)
{
    // Buffer for dequeued items
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        ConditionalValue<int> ret;

        for(int i = 0; i < batchSize; ++i)
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if (ret.HasValue)
            {
                // If an item was dequeued, add to the buffer for processing
                processItems.Add(ret.Value);
            }
            else
            {
                // else break the for loop
                break;
            }
        }

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }

    int delayFactor = batchSize - processItems.Count;
    await Task.Delay(TimeSpan.FromMilliseconds(delayMs * delayFactor), cancellationToken);
}
```

### <a name="best-effort-notification-based-processing"></a>Processamento de melhor esforço baseado em notificação
Outro padrão de programação interessante usa a API de Contagem. Aqui, podemos implementar o processamento baseado em notificação de melhor esforço para a fila. A Contagem de fila pode ser usada para restringir uma tarefa de enfileiramento ou de remoção da fila.  Observe que, como no exemplo anterior, já que o processamento ocorre fora da transação, os itens não processados poderão ser perdidos se ocorrer uma falha durante o processamento.

```
int threshold = 5;
long delayMs = 1000;

while(!cancellationToken.IsCancellationRequested)
{
    while (this.Queue.Count < threshold)
    {
        cancellationToken.ThrowIfCancellationRequested();

        // If the queue does not have the threshold number of items, delay the task and check again
        await Task.Delay(TimeSpan.FromMilliseconds(delayMs), cancellationToken);
    }

    // If there are approximately threshold number of items, try and process the queue

    // Buffer for dequeued items
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        ConditionalValue<int> ret;

        do
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if (ret.HasValue)
            {
                // If an item was dequeued, add to the buffer for processing
                processItems.Add(ret.Value);
            }
        } while (processItems.Count < threshold && ret.HasValue);

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }
}
```

### <a name="best-effort-drain"></a>Drenagem de melhor esforço
Uma drenagem da fila não pode ser garantida devido à natureza simultânea da estrutura de dados.  É possível que, mesmo que nenhuma operação de usuário na fila esteja em andamento, uma chamada específica para TryDequeueAsync não retorne um item que foi anteriormente enfileirado e confirmado.  É garantido que o item enfileirado *acabará* ficando visível à remoção da fila, porém, sem um mecanismo de comunicação fora da banda, um consumidor independente não tem como saber que a fila atingiu um estado estável mesmo que todos os produtores tenham sido interrompidos e nenhuma nova operação de enfileiramento seja permitida. Portanto, a operação de drenagem é o melhor esforço conforme implementado abaixo.

O usuário deve interromper todas as outras tarefas de produtor e consumidor e esperar qualquer transação em andamento ser confirmada ou anulada antes de tentar drenar a fila.  Se o usuário souber qual é o número esperado de itens na fila, ele poderá configurar uma notificação que sinalize que todos os itens foram removidos da fila.

```
int numItemsDequeued;
int batchSize = 5;

ConditionalValue ret;

do
{
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        do
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if(ret.HasValue)
            {
                // Buffer the dequeues
                processItems.Add(ret.Value);
            }
        } while (ret.HasValue && processItems.Count < batchSize);

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }
} while (ret.HasValue);
```

### <a name="peek"></a>Espiar
ReliableConcurrentQueue não fornece a API *TryPeekAsync*. Os usuários podem obter a semântica da espiada usando um *TryDequeueAsync* e, em seguida, anulando a transação. Neste exemplo, remoções da fila serão processadas somente se o valor do item for maior do que *10*.

```
using (var txn = this.StateManager.CreateTransaction())
{
    ConditionalValue ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);
    bool valueProcessed = false;

    if (ret.HasValue)
    {
        if (ret.Value > 10)
        {
            // Process the item
            Console.WriteLine("Value : " + ret.Value);
            valueProcessed = true;
        }
    }

    if (valueProcessed)
    {
        await txn.CommitAsync();    
    }
    else
    {
        await txn.AbortAsync();
    }
}
```

## <a name="must-read"></a>Deve ler
* [Início rápido de Reliable Services](service-fabric-reliable-services-quick-start.md)
* [Trabalhando com Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Notificações do Reliable Services](service-fabric-reliable-services-notifications.md)
* [Backup e restauração de Reliable Services (recuperação de desastre)](service-fabric-reliable-services-backup-restore.md)
* [Configuração do Gerenciador de Estado Confiável](service-fabric-reliable-services-configuration.md)
* [Introdução aos serviços de API Web do Service Fabric](service-fabric-reliable-services-communication-webapi.md)
* [Uso avançado do modelo de programação de Reliable Services](service-fabric-reliable-services-advanced-usage.md)
* [Referência do desenvolvedor para Coleções Confiáveis](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
