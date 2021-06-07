---
title: Porta frontal do Azure-métodos de roteamento de tráfego | Microsoft Docs
description: Este artigo ajuda você a entender os diferentes métodos de roteamento de tráfego usados pelo Front Door
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
ms.openlocfilehash: 2bc056620ff964747dfd83e7525cb5bfd2eb8e52
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91449139"
---
# <a name="front-door-routing-methods"></a>Métodos de roteamento do Front Door

A porta frontal do Azure dá suporte a diferentes tipos de métodos de roteamento de tráfego para determinar como rotear seu tráfego HTTP/HTTPS para diferentes pontos de extremidade de serviço. Quando o cliente solicita a porta frontal, o método de roteamento configurado é aplicado para garantir que as solicitações sejam encaminhadas para a melhor instância de back-end. 

Há quatro métodos de roteamento de tráfego disponíveis na porta frontal:

* **[Latência](#latency):** o roteamento baseado em latência garante que as solicitações sejam enviadas para os back-ends de latência mais baixos aceitáveis dentro de um intervalo de sensibilidade. Basicamente, suas solicitações de usuário são enviadas para o conjunto "mais próximo" de back-ends em relação à latência de rede.
* **[Prioridade](#priority):** Você pode atribuir prioridades a seus back-ends quando desejar configurar um back-end primário para atender a todo o tráfego. O back-end secundário pode ser um backup caso o back-end primário fique indisponível.
* **[Ponderado](#weighted):** Você pode atribuir pesos aos back-ends quando desejar distribuir o tráfego entre um conjunto de back-ends. Se você deseja distribuir uniformemente ou de acordo com os coeficientes de peso.
* **[Afinidade de sessão](#affinity):** Você pode configurar a afinidade de sessão para seus hosts front-end ou domínios para garantir que as solicitações do mesmo usuário final sejam enviadas para o mesmo back-end.

Todas as configurações do Front Door incluem monitoramento de integridade de back-end e failover global instantâneo automatizado. Para obter mais informações, confira [Monitoramento de back-end do Front Door](front-door-health-probes.md). Sua porta frontal pode trabalhar com base em um único método de roteamento. Mas, dependendo das necessidades do seu aplicativo, você também pode combinar vários métodos de roteamento para criar uma topologia de roteamento ideal.

## <a name="lowest-latencies-based-traffic-routing"></a><a name = "latency"></a>Roteamento de tráfego baseado em latências mais baixas

A implantação de back-ends em dois ou mais locais em todo o mundo pode melhorar a capacidade de resposta de seus aplicativos roteando o tráfego para o destino que é ' mais próximo ' aos seus usuários finais. O método de roteamento de tráfego padrão para a configuração de porta frontal encaminha as solicitações de seus usuários finais para o back-end mais próximo do ambiente de porta frontal que recebeu a solicitação. Combinado com a arquitetura anycast da porta frontal do Azure, essa abordagem garante que cada um de seus usuários finais obtenha o máximo de desempenho personalizado com base em sua localização.

O back-end ' mais próximo ' não é necessariamente mais próximo que medido por distância geográfica. Em vez disso, o Front Door determina os back-ends mais próximos medindo a latência de rede. Leia mais sobre a [arquitetura de roteamento do Front Door](front-door-routing-architecture.md). 

Veja abaixo o fluxo geral de decisão:

| Back-ends disponíveis | Prioridade | Sinal de latência (baseado na investigação de integridade) | Pesos |
|-------------| ----------- | ----------- | ----------- |
| Primeiro, selecione todos os back-ends que estão habilitados e retornou íntegro (200 OK) para a investigação de integridade. Se houver seis back-ends A, B, C, D, e e F e entre eles, o C não está íntegro e o E está desabilitado. A lista de back-ends disponíveis é A, B, D e F.  | Em seguida, os back-ends de prioridade superior entre os disponíveis são selecionados. Se o back-end A, B e D tiverem prioridade 1 e o back-end F tiver uma prioridade de 2. Em seguida, os back-ends selecionados serão A, B e D.| Selecione os back-ends com intervalo de latência (menor latência e sensibilidade à latência especificadas em ms). Se o back-end A for 15 ms, B será 30 ms e D é de 60 MS longe do ambiente de porta frontal em que a solicitação descarregou, e a sensibilidade de latência for de 30 ms, o pool de latência mais baixo consistirá no back-end A e B, porque D está além de 30 ms para longe do back-end mais próximo que é um. | Por fim, o Front Door realizará round robin no tráfego entre o pool final de back-ends selecionado conforme a proporção de ponderação especificada. Por exemplo, se um back-end A tiver um peso 5 e o B tiver um peso 8, o tráfego será distribuído conforme a proporção de 5:8 entre os back-ends A e B. |

>[!NOTE]
> Por padrão, a propriedade de sensibilidade à latência é definida como 0 ms, ou seja, a solicitação sempre é encaminhada para o back-end mais rápido disponível.

## <a name="priority-based-traffic-routing"></a><a name = "priority"></a>Roteamento de tráfego baseado em prioridade

Muitas vezes, uma organização deseja fornecer alta disponibilidade para seus serviços implantando mais de um serviço de backup, caso o primário fique inativo. Em toda a indústria, essa topologia também é conhecida como topologia de implantação Ativo/Em espera ou Ativo/Passivo. O método de roteamento de tráfego por “Prioridade” permite que os clientes do Azure implementem esse padrão de failover com facilidade.

O Front Door padrão contém uma lista de back-ends de igual prioridade. Por padrão, o Front Door envia o tráfego somente para os back-ends de prioridade mais alta (valor mais baixo de prioridade), ou seja, o conjunto principal de back-ends. Se os back-ends primários não estiverem disponíveis, a porta frontal roteará o tráfego para o conjunto secundário de back-ends (segundo valor mais baixo para prioridade). Se os back-ends primário e secundário não estiverem disponíveis, o tráfego passará para o terceiro e assim por diante. A disponibilidade do back-end depende do status configurado (habilitado ou desabilitado) e do status contínuo de integridade do back-end, conforme determinado pela investigações de integridade.

### <a name="configuring-priority-for-backends"></a>Configurar prioridade para back-ends

Cada back-end em seu pool de back-end da configuração de porta frontal tem uma propriedade chamada ' priority ', que pode ser um número entre 1 e 5. Com a porta frontal do Azure, você configura a prioridade de back-end explicitamente usando essa propriedade para cada back-end. Essa propriedade é um valor entre 1 e 5. Valores mais baixos representam uma prioridade mais alta. Os back-ends podem ter os mesmos valores de prioridade.

## <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Método de roteamento de tráfego ponderado
O método de roteamento de tráfego “Ponderado” permite que você distribua o tráfego de maneira uniforme ou use uma ponderação predefinida.

No Método de roteamento de tráfego ponderado, você atribui um peso a cada back-end na configuração do Front Door do seu pool de back-ends. Cada peso é um inteiro de 1 a 1000. Esse parâmetro usa um peso padrão de “50”.

Com a lista de back-ends disponíveis que têm uma sensibilidade de latência aceitável, o tráfego é distribuído com um mecanismo de rodízio usando a taxa de pesos especificados. Se a sensibilidade de latência for definida como 0 milissegundos, essa propriedade não entrará em vigor, a menos que haja dois back-ends com a mesma latência de rede. 

O método ponderado permite alguns cenários úteis:

* **Atualização gradual de aplicativos**: fornece uma porcentagem de tráfego a ser roteado para um novo back-end e aumenta gradualmente o tráfego ao longo do tempo para trazê-lo em um mesmo nível com outros back-ends.
* **Migração de aplicativo para o Azure**: crie um pool de back-end com back-ends externos e do Azure. Ajuste o peso dos back-ends para dar preferência aos novos back-ends. Você pode definir isso gradualmente começando com os novos back-ends desabilitados e atribuindo a eles pesos mais baixos, aumentando lentamente até alcançar os níveis em que eles receberão a maior parte do tráfego. Por fim, basta desabilitar os back-end menos preferenciais e removê-los do pool.  
* **Estouro de nuvem para capacidade adicional**: expanda rapidamente uma implantação local na nuvem colocando-a atrás do Front Door. Quando você precisar de capacidade extra na nuvem, poderá adicionar ou habilitar mais back-ends e especificar qual parte do tráfego vai para cada back-end.

## <a name="session-affinity"></a><a name = "affinity"></a>Afinidade de sessão
Por padrão, sem afinidade de sessão, a porta frontal encaminha as solicitações provenientes do mesmo cliente para back-ends diferentes. Alguns aplicativos com monitoração de estado ou em determinados cenários que inprocessam solicitações do mesmo usuário preferem o mesmo back-end que processou a solicitação inicial. O recurso de afinidade de sessão baseada em cookies é útil quando você deseja manter uma sessão de usuário no mesmo back-end. Usando cookies gerenciados, a porta frontal do Azure pode direcionar o tráfego de uma sessão de usuário para o mesmo back-end para processamento.

A afinidade de sessão pode ser habilitada em um nível de host de front-end para cada um dos domínios (ou subdomínios) configurados. Uma vez habilitado, o Front Door adiciona um cookie à sessão do usuário. A afinidade de sessão baseada em cookie permite que o Front Door identifique os diferentes usuários, mesmo se estiverem por trás do mesmo endereço IP que, por sua vez, possibilita uma distribuição mais uniforme do tráfego entre seus diferentes back-ends.

O tempo de vida do cookie é o mesmo que a sessão do usuário, visto que o Front Door é compatível apenas com cookies de sessão no momento. 

> [!NOTE]
> Proxies públicos podem interferir nas afinidade de sessão. Isso ocorre porque estabelecer uma sessão requer que o Front Door adicione um cookie de afinidade de sessão à resposta, o que não poderá ser realizado se a resposta for armazenável em cache, pois ela poderia atrapalhar os cookies de outros clientes que solicitam o mesmo recurso. Para evitar isso, a afinidade de sessão **não** será estabelecida se o back-end enviar uma resposta armazenável em cache ao tentar fazer isso. Se a sessão já tiver sido estabelecida, não importará se a resposta de back-end for armazenável em cache ou não.
> A afinidade de sessão será estabelecida nas seguintes circunstâncias, **a menos que** a resposta tenha um código de status HTTP 304:
> - A resposta tem valores específicos definidos para o ```Cache-Control``` cabeçalho que impede o armazenamento em cache, como "particular" ou no-Store ".
> - A resposta contém um cabeçalho ```Authorization``` que não expirou.
> - A resposta tem um código de status HTTP 302.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).
