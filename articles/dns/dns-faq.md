---
title: Perguntas frequentes-DNS do Azure
description: Neste artigo, saiba mais sobre as perguntas frequentes sobre o DNS do Azure
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 01/11/2021
ms.author: rohink
ms.openlocfilehash: cb14cb95ec1362782a634b0e62cfa2f8237a5852
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98246375"
---
# <a name="azure-dns-faq"></a>Perguntas frequentes do DNS do Azure

## <a name="about-azure-dns"></a>Sobre o DNS do Azure

### <a name="what-is-azure-dns"></a>O que é o DNS do Azure?

O Sistema de Nomes de Domínio (DNS) traduz ou resolve um nome de site ou serviço para seu endereço IP. O DNS do Azure é um serviço de hospedagem para domínios DNS. Ele fornece resolução de nomes usando a infraestrutura do Microsoft Azure. Ao hospedar seus domínios no Azure, você pode gerenciar seus registros DNS usando as mesmas credenciais, APIs, ferramentas e faturamento que os outros serviços do Azure.

Domínios DNS no DNS do Azure estão hospedados na rede global do Azure de servidores de nomes DNS. Esse sistema usa a rede Anycast para que cada consulta DNS seja respondida pelo servidor DNS mais próximo disponível. O DNS do Azure fornece desempenho rápido e alta disponibilidade para seu domínio.

O DNS do Azure é baseado no Azure Resource Manager. O Azure DNS beneficia-se dos recursos do Resource Manager, como o controle de acesso baseado em função do Azure, os logs de auditoria e o bloqueio de recursos. Você pode gerenciar domínios e registros por meio do portal do Azure, dos cmdlets do Azure PowerShell e da CLI do Azure de plataforma cruzada. Os aplicativos que exigem gerenciamento automático de DNS podem se integrar ao serviço por meio da API REST e dos SDKs.

### <a name="how-much-does-azure-dns-cost"></a>Quanto custa o DNS do Azure?

O modelo de faturamento do DNS do Azure é baseado no número de zonas DNS hospedadas no DNS do Azure. Também é baseado no número de consultas DNS recebidas. Descontos são fornecidos com base no uso.

