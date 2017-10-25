---
title: "Gerenciador de estado confiável do Azure Service Fabric e elementos internos de Coleções Confiáveis | Microsoft Docs"
description: "Aprofunde-se nos conceitos e design de coleções confiáveis no Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: rajak
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: mcoskun
ms.openlocfilehash: d607449a16e886337ab1bd96213fbb4231124353
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-service-fabric-reliable-state-manager-and-reliable-collection-internals"></a>Gerenciador de Estado Confiável do Azure Service Fabric e elementos internos de Coleções Confiáveis
Este documento se aprofunda no Gerenciador de Estado Confiável e Coleções Confiáveis para ver como os componentes principais funcionam nos bastidores.

> [!NOTE]
> Este documento é um trabalho em andamento. Adicione comentários a este artigo e diga-nos sobre qual tópico você gostaria de saber mais.
>

##  <a name="local-persistence-model-log-and-checkpoint"></a>Modelo de persistência local: ponto de verificação e de log
O Gerenciador de Estado Confiável e as Coleções Confiáveis seguem um modelo de persistência chamado Log e Ponto de Verificação.
Nesse modelo, cada alteração de estado é registrada primeiro no disco e, em seguida, aplicada na memória.
O estado de conclusão em si é persistido apenas ocasionalmente (também conhecido como ponto de verificação).
O benefício é que deltas são transformados em gravações sequenciais somente de acréscimo em disco para melhorar o desempenho.

Para entender melhor o modelo de Log e Ponto de verificação, primeiro vamos analisar o cenário de disco infinito.
O Gerenciador de Estado Confiável registra cada operação antes que ela seja replicada.
O registro em log permite que as Coleções Confiáveis apliquem a operação somente na memória.
Já que os logs são persistidos, mesmo quando a réplica falha e precisa ser reiniciada, o Gerenciador de Estado Confiável tem informações suficientes em seu log para repetir todas as operações que a réplica perdeu.
Como o disco é infinito, registros de log nunca precisam ser removidos e a Coleção Confiável precisa apenas gerenciar o estado na memória.

Agora vamos examinar o cenário de disco finito.
Os registros do log se acumulam, o Gerenciador de Estado Confiável ficará sem espaço em disco.
Antes que isso ocorra, o Gerenciador de Estado Confiável precisa truncar seu log para liberar espaço para os registros mais recentes.
O Gerenciador de Estado Confiável solicita às Coleções Confiáveis que criem, em disco, um ponto de verificação do seu estado na memória.
Neste ponto, as Coleções Confiáveis persistiririam seu estado na memória.
Depois que as Coleções Confiáveis concluírem seus pontos de verificação, o Gerenciador de Estado Confiável poderá truncar o log para liberar espaço em disco.
Quando a réplica precisa ser reiniciada, as Coleções Confiáveis recuperam seu estado com ponto de verificação e o Gerenciador de Estado Confiável recupera e reproduz todas as alterações de estado que ocorreram desde o último ponto de verificação.

A adição de outro valor de ponto de verificação aprimora os tempos de recuperação em cenários comuns. O log contém todas as operações que ocorreram desde o último ponto de verificação.
Portanto, ele pode incluir várias versões de um item, tal como vários valores para uma determinada linha no Dicionário Confiável.
Em contraste, uma Coleção Confiável grava em pontos de verificação somente a versão mais recente de cada valor de uma chave.

## <a name="next-steps"></a>Próximas etapas
* [Transações e Bloqueios](service-fabric-reliable-services-reliable-collections-transactions-locks.md)

