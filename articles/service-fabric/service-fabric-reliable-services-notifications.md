---
title: Notificações de Reliable Services | Microsoft Docs
description: Documentação conceitual para notificações de Reliable Services do Service Fabric
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: chackdan
editor: masnider,vturecek
ms.assetid: cdc918dd-5e81-49c8-a03d-7ddcd12a9a76
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/29/2017
ms.author: mcoskun
ms.openlocfilehash: a3df5f28475b03f1799dc1e245c3a7e904b49cb3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60772917"
---
# <a name="reliable-services-notifications"></a>Notificações do Reliable Services
As notificações permitem que os clientes controle as alterações que estão sendo feitas em um objeto no qual eles estão interessados. Dois tipos de objetos dá suporte a notificações: *Gerenciador de estado confiável* e *dicionário confiável*.

Os motivos comuns para usar as Notificações são:

* Criação de modos de exibição materializados, como índices secundários, ou modos de exibição agregadas e filtradas do estado da réplica. Um exemplo é um índice classificado de todas as chaves em um Dicionário Confiável.
* Envio de dados de monitoramento, como o número de usuários adicionados na última hora.

As notificações são disparadas como parte da aplicação de operações. Por disso, as notificações devem ser manipuladas tão rápido quanto possível, e eventos síncronos não devem incluir operações dispendiosas.

## <a name="reliable-state-manager-notifications"></a>Notificações do Gerenciador de Estado Confiável
O Gerenciador de Estado Confiável fornece notificações para os eventos a seguir:

* Transação
  * Confirmar
* Gerenciador de estado
  * Recompilação
  * Adição de um estado confiável
  * Remoção de um estado confiável

O Gerenciador de Estado Confiável rastreia as transações em execução no momento. A única alteração no estado da transação que faz com que uma notificação seja acionada é uma confirmação de transação.

O Gerenciador de Estado Confiável mantém uma coleção de estados confiáveis, como Dicionário Confiável e Fila Confiável. O Gerenciador de Estado Confiável dispara notificações quando essa coleção muda: um estado confiável é adicionado ou removido ou toda a coleção é recompilada.
A coleção do Gerenciador de Estado Confiável é recompilada em três casos:

* Recuperação: Quando uma réplica é iniciado, ele recupera seu estado anterior do disco. Ao fim da recuperação, ela usa **NotifyStateManagerChangedEventArgs** para disparar um evento que contém o conjunto de estados confiáveis recuperados.
* Cópia completa: Antes que uma réplica possa ingressar no conjunto de configurações, ele deve ser criado. Às vezes, isso exige a aplicação de uma cópia completa do estado do Gerenciador de Estado Confiável da réplica primária para a réplica secundária ociosa. O Gerenciador de Estado Confiável na réplica secundária usa **NotifyStateManagerChangedEventArgs** para disparar um evento que contém o conjunto de estados confiáveis que adquiriu da réplica primária.
* Restaure: Em cenários de recuperação de desastre, o estado da réplica pode ser restaurado a partir de um backup por meio **RestoreAsync**. Nesses casos, o Gerenciador de Estado Confiável na réplica primária usa **NotifyStateManagerChangedEventArgs** para disparar um evento que contém o conjunto de estados confiáveis que ele restaurou do backup.

Para se registrar e receber notificações de transação e/ou notificações do gerenciador de estado, você precisa se registrar com eventos **TransactionChanged** ou **StateManagerChanged** no Gerenciador de Estado Confiável. Um lugar comum para registrar esses manipuladores de eventos é o construtor de seu serviço com estado. Quando você se registrar no construtor, não perderá notificações causadas por uma alteração durante a vida útil de **IReliableStateManager**.

