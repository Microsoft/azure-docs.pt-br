---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 12/14/2018
ms.date: 12/24/2018
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: 36b3fcfa90b5b1de9c9d3262da1f3e519cc99c19
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60456405"
---
### <a name="is-custom-ipsecike-policy-supported-on-all-azure-vpn-gateway-skus"></a>A política de IPsec/IKE personalizada tem suporte em todos os SKUs de Gateway de VPN do Azure?
A política de IPsec/IKE personalizada tem suporte nos gateways de VPN do Azure **VpnGw1, VpnGw2, VpnGw3, Standard** e **HighPerformance**. A SKU **Básica** **não** tem suporte.

### <a name="how-many-policies-can-i-specify-on-a-connection"></a>Quantas políticas eu posso especificar em uma conexão?
Você só pode especificar ***uma*** combinação de políticas para uma determinada conexão.

### <a name="can-i-specify-a-partial-policy-on-a-connection-for-example-only-ike-algorithms-but-not-ipsec"></a>Eu posso especificar uma política parcial em uma conexão? (por exemplo, somente os algoritmos de IKE, mas não IPsec)
Não, você deve especificar todos os algoritmos e parâmetros para IKE (modo principal) e IPsec (modo rápido). A especificação de política parcial não é permitida.

### <a name="what-are-the-algorithms-and-key-strengths-supported-in-the-custom-policy"></a>Quais são os algoritmos e as restrições principais suportadas na política personalizada?
A tabela a seguir lista os algoritmos de criptografia com suporte e restrições de chave configuráveis pelos clientes. Você deve selecionar uma opção para cada campo.

| **IPsec/IKEv2**  | **Opções**                                                                   |
| ---              | ---                                                                           |
| Criptografia IKEv2 | AES256, AES192, AES128, DES3, DES                                             |
| Integridade do IKEv2  | SHA384, SHA256, SHA1, MD5                                                     |
| Grupo DH         | DHGroup24, ECP384, ECP256, DHGroup14 (DHGroup2048), DHGroup2, DHGroup1, nenhum |
| Criptografia IPsec | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, nenhum      |
| Integridade do IPsec  | GCMAES256, GCMAES192, GCMAES128, SHA256, SHA1, MD5                            |
| Grupo PFS        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, nenhum                              |
| Tempo de vida da QM SA   | Segundos (inteiro; **mínimo de 300**/padrão de 27000 segundos)<br>KBytes (inteiro; **mín. de 1024** /padrão de 102400000 KBytes)           |
| Seletor de tráfego | UsePolicyBasedTrafficSelectors ($True/$False; default $False)                 |
|                  |                                                                               |

