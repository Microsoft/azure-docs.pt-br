---
title: Introdução às Coleções Confiáveis nos serviços com estado do Azure Service Fabric | Microsoft Docs
description: Os serviços com monitoração de estado do Service Fabric fornecem coleções confiáveis que permitem escrever aplicativos em nuvem altamente disponíveis, escalonáveis e com baixa latência.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: masnider,rajak,zhol
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 1/3/2019
ms.author: aljo
ms.openlocfilehash: 4ed76b207db4712058b5524cd1b31fd65b0e19a4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60773615"
---
# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Introdução à Reliable Collections nos serviços com monitoração de estado do Service Fabric do Azure

As Coleções Confiáveis permitem desenvolver aplicativos em nuvem altamente disponíveis, escalonáveis e de baixa latência como se você estivesse desenvolvendo aplicativos de computador único. As classes no namespace **Microsoft.ServiceFabric.Data.Collections** fornecem um conjunto de coleções que automaticamente tornam seu estado altamente disponível. Os desenvolvedores só precisam programar para as APIs de Coleções Confiáveis e permitir que as Coleções Confiáveis gerenciem o estado local e replicado.

A principal diferença entre Coleções Confiáveis e outras tecnologias de alta disponibilidade (como Redis, serviço Tabela do Azure e serviço Fila do Azure) é que o estado é mantido localmente na instância de serviço, além de se tornar altamente disponível. Isso significa que:

* Todas as leituras são locais, o que resulta em leituras de baixa latência e alta taxa de transferência.
* Todas as gravações geram o mínimo de operações de E/S de rede, o que resulta em gravações de baixa latência e alta taxa.

![Imagem da evolução das coleções.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

As Coleções Confiáveis podem ser interpretadas como a evolução natural das classes **System.Collections** : um novo conjunto de coleções projetadas para a nuvem e aplicativos com vários computadores sem aumentar a complexidade para desenvolvedores. Sendo assim, as Coleções confiáveis são:

* Replicadas: Alterações de estado são replicadas para alta disponibilidade.
* Persistentes: Os dados são persistidos no disco para durabilidade contra interrupções em larga escala (por exemplo, uma interrupção de energia de datacenter).
* Como as gravações são persistidas e replicadas, você não pode criar um ReliableDictionary volátil, um ReliableQueue ou outra coleção confiável que somente persista dados na memória.
* Assíncronas: As APIs são assíncronas para garantir que os threads não sejam bloqueados quando gerarem E/S.
* Transacionais: As APIs utilizam a abstração de transações para que você possa gerenciar várias Coleções Confiáveis em um serviço com facilidade.

As Coleções Confiáveis fornecem garantias de coerência forte prontas para uso para facilitar o raciocínio sobre o estado do aplicativo.
A coerência forte é obtida com a garantia de que as confirmações de transação só são concluídas depois que toda a transação tiver sido registrada em uma quorum de réplicas que seja a maioria, incluindo a primária.
Para obter consistência mais fraca, os aplicativos podem confirmar para o cliente/solicitante antes de a confirmação assíncrona retornar.

As APIs de Coleções Confiáveis são uma evolução das APIs de coleções simultâneas (encontradas no namespace **System.Collections.Concurrent** ):

* Assíncronas: Retorna uma tarefa, pois, ao contrário das coleções simultâneas, as operações são replicadas e persistentes.
* Sem parâmetros externos: usa `ConditionalValue<T>` para retornar um `bool` e um valor, em vez de parâmetros externos. `ConditionalValue<T>` é como `Nullable<T>`, mas não requer T para ser um struct.
* Transações: Usam um objeto de transação para permitir que o usuário agrupe as ações em várias Coleções Confiáveis em uma transação.

Hoje, **Microsoft.ServiceFabric.Data.Collections** contém três coleções:

* [Dicionário Confiável](https://msdn.microsoft.com/library/azure/dn971511.aspx): Representa uma coleção de pares chave/valor replicada, transacional e assíncrona. Semelhante a **ConcurrentDictionary**, a chave e o valor podem ser de qualquer tipo.
* [Fila Confiável](https://msdn.microsoft.com/library/azure/dn971527.aspx): Representa uma fila PEPS (primeiro a entrar, primeiro a sair) estrita, assíncrona, transacional e replicada. Semelhante a **ConcurrentQueue**, a chave pode ser de qualquer tipo.
* [Fila Simultânea Confiável](service-fabric-reliable-services-reliable-concurrent-queue.md): Representa uma fila de ordenação de melhor esforço replicada, transacional e assíncrona para alta taxa de transferência. Semelhante à **ConcurrentQueue**, o valor pode ser de qualquer tipo.

## <a name="next-steps"></a>Próximas etapas

* [Recomendações e Diretrizes de Coleções Confiáveis](service-fabric-reliable-services-reliable-collections-guidelines.md)
* [Trabalhando com Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transações e bloqueios](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Gerenciando dados
  * [Backup e restauração](service-fabric-reliable-services-backup-restore.md)
  * [Notificações](service-fabric-reliable-services-notifications.md)
  * [Serialização de coleção confiável](service-fabric-reliable-services-reliable-collections-serialization.md)
  * [Serialização e atualização](service-fabric-application-upgrade-data-serialization.md)
  * [Configuração do Gerenciador de Estado Confiável](service-fabric-reliable-services-configuration.md)
* Outros
  * [Início Rápido dos Serviços Confiáveis](service-fabric-reliable-services-quick-start.md)
  * [Referência do desenvolvedor para Coleções Confiáveis](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
