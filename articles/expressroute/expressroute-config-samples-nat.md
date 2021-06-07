---
title: 'Azure ExpressRoute: exemplos de configuração do roteador-NAT'
description: Esta página fornece exemplos de configuração do roteador para os roteadores da série Cisco ASA e Juniper.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 01/07/2021
ms.author: duau
ms.openlocfilehash: ae0a39d65bf0f1bc5221cd5e46493c489f7630f8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98012658"
---
# <a name="router-configuration-samples-to-set-up-and-manage-nat"></a>Exemplos de configuração do roteador para configurar e gerenciar o NAT

Este artigo fornece exemplos de configuração de NAT para roteadores da série Cisco ASA e Juniper SRX ao trabalhar com o ExpressRoute. Essas configurações de roteador devem ser exemplos apenas para orientação e não devem ser usadas como estão. Você precisará trabalhar com seu fornecedor para criar as configurações apropriadas para sua rede.

> [!IMPORTANT]
> Exemplos nesta página devem ser exclusivamente para obter orientação. Trabalhe com a equipe de vendas / equipe técnica e sua equipe de rede para exibir as configurações adequadas para atendar às suas necessidades. A Microsoft não oferecerá suporte a problemas relacionados a configurações listadas nesta página. Você deve entrar em contato com o fornecedor do dispositivo para problemas de suporte.
> 
> 

* Os exemplos de configuração de roteador abaixo se aplicam a emparelhamentos do Azure Public e Microsoft. Você não configura o NAT para o emparelhamento privado do Azure. Examine [Emparelhamentos do ExpressRoute](expressroute-circuit-peerings.md) e [Requisitos de NAT do ExpressRoute](expressroute-nat.md) para obter mais detalhes.

* Você DEVE usar pools de IP de NAT separados para conectividade à Internet e ExpressRoute. Usando o mesmo pool de IP de NAT através da Internet e ExpressRoute vai resultar em roteamento assimétrico e perda de conectividade.


## <a name="cisco-asa-firewalls"></a>Firewalls do Cisco ASA
### <a name="pat-configuration-for-traffic-from-customer-network-to-microsoft"></a>Configuração de PAT para o tráfego de rede de cliente para a Microsoft

```console
object network MSFT-PAT
  range <SNAT-START-IP> <SNAT-END-IP>


object-group network MSFT-Range
  network-object <IP> <Subnet_Mask>

object-group network on-prem-range-1
  network-object <IP> <Subnet-Mask>

object-group network on-prem-range-2
  network-object <IP> <Subnet-Mask>

object-group network on-prem
  network-object object on-prem-range-1
  network-object object on-prem-range-2

nat (outside,inside) source dynamic on-prem pat-pool MSFT-PAT destination static MSFT-Range MSFT-Range
```

### <a name="pat-configuration-for-traffic-from-microsoft-to-customer-network"></a>Configuração PAT do tráfego da Microsoft para a rede do cliente

**Interfaces e direção:**

Interface de origem (onde o tráfego entra no ASA): dentro da interface de destino (onde o tráfego sai do ASA): fora

**Configuração:**

Pool de NAT:

```console
object network outbound-PAT
    host <NAT-IP>
```

Servidor de destino:

```console
object network Customer-Network
    network-object <IP> <Subnet-Mask>
```

Grupo de objetos para endereços IP do cliente:

```console
object-group network MSFT-Network-1
    network-object <MSFT-IP> <Subnet-Mask>

object-group network MSFT-PAT-Networks
    network-object object MSFT-Network-1
```

Comandos de NAT

```console
nat (inside,outside) source dynamic MSFT-PAT-Networks pat-pool outbound-PAT destination static Customer-Network Customer-Network
```


## <a name="juniper-srx-series-routers"></a>Roteadores da série Juniper SRX
### <a name="1-create-redundant-ethernet-interfaces-for-the-cluster"></a>1. criar interfaces Ethernet redundantes para o cluster

```console
    interfaces {
        reth0 {
            description "To Internal Network";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 1;
            }
            unit 100 {
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
        reth1 {
            description "To Microsoft via Edge Router";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 2;
            }
            unit 100 {
                description "To Microsoft via Edge Router";
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
    }
```

