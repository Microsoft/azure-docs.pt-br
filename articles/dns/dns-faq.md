---
title: Perguntas frequentes do DNS do Azure | Microsoft Docs
description: Perguntas frequentes sobre o DNS do Azure
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
editor: 
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2017
ms.author: jonatul
ms.openlocfilehash: 9d786ce4d06ec95a647a755bed51f824e72ad04c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-dns-faq"></a>Perguntas frequentes do DNS do Azure

## <a name="about-azure-dns"></a>Sobre o DNS do Azure

### <a name="what-is-azure-dns"></a>O que é o DNS do Azure?

O sistema de nomes de domínio, ou DNS, é responsável por converter (ou seja, resolver) um nome do site ou serviço para seu endereço IP. O DNS do Azure é um serviço de hospedagem para domínios DNS, fornecendo resolução de nomes usando a infraestrutura do Microsoft Azure. Ao hospedar seus domínios no Azure, você pode gerenciar seus registros DNS usando as mesmas credenciais, APIs, ferramentas e cobrança que seus outros serviços do Azure.

Domínios DNS no DNS do Azure são hospedados na rede global do Azure de servidores de nomes DNS. Podemos usar a rede Anycast, para que cada consulta DNS seja atendida pelo servidor DNS mais próximo disponível. Isso fornece desempenho rápido e alta disponibilidade para seu domínio.

O serviço DNS do Azure baseia-se no Azure Resource Manager. Sendo assim, ele aproveita recursos do Resource Manager, como o controle de acesso baseado em função, os logs de auditoria e o bloqueio de recursos. Seus domínios e registros podem ser gerenciados por meio do portal do Azure, cmdlets do Azure PowerShell e a CLI do Azure de plataforma cruzada. Aplicativos que requerem gerenciamento automático de DNS podem se integrar com o serviço por meio da API REST e dos SDKs.

### <a name="how-much-does-azure-dns-cost"></a>Quanto custa o DNS do Azure?

O modelo de preço do DNS do Azure é baseado no número de zonas DNS hospedadas no DNS do Azure e o número de consultas DNS que eles recebem. Descontos são fornecidos com base no uso.

