---
title: O que é uma rede de distribuição de conteúdo (CDN)? - Azure | Microsoft Docs
description: Saiba o que é a CDN (Rede de Distribuição de Conteúdo) do Azure e como usá-la para fornecer conteúdo de alta largura de banda.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 866e0c30-1f33-43a5-91f0-d22f033b16c6
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 05/09/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 6aaa98bea5e6cf1bdffc3dbe28e42e9545a626ea
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593618"
---
# <a name="what-is-a-content-delivery-network-on-azure"></a>O que é uma rede de distribuição de conteúdo no Azure?
Uma CDN (rede de distribuição de conteúdo) é uma rede distribuída de servidores que pode fornecer conteúdo da Web para os usuários com eficiência. As CDNs armazenam conteúdo armazenado em cache em servidores de borda em localizações de ponto de presença (POP) que estão próximas aos usuários finais, para minimizar a latência. 

A CDN oferece aos desenvolvedores uma solução global de fornecimento rápido de conteúdo de alta largura de banda para usuários armazenando em cache o conteúdo em nós físicos estrategicamente posicionados em todo o mundo. A CDN do Azure também pode acelerar o conteúdo dinâmico, que não pode ser armazenado em cache, aproveitando várias otimizações de rede usando POPs de CDN. Por exemplo, a otimização da rota para ignorar o Border Gateway Protocol (BGP).

As vantagens de usar a CDN do Azure para entregar ativos de site da Web incluem:

* Melhor desempenho e experiência aprimorada de usuário para usuários finais, especialmente ao se usar aplicativos em que várias viagens de ida e volta são necessárias para carregar o conteúdo.
* Grande dimensionamento para lidar melhor com alta carga instantânea, como no início de um evento de lançamento de produto.
* Distribuição de solicitações de usuário e fornecimento de conteúdo de servidores de borda, assim menos tráfego é enviado ao servidor de origem.

Para ver uma lista de locais de nó CDN atuais, confira [Locais POP de CDN do Azure](cdn-pop-locations.md).

## <a name="how-it-works"></a>Como ele funciona
![Visão geral da CDN](./media/cdn-overview/cdn-overview.png)

1. Uma usuária (Brenda) solicita um arquivo (também chamado de ativo) usando uma URL com um nome de domínio especial, como _&lt;nome do ponto de extremidade&gt;_ .azureedge.net. Esse nome pode ser um nome de host do ponto de extremidade ou um domínio personalizado. O DNS encaminha a solicitação para o local POP com o melhor desempenho, que é geralmente o POP geograficamente mais próximo ao usuário.
    
2. Se os servidores de borda no POP não tiverem o arquivo em seu cache, as solicitações de POP solicitarão o arquivo do servidor de origem. O servidor de origem pode ser um Aplicativo Web do Azure, o Serviço de Nuvem do Azure, a conta de Armazenamento do Azure ou qualquer servidor Web acessível publicamente.
   
3. O servidor de origem retorna o arquivo para um servidor de borda em POP.
    
4. O servidor de borda em POP armazena o arquivo em cache e o retorna à solicitante original (Brenda). O arquivo permanece em cache no servidor de borda em POP até o tempo de vida (TTL) especificado por seus cabeçalhos HTTP expirar. Se o servidor de origem não especificar uma vida útil, a vida útil padrão será de sete dias.
    
5. Usuários adicionais podem solicitar o mesmo arquivo, usando a mesma URL que Brenda usou e também podem ser direcionados para o mesmo POP.
    
6. Se a vida útil do arquivo ainda não tiver expirado, o servidor de borda POP retornará os arquivos diretamente do cache. Esse processo resulta em uma experiência de usuário mais rápida e responsiva.

## <a name="requirements"></a>Requisitos
Para usar a CDN do Azure, você deve ter pelo menos uma assinatura do Azure. Você também precisa criar pelo menos um perfil CDN, que é uma coleção de pontos de extremidade CDN. Cada ponto de extremidade CDN representa uma configuração específica do comportamento de entrega de conteúdo e o acesso. Para organizar seus pontos de extremidade CDN por domínio de Internet, aplicativo Web ou algum outro critério, use vários perfis. Como os [preços do Azure CDN](https://azure.microsoft.com/pricing/details/cdn/) são aplicados no nível do perfil CDN, você deve criar vários perfis CDN se quiser usar uma combinação de tipos de preços. Para obter informações sobre a estrutura de cobrança da CDN do Azure, consulte [Noções básicas da cobrança da CDN do Azure](cdn-billing.md).

### <a name="limitations"></a>Limitações
Cada assinatura do Azure tem limites padrão para os seguintes recursos:
 - O número de perfis CDN que podem ser criados.
 - O número de pontos de extremidade que podem ser criados em um perfil CDN. 
 - O número de domínios personalizados que podem ser mapeados para um ponto de extremidade.

Para saber mais informações sobre limites de assinatura de CDN, confira [Limites de CDN](https://docs.microsoft.com/azure/azure-subscription-service-limits).
    
## <a name="azure-cdn-features"></a>Recursos da CDN do Azure
A CDN do Azure oferece os seguintes recursos principais:

- [Aceleração de site dinâmica](cdn-dynamic-site-acceleration.md)
- [Regras de cache da CDN](cdn-caching-rules.md)
- [Suporte de domínio personalizado HTTPS](cdn-custom-ssl.md)
- [Logs de diagnóstico do Azure](cdn-azure-diagnostic-logs.md)
- [Compactação de arquivos](cdn-improve-performance.md)
- [Filtragem geográfica](cdn-restrict-access-by-country.md)

Para obter uma lista completa de recursos que dá suporte a cada produto da CDN do Azure, confira [Comparar recursos de produto da CDN do Azure](cdn-features.md).

## <a name="next-steps"></a>Próximas etapas
- Para começar com a CDN, confira [Criar um perfil e ponto de extremidade da CDN do Azure](cdn-create-new-endpoint.md).
- Gerencie seus pontos de extremidade CDN por meio do [Portal do Microsoft Azure](https://portal.azure.com) ou do [PowerShell](cdn-manage-powershell.md).
- Saiba como automatizar a CDN do Azure com [.NET](cdn-app-dev-net.md) ou [Node.js](cdn-app-dev-node.md).
- Para ver a CDN do Azure em ação, assista aos [Vídeos da CDN do Azure](https://azure.microsoft.com/resources/videos/index/?services=cdn&sort=newest).
- Para obter informações sobre os recursos mais recentes da CDN do Azure, consulte o [blog da CDN do Azure](https://azure.microsoft.com/blog/tag/azure-cdn/).
