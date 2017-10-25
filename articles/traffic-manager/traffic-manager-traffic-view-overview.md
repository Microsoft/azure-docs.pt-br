---
title: "Exibição do Tráfego no Gerenciador de Tráfego do Azure | Microsoft Docs"
description: "Introdução à Exibição do Tráfego do Gerenciador de Tráfego"
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/19/2017
ms.author: kumud
ms.custom: 
ms.openlocfilehash: f1ab98ab173edc794bfabdf55d950ac689a37c0a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="traffic-manager-traffic-view"></a>Exibição do Tráfego do Gerenciador de Tráfego

>[!NOTE]
>O recurso de Exibição do Tráfego no Gerenciador de Tráfego está na visualização pública e pode não ter o mesmo nível de disponibilidade e confiabilidade do que os recursos que estão na versão já disponível. Não há suporte para o recurso, o recurso pode ter funcionalidades restritas e ele pode não estar disponível em todas as localizações do Azure. Para receber as notificações mais recentes sobre a disponibilidade e o status desse recurso, confira a página [Atualizações do Gerenciador de Tráfego do Azure](https://azure.microsoft.com/updates/?product=traffic-manager).

O Gerenciador de Tráfego fornece roteamento no nível do DNS para que os usuários finais sejam direcionados ao pontos de extremidade íntegros, com base no método de roteamento que foi configurado quando o perfil foi criado. Isso fornece ao Gerenciador de Tráfego uma exibição de suas bases de usuários (no nível de granularidade do resolvedor de DNS) e o padrão de tráfego. Quando você habilita a Exibição do Tráfego, essas informações são processadas para fornecer ideias acionáveis. 

Usando a Exibição do Tráfego, você pode:
- entender onde suas bases de usuários estão localizadas (granularidade até o nível de um resolvedor de DNS local).
- entender o volume de tráfego originado dessas regiões.
- entender qual é a latência representativa experimentada por esses usuários.
- mergulhar profundamente nos padrões de tráfego específicos de cada uma dessas bases de usuários para as regiões do Azure em que você tem pontos de extremidade. 

Por exemplo, você pode usar a Exibição do Tráfego para entender quais regiões têm um grande número de tráfego, mas sofrem com latências mais altas. Em seguida, você pode usar essas informações para planejar a expansão da superfície para novas regiões do Azure para que esses usuários tenham uma experiência de latência mais baixa.

## <a name="how-traffic-view-works"></a>Como a Exibição do Tráfego funciona

A Exibição do Tráfego funciona com o Gerenciador de Tráfego examinando as consultas de entrada recebidas nos últimos sete dias em relação a um perfil com esse recurso habilitado. Dessas informações, ele extrai o IP de origem do resolvedor de DNS, que é usado como uma representação da localização dos usuários. Em seguida, eles são agrupados em uma granularidade no nível do resolvedor de DNS para criar regiões de base de usuários usando as informações geográficas dos endereços IP mantidos pelo Gerenciador de Tráfego. Em seguida, o Gerenciador de Tráfego analisa as regiões do Azure para as quais a consulta foi roteada e constrói um mapa de fluxo de tráfego para os usuários dessas regiões.
Na próxima etapa, o Gerenciador de Tráfego correlaciona o mapeamento da região da base de usuários à região do Azure com as tabelas de latência de inteligência de rede que ele mantém para diferentes redes de usuários finais a fim de entender a latência média experimentada pelos usuários dessas regiões durante a conexão com as regiões do Azure. Em seguida, todos esses cálculos são tabulados no nível do IP do resolvedor de DNS local antes de serem apresentados. Você pode processar essas informações em um fluxo de trabalho de análise de sua escolha e também pode baixar essas informações como um arquivo CSV.
Ao usar a Exibição do Tráfego, você é cobrado com base no número de pontos de dados usados para criar as informações apresentadas. Atualmente, o único tipo de ponto de dados usado são as consultas recebidas em relação ao seu perfil do Gerenciador de Tráfego. Para obter mais detalhes sobre os preços, visite o [página de preços do Gerenciador de Tráfego](https://azure.microsoft.com/pricing/details/traffic-manager/).


## <a name="next-steps"></a>Próximas etapas

- Saiba [como funciona o Gerenciador de Tráfego](traffic-manager-overview.md)
- Saiba mais sobre os [métodos de roteamento do tráfego](traffic-manager-routing-methods.md) com suporte pelo Gerenciador de Tráfego
- Aprenda a [criar um perfil do Gerenciador de Tráfego](traffic-manager-create-profile.md)