Para saber mais, veja a [página de preços do DNS do Azure](https://azure.microsoft.com/pricing/details/dns/).

### <a name="what-is-the-sla-for-azure-dns"></a>O que é o SLA para DNS do Azure?

O Azure garante que as solicitações válidas de DNS recebam uma resposta de pelo menos um servidor de nomes DNS do Azure 100% do tempo.

Para saber mais, confira a [página de SLA do DNS do Azure](https://azure.microsoft.com/support/legal/sla/dns).

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>O que é uma zona DNS? É a mesma coisa que um domínio DNS? 

Um domínio é um nome exclusivo no sistema de nomes de domínio. Um exemplo é contoso.com.

Uma zona DNS é usada para hospedar os registros DNS para um domínio específico. Por exemplo, o domínio contoso.com pode conter vários registros DNS. Os registros podem incluir mail.contoso.com para um servidor de email e o www \. contoso.com para um site. Esses registros estão hospedados na zona DNS contoso.com.

É um nome de domínio *apenas um nome*. Uma zona DNS é um recurso de dados que contém os registros DNS de um nome de domínio. Você pode usar o DNS do Azure para hospedar uma zona DNS e gerenciar os registros DNS para um domínio no Azure. Ele também fornece servidores de nomes de DNS para resolver consultas de DNS da Internet.

### <a name="do-i-need-to-buy-a-dns-domain-name-to-use-azure-dns"></a>Preciso comprar um nome de domínio DNS para usar o DNS do Azure? 

Não necessariamente.

Você não precisa comprar um domínio para hospedar uma zona DNS no DNS do Azure. É possível criar uma zona DNS a qualquer momento sem ser proprietário do nome de domínio. As consultas DNS para essa zona só serão resolvidas se forem direcionadas para os servidores de nomes DNS do Azure atribuídos à zona.

Para vincular sua zona DNS à hierarquia DNS global, você deve comprar o nome de domínio. Então, as consultas DNS de qualquer lugar do mundo encontram sua zona DNS e respondem com seus registros DNS.

## <a name="azure-dns-features"></a>Recursos do DNS do Azure

### <a name="are-there-any-restrictions-when-using-alias-records-for-a-domain-name-apex-with-traffic-manager"></a>Existem restrições ao usar registros de alias para um apex de nome de domínio com o Gerenciador de Tráfego?

Sim. Você deve usar endereços IP públicos estáticos com o Gerenciador de Tráfego do Azure. Configure o destino **External endpoint** usando um endereço IP estático. 

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>O DNS do Azure dá suporte a roteamento de tráfego com base em DNS ou failover de ponto de extremidade?

Roteamento de tráfego baseado em DNS e failover de ponto de extremidade são fornecidos pelo Gerenciador de Tráfego. O Gerenciador de Tráfego é um serviço do Azure separado que pode ser usado com o DNS do Azure. Para saber mais, confira a [Visão Geral do Gerenciador de Tráfego](../traffic-manager/traffic-manager-overview.md).

O DNS do Azure só oferece suporte à hospedagem de domínios DNS estáticos, em que cada consulta DNS de um determinado registro DNS sempre recebe a mesma resposta de DNS.

### <a name="does-azure-dns-support-domain-name-registration"></a>O DNS do Azure dá suporte ao registro de nome de domínio?

Não. O DNS do Azure atualmente não oferece suporte à opção de comprar nomes de domínio. Para comprar domínios, você deve usar um registrador de nomes de domínio de terceiros. O registrador normalmente cobra uma pequena taxa anual. Os domínios, em seguida, podem ser hospedados no DNS do Azure para gerenciamento de registros DNS. Confira [Delegar um domínio ao DNS do Azure](dns-domain-delegation.md)para saber mais.

O recurso para comprar nomes de domínio é rastreado no backlog do Azure. Use o site de feedback para [registrar seu suporte para esse recurso](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar).

### <a name="does-azure-dns-support-dnssec"></a>O DNS do Azure oferece suporte a DNSSEC?

Não. O DNS do Azure atualmente não oferece suporte às extensões de segurança do sistema de nomes de domínio (DNSSEC).

O recurso DNSSEC é rastreado no backlog de DNS do Azure. Use o site de feedback para [registrar seu suporte para esse recurso](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support).

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>O DNS do Azure dá suporte para transferências de zona (AXFR/IXFR)?

Não. O DNS do Azure não suporta atualmente as transferências de zona. As zonas DNS podem ser [importadas para o DNS do Azure usando a CLI do Azure](dns-import-export.md). Os registros DNS são gerenciados por meio do [portal de gerenciamento do DNS do Azure](dns-operations-recordsets-portal.md), [API REST](/powershell/module/az.dns), [SDK](dns-sdk.md), [cmdlets do PowerShell](dns-operations-recordsets.md) ou da [ferramenta CLI](dns-operations-recordsets-cli.md).

O recurso de transferência de zona é rastreado no backlog de DNS do Azure. Use o site de feedback para [registrar seu suporte para esse recurso](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c).

### <a name="does-azure-dns-support-url-redirects"></a>O DNS do Azure oferece suporte a redirecionamentos de URL?

Não. Os serviços de redirecionamento de URL não são um serviço DNS. Eles trabalham no nível HTTP, e não no nível DNS. Alguns provedores DNS agrupam um serviço de redirecionamento de URL como parte de sua oferta geral. No momento, esse serviço não é suportado pelo DNS do Azure.

O recurso de redirecionamento de URL é rastreado no backlog de DNS do Azure. Use o site de feedback para [registrar seu suporte para esse recurso](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape).

### <a name="does-azure-dns-support-the-extended-ascii-encoding-8-bit-set-for-txt-record-sets"></a>O DNS do Azure oferece suporte à codificação ASCII estendida (8 bits) definida para conjuntos de registros TXT?

Sim. O DNS do Azure suporta o conjunto de codificação ASCII estendido para conjuntos de registros TXT. Mas você deve usar a versão mais recente das APIs REST do Azure, SDKs, PowerShell e CLI. Versões anteriores a 1º de outubro de 2017 ou SDK 2.1 não suportam o conjunto ASCII estendido. 

Por exemplo, você pode fornecer uma string como o valor para um registro TXT que tem o caractere ASCII estendido \128. Um exemplo é "abcd\128efgh". O DNS do Azure usa o valor de byte desse caractere, que é 128, na representação interna. No momento da resolução do DNS, esse valor de byte é retornado na resposta. Observe também que "abc" e "\097\098\099" são intercambiáveis com relação à resolução. 

Seguimos as regras de escape de formatação mestre de arquivo da zona [RFC 1035](https://www.ietf.org/rfc/rfc1035.txt) para registros TXT. Por exemplo, `\` agora realmente escapa de tudo pelo RFC. Se você especificar `A\B` como o valor do registro TXT, ele será representado e resolvido como apenas `AB`. Se você realmente quer que o registro TXT tenha `A\B` na resolução, você precisa escapar de `\` novamente. Como exemplo, especifique `A\\B`.

Atualmente, esse suporte não está disponível para registros TXT criados no portal do Azure.

## <a name="alias-records"></a>Registros de alias

### <a name="what-are-some-scenarios-where-alias-records-are-useful"></a>Quais são alguns cenários em que os registros de alias são úteis?

Consulte a seção de cenários na [visão geral dos registros de alias do DNS do Azure](dns-alias.md).

### <a name="what-record-types-are-supported-for-alias-record-sets"></a>Quais tipos de registro têm suporte para conjuntos de registros de alias?

Os conjuntos de registros de alias são suportados para os seguintes tipos de registro em uma zona DNS do Azure:
 
- A 
- AAAA
- CNAME 

### <a name="what-resources-are-supported-as-targets-for-alias-record-sets"></a>Quais recursos têm suporte como destinos para os conjuntos de registros de alias?

- **Aponte para um recurso IP público de um conjunto de registros DNS A / AAAA.** Você pode criar um conjunto de registros A / AAAA e torná-lo um conjunto de registros de alias para apontar para um recurso IP público.
- **Aponte para um perfil do Gerenciador de Tráfego de um conjunto de registros DNS A / AAAA / CNAME.** Você pode apontar para o CNAME de um perfil do Gerenciador de Tráfego a partir de um conjunto de registros CNAME do DNS. Um exemplo é contoso.trafficmanager.net. Agora, você também pode apontar para um perfil do Gerenciador de Tráfego que tenha pontos de extremidade externos de um registro A ou AAAA definido em sua zona DNS.
- **Aponte para um ponto de extremidade da CDN (rede de distribuição de conteúdo) do Azure**. Isso é útil quando você cria sites estáticos usando o armazenamento do Azure e a CDN do Azure.
- **Apontar para outro conjunto de registros DNS dentro da mesma zona.** Registros de alias podem fazer referência a outros conjuntos de registros do mesmo tipo. Por exemplo, você pode ter um conjunto de registros DNS CNAME como um alias para outro conjunto de registros CNAME do mesmo tipo. Essa organização é útil se você quiser que alguns conjuntos de registros sejam aliases e alguns não-aliases.

### <a name="can-i-create-and-update-alias-records-from-the-azure-portal"></a>Posso criar e atualizar os registros de alias do portal do Azure?

Sim. Você pode criar ou gerenciar registros de alias no portal do Azure junto com as APIs REST do Azure, o PowerShell, a CLI e os SDKs.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-deleted-when-the-underlying-public-ip-is-deleted"></a>Os registros de alias ajudam a garantir que meu conjunto de registros DNS seja excluído quando o IP público subjacente é excluído?

Sim. Esse recurso é um dos principais recursos dos registros de alias. Isso ajuda a evitar possíveis interrupções para os usuários do seu aplicativo.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-updated-to-the-correct-ip-address-when-the-underlying-public-ip-address-changes"></a>Os registros de alias ajudam a garantir que meu conjunto de registros DNS seja atualizado para o endereço IP correto quando o endereço IP público subjacente for alterado?

Sim. Esse recurso é um dos principais recursos dos registros de alias. Isso ajuda a evitar possíveis interrupções ou riscos de segurança para seu aplicativo.

### <a name="are-there-any-restrictions-when-using-alias-record-sets-for-a-or-aaaa-records-to-point-to-traffic-manager"></a>Há alguma restrição ao usar conjuntos de registros de alias para registros A ou AAAA para apontar para o Gerenciador de Tráfego?

Sim. Para apontar para um perfil do Gerenciador de Tráfego como um alias de um conjunto de registros A ou AAAA, o perfil do Gerenciador de Tráfego deve usar apenas pontos de extremidade externos. Ao criar os terminais externos no Gerenciador de Tráfego, forneça os endereços IP reais dos nós de extremidade.

### <a name="is-there-an-additional-charge-to-use-alias-records"></a>Existe um custo adicional para usar registros de alias?

Os registros de alias são uma qualificação em um conjunto de registros DNS válido. Não há cobrança adicional para registros de alias.

## <a name="use-azure-dns"></a>Use o DNS do Azure

### <a name="can-i-co-host-a-domain-by-using-azure-dns-and-another-dns-provider"></a>Posso cohospedar um domínio usando o DNS do Azure e outro provedor de DNS?

Sim. O DNS do Azure oferece suporte a domínios de co-hospedagem com outros serviços de DNS.

Para configurar a hospedagem cooperativa, modifique os registros NS para o domínio para apontar para os servidores de nome de ambos os provedores. Os registros do servidor de nomes (NS) controlam quais provedores recebem consultas DNS para o domínio. Você pode modificar esses registros do NS no DNS do Azure, no outro provedor e na zona pai. A zona pai geralmente é configurada por meio do registrador de nomes de domínio. Para saber mais sobre delegação de DNS, veja [Delegação de domínio de DNS](dns-domain-delegation.md).

Além disso, verifique se os registros DNS do domínio estão sincronizados entre os dois provedores de DNS. O DNS do Azure atualmente não oferece suporte a transferências de zona DNS. Os registros DNS devem ser sincronizados usando o [portal de gerenciamento do DNS do Azure](dns-operations-recordsets-portal.md), [API REST](/rest/api/dns/), [SDK](dns-sdk.md), [cmdlets do PowerShell](dns-operations-recordsets.md) ou [Ferramenta CLI](dns-operations-recordsets-cli.md).

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>Preciso delegar meu domínio para todos os quatro servidores de nome de DNS do Azure?

Sim. O DNS do Azure atribui quatro servidores de nomes a cada zona DNS. Esse arranjo é para isolamento de falhas e maior resiliência. Para se qualificar para o SLA de DNS do Azure, delegue seu domínio a todos os quatro servidores de nomes.

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Quais são os limites de uso do DNS do Azure?

Os limites padrão a seguir se aplicam quando você usa o DNS do Azure.

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>Posso mover uma zona DNS do Azure entre grupos de recursos ou entre assinaturas?

Sim. As zonas DNS podem ser movidas entre grupos de recursos ou entre assinaturas.

Não há efeito nas consultas DNS quando você move uma zona DNS. Os servidores de nomes atribuídos à zona permanecem os mesmos. Consultas DNS são processadas normalmente.

Para saber mais e instruções sobre como mover as zonas DNS, veja [Mover recursos para um novo grupo de recursos ou assinatura](../azure-resource-manager/management/move-resource-group-and-subscription.md).

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>Quanto tempo leva para as alterações de DNS entrarem em vigor?

Novas zonas DNS e registros DNS geralmente aparecem nos servidores de nomes DNS do Azure rapidamente. O tempo é de alguns segundos.

Alterações nos registros DNS existentes podem demorar um pouco mais. Eles geralmente aparecem nos servidores de nomes DNS do Azure em 60 segundos. O armazenamento em cache de DNS por clientes DNS e resolvedores recursivos de DNS fora do DNS do Azure também pode afetar o tempo. Para controlar essa duração do cache, use a propriedade Time-To-Live (TTL) de cada conjunto de registros.

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>Como posso proteger minhas zonas DNS contra exclusão acidental?

O DNS do Azure é gerenciado usando o Azure Resource Manager. O DNS do Azure se beneficia dos recursos de controle de acesso fornecidos pelo Azure Resource Manager. O controle de acesso baseado em função do Azure pode ser usado para controlar quais usuários têm acesso de leitura ou de gravação a zonas DNS e conjuntos de registros. Bloqueios de recursos impedem a modificação acidental ou a exclusão de zonas DNS e conjuntos de registros.

Para obter mais informações, consulte [Proteger zonas DNS e registros](dns-protect-zones-recordsets.md).

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Como posso configurar registros de SPF no DNS do Azure?

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-name-servers-resolve-over-ipv6"></a>Servidores de nome DNS do Azure são resolvidas pelo IPv6? 

Sim. Os servidores de nomes DNS do Azure são de pilha dupla. Pilha dupla significa que eles têm endereços IPv4 e IPv6. Para localizar o endereço IPv6 dos servidores de nomes DNS do Azure atribuídos à sua zona DNS, use uma ferramenta como nslookup. Um exemplo é `nslookup -q=aaaa <Azure DNS Nameserver>`.

### <a name="how-do-i-set-up-an-idn-in-azure-dns"></a>Como configuro um IDN no DNS do Azure?

Os nomes de domínio internacionalizados (IDNs) codificam cada nome DNS usando [punycode](https://en.wikipedia.org/wiki/Punycode). Consultas DNS são feitas usando esses nomes codificados com punycode.

Para configurar IDNs no DNS do Azure, converta o nome da zona ou o nome do conjunto de registros em punycode. No momento, o DNS do Azure não oferece suporte à conversão interna de ou para o punycode.

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre o DNS do Azure](dns-overview.md).

- [Saiba mais sobre como usar o DNS do Azure para domínios privados](private-dns-overview.md).

- [Saiba mais sobre zonas e registros DNS](dns-zones-records.md).

- [Introdução ao DNS do Azure](dns-getstarted-portal.md).