```csharp
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

O manipulador de eventos **TransactionChanged** usa **NotifyTransactionChangedEventArgs** para fornecer detalhes sobre o evento. Contém a propriedade de ação (por exemplo, **NotifyTransactionChangedAction.Commit**) que especifica o tipo de alteração. Também contém a propriedade de transação que fornece uma referência para a transação que foi alterada.

> [!NOTE]
> Hoje, eventos **TransactionChanged** são gerados somente se a transação é confirmada. A ação é igual a **NotifyTransactionChangedAction.Commit**. Porém, no futuro, eventos poderão ser gerados para outros tipos de alterações de estado de transação. É recomendável verificar a ação e processar o evento somente se ele for o que você espera.
> 
> 

Veja a seguir um exemplo de manipulador de eventos **TransactionChanged** .

```csharp
private void OnTransactionChangedHandler(object sender, NotifyTransactionChangedEventArgs e)
{
    if (e.Action == NotifyTransactionChangedAction.Commit)
    {
        this.lastCommitLsn = e.Transaction.CommitSequenceNumber;
        this.lastTransactionId = e.Transaction.TransactionId;

        this.lastCommittedTransactionList.Add(e.Transaction.TransactionId);
    }
}
```

O manipulador de eventos **StateManagerChanged** usa **NotifyStateManagerChangedEventArgs** para fornecer detalhes sobre o evento.
**NotifyStateManagerChangedEventArgs** tem duas subclasses: **NotifyStateManagerRebuildEventArgs** e **NotifyStateManagerSingleEntityChangedEventArgs**.
Use a propriedade de ação no **NotifyStateManagerChangedEventArgs** para converter **NotifyStateManagerChangedEventArgs** na subclasse correta:

* **NotifyStateManagerChangedAction.Rebuild**: **NotifyStateManagerRebuildEventArgs**
* **Notifystatemanagerchangedaction. Add** e **notifystatemanagerchangedaction. Remove**: **NotifyStateManagerSingleEntityChangedEventArgs**

Veja a seguir um exemplo de manipulador de notificação **StateManagerChanged** .

```csharp
public void OnStateManagerChangedHandler(object sender, NotifyStateManagerChangedEventArgs e)
{
    if (e.Action == NotifyStateManagerChangedAction.Rebuild)
    {
        this.ProcessStateManagerRebuildNotification(e);

        return;
    }

    this.ProcessStateManagerSingleEntityNotification(e);
}
```

## <a name="reliable-dictionary-notifications"></a>Notificações de Dicionário Confiável
O Dicionário Confiável fornece notificações para os seguintes eventos:

* Recompilar: Chamado quando **ReliableDictionary** recuperou o estado de um backup ou recuperou ou copiou o estado local.
* Clara: Chamado quando o estado de **ReliableDictionary** foi limpo por meio de **ClearAsync** método.
* Adicionar: Chamado quando um item foi adicionado ao **ReliableDictionary**.
* Atualização: Chamado quando um item na **IReliableDictionary** foi atualizado.
* Remova: Chamado quando um item na **IReliableDictionary** foi excluído.

Para obter notificações de dicionário confiável, você precisa se registrar com o manipulador de eventos **DictionaryChanged** em **IReliableDictionary**. Um lugar comum para se registrar com esses manipuladores de eventos é na notificação de adição **ReliableStateManager.StateManagerChanged** .
O registro quando **IReliableDictionary** é adicionado a **IReliableStateManager** garante que você não perca notificações.

```csharp
private void ProcessStateManagerSingleEntityNotification(NotifyStateManagerChangedEventArgs e)
{
    var operation = e as NotifyStateManagerSingleEntityChangedEventArgs;

    if (operation.Action == NotifyStateManagerChangedAction.Add)
    {
        if (operation.ReliableState is IReliableDictionary<TKey, TValue>)
        {
            var dictionary = (IReliableDictionary<TKey, TValue>)operation.ReliableState;
            dictionary.RebuildNotificationAsyncCallback = this.OnDictionaryRebuildNotificationHandlerAsync;
            dictionary.DictionaryChanged += this.OnDictionaryChangedHandler;
        }
    }
}
```

> [!NOTE]
> **ProcessStateManagerSingleEntityNotification** é o método de exemplo que o exemplo de **OnStateManagerChangedHandler** anterior chama.
> 
> 

O conjunto de código anterior define a interface de **IReliableNotificationAsyncCallback**, juntamente com **DictionaryChanged**. Como **NotifyDictionaryRebuildEventArgs** contém uma interface **IAsyncEnumerable** que precisa ser enumerada de forma assíncrona, as notificações de recriação são disparadas por meio de **RebuildNotificationAsyncCallback** em vez de **OnDictionaryChangedHandler**.

```csharp
public async Task OnDictionaryRebuildNotificationHandlerAsync(
    IReliableDictionary<TKey, TValue> origin,
    NotifyDictionaryRebuildEventArgs<TKey, TValue> rebuildNotification)
{
    this.secondaryIndex.Clear();

    var enumerator = e.State.GetAsyncEnumerator();
    while (await enumerator.MoveNextAsync(CancellationToken.None))
    {
        this.secondaryIndex.Add(enumerator.Current.Key, enumerator.Current.Value);
    }
}
```

> [!NOTE]
> No código anterior, como parte do processamento da notificação de recriação, primeiro o estado agregado mantido é limpo. Como a coleção confiável está sendo recompilada com um novo estado, todas as notificações anteriores são irrelevantes.
> 
> 

O manipulador de eventos **DictionaryChanged** usa o **NotifyDictionaryChangedEventArgs** para fornecer detalhes sobre o evento.
**NotifyDictionaryChangedEventArgs** tem cinco subclasses. Use a propriedade de ação em **NotifyDictionaryChangedEventArgs** para converter **NotifyDictionaryChangedEventArgs** na subclasse correta:

* **NotifyDictionaryChangedAction.Rebuild**: **NotifyDictionaryRebuildEventArgs**
* **NotifyDictionaryChangedAction.Clear**: **NotifyDictionaryClearEventArgs**
* **Notifydictionarychangedaction. Add** e **NotifyDictionaryChangedAction.Remove**: **NotifyDictionaryItemAddedEventArgs**
* **NotifyDictionaryChangedAction.Update**: **NotifyDictionaryItemUpdatedEventArgs**
* **NotifyDictionaryChangedAction.Remove**: **NotifyDictionaryItemRemovedEventArgs**

```csharp
public void OnDictionaryChangedHandler(object sender, NotifyDictionaryChangedEventArgs<TKey, TValue> e)
{
    switch (e.Action)
    {
        case NotifyDictionaryChangedAction.Clear:
            var clearEvent = e as NotifyDictionaryClearEventArgs<TKey, TValue>;
            this.ProcessClearNotification(clearEvent);
            return;

        case NotifyDictionaryChangedAction.Add:
            var addEvent = e as NotifyDictionaryItemAddedEventArgs<TKey, TValue>;
            this.ProcessAddNotification(addEvent);
            return;

        case NotifyDictionaryChangedAction.Update:
            var updateEvent = e as NotifyDictionaryItemUpdatedEventArgs<TKey, TValue>;
            this.ProcessUpdateNotification(updateEvent);
            return;

        case NotifyDictionaryChangedAction.Remove:
            var deleteEvent = e as NotifyDictionaryItemRemovedEventArgs<TKey, TValue>;
            this.ProcessRemoveNotification(deleteEvent);
            return;

        default:
            break;
    }
}
```

## <a name="recommendations"></a>Recomendações
* *Conclua* os eventos de notificações o mais rápido possível.
* *Não* execute quaisquer operações dispendiosas (por exemplo, operações de E/S) como parte de eventos síncronos.
* *Verifique* o tipo de ação antes de processar o evento. Novos tipos de ação podem ser adicionados no futuro.

Eis aqui algumas coisas que se deve manter em mente:

* As notificações são disparadas como parte da execução de uma operação. Por exemplo, uma notificação de restauração é acionada como a última etapa de uma operação de restauração. Uma restauração não será concluída até que o evento de notificação seja processado.
* Como as notificações são disparadas como parte das operações de aplicação, os clientes veem apenas as notificações para operações confirmadas localmente. E como as operações têm garantia de serem confirmadas apenas localmente (em outras palavras, registradas em log), podem ou não pode ser desfeitas no futuro.
* No caminho de restauração, uma única notificação é disparada para cada operação aplicada. Isso significa que se a transação T1 incluir Create(X), Delete(X) e Create(X), você obterá uma notificação para a criação de X, uma para a exclusão e outra para a criação, nessa ordem.
* Para transações que contêm várias operações, as operações são aplicadas na ordem em que foram recebidas na réplica primária do usuário.
* Como parte do processamento do progresso falso, algumas operações podem ser desfeitas. As notificações são geradas para essas operações da ação de desfazer, revertendo o estado da réplica de volta a um ponto estável. Uma diferença importante das notificações de ação de desfazer é que os eventos que têm chaves duplicadas são agregados. Por exemplo, se a transação T1 estiver sendo desfeita, você verá uma notificação única para Delete(X).

## <a name="next-steps"></a>Próximas etapas
* [Coleções Confiáveis](service-fabric-work-with-reliable-collections.md)
* [Início Rápido dos Serviços Confiáveis](service-fabric-reliable-services-quick-start.md)
* [Backup e restauração do Reliable Services (recuperação de desastre)](service-fabric-reliable-services-backup-restore.md)
* [Referência do desenvolvedor para Coleções Confiáveis](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

