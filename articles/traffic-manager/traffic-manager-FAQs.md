---
title: "Gerenciador de Tráfego do Azure — perguntas frequentes | Microsoft Docs"
description: "Este artigo fornece respostas a perguntas frequentes sobre o Gerenciador de Tráfego"
services: traffic-manager
documentationcenter: 
author: KumudD
manager: timlt
editor: 
ms.assetid: 75d5ff9a-f4b9-4b05-af32-700e7bdfea5a
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2017
ms.author: kumud
ms.openlocfilehash: 868d3ee973a03aca82c9775371d9832b7a063e9a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="traffic-manager-frequently-asked-questions-faq"></a>Perguntas frequentes sobre o Gerenciador de Tráfego

## <a name="traffic-manager-basics"></a>Noções básicas sobre o Gerenciador de Tráfego

### <a name="what-ip-address-does-traffic-manager-use"></a>Qual endereço IP o Gerenciador de Tráfego usa?

Conforme explicado em [Como funciona o Gerenciador de Tráfego](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), o Gerenciador de Tráfego funciona no nível do DNS. Ele envia as respostas DNS para direcionar os clientes ao ponto de extremidade de serviço apropriado. Em seguida, os clientes se conectam diretamente ao ponto de extremidade de serviço, não pelo Gerenciador de Tráfego.

Portanto, o Gerenciador de Tráfego não fornece um ponto de extremidade ou o endereço IP para que os clientes se conectem. Se você desejar um endereço IP estático para o serviço, ele deverá ser configurado no serviço, não no Gerenciador de Tráfego.

### <a name="does-traffic-manager-support-sticky-sessions"></a>O Gerenciador de Tráfego dá suporte a sessões “temporárias”?

Conforme explicado em [Como funciona o Gerenciador de Tráfego](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), o Gerenciador de Tráfego funciona no nível do DNS. Ele usa as respostas DNS para direcionar os clientes ao ponto de extremidade de serviço apropriado. Os clientes se conectam diretamente ao ponto de extremidade de serviço, não pelo Gerenciador de Tráfego. Portanto, o Gerenciador de Tráfego não vê o tráfego HTTP entre o cliente e o servidor.

Além disso, o endereço IP de origem da consulta DNS recebida pelo Gerenciador de Tráfego pertence ao serviço DNS recursivo, não ao cliente. Portanto, o Gerenciador de Tráfego não consegue acompanhar clientes individuais e não pode implementar sessões “temporárias”. Essa limitação é comum a todos os sistemas de gerenciamento de tráfego baseados em DNS e não é específica ao Gerenciador de Tráfego.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Por que vejo um erro de HTTP ao usar o Gerenciador de Tráfego?

Conforme explicado em [Como funciona o Gerenciador de Tráfego](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), o Gerenciador de Tráfego funciona no nível do DNS. Ele usa as respostas DNS para direcionar os clientes ao ponto de extremidade de serviço apropriado. Em seguida, os clientes se conectam diretamente ao ponto de extremidade de serviço, não pelo Gerenciador de Tráfego. O Gerenciador de Tráfego não vê o tráfego HTTP entre o cliente e o servidor. Portanto, qualquer erro HTTP visto deve ser proveniente do aplicativo. Para que o cliente se conecte ao aplicativo, todas as etapas de resolução DNS são concluídas. Isso inclui qualquer interação que o Gerenciador de Tráfego tem no fluxo de tráfego do aplicativo.

Portanto, as investigações adicionais devem se concentrar no aplicativo.

O cabeçalho de host HTTP enviado do navegador do cliente é a fonte mais comum de problemas. Verifique se o aplicativo está configurado para aceitar o cabeçalho de host correto para o nome de domínio que está sendo usado. Para pontos de extremidade que usam o Serviço de Aplicativo do Azure, consulte [Configurando um nome de domínio personalizado para um aplicativo Web no Serviço de Aplicativo do Azure usando o Gerenciador de Tráfego](../app-service/web-sites-traffic-manager-custom-domain-name.md).

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>Qual é o impacto no desempenho de usar o Gerenciador de Tráfego?

Conforme explicado em [Como funciona o Gerenciador de Tráfego](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), o Gerenciador de Tráfego funciona no nível do DNS. Como os clientes se conectam diretamente ao pontos de extremidade de seu serviço, não há qualquer impacto no desempenho ao usar o Gerenciador de Tráfego quando a conexão é estabelecida.

