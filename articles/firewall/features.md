---
title: Recursos do Firewall do Azure
description: Saiba mais sobre os recursos do firewall do Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 10/08/2020
ms.author: victorh
ms.openlocfilehash: 69eaf3ca60378afd810d712d85ea7ef732e41e3e
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98788223"
---
# <a name="azure-firewall-features"></a>Recursos do Firewall do Azure

O [Firewall do Azure](overview.md) é um serviço de segurança de rede gerenciado baseado em nuvem que protege os recursos de rede virtual do Azure.

![Visão geral do firewall](media/overview/firewall-threat.png)

O Firewall do Azure inclui os seguintes recursos:

- [Alta disponibilidade interna](#built-in-high-availability)
- [Zonas de Disponibilidade](#availability-zones)
- [Escalabilidade de nuvem irrestrita](#unrestricted-cloud-scalability)
- [Regras de filtragem de FQDN de aplicativo](#application-fqdn-filtering-rules)
- [Regras de filtragem de tráfego de rede](#network-traffic-filtering-rules)
- [Marcas de FQDN](#fqdn-tags)
- [Marcas de serviço](#service-tags)
- [Inteligência contra ameaças](#threat-intelligence)
- [Suporte a SNAT de saída](#outbound-snat-support)
- [Suporte a DNAT de entrada](#inbound-dnat-support)
- [Vários endereços IP públicos](#multiple-public-ip-addresses)
- [Log de Azure Monitor](#azure-monitor-logging)
- [Túnel forçado](#forced-tunneling)
- [Certificações](#certifications)

## <a name="built-in-high-availability"></a>Alta disponibilidade interna

A alta disponibilidade é interna, portanto, nenhum balanceador de carga adicional é necessário e nenhuma configuração é necessária.

## <a name="availability-zones"></a>Zonas de Disponibilidades

O Firewall do Azure pode ser configurado durante a implantação para abranger várias Zonas de Disponibilidade para aumentar a disponibilidade. Com Zonas de Disponibilidade, a sua disponibilidade aumenta para um tempo de atividade de 99,99%. Para saber mais, confira o [Contrato de Nível de Serviço (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/) do Firewall do Azure. O SLA de tempo de atividade de 99,99% é oferecido quando duas ou mais Zonas de Disponibilidade estão selecionadas.

Você também pode associar o Firewall do Azure a uma zona específica apenas por motivos de proximidade, usando o SLA de 99,95% padrão de serviço.

Não há custo adicional para um firewall implantado em uma Zona de Disponibilidade. No entanto, há custos adicionais para transferências de dados de entrada e saída associados com as Zonas de Disponibilidade. Para saber mais, confira [Detalhes de preços de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/).

As Zonas de Disponibilidade do Firewall do Azure estão disponíveis nas regiões que dão suporte a Zonas de Disponibilidade. Para mais informações, confira [Regiões que dão suporte às Zonas de Disponibilidade no Azure](../availability-zones/az-region.md)

> [!NOTE]
> As Zonas de Disponibilidade só podem ser configuradas durante a implantação. Você não pode configurar um firewall existente para incluir Zonas de Disponibilidade.

Para obter informações sobre Zonas de Disponibilidade, confira [Regiões e Zonas de Disponibilidade no Azure](../availability-zones/az-overview.md)

## <a name="unrestricted-cloud-scalability"></a>Escalabilidade de nuvem sem restrições

O Firewall do Azure pode escalar verticalmente o quanto você precisar a fim de acomodar fluxos de tráfego de rede cambiáveis, para que você não precise de orçamento para o tráfego de pico.

## <a name="application-fqdn-filtering-rules"></a>Regras de filtragem de FQDN de aplicativo

Você pode limitar o tráfego de HTTP/S de saída ou o tráfego SQL do Azure a uma lista especificada de FQDN (nomes de domínio totalmente qualificados), incluindo curingas. Esse recurso não exige o encerramento de TLS.

## <a name="network-traffic-filtering-rules"></a>Regras de filtragem de tráfego de rede

Você pode criar centralmente regras de filtragem de rede para *permitir* ou *negar* por endereço IP de origem e destino, porta e protocolo. O Firewall do Azure é totalmente com estado, para que possa distinguir pacotes legítimos de diferentes tipos de conexões. As regras são impostas e registradas em várias assinaturas e redes virtuais.

## <a name="fqdn-tags"></a>Marcas de FQDN

As [marcas de FQDN](fqdn-tags.md) facilitam a permissão de tráfego de rede do serviço do Azure conhecido através do firewall. Por exemplo, digamos que você deseja permitir o tráfego de rede do Windows Update por meio de seu firewall. Você cria uma regra de aplicativo e inclui a marca do Windows Update. Agora o tráfego de rede do Windows Update pode fluir através do firewall.

## <a name="service-tags"></a>Marcas de serviço

Uma [marca de serviço](service-tags.md) representa um grupo de prefixos de endereço IP para ajudar a minimizar a complexidade da criação de regra de segurança. Você não pode criar sua própria marca de serviço ou especificar quais endereços IP estão incluídos em uma marca. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

## <a name="threat-intelligence"></a>Inteligência contra ameaças

A filtragem baseada em [inteligência contra ameaças](threat-intel.md) pode ser habilitada para o seu firewall para alertar e rejeitar o tráfego de/para endereços IP e domínios mal-intencionados. Os endereços IP e os domínios são originados do feed de inteligência de ameaças da Microsoft.

## <a name="outbound-snat-support"></a>Suporte a SNAT de saída

Todos os endereços IP de tráfego de rede virtual de saída são convertidos no IP público do Firewall do Azure (conversão de endereço de rede de origem). Você pode identificar e permitir o tráfego proveniente de sua rede virtual para destinos de Internet remotos. O Firewall do Azure não usa SNAT quando o IP de destino é um intervalo IP privado de acordo com o [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). 

Se sua organização usa um intervalo de endereços IP públicos para redes privadas, o Firewall do Azure usará SNAT para o tráfego de um dos endereços IP privados do firewall em AzureFirewallSubnet. Você pode configurar o Firewall do Azure para **não** realizar SNAT de seu intervalo de endereços IP públicos. Para obter mais informações, confira [Intervalos de endereços IP privados do SNAT do Firewall do Azure](snat-private-range.md).

## <a name="inbound-dnat-support"></a>Suporte a DNAT de entrada

O tráfego de rede da Internet de entrada para o seu endereço IP público do firewall é convertido (conversão de endereços de rede de destino) e filtrado para os endereços IP privados nas redes virtuais.

## <a name="multiple-public-ip-addresses"></a>Vários endereços IP públicos

Você pode associar [vários endereços IP públicos](deploy-multi-public-ip-powershell.md) (até 250) com o seu firewall.

Isso permite os seguintes cenários:

- **DNAT**: várias instâncias de porta padrão podem ser traduzidas em seus servidores de back-end. Por exemplo, se você tem dois endereços IP públicos, pode traduzir a porta TCP 3389 (RDP) para os dois endereços IP.
- **SNAT**: as portas adicionais estão disponíveis para conexões SNAT de saída, reduzindo a possibilidade de esgotamento da porta SNAT. Neste momento, o Firewall do Azure seleciona aleatoriamente o endereço IP público do código-fonte a ser usado para uma conexão. Se você tiver qualquer filtragem downstream em sua rede, precisará permitir todos os endereços IP públicos associados com seu firewall. Considere usar um [prefixo de endereço IP público](../virtual-network/public-ip-address-prefix.md) para simplificar essa configuração.

## <a name="azure-monitor-logging"></a>Registro em log do Azure Monitor

Todos os eventos são integrados ao Azure Monitor, permitindo que você arquive logs em uma conta de armazenamento, transmita eventos ao Hub de Eventos ou envie-os aos logs do Azure Monitor. Para obter Azure Monitor exemplos de log, consulte [Azure monitor logs do firewall do Azure](./firewall-workbook.md).

Para saber mais, confira [Tutorial: Monitorar os logs e as métricas do Firewall do Azure](./firewall-diagnostics.md). 

A pasta de trabalho do firewall do Azure fornece uma tela flexível para análise de dados do firewall do Azure. Você pode usá-lo para criar relatórios visuais avançados dentro do portal do Azure. Para obter mais informações, consulte [monitorar logs usando a pasta de trabalho do firewall do Azure](firewall-workbook.md).

## <a name="forced-tunneling"></a>Túnel forçado

Você pode configurar o Firewall do Azure para rotear todo o tráfego vinculado à Internet para um próximo salto designado em vez de ir diretamente para a Internet. Por exemplo, você pode ter um firewall de borda local ou outra NVA (solução de virtualização de rede) para processar o tráfego de rede antes que ele seja passado para a Internet. Para obter mais informações, confira [Túnel forçado do Firewall do Azure](forced-tunneling.md).

## <a name="certifications"></a>Certificações

O Firewall do Azure está em conformidade com PCI (Payment Card Industry), SOC (Service Organization Controls), ISO (Organização Internacional de Normalização) e com a ICSA Labs. Para obter mais informações, confira [Certificações de conformidade do Firewall do Azure](compliance-certifications.md).

## <a name="next-steps"></a>Próximas etapas

- [Lógica de processamento de regra do Firewall do Azure](rule-processing.md)