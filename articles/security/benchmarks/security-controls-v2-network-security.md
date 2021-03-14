---
title: Benchmark de segurança do Azure v2-segurança de rede
description: Segurança de rede do benchmark de segurança do Azure v2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: b47f833a3b47dae145aa18eb6eda0c6bd95f6689
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102042906"
---
# <a name="security-control-v2-network-security"></a>Controle de segurança v2: segurança de rede

A segurança de rede abrange controles para proteger e proteger redes do Azure. Isso inclui a proteção de redes virtuais, o estabelecimento de conexões privadas, a prevenção e a mitigação de ataques externos e a proteção do DNS.

Para ver o Azure Policy interno aplicável, consulte [os detalhes da iniciativa interna de conformidade de benchmark de segurança do Azure: segurança de rede](../../governance/policy/samples/azure-security-benchmark.md#network-security)

## <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementar a segurança para tráfego interno

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| NS-1 | 9,2, 9,4, 14,1, 14,2, 14,3 | AC-4, CA-3, SC-7 |

Verifique se todas as redes virtuais do Azure seguem um princípio de segmentação empresarial que se alinha aos riscos de negócios. Qualquer sistema que possa incorrer em risco maior para a organização deve ser isolado em sua própria rede virtual e suficientemente protegido com um NSG (grupo de segurança de rede) e/ou o Firewall do Azure.

Com base em seus aplicativos e estratégia de segmentação corporativa, restrinja ou permita o tráfego entre os recursos internos com base nas regras do grupo de segurança de rede. Para aplicativos específicos bem definidos (como um aplicativo de três camadas), isso pode ser uma abordagem altamente segura de "negar por padrão, permitir por exceção". Isso pode não ser bem dimensionado se você tiver muitos aplicativos e pontos de extremidade interagindo entre si. Você também pode usar o Firewall do Azure em circunstâncias em que o gerenciamento central é necessário em um grande número de segmentos empresariais ou spokes (em uma topologia hub/spoke).

Use a proteção de rede adaptável da central de segurança do Azure para recomendar configurações de grupo de segurança de rede que limitam portas e IPs de origem com base na referência a regras de tráfego de rede externa.

Use o Azure Sentinel para descobrir o uso de protocolos inseguros herdados, como SSL/TLSv1, SMBv1, LM/NTLMv1, wDigest, associações LDAP não assinadas e codificações fracas no Kerberos.

- [Como criar um grupo de segurança de rede com regras de segurança](../../virtual-network/tutorial-filter-network-traffic.md)

- [Como implantar e configurar o Firewall do Azure](../../firewall/tutorial-firewall-deploy-portal.md)

- [Proteção de rede adaptável na central de segurança do Azure](../../security-center/security-center-adaptive-network-hardening.md)

- [Pasta de trabalho de protocolos inseguros do Azure Sentinel](../../sentinel/quickstart-get-visibility.md#use-built-in-workbooks)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Gerenciamento de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Segurança e DevOps de aplicativos](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-2-connect-private-networks-together"></a>NS-2: conectar redes privadas juntas

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| NS-2 | N/D | CA-3, AC-17, MA-4 |

Use o Azure ExpressRoute ou a VPN (rede virtual privada) do Azure para criar conexões privadas entre os data centers do Azure e a infraestrutura local em um ambiente de colocação. As conexões do ExpressRoute não passam pela Internet pública e oferecem mais confiabilidade, velocidades mais rápidas e latências menores do que as conexões típicas da Internet. Para VPN ponto a site e VPN site a site, você pode conectar dispositivos ou redes locais a uma rede virtual usando qualquer combinação dessas opções de VPN e do Azure ExpressRoute.

Para conectar duas ou mais redes virtuais no Azure juntas, use o emparelhamento de rede virtual ou o link privado. O tráfego de rede entre redes virtuais emparelhadas é privado e é mantido na rede de backbone do Azure.

- [Quais são os modelos de conectividade do ExpressRoute](../../expressroute/expressroute-connectivity-models.md)

- [Visão geral da VPN do Azure](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

- [Emparelhamento de rede virtual](../../virtual-network/virtual-network-peering-overview.md)

- [Link Privado do Azure](../../private-link/private-link-service-overview.md)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Gerenciamento de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Segurança e DevOps de aplicativos](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: estabelecer o acesso de rede privada aos serviços do Azure

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| NS-3 | 14,1 | AC-4, CA-3, SC-7 |

Use o link privado do Azure para habilitar o acesso privado aos serviços do Azure de suas redes virtuais, sem cruzar a Internet. Em situações em que o link privado do Azure ainda não está disponível, use pontos de extremidade de serviço de rede virtual do Azure. Os pontos de extremidade de serviço de rede virtual do Azure fornecem acesso seguro aos serviços por meio de uma rota otimizada na rede de backbone do Azure.

O acesso privado é uma medida aprofundada de defesa adicional, além da segurança de autenticação e tráfego oferecida pelos serviços do Azure.

- [Entender o link privado do Azure](../../private-link/private-link-overview.md)

- [Entender os pontos de extremidade de serviço de rede virtual](../../virtual-network/virtual-network-service-endpoints-overview.md)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Gerenciamento de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Segurança e DevOps de aplicativos](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

## <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: proteger aplicativos e serviços de ataques de rede externa

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| NS-4 | 9,5, 12,3, 12,9 | SC-5, SC-7 |

Proteja os recursos do Azure contra ataques de redes externas, incluindo ataques de DDoS (negação de serviço distribuído), ataques específicos de aplicativos e tráfego de Internet não solicitado e potencialmente mal-intencionado. O Azure inclui recursos nativos para isso:
-   Use o Firewall do Azure para proteger aplicativos e serviços contra tráfego potencialmente mal-intencionado da Internet e de outros locais externos. 

-   Use os recursos do WAF (firewall do aplicativo Web) no gateway Aplicativo Azure, na porta frontal do Azure e na CDN (rede de distribuição de conteúdo) do Azure para proteger seus aplicativos, serviços e APIs contra ataques de camada de aplicativo.

-   Proteja seus ativos contra ataques de DDoS habilitando a proteção padrão de DDoS em suas redes virtuais do Azure.
-   Use a central de segurança do Azure para detectar riscos de configuração incorreta relacionados ao acima.

- [Documentação do firewall do Azure](../../firewall/index.yml)

- [Como implantar o Azure WAF](../../web-application-firewall/overview.md)

- [Gerenciar a Proteção contra DDoS do Azure Standard usando o portal do Azure](../../ddos-protection/manage-ddos-protection.md)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Gerenciamento de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Segurança e DevOps de aplicativos](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: implantar sistemas de detecção de intrusões/prevenção de intrusão (IDS/IPS)

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| NS-5 | 12,6, 12,7 | SI-4 |

Use a filtragem baseada em inteligência de ameaças do firewall do Azure para alertar e/ou bloquear o tráfego de e para domínios e endereços IP mal-intencionados conhecidos. Os endereços IP e os domínios são originados do feed de inteligência de ameaças da Microsoft. Quando a inspeção de carga é necessária, você pode implantar um sistema de detecção de intrusão/IPS de terceiros do Azure Marketplace com recursos de inspeção de conteúdo. Como alternativa, você pode usar IDS/IPS baseados em host ou uma solução de EDR (detecção e resposta de ponto de extremidade) baseada em host em conjunto com ou em vez de IDS/IPS baseados em rede.

Observação: se você tiver uma regulamentação ou outro requisito para uso de IDS/IPS, verifique se ele sempre está ajustado para fornecer alertas de alta qualidade para sua solução SIEM. 

- [Como implantar o Firewall do Azure](../../firewall/tutorial-firewall-deploy-portal.md)

- [O Azure Marketplace inclui recursos de IDS de terceiros](https://azuremarketplace.microsoft.com/marketplace?search=IDS)

- [Recurso do Microsoft defender para ponto de extremidade](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Gerenciamento de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Segurança e DevOps de aplicativos](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-6-simplify-network-security-rules"></a>NS-6: simplificar as regras de segurança de rede

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| NS-6 | 1.5 | IA-4 |

Simplifique as regras de segurança de rede aproveitando as marcas de serviço e os ASGs (grupos de segurança de aplicativo).

Use marcas de serviço de rede virtual para definir controles de acesso de rede em grupos de segurança de rede ou no firewall do Azure. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço no campo de origem ou de destino de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

Você também pode usar grupos de segurança de aplicativo para ajudar a simplificar a configuração de segurança complexa. Em vez de definir a política com base em endereços IP explícitos em grupos de segurança de rede, os grupos de segurança de aplicativo permitem que você configure a segurança de rede como uma extensão natural da estrutura de um aplicativo, permitindo que você agrupe máquinas virtuais e defina políticas de segurança de rede com base nesses grupos.

- [Entender e usar marcas de serviço](../../virtual-network/service-tags-overview.md)

- [Entender e usar grupos de segurança de aplicativos](../../virtual-network/network-security-groups-overview.md#application-security-groups)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Gerenciamento de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Segurança e DevOps de aplicativos](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: serviço de nomes de domínio seguro (DNS)

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| NS-7 | N/D | SC-20, SC-21 |

Siga as práticas recomendadas para a segurança do DNS para mitigar ataques comuns, como pendente DNS, interduções de DNS, ataques de envenenamento e falsificação de DNS, etc.

Quando o DNS do Azure é usado como seu serviço DNS autoritativo, verifique se as zonas e os registros DNS estão protegidos contra modificações acidentais ou mal-intencionadas usando os bloqueios de recursos e RBAC do Azure.

- [Visão geral do DNS do Azure](../../dns/dns-overview.md)

- [Guia de implantação do DNS (sistema de nomes de domínio) seguro](https://csrc.nist.gov/publications/detail/sp/800-81/2/final)

- [Impedir entradas DNS pendente e evitar subdomínio tomada](../fundamentals/subdomain-takeover.md)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Gerenciamento de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Segurança e DevOps de aplicativos](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)