Como o Gerenciador de Tráfego é integrado aos aplicativos no nível de DNS, ele requer uma pesquisa de DNS adicional para ser inserido na cadeia de resolução de DNS. O impacto do Gerenciador de Tráfego no tempo de resolução de DNS é mínimo. O Gerenciador de Tráfego usa uma rede global de servidores de nomes, bem como a rede [anycast](https://en.wikipedia.org/wiki/Anycast) para garantir que as consultas DNS são sempre encaminhadas para o servidor de nomes mais próximo disponível. Além disso, o armazenamento em cache de respostas DNS significa que a latência DNS adicional gerada pelo uso do Gerenciador de Tráfego só se aplica a uma fração de sessões.

O método por Desempenho encaminha o tráfego para o ponto de extremidade mais próximo disponível. O resultado final é que o impacto de desempenho geral associado a esse método deve ser mínimo. Qualquer aumento na latência DNS deve ser deslocado pela menor latência de rede para o ponto de extremidade.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Quais protocolos de aplicativo posso usar com o Gerenciador de Tráfego?

Conforme explicado em [Como funciona o Gerenciador de Tráfego](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), o Gerenciador de Tráfego funciona no nível do DNS. Após a conclusão da pesquisa de DNS, os clientes se conectam diretamente ao ponto de extremidade do aplicativo, não pelo Gerenciador de Tráfego. Portanto, a conexão pode usar qualquer protocolo de aplicativo. Se você selecionar TCP como protocolo de monitoramento, o monitoramento de integridade do ponto de extremidade do Gerenciador de Tráfego poderá ser feito sem usar qualquer protocolo de aplicativo. Se você optar por ter a integridade verificada usando um protocolo de aplicativo, o ponto de extremidade precisará ser capaz de responder às solicitações HTTP ou HTTPS GET.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>Posso usar o Gerenciador de Tráfego com um nome de domínio raiz?

Nº Os padrões de DNS não permitem que CNAMEs coexistam com outros registros DNS do mesmo nome. O apex (ou raiz) de uma zona DNS sempre contém dois registros DNS já existentes: a SOA e os registros NS autoritativos. Isso significa que um registro CNAME não pode ser criado no apex da zona sem violar os padrões do DNS.

O Gerenciador de Tráfego exige um registro CNAME DNS para mapear o nome DNS intuitivo. Por exemplo, você mapeia www.contoso.com para o nome DNS do perfil do Gerenciador de Tráfego contoso.trafficmanager.net. Além disso, o perfil do Gerenciador de Tráfego retorna um segundo DNS CNAME para indicar a qual ponto de extremidade o cliente deve se conectar.

Para solucionar esse problema, recomendamos o uso de um redirecionamento HTTP para direcionar o tráfego do nome de domínio raiz para uma URL diferente, que então poderá usar o Gerenciador de Tráfego. Por exemplo, o domínio raiz “contoso.com” pode redirecionar os usuários para o CNAME “www.contoso.com” que aponta para o nome DNS do Gerenciador de Tráfego.

O suporte completo para domínios naked no Gerenciador de Tráfego é controlado em nossa lista de pendências de recurso. Você pode registrar seu suporte para essa solicitação de recurso [votando em nosso site de comentários da comunidade](https://feedback.azure.com/forums/217313-networking/suggestions/5485350-support-apex-naked-domains-more-seamlessly).

### <a name="does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries"></a>O Gerenciador de Tráfego considera o endereço de sub-rede do cliente ao manipular consultas DNS? 
Sim, além do endereço IP de origem da consulta DNS que ele recebe (que geralmente é o endereço IP do resolvedor de DNS), ao realizar pesquisas para métodos de roteamento geográfico e de desempenho, o Gerenciador de Tráfego também vai considerar o endereço de sub-rede do cliente se ele estiver incluído na consulta quando o resolvedor fizer a solicitação em nome do usuário final.  
Especificamente, a [RFC 7871 – sub-rede do cliente nas consultas DNS](https://tools.ietf.org/html/rfc7871) que fornece um [Mecanismo de Extensão para DNS (EDNS0)](https://tools.ietf.org/html/rfc2671) que pode passar o endereço de sub-rede do cliente dos resolvedores com suporte.

### <a name="what-is-dns-ttl-and-how-does-it-impact-my-users"></a>O que é o TTL do DNS e como ele afeta os meus usuários?

Quando uma consulta DNS chega no Gerenciador de Tráfego, ela define um valor na resposta chamado tempo de vida (TTL). Esse valor, cuja unidade é em segundos, indica ao downstream dos resolvedores DNS o tempo necessário para armazenar em cache essa resposta. Enquanto não há garantia de que os resolvedores DNS armazenem esse resultado em cache, este armazenamento permite que respondam a todas as consultas subsequentes além do cache, em vez de irem para servidores DNS do Gerenciador de Tráfego. Isso afeta as respostas da seguinte maneira:
- um TTL maior reduz o número de consultas que chegam aos servidores DNS do Gerenciador de Tráfego, o que pode reduzir o custo para um cliente, pois o número de consultas apresentadas é um uso faturável.
- um TTL maior pode reduzir potencialmente o tempo que leva para realizar uma pesquisa de DNS.
- um TTL maior também significa que os dados não refletem as últimas informações sobre integridade que o Gerenciador de Tráfego obteve por meio de seus agentes de investigação.

### <a name="how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses"></a>Como posso definir o nível (alto ou baixo) do TTL para respostas do Gerenciador de Tráfego?

Você pode definir, em um nível por perfil, o TTL do DNS tão baixo quanto 0 segundos e tão alto quanto 2.147.483.647 segundos (o intervalo máximo em conformidade com [RFC-1035](https://www.ietf.org/rfc/rfc1035.txt )). Um TTL de 0 significa que os resolvedores DNS de downstream não armazenam em cache respostas de consultas e que todas as consultas devem alcançar os servidores DNS do Gerenciador de Tráfego para a resolução.

## <a name="traffic-manager-geographic-traffic-routing-method"></a>Método de roteamento de tráfego geográfico do Gerenciador de Tráfego

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>Quais são alguns casos de uso em que o roteamento geográfico é útil? 
O tipo de roteamento geográfico pode ser usado em qualquer cenário em que um cliente do Azure precise diferenciar seus usuários com base nas regiões geográficas. Por exemplo, ao usar o método de roteamento de tráfego geográfico, você proporciona aos usuários de regiões específicas uma experiência de usuário diferente daquelas de outras regiões. Outro exemplo é estar em conformidade com as normas soberanas de dados locais que exigem que os usuários de uma região específica sejam atendidos somente pelos pontos de extremidade dessa região.

### <a name="what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing"></a>Quais são as regiões com suporte do Gerenciador de Tráfego para roteamento geográfico? 
A hierarquia de país/região usada pelo Gerenciador de Tráfego pode ser encontrada [aqui](traffic-manager-geographic-regions.md). Embora essa página esteja sempre atualizada com todas as alterações, você também pode recuperar, de modo programático, as mesmas informações usando a [API REST do Gerenciador de Tráfego do Azure](https://docs.microsoft.com/rest/api/trafficmanager/). 

### <a name="how-does-traffic-manager-determine-where-a-user-is-querying-from"></a>Como o Gerenciador de Tráfego determina de que região um usuário está fazendo uma consulta? 
O Gerenciador de Tráfego analisa o IP de origem da consulta (provavelmente é um resolvedor DNS local fazendo a consulta em nome do usuário) e usa um IP interno do mapa da região para determinar o local. Esse mapa é atualizado continuamente para levar em conta as alterações na Internet. 

### <a name="is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case"></a>Há garantia de que o Gerenciador de Tráfego possa determinar o local geográfico exato do usuário em todos os casos corretamente?
Não, o Gerenciador de Tráfego não pode assegurar que a região geográfica que podemos deduzir do endereço IP de origem de uma consulta DNS sempre corresponderá ao local do usuário, pelos seguintes motivos: 

- Primeiro, conforme descrito nas perguntas frequentes anteriores, o endereço IP de origem que vemos é aquele de um resolvedor DNS fazendo a pesquisa em nome do usuário. Embora a localização geográfica do resolvedor DNS seja um bom proxy para a localização geográfica do usuário, ela também pode ser diferente dependendo da superfície de serviço de resolvedor DNS e do serviço de resolvedor DNS específico que um cliente tiver optado por usar. Por exemplo, um cliente localizado na Malásia poderia especificar no uso de configurações do seu dispositivo um serviço de resolução DNS cujo servidor DNS em Cingapura pode selecionado para tratar as resoluções de consulta desse usuário/dispositivo. Nesse caso, o Gerenciador de Tráfego pode ver somente o endereço IP do resolvedor que corresponde à localização de Cingapura. Além disso, consulte as Perguntas Frequentes anteriores sobre o suporte de endereço de sub-rede do cliente nesta página.

- Em segundo lugar, o Gerenciador de Tráfego usa um mapa interno para fazer a conversão do endereço IP para a região geográfica. Embora esse mapa seja validado e atualizado regularmente para aumentar a precisão e leve em conta a natureza evolutiva da Internet, ainda há a possibilidade de que nossas informações não sejam uma representação exata da localização geográfica de todos os endereços IP.


###  <a name="does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing"></a>Um ponto de extremidade precisa estar fisicamente localizado na mesma região com a qual ele foi configurado para roteamento geográfico? 
Não, o local do ponto de extremidade não impõe restrições sobre quais regiões podem ser mapeadas para ele. Por exemplo, um ponto de extremidade na região Central dos EUA do Azure pode ter todos os usuários da Índia direcionados para ele.

### <a name="can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing"></a>Posso atribuir regiões geográficas aos pontos de extremidade em um perfil que não esteja configurado para fazer roteamento geográfico? 

Sim, se o método de roteamento de um perfil não for geográfico, você poderá usar a [API REST do Gerenciador de Tráfego do Azure](https://docs.microsoft.com/rest/api/trafficmanager/) para atribuir regiões geográficas aos pontos de extremidade nesse perfil. No caso de perfis de tipo de roteamento não geográfico, essa configuração será ignorada. Se você alterar esse perfil para tipo de roteamento geográfico em um momento posterior, o Gerenciador de Tráfego usará esses mapeamentos.


### <a name="why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic"></a>Por que está ocorrendo um erro ao tentar alterar o método de roteamento de um perfil existente para Geográfico?

Todos os pontos de extremidade em um perfil com roteamento geográfico precisam ter pelo menos uma região mapeada para eles. Para converter um perfil existente em tipo de roteamento geográfico, primeiramente, você precisa associar regiões geográficas a todos os respectivos pontos de extremidade usando a [API REST do Gerenciador de Tráfego do Azure](https://docs.microsoft.com/rest/api/trafficmanager/) antes de alterar o tipo de roteamento para geográfico. Se for usar o portal, primeiramente exclua os pontos de extremidade, altere o método de roteamento do perfil para geográfico e, por fim, adicione os pontos de extremidade com o respectivo mapeamento da região geográfica. 


###  <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>Por que é enfaticamente aconselhável que os clientes criem perfis aninhados em vez de pontos de extremidade em um perfil com o roteamento geográfico habilitado? 

Uma região poderá ser atribuída apenas a um ponto de extremidade em um perfil se ela estiver usando tipo de roteamento geográfico. Se esse ponto de extremidade não for um tipo aninhado com um perfil filho conectado a ele, caso esse ponto de extremidade se torne não íntegro, o Gerenciador de Tráfego continuará enviando tráfego a ele, pois a alternativa de não enviar tráfego não é a opção melhor. O Gerenciador de Tráfego não faz failover para outro ponto de extremidade, mesmo quando a região atribuída for "pai" da região atribuída ao ponto de extremidade que deixou de ser íntegro (por exemplo, se um ponto de extremidade que tenha a região Espanha deixar de ser íntegro, não ocorrerá o failover para outro ponto de extremidade que tenha a região Europa atribuída). Isso é para garantir que o Gerenciador de Tráfego respeite os limites geográficos que um cliente configurou em seu perfil. Para obter o benefício de fazer failover para outro ponto de extremidade quando um se torna não íntegro, é aconselhável que as regiões geográficas sejam atribuídas a perfis aninhados com vários pontos de extremidade neles, em vez de pontos de extremidade individuais. Dessa forma, se um ponto de extremidade no perfil filho aninhado falhar, o tráfego pode fazer failover para outro ponto de extremidade no mesmo perfil filho aninhado.

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>Existem restrições quanto à versão de API que oferece suporte a esse tipo de roteamento?

Sim, somente a versão 2017-03-01 da API e as mais recentes dão suporte ao tipo de roteamento Geográfico. Qualquer versão de API mais antiga não pode ser usada para criar perfis de tipo de roteamento Geográfico ou atribuir regiões geográficas aos pontos de extremidade. Se uma versão de API mais antiga for usada para recuperar perfis de uma assinatura do Azure, nenhum perfil de tipo de roteamento Geográfico será retornado. Além disso, ao usar versões de API mais antigas, todos os perfis retornados que tiverem pontos de extremidade com uma atribuição de região geográfica não terão essa atribuição exibida.

## <a name="real-user-measurements"></a>Medidas Reais de Usuário

>[!NOTE]
>O recurso de Medidas Reais de Usuário no Gerenciador de Tráfego está na visualização pública e pode não ter o mesmo nível de disponibilidade e confiabilidade que os recursos que já estão disponíveis. Não há suporte para o recurso, o recurso pode ter funcionalidades restritas e ele pode não estar disponível em todas as localizações do Azure. Para receber as notificações mais recentes sobre a disponibilidade e o status desse recurso, confira a página [Atualizações do Gerenciador de Tráfego do Azure](https://azure.microsoft.com/updates/?product=traffic-manager).

### <a name="what-are-the-benefits-of-using-real-user-measurements"></a>Quais são os benefícios do uso de Medidas Reais de Usuário?
Quando você usa o método de roteamento de desempenho, o Gerenciador de Tráfego escolhe a melhor região do Azure para que o usuário final se conecte, inspecionando o IP de origem e a sub-rede do cliente EDNS (se for passada) e verificando-os em relação à inteligência de latência da rede que o serviço mantém. O recurso de Medidas Reais de Usuário melhora isso para sua base de usuários finais, fazendo com que a experiência deles contribua nessa tabela de latência, além de assegurar que essa tabela abranja corretamente as redes dos usuários finais das quais os usuários finais se conectam ao Azure. Isso leva a um aumento de precisão no roteamento de seus usuários finais.

### <a name="can-i-use-real-user-measurements-with-non-azure-regions"></a>É possível usar as Medidas Reais de Usuário com regiões que não são do Azure?
O recurso de Medidas Reais de Usuário mede e relata apenas a latência para alcançar as regiões do Azure. Se você estiver usando o roteamento baseado em desempenho com pontos de extremidade hospedados em regiões que não são do Azure, você ainda poderá aproveitar esse recurso com o aumento de informações de latência sobre a região do Azure representante que você selecionou para associar a esse ponto de extremidade.

### <a name="which-routing-method-benefits-from-real-user-measurements"></a>Qual método de roteamento se beneficia das Medidas Reais de Usuário?
As informações adicionais obtidas por meio das Medidas Reais de Usuário são aplicáveis apenas aos perfis que usam o método de roteamento de desempenho. Observe que o link de Medidas Reais de Usuário está disponível em todos os perfis exibidos por meio do portal do Azure.

### <a name="do-i-need-to-enable-real-user-measurements-each-profile-separately"></a>É necessário habilitar as Medidas Reais de Usuário para cada perfil separadamente?
Não, você precisa habilitá-las uma únicas vez para cada assinatura e todas as informações de latência medidas e relatadas estarão disponíveis para todos os perfis.

### <a name="how-do-i-turn-off-real-user-measurements-for-my-subscription"></a>Como desligar as Medidas Reais de Usuário da minha assinatura?
Você pode interromper o acúmulo de encargos relacionados às Medidas Reais de Usuário ao parar de coletar e enviar as medidas de latência do seu aplicativo cliente. Por exemplo, quando o JavaScript de medida está inserido em páginas da Web, você pode parar de usar esse recurso removendo o JavaScript ou desabilitando sua invocação quando a página é renderizada.
Outra maneira de desligar as Medidas Reais de Usuário é excluir a chave. Depois de fazer isso, caso haja medidas enviadas ao Gerenciador de Tráfego com essa chave, elas serão descartadas.

### <a name="can-i-use-real-user-measurements-with-client-applications-other-than-web-pages"></a>Posso usar as Medidas Reais de Usuário com aplicativos clientes que não sejam páginas da Web?
Sim, as Medidas Reais de Usuário são projetadas para ingeir dados coletados por meio de diferentes tipos de clientes do usuário final. Estas perguntas frequentes serão atualizadas à medida que novos tipos de clientes obtenham suporte.

### <a name="how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered"></a>Quantas medidas são feitas quando uma página da Web habilitada para Medidas Reais de Usuário é renderizada?
Quando o recurso de Medidas Reais de Usuário é usado com o JavaScript de medidas fornecido, cada página renderizada resulta em seis medidas. Em seguida, elas são retornadas ao serviço do Gerenciador de Tráfego. Observe que você será cobrado por esse recurso com base no número de medidas relatadas ao serviço do Gerenciador de Tráfego. Por exemplo, se o usuário sair da sua página da Web enquanto as medidas estiverem sendo obtidas, mas antes que elas sejam relatadas, essas medidas não serão consideradas para fins de cobrança.

### <a name="is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage"></a>Existe algum atraso antes que o script das Medidas Reais de Usuário seja executado na minha página da Web?
Não há nenhum atraso programado antes que o script seja invocado.

### <a name="can-i-use-configure-real-user-measurements-with-only-the-azure-regions-i-want-to-measure"></a>Posso usar a configuração de Medidas Reais de Usuário apenas com as regiões do Azure que desejo medir?
Não, sempre que o recurso é invocado, o script de Medidas Reais do Usuário mede um conjunto de seis regiões do Azure, conforme é determinado pelo serviço. Esse conjunto é alterado entre diferentes invocações e quando um grande número dessas invocações acontece, a cobertura das medidas abrange diferentes regiões do Azure.

### <a name="can-i-limit-the-number-of-measurements-made-to-a-specific-number"></a>Posso limitar o número de medidas feitas para um número específico?
O JavaScript de medidas é inserido em sua página da Web e você tem o controle total sobre quando iniciar e parar de usá-lo. Desde que o serviço do Gerenciador de Tráfego recebe uma solicitação para obter uma lista de regiões do Azure a serem medidas, um conjunto de regiões será retornado. Também tenha em mente que durante o período de versão prévia, você será não cobrado por nenhuma medida relatadas ao Gerenciador de Tráfego

### <a name="can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements"></a>Posso ver as medidas obtidas pelo meu aplicativo cliente como parte das Medidas Reais de Usuário?
Como a lógica de medida é executada no seu aplicativo cliente, você tem o controle total sobre o que acontece, incluindo observar as medições de latência. O Gerenciador de Tráfego não relata uma exibição agregada das medidas recebidas sob a chave vinculada à sua assinatura

### <a name="can-i-modify-the-measurement-script-provided-by-traffic-manager"></a>Posso modificar o script de medida fornecido pelo Gerenciador de Tráfego?
Enquanto você controlar o que é inserido em sua página da Web, nós realmente recomendamos que você não faça alterações no script de medidas para garantir que ele meça e relate as latências corretamente.

### <a name="will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements"></a>Será possível que outras pessoas vejam a chave que eu uso com as Medidas Reais de Usuário?
Quando você inserir o script de medidas em uma página da Web, outras pessoas poderão ver o script e a sua chave de RUM (Medidas Reais de Usuário). Mas é importante saber que essa chave é diferente da ID da sua assinatura e ela é gerada pelo Gerenciador de Tráfego a ser usado apenas para essa finalidade. O fato de outras pessoas saberem qual é a sua chave de RUM não comprometerá a segurança da conta do Azure

### <a name="can-others-abuse-my-rum-key"></a>Outras pessoas podem usar minha chave de RUM?
Embora seja possível que outras pessoas usem sua chave para enviar informações erradas ao Azure, observe que algumas medidas erradas não alterarão o roteamento, pois ele é considerado em conjunto com todas as outras medidas que recebemos. Se precisar alterar as chaves, você poderá gerar a chave novamente no ponto em que a chave antiga for descartada.

###  <a name="do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages"></a>É necessário colocar o JavaScript de medida em todas as páginas da Web?
O recurso de Medidas Reais de Usuário oferece mais valor à medida que o número de medidas aumenta. Dito isso, fica a seu critério se é necessário colocá-lo em todas as páginas da Web ou apenas em algumas. Nossa recomendação é começar colocando-o em sua página mais visitada, na qual o usuário geralmente permanece por cinco segundos ou mais.

### <a name="can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements"></a>As informações sobre meus usuários finais poderão ser identificadas pelo Gerenciador de Tráfego se eu usar as Medidas Reais de Usuário?
Quando o JavaScript de medida fornecido for usado, o Gerenciador de Tráfego terá visibilidade do endereço IP do cliente do usuário final e do endereço IP de origem do resolvedor de DNS local usado. O Gerenciador de Tráfego usa o endereço IP do cliente somente após truncá-lo para não ser capaz de identificar o usuário final específico que enviou as medidas. 

### <a name="does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing"></a>A página da Web que está medindo as Medidas Reais de Usuário precisa estar usando o Gerenciador de Tráfego para o roteamento?
Não, ela não precisa usar o Gerenciador de Tráfego. O lado de roteamento do Gerenciador de Tráfego opera separadamente da parte das Medidas Reais do Usuário e embora seja uma boa ideia que ambos estejam na mesma propriedade de Web, eles não precisam estar.

### <a name="do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements"></a>É necessário hospedar algum serviço nas regiões do Azure a serem usadas com as Medidas Reais de Usuário?
Não, você não precisa hospedar nenhum componente do servidor no Azure para que as Medidas Reais de Usuário funcionem. A imagem de pixel único baixada pelo JavaScript de medida e o serviço que o executa em diferentes regiões do Azure é hospedado e gerenciado pelo Azure. 

### <a name="will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements"></a>Meu uso de largura de banda do Azure aumentará com o uso das Medidas Reais de Usuário?
Conforme foi mencionado na resposta anterior, os componentes do servidor das Medidas Reais de Usuário pertencem ao Azure e são gerenciados pelo Azure. Isso significa que o uso de largura de banda do Azure não aumentará com o uso das Medidas Reais de Usuário. Observe que isso não inclui nenhum uso de largura de banda adicional à que é cobrada pelo Azure. Podemos minimizar a largura de banda usada baixando uma imagem de pixel único para medir a latência para uma região do Azure. 

## <a name="traffic-view"></a>Exibição do Tráfego

>[!NOTE]
>O recurso de Exibição do Tráfego no Gerenciador de Tráfego está na visualização pública e pode não ter o mesmo nível de disponibilidade e confiabilidade do que os recursos que estão na versão já disponível. Não há suporte para o recurso, o recurso pode ter funcionalidades restritas e ele pode não estar disponível em todas as localizações do Azure. Para receber as notificações mais recentes sobre a disponibilidade e o status desse recurso, confira a página [Atualizações do Gerenciador de Tráfego do Azure](https://azure.microsoft.com/updates/?product=traffic-manager).

### <a name="what-does-traffic-view-do"></a>O que faz a Exibição do Tráfego faz?
A Exibição do Tráfego é um recurso do Gerenciador de Tráfego que ajuda a saber mais sobre os usuários e suas experiências. Ele usa as consultas recebidas pelo Gerenciador de Tráfego e as tabelas de inteligência de latência de rede que o serviço mantém para fornecer o seguinte:
- As regiões de onde os usuários se conectam aos pontos de extremidade no Azure.
- O volume de usuários que se conectam dessas regiões.
- As regiões do Azure às quais eles estão sendo roteados.
- Suas experiências de latência para essas regiões do Azure.

Essas informações estão disponíveis para você consumir por meio de uma exibição tabular no portal, além de estarem disponíveis como dados brutos para download.

### <a name="how-can-i-benefit-from-using-traffic-view"></a>Como posso me beneficiar do uso da Exibição do Tráfego?

A Exibição do Tráfego fornece a visão geral do tráfego que seus perfis do Gerenciador de Tráfego recebem. Ela pode ser usada principalmente para entender de onde sua base de usuários se conecta e também qual é a experiência de latência média desses usuários. Você pode usar essas informações para localizar áreas em que precisa se concentrar, por exemplo, expandindo sua superfície do Azure para uma região que possa atender esses usuários com uma latência mais baixa. Outras informações que você pode extrair usando a Exibição do Tráfego são os padrões de tráfego em diferentes regiões, o que pode ajudá-lo a tomar decisões sobre aumentar ou diminuir o invent. nessas regiões.

### <a name="how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor"></a>Por que a Exibição do Tráfego é diferente das métricas do Gerenciador de Tráfego disponíveis por meio do Azure Monitor?

O Azure Monitor pode ser usado para entender, em um nível de agregação, o tráfego recebido pelo seu perfil e seus pontos de extremidade. Ele também permite que você acompanhe o status de integridade dos pontos de extremidade expondo os resultados da verificação de integridade. Quando você precisa ir além disso e entender a experiência do usuário final ao se conectar ao Azure em um nível regional, é possível usar a Exibição do Tráfego.

### <a name="does-traffic-view-use-edns-client-subnet-information"></a>A Exibição do Tráfego usa informações de sub-rede de cliente EDNS?

A Exibição do Tráfego não considera as informações de sub-rede de cliente EDNS durante a criação de sua saída. Ele usa o endereço IP do resolvedor de DNS local dos usuários para agrupá-los.

### <a name="how-many-days-of-data-does-traffic-view-use"></a>Quantos dias de dados a Exibição do Tráfego usa?

A Exibição do Tráfego cria sua saída processando os dados dos sete dias que precedem o dia anterior de sua exibição. Esse é um período móvel e os dados mais recentes serão usados sempre que você a visitar.

### <a name="how-does-traffic-view-handle-external-endpoints"></a>Como a Exibição do Tráfego lida com pontos de extremidade externos?

Quando você usa pontos de extremidade externos, hospedados fora das regiões do Azure em um perfil do Gerenciador de Tráfego, você pode escolher que eles sejam mapeados para uma região do Azure que seja um proxy para suas características de latência (isso na verdade é necessário quando você usa o método de roteamento de desempenho). Se houver esse mapeamento de região do Azure, as métricas de latência dessa região do Azure serão usadas para criar a saída da Exibição do Tráfego. Se não houver nenhuma região do Azure especificada, as informações de latência ficarão vazias nos dados desses pontos de extremidade externos.

### <a name="do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription"></a>É necessário habilitar a Exibição do Tráfego para cada perfil na minha assinatura?
Durante o período de versão prévia, a Exibição do Tráfego será habilitada em um nível de assinatura e estará disponível para todos os perfis do Gerenciador de Tráfego na assinatura.

### <a name="how-can-i-turn-off-traffic-view"></a>Como posso desligar a Exibição do Tráfego?
Durante o período de versão prévia, solicitamos que você crie um tíquete de suporte para desabilitar a Exibição do Tráfego para sua assinatura.

### <a name="how-does-traffic-view-billing-work"></a>Como funciona a cobrança da Exibição do Tráfego?

O preço da Exibição do Tráfego é baseado no número de pontos de dados usados para criar a saída. Atualmente, o único tipo de dados com suporte são as consultas que o seu perfil recebe. Além disso, você será cobrado somente pelo processamento que foi feito quando a Exibição do Tráfego estava habilitada. Isso significa que, se você habilitar a Exibição do Tráfego em um certo período do mês e desabilitá-la em outros períodos, somente os pontos de dados processados enquanto o recurso estava habilitado serão cobrados.
Durante o período de versão prévia, você não será cobrado para usar a Exibição do Tráfego.

## <a name="traffic-manager-endpoints"></a>Pontos de extremidade do Gerenciador de Tráfego

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>Posso usar o Gerenciador de Tráfego com pontos de extremidade de várias assinaturas?

Não é possível usar pontos de extremidade de várias assinaturas com Aplicativos Web do Azure. Os Aplicativos Web do Azure exigem que qualquer nome de domínio personalizado usado com Aplicativos Web seja usado somente em uma única assinatura. Não é possível usar Aplicativos Web de várias assinaturas com o mesmo nome de domínio.

Para outros tipos de ponto de extremidade, é possível usar o Gerenciador de Tráfego com pontos de extremidade de mais de uma assinatura. No Gerenciador de Recursos, os pontos de extremidade de qualquer assinatura podem ser adicionados ao Gerenciador de Tráfego, desde que a pessoa que configura o perfil do Gerenciador de Tráfego tenha o acesso de leitura ao ponto de extremidade. Essas permissões podem ser concedidas usando o [RBAC (controle de acesso baseado em função) do Azure Resource Manager](../active-directory/role-based-access-control-configure.md).


### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>Posso usar o Gerenciador de Tráfego com os slots de “Preparo” do Serviço de Nuvem?

Sim. Os slots de “preparo” do Serviço de Nuvem podem ser configurados no Gerenciador de Tráfego como pontos de extremidade Externos. Verificações de integridade ainda são cobradas à taxa de Pontos de Extremidade do Azure. Como o tipo do ponto de extremidade externo está em uso, as alterações no serviço subjacente não serão selecionadas automaticamente. Com pontos de extremidade externos, o Gerenciador de Tráfego não consegue detectar quando o Serviço de Nuvem é parado ou excluído. Portanto, o Gerenciador de Tráfego continua cobrando por verificações de integridade até que o ponto de extremidade seja desabilitado ou excluído.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>O Gerenciador de Tráfego dá suporte aos pontos de extremidade IPv6?

No momento, o Gerenciador de Tráfego não oferece servidores de nome endereçáveis por IPv6. No entanto, o Gerenciador de Tráfego ainda pode ser usado por clientes IPv6 que se conectem a pontos de extremidade IPv6. Um cliente não faz solicitações DNS diretamente ao Gerenciador de tráfego. Em vez disso, o cliente usa um serviço DNS recursivo. Um cliente somente IPv6 envia solicitações para o serviço DNS recursivo via IPv6. Então o serviço recursivo deve conseguir contatar os servidores de nome do Gerenciador de Tráfego usando IPv4.

O Gerenciador de Tráfego responde com o nome DNS do ponto de extremidade. Para dar suporte a um ponto de extremidade IPv6, deve haver um registro DNS AAAA apontando o nome DNS do ponto de extremidade para o endereço IPv6. Verificações de integridade do Gerenciador de Tráfego dão suporte apenas a endereços IPv4. O serviço deve expor um ponto de extremidade IPv4 no mesmo nome DNS.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>Posso usar o Gerenciador de Tráfego com mais de um Aplicativo Web na mesma região?

Normalmente, o Gerenciador de Tráfego é usado para direcionar o tráfego para os aplicativos implantados em regiões diferentes. No entanto, ele também pode ser usado onde um aplicativo tem mais de uma implantação na mesma região. Os pontos de extremidade do Gerenciador de Tráfego do Azure não permitem adicionar mais de um ponto de extremidade do Aplicativo Web da mesma região do Azure ao mesmo perfil do Gerenciador de Tráfego.

##  <a name="traffic-manager-endpoint-monitoring"></a>Monitoramento de ponto de extremidade do Gerenciador de Tráfego

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>O Gerenciador de Tráfego é resistente a falhas de região do Azure?

O Gerenciador de Tráfego é um componente fundamental da entrega de aplicativos altamente disponíveis no Azure.
Para entregar alta disponibilidade, o Gerenciador de Tráfego deve ter um nível muito alto de disponibilidade e ser resiliente a falhas de região.

Por design, os componentes do Gerenciador de Tráfego são resilientes a uma falha completa de qualquer região do Azure. Essa resiliência se aplica a todos os componentes do Gerenciador de Tráfego: os servidores de nome DNS, a API, a camada de armazenamento e o serviço de monitoramento de ponto de extremidade.

No caso improvável de uma falha de uma região do Azure inteira, Gerenciador de Tráfego deve continuar a funcionar normalmente. Os aplicativos implantados em várias regiões do Azure podem contar com o Gerenciador de Tráfego para direcionar o tráfego para uma instância disponível de seu aplicativo.

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>Como a escolha da localização do grupo de recursos afeta o Gerenciador de Tráfego?

O Gerenciador de Tráfego é um serviço global único. Não é regional. A escolha do local do grupo de recursos não faz diferença para perfis do Gerenciador de Tráfego implantados nesse grupo de recursos.

O Azure Resource Manager exige que todos os grupos de recursos especifiquem uma localização, que determina a localização padrão para os recursos implantados nesse grupo de recursos. Quando você cria um perfil do Gerenciador de Tráfego, ele é criado em um grupo de recursos. Todos os perfis do Gerenciador de Tráfego usam **global** como a localização, substituindo o padrão do grupo de recursos.

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>Como determinar a integridade atual de cada ponto de extremidade?

O status de monitoramento atual de cada ponto de extremidade, bem como o perfil geral, é exibido no portal do Azure. Essas informações também estão disponíveis por meio da [API REST](https://msdn.microsoft.com/library/azure/mt163667.aspx) do Traffic Monitor, dos [cmdlets do PowerShell](https://msdn.microsoft.com/library/mt125941.aspx) e da [CLI do Azure entre plataformas](../cli-install-nodejs.md).

O Azure não fornece informações históricas sobre dados passados referentes à integridade do ponto de extremidade ou à capacidade de emitir alertas sobre alterações na integridade do ponto de extremidade.

### <a name="can-i-monitor-https-endpoints"></a>Posso monitorar os pontos de extremidade HTTPS?

Sim. O Gerenciador de Tráfego oferece suporte à investigação por HTTPS. Configure **HTTPS** como o protocolo na configuração de monitoramento.

O Gerenciador de Tráfego não pode fornecer nenhuma validação de certificado, incluindo:

* Certificados no lado do servidor não estão validados
* Certificados no lado do servidor SNI não estão validados
* Não há suporte para certificados de cliente

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

### <a name="what-host-header-do-endpoint-health-checks-use"></a>Qual cabeçalho host as verificações de integridade do ponto de extremidade usam?

O Gerenciador de Tráfego usa cabeçalhos de host em verificações de integridade HTTP e HTTPS. O cabeçalho de host usado pelo Gerenciador de Tráfego é o nome do ponto de extremidade de destino configurado no perfil. O valor usado no cabeçalho do host não pode ser especificado separadamente da propriedade “target”.

### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>Quais são os endereços IP dos quais as verificações de integridade se originam?

A lista a seguir contém os endereços IP dos quais as verificações de integridade do Gerenciador de Tráfego podem se originar. Você pode usar esta lista para garantir que as conexões de entrada desses endereços IP sejam permitidas nos pontos de extremidades para verificar o status de integridade.

* 40.68.30.66
* 40.68.31.178
* 137.135.80.149
* 137.135.82.249
* 23.96.236.252
* 65.52.217.19
* 40.87.147.10
* 40.87.151.34
* 13.75.124.254
* 13.75.127.63
* 52.172.155.168
* 52.172.158.37
* 104.215.91.84
* 13.75.153.124
* 13.84.222.37
* 23.101.191.199
* 23.96.213.12
* 137.135.46.163
* 137.135.47.215
* 191.232.208.52
* 191.232.214.62
* 13.75.152.253
* 104.41.187.209
* 104.41.190.203
* 52.173.90.107
* 52.173.250.232
* 104.45.149.110
* 40.114.5.197
* 52.240.151.125
* 52.240.144.45
* 13.65.95.152
* 13.65.92.252
* 40.78.67.110
* 104.42.192.195

### <a name="how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager"></a>Quantas verificações de integridade no meu ponto de extremidade posso esperar do Gerenciador de Tráfego?

O número de verificações de integridade do Gerenciador de Tráfego que atingem seu ponto de extremidade depende do seguinte:
- o valor que você definiu para o intervalo de monitoramento (intervalo menor significa que mais solicitações chegaram no ponto de extremidade em um determinado período de tempo).
- o número de locais dos quais as verificações de integridade se originam (os endereços IP dos quais você pode esperar essas verificações estão listados nas perguntas frequentes anteriores).

## <a name="traffic-manager-nested-profiles"></a>Perfis aninhados do Gerenciador de Tráfego

### <a name="how-do-i-configure-nested-profiles"></a>Como posso configurar perfis aninhados?

Os perfis aninhados do Gerenciador de Tráfego podem ser configurados usando o Azure Resource Manager e as APIs REST clássicas do Azure, cmdlets do Azure PowerShell e comandos da CLI do Azure de plataforma cruzada. Também há suporte para eles no novo portal do Azure. Não há suporte para eles no portal clássico.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>A quantas camadas de aninhamento o Gerenciador de Tráfego dá suporte?

Você pode aninhar perfis em até 10 níveis de profundidade. “Loops” não são permitidos.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>Posso combinar outros tipos de ponto de extremidade com perfis filho aninhados no mesmo perfil do Gerenciador de Tráfego?

Sim. Não há nenhuma restrição sobre como combinar os pontos de extremidade de diferentes tipos em um perfil.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>Como o modelo de cobrança se aplica a perfis aninhados?

Há não impacto negativo sobre os preços ao usar perfis aninhados.

A cobrança do Gerenciador de Tráfego tem dois componentes: verificações de integridade do ponto de extremidade e milhões de consultas DNS

* Verificações de integridade de ponto de extremidade: não há nenhum encargo para um perfil filho quando configurado como um ponto de extremidade em um perfil pai. O monitoramento dos pontos de extremidade no perfil filho é cobrado como de costume.
* Consultas DNS: cada consulta é contada apenas uma vez. Uma consulta em um perfil pai que retorna um ponto de extremidade de um perfil filho é contada apenas no perfil pai.

Para obter detalhes completos, consulte a [página de preços do Gerenciador de Tráfego](https://azure.microsoft.com/pricing/details/traffic-manager/).

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>Há impacto no desempenho para perfis aninhados?

Nº Não há nenhum impacto no desempenho ao usar perfis aninhados.

Os servidores de nomes do Gerenciador de Tráfego atravessam a hierarquia de perfil internamente durante o processamento de cada consulta DNS. Uma consulta DNS a um perfil pai pode receber uma resposta DNS com um ponto de extremidade de um perfil filho. Um único registro CNAME é usado se você está usando um único perfil ou perfis aninhados. Não é necessário criar um registro CNAME para cada perfil na hierarquia.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Como o Gerenciador de Tráfego calcula a integridade de um ponto de extremidade aninhado em um perfil pai?

O perfil pai não executa verificações de integridade no filho diretamente. Em vez disso, a integridade dos pontos de extremidade do perfil filho é usada para calcular a integridade geral do perfil filho. Essas informações são propagadas até a hierarquia do perfil aninhado para determinar a integridade do ponto de extremidade aninhado. O perfil pai usa essa integridade agregada para determinar se o tráfego pode ser direcionado para o filho.

A tabela a seguir descreve o comportamento das verificações de integridade do Gerenciador de Tráfego de um ponto de extremidade aninhado.

| Status do Monitor de perfil filho | Status do monitor de ponto de extremidade pai | Observações |
| --- | --- | --- |
| Desabilitado. O perfil filho foi desabilitado. |Parada |O estado do ponto de extremidade pai é Parado, não Desabilitado. O estado Desabilitado é reservado para indicar que você desabilitou o ponto de extremidade no perfil pai. |
| Degradado. Pelo menos um ponto de extremidade do perfil filho está no estado Degradado. |Online: o número de pontos de extremidade Online no perfil filho é pelo menos o valor de MinChildEndpoints.<BR>CheckingEndpoint: o número de pontos de extremidade Online mais CheckingEndpoint no perfil filho é pelo menos o valor de MinChildEndpoints.<BR>Degradado: caso contrário. |O tráfego é roteado para um ponto de extremidade do status CheckingEndpoint. Se MinChildEndpoints estiver definido com um valor muito alto, o ponto de extremidade estará sempre degradado. |
| Online. Pelo menos, um ponto de extremidade do perfil filho está em um estado Online. Nenhum ponto de extremidade está no estado Degradado. |Veja acima. | |
| CheckingEndpoints. Pelo menos, um ponto de extremidade do perfil filho é um 'CheckingEndpoint'. Nenhum ponto de extremidade está ‘Online’ ou ‘Degradado’ |Mesmo que acima. | |
| Inativo. Todos os pontos de extremidade de perfil filho estão com status Desabilitado ou Parado ou esse é um perfil que não tem nenhum ponto de extremidade. |Parada | |

## <a name="next-steps"></a>Próximas etapas:
- Saiba mais sobre o [failover automático e monitoramento do ponto de extremidade](../traffic-manager/traffic-manager-monitoring.md)do Gerenciador de Tráfego.
- Saiba mais sobre os [métodos de roteamento de tráfego](../traffic-manager/traffic-manager-routing-methods.md)do Gerenciador de Tráfego.