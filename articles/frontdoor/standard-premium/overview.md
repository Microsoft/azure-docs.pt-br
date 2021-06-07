---
title: Azure Front Door Standard/Premium | Microsoft Docs
description: Este artigo fornece uma visão geral do Azure Front Door Standard/Premium.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: overview
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 5c3ae5c7b1c45d170548f6fa00481094117e1737
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105937355"
---
# <a name="what-is-azure-front-door-standardpremium-preview"></a>O que é o Azure Front Door Standard/Premium (Versão Prévia)?

> [!IMPORTANT]
> Esta documentação é específica para o Azure Front Door Standard/Premium (Versão Prévia). Em busca de informações sobre o Azure Front Door? Veja a [Documentação do Azure Front Door](../front-door-overview.md).

O Azure Front Door Standard/Premium é uma CDN de nuvem moderna, rápida, confiável e segura que usa a rede de borda global da Microsoft e se integra à proteção inteligente contra ameaças. Ele combina as funcionalidades do Azure Front Door, o padrão da CDN (Rede de Distribuição de Conteúdo) do Azure e o WAF (Firewall de Aplicativo Web) do Azure em uma única plataforma CDN de nuvem segura.

Com o Azure Front Door Standard/Premium, você pode transformar seus aplicativos empresariais e consumidores globais em aplicativos modernos e de alto desempenho personalizados com um conteúdo que atinja um público-alvo global na borda da rede perto do usuário. Ele também permite que seu aplicativo se expanda sem aquecimento enquanto se beneficia do balanceamento de carga HTTP global com failover instantâneo.

   :::image type="content" source="../media/overview/front-door-overview.png" alt-text="Arquitetura do Azure Front Door Standard/Premium" lightbox="../media/overview/front-door-overview-expanded.png":::

O Azure Front Door Standard/Premium funciona na Camada 7 (Camada HTTP/HTTPS) e usa o protocolo anycast com TCP de divisão e a rede global da Microsoft para aprimorar a conectividade global. Com base no método de roteamento personalizado que usa o conjunto de regras, é possível garantir que o Azure Front Door roteará as suas solicitações de cliente para a origem disponível mais rápida e disponível. Uma origem de aplicativo é qualquer serviço voltado para a Internet hospedado dentro ou fora do Azure. O Azure Front Door Standard/Premium oferece uma variedade de métodos de roteamento de tráfego e opções de monitoramento de integridade de origem para atender às diferentes necessidades dos aplicativos e cenários de failover automático. De maneira semelhante ao Gerenciador de Tráfego, o Front Door é resiliente a falhas, incluindo falhas de uma região inteira do Azure.

O Azure Front Door também protege seu aplicativo nas bordas com proteção do Firewall de Aplicativo Web integrada, a Proteção contra Bots e proteção interna de DDoS (negação de serviço distribuído) de camada 3/camada 4. Ele também protege seus back-ends privados com o serviço de link privado. O Azure Front Door oferece a você a melhor segurança da Microsoft em escala global na prática.  

>[!NOTE]
> O Azure fornece um conjunto de soluções de balanceamento de carga totalmente gerenciadas para seus cenários.
>
> * Se você quiser realizar um roteamento global baseado em DNS e **não** tiver os requisitos para encerramento de protocolo TLS ("descarregamento de SSL") ou para processamento de camada de aplicativo por solicitação HTTP/HTTPS, examine [Gerenciador de Tráfego](../../traffic-manager/traffic-manager-overview.md).
> * Se você quiser balancear a carga entre os servidores em uma região na camada de aplicativo, examine [Gateway de Aplicativo](../../application-gateway/overview.md)
> * Para fazer o balanceamento de carga de camada de rede, examine [Load Balancer](../../load-balancer/load-balancer-overview.md).
>
> Os cenários de ponta a ponta podem se beneficiar da combinação dessas soluções, conforme necessário.
> Para obter uma comparação das opções de balanceamento de carga do Azure, confira [Visão geral das opções de balanceamento de carga no Azure](/azure/architecture/guide/technology-choices/load-balancing-overview).

> [!IMPORTANT]
> O Azure Front Door Standard/Premium está atualmente em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="why-use-azure-front-door-standardpremium-preview"></a>Por que usar o Azure Front Door Standard/Premium (versão prévia)?

O Azure Front Door Standard/Premium fornece uma plataforma unificada que atende à aceleração dinâmica e estática com a integração de segurança interna pronta para uso e um modelo de preços simples e previsível. O Front Door também permite que você defina, gerencie e monitore o roteamento global para seu aplicativo.

Principais recursos incluídos com o Azure Front Door Standard/Premium (versão prévia):

- Desempenho de aplicativo acelerado usando o protocolo anycast **[baseado em TCP dividido](../front-door-routing-architecture.md#splittcp)** .

- Monitoramento de **[investigação de integridade](concept-health-probes.md)** inteligente e balanceamento de carga entre **[origens](concept-origin.md)** .

- Defina seu **[domínio personalizado](how-to-add-custom-domain.md)** com validação de domínio flexível.

- Segurança de aplicativo com o **[WAF (Firewall de Aplicativo Web)](../../web-application-firewall/afds/afds-overview.md)** integrado.

- Descarregamento de SSL e **[gerenciamento de certificado](how-to-configure-https-custom-domain.md)** integrado.

- Proteja suas origens com o **[Link Privado](concept-private-link.md)** .  

- Roteamento e otimizações de tráfego personalizáveis por meio de **[Conjunto de Regras](concept-rule-set.md)** .

- **[Relatórios internos](how-to-reports.md)** com um painel tudo em um para os padrões do Front Door e de segurança.

- **[Monitoramento em tempo real](how-to-monitor-metrics.md)** e alertas que se integram ao Monitoramento do Azure.

- **[Registro em log](how-to-logs.md)** para cada solicitação do Front Door e investigações de integridade com falha.

- Suporte nativo de conectividade IPv6 de ponta a ponta e protocolo HTTP/2.

## <a name="pricing"></a>Preços

O Azure Front Door Standard/Premium tem dois SKUs: Standard e Premium. Confira [Comparação de camadas](tier-comparison.md). Para obter informações sobre preços, consulte [Preços do Front Door](https://azure.microsoft.com/pricing/details/frontdoor/). 

## <a name="whats-new"></a>Novidades

Assine o RSS feed e veja as atualizações mais recentes dos recursos do Azure Front Door na página [Atualizações do Azure](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Front%20Door).

## <a name="next-steps"></a>Próximas etapas

* Saiba como [criar um Front Door](create-front-door-portal.md).
