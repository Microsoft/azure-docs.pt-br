---
title: Visão geral do exemplo de blueprint do Azure Security Benchmark Foundation
description: Visão geral e arquitetura do exemplo de blueprint do Azure Security Benchmark Foundation.
ms.date: 02/17/2020
ms.topic: sample
ms.openlocfilehash: ed497eff85e07b6a51939907bc751f3b40c99b30
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101741864"
---
# <a name="overview-of-the-azure-security-benchmark-foundation-blueprint-sample"></a>Visão geral do exemplo de blueprint do Azure Security Benchmark Foundation

O exemplo de blueprint do Azure Security Benchmark Foundation fornece um conjunto de padrões de infraestrutura de linha de base para ajudar você a criar um ambiente do Azure seguro e em conformidade. O blueprint ajuda você a implantar uma arquitetura baseada em nuvem que oferece soluções para os cenários com requisitos de conformidade ou de credenciamento. Este exemplo de blueprint de base é uma extensão do [blueprint de exemplo do Azure Security Benchmark](../azure-security-benchmark.md). Ele implanta e configura limites de rede, monitoramento e outros recursos em alinhamento com as políticas e outros verificadores de integridade definidos no [Azure Security Benchmark](../../../../security/benchmarks/index.yml).

## <a name="architecture"></a>Arquitetura

O ambiente de base criado por esse exemplo de blueprint é baseado nas entidades de segurança de arquitetura de um [modelo hub-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke).
O blueprint implanta uma rede virtual de hub que contém recursos comuns e compartilhados, serviços e artefatos como o Azure Bastion, gateway e firewall para conectividade, sub-redes de gerenciamento e de jumpbox para hospedar infraestrutura adicional/opcional de gerenciamento, manutenção, administração e conectividade. Uma ou mais redes virtuais de spoke são implantadas para cargas de trabalho de aplicativo host como serviços Web e de banco de dados. As redes virtuais de spoke são conectadas à rede virtual de hub usando o emparelhamento de rede virtual do Azure para uma conectividade contínua e segura. É possível adicionar spokes reatribuindo o blueprint de exemplo ou criando manualmente uma rede virtual do Azure e emparelhando-a à rede virtual do hub. Toda a conectividade externa às redes virtuais de spoke e sub-redes é configurada para ser roteada pela rede virtual de hub e via jump boxes de firewall, de gateway e de gerenciamento.

:::image type="content" source="../../media/azure-security-benchmark-foundation/architecture.png" alt-text="Diagrama de arquitetura do exemplo de blueprint do Azure Security Benchmark Foundation" border="false":::

O blueprint implanta vários serviços do Azure para fornecer uma fundação segura, monitorada e pronta para empresas. Esse ambiente é composto de:

- [Logs do Azure Monitor](../../../../azure-monitor/logs/data-platform-logs.md) e uma conta de Armazenamento do Azure para assegurar que logs de recurso, logs de atividade, métricas e fluxos de tráfego de rede sejam armazenados em uma localização central para facilidade de consulta, análise, arquivamento e alerta.
- A [Central de Segurança do Azure](../../../../security-center/security-center-introduction.md) (versão padrão) fornece proteção contra ameaças para recursos do Azure.
- A [Rede Virtual do Azure](../../../../virtual-network/virtual-networks-overview.md) no hub dá suporte a sub-redes para conectividade com uma rede local, uma pilha de entrada e saída para conectividade com a Internet e sub-redes opcionais para implantação de serviços administrativos ou de gerenciamento adicionais. A Rede Virtual no spoke contém sub-redes para hospedagem de cargas de trabalho de aplicativo. Sub-redes adicionais podem ser criadas após a implantação conforme necessário para darem suporte aos cenários aplicáveis.
- [Firewall do Azure](../../../../firewall/overview.md), para rotear todo o tráfego de Internet de saída e para permitir o tráfego de Internet de entrada via jumpbox. (As regras de firewall padrão bloqueiam todo o tráfego de Internet de entrada e saída e é preciso configurar as regras após a implantação, conforme aplicável.)
- [NSGs](../../../../virtual-network/network-security-group-how-it-works.md) (grupos de segurança de rede) atribuídos a todas as sub-redes (exceto a sub-redes de propriedade de serviços, como o Azure Bastion, o Gateway e o Firewall do Azure) configurados para bloquear todo o tráfego de Internet de entrada e de saída.
- [Grupos de segurança de aplicativo](../../../../virtual-network/application-security-groups.md) para habilitar o agrupamento de máquinas virtuais do Azure a fim de aplicar políticas de segurança de rede comuns.
- [Tabelas de rotas](../../../../virtual-network/manage-route-table.md) para rotear todo o tráfego de Internet de saída de sub-redes por meio do firewall. (As regras do Firewall do Azure e de NSG precisarão ser configuradas após a implantação para uma conectividade aberta.)
- [Observador de Rede do Azure](../../../../network-watcher/network-watcher-monitoring-overview.md) para monitorar, diagnosticar e exibir métricas de recursos na rede virtual do Azure.
- [Proteção contra DDoS do Azure Standard](../../../../ddos-protection/ddos-protection-overview.md) para proteger os recursos do Azure contra DDoS.
- [Azure Bastion](../../../../bastion/bastion-overview.md) para fornecer conectividade contínua e segura para uma máquina virtual que não exige um endereço IP público, um agente nem um software cliente especial.
- [Gateway de VPN do Azure](../../../../vpn-gateway/vpn-gateway-about-vpngateways.md) para enviar tráfego criptografado entre uma rede virtual do Azure e uma localização local pela Internet pública.

> [!NOTE] 
> O Azure Security Benchmark Foundation apresenta uma arquitetura de base para as cargas de trabalho. O diagrama de arquitetura acima inclui vários recursos teóricos para demonstrar o uso potencial de sub-redes. Ainda é necessário implantar cargas de trabalho nessa arquitetura de base.

## <a name="next-steps"></a>Próximas etapas

Você revisou a visão geral e a arquitetura do exemplo de blueprint do Azure Security Benchmark Foundation.

> [!div class="nextstepaction"]
> [Blueprint do Azure Security Benchmark Foundation – etapas de implantação](./deploy.md)

Outros artigos sobre blueprints e como usá-los:

- Saiba mais sobre o [ciclo de vida do blueprint](../../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../../how-to/update-existing-assignments.md).