---
title: Controle de segurança do Azure-segurança de rede
description: Segurança de rede do controle de segurança do Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 3a232f8e8c35e265a8243ac79e465c03f6b9650e
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96487857"
---
# <a name="security-control-network-security"></a>Controle de segurança: segurança de rede

As recomendações de segurança de rede concentram-se na especificação de quais protocolos de rede, portas TCP/UDP e serviços conectados à rede têm acesso permitido ou negado aos serviços do Azure.

## <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteger os recursos do Azure em redes virtuais

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 1,1 | 9,2, 9,4, 14,1, 14,2, 14,3 | Cliente |

Verifique se todas as implantações de sub-rede da rede virtual têm um grupo de segurança de rede aplicado com controles de acesso à rede específicos às portas e fontes confiáveis do seu aplicativo. Quando disponível, use pontos de extremidade privados com o link privado para proteger seus recursos de serviço do Azure para sua rede virtual, estendendo a identidade de VNet para o serviço. Quando os pontos de extremidade privados e o link privado não estão disponíveis, use pontos de extremidade de serviço. Para requisitos específicos de serviço, consulte a recomendação de segurança para esse serviço específico. 

Como alternativa, se você tiver um caso de uso específico, o requisito poderá ser atendido com a implementação do firewall do Azure.

- [Entender os pontos de extremidade de serviço de rede virtual](../../virtual-network/virtual-network-service-endpoints-overview.md)

- [Entender o link privado do Azure](../../private-link/private-link-overview.md)

- [Como criar uma Rede Virtual](../../virtual-network/quick-create-portal.md)

- [Como criar um NSG com uma configuração de segurança](../../virtual-network/tutorial-filter-network-traffic.md)

- [Como implantar e configurar o Firewall do Azure](../../firewall/tutorial-firewall-deploy-portal.md)

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: monitorar e registrar a configuração e o tráfego de redes virtuais, sub-redes e NICs

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 1.2 | 9,3, 12,2, 12,8 | Cliente |

Use a central de segurança do Azure e siga as recomendações de proteção de rede para ajudar a proteger seus recursos de rede no Azure. Habilite logs de fluxo de NSG e envie logs para uma conta de armazenamento para auditoria de tráfego. Você também pode enviar logs de fluxo NSG para um espaço de trabalho Log Analytics e usar Análise de Tráfego para fornecer informações sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens da Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças à segurança, compreender os padrões de fluxo de tráfego e identificar configurações incorretas de rede de pinpoint.

- [Como habilitar logs de fluxo de NSG](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como habilitar e usar a Análise de Tráfego](../../network-watcher/traffic-analytics.md)

- [Entender a segurança de rede fornecida pela central de segurança do Azure](../../security-center/security-center-network-recommendations.md)

## <a name="13-protect-critical-web-applications"></a>1.3: proteger aplicativos Web críticos

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 1,3 | 9,5 | Cliente |

Implante o WAF (firewall do aplicativo Web) do Azure na frente de aplicativos Web críticos para inspeção adicional do tráfego de entrada. Habilite a configuração de diagnóstico para WAF e ingerir logs em uma conta de armazenamento, Hub de eventos ou espaço de trabalho de Log Analytics.

- [Como implantar o Azure WAF](../../web-application-firewall/ag/create-waf-policy-ag.md)

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: rejeitar comunicações com endereços IP maliciosos conhecidos

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 1.4 | 12.3 | Cliente |

Habilite a proteção padrão de DDoS em suas redes virtuais do Azure para proteger contra ataques de DDoS. Use a inteligência de ameaças integrada da central de segurança do Azure para negar comunicações com endereços IP mal-intencionados conhecidos.

Implante o Firewall do Azure em cada um dos limites de rede da organização com inteligência contra ameaças habilitada e configurada para "alertar e negar" para tráfego de rede mal-intencionado.

Use o acesso à rede just in time da central de segurança do Azure para configurar o NSGs para limitar a exposição de pontos de extremidade a endereços IP aprovados por um período limitado.

Use a proteção de rede adaptável da central de segurança do Azure para recomendar configurações de NSG que limitam portas e IPs de origem com base no tráfego real e na inteligência contra ameaças.

- [Como configurar a proteção contra DDoS](../../ddos-protection/manage-ddos-protection.md)

- [Como implantar o Firewall do Azure](../../firewall/tutorial-firewall-deploy-portal.md)

- [Compreender a inteligência contra ameaças integrada da Central de Segurança do Azure](../../security-center/azure-defender.md)

- [Entender a proteção de rede adaptável da central de segurança do Azure](../../security-center/security-center-adaptive-network-hardening.md)

- [Entender o controle de acesso à rede just in time da central de segurança do Azure](../../security-center/security-center-just-in-time.md)

## <a name="15-record-network-packets"></a>1,5: gravar pacotes de rede

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 1.5 | 12.5 | Cliente |

Habilite a captura de pacotes do observador de rede para investigar atividades anormais.

