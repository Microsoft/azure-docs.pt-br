---
title: Gerenciador de Tráfego do Azure – Perguntas frequentes
description: Este artigo fornece respostas a perguntas frequentes sobre o Gerenciador de Tráfego
services: traffic-manager
documentationcenter: ''
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: allensu
ms.openlocfilehash: 37f1a0d9c70afc0a3a86ac76b682ee7b2adb253d
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335800"
---
# <a name="traffic-manager-frequently-asked-questions-faq"></a>Perguntas frequentes sobre o Gerenciador de Tráfego

## <a name="traffic-manager-basics"></a>Noções básicas sobre o Gerenciador de Tráfego

### <a name="what-ip-address-does-traffic-manager-use"></a>Qual endereço IP o Gerenciador de Tráfego usa?

Conforme explicado em [Como funciona o Gerenciador de Tráfego](../traffic-manager/traffic-manager-how-it-works.md), o Gerenciador de Tráfego funciona no nível do DNS. Ele envia as respostas DNS para direcionar os clientes ao ponto de extremidade de serviço apropriado. Em seguida, os clientes se conectam diretamente ao ponto de extremidade de serviço, não pelo Gerenciador de Tráfego.

Portanto, o Gerenciador de Tráfego não fornece um ponto de extremidade ou o endereço IP para que os clientes se conectem. Se você desejar um endereço IP estático para o serviço, ele deverá ser configurado no serviço, não no Gerenciador de Tráfego.

