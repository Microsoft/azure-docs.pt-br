---
title: Azure Front Door Service – métodos de roteamento de tráfego | Microsoft Docs
description: Este artigo ajuda você a entender os diferentes métodos de roteamento de tráfego usados pelo Front Door
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: bd1278db43ba31ed78f13a826a330e16c3bc8d57
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736217"
---
# <a name="front-door-routing-methods"></a>Métodos de roteamento do Front Door

O Azure Front Door Service é compatível com diversos métodos de roteamento de tráfego para determinar como rotear o tráfego HTTP/HTTPS para os vários pontos de extremidade de serviço. O método de roteamento configurado é aplicado a cada solicitação do cliente que chega ao Front Door para garantir que as solicitações sejam direcionadas à melhor instância de back-end. 

Há quatro conceitos principais para roteamento de tráfego disponíveis no Front Door:

* **[Latência](#latency):** o roteamento baseado em latência garante que as solicitações sejam enviadas para os back-ends de latência mais baixos aceitáveis dentro de um intervalo de sensibilidade. Basicamente, as solicitações do usuário são enviadas para o conjunto de back-ends “mais próximo” com relação à latência de rede.
* **[Prioridade](#priority):** você pode atribuir prioridades para seus diferentes back-ends quando desejar usar um back-end de serviço primário para todo o tráfego e fornecer backups caso os back-ends primários ou de backup não estejam disponíveis.
* **[Ponderado](#weighted):** você pode atribuir pesos para seus diferentes back-ends quando desejar distribuir o tráfego entre um conjunto de back-ends, seja uniformemente ou de acordo com os coeficientes de peso.
* **Afinidade de sessão:** configure a afinidade de sessão para seus hosts de front-end ou domínios quando desejar que as solicitações subsequentes de um usuário sejam enviadas para o mesmo back-end, desde que a sessão do usuário ainda esteja ativa e a instância de back-end ainda seja relatada como íntegra nas investigações de integridade. 

Todas as configurações do Front Door incluem monitoramento de integridade de back-end e failover global instantâneo automatizado. Para obter mais informações, confira [Monitoramento de back-end do Front Door](front-door-health-probes.md). Seu Front Door pode ser configurado para funcionar com base em um único método de roteamento e, dependendo das necessidades do seu aplicativo, é possível usar vários ou todos esses métodos de roteamento juntos para criar uma topologia de roteamento ideal.

## <a name = "latency"></a>Roteamento de tráfego baseado em latências mais baixas

A implantação de back-ends em duas ou mais localizações do mundo pode melhorar a capacidade de resposta de vários aplicativos, encaminhando o tráfego para a localização “mais próxima” de você e dos usuários finais. O método de roteamento de tráfego padrão para a sua configuração do Front Door encaminha as solicitações dos usuários finais para o back-end mais próximo do ambiente do Front Door que recebeu a solicitação. Combinada com a arquitetura Anycast do Azure Front Door Service, essa abordagem garante que todos os seus usuários finais receberão desempenho máximo personalizado com base na localização deles.

O back-end “mais próximo” não é necessariamente o mais próximo em termos de distância geográfica. Em vez disso, o Front Door determina os back-ends mais próximos medindo a latência de rede. Leia mais sobre a [arquitetura de roteamento do Front Door](front-door-routing-architecture.md). 

Veja abaixo o fluxo geral de decisão:

| Back-ends disponíveis | Prioridade | Sinal de latência (baseado na investigação de integridade) | Pesos |
|-------------| ----------- | ----------- | ----------- |
| Em primeiro lugar, selecione todos os back-ends que estão habilitadas e foram declarados íntegros (200 OK) na investigação de integridade. Digamos que há seis back-ends, A, B, C, D, E e F e entre ele C não está íntegro e E está desabilitado. Portanto, a lista de back-ends disponíveis é A, B, D e F.  | Em seguida, são selecionados os back-ends de prioridade mais alta entre aqueles disponíveis. Digamos que os back-ends A, B e D têm prioridade 1 e o back-end F tem prioridade 2. Portanto, os back-ends selecionados serão A, B e D.| Selecione os back-ends com intervalo de latência (menor latência e sensibilidade à latência especificadas em ms). Por exemplo, se A estiver a 15 ms, B a 30 ms e D a 60 ms de distância do ambiente do Front Door em que a solicitação foi recebida e a sensibilidade à latência é de 30 ms, o pool de latência mais baixo será composto pelos back-ends A e B, porque D está mais longe do que 30 ms do back-end mais próximo, que é A. | Por fim, o Front Door realizará round robin no tráfego entre o pool final de back-ends selecionado conforme a proporção de ponderação especificada. Por exemplo, se um back-end A tiver um peso 5 e o B tiver um peso 8, o tráfego será distribuído conforme a proporção de 5:8 entre os back-ends A e B. |

>[!NOTE]
> Por padrão, a propriedade de sensibilidade à latência é definida como 0 ms, ou seja, a solicitação sempre é encaminhada para o back-end mais rápido disponível.


## <a name = "priority"></a>Roteamento de tráfego baseado em prioridade

Frequentemente, as organizações desejam fornecer confiabilidade para seus serviços implantando um ou mais serviços de backup, caso seu serviço primário fique inativo. Em toda a indústria, essa topologia também é conhecida como topologia de implantação Ativo/Em espera ou Ativo/Passivo. O método de roteamento de tráfego por “Prioridade” permite que os clientes do Azure implementem esse padrão de failover com facilidade.

O Front Door padrão contém uma lista de back-ends de igual prioridade. Por padrão, o Front Door envia o tráfego somente para os back-ends de prioridade mais alta (valor mais baixo de prioridade), ou seja, o conjunto principal de back-ends. Se o back-ends primários não estiverem disponíveis, o Front Door roteará o tráfego para o conjunto de back-ends secundário (segundo valor de prioridade mais baixa). Se ambos os pontos de extremidade primário e secundário não estiverem disponíveis, o tráfego passará para o terceiro, e assim por diante. A disponibilidade do back-end depende do status configurado (habilitado ou desabilitado) e do status contínuo de integridade do back-end, conforme determinado pela investigações de integridade.

### <a name="configuring-priority-for-backends"></a>Configurar prioridade para back-ends

Cada back-end no pool de back-end dentro da configuração do Front Door tem uma propriedade chamada “Priority” que pode ser um número entre 1 e 5. No Azure Front Door Service, a prioridade do back-end é configurada explicitamente usando essa propriedade para cada back-end. Essa propriedade é um valor entre 1 e 5. Valores mais baixos representam uma prioridade mais alta. Os back-ends podem ter os mesmos valores de prioridade.

## <a name = "weighted"></a>Método de roteamento de tráfego ponderado
O método de roteamento de tráfego “Ponderado” permite que você distribua o tráfego de maneira uniforme ou use uma ponderação predefinida.

No Método de roteamento de tráfego ponderado, você atribui um peso a cada back-end na configuração do Front Door do seu pool de back-ends. Cada peso é um inteiro de 1 a 1000. Esse parâmetro usa um peso padrão de “50”.

O tráfego é distribuído por meio de um mecanismo round robin na proporção dos pesos especificados entre a lista de back-ends disponíveis dentro da sensibilidade à latência aceita, conforme especificado. Se a sensibilidade à latência for definida como 0 milissegundo, essa propriedade não entrará em vigor, a menos que haja dois back-ends com a mesma latência de rede. 

O método ponderado permite alguns cenários úteis:

* **Atualização gradual do aplicativo**: aloque um percentual do tráfego para roteá-lo para um novo back-end e aumente gradualmente o tráfego com o passar do tempo até que ele esteja no mesmo nível dos demais back-ends.
* **Migração de aplicativos para o Azure**: crie um pool de back-end com back-ends externos e do Azure. Ajuste o peso dos back-ends para dar preferência aos novos back-ends. Você pode definir isso gradualmente começando com os novos back-ends desabilitados e atribuindo a eles pesos mais baixos, aumentando lentamente até alcançar os níveis em que eles receberão a maior parte do tráfego. Por fim, basta desabilitar os back-end menos preferenciais e removê-los do pool.  
* **Estouro de nuvem para capacidade adicional**: expanda rapidamente uma implantação local na nuvem colocando-a atrás do Front Door. Quando você precisar de capacidade extra na nuvem, poderá adicionar ou habilitar mais back-ends e especificar qual parte do tráfego vai para cada back-end.

## <a name = "affinity"></a>Afinidade de sessão
Por padrão, sem afinidade de sessão, o Front Door encaminha as solicitações originadas do mesmo cliente para back-ends diferentes com base na configuração de balanceamento de carga, especialmente no caso de alteração das latências de em back-ends diferentes ou se diferentes solicitações do mesmo usuário chegarem a outros ambientes do Front Door. No entanto, para alguns aplicativos com estado ou em determinados cenários, é preferível que as solicitações subsequentes do mesmo usuário sejam levadas para o mesmo back-end que processou a solicitação inicial. O recurso de afinidade de sessão baseada em cookies é útil quando você deseja manter uma sessão de usuário no mesmo back-end. Usando cookies gerenciados pelo Front Door, o Azure Front Door Service pode direcionar o tráfego subsequente de uma sessão de usuário para o mesmo back-end para processamento, desde que o back-end esteja íntegro e a sessão do usuário não tenha expirado. 

A afinidade de sessão pode ser habilitada em um nível de host de front-end para cada um dos domínios (ou subdomínios) configurados. Uma vez habilitado, o Front Door adiciona um cookie à sessão do usuário. A afinidade de sessão baseada em cookie permite que o Front Door identifique os diferentes usuários, mesmo se estiverem por trás do mesmo endereço IP que, por sua vez, possibilita uma distribuição mais uniforme do tráfego entre seus diferentes back-ends.

O tempo de vida do cookie é o mesmo que a sessão do usuário, visto que o Front Door é compatível apenas com cookies de sessão no momento. 

> [!NOTE]
> Proxies públicos podem interferir nas afinidade de sessão. Isso ocorre porque estabelecer uma sessão requer que o Front Door adicione um cookie de afinidade de sessão à resposta, o que não poderá ser realizado se a resposta for armazenável em cache, pois ela poderia atrapalhar os cookies de outros clientes que solicitam o mesmo recurso. Para evitar isso, a afinidade de sessão **não** será estabelecida se o back-end enviar uma resposta armazenável em cache ao tentar fazer isso. Se a sessão já tiver sido estabelecida, não importará se a resposta de back-end for armazenável em cache ou não.
> A afinidade de sessão será estabelecida nas seguintes circunstâncias, **a menos que** a resposta tenha um código de status HTTP 304:
> - A resposta tem valores específicos definidos para o cabeçalho ```Cache-Control``` que impedem o armazenamento em cache, como “private” (privado) ou “no-store” (não armazenar).
> - A resposta contém um cabeçalho ```Authorization``` que não expirou.
> - A resposta tem um código de status HTTP 302.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).