- [Como habilitar o Observador de Rede](../../network-watcher/network-watcher-create.md)

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: implantar sistemas de detecção/prevenção de intrusões (IDS/IPS) baseados em rede

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 1.6 | 12,6, 12,7 | Cliente |

Selecione uma oferta do Azure Marketplace que dê suporte à funcionalidade de IDS/IPS com recursos de inspeção de conteúdo.  Se a detecção de intrusão e/ou prevenção baseada na inspeção de conteúdo não for um requisito, o Firewall do Azure com a inteligência contra ameaças poderá ser usado. A filtragem baseada em inteligência contra ameaças do Firewall do Azure pode alertar e rejeitar o tráfego de e para endereços IP e domínios conhecidos mal-intencionados. Os endereços IP e os domínios são originados do feed de inteligência de ameaças da Microsoft.

Implante a solução de firewall de sua escolha em cada um dos limites de rede da sua organização para detectar e/ou negar tráfego mal-intencionado.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Como implantar o Firewall do Azure](../../firewall/tutorial-firewall-deploy-portal.md)

- [Como configurar alertas com o Firewall do Azure](../../firewall/threat-intel.md)

## <a name="17-manage-traffic-to-web-applications"></a>1.7: gerenciar o tráfego para aplicativos Web

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 1.7 | 12,9, 12,10 | Cliente |

Implante Aplicativo Azure gateway para aplicativos Web com HTTPS/TLS habilitado para certificados confiáveis.

- [Como implantar o gateway de aplicativo](../../application-gateway/quick-create-portal.md)

- [Como configurar o gateway de aplicativo para usar HTTPS](../../application-gateway/create-ssl-portal.md)

- [Entender o balanceamento de carga de camada 7 com gateways de aplicativo Web do Azure](../../application-gateway/overview.md)

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: minimizar a complexidade e a sobrecarga administrativa de regras de segurança de rede

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 1.8 | 1.5 | Cliente |

Use marcas de serviço de rede virtual para definir controles de acesso de rede em grupos de segurança de rede ou no firewall do Azure. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo, ApiManagement) no campo correto de origem ou destino de uma regra, você poderá permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

Você também pode usar grupos de segurança de aplicativo para ajudar a simplificar a configuração de segurança complexa. Os grupos de segurança de aplicativo permitem a você configurar a segurança de rede como uma extensão natural da estrutura de um aplicativo, permitindo o agrupamento de máquinas virtuais e a definição de políticas de segurança de rede com base nesses grupos.

- [Entender e usar marcas de serviço](../../virtual-network/service-tags-overview.md)

- [Entender e usar grupos de segurança de aplicativos](../../virtual-network/network-security-groups-overview.md#application-security-groups)

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: manter configurações de segurança padrão para dispositivos de rede

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 1,9 | 11,1 | Cliente |

Defina e implemente configurações de segurança padrão para recursos de rede com Azure Policy.

Você também pode usar plantas do Azure para simplificar implantações do Azure de grande escala ao empacotar artefatos de ambiente-chave, como modelos do Azure Resource Manager, controles RBAC do Azure e políticas, em uma única definição de Blueprint. Você pode aplicar o plano gráfico a novas assinaturas e ajustar o controle e o gerenciamento por meio da versão.

- [Como configurar e gerenciar o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Exemplos de Azure Policy para rede](../../governance/policy/samples/built-in-policies.md#network)

- [Como criar um blueprint do Azure](../../governance/blueprints/create-blueprint-portal.md)

## <a name="110-document-traffic-configuration-rules"></a>1.10: documentar regras de configuração de tráfego

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 1,10 | 11.2 | Cliente |

Use marcas para NSGs e outros recursos relacionados à segurança de rede e ao fluxo de tráfego. Para regras de NSG individuais, use o campo "Descrição" para especificar a necessidade empresarial e/ou a duração (etc.) para regras que permitam tráfego de/para uma rede.

Use qualquer uma das definições de Azure Policy internas relacionadas à marcação, como "exigir marca e seu valor" para garantir que todos os recursos sejam criados com marcas e notificá-lo de recursos não marcados existentes.

Você pode usar Azure PowerShell ou CLI do Azure para pesquisar ou executar ações em recursos com base em suas marcas.

- [Como criar e usar marcas](../../azure-resource-manager/management/tag-resources.md)

- [Como criar uma Rede Virtual](../../virtual-network/quick-create-portal.md)

- [Como criar um NSG com uma configuração de segurança](../../virtual-network/tutorial-filter-network-traffic.md)

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 1.11 | 11.3 | Cliente |

Use o log de atividades do Azure para monitorar as configurações de recursos e detectar alterações nos recursos do Azure. Crie alertas no Azure Monitor que serão disparados quando ocorrerem alterações em recursos críticos.

- [Como exibir e recuperar eventos do log de atividades do Azure](../../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Como criar alertas no Azure Monitor](../../azure-monitor/platform/alerts-activity-log.md)

## <a name="next-steps"></a>Próximas etapas

- Consulte o próximo controle de segurança: [registro em log e monitoramento](security-control-logging-monitoring.md)