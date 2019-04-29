---
title: Transações e modos de bloqueio em Coleções Confiáveis do Azure Service Fabric | Microsoft Docs
description: Gerenciador de estado confiável do Azure Service Fabric e Bloqueio e Transações de Coleções Confiáveis.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: masnider,rajak
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: aljo
ms.openlocfilehash: 9785a09a3ac3e119507b4ac28075d887c7edc619
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60774056"
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Transações e modos de bloqueio em Coleções Confiáveis do Azure Service Fabric

## <a name="transaction"></a>Transação
Uma transação é uma sequência de operações realizadas como uma única unidade lógica de trabalho.
Uma transação deve exibir as propriedades ACID a seguir. Confira: https://technet.microsoft.com/library/ms190612)
* **Atomicidade**: Uma transação deve ser uma unidade atômica de trabalho. Em outras palavras, todas as suas modificações de dados são realizadas ou nenhuma delas é realizada.
* **Consistência**: Quando concluída, uma transação deve deixar todos os dados em um estado consistente. Todas as estruturas de dados internos devem estar corretas ao final da transação.
* **Isolamento**: As modificações feitas por transações simultâneas devem ser isoladas das modificações feitas por quaisquer outras transações simultâneas. O nível de isolamento usado para uma operação em um ITransaction é determinado pelo IReliableState que executa a operação.
* **Durabilidade**: Após uma transação ser concluída, seus efeitos ficam permanentemente vigentes no sistema. As modificações persistem até mesmo no caso de uma falha do sistema.

### <a name="isolation-levels"></a>Níveis de isolamento
Nível de isolamento define o grau no qual a transação deve ser isolada de modificações feitas por outras transações.
Há dois níveis de isolamento com suporte nas Coleções Confiáveis:

* **Leitura repetida**: Especifica que as instruções não podem ler dados que foram modificados, mas ainda não foram confirmados por outras transações e que nenhuma outra transação pode modificar dados que foram lidos pela transação atual até que a transação atual seja concluída. Para obter mais informações, consulte [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).
* **Instantâneo**: Especifica que os dados lidos por qualquer instrução em uma transação são a versão transacionalmente consistente dos dados que existiam no início da transação.
  A transação pode reconhecer apenas modificações de dados que foram confirmadas antes do início da transação.
  Modificações de dados feitas por outras transações após o início da transação atual não são visíveis para instruções em execução na transação atual.
  O efeito é como se as instruções em uma transação obtivessem um instantâneo dos dados confirmados conforme existiam no início da transação.
  Os instantâneos são consistentes entre as Coleções Confiáveis.
  Para obter mais informações, consulte [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).

As Coleções Confiáveis escolhem automaticamente o nível de isolamento a ser usado para uma determinada operação de leitura dependendo da operação e da função da réplica no momento da criação da transação.
A seguir está a tabela que descreve os padrões de nível de isolamento para operações de Dicionário Confiável e Fila.

| Operação\função | Primário | Secundário |
| --- |:--- |:--- |
| Leitura de entidade única |Leitura repetida |Instantâneo |
| Enumeração, Contagem |Instantâneo |Instantâneo |

> [!NOTE]
> Exemplos comuns de Operações de Entidade Única são `IReliableDictionary.TryGetValueAsync`, `IReliableQueue.TryPeekAsync`.
> 

O Dicionário Confiável e a Fila Confiável dão suporte Read Your Writes.
Em outras palavras, qualquer gravação em uma transação será visível para uma leitura seguinte que pertence à mesma transação.

## <a name="locks"></a>Bloqueios
Nas Coleções Confiáveis, todas as transações implementam um bloqueio de duas fases rigoroso: uma transação não libera os bloqueios que adquiriu até que a transação seja encerrada com uma confirmação ou anulação.

Dicionário Confiável usa bloqueio para todas as operações de entidade única em nível de linha.
Fila Confiável compensa simultaneidade para propriedade PEPS transacional estrita.
Fila Confiável usa bloqueios de nível de operação permitindo que uma transação com `TryPeekAsync` e/ou `TryDequeueAsync` e uma transação com `EnqueueAsync` por vez.
Observe que, para preservar PEPS, se um `TryPeekAsync` ou `TryDequeueAsync` nunca observarem que a Fila Confiável está vazia, também bloquearão `EnqueueAsync`.

Operações de gravação sempre utilizam bloqueios exclusivos.
Para operações de leitura, o bloqueio depende de alguns fatores.
Qualquer operação de leitura feita usando o isolamento de Instantâneo é livre de bloqueio.
Qualquer operação de Leitura Repetida por padrão recebe bloqueios compartilhados.
No entanto, para qualquer operação de leitura que dá suporte à Leitura Repetida, o usuário pode solicitar um bloqueio de atualização, em vez do bloqueio compartilhado.
Um Bloqueio de atualização é um bloqueio assimétrico usado para evitar uma forma comum de deadlock que ocorre quando várias transações bloqueiam recursos para atualização potencial em um momento posterior.

A matriz de compatibilidade de bloqueio pode ser encontrada na tabela a seguir:

| Solicitação\concedida | Nenhum | Compartilhado | Atualizar | Exclusivo |
| --- |:--- |:--- |:--- |:--- |
| Compartilhado |Sem conflito |Sem conflito |Conflito |Conflito |
| Atualizar |Sem conflito |Sem conflito |Conflito |Conflito |
| Exclusivo |Sem conflito |Conflito |Conflito |Conflito |

O argumento de tempo limite nas APIs de Coleções Confiáveis é usado como uma detecção de deadlock.
Por exemplo, duas transações (T1 e T2) estão tentando ler e atualizar K1.
É possível que ocorra um deadlock, porque ambas acabam tendo o bloqueio compartilhado.
Nesse caso, uma ou ambas as operações atingirão o tempo limite.

Esse cenário de deadlock é um ótimo exemplo de como um Bloqueio de atualização pode evitar deadlocks.

## <a name="next-steps"></a>Próximas etapas
* [Trabalhando com Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Notificações do Reliable Services](service-fabric-reliable-services-notifications.md)
* [Backup e restauração do Reliable Services (recuperação de desastre)](service-fabric-reliable-services-backup-restore.md)
* [Configuração do Gerenciador de Estado Confiável](service-fabric-reliable-services-configuration.md)
* [Referência do desenvolvedor para Coleções Confiáveis](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

