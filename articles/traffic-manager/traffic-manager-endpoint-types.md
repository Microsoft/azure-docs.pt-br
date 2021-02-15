---
title: Tipos de Ponto de Extremidade do Gerenciador de Tráfego | Microsoft Docs
description: Este artigo explica os diferentes tipos de pontos de extremidade que podem ser usados com o Gerenciador de Tráfego do Azure
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/21/2021
ms.author: duau
ms.openlocfilehash: 7686f2f97da0113704216dcab741c063a80d3136
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99051220"
---
# <a name="traffic-manager-endpoints"></a>Pontos de extremidade do Gerenciador de Tráfego

O Gerenciador de Tráfego do Microsoft Azure permite controlar como o tráfego de rede é distribuído para implantações de aplicativos executados em diferentes datacenters. Configure cada implantação de aplicativo como um "ponto de extremidade" no Gerenciador de Tráfego. Quando o Gerenciador de Tráfego recebe uma solicitação DNS, ele escolhe um ponto de extremidade disponível para retornar na resposta DNS. O Gerenciador de Tráfego baseia a escolha no status atual do ponto de extremidade e o método de roteamento de tráfego. Para obter mais informações, consulte [como funciona o Gerenciador de tráfego](traffic-manager-how-it-works.md).

O Gerenciador de Tráfego dá suporte a três tipos de ponto de extremidade:

* Os **pontos de extremidade do Azure** são usados para os serviços hospedados no Azure.
* Os **pontos de extremidade externos** são usados para endereços IPv4/IPv6, FQDNs ou para serviços hospedados fora do Azure. Esses serviços podem estar no local ou com um provedor de hospedagem diferente.
* Os **pontos de extremidade aninhados** são usados para combinar os perfis do Gerenciador de Tráfego para criar esquemas de roteamento de tráfego mais flexíveis para dar suporte às necessidades de implantações maiores e mais complexas.

Não há nenhuma restrição sobre como os pontos de extremidade de diferentes tipos são combinados em um único perfil do Gerenciador de tráfego. Cada perfil pode conter qualquer combinação de tipos de ponto de extremidade.

As seções a seguir descrevem cada tipo de ponto de extremidade com mais detalhes.

## <a name="azure-endpoints"></a>pontos de extremidade do Azure

Os pontos de extremidade do Azure são usados para serviços baseados no Azure no Gerenciador de Tráfego. Há suporte para os seguintes tipos de recursos do Azure:

* Serviços de nuvem PaaS.
* Aplicativos Web
* Slots do aplicativo Web
* Recursos PublicIPAddress (que podem ser conectados às VMs diretamente ou por meio de um Azure Load Balancer). O publicIpAddress deve ter um nome DNS atribuído para ser usado no perfil do Gerenciador de Tráfego.

Os recursos de PublicIPAddress são recursos do Azure Resource Manager. Eles não existem no modelo de implantação clássico e só têm suporte nas experiências de Azure Resource Manager do Gerenciador de tráfego. Os outros tipos de ponto de extremidade têm suporte por meio do Gerenciador de Recursos e o modelo de implantação clássico.