### <a name="2-create-two-security-zones"></a>2. criar duas zonas de segurança
* A Zona de Confiança para a rede interna e Zona Não Confiável para rede externa de frente para os Roteadores de Extremidade
* Atribuir as interfaces apropriadas para as zonas
* Permitir os serviços nas interfaces

```console
    security {
        zones {
            security-zone Trust {
                host-inbound-traffic {
                    system-services {
                        ping;
                    }
                    protocols {
                        bgp;
                    }
                }
                interfaces {
                    reth0.100;
                }
            }
            security-zone Untrust {
                host-inbound-traffic {
                    system-services {
                        ping;
                    }
                    protocols {
                        bgp;
                    }
                }
                interfaces {
                    reth1.100;
                }
            }
        }
    }
```


### <a name="3-create-security-policies-between-zones"></a>3. criar políticas de segurança entre zonas

```console
    security {
        policies {
            from-zone Trust to-zone Untrust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
            from-zone Untrust to-zone Trust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
        }
    }
```

### <a name="4-configure-nat-policies"></a>4. configurar políticas de NAT
* Criar dois pools NAT. Um será usado para a saída de tráfego NAT para a Microsoft e outro da Microsoft para o cliente.
* Criar o respectivo tráfego de regras de NAT

```console
       security {
           nat {
               source {
                   pool SNAT-To-ExpressRoute {
                       routing-instance {
                           External-ExpressRoute;
                       }
                       address {
                           <NAT-IP-address/Subnet-mask>;
                       }
                   }
                   pool SNAT-From-ExpressRoute {
                       routing-instance {
                           Internal;
                       }
                       address {
                           <NAT-IP-address/Subnet-mask>;
                       }
                   }
                   rule-set Outbound_NAT {
                       from routing-instance Internal;
                       to routing-instance External-ExpressRoute;
                       rule SNAT-Out {
                           match {
                               source-address 0.0.0.0/0;
                           }
                           then {
                               source-nat {
                                   pool {
                                       SNAT-To-ExpressRoute;
                                   }
                               }
                           }
                       }
                   }
                   rule-set Inbound-NAT {
                       from routing-instance External-ExpressRoute;
                       to routing-instance Internal;
                       rule SNAT-In {
                           match {
                               source-address 0.0.0.0/0;
                           }
                           then {
                               source-nat {
                                   pool {
                                       SNAT-From-ExpressRoute;
                                   }
                               }
                           }
                       }
                   }
               }
           }
       }
```

### <a name="5-configure-bgp-to-advertise-selective-prefixes-in-each-direction"></a>5. configurar o BGP para anunciar prefixos seletivos em cada direção
Consulte os exemplos na página [exemplos de configuração de roteamento](expressroute-config-samples-routing.md) .

### <a name="6-create-policies"></a>6. criar políticas

```console
    routing-options {
                  autonomous-system <Customer-ASN>;
    }
    policy-options {
        prefix-list Microsoft-Prefixes {
            <IP-Address/Subnet-Mask;
            <IP-Address/Subnet-Mask;
        }
        prefix-list private-ranges {
            10.0.0.0/8;
            172.16.0.0/12;
            192.168.0.0/16;
            100.64.0.0/10;
        }
        policy-statement Advertise-NAT-Pools {
            from {
                protocol static;
                route-filter <NAT-Pool-Address/Subnet-mask> prefix-length-range /32-/32;
            }
            then accept;
        }
        policy-statement Accept-from-Microsoft {
            term 1 {
                from {
                    instance External-ExpressRoute;
                    prefix-list-filter Microsoft-Prefixes orlonger;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
        policy-statement Accept-from-Internal {
            term no-private {
                from {
                    instance Internal;
                    prefix-list-filter private-ranges orlonger;
                }
                then reject;
            }
            term bgp {
                from {
                    instance Internal;
                    protocol bgp;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
    }
    routing-instances {
        Internal {
            instance-type virtual-router;
            interface reth0.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Microsoft;
            }
            protocols {
                bgp {
                    group customer {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-ASN-1>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
        External-ExpressRoute {
            instance-type virtual-router;
            interface reth1.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Internal;
            }
            protocols {
                bgp {
                    group edge-router {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-Public-ASN>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
    }
```

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações, confira [Perguntas frequentes sobre o ExpressRoute](expressroute-faqs.md).

