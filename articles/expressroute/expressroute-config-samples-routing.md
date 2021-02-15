---
title: 'Microsoft Azure ExpressRoute: Amostras de configuração do roteador'
description: Use esses exemplos de configuração de interface e roteamento para os roteadores Cisco IOS-XE e Juniper MX Series como exemplos para trabalhar com o Azure ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 03/26/2020
ms.author: duau
ms.openlocfilehash: 3bc850f02884ae0547c2ecf56a46a57a4e66a752
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89397399"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Exemplos de configuração do roteador para configurar e gerenciar o roteamento
Esta página fornece modelos de configuração de interface e roteamento para roteadores da série Cisco IOS-XE e Juniper MX ao trabalhar com o Azure ExpressRoute.

> [!IMPORTANT]
> As amostras desta página são meramente indicativas. Trabalhe com a equipe de vendas/equipe técnica do seu fornecedor e sua equipe de rede para encontrar as configurações apropriadas para atendar às suas necessidades. A Microsoft não oferecerá suporte a problemas relacionados às configurações listadas nesta página. Entre em contato com o fornecedor do dispositivo para obter suporte.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>Configurações de MSS TCP e MTU em interfaces do roteador
A unidade de transmissão máxima (MTU) para a interface do ExpressRoute é 1.500, que é a MTU padrão típica para uma interface Ethernet em um roteador. A menos que seu roteador tenha uma MTU diferente por padrão, não é necessário especificar um valor na interface do roteador.

Ao contrário de um gateway de VPN do Azure, o tamanho máximo do segmento (MSS) do TCP para um circuito ExpressRoute não precisa ser especificado.

As amostras de configuração do roteador neste artigo se aplicam a todos os emparelhamentos. Examine [emparelhamentos do ExpressRoute](expressroute-circuit-peerings.md) e [requisitos de roteamento do ExpressRoute](expressroute-routing.md) para obter mais detalhes sobre roteamento.


## <a name="cisco-ios-xe-based-routers"></a>Roteadores com base em Cisco IOS-XE
As amostras nesta seção se aplicam a qualquer roteador que esteja executando a família de sistemas operacionais IOS-XE.

### <a name="configure-interfaces-and-subinterfaces"></a>Configurar interfaces e subinterfaces
Você precisará de uma subinterface por emparelhamento em cada roteador conectado à Microsoft. Uma subinterface pode ser identificada com uma ID de VLAN ou um par empilhado de IDs de VLAN e um endereço IP.

**Definição da interface Dot1Q**

Esta amostra fornece a definição de subinterface para uma subinterface com uma ID de VLAN única. A ID de VLAN é exclusiva por emparelhamento. O último octeto de seu endereço IPv4 sempre será um número ímpar.

```console
interface GigabitEthernet<Interface_Number>.<Number>
 encapsulation dot1Q <VLAN_ID>
 ip address <IPv4_Address><Subnet_Mask>
```

**Definição da interface QinQ**

Esta amostra fornece a definição de subinterface para uma subinterface com duas IDs de VLAN. A ID da VLAN externa (marca s), se usada,permanece a mesma em todos os emparelhamentos. A ID de VLAN interna (marca c) é exclusiva por emparelhamento. O último octeto de seu endereço IPv4 sempre será um número ímpar.

```console
interface GigabitEthernet<Interface_Number>.<Number>
 encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
 ip address <IPv4_Address><Subnet_Mask>
```

### <a name="set-up-ebgp-sessions"></a>Configurar sessões eBGP
Você deve configurar uma sessão BGP com a Microsoft para cada emparelhamento. Configure uma sessão BGP usando a seguinte amostra. Se o endereço IPv4 usado para a sua subinterface era a.b.c.d, o endereço IP do vizinho BGP (Microsoft) será a.b.c.d + 1. O último octeto do endereço de IPv4 do vizinho BGP sempre será um número par.

```console
router bgp <Customer_ASN>
 bgp log-neighbor-changes
 neighbor <IP#2_used_by_Azure> remote-as 12076
 !
 address-family ipv4
 neighbor <IP#2_used_by_Azure> activate
 exit-address-family
!
```

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>Configurar prefixos a serem anunciados sobre a sessão BGP
Configure seu roteador para anunciar prefixos selecionados para a Microsoft usando o exemplo a seguir.