> [!IMPORTANT]
> 1. DHGroup2048 e PFS2048 são semelhantes ao Grupo Diffie-Hellman **14** em IKE e IPsec PFS. Confira [Grupos Diffie-Hellman](#DH) para obter os mapeamentos completos.
> 2. Para os algoritmos GCMAES, você deve especificar o mesmo algoritmo GCMAES e o comprimento de chave para a Criptografia e a Integridade IPsec.
> 3. O tempo de vida da SA de modo principal IKEv2 é fixo em 28.800 segundos nos gateways de VPN do Azure
> 4. As Vidas Úteis de SA QM são parâmetros opcionais. Se nenhum for especificado, os valores padrão de 27.000 segundos (7,5 horas) e 102400000 KBytes (102 GB) serão usados.
> 5. UsePolicyBasedTrafficSelector é um parâmetro de opção na conexão. Confira o próximo item de Perguntas frequentes para "UsePolicyBasedTrafficSelectors"

### <a name="does-everything-need-to-match-between-the-azure-vpn-gateway-policy-and-my-on-premises-vpn-device-configurations"></a>As configurações do dispositivo VPN local e a política do gateway de VPN do Azure devem corresponder em todos os aspectos?
A configuração do dispositivo VPN local deve corresponder ou conter os seguintes algoritmos e parâmetros que você especifica na política de IPsec/IKE do Azure:

* Algoritmo de criptografia IKE
* Algoritmo de integridade de IKE
* Grupo DH
* Algoritmo de criptografia IPsec
* Algoritmo de integridade de IPsec
* Grupo PFS
* Seletor de tráfego (\*)

Os tempos de vida da SA são apenas especificações locais, portanto não precisam ser correspondentes.

Caso habilite **UsePolicyBasedTrafficSelectors**, você precisa garantir que o seu dispositivo VPN tem os seletores de tráfego correspondentes definidos com todas as combinações de prefixos de rede local (gateway de rede local) de/para prefixos de rede virtual 'do Azure, em vez de "qualquer para qualquer". Por exemplo, se os prefixos da rede local são 10.1.0.0/16 e 10.2.0.0/16, e os prefixos da rede virtual são 192.168.0.0/16 e 172.16.0.0/16, você precisa especificar os seguintes seletores de tráfego:
* 10.1.0.0/16 <====> 192.168.0.0/16
* 10.1.0.0/16 <====> 172.16.0.0/16
* 10.2.0.0/16 <====> 192.168.0.0/16
* 10.2.0.0/16 <====> 172.16.0.0/16

Para saber mais, confira [Conectar vários dispositivos VPN locais baseados em políticas](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md).

### <a name ="DH"></a>Há suporte para quais grupos Diffie-Hellman?
A tabela abaixo lista os Grupos Diffie-Hellman com suporte para IKE (DHGroup) e IPsec (PFSGroup):

| **Grupo Diffie-Hellman**  | **DHGroup**              | **PFSGroup** | **Comprimento da chave** |
| ---                       | ---                      | ---          | ---            |
| 1                         | DHGroup1                 | PFS1         | MODP de 768 bits   |
| 2                         | DHGroup2                 | PFS2         | MODP de 1024 bits  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | MODP de 2048 bits  |
| 19                        | ECP256                   | ECP256       | ECP de 256 bits    |
| 20                        | ECP384                   | ECP384       | ECP de 384 bits    |
| 24                        | DHGroup24                | PFS24        | MODP de 2048 bits  |
|                           |                          |              |                |

Para saber mais, confira [RFC3526](https://tools.ietf.org/html/rfc3526) e [RFC5114](https://tools.ietf.org/html/rfc5114).

### <a name="does-the-custom-policy-replace-the-default-ipsecike-policy-sets-for-azure-vpn-gateways"></a>A política personalizada substitui os conjuntos de políticas de IPsec/IKE padrão para gateways de VPN do Azure?
Sim, depois que uma política personalizada for especificada em uma conexão, o gateway de VPN do Azure usará a política na conexão, como iniciador do IKE e respondente do IKE.

### <a name="if-i-remove-a-custom-ipsecike-policy-does-the-connection-become-unprotected"></a>Se eu remover uma política de IPsec/IKE personalizada, a conexão ficará desprotegida?
Não, a conexão ainda estará protegida por IPsec/IKE. Após você remover a política personalizada de uma conexão, o gateway de VPN do Azure reverterá para a [lista padrão de propostas de IPsec/IKE](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md) e reiniciará o handshake do IKE novamente com o dispositivo VPN local.

### <a name="would-adding-or-updating-an-ipsecike-policy-disrupt-my-vpn-connection"></a>Adicionar ou atualizar uma política de IPsec/IKE pode atrapalhar minha conexão VPN?
Sim, isso pode causar uma interrupção pequena (alguns segundos) uma vez que o gateway de VPN do Azure subdivide a conexão existente e reiniciará o handshake do IKE para restabelecer o túnel IPsec com os algoritmos de criptografia e os parâmetros novos. Verifique se o dispositivo VPN local também está configurado com os algoritmos correspondentes e as restrições de chave para minimizar a interrupção.

### <a name="can-i-use-different-policies-on-different-connections"></a>Eu posso usar políticas diferentes em conexões diferentes?
Sim. A política personalizada é aplicada em uma base por conexão. Você pode criar e aplicar políticas de IPsec/IKE diferentes em conexões diferentes. Também é possível aplicar políticas personalizadas em um subconjunto de conexões. As restantes usam os conjuntos de políticas de IPsec/IKE padrão do Azure.

### <a name="can-i-use-the-custom-policy-on-vnet-to-vnet-connection-as-well"></a>Eu também posso usar a política personalizada na conexão de VNet para VNet?
Sim, você pode aplicar a política personalizada em conexões entre locais de IPsec ou conexões de VNet para VNet.

### <a name="do-i-need-to-specify-the-same-policy-on-both-vnet-to-vnet-connection-resources"></a>É necessário especificar a mesma política em ambos os recursos de conexão de VNet para VNet?
Sim. Um túnel de VNet para VNet consiste em dois recursos de conexão no Azure, uma para cada sentido. Faça com que ambos os recursos de conexão tenham a mesma política; caso contrário, a conexão de VNet para VNet não se estabelecerá.

### <a name="does-custom-ipsecike-policy-work-on-expressroute-connection"></a>A política de IPsec/IKE personalizada funciona em conexão de ExpressRoute?
Não. A política IPsec/IKE só funciona em conexões VNet para VNet e VPN S2S por meio de gateways de VPN do Azure.

### <a name="where-can-i-find-more-configuration-information-for-ipsec"></a>Onde posso encontrar mais informações de configuração para o IPsec?
Veja [Configurar a política de IPsec/IKE para conexões VPN S2S ou VNet para VNet](../articles/vpn-gateway/vpn-gateway-ipsecikepolicy-rm-powershell.md)