Ao usar os pontos de extremidade do Azure, o Gerenciador de tráfego detecta quando um aplicativo Web é interrompido e iniciado. Esse status é refletido no status de ponto de extremidade. Consulte detalhes em [Monitoramento do ponto de extremidade do Gerenciador de Tráfego](traffic-manager-monitoring.md#endpoint-and-profile-status). Quando o serviço subjacente é interrompido, o Gerenciador de tráfego não faz verificações de integridade do ponto de extremidade ou direciona o tráfego para o ponto de extremidade. Nenhum evento de cobrança do Gerenciador de Tráfego ocorre para a instância parada. Quando o serviço é reiniciado, a cobrança é retomada e o ponto de extremidade está qualificado para receber tráfego. Essa detecção não se aplica a pontos de extremidade PublicIpAddress.

## <a name="external-endpoints"></a>pontos de extremidade externos

Os pontos de extremidade externos são usados para endereços IPv4/IPv6, FQDNs ou para serviços fora do Azure. O uso dos pontos de extremidade nos endereços IPv4/IPv6 permite que o gerenciador de tráfego verifique a integridade dos pontos de extremidade, sem exigir um nome DNS para eles. Como resultado, o Gerenciador de Tráfego pode responder a consultas com registros A/AAAA ao retornar o ponto de extremidade em uma resposta. Os serviços fora do Azure podem incluir um serviço hospedado localmente ou em um provedor diferente. Pontos de extremidade externos podem ser usados sozinhos ou combinados com os Pontos de Extremidade do Azure no mesmo perfil do Gerenciador de Tráfego. A exceção é para pontos de extremidade que são especificados como endereços IPv4 ou IPv6, que só podem ser pontos de extremidade externos. Combinar pontos de extremidade do Azure com pontos de extremidade Externos permite vários cenários:

* Fornecer redundância aumentada para um aplicativo local existente, em um modelo de failover ativo-ativo ou ativo-passivo usando o Azure. 
* Rotear o tráfego para pontos de extremidade que não têm um nome DNS associado a eles. Também reduz a latência de pesquisa de DNS geral, removendo a necessidade de executar uma segunda consulta DNS para obter um endereço IP de um nome DNS retornado.
* Reduza a latência de aplicativos para usuários em todo o mundo, estenda um aplicativo local existente para outros locais geográficos no Azure. Para obter mais informações, consulte [Roteamento de tráfego por “Desempenho” do Gerenciador de Tráfego](traffic-manager-routing-methods.md#performance).
* Forneça mais capacidade para um aplicativo local existente, continuamente ou como uma solução de "intermitência para nuvem" para atender a um pico de demanda usando o Azure.

Em alguns casos, é útil usar pontos de extremidade externos para fazer referência aos serviços do Azure. Consulte as [perguntas frequentes](traffic-manager-faqs.md#traffic-manager-endpoints) para obter exemplos. As verificações de integridade são cobradas na taxa de pontos de extremidade do Azure, não na taxa de pontos de extremidade externos. Ao contrário dos pontos de extremidade do Azure, se você parar ou excluir o serviço subjacente, a cobrança de verificação de integridade continuará. A cobrança será interrompida quando você desabilitar ou excluir o ponto de extremidade no Gerenciador de tráfego.

## <a name="nested-endpoints"></a>pontos de extremidade aninhados

Os pontos de extremidade aninhados combinam vários perfis do Gerenciador de tráfego para criar esquemas flexíveis de roteamento de tráfego para dar suporte às necessidades de implantações maiores e complexas. Com os pontos de extremidade aninhados, um perfil “filho” é adicionado como um ponto de extremidade a um perfil “pai”. Os perfis pai e filho podem conter outros pontos de extremidade de qualquer tipo, incluindo outros perfis aninhados. 

Para obter mais informações, consulte [perfis aninhados do Gerenciador de Tráfego](traffic-manager-nested-profiles.md).

## <a name="web-apps-as-endpoints"></a>Aplicativos Web como pontos de extremidade

Algumas considerações se aplicam ao configurar aplicativos Web como pontos de extremidade no Gerenciador de tráfego:

1. Somente Aplicativos Web na SKU “Standard” ou superior estão qualificados para o uso com o Gerenciador de Tráfego. Falha nas tentativas de adicionar um Aplicativo Web de uma SKU inferior. Fazer o downgrade da SKU de um aplicativo Web existente resulta em o Gerenciador de tráfego não enviar mais tráfego para esse Aplicativo Web. Para obter mais informações sobre os planos com suporte, consulte os [planos do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/plans/)
2. Quando um ponto de extremidade recebe uma solicitação HTTP, ele usa o cabeçalho “host” na solicitação para determinar qual aplicativo Web deve atender à solicitação. O cabeçalho de host contém o nome DNS usado para iniciar a solicitação, por exemplo, ' contosoapp.azurewebsites.net '. Para usar um nome DNS diferente com seu Aplicativo Web, o nome DNS deve ser registrado como um nome de domínio personalizado para o Aplicativo. Ao adicionar um ponto de extremidade do Aplicativo Web como um ponto de extremidade do Azure, o nome DNS do perfil do Gerenciador de Tráfego é registrado automaticamente para o Aplicativo. Esse registro é removido automaticamente quando o ponto de extremidade é excluído.
3. Cada perfil do Gerenciador de Tráfego pode ter, no máximo, um ponto de extremidade do aplicativo Web de cada região do Azure. Para contornar essa restrição, você pode configurar um Aplicativo Web como um ponto de extremidade Externo. Consulte mais informações em [Perguntas Frequentes](traffic-manager-faqs.md#traffic-manager-endpoints).

## <a name="enabling-and-disabling-endpoints"></a>Habilitando e desabilitando os pontos de extremidade

Desabilitar um ponto de extremidade no Gerenciador de Tráfego pode ser útil para remover temporariamente o tráfego de um ponto de extremidade que esteja no modo de manutenção ou sendo reimplantado. Quando o ponto de extremidade estiver em execução novamente, ele poderá ser reabilitado.

Você pode habilitar ou desabilitar pontos de extremidade por meio do portal do Gerenciador de tráfego, do PowerShell, da CLI ou da API REST.

> [!NOTE]
> Desabilitar um ponto de extremidade do Azure não tem nenhuma relação com o estado de implantação no Azure. Um serviço do Azure, como uma VM ou aplicativo Web, permanece em execução e capaz de receber tráfego mesmo quando desabilitado no Gerenciador de Tráfego. O tráfego pode ser tratado diretamente para a instância do serviço, em vez de por meio do nome DNS do perfil do Gerenciador de Tráfego. Para obter mais informações, consulte [como o Gerenciador de Tráfego funciona](traffic-manager-how-it-works.md).

A qualificação atual de cada ponto de extremidade para receber tráfego depende dos seguintes fatores:

* O status do perfil (habilitado/desabilitado)
* O status de ponto de extremidade (habilitado/desabilitado)
* Os resultados das verificações de integridade para o ponto de extremidade

Para obter detalhes, consulte [Monitoramento do ponto de extremidade do Gerenciador de Tráfego](traffic-manager-monitoring.md#endpoint-and-profile-status).

> [!NOTE]
> Como o Gerenciador de Tráfego funciona no nível do DNS, ele não consegue influenciar as conexões existentes com qualquer ponto de extremidade. Quando um ponto de extremidade não estiver disponível, o Gerenciador de Tráfego direcionará novas conexões para outro ponto de extremidade disponível. No entanto, o host atrás do ponto de extremidade desabilitado ou não íntegro pode continuar recebendo tráfego por meio de conexões existentes até que as sessões sejam encerradas. Os aplicativos devem limitar a duração da sessão para permitir a drenagem do tráfego de conexões existentes.

Se todos os pontos de extremidade em um perfil forem desabilitados ou se o próprio perfil for desabilitado, o Gerenciador de tráfego enviará uma resposta "NXDOMAIN" para uma nova consulta DNS.

## <a name="faqs"></a>Perguntas frequentes

* [Posso usar o Gerenciador de Tráfego com pontos de extremidade de várias assinaturas?](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions)

* [Posso usar o Gerenciador de Tráfego com os slots de “Preparo” do Serviço de Nuvem?](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-cloud-service-staging-slots)

* [O Gerenciador de Tráfego dá suporte aos pontos de extremidade IPv6?](./traffic-manager-faqs.md#does-traffic-manager-support-ipv6-endpoints)

* [Posso usar o Gerenciador de Tráfego com mais de um Aplicativo Web na mesma região?](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region)

* [Como faço para mover os pontos de extremidade do Azure do meu perfil do Gerenciador de Tráfego para um grupo de recursos diferente?](./traffic-manager-faqs.md#how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group-or-subscription)

## <a name="next-steps"></a>Próximas etapas

* Saiba [como funciona o Gerenciador de tráfego](traffic-manager-how-it-works.md).
* Saiba mais sobre [o monitoramento de ponto de extremidade e failover automático](traffic-manager-monitoring.md)do Gerenciador de Tráfego.
* Saiba mais sobre os [métodos de roteamento de tráfego](traffic-manager-routing-methods.md)do Gerenciador de Tráfego.