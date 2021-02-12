---
title: Exibição de Tráfego no Gerenciador de Tráfego do Microsoft Azure
description: Nesta introdução, saiba como funciona a exibição de tráfego do Gerenciador de tráfego.
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 01/22/2021
ms.author: duau
ms.custom: ''
ms.openlocfilehash: 66376655c61903761d93ea228c6d72fa05734353
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743042"
---
# <a name="traffic-manager-traffic-view"></a>Exibição do Tráfego do Gerenciador de Tráfego

O Gerenciador de tráfego fornece roteamento de nível de DNS (sistema de nomes de domínio). Esse serviço permite que os usuários finais sejam direcionados a pontos de extremidade íntegros com base no método de roteamento de sua escolha. A Exibição de Tráfego fornece ao Gerenciador de Tráfego uma exibição das bases de usuários (em um nível de granularidade do resolvedor de DNS) e seu padrão de tráfego. Quando você habilita a Exibição do Tráfego, essas informações são processadas para fornecer ideias acionáveis. 

Usando a Exibição do Tráfego, você pode:
- entender onde suas bases de usuários estão localizadas (granularidade até o nível de um resolvedor de DNS local).
- exiba o volume de tráfego (observado como consultas DNS manipuladas pelo Gerenciador de Tráfego do Azure) proveniente dessas regiões.
- obtenha informações para entender qual é a latência representativa experimentada por esses usuários.
- mergulhar profundamente nos padrões de tráfego específicos de cada uma dessas bases de usuários para as regiões do Azure em que você tem pontos de extremidade. 

Por exemplo, você pode usar Exibição de Tráfego para entender quais regiões têm uma grande quantidade de tráfego, mas sofrem latências mais altas. Em seguida, use essas informações para planejar sua expansão de superfície para novas regiões do Azure. Dessa forma, os usuários terão uma experiência de latência mais baixa.

## <a name="how-traffic-view-works"></a>Como a Exibição do Tráfego funciona

Exibição de Tráfego funciona examina as consultas recebidas nos últimos sete dias para um perfil. Nas informações de consultas de entrada, Exibição de Tráfego extrai o IP de origem do resolvedor de DNS usado para representar o local dos usuários. Essas informações são agrupadas em um nível de resolvedor de DNS para criar regiões de base de usuário. O Traffic Manager mantém as informações geográficas dos endereços IP. O Gerenciador de tráfego examina as regiões do Azure para as quais a consulta é roteada e constrói um mapa de fluxo de tráfego para os usuários dessas regiões.
 
Na próxima etapa, o Gerenciador de tráfego correlaciona a região base de usuário ao mapeamento de região do Azure com as tabelas de latência de inteligência de rede. Essa tabela é mantida para diferentes redes de usuário final para entender a latência média realizada pelos usuários dessas regiões ao se conectarem às regiões do Azure. Todos esses cálculos são combinados em um nível de IP do resolvedor DNS por local antes de sua apresentação. Você pode consumir as informações de várias maneiras.

A frequência da atualização de dados da exibição de tráfego depende de várias variáveis de serviço internas. No entanto, os dados são atualizados uma vez a cada 24 horas.

>[!NOTE]
>A latência descrita na Exibição de Tráfego é uma latência representativa entre o usuário final e as regiões do Azure às quais ele se conectou e não é a latência de pesquisa de DNS. A Exibição de Tráfego faz a melhor estimativa possível da latência entre o resolvedor de DNS local e a região do Azure para a qual a consulta foi roteada. Se houver dados insuficientes disponíveis, a latência retornada será nula. 

## <a name="visual-overview"></a>Visão geral do visual

Ao navegar até a seção **exibição de tráfego** na página do Gerenciador de tráfego, você verá um mapa geográfico com uma sobreposição de exibição de tráfego insights. O mapa fornece informações sobre a base de usuários e os pontos de extremidade para seu perfil do Gerenciador de Tráfego.

![Exibição geográfica de Exibição de Tráfego do Traffic Manager][1]

### <a name="user-base-information"></a>Informações da base de usuários

Para os resolvedores DNS locais para os quais as informações de localização estão disponíveis, elas são mostradas no mapa. A cor do resolvedor de DNS indica a latência média experimentada pelos usuários finais que usaram o resolvedor de DNS para suas consultas do Gerenciador de Tráfego.