Para saber mais, veja a [página de preços do DNS do Azure](https://azure.microsoft.com/pricing/details/dns/).

### <a name="what-is-the-sla-for-azure-dns"></a>O que é o SLA para DNS do Azure?

Garantimos que as solicitações DNS válidas receberão uma resposta de pelo menos um servidor de nome DNS do Azure, no mínimo 99,99% do tempo.

Para saber mais, confira a [página de SLA do DNS do Azure](https://azure.microsoft.com/support/legal/sla/dns).

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>O que é uma 'zona DNS'? É a mesma coisa que um domínio DNS? 

Um domínio é um nome exclusivo no sistema de nomes de domínio, por exemplo, 'contoso.com'.

Uma zona DNS é usada para hospedar os registros DNS para um domínio específico. Por exemplo, o domínio "contoso.com" pode conter vários registros DNS, como ‘mail.contoso.com’ (para um servidor de email) e ‘www.contoso.com’ (para um site da Web). Essas seriam hospedadas na zona DNS 'contoso.com'.

Um nome de domínio é *apenas um nome*, enquanto que uma zona DNS é um recurso de dados que contém os registros de DNS para um nome de domínio. O Azure DNS permite hospedar uma zona DNS e gerenciar os registros DNS para um domínio no Azure. Ele também fornece servidores de nomes de DNS para resolver consultas de DNS da Internet.

### <a name="do-i-need-to-purchase-a-dns-domain-name-to-use-azure-dns"></a>Preciso comprar um nome de domínio DNS para usar o DNS do Azure? 

Não necessariamente.

Você não precisa comprar um domínio para hospedar uma zona DNS no DNS do Azure. É possível criar uma zona DNS a qualquer momento sem ser proprietário do nome de domínio. As consultas de DNS para esta zona somente resolverão se elas estiverem direcionadas para os servidores de nome de DNS do Azure atribuídos à zona.

Você precisará obter o nome de domínio se desejar vincular sua zona DNS na hierarquia DNS global; isso permite que consultas DNS de qualquer lugar no mundo encontrem sua zona DNS e sejam respondidos em seus registros DNS.

## <a name="azure-dns-features"></a>Recursos do DNS do Azure

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>O DNS do Azure dá suporte a roteamento de tráfego com base em DNS ou failover de ponto de extremidade?

O roteamento de tráfego com base em DNS e o failover de ponto de extremidade são fornecidos pelo Gerenciador de Tráfego do Azure. Isso é um serviço do Azure separado que pode ser usado junto com o DNS do Azure. Para saber mais, confira a [Visão Geral do Gerenciador de Tráfego](../traffic-manager/traffic-manager-overview.md).

O DNS do Azure só oferece suporte à hospedagem de domínios de DNS 'estáticos', em que cada consulta DNS para um determinado registro DNS sempre recebe a mesma resposta DNS.

### <a name="does-azure-dns-support-domain-name-registration"></a>O DNS do Azure dá suporte ao registro de nome de domínio?

Não. O DNS do Azure não dá suporte a compra de nomes de domínio. Se você deseja adquirir domínios, será necessário usar um registrador de nomes de domínio de terceiros. O registrador normalmente cobra uma pequena taxa anual. Os domínios, em seguida, podem ser hospedados no DNS do Azure para gerenciamento de registros DNS. Consulte [Delegar um domínio ao DNS do Azure](dns-domain-delegation.md) para obter detalhes.

Esse é um recurso que estamos acompanhando em nossa lista de pendências. Você pode usar nosso site de comentários para [registrar seu apoio para esse recurso](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar).

### <a name="does-azure-dns-support-private-domains"></a>O DNS do Azure oferece suporte a domínios 'privados'?
O suporte para domínios "privados" é implementado usando zonas DNS privadas.  Este recurso está disponível atualmente como uma versão prévia.  As zonas DNS privadas são gerenciadas usando as mesmas ferramentas que as zonas DNS do Azure para a Internet, mas só podem ser resolvidas de dentro das redes virtuais especificadas.  Consulte a [visão geral](private-dns-overview.md) para obter detalhes.

Para saber mais sobre outras opções de DNS internas no Azure, veja [Resolução de nomes para VMs e Instâncias de função](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

### <a name="does-azure-dns-support-dnssec"></a>O DNS do Azure oferece suporte a DNSSEC?

Não. Atualmente não há suporte para DNSSEC no DNS do Azure.

Esse é um recurso que estamos acompanhando em nossa lista de pendências. Você pode usar nosso site de comentários para [registrar seu apoio para esse recurso](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support).

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>O DNS do Azure dá suporte para transferências de zona (AXFR/IXFR)?

Não. Atualmente não há suporte para transferências de zona. As zonas DNS podem ser [importadas para o DNS do Azure usando a CLI do Azure](dns-import-export.md). Os registros de DNS podem ser gerenciados por meio do [portal de gerenciamento do DNS do Azure](dns-operations-recordsets-portal.md), nossa [API REST](https://docs.microsoft.com/powershell/module/azurerm.dns), [SDK](dns-sdk.md), [cmdlets do PowerShell](dns-operations-recordsets.md) ou [ferramenta CLI](dns-operations-recordsets-cli.md).

Esse é um recurso que estamos acompanhando em nossa lista de pendências. Você pode usar nosso site de comentários para [registrar seu apoio para esse recurso](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c).

### <a name="does-azure-dns-support-url-redirects"></a>O DNS do Azure oferece suporte a redirecionamentos de URL?

Não. Os serviços de redirecionamento de URL não são, na verdade, um serviço do DNS: eles funcionam no nível de HTTP, em vez de no nível de DNS. Alguns provedores de DNS incluem um serviço de redirecionamento de URL como parte da oferta geral. Atualmente, isso não tem o suporte do DNS do Azure.

Esse recurso é acompanhado em nossa lista de pendências. Você pode usar nosso site de comentários para [registrar seu apoio para esse recurso](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape).

## <a name="using-azure-dns"></a>Usando o DNS do Azure

### <a name="can-i-co-host-a-domain-using-azure-dns-and-another-dns-provider"></a>É possível co-hospedar um domínio usando o DNS do Azure e outro provedor de DNS?

Sim. O DNS do Azure oferece suporte a domínios de co-hospedagem com outros serviços de DNS.

Para fazer isso, você precisará modificar os registros de DNS para o domínio (que controlam quais provedores recebem consultas de DNS para o domínio) para apontar para os servidores de nome de ambos os provedores. Esses registros de DNS precisam ser modificados em 3 lugares: no DNS do Azure, no outro provedor e na zona pai (normalmente configuradas por meio do registrador de nomes de domínio). Para saber mais sobre delegação de DNS, veja [Delegação de domínio de DNS](dns-domain-delegation.md).

Além disso, você precisa garantir que os registros de DNS para o domínio estejam em sincronia entre os dois provedores de DNS. Atualmente não há suporte para transferências de zona DNS. Você precisa sincronizar os registros de DNS usando o [portal de gerenciamento do DNS do Azure](dns-operations-recordsets-portal.md), nossa [API REST](https://docs.microsoft.com/powershell/module/azurerm.dns), [SDK](dns-sdk.md), [cmdlets do PowerShell](dns-operations-recordsets.md) ou [ferramenta CLI](dns-operations-recordsets-cli.md).

### <a name="do-i-have-to-delegate-my-domain-to-all-4-azure-dns-name-servers"></a>Preciso delegar meu domínio para todos os 4 servidores de nome de DNS do Azure?

Sim. O DNS do Azure atribui 4 servidores de nomes para cada zona DNS, para isolamento de falhas e aumento da resiliência. Para se qualificar para o SLA de DNS do Azure, você precisará delegar seu domínio para todos os 4 servidores de nome.

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Quais são os limites de uso do DNS do Azure?

Os limites padrão abaixo se aplicam ao usar o DNS do Azure:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>Posso mover uma zona DNS do Azure entre grupos de recursos ou entre assinaturas?

Sim. As zonas DNS podem ser movidas entre grupos de recursos ou entre assinaturas.

Não há nenhum impacto nas consultas de DNS ao mover uma zona DNS. Os servidores de nomes atribuídos à zona permanecem os mesmos e as consultas de DNS são processadas normalmente em tudo.

Para saber mais e instruções sobre como mover as zonas DNS, veja [Mover recursos para um novo grupo de recursos ou assinatura](../azure-resource-manager/resource-group-move-resources.md).

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>Quanto tempo leva para as alterações de DNS entrarem em vigor?

As novas zonas DNS e registros DNS são normalmente refletidas nos servidores de nomes do DNS do Azure rapidamente, dentro de alguns segundos.

As alterações nos registros de DNS existentes podem demorar um pouco mais, mas ainda assim devem ser refletidas nos servidores de nomes de DNS do Azure dentro de 60 segundos. Nesse caso, o cache de DNS fora do DNS do Azure (por clientes de DNS e resolvedores recursivos do DNS) também pode afetar o tempo necessário para uma alteração de DNS ser eficaz. A duração do cache pode ser controlada usando a propriedade de vida útil (TTL) de cada conjunto de registros.

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>Como posso proteger minhas zonas DNS contra exclusão acidental?

O DNS do Azure é gerenciado usando o Resource Manager e utiliza recursos de controle de acesso de recursos que o Azure Resource Manager fornece. O controle de acesso baseado em função pode ser usado para controlar quais usuários têm acesso de leitura ou gravação para os conjuntos de zonas e registros DNS. Bloqueios de recursos podem ser aplicados para evitar a modificação ou exclusão acidental de conjuntos de zonas e registros DNS.

Para saber mais, veja [Proteção de zonas e registros DNS](dns-protect-zones-recordsets.md).

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Como posso configurar registros de SPF no DNS do Azure?

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="how-do-i-set-up-an-international-domain-name-idn-in-azure-dns"></a>Como posso configurar um nome de domínio internacional (IDN) no DNS do Azure?

Nomes de domínios internacionais (IDNs) funcionam por codificação de cada nome de DNS usando '[punycode](https://en.wikipedia.org/wiki/Punycode)'. As consultas de DNS são feitas usando esses nomes codificados para punycode.

Você pode configurar nomes de domínios internacionais (IDNs) no DNS do Azure primeiro convertendo o nome da zona ou nome do conjunto de registros para punycode. Atualmente não há suporte para conversão interna de/para punycode no DNS do Azure.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre DNS do Azure](dns-overview.md)
<br>
[Saiba mais sobre o uso do DNS do Azure para domínios privados](private-dns-overview.md)
<br>
[Saiba mais sobre zonas e registros DNS](dns-zones-records.md)
<br>
[Introdução ao DNS do Azure](dns-getstarted-portal.md)

