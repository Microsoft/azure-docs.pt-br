---
title: Arquitetura de roteamento de porta frontal do Azure | Microsoft Docs
description: Este artigo ajuda você a entender o aspecto de exibição global da arquitetura do Front Door.
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 948cf3c65dfdc912f2f807dfac34076985f1bc89
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91449190"
---
# <a name="routing-architecture-overview"></a>Visão geral da arquitetura de roteamento

Quando a porta frontal do Azure recebe suas solicitações de cliente, ele fará uma das duas coisas. Responde a eles se você habilitar o Caching ou encaminhá-los para o back-end do aplicativo apropriado como um proxy reverso.

## <a name="selecting-the-front-door-environment-for-traffic-routing-anycast"></a><a name = "anycast"></a>Selecionando o ambiente do Front Door para roteamento de tráfego (Anycast)

O tráfego roteado para os ambientes de porta frontal do Azure usa a [anycast](https://en.wikipedia.org/wiki/Anycast) para o tráfego de DNS (sistema de nomes de domínio) e http (Hypertext Transfer Protocol), que permite que as solicitações do usuário atinjam o ambiente mais próximo na menor parte dos saltos de rede. Essa arquitetura oferece melhores tempos de ida e volta para os usuários finais, maximizando os benefícios do TCP dividido. O Front Door organiza seus ambientes em "anéis" primários e de fallback. O anel externo contém ambientes mais próximos aos usuários, oferecendo latências menores.  O anel interno tem ambientes que podem lidar com o failover para o ambiente de anel externo caso ocorram problemas. O anel externo é o destino preferencial para todo o tráfego e o anel interno é manipular o estouro de tráfego do anel externo. Cada host de front-end ou domínio servido pela porta de entrada recebe um VIP primário (endereços de protocolo de Internet virtual), que é anunciado por ambientes no anel interno e externo. Um VIP de fallback é anunciado apenas por ambientes no anel interno. 

Essa arquitetura garante que as solicitações de seus usuários finais sempre cheguem ao ambiente de porta frontal mais próximo. Mesmo que o ambiente de front door preferencial não esteja íntegro, todo o tráfego se moverá automaticamente para o próximo ambiente mais próximo.

## <a name="connecting-to-front-door-environment-split-tcp"></a><a name = "splittcp"></a>Conectando ao ambiente do Front Door (TCP de divisão)

[TCP de divisão](https://en.wikipedia.org/wiki/Performance-enhancing_proxy) é uma técnica para reduzir latências e problemas de TCP dividindo em partes menores uma conexão que poderia apresentar um tempo longo de viagem de ida e volta. Com os ambientes de porta frontal mais próximos dos usuários finais, as conexões TCP são encerradas dentro do ambiente de porta frontal. Uma conexão TCP que tem um RTT (tempo de ida e volta) grande para o back-end do aplicativo é dividida em duas conexões separadas. A "conexão curta" entre o usuário final e o ambiente de porta frontal significa que a conexão é estabelecida em três viagens curtas em vez de três idas longas, o que resulta em economia de latência. A "conexão longa" entre o ambiente de porta frontal e o back-end pode ser predefinida e, em seguida, reutilizada em outros usuários finais solicitações de salvamento de tempo de conectividade. O efeito da divisão TCP é multiplicado ao estabelecer uma conexão SSL/TLS (Transport Layer Security), pois há mais viagens de ida e volta para proteger uma conexão.

## <a name="processing-request-to-match-a-routing-rule"></a>Processando a solicitação para corresponder a uma regra de roteamento
Depois de estabelecer uma conexão e concluir um handshake de TLS, a primeira etapa depois que uma solicitação chega em um ambiente de porta de front-end é fazer a correspondência com a regra de roteamento. A correspondência é determinada pelas configurações na porta frontal à qual a regra de roteamento específica corresponde à solicitação. Leia sobre como o Front Door faz a [correspondência de rota](front-door-route-matching.md) para saber mais.

## <a name="identifying-available-backends-in-the-backend-pool-for-the-routing-rule"></a>Identificando back-ends disponíveis no pool de back-ends para a regra de roteamento
Depois que a porta da frente corresponder a uma regra de roteamento para uma solicitação de entrada, a próxima etapa será obter o status da investigação de integridade para o pool de back-end associado à regra de roteamento, se não houver cache. Leia sobre como o Front Door monitora a integridade do back-end usando [Investigações de Integridade](front-door-health-probes.md) para saber mais.

## <a name="forwarding-the-request-to-your-application-backend"></a>Encaminhando a solicitação ao back-end do aplicativo
Por fim, supondo que o Caching não esteja configurado, a solicitação do usuário é encaminhada para o "melhor" back-end com base na sua configuração de [método de roteamento](front-door-routing-methods.md) .

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
