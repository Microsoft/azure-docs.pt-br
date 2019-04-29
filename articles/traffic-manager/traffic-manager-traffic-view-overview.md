---
title: Exibição de Tráfego no Gerenciador de Tráfego do Microsoft Azure
description: Introdução à Exibição do Tráfego do Gerenciador de Tráfego
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 70ac4319e2ea0081f7805c2fb936af1310d57d8f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771593"
---
# <a name="traffic-manager-traffic-view"></a>Exibição do Tráfego do Gerenciador de Tráfego

O Gerenciador de Tráfego fornece roteamento no nível do DNS para que os usuários finais sejam direcionados aos pontos de extremidade íntegros, com base no método de roteamento especificado quando o perfil foi criado. A Exibição de Tráfego fornece ao Gerenciador de Tráfego uma exibição das bases de usuários (em um nível de granularidade do resolvedor de DNS) e seu padrão de tráfego. Quando você habilita a Exibição do Tráfego, essas informações são processadas para fornecer ideias acionáveis. 

Usando a Exibição do Tráfego, você pode:
- entender onde suas bases de usuários estão localizadas (granularidade até o nível de um resolvedor de DNS local).
- exiba o volume de tráfego (observado como consultas DNS manipuladas pelo Gerenciador de Tráfego do Azure) proveniente dessas regiões.
- obtenha informações para entender qual é a latência representativa experimentada por esses usuários.
- mergulhar profundamente nos padrões de tráfego específicos de cada uma dessas bases de usuários para as regiões do Azure em que você tem pontos de extremidade. 

Por exemplo, você pode usar a Exibição do Tráfego para entender quais regiões têm um grande número de tráfego, mas sofrem com latências mais altas. Em seguida, use essas informações para planejar a expansão da superfície para novas regiões do Azure, de modo que esses usuários tenham uma experiência de latência mais baixa.

## <a name="how-traffic-view-works"></a>Como a Exibição do Tráfego funciona

A Exibição do Tráfego funciona com o Gerenciador de Tráfego examinando as consultas de entrada recebidas nos últimos sete dias em relação a um perfil com esse recurso habilitado. Com base nas informações das consultas de entrada, a Exibição de Tráfego extrai o IP de origem do resolvedor de DNS, que é usado como uma representação da localização dos usuários. Em seguida, eles são agrupados em uma granularidade no nível do resolvedor de DNS para criar regiões de base de usuários usando as informações geográficas dos endereços IP mantidos pelo Gerenciador de Tráfego. Em seguida, o Gerenciador de Tráfego analisa as regiões do Azure para as quais a consulta foi roteada e constrói um mapa de fluxo de tráfego para os usuários dessas regiões.  
Na próxima etapa, o Gerenciador de Tráfego correlaciona o mapeamento da região da base de usuários à região do Azure com as tabelas de latência de inteligência de rede que ele mantém para diferentes redes de usuários finais, a fim de entender a latência média experimentada pelos usuários nessas regiões durante a conexão com as regiões do Azure. Em seguida, todos esses cálculos são combinados no nível do IP do resolvedor de DNS local antes de serem apresentados. Você pode consumir as informações de várias maneiras.

A frequência de atualização de dados de exibição de tráfego depende de diversas variáveis de serviço interno. No entanto, os dados geralmente são atualizados uma vez a cada 24 horas.

>[!NOTE]
>A latência descrita na Exibição de Tráfego é uma latência representativa entre o usuário final e as regiões do Azure às quais ele se conectou e não é a latência de pesquisa de DNS. A Exibição de Tráfego faz a melhor estimativa possível da latência entre o resolvedor de DNS local e a região do Azure para a qual a consulta foi roteada. Se houver dados insuficientes disponíveis, a latência retornada será nula. 

## <a name="visual-overview"></a>Visão geral do visual

Ao navegar para a seção **Exibição de Tráfego** da página do Gerenciador de Tráfego, você verá um mapa geográfico com uma sobreposição dos insights da Exibição de Tráfego. O mapa fornece informações sobre a base de usuários e os pontos de extremidade para seu perfil do Gerenciador de Tráfego.

### <a name="user-base-information"></a>Informações da base de usuários

Para os resolvedores de DNS locais para os quais as informações de localização estão disponíveis, elas serão mostradas no mapa. A cor do resolvedor de DNS indica a latência média experimentada pelos usuários finais que usaram o resolvedor de DNS para suas consultas do Gerenciador de Tráfego.

Se você focalizar uma localização do resolvedor de DNS no mapa, ele mostrará:
- o endereço IP do resolvedor de DNS
- o volume de tráfego da consulta DNS visto pelo Gerenciador de Tráfego desse local
- os pontos de extremidade para os quais o tráfego do resolvedor de DNS foi encaminhado, como uma linha entre o ponto de extremidade e o resolvedor de DNS 
- a latência média desse local até o ponto de extremidade, representada como a cor da linha de conexão

### <a name="endpoint-information"></a>Informações do ponto de extremidade

As regiões do Azure em que residem os pontos de extremidade são mostradas como pontos azuis no mapa. Se o ponto de extremidade for externo e não tiver uma região do Azure mapeada para ele, ele será mostrado na parte superior do mapa. Clique em qualquer ponto de extremidade para ver as diferentes localizações (com base no resolvedor DNS usado) de onde o tráfego foi direcionado para esse ponto de extremidade. As conexões são mostradas como uma linha entre o ponto de extremidade e a localização do resolvedor DNS e são coloridas de acordo com a latência representativa entre esse par. Além disso, você pode ver o nome do ponto de extremidade, a região do Azure em que ele é executado e o volume total de solicitações que foram direcionadas para ele por este perfil do Gerenciador de Tráfego.


## <a name="tabular-listing-and-raw-data-download"></a>Download de listagem de tabela e de dados brutos

Exiba os dados da Exibição de Tráfego em um formato de tabela no portal do Azure. Há uma entrada para cada par de IP de resolvedor de DNS/ponto de extremidade que mostra o endereço IP do resolvedor de DNS, o nome e o local geográfico da região do Azure em que o ponto de extremidade está localizado (se disponível), o volume de solicitações associadas ao resolvedor de DNS para esse o ponto de extremidade e a latência representativa associada aos usuários finais que usam esse DNS (quando disponível). Também baixe os dados da Exibição de Tráfego como um arquivo CSV que pode ser usado como parte de um fluxo de trabalho de análise de sua escolha.

## <a name="billing"></a>Cobrança

Ao usar a Exibição do Tráfego, você é cobrado com base no número de pontos de dados usados para criar as informações apresentadas. Atualmente, o único tipo de ponto de dados usado são as consultas recebidas em seu perfil do Gerenciador de Tráfego. Para obter mais detalhes sobre os preços, visite o [página de preços do Gerenciador de Tráfego](https://azure.microsoft.com/pricing/details/traffic-manager/).


## <a name="next-steps"></a>Próximas etapas

- Saiba [como funciona o Gerenciador de Tráfego](traffic-manager-overview.md)
- Saiba mais sobre os [métodos de roteamento do tráfego](traffic-manager-routing-methods.md) com suporte pelo Gerenciador de Tráfego
- Aprenda a [criar um perfil do Gerenciador de Tráfego](traffic-manager-create-profile.md)

