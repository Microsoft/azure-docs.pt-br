---
title: "Exemplos de configuração do roteador do cliente ExpressRoute | Microsoft Docs"
description: "Esta página fornece exemplos de configuração do roteador para os roteadores da série Cisco ASA e Juniper."
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: 
ms.assetid: 564826bc-017a-4683-a385-37c9fa814948
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: cherylmc
ms.openlocfilehash: 032e584dc5abf59e9e3e8d80673b402f1fbf721b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Exemplos de configuração do roteador para configurar e gerenciar o roteamento
Esta página fornece interface e modelos de configuração de roteamento para roteadores da série Cisco IOS-XE e Juniper MX. Devem ser exemplos para obter orientação apenas e não devem ser usados como estão. Você pode trabalhar com o fornecedor para exibir as configurações apropriadas para sua rede. 

> [!IMPORTANT]
> Exemplos nesta página devem ser exclusivamente para obter orientação. Trabalhe com a equipe de vendas / equipe técnica e sua equipe de rede para exibir as configurações adequadas para atendar às suas necessidades. A Microsoft não oferecerá suporte a problemas relacionados a configurações listadas nesta página. Você deve entrar em contato com o fornecedor do dispositivo para problemas de suporte.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>Configurações de MSS TCP e MTU em interfaces do roteador
* A MTU para a interface do ExpressRoute é 1500, que é a MTU padrão típica para uma interface Ethernet em um roteador. A menos que seu roteador tenha uma MTU diferente por padrão, não é necessário especificar um valor na interface do roteador.
* Ao contrário de um Gateway de VPN do Azure, o MSS TCP para um circuito ExpressRoute não precisa ser especificado.

Os modelos de configuração abaixo se aplicam a todos os emparelhamentos. Examine [emparelhamentos do ExpressRoute](expressroute-circuit-peerings.md) e [requisitos de roteamento do ExpressRoute](expressroute-routing.md) para obter mais detalhes sobre roteamento.


## <a name="cisco-ios-xe-based-routers"></a>Roteadores com base em Cisco IOS-XE
Os modelos nesta seção se aplicam a qualquer roteador que esteja executando a família de sistemas operacionais IOS XE.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Configurando interfaces e sub-interfaces
Você precisará de uma sub interface por emparelhamento em cada roteador, que o conecte à Microsoft. Uma interface de sub-rotina pode ser identificada com uma ID de VLAN ou um par empilhado de IDs VLAN e um endereço IP.

**Definição da interface Dot1Q**

Este exemplo fornece a definição de sub-interface para a sub-interface com uma ID de VLAN única. A ID de VLAN é exclusiva por emparelhamento. O último octeto de seu endereço IPv4 sempre será um número ímpar.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

**Definição da interface QinQ**

Este exemplo fornece a definição de sub-interface para a sub-interface com duas IDs de VLAN única. ID da VLAN externa (s-tag), se usada permanece a mesma em todos os emparelhamentos. A ID de VLAN interna (marca c) é exclusiva por emparelhamento. O último octeto de seu endereço IPv4 sempre será um número ímpar.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>

### <a name="2-setting-up-ebgp-sessions"></a>2. Configuração das sessões eBGP
Você deve configurar uma sessão BGP com a Microsoft para cada emparelhamento. O exemplo a seguir lhe permite configurar uma sessão BGP com a Microsoft. Se o endereço IPv4 usado para a sua sub interface era a.b.c. d, o endereço IP do vizinho BGP (Microsoft) será a.b.c.d+1. O último octeto do endereço de IPv4 do vizinho BGP sempre será um número par.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Configurando os prefixos anunciados sobre a sessão BGP
Você pode configurar seu roteador para anunciar prefixos selecionados para a Microsoft. Você pode fazer isso usando o exemplo a seguir.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="4-route-maps"></a>4. Mapas de rotas
Você pode usar mapas de rotas e listas de prefixo para prefixos de filtro propagados em sua rede. Você pode usar o exemplo a seguir para realizar a tarefa. Certifique-se de que as listas de prefixo foram configuradas apropriadamente.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
      neighbor <IP#2_used_by_Azure> route-map <MS_Prefixes_Inbound> in
     exit-address-family
    !
    route-map <MS_Prefixes_Inbound> permit 10
     match ip address prefix-list <MS_Prefixes>
    !


## <a name="juniper-mx-series-routers"></a>Roteadores da série Juniper MX
Os exemplos nesta seção se aplicam aos os roteadores da série Juniper MX.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Configurando interfaces e sub-interfaces

**Definição da interface Dot1Q**

Este exemplo fornece a definição de sub-interface para a sub-interface com uma ID de VLAN única. A ID de VLAN é exclusiva por emparelhamento. O último octeto de seu endereço IPv4 sempre será um número ímpar.

    interfaces {
        vlan-tagging;
        <Interface_Number> {
            unit <Number> {
                vlan-id <VLAN_ID>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }
            }
        }
    }


**Definição da interface QinQ**

Este exemplo fornece a definição de sub-interface para a sub-interface com duas IDs de VLAN única. ID da VLAN externa (s-tag), se usada permanece a mesma em todos os emparelhamentos. A ID de VLAN interna (marca c) é exclusiva por emparelhamento. O último octeto de seu endereço IPv4 sempre será um número ímpar.

    interfaces {
        <Interface_Number> {
            flexible-vlan-tagging;
            unit <Number> {
                vlan-tags outer <S-tag> inner <C-tag>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }                           
            }                               
        }                                   
    }                           

### <a name="2-setting-up-ebgp-sessions"></a>2. Configuração das sessões eBGP
Você deve configurar uma sessão BGP com a Microsoft para cada emparelhamento. O exemplo a seguir lhe permite configurar uma sessão BGP com a Microsoft. Se o endereço IPv4 usado para a sua sub interface era a.b.c. d, o endereço IP do vizinho BGP (Microsoft) será a.b.c.d+1. O último octeto do endereço de IPv4 do vizinho BGP sempre será um número par.

    routing-options {
        autonomous-system <Customer_ASN>;
    }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Configurando os prefixos anunciados sobre a sessão BGP
Você pode configurar seu roteador para anunciar prefixos selecionados para a Microsoft. Você pode fazer isso usando o exemplo a seguir.

    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
        route-filter <Prefix_to_be_advertised/Subnet_Mask> exact;
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }


### <a name="4-route-maps"></a>4. Mapas de rotas
Você pode usar mapas de rotas e listas de prefixo para prefixos de filtro propagados em sua rede. Você pode usar o exemplo a seguir para realizar a tarefa. Certifique-se de que as listas de prefixo foram configuradas apropriadamente.

    policy-options {
        prefix-list MS_Prefixes {
            <IP_Prefix_1/Subnet_Mask>;
            <IP_Prefix_2/Subnet_Mask>;
        }
        policy-statement <MS_Prefixes_Inbound> {
            term 1 {
                from {
        prefix-list MS_Prefixes;
                }
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                import <MS_Prefixes_Inbound>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

## <a name="next-steps"></a>Próximas etapas
Consulte as [Perguntas Frequentes sobre ExpressRoute](expressroute-faqs.md) para obter mais detalhes.