```console
router bgp <Customer_ASN>
 bgp log-neighbor-changes
 neighbor <IP#2_used_by_Azure> remote-as 12076
 !
 address-family ipv4
  network <Prefix_to_be_advertised> mask <Subnet_mask>
  neighbor <IP#2_used_by_Azure> activate
 exit-address-family
!
```

### <a name="route-maps"></a>Mapas de rotas
Use mapas de rotas e listas de prefixo para prefixos de filtro propagados em sua rede. Veja a amostra a seguir e verifique se você tem as listas de prefixos apropriadas configuradas.

```console
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
```

### <a name="configure-bfd"></a>Configurar BFD

Você configurará o BFD em dois locais: um no nível da interface e outro no nível do BGP. O exemplo aqui é para a interface QinQ. 

```console
interface GigabitEthernet<Interface_Number>.<Number>
 bfd interval 300 min_rx 300 multiplier 3
 encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
 ip address <IPv4_Address><Subnet_Mask>

router bgp <Customer_ASN>
 bgp log-neighbor-changes
 neighbor <IP#2_used_by_Azure> remote-as 12076
 !
 address-family ipv4
  neighbor <IP#2_used_by_Azure> activate
  neighbor <IP#2_used_by_Azure> fall-over bfd
 exit-address-family
!
```


## <a name="juniper-mx-series-routers"></a>Roteadores da série Juniper MX
As amostras nesta seção aplicam-se a qualquer roteador da série Juniper MX.

### <a name="configure-interfaces-and-subinterfaces"></a>Configurar interfaces e subinterfaces

**Definição da interface Dot1Q**

Esta amostra fornece a definição de subinterface para uma subinterface com uma ID de VLAN única. A ID de VLAN é exclusiva por emparelhamento. O último octeto de seu endereço IPv4 sempre será um número ímpar.

```console
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
```


**Definição da interface QinQ**

Esta amostra fornece a definição de subinterface para uma subinterface com duas IDs de VLAN. A ID da VLAN externa (marca s), se usada,permanece a mesma em todos os emparelhamentos. A ID de VLAN interna (marca c) é exclusiva por emparelhamento. O último octeto de seu endereço IPv4 sempre será um número ímpar.

```console
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
```

### <a name="set-up-ebgp-sessions"></a>Configurar sessões eBGP
Você deve configurar uma sessão BGP com a Microsoft para cada emparelhamento. Configure uma sessão BGP usando a seguinte amostra. Se o endereço IPv4 usado para a sua subinterface era a.b.c.d, o endereço IP do vizinho BGP (Microsoft) será a.b.c.d + 1. O último octeto do endereço de IPv4 do vizinho BGP sempre será um número par.

```console
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
```

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>Configurar prefixos a serem anunciados sobre a sessão BGP
Configure seu roteador para anunciar prefixos selecionados para a Microsoft usando o exemplo a seguir.

```console
    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
                route-filter; 
                <Prefix_to_be_advertised/Subnet_Mask> exact;
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>;
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }
```

### <a name="route-policies"></a>Políticas de rotas
Você pode usar mapas de rotas e listas de prefixo para prefixos de filtro propagados em sua rede. Veja a amostra a seguir e verifique se você tem as listas de prefixos apropriadas configuradas.

```console
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
                export <Policy_Name>;
                import <MS_Prefixes_Inbound>;
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }
```

### <a name="configure-bfd"></a>Configurar BFD
Configure o BFD apenas na seção BGP do protocolo.

```console
    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
                bfd-liveness-detection {
                       minimum-interval 3000;
                       multiplier 3;
                }
            }                               
        }                                   
    }
```

### <a name="configure-macsec"></a>Configurar o MACSec
Para a configuração MACSec, a Chave de Associação de Conectividade (CAK) e o Nome da Chave de Associação de Conectividade (CKN) devem corresponder aos valores configurados por meio dos comandos do PowerShell.

```console
    security {
        macsec {
            connectivity-association <Connectivity_Association_Name> {
                cipher-suite gcm-aes-xpn-128;
                security-mode static-cak;
                pre-shared-key {
                    ckn <Connectivity_Association_Key_Name>;
                    cak <Connectivity_Association_Key>; ## SECRET-DATA
                }
            }
            interfaces {
                <Interface_Number> {
                    connectivity-association <Connectivity_Association_Name>;
                }
            }
        }
    }
```

## <a name="next-steps"></a>Próximas etapas
Consulte as [Perguntas Frequentes sobre ExpressRoute](expressroute-faqs.md) para obter mais detalhes.