### <a name="what-types-of-traffic-can-be-routed-using-traffic-manager"></a>Que tipos de tráfego podem ser roteados usando Gerenciador de Tráfego?
Conforme explicado em [Como o Gerenciador de Tráfego Funciona](../traffic-manager/traffic-manager-how-it-works.md), um ponto de extremidade de Gerenciador de Tráfego pode ser qualquer internet voltada para o serviço hospedado dentro ou fora do Azure. Portanto, Gerenciador de Tráfego pode rotear tráfego que origina da internet pública para um conjunto de pontos de extremidade que também são voltado para a internet. Se você tiver pontos de extremidade que estão dentro de uma rede privada (por exemplo, uma versão interna do [Azure Load Balancer](../load-balancer/load-balancer-overview.md#internalloadbalancer)) ou têm usuários fazendo solicitações DNS de tais redes internas, será possível usar o Gerenciador de Tráfego do Microsoft Azure para rotear esse tráfego.

### <a name="does-traffic-manager-support-sticky-sessions"></a>O Gerenciador de tráfego dá suporte a sessões "adesivas"?

Conforme explicado em [Como funciona o Gerenciador de Tráfego](../traffic-manager/traffic-manager-how-it-works.md), o Gerenciador de Tráfego funciona no nível do DNS. Ele usa as respostas DNS para direcionar os clientes ao ponto de extremidade de serviço apropriado. Os clientes se conectam diretamente ao ponto de extremidade de serviço, não pelo Gerenciador de Tráfego. Portanto, o Gerenciador de Tráfego não vê o tráfego HTTP entre o cliente e o servidor.

Além disso, o endereço IP de origem da consulta DNS recebida pelo Gerenciador de Tráfego pertence ao serviço DNS recursivo, não ao cliente. Portanto, o Gerenciador de Tráfego não consegue acompanhar clientes individuais e não pode implementar sessões “temporárias”. Essa limitação é comum a todos os sistemas de gerenciamento de tráfego baseados em DNS e não é específica ao Gerenciador de Tráfego.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Por que vejo um erro de HTTP ao usar o Gerenciador de Tráfego?

Conforme explicado em [Como funciona o Gerenciador de Tráfego](../traffic-manager/traffic-manager-how-it-works.md), o Gerenciador de Tráfego funciona no nível do DNS. Ele usa as respostas DNS para direcionar os clientes ao ponto de extremidade de serviço apropriado. Em seguida, os clientes se conectam diretamente ao ponto de extremidade de serviço, não pelo Gerenciador de Tráfego. O Gerenciador de Tráfego não vê o tráfego HTTP entre o cliente e o servidor. Portanto, qualquer erro HTTP visto deve ser proveniente do aplicativo. Para que o cliente se conecte ao aplicativo, todas as etapas de resolução DNS são concluídas. Isso inclui qualquer interação que o Gerenciador de Tráfego tem no fluxo de tráfego do aplicativo.

Portanto, as investigações adicionais devem se concentrar no aplicativo.

O cabeçalho de host HTTP enviado do navegador do cliente é a fonte mais comum de problemas. Verifique se o aplicativo está configurado para aceitar o cabeçalho de host correto para o nome de domínio que está sendo usado. Para pontos de extremidade que usam o Serviço de Aplicativo do Azure, consulte [Configurando um nome de domínio personalizado para um aplicativo Web no Serviço de Aplicativo do Azure usando o Gerenciador de Tráfego](../app-service/web-sites-traffic-manager-custom-domain-name.md).

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>Qual é o impacto no desempenho de usar o Gerenciador de Tráfego?

Conforme explicado em [Como funciona o Gerenciador de Tráfego](../traffic-manager/traffic-manager-how-it-works.md), o Gerenciador de Tráfego funciona no nível do DNS. Como os clientes se conectam diretamente ao pontos de extremidade de seu serviço, não há qualquer impacto no desempenho ao usar o Gerenciador de Tráfego quando a conexão é estabelecida.

Como o Gerenciador de Tráfego é integrado aos aplicativos no nível de DNS, ele requer uma pesquisa de DNS adicional para ser inserido na cadeia de resolução de DNS. O impacto do Gerenciador de Tráfego no tempo de resolução de DNS é mínimo. O Gerenciador de Tráfego usa uma rede global de servidores de nomes, bem como a rede [anycast](https://en.wikipedia.org/wiki/Anycast) para garantir que as consultas DNS são sempre encaminhadas para o servidor de nomes mais próximo disponível. Além disso, o armazenamento em cache de respostas DNS significa que a latência DNS adicional gerada pelo uso do Gerenciador de Tráfego só se aplica a uma fração de sessões.

O método por Desempenho encaminha o tráfego para o ponto de extremidade mais próximo disponível. O resultado final é que o impacto de desempenho geral associado a esse método deve ser mínimo. Qualquer aumento na latência DNS deve ser deslocado pela menor latência de rede para o ponto de extremidade.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Quais protocolos de aplicativo posso usar com o Gerenciador de Tráfego?

Conforme explicado em [Como funciona o Gerenciador de Tráfego](../traffic-manager/traffic-manager-how-it-works.md), o Gerenciador de Tráfego funciona no nível do DNS. Após a conclusão da pesquisa de DNS, os clientes se conectam diretamente ao ponto de extremidade do aplicativo, não pelo Gerenciador de Tráfego. Portanto, a conexão pode usar qualquer protocolo de aplicativo. Se você selecionar TCP como protocolo de monitoramento, o monitoramento de integridade do ponto de extremidade do Gerenciador de Tráfego poderá ser feito sem usar qualquer protocolo de aplicativo. Se você optar por ter a integridade verificada usando um protocolo de aplicativo, o ponto de extremidade precisará ser capaz de responder às solicitações HTTP ou HTTPS GET.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>Posso usar o Gerenciador de tráfego com um nome de domínio "Naked"?

Sim. Para saber como criar um registro de alias para o Apex do nome de domínio para fazer referência a um perfil do Gerenciador de tráfego do Azure, consulte [configurar um registro de alias para dar suporte a nomes de domínio Apex com o Gerenciador de tráfego](../dns/tutorial-alias-tm.md).

### <a name="does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries"></a>O Gerenciador de Tráfego considera o endereço de sub-rede do cliente ao manipular consultas DNS? 

Sim, além do endereço IP de origem da consulta DNS que ele recebe (que normalmente é o endereço IP do resolvedor de DNS), ao executar pesquisas para os métodos de roteamento de Sub-rede, Desempenho e Geográfico, o gerenciador de tráfego também considera o endereço de sub-rede do cliente se ele estiver incluído na consulta pelo resolvedor que faz a solicitação em nome do usuário final.  
Especificamente, a [RFC 7871 – sub-rede do cliente nas consultas DNS](https://tools.ietf.org/html/rfc7871) que fornece um [Mecanismo de Extensão para DNS (EDNS0)](https://tools.ietf.org/html/rfc2671) que pode passar o endereço de sub-rede do cliente dos resolvedores com suporte.

### <a name="what-is-dns-ttl-and-how-does-it-impact-my-users"></a>O que é o TTL do DNS e como ele afeta os meus usuários?

Quando uma consulta DNS chega no Gerenciador de Tráfego, ela define um valor na resposta chamado tempo de vida (TTL). Esse valor, cuja unidade é em segundos, indica ao downstream dos resolvedores DNS o tempo necessário para armazenar em cache essa resposta. Enquanto não há garantia de que os resolvedores DNS armazenem esse resultado em cache, este armazenamento permite que respondam a todas as consultas subsequentes além do cache, em vez de irem para servidores DNS do Gerenciador de Tráfego. Isso afeta as respostas da seguinte maneira:

- um TTL maior reduz o número de consultas que chegam aos servidores DNS do Gerenciador de Tráfego, o que pode reduzir o custo para um cliente, pois o número de consultas apresentadas é um uso faturável.
- um TTL maior pode reduzir potencialmente o tempo que leva para realizar uma pesquisa de DNS.
- um TTL maior também significa que os dados não refletem as últimas informações sobre integridade que o Gerenciador de Tráfego obteve por meio de seus agentes de investigação.

### <a name="how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses"></a>Como posso definir o nível (alto ou baixo) do TTL para respostas do Gerenciador de Tráfego?

Você pode definir, em um nível por perfil, o TTL do DNS tão baixo quanto 0 segundos e tão alto quanto 2.147.483.647 segundos (o intervalo máximo em conformidade com [RFC-1035](https://www.ietf.org/rfc/rfc1035.txt )). Um TTL de 0 significa que os resolvedores DNS de downstream não armazenam em cache respostas de consultas e que todas as consultas devem alcançar os servidores DNS do Gerenciador de Tráfego para a resolução.

### <a name="how-can-i-understand-the-volume-of-queries-coming-to-my-profile"></a>Como faço para entender o volume de consultas que chega ao meu perfil? 

Um das métricas de fornecidas pelo Gerenciador de Tráfego é o número de consultas respondidas por um perfil. Consiga essas informações em uma agregação de nível de perfil, ou divida-as para ver o volume de consultas no qual os pontos de extremidade específicos foram retornados. Além disso, você pode configurar alertas para receber uma notificação se o volume de resposta da consulta atender às condições que você definiu. Para obter mais detalhes, [Métricas e alertas do Gerenciador de Tráfego](traffic-manager-metrics-alerts.md).

## <a name="traffic-manager-geographic-traffic-routing-method"></a>Método de roteamento de tráfego geográfico do Gerenciador de Tráfego

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>Quais são alguns casos de uso em que o roteamento geográfico é útil?

O tipo de roteamento geográfico pode ser usado em qualquer cenário em que um cliente do Azure precise diferenciar seus usuários com base nas regiões geográficas. Por exemplo, ao usar o método de roteamento de tráfego geográfico, você proporciona aos usuários de regiões específicas uma experiência de usuário diferente daquelas de outras regiões. Outro exemplo é estar em conformidade com as normas soberanas de dados locais que exigem que os usuários de uma região específica sejam atendidos somente pelos pontos de extremidade dessa região.

### <a name="how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method"></a>Como fazer para decidir se eu devo usar o método de roteamento de desempenho ou método de roteamento geográfico?

A principal diferença entre esses dois métodos de roteamentos populares é que, no método de roteamento de desempenho o principal objetivo é enviar o tráfego para o ponto de extremidade que pode fornecer a latência mais baixa para o chamador, enquanto no roteamento geográfico o objetivo principal é impor uma limitação geográfica para os chamadores, para que você possa encaminhá-los deliberadamente para um ponto de extremidade específico. A sobreposição acontece porque não há uma correlação entre proximidade geográfica e latência mais baixa, embora isso nem sempre seja verdadeiro. Pode haver um ponto de extremidade em uma geografia diferente que pode fornecer uma experiência de latência melhor para o chamador e, nesse caso, o roteamento de desempenho enviará o usuário para esse ponto de extremidade, mas o roteamento geográfico sempre os enviará para o ponto de extremidade que você mapeou para o região geográfica. Para esclarecer isso ainda mais, veja o seguinte exemplo - com o roteamento geográfico, você pode fazer mapeamentos incomuns, como enviar todo o tráfego da Ásia para pontos de extremidade nos EUA, e em todo o tráfego dos EUA para pontos de extremidade na Ásia. Nesse caso, o roteamento geográfico fará deliberadamente exatamente o que você configurou, e a otimização de desempenho não entra em consideração. 
>[!NOTE]
>Talvez, em algumas situações, você precise dos recursos do roteamento de desempenho e geográfico. Nesses cenários, os perfis aninhados podem ser uma ótima opção. Por exemplo, configure um perfil pai com roteamento geográfico, em que você envia todo o tráfego da América do Norte para um perfil aninhado com pontos de extremidade nos EUA, e usa o roteamento de desempenho para enviar esses tráfego para o melhor ponto de extremidade nesse conjunto. 

### <a name="what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing"></a>Quais são as regiões com suporte do Gerenciador de Tráfego para roteamento geográfico?

A hierarquia de país/região usada pelo Gerenciador de Tráfego pode ser encontrada [aqui](traffic-manager-geographic-regions.md). Embora essa página esteja sempre atualizada com todas as alterações, você também pode recuperar, de modo programático, as mesmas informações usando a [API REST do Gerenciador de Tráfego do Azure](https://docs.microsoft.com/rest/api/trafficmanager/). 

### <a name="how-does-traffic-manager-determine-where-a-user-is-querying-from"></a>Como o Gerenciador de Tráfego determina de que região um usuário está fazendo uma consulta?

O Gerenciador de Tráfego analisa o IP de origem da consulta (provavelmente é um resolvedor DNS local fazendo a consulta em nome do usuário) e usa um IP interno do mapa da região para determinar o local. Esse mapa é atualizado continuamente para levar em conta as alterações na Internet. 

### <a name="is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case"></a>Há garantia de que o Gerenciador de Tráfego possa determinar o local geográfico exato do usuário em todos os casos corretamente?

Não, o Gerenciador de Tráfego não pode assegurar que a região geográfica que podemos deduzir do endereço IP de origem de uma consulta DNS sempre corresponderá ao local do usuário, pelos seguintes motivos:

- Primeiro, conforme descrito nas perguntas frequentes anteriores, o endereço IP de origem que vemos é aquele de um resolvedor DNS fazendo a pesquisa em nome do usuário. Embora a localização geográfica do resolvedor DNS seja um bom proxy para a localização geográfica do usuário, ela também pode ser diferente dependendo da superfície de serviço de resolvedor DNS e do serviço de resolvedor DNS específico que um cliente tiver optado por usar. Por exemplo, um cliente localizado na Malásia poderia especificar no uso de configurações do seu dispositivo um serviço de resolução DNS cujo servidor DNS em Singapura pode selecionado para tratar as resoluções de consulta desse usuário/dispositivo. Nesse caso, o Gerenciador de Tráfego pode ver somente o endereço IP do resolvedor que corresponde à localização de Singapura. Além disso, consulte as Perguntas Frequentes anteriores sobre o suporte de endereço de sub-rede do cliente nesta página.

- Em segundo lugar, o Gerenciador de Tráfego usa um mapa interno para fazer a conversão do endereço IP para a região geográfica. Embora esse mapa seja validado e atualizado regularmente para aumentar a precisão e leve em conta a natureza evolutiva da Internet, ainda há a possibilidade de que nossas informações não sejam uma representação exata da localização geográfica de todos os endereços IP.

###  <a name="does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing"></a>Um ponto de extremidade precisa estar fisicamente localizado na mesma região com a qual ele foi configurado para roteamento geográfico?

Não, o local do ponto de extremidade não impõe restrições sobre quais regiões podem ser mapeadas para ele. Por exemplo, um ponto de extremidade na região Central dos EUA do Azure pode ter todos os usuários da Índia direcionados para ele.

### <a name="can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing"></a>Posso atribuir regiões geográficas aos pontos de extremidade em um perfil que não esteja configurado para fazer roteamento geográfico?

Sim, se o método de roteamento de um perfil não for geográfico, você poderá usar a [API REST do Gerenciador de Tráfego do Azure](https://docs.microsoft.com/rest/api/trafficmanager/) para atribuir regiões geográficas aos pontos de extremidade nesse perfil. No caso de perfis de tipo de roteamento não geográfico, essa configuração será ignorada. Se você alterar esse perfil para tipo de roteamento geográfico em um momento posterior, o Gerenciador de Tráfego usará esses mapeamentos.

### <a name="why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic"></a>Por que está ocorrendo um erro ao tentar alterar o método de roteamento de um perfil existente para Geográfico?

Todos os pontos de extremidade em um perfil com roteamento geográfico precisam ter pelo menos uma região mapeada para eles. Para converter um perfil existente em tipo de roteamento geográfico, primeiramente, você precisa associar regiões geográficas a todos os respectivos pontos de extremidade usando a [API REST do Gerenciador de Tráfego do Azure](https://docs.microsoft.com/rest/api/trafficmanager/) antes de alterar o tipo de roteamento para geográfico. Se for usar o portal, primeiramente exclua os pontos de extremidade, altere o método de roteamento do perfil para geográfico e, por fim, adicione os pontos de extremidade com o respectivo mapeamento da região geográfica.

### <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>Por que é enfaticamente aconselhável que os clientes criem perfis aninhados em vez de pontos de extremidade em um perfil com o roteamento geográfico habilitado?

Uma região pode ser atribuída a apenas um ponto de extremidade em um perfil se ele estiver usando o método de roteamento geográfico. Se esse ponto de extremidade não for um tipo aninhado com um perfil filho conectado a ele, caso esse ponto de extremidade se torne não íntegro, o Gerenciador de Tráfego continuará enviando tráfego a ele, pois a alternativa de não enviar tráfego não é a opção melhor. O Gerenciador de Tráfego não faz failover para outro ponto de extremidade, mesmo quando a região atribuída for "pai" da região atribuída ao ponto de extremidade que deixou de ser íntegro (por exemplo, se um ponto de extremidade que tenha a região Espanha deixar de ser íntegro, não ocorrerá o failover para outro ponto de extremidade que tenha a região Europa atribuída). Isso é para garantir que o Gerenciador de Tráfego respeite os limites geográficos que um cliente configurou em seu perfil. Para obter o benefício de fazer failover para outro ponto de extremidade quando um se torna não íntegro, é aconselhável que as regiões geográficas sejam atribuídas a perfis aninhados com vários pontos de extremidade neles, em vez de pontos de extremidade individuais. Dessa forma, se um ponto de extremidade no perfil filho aninhado falhar, o tráfego pode fazer failover para outro ponto de extremidade no mesmo perfil filho aninhado.

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>Existem restrições quanto à versão de API que oferece suporte a esse tipo de roteamento?

Sim, somente a versão 2017-03-01 da API e as mais recentes dão suporte ao tipo de roteamento Geográfico. Qualquer versão de API mais antiga não pode ser usada para criar perfis de tipo de roteamento Geográfico ou atribuir regiões geográficas aos pontos de extremidade. Se uma versão de API mais antiga for usada para recuperar perfis de uma assinatura do Azure, nenhum perfil de tipo de roteamento Geográfico será retornado. Além disso, ao usar versões de API mais antigas, todos os perfis retornados que tiverem pontos de extremidade com uma atribuição de região geográfica não terão essa atribuição exibida.

## <a name="traffic-manager-subnet-traffic-routing-method"></a>Método de roteamento de tráfego de Sub-rede do Gerenciador de Tráfego

### <a name="what-are-some-use-cases-where-subnet-routing-is-useful"></a>Quais são alguns casos de uso em que o roteamento de sub-rede é útil?

O roteamento de sub-rede permite diferenciar a experiência que você fornece para conjuntos específicos de usuários identificados pelo IP de origem do endereço IP de solicitações de DNS. Um exemplo seria mostrar um conteúdo diferente se os usuários estiverem conectando um site da sede corporativa. Outra seria restringir os usuários de determinados ISPs a acessarem somente pontos de extremidade que dão suporte apenas a conexões IPv4 se esses ISPs tiverem um desempenho abaixo da média quando o IPv6 for usado.
Outro motivo para usar o método de roteamento de Sub-rede é juntamente com outros perfis em um conjunto de perfis aninhados. Por exemplo, se quiser usar o método de roteamento geográfico para isolamento geográfico de seus usuários, mas para um ISP específico que deseja fazer um método de roteamento diferente, você pode ter um perfil com o método de roteamento de Sub-rede como o perfil pai e substituir esse ISP para usar um perfil filho específico e ter o perfil Geográfico padrão para todos os outros.

### <a name="how-does-traffic-manager-know-the-ip-address-of-the-end-user"></a>Como o Gerenciador de Tráfego reconhece o endereço IP do usuário final?

Dispositivos de usuário final normalmente usam um resolvedor de DNS para fazer a pesquisa de DNS em seu nome. O IP de saída de tais resolvedores é o que o Gerenciador de Tráfego reconhece como o IP de origem. Além disso, o método de roteamento de Sub-rede também verifica se há informações de ECS (Sub-rede do Cliente Estendida) EDNS0 que foram passadas com a solicitação. Se a informação de ECS estiver presente, esse será o endereço usado para determinar o roteamento. Na ausência de informações de ECS, o IP de origem da consulta é usado para fins de roteamento.

### <a name="how-can-i-specify-ip-addresses-when-using-subnet-routing"></a>Como posso especificar endereços IP ao usar o roteamento de sub-rede?

Os endereços IP a serem associados a um ponto de extremidade podem ser especificados de duas maneiras. Primeiro, é possível usar a notação de octeto decimal quad-pontilhado com um endereço inicial e final para especificar o intervalo (por exemplo, 1.2.3.4-5.6.7.8 ou 3.4.5.6-3.4.5.6). Segundo, é possível usar a notação CIDR para especificar o intervalo (por exemplo, 1.2.3.0/24). Você pode especificar vários intervalos e usar os dois tipos de notação em um conjunto de intervalos. Algumas restrições são aplicáveis.

-   Não é possível sobreposição de intervalos de endereços, pois cada IP precisa ser mapeado para apenas um único ponto de extremidade
-   O endereço inicial não pode ser maior que o endereço final
-   No caso da notação CIDR, o endereço IP antes de "/" deve ser o endereço inicial desse intervalo (por exemplo, 1.2.3.0/24 é válido, mas 1.2.3.4.4 / 24 NÃO é válido)

### <a name="how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing"></a>Como posso especificar um ponto de extremidade de fallback ao usar o roteamento de sub-rede?

Em um perfil com roteamento de Sub-rede, se houver um ponto de extremidade sem sub-redes mapeadas para ele, qualquer solicitação que não corresponda a outros pontos de extremidade será direcionada por aqui. É altamente recomendável ter esse ponto de extremidade de fallback no perfil, pois o Gerenciador de Tráfego retornará uma resposta NXDOMAIN se uma solicitação chegar e não estiver mapeada para nenhum ponto de extremidade ou se estiver mapeada para um ponto de extremidade, mas esse ponto de extremidade não estiver íntegro.

### <a name="what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile"></a>O que acontece se um ponto de extremidade estiver desabilitado em um perfil de tipo de roteamento de sub-rede?

Em um perfil com roteamento de Sub-rede, se houver um ponto de extremidade com esse valor desabilitado, o Gerenciador de Tráfego se comportará como se esse ponto de extremidade e os mapeamentos de sub-rede que ele possui não existissem. Se uma consulta correspondente ao mapeamento de endereço IP for recebida e o ponto de extremidade estiver desabilitado, o Gerenciador de Tráfego retornará um ponto de extremidade de fallback (um sem mapeamentos) ou, se esse ponto de extremidade não estiver presente, retornará uma resposta NXDOMAIN.

## <a name="traffic-manager-multivalue-traffic-routing-method"></a>Método de roteamento de tráfego de Múltiplos Valores do Gerenciador de Tráfego

### <a name="what-are-some-use-cases-where-multivalue-routing-is-useful"></a>Quais são alguns casos de uso em que o roteamento de Múltiplos Valores é útil?

O roteamento de Múltiplos Valores retorna vários pontos de extremidade íntegros em uma única resposta de consulta. A principal vantagem disso é que, se um ponto de extremidade não estiver íntegro, o cliente terá mais opções para tentar novamente sem fazer outra chamada DNS (o que pode retornar o mesmo valor de um cache de upstream). Isso é aplicável a aplicativos confidenciais de disponibilidade que desejam minimizar o tempo de inatividade.
Outro uso para o método de roteamento de Múltiplos Valores é se um ponto de extremidade for "dual-homed" para endereços IPv4 e IPv6 e você quiser fornecer ao chamador ambas as opções para escolher quando iniciar uma conexão com o ponto de extremidade.

### <a name="how-many-endpoints-are-returned-when-multivalue-routing-is-used"></a>Quantos pontos de extremidade serão retornados quando o roteamento de Múltiplos Valores for usado?

Você pode especificar o número máximo de pontos de extremidade a serem retornados e quando uma consulta for recebida, os Múltiplos Valores não retornarão pontos de extremidade íntegros acima do especificado. O valor máximo possível para essa configuração é 10.

### <a name="will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used"></a>Obterei o mesmo conjunto de pontos de extremidade quando o roteamento de Múltiplos Valores for usado?

Não é possível garantir que o mesmo conjunto de pontos de extremidade seja retornado em cada consulta. Isso também é afetado pelo fato de que alguns dos pontos de extremidade podem se tornar não íntegros, ponto no qual eles não serão incluídos na resposta

## <a name="real-user-measurements"></a>Medidas Reais de Usuário

### <a name="what-are-the-benefits-of-using-real-user-measurements"></a>Quais são os benefícios do uso de Medidas Reais de Usuário?

Quando você usa o método de roteamento de desempenho, o Gerenciador de Tráfego escolhe a melhor região do Azure para que o usuário final se conecte, inspecionando o IP de origem e a sub-rede do cliente EDNS (se for passada) e verificando-os em relação à inteligência de latência da rede que o serviço mantém. O recurso de Medidas Reais de Usuário melhora isso para sua base de usuários finais, fazendo com que a experiência deles contribua nessa tabela de latência, além de assegurar que essa tabela abranja corretamente as redes dos usuários finais das quais os usuários finais se conectam ao Azure. Isso leva a um aumento de precisão no roteamento de seus usuários finais.

### <a name="can-i-use-real-user-measurements-with-non-azure-regions"></a>É possível usar as Medidas Reais de Usuário com regiões que não são do Azure?

O recurso de Medidas Reais de Usuário mede e relata apenas a latência para alcançar as regiões do Azure. Se você estiver usando o roteamento baseado em desempenho com pontos de extremidade hospedados em regiões que não são do Azure, você ainda poderá aproveitar esse recurso com o aumento de informações de latência sobre a região do Azure representante que você selecionou para associar a esse ponto de extremidade.

### <a name="which-routing-method-benefits-from-real-user-measurements"></a>Qual método de roteamento se beneficia das Medidas Reais de Usuário?

As informações adicionais obtidas por meio das Medidas Reais de Usuário são aplicáveis apenas aos perfis que usam o método de roteamento de desempenho. O link de Medidas de Usuário Reais está disponível em todos os perfis exibidos por meio do portal do Azure.

### <a name="do-i-need-to-enable-real-user-measurements-each-profile-separately"></a>É necessário habilitar as Medidas Reais de Usuário para cada perfil separadamente?

Não, você precisa habilitá-las uma únicas vez para cada assinatura e todas as informações de latência medidas e relatadas estarão disponíveis para todos os perfis.

### <a name="how-do-i-turn-off-real-user-measurements-for-my-subscription"></a>Como desligar as Medidas Reais de Usuário da minha assinatura?

Você pode interromper o acúmulo de encargos relacionados às Medidas Reais de Usuário ao parar de coletar e enviar as medidas de latência do seu aplicativo cliente. Por exemplo, quando o JavaScript de medida está inserido em páginas da Web, você pode parar de usar esse recurso removendo o JavaScript ou desabilitando sua invocação quando a página é renderizada.

Você também pode desativar Medidas de Usuário Reais excluindo sua chave. Depois de excluir a chave, caso haja medidas enviadas ao Gerenciador de Tráfego com essa chave, elas serão descartadas.

### <a name="can-i-use-real-user-measurements-with-client-applications-other-than-web-pages"></a>Posso usar as Medidas Reais de Usuário com aplicativos clientes que não sejam páginas da Web?

Sim, as Medidas Reais de Usuário são projetadas para ingeir dados coletados por meio de diferentes tipos de clientes do usuário final. Estas perguntas frequentes serão atualizadas à medida que novos tipos de clientes obtenham suporte.

### <a name="how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered"></a>Quantas medidas são feitas quando uma página da Web habilitada para Medidas Reais de Usuário é renderizada?

Quando o recurso de Medidas Reais de Usuário é usado com o JavaScript de medidas fornecido, cada página renderizada resulta em seis medidas. Em seguida, elas são retornadas ao serviço do Gerenciador de Tráfego. Você será cobrado por esse recurso com base no número de medidas relatadas ao serviço do Gerenciador de Tráfego. Por exemplo, se o usuário sair da sua página da Web enquanto as medidas estiverem sendo obtidas, mas antes que elas sejam relatadas, essas medidas não serão consideradas para fins de cobrança.

### <a name="is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage"></a>Existe algum atraso antes que o script das Medidas Reais de Usuário seja executado na minha página da Web?

Não há nenhum atraso programado antes que o script seja invocado.

### <a name="can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure"></a>Posso usar as Medidas de Usuário Reais apenas com as regiões do Azure que desejo medir?

Não, sempre que o recurso é invocado, o script de Medidas Reais do Usuário mede um conjunto de seis regiões do Azure, conforme é determinado pelo serviço. Esse conjunto é alterado entre diferentes invocações e quando um grande número dessas invocações acontece, a cobertura das medidas abrange diferentes regiões do Azure.

### <a name="can-i-limit-the-number-of-measurements-made-to-a-specific-number"></a>Posso limitar o número de medidas feitas para um número específico?

O JavaScript de medidas é inserido em sua página da Web e você tem o controle total sobre quando iniciar e parar de usá-lo. Desde que o serviço do Gerenciador de Tráfego recebe uma solicitação para obter uma lista de regiões do Azure a serem medidas, um conjunto de regiões será retornado.

### <a name="can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements"></a>Posso ver as medidas obtidas pelo meu aplicativo cliente como parte das Medidas Reais de Usuário?

Como a lógica de medida é executada no seu aplicativo cliente, você tem o controle total sobre o que acontece, incluindo observar as medições de latência. O Gerenciador de Tráfego não relata uma exibição agregada das medidas recebidas sob a chave vinculada à sua assinatura.

### <a name="can-i-modify-the-measurement-script-provided-by-traffic-manager"></a>Posso modificar o script de medida fornecido pelo Gerenciador de Tráfego?

Enquanto você controlar o que é inserido em sua página da Web, nós realmente recomendamos que você não faça alterações no script de medidas para garantir que ele meça e relate as latências corretamente.

### <a name="will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements"></a>Será possível que outras pessoas vejam a chave que eu uso com as Medidas Reais de Usuário?

Quando você inserir o script de medidas em uma página da Web, outras pessoas poderão ver o script e a sua chave de RUM (Medidas Reais de Usuário). Mas é importante saber que essa chave é diferente da ID da sua assinatura e ela é gerada pelo Gerenciador de Tráfego a ser usado apenas para essa finalidade. O fato de outras pessoas saberem qual é a sua chave de RUM não comprometerá a segurança da conta do Azure.

### <a name="can-others-abuse-my-rum-key"></a>Outras pessoas podem usar minha chave de RUM?

Embora seja possível que outras pessoas usem sua chave para enviar informações erradas ao Azure, algumas medidas erradas não alterarão o roteamento, pois ele é considerado em conjunto com todas as outras medidas que recebemos. Se precisar alterar as chaves, você poderá gerar a chave novamente no ponto em que a chave antiga for descartada.

### <a name="do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages"></a>É necessário colocar o JavaScript de medida em todas as páginas da Web?

O recurso de Medidas Reais de Usuário oferece mais valor à medida que o número de medidas aumenta. Dito isso, fica a seu critério se é necessário colocá-lo em todas as páginas da Web ou apenas em algumas. Nossa recomendação é começar colocando-o em sua página mais visitada, na qual o usuário geralmente permanece por cinco segundos ou mais.

### <a name="can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements"></a>As informações sobre meus usuários finais poderão ser identificadas pelo Gerenciador de Tráfego se eu usar as Medidas Reais de Usuário?

Quando o JavaScript de medida fornecido for usado, o Gerenciador de Tráfego terá visibilidade do endereço IP do cliente do usuário final e do endereço IP de origem do resolvedor de DNS local usado. O Gerenciador de Tráfego usa o endereço IP do cliente somente após truncá-lo para não ser capaz de identificar o usuário final específico que enviou as medidas.

### <a name="does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing"></a>A página da Web que está medindo as Medidas Reais de Usuário precisa estar usando o Gerenciador de Tráfego para o roteamento?

Não, ela não precisa usar o Gerenciador de Tráfego. O lado de roteamento do Gerenciador de Tráfego opera separadamente da parte das Medidas Reais do Usuário e embora seja uma boa ideia que ambos estejam na mesma propriedade de Web, eles não precisam estar.

### <a name="do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements"></a>É necessário hospedar algum serviço nas regiões do Azure a serem usadas com as Medidas Reais de Usuário?

Não, você não precisa hospedar nenhum componente do servidor no Azure para que as Medidas de Usuário Reais funcionem. A imagem de pixel único baixada pelo JavaScript de medida e o serviço que o executa em diferentes regiões do Azure é hospedado e gerenciado pelo Azure. 

### <a name="will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements"></a>Meu uso de largura de banda do Azure aumentará com o uso das Medidas Reais de Usuário?

Conforme foi mencionado na resposta anterior, os componentes do servidor das Medidas Reais de Usuário pertencem ao Azure e são gerenciados pelo Azure. Isso significa que o uso de largura de banda do Azure não aumentará com o uso das Medidas Reais de Usuário. Isso não inclui nenhum uso de largura de banda adicional à que é cobrada pelo Azure. Podemos minimizar a largura de banda usada baixando uma imagem de pixel único para medir a latência para uma região do Azure. 

## <a name="traffic-view"></a>Exibição do Tráfego

### <a name="what-does-traffic-view-do"></a>O que faz a Exibição do Tráfego faz?

A Exibição do Tráfego é um recurso do Gerenciador de Tráfego que ajuda a saber mais sobre os usuários e suas experiências. Ele usa as consultas recebidas pelo Gerenciador de Tráfego e as tabelas de inteligência de latência de rede que o serviço mantém para fornecer o seguinte:

- As regiões de onde os usuários se conectam aos pontos de extremidade no Azure.
- O volume de usuários que se conectam dessas regiões.
- As regiões do Azure às quais eles estão sendo roteados.
- Suas experiências de latência para essas regiões do Azure.

Essas informações estão disponíveis para você consumir por meio de sobreposição de mapa geográfico e exibições tabulares no portal, além de estarem disponíveis como dados brutos para download.

### <a name="how-can-i-benefit-from-using-traffic-view"></a>Como posso me beneficiar do uso da Exibição do Tráfego?

A Exibição do Tráfego fornece a visão geral do tráfego que seus perfis do Gerenciador de Tráfego recebem. Ela pode ser usada principalmente para entender de onde sua base de usuários se conecta e também qual é a experiência de latência média desses usuários. Você pode usar essas informações para localizar áreas em que precisa se concentrar, por exemplo, expandindo sua superfície do Azure para uma região que possa atender esses usuários com uma latência mais baixa. Outras informações que você pode extrair usando a Exibição do Tráfego são os padrões de tráfego em diferentes regiões, o que pode ajudá-lo a tomar decisões sobre aumentar ou diminuir o invent. nessas regiões.

### <a name="how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor"></a>Por que a Exibição do Tráfego é diferente das métricas do Gerenciador de Tráfego disponíveis por meio do Azure Monitor?

O Azure Monitor pode ser usado para entender, em um nível de agregação, o tráfego recebido pelo seu perfil e seus pontos de extremidade. Ele também permite que você acompanhe o status de integridade dos pontos de extremidade expondo os resultados da verificação de integridade. Quando você precisa ir além disso e entender a experiência do usuário final ao se conectar ao Azure em um nível regional, é possível usar a Exibição do Tráfego.

### <a name="does-traffic-view-use-edns-client-subnet-information"></a>A Exibição do Tráfego usa informações de sub-rede de cliente EDNS?

As consultas DNS atendidas pelo Gerenciador de Tráfego do Azure consideram que as informações de ECS aumentam a precisão do roteamento. Mas, ao criar o conjunto de dados que mostra de onde os usuários estão se conectando, a Exibição de Tráfego está usando somente o endereço IP do resolvedor de DNS.

### <a name="how-many-days-of-data-does-traffic-view-use"></a>Quantos dias de dados a Exibição do Tráfego usa?

A Exibição do Tráfego cria sua saída processando os dados dos sete dias que precedem o dia anterior de sua exibição. Esse é um período móvel e os dados mais recentes serão usados sempre que você a visitar.

### <a name="how-does-traffic-view-handle-external-endpoints"></a>Como a Exibição do Tráfego lida com pontos de extremidade externos?

Quando você usa pontos de extremidade externos, hospedados fora das regiões do Azure em um perfil do Gerenciador de Tráfego, você pode escolher que eles sejam mapeados para uma região do Azure que seja um proxy para suas características de latência (isso na verdade é necessário quando você usa o método de roteamento de desempenho). Se houver esse mapeamento de região do Azure, as métricas de latência dessa região do Azure serão usadas para criar a saída da Exibição de Tráfego. Se não houver nenhuma região do Azure especificada, as informações de latência ficarão vazias nos dados desses pontos de extremidade externos.

### <a name="do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription"></a>É necessário habilitar a Exibição do Tráfego para cada perfil na minha assinatura?

Durante o período de versão prévia, a Exibição de Tráfego foi habilitada em nível de assinatura. Como parte das melhorias que fizemos antes da disponibilidade geral, agora você pode habilitar a Exibição de Tráfego em nível de perfil, permitindo que você habilite esse recurso de forma mais granular. Por padrão, a Exibição de Tráfego será desabilitada para um perfil.

>[!NOTE]
>Se você habilitou a Exibição de Tráfego em nível de assinatura durante o período de versão prévia, você precisará habilitá-la novamente para cada perfil da assinatura.
 
### <a name="how-can-i-turn-off-traffic-view"></a>Como posso desligar a Exibição do Tráfego?

Você pode desativar a Exibição de Tráfego para qualquer perfil usando o Portal ou a API REST. 

### <a name="how-does-traffic-view-billing-work"></a>Como funciona a cobrança da Exibição do Tráfego?

O preço da Exibição do Tráfego é baseado no número de pontos de dados usados para criar a saída. Atualmente, o único tipo de dados com suporte são as consultas que o seu perfil recebe. Além disso, você será cobrado somente pelo processamento que foi feito quando a Exibição do Tráfego estava habilitada. Isso significa que, se você habilitar a Exibição do Tráfego em um certo período do mês e desabilitá-la em outros períodos, somente os pontos de dados processados enquanto o recurso estava habilitado serão cobrados.

## <a name="traffic-manager-endpoints"></a>Pontos de extremidade do Gerenciador de Tráfego

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>Posso usar o Gerenciador de Tráfego com pontos de extremidade de várias assinaturas?

Não é possível usar pontos de extremidade de várias assinaturas com Aplicativos Web do Azure. Os Aplicativos Web do Azure exigem que qualquer nome de domínio personalizado usado com Aplicativos Web seja usado somente em uma única assinatura. Não é possível usar Aplicativos Web de várias assinaturas com o mesmo nome de domínio.

Para outros tipos de ponto de extremidade, é possível usar o Gerenciador de Tráfego com pontos de extremidade de mais de uma assinatura. No Gerenciador de Recursos, os pontos de extremidade de qualquer assinatura podem ser adicionados ao Gerenciador de Tráfego, desde que a pessoa que configura o perfil do Gerenciador de Tráfego tenha o acesso de leitura ao ponto de extremidade. Essas permissões podem ser concedidas usando o [RBAC (controle de acesso baseado em função) do Azure Resource Manager](../role-based-access-control/role-assignments-portal.md).

### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>Posso usar o Gerenciador de Tráfego com os slots de “Preparo” do Serviço de Nuvem?

Sim. Os slots de “preparo” do Serviço de Nuvem podem ser configurados no Gerenciador de Tráfego como pontos de extremidade Externos. Verificações de integridade ainda são cobradas à taxa de Pontos de Extremidade do Azure.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>O Gerenciador de Tráfego dá suporte aos pontos de extremidade IPv6?

No momento, o Gerenciador de Tráfego não oferece servidores de nome endereçáveis por IPv6. No entanto, o Gerenciador de Tráfego ainda pode ser usado por clientes IPv6 que se conectem a pontos de extremidade IPv6. Um cliente não faz solicitações DNS diretamente ao Gerenciador de tráfego. Em vez disso, o cliente usa um serviço DNS recursivo. Um cliente somente IPv6 envia solicitações para o serviço DNS recursivo via IPv6. Então o serviço recursivo deve conseguir contatar os servidores de nome do Gerenciador de Tráfego usando IPv4.

O Gerenciador de Tráfego responde com o nome DNS ou endereço IP do ponto de extremidade. Para dar suporte a um ponto de extremidade IPv6, há duas opções. Você pode adicionar o ponto de extremidade como um nome DNS que possui um registro AAAA associado e o Gerenciador de Tráfego verificará esse ponto de extremidade e o retornará como um tipo de registro CNAME na resposta da consulta. Também é possível adicionar esse ponto de extremidade diretamente usando o endereço IPv6 e o Gerenciador de Tráfego retornará um registro do tipo AAAA na resposta da consulta.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>Posso usar o Gerenciador de Tráfego com mais de um Aplicativo Web na mesma região?

Normalmente, o Gerenciador de Tráfego é usado para direcionar o tráfego para os aplicativos implantados em regiões diferentes. No entanto, ele também pode ser usado onde um aplicativo tem mais de uma implantação na mesma região. Os pontos de extremidade do Gerenciador de Tráfego do Azure não permitem adicionar mais de um ponto de extremidade do Aplicativo Web da mesma região do Azure ao mesmo perfil do Gerenciador de Tráfego.

### <a name="how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group"></a>Como faço para mover os pontos de extremidade do Azure do meu perfil do Gerenciador de Tráfego para um grupo de recursos diferente?

Os pontos de extremidade do Azure associados a um perfil do Gerenciador de Tráfego são rastreados com suas IDs de recurso. Quando um recurso do Azure sendo usado como um ponto de extremidade (por exemplo, IP Público, Serviço de Nuvem Clássico, WebApp ou outro perfil do Gerenciador de Tráfego usado de forma aninhada) é movido para um grupo de recursos diferentes, sua ID de recurso é alterada. Neste cenário, no momento, você deve atualizar o perfil do Gerenciador de Tráfego primeiro excluindo e então adicionando novamente os pontos de extremidade ao perfil.

## <a name="traffic-manager-endpoint-monitoring"></a>Monitoramento de ponto de extremidade do Gerenciador de Tráfego

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>O Gerenciador de Tráfego é resistente a falhas de região do Azure?

O Gerenciador de Tráfego é um componente fundamental da entrega de aplicativos altamente disponíveis no Azure.
Para entregar alta disponibilidade, o Gerenciador de Tráfego deve ter um nível muito alto de disponibilidade e ser resiliente a falhas de região.

Por design, os componentes do Gerenciador de Tráfego são resilientes a uma falha completa de qualquer região do Azure. Essa resiliência se aplica a todos os componentes do Gerenciador de Tráfego: os servidores de nome DNS, a API, a camada de armazenamento e o serviço de monitoramento de ponto de extremidade.

No caso improvável de uma falha de uma região do Azure inteira, Gerenciador de Tráfego deve continuar a funcionar normalmente. Os aplicativos implantados em várias regiões do Azure podem contar com o Gerenciador de Tráfego para direcionar o tráfego para uma instância disponível de seu aplicativo.

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>Como a escolha da localização do grupo de recursos afeta o Gerenciador de Tráfego?

O Gerenciador de Tráfego é um serviço global único. Não é regional. A escolha do local do grupo de recursos não faz diferença para perfis do Gerenciador de Tráfego implantados nesse grupo de recursos.

O Azure Resource Manager exige que todos os grupos de recursos especifiquem uma localização, que determina a localização padrão para os recursos implantados nesse grupo de recursos. Quando você cria um perfil do Gerenciador de Tráfego, ele é criado em um grupo de recursos. Todos os perfis do Gerenciador de Tráfego usam **global** como a localização, substituindo o padrão do grupo de recursos.

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>Como determinar a integridade atual de cada ponto de extremidade?

O status de monitoramento atual de cada ponto de extremidade, bem como o perfil geral, é exibido no portal do Azure. Essas informações também estão disponíveis por meio da [API REST](https://msdn.microsoft.com/library/azure/mt163667.aspx) do Traffic Monitor, dos [cmdlets do PowerShell](https://docs.microsoft.com/powershell/module/az.trafficmanager) e da [CLI do Azure entre plataformas](../cli-install-nodejs.md).

Você também pode usar o Azure Monitor para controlar a integridade de seus pontos de extremidade e ver uma representação visual deles. Para obter mais informações sobre como usar o Azure Monitor, consulte a [Documentação de monitoramento do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

### <a name="can-i-monitor-https-endpoints"></a>Posso monitorar os pontos de extremidade HTTPS?

Sim. O Gerenciador de Tráfego oferece suporte à investigação por HTTPS. Configure **HTTPS** como o protocolo na configuração de monitoramento.

O Gerenciador de Tráfego não pode fornecer nenhuma validação de certificado, incluindo:

* Certificados no lado do servidor não estão validados
* Os certificados do lado do servidor SNI não são validados
* Não há suporte para certificados de cliente

### <a name="do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint"></a>Eu devo usar um endereço IP ou um nome DNS ao adicionar um ponto de extremidade?

O Gerenciador de Tráfego dá suporte para adição de pontos de extremidade usando três maneiras de encaminhá-los – como um nome DNS, como um endereço IPv4 e como um endereço IPv6. Se o ponto de extremidade for adicionado como um endereço IPv4 ou IPv6, a resposta da consulta será do tipo de registro A ou AAAA, respectivamente. Se o ponto de extremidade foi adicionado como um nome DNS, a resposta da consulta será do tipo de registro CNAME. A adição de pontos de extremidade como endereço IPv4 ou IPv6 será permitida apenas se o ponto de extremidade for do tipo **Externo**.
Todos os métodos de roteamento e configurações de monitoramento têm suporte pelos três tipos de endereçamento de ponto de extremidade.

### <a name="what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint"></a>Quais tipos de endereços IP eu posso usar ao adicionar um ponto de extremidade?

O Gerenciador de Tráfego permite usar endereços IPv4 ou IPv6 para especificar pontos de extremidade. Há algumas restrições listadas abaixo:

- Endereços que correspondem a espaços de endereços IP privados reservados não são permitidos. Esses endereços incluem aqueles chamados em RFC 1918, RFC 6890, RFC 5737, RFC 3068, RFC 2544 e RFC 5771
- O endereço não deve conter nenhum número de porta (você pode especificar as portas a serem usadas nas configurações do perfil)
- Nenhum ponto de extremidade no mesmo perfil pode ter o mesmo endereço IP de destino

### <a name="can-i-use-different-endpoint-addressing-types-within-a-single-profile"></a>É possível usar diferentes tipos de endereçamento de ponto de extremidade em um único perfil?

Não, o Gerenciador de Tráfego não permite combinar tipos de endereçamento de ponto de extremidade em um perfil, exceto no caso de um perfil com o tipo de roteamento de Múltiplos Valores em que é possível combinar tipos de endereçamento IPv4 e IPv6

### <a name="what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints"></a>O que acontece quando o tipo de registro de uma consulta recebida é diferente do tipo de registro associado ao tipo de endereçamento dos pontos de extremidade?

Quando uma consulta é recebida em um perfil, o Gerenciador de Tráfego primeiro localiza o ponto de extremidade que precisa ser retornado conforme o método de roteamento especificado e o status de integridade dos pontos de extremidade. Em seguida, analisa o tipo de registro solicitado na consulta de entrada e o tipo de registro associado ao ponto de extremidade antes de retornar uma resposta com base na tabela abaixo.

Para perfis com qualquer método de roteamento que não seja de Múltiplos Valores:

|Solicitação de consulta de entrada|    Tipo de ponto de extremidade|  Resposta fornecida|
|--|--|--|
|QUALQUER |  A / AAAA / CNAME |  Ponto de extremidade de destino| 
|A |    A / CNAME | Ponto de extremidade de destino|
|A |    AAAA |  NODATA |
|AAAA | AAAA / CNAME |  Ponto de extremidade de destino|
|AAAA | A | NODATA |
|CNAME |    CNAME | Ponto de extremidade de destino|
|CNAME  |A / AAAA | NODATA |
|

Para perfis com o método de roteamento definido como de Múltiplos Valores:

|Solicitação de consulta de entrada|    Tipo de ponto de extremidade | Resposta fornecida|
|--|--|--|
|QUALQUER |  Combinação de A e AAAA | Pontos de extremidade de destino|
|A |    Combinação de A e AAAA | Somente pontos de extremidade de destino do tipo A|
|AAAA   |Combinação de A e AAAA|     Somente pontos de extremidade de destino do tipo AAAA|
|CNAME |    Combinação de A e AAAA | NODATA |

### <a name="can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile"></a>É possível usar um perfil com pontos de extremidade endereçados IPv4/IPv6 em um perfil aninhado?

Sim, é possível, com a exceção de que um perfil do tipo Múltiplos Valores não pode ser um perfil pai em um conjunto de perfis aninhados.

### <a name="i-stopped-an-web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this"></a>Parei um ponto de extremidade do aplicativo Web no meu perfil do Gerenciador de tráfego, mas não estou recebendo nenhum tráfego mesmo depois de reiniciá-lo. Como posso corrigir isso?

Quando um ponto de extremidade de aplicativo Web do Azure é interrompido, o Gerenciador de tráfego para de verificar sua integridade e reinicia as verificações de integridade somente após detectar que o ponto de extremidade foi reiniciado. Para evitar esse atraso, desabilite e reabilite esse ponto de extremidade no perfil do Gerenciador de Tráfego depois de reiniciar o ponto de extremidade.

### <a name="can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https"></a>Posso usar o Gerenciador de Tráfego mesmo se o aplicativo não tiver suporte para HTTP ou HTTPS?

Sim. É possível especificar o TCP como protocolo de monitoramento e o Gerenciador de Tráfego pode iniciar uma conexão TCP e aguardar uma resposta do ponto de extremidade. Se o ponto de extremidade responder à solicitação de conexão com uma resposta para estabelecer a conexão, dentro do período de tempo limite, esse ponto de extremidade será marcado como íntegro.

### <a name="what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring"></a>Quais respostas específicas são necessárias do ponto de extremidade ao usar o monitoramento de TCP?

Quando o monitoramento de TCP é usado, o Gerenciador de Tráfego inicia um handshake TCP de três vias enviando uma solicitação SYN ao ponto de extremidade na porta especificada. Em seguida, ele aguarda um período de tempo (como especificado nas configurações de tempo limite) por uma resposta do ponto de extremidade. Se o ponto de extremidade responde à solicitação de SYN com uma resposta de SYN-ACK dentro do período de tempo limite especificado nas configurações de monitoramento, então esse ponto de extremidade será considerado íntegro. Se a resposta de SYN-ACK for recebida, o Gerenciador de Tráfego redefinirá a conexão ao responder novamente com um RST.

### <a name="how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint"></a>Com qual velocidade o Gerenciador de Tráfego move meus usuários para fora de um ponto de extremidade não íntegro?

O Gerenciador de Tráfego fornece várias configurações que podem ajudar a controlar o comportamento de failover do seu perfil do Gerenciador de Tráfego da seguinte maneira:

- você pode especificar que o Gerenciador de Tráfego investigue os pontos de extremidade com mais frequência ao configurando o intervalo de investigação em 10 segundos. Isso garante que qualquer ponto de extremidade que esteja ficando não íntegro possa ser detectado assim que possível. 
- você pode especificar quanto tempo deve aguardar antes que uma solicitação de verificação de integridade atinja o tempo limite (valor de tempo limite mínimo é 5s).
- você pode especificar quantas falhas podem ocorrer antes de o ponto de extremidade ser marcado como não íntegro. Esse valor pode ser baixo como 0. Nesse caso, o ponto de extremidade será marcado como não íntegro assim que a primeira verificação de integridade falhar. No entanto, usar o valor mínimo de 0 para o número tolerado de falhas pode levar a pontos de extremidade que estão sendo retirados da rotação devido a problemas transitórios que podem ocorrer na hora da investigação.
- você pode especificar o tempo de vida (TTL) para a resposta DNS tão baixo quanto 0. Essa ação significa que os resolvedores DNS não podem armazenar a resposta em cache e cada nova consulta obtém uma resposta que incorpora as informações de integridade mais atualizadas que o Gerenciador de Tráfego tem.

Ao usar essas configurações, o Gerenciador de Tráfego pode fornecer failovers de até 10 segundos depois que um ponto de extremidade ficar não íntegro e uma consulta DNS for feita com base no perfil correspondente.

### <a name="how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile"></a>Como especificar diferentes configurações de monitoramento para diferentes pontos de extremidade em um perfil?

As configurações de monitoramento do Gerenciador de Tráfego estão em um nível por perfil. Se você precisar usar uma configuração de monitoramento diferente para somente um ponto de extremidade, isso será possível ao ter esse ponto de extremidade como um [perfil aninhado](traffic-manager-nested-profiles.md), cujas configurações de monitoramento são diferentes do perfil pai.

### <a name="how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints"></a>Como posso atribuir cabeçalhos HTTP às verificações de saúde do Gerenciador de Tráfego aos meus pontos de extremidade?

O Gerenciador de Tráfego permite especificar cabeçalhos personalizados nas verificações de integridade de HTTP(S) que ele inicia nos pontos de extremidade. Se você quiser especificar um cabeçalho personalizado, poderá fazer isso no nível do perfil (aplicável a todos os pontos de extremidade) ou especificá-lo no nível do ponto de extremidade. Se um cabeçalho for definido em ambos os níveis, o especificado no nível do ponto de extremidade substituirá o nível de perfil um.
Um caso de uso comum para isso é especificar cabeçalhos de host para que as solicitações do Gerenciador de Tráfego sejam roteadas corretamente para um ponto de extremidade hospedado em um ambiente de vários locatários. Outro caso de uso disso é identificar as solicitações do Gerenciador de Tráfego dos logs de solicitações HTTP(S) de um ponto de extremidade

### <a name="what-host-header-do-endpoint-health-checks-use"></a>Qual cabeçalho host as verificações de integridade do ponto de extremidade usam?

Se nenhuma configuração de cabeçalho de host personalizado for fornecida, o cabeçalho de host usado pelo Gerenciador de Tráfego será o nome DNS do destino do ponto de extremidade configurado no perfil, se disponível.

### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>Quais são os endereços IP dos quais as verificações de integridade se originam?

Clique em [aqui](https://azuretrafficmanagerdata.blob.core.windows.net/probes/azure/probe-ip-ranges.json) para exibir o arquivo JSON que lista os endereços IP dos quais as verificações de integridade do Gerenciador de Tráfego podem se originar. Examine os IPs listados no arquivo JSON para garantir que as conexões de entrada desses endereços IP sejam permitidas nos pontos de extremidades, possibilitando a verificação do status de integridade.

### <a name="how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager"></a>Quantas verificações de integridade no meu ponto de extremidade posso esperar do Gerenciador de Tráfego?

O número de verificações de integridade do Gerenciador de Tráfego que atingem seu ponto de extremidade depende do seguinte:

- o valor que você definiu para o intervalo de monitoramento (intervalo menor significa que mais solicitações chegaram no ponto de extremidade em um determinado período de tempo).
- o número de locais dos quais as verificações de integridade se originam (os endereços IP dos quais você pode esperar essas verificações estão listados nas perguntas frequentes anteriores).

### <a name="how-can-i-get-notified-if-one-of-my-endpoints-goes-down"></a>Como posso receber uma notificação se um dos meus pontos de extremidade ficar inativo?

Um das métricas fornecidas pelo Gerenciador de Tráfego é o status de integridade dos pontos de extremidade em um perfil. Veja isso como uma agregação de todos os pontos de extremidade dentro de um perfil (por exemplo, 75% de seus pontos de extremidade estão íntegros), ou por nível de ponto de extremidade. As métricas do Gerenciador de Tráfego são expostas por meio do Azure Monitor, e você pode usar seus [recursos de alerta](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) para receber notificações quando há uma alteração no status de integridade de seu ponto de extremidade. Para obter mais detalhes, confira [Métricas e alertas do Gerenciador de Tráfego](traffic-manager-metrics-alerts.md).  

## <a name="traffic-manager-nested-profiles"></a>Perfis aninhados do Gerenciador de Tráfego

### <a name="how-do-i-configure-nested-profiles"></a>Como posso configurar perfis aninhados?

Os perfis aninhados do Gerenciador de Tráfego podem ser configurados usando o Azure Resource Manager e as APIs REST clássicas do Azure, cmdlets do Azure PowerShell e comandos da CLI do Azure de plataforma cruzada. Também há suporte para eles no novo portal do Azure.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>A quantas camadas de aninhamento o Gerenciador de Tráfego dá suporte?

Você pode aninhar perfis em até 10 níveis de profundidade. “Loops” não são permitidos.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>Posso combinar outros tipos de ponto de extremidade com perfis filho aninhados no mesmo perfil do Gerenciador de Tráfego?

Sim. Não há nenhuma restrição sobre como combinar os pontos de extremidade de diferentes tipos em um perfil.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>Como o modelo de cobrança se aplica a perfis aninhados?

Há não impacto negativo sobre os preços ao usar perfis aninhados.

A cobrança do Gerenciador de Tráfego tem dois componentes: verificações de integridade do ponto de extremidade e milhões de consultas DNS

* Verificações de integridade do ponto de extremidade: Não há nenhum encargo para um perfil filho quando configurado como um ponto de extremidade em um perfil pai. O monitoramento dos pontos de extremidade no perfil filho é cobrado como de costume.
* Consultas DNS: Cada consulta é contada apenas uma vez. Uma consulta em um perfil pai que retorna um ponto de extremidade de um perfil filho é contada apenas no perfil pai.

Para obter detalhes completos, consulte a [página de preços do Gerenciador de Tráfego](https://azure.microsoft.com/pricing/details/traffic-manager/).

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>Há impacto no desempenho para perfis aninhados?

Nº Não há nenhum impacto no desempenho ao usar perfis aninhados.

Os servidores de nomes do Gerenciador de Tráfego atravessam a hierarquia de perfil internamente durante o processamento de cada consulta DNS. Uma consulta DNS a um perfil pai pode receber uma resposta DNS com um ponto de extremidade de um perfil filho. Um único registro CNAME é usado se você está usando um único perfil ou perfis aninhados. Não é necessário criar um registro CNAME para cada perfil na hierarquia.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Como o Gerenciador de Tráfego calcula a integridade de um ponto de extremidade aninhado em um perfil pai?

O perfil pai não executa verificações de integridade no filho diretamente. Em vez disso, a integridade dos pontos de extremidade do perfil filho é usada para calcular a integridade geral do perfil filho. Essas informações são propagadas até a hierarquia do perfil aninhado para determinar a integridade do ponto de extremidade aninhado. O perfil pai usa essa integridade agregada para determinar se o tráfego pode ser direcionado para o filho.

A tabela a seguir descreve o comportamento das verificações de integridade do Gerenciador de Tráfego de um ponto de extremidade aninhado.

| Status do Monitor de perfil filho | Status do monitor de ponto de extremidade pai | Observações |
| --- | --- | --- |
| Desabilitado. O perfil filho foi desabilitado. |Parado |O estado do ponto de extremidade pai é Parado, não Desabilitado. O estado Desabilitado é reservado para indicar que você desabilitou o ponto de extremidade no perfil pai. |
| Degradado. Pelo menos um ponto de extremidade do perfil filho está no estado Degradado. |Online: o número de pontos de extremidade Online no perfil filho é pelo menos o valor de MinChildEndpoints.<BR>CheckingEndpoint: o número de pontos de extremidade Online mais CheckingEndpoint no perfil filho é pelo menos o valor de MinChildEndpoints.<BR>Degradado: caso contrário. |O tráfego é roteado para um ponto de extremidade do status CheckingEndpoint. Se MinChildEndpoints estiver definido com um valor muito alto, o ponto de extremidade estará sempre degradado. |
| Online. Pelo menos, um ponto de extremidade do perfil filho está em um estado Online. Nenhum ponto de extremidade está no estado Degradado. |Veja acima. | |
| CheckingEndpoints. Pelo menos, um ponto de extremidade do perfil filho é um 'CheckingEndpoint'. Nenhum ponto de extremidade está ‘Online’ ou ‘Degradado’ |Mesmo que acima. | |
| Inativo. Todos os pontos de extremidade de perfil filho estão com status Desabilitado ou Parado ou esse é um perfil que não tem nenhum ponto de extremidade. |Parado | |

## <a name="next-steps"></a>Próximas etapas:

- Saiba mais sobre o [failover automático e monitoramento do ponto de extremidade](../traffic-manager/traffic-manager-monitoring.md)do Gerenciador de Tráfego.
- Saiba mais sobre os [métodos de roteamento de tráfego](../traffic-manager/traffic-manager-routing-methods.md)do Gerenciador de Tráfego.
