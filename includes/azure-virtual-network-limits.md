---
title: incluir arquivo
description: incluir arquivo
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 01/14/2020
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: 2262290486047c225cec8e8f6bb73242df7258fa
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106271536"
---
### <a name="networking-limits---azure-resource-manager"></a><a name="azure-resource-manager-virtual-networking-limits"></a>Limites de rede – Azure Resource Manager
Os limites a seguir aplicam-se apenas a recursos de rede gerenciados pelo **Azure Resource Manager** por região e assinatura. Saiba como [exibir seu uso de recursos atual em relação a seus limites de assinatura](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> Recentemente, aumentamos todos os limites padrão para seus limites máximos. Se não houver nenhuma coluna de limite máximo, o recurso não terá limites ajustáveis. Se o suporte aumentou esses limites no passado e você não consegue vê-los atualizados nas tabelas a seguir, [abra uma solicitação de suporte de atendimento ao cliente online sem custo adicional](../articles/azure-resource-manager/templates/error-resource-quota.md)

| Recurso | Limite | 
| --- | --- |
| Redes virtuais |1.000 |
| Sub-redes por rede virtual |3.000 |
| Emparelhamentos de rede virtual por rede virtual |500 |
| [Gateways de rede virtual (gateways de VPN) por rede virtual](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |1 |
| [Gateways de rede virtual (gateways do ExpressRoute) por rede virtual](../articles/expressroute/expressroute-about-virtual-network-gateways.md#gwsku) |1 |
| Servidores DNS por rede virtual |20 |
| Endereços IP privados por rede virtual |65.536 |
| Endereços IP privados por adaptador de rede |256 |
| Endereços IP privados por máquina virtual |256 |
| Endereços IP públicos por adaptador de rede |256 |
| Endereços IP públicos por máquina virtual |256 |
| [Fluxos TCP ou UDP simultâneos por NIC de uma máquina virtual ou instância de função](../articles/virtual-network/virtual-machine-network-throughput.md#flow-limits-and-active-connections-recommendations) |500.000 |
| Placas de interface de rede |65.536 |
| Grupos de segurança de rede |5\.000 |
| Regras de NSG por NSG |1,000 |
| Endereços e intervalos IP especificados para a origem ou o destino em um grupo de segurança |4.000 |
| Grupos de segurança do aplicativo |3\.000 |
| Grupos de segurança do aplicativo por configuração de IP, por NIC |20 |
| Configurações de IP por grupo de segurança do aplicativo |4.000 |
| Os grupos de segurança do aplicativo que podem ser especificados em todas as regras de segurança de um grupo de segurança de rede |100 |
| Tabelas de rotas definidas pelo usuário |200 |
| Rotas definidas pelo usuário por tabela de rotas |400 |
| Certificados raiz de ponto a site por Gateway de VPN do Azure |20 |
| TAPs de rede virtual |100 |
| Configurações de TAP do adaptador de rede por TAP de rede virtual |100 |

#### <a name="public-ip-address-limits"></a><a name="publicip-address"></a>Limites do endereço IP público
| Recurso | Limite padrão | Limite máximo |
| --- | --- | --- |
| Endereços IP públicos<sup>1</sup> | 10 para Básico. | Entre em contato com o suporte. |
| Endereços IP públicos estáticos<sup>1</sup> | 10 para Básico. | Entre em contato com o suporte. |
| Endereços IP públicos Standard<sup>1</sup> | 10 | Entre em contato com o suporte. |
| [Endereços IP públicos por grupo de recursos](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md#microsoftnetwork) | 800 | Entre em contato com o suporte. | 
| Prefixos IP públicos | limitados pelo número de IPs Públicos Standard em uma assinatura | Entre em contato com o suporte. |
| Comprimento do prefixo de IP público | / 28 | Entre em contato com o suporte. |

<sup>1</sup>Os limites padrão para endereços IP públicos variam de acordo com o tipo de categoria oferecido, como Avaliação Gratuita, Pago Conforme o Uso, CSP. Por exemplo, o padrão para assinaturas do Contrato Enterprise é 1000.

#### <a name="load-balancer-limits"></a><a name="load-balancer"></a>Limites de balanceadores de carga
Os limites a seguir aplicam-se apenas a recursos de rede gerenciados pelo Azure Resource Manager por região e assinatura. Saiba como [exibir seu uso de recursos atual em relação a seus limites de assinatura](../articles/networking/check-usage-against-limits.md).

**Standard Load Balancer**

| Recurso                                | Limite         |
|-----------------------------------------|-------------------------------|
| Balanceadores de carga                          | 1,000                         |
| Regras (Balanceador de carga + NAT de entrada) por recurso                      | 1\.500                         |
| Regras por NIC (em todos os IPs em uma NIC) | 300                           |
| Configuração do IP de front-end              | 600                           |
| Tamanho do pool de back-end                       | 1\.000 configurações de IP, rede virtual única |
| Recursos de back-end por Load Balancer <sup>1<sup> | 1.200                   |
| Portas de alta disponibilidade                 | 1 por front-end interno       |
| Regras de saída por Load Balancer        | 600                           |
| Load Balancers por VM                   | 2 (1 público e 1 interno)   |

<sup>1</sup> O limite é, no máximo, 1.200 recursos, em qualquer combinação de recursos de máquina virtual autônoma, recursos do conjunto de disponibilidade e grupos de posicionamento de conjunto de dimensionamento de máquinas virtuais.

**Load Balancer Básico**

| Recurso                                | Limite        |
|-----------------------------------------|------------------------------|
| Balanceadores de carga                          | 1,000                        |
| Regras por recurso                      | 250                          |
| Regras por NIC (em todos os IPs em uma NIC) | 300                          |
| Configurações do IP de front-end <sup>2<sup>  | 200                          |
| Tamanho do pool de back-end                       | 300 configurações de IP, conjunto de disponibilidade único |
| Conjuntos de disponibilidade por Load Balancer     | 1                            |
| Load Balancers por VM                   | 2 (1 público e 1 interno)  |

<sup>2</sup> O limite para um único recurso discreto em um pool de back-end (máquina virtual autônoma, conjunto de disponibilidade ou grupo de posicionamento de conjunto de dimensionamento da máquina virtual) é ter até 250 configurações de IP de front-end em um único balanceador de carga público básico e balanceador de carga interno básico.

<a name="virtual-networking-limits-classic"></a>Os limites a seguir aplicam-se apenas a recursos de rede gerenciados pelo modelo de implantação **clássico** por assinatura. Saiba como [exibir seu uso de recursos atual em relação a seus limites de assinatura](../articles/networking/check-usage-against-limits.md).

| Recurso | Limite padrão | Limite máximo |
| --- | --- | --- |
| Redes virtuais |100 |100 |
| Sites de redes locais |20 |50 |
| Servidores DNS por rede virtual |20 |20 |
| Endereços IP privados por rede virtual |4\.096 |4\.096 |
| Fluxos TCP ou UDP simultâneos por NIC de uma máquina virtual ou instância de função |500 mil, até 1 milhão para duas ou mais NICs. |500 mil, até 1 milhão para duas ou mais NICs. |
| Grupos de segurança de rede (NSG) |200 |200 |
| Regras de NSG por NSG |200 |1,000 |
| Tabelas de rotas definidas pelo usuário |200 |200 |
| Rotas definidas pelo usuário por tabela de rotas |400 |400 |
| Endereços IP públicos (dinâmicos) |500 |500 |
| Endereços IP públicos reservados |500 |500 |
| IP público por implantação |5 |Contate o suporte |
| IP privado (balanceamento de carga interno) por implantação |1 |1 |
| ACLs (listas de controle de acesso) de ponto de extremidade |50 |50 |