Se você focalizar uma localização do resolvedor de DNS no mapa, ele mostrará:
- o endereço IP do resolvedor de DNS
- o volume de tráfego da consulta DNS visto pelo Gerenciador de Tráfego desse local
- os pontos de extremidade para os quais o tráfego do resolvedor de DNS foi encaminhado, como uma linha entre o ponto de extremidade e o resolvedor de DNS 
- a latência média desse local até o ponto de extremidade, representada como a cor da linha de conexão

### <a name="endpoint-information"></a>Informações do ponto de extremidade

As regiões do Azure nas quais os pontos de extremidade estão localizados são mostradas como pontos azuis no mapa. Se o ponto de extremidade for externo e não tiver um mapa de região do Azure para ele, ele será mostrado na parte superior do mapa. Selecione qualquer ponto de extremidade para ver os diferentes locais (com base no resolvedor de DNS usado) de onde o tráfego foi direcionado para esse ponto de extremidade. As conexões são mostradas como uma linha entre o ponto de extremidade e o local do resolvedor de DNS. Eles são coloridos de acordo com a latência representativa entre esse par. Você pode ver o nome do ponto de extremidade e a região do Azure em que ele é executado. O volume total de solicitações que são direcionadas a ele por esse perfil do Gerenciador de tráfego também é exibido.


## <a name="tabular-listing-and-raw-data-download"></a>Download de listagem de tabela e de dados brutos

Exiba os dados da Exibição de Tráfego em um formato de tabela no portal do Azure. Há uma entrada para cada par de IP/ponto de extremidade do resolvedor de DNS que mostra:

* O endereço IP do resolvedor de DNS.
* O nome.
* A localização geográfica da região do Azure na qual o ponto de extremidade está localizado (se disponível).
* O volume de solicitações associadas a esse resolvedor DNS para esse ponto de extremidade.
* A latência representativa associada aos usuários finais que usam esse DNS (quando disponível). 

Também baixe os dados da Exibição de Tráfego como um arquivo CSV que pode ser usado como parte de um fluxo de trabalho de análise de sua escolha.

## <a name="billing"></a>Cobrança

Ao usar Exibição de Tráfego, você será cobrado com base no número de pontos de dados usados para criar as informações apresentadas. Atualmente, o único tipo de ponto de dados usado são as consultas recebidas em seu perfil do Gerenciador de Tráfego. Para obter mais detalhes sobre os preços, visite o [página de preços do Gerenciador de Tráfego](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="faqs"></a>Perguntas Frequentes

* [O que faz a Exibição do Tráfego faz?](./traffic-manager-faqs.md#what-does-traffic-view-do)

* [Como posso me beneficiar do uso da Exibição do Tráfego?](./traffic-manager-faqs.md#how-can-i-benefit-from-using-traffic-view)

* [Por que a Exibição do Tráfego é diferente das métricas do Gerenciador de Tráfego disponíveis por meio do Azure Monitor?](./traffic-manager-faqs.md#how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor)

* [A Exibição do Tráfego usa informações de sub-rede de cliente EDNS?](./traffic-manager-faqs.md#does-traffic-view-use-edns-client-subnet-information)

* [Quantos dias de dados a Exibição do Tráfego usa?](./traffic-manager-faqs.md#how-many-days-of-data-does-traffic-view-use)

* [Como a Exibição do Tráfego lida com pontos de extremidade externos?](./traffic-manager-faqs.md#how-does-traffic-view-handle-external-endpoints)

* [É necessário habilitar a Exibição do Tráfego para cada perfil na minha assinatura?](./traffic-manager-faqs.md#do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription)

* [Como posso desligar a Exibição do Tráfego?](./traffic-manager-faqs.md#how-can-i-turn-off-traffic-view)

* [Como funciona a cobrança da Exibição do Tráfego?](./traffic-manager-faqs.md#how-does-traffic-view-billing-work)

## <a name="next-steps"></a>Próximas etapas

- Saiba [como funciona o Gerenciador de Tráfego](traffic-manager-overview.md)
- Saiba mais sobre os [métodos de roteamento do tráfego](traffic-manager-routing-methods.md) com suporte pelo Gerenciador de Tráfego
- Saiba como [criar um perfil do Gerenciador de tráfego](./quickstart-create-traffic-manager-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-traffic-view-overview/trafficview.png