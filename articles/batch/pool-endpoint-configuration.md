---
title: Configurar pontos de extremidade de nó no pool do Lote do Azure | Microsoft Docs
description: Como configurar ou desabilitar o acesso a portas SSH ou RDP em nós de computação em um pool do Lote do Azure.
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 02/13/2018
ms.author: lahugh
ms.openlocfilehash: a6c2c343b13b77048c772cb1e5c2ba06cf8add50
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60616850"
---
# <a name="configure-or-disable-remote-access-to-compute-nodes-in-an-azure-batch-pool"></a>Configurar ou desabilitar o acesso remoto a nós de computação em um pool do Lote do Azure

Por padrão, o lote permite um [usuário de nó](/rest/api/batchservice/computenode/adduser) com conectividade de rede para conectar-se externamente a um nó de computação em um pool do Lote. Por exemplo, um usuário pode se conectar por RDP (Área de Trabalho Remota) na porta 3389 a um nó de computação em um pool do Windows. Da mesma forma, por padrão, um usuário pode se conectar por SSH (Secure Shell) na porta 22 a um nó de computação em um pool de Linux. 

Em seu ambiente, talvez seja necessário restringir ou desabilitar essas configurações de acesso externo padrão. Você pode modificar essas configurações usando as APIs de Lote para definir a propriedade [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration). 

## <a name="about-the-pool-endpoint-configuration"></a>Sobre a configuração de ponto de extremidade do pool
A configuração de ponto de extremidade consiste em um ou mais [pools de NAT (Conversão de Endereços de Rede)](/rest/api/batchservice/pool/add#inboundnatpool) de portas de front-end. (Não confunda um pool de NAT com o pool de Lote de nós de computação.) Você pode configurar cada pool de NAT para substituir as configurações de conexão padrão nos nós de computação do pool. 

Cada configuração de pool de NAT inclui uma ou mais [Regras de NSG (Grupo de Segurança de Rede)](/rest/api/batchservice/pool/add#networksecuritygrouprule). Cada regra de NSG permite ou nega determinado tráfego de rede para o ponto de extremidade. Você pode optar por permitir ou negar todo o tráfego, o tráfego identificado por uma [marca de serviço](../virtual-network/security-overview.md#service-tags) (como "Internet") ou o tráfego de endereços IP ou sub-redes específicas.

### <a name="considerations"></a>Considerações
* A configuração de ponto de extremidade do pool é parte da [configuração de rede](/rest/api/batchservice/pool/add#NetworkConfiguration) do pool. A configuração de rede, opcionalmente, pode incluir configurações para ingressar o pool em uma [rede virtual do Azure](batch-virtual-network.md). Se você configurar o pool em uma rede virtual, poderá criar regras de NSG que usarão configurações de endereço na rede virtual.
* Você pode configurar várias regras de NSG ao configurar um pool de NAT. As regras são verificadas na ordem de prioridade. Depois que uma regra se aplica, outras regras não são testadas quanto à correspondência.


## <a name="example-deny-all-rdp-traffic"></a>Exemplo: Negar todo o tráfego de RDP

O seguinte snippet código de C# mostra como configurar o ponto de extremidade RDP em nós de computação em um pool do Windows para negar todo o tráfego de rede. O ponto de extremidade usa um pool do front-end de portas no intervalo *60000-60099*. 

```csharp
pool.NetworkConfiguration = new NetworkConfiguration
{
    EndpointConfiguration = new PoolEndpointConfiguration(new InboundNatPool[]
    {
      new InboundNatPool("RDP", InboundEndpointProtocol.Tcp, 3389, 60000, 60099, new NetworkSecurityGroupRule[]
        {
            new NetworkSecurityGroupRule(162, NetworkSecurityGroupRuleAccess.Deny, "*"),
        })
    })    
};
```

## <a name="example-deny-all-ssh-traffic-from-the-internet"></a>Exemplo: Negar todo o tráfego SSH da Internet

O seguinte snippet código de Python mostra como configurar o ponto de extremidade SSH em nós de computação em um pool do Linux para negar todo o tráfego da Internet. O ponto de extremidade usa um pool do front-end de portas no intervalo *4000-4100*. 

```python
pool.network_configuration=batchmodels.NetworkConfiguration(
    endpoint_configuration=batchmodels.PoolEndpointConfiguration(
        inbound_nat_pools=[batchmodels.InboundNATPool(
            name='SSH',
            protocol='tcp',
            backend_port=22,
            frontend_port_range_start=4000,
            frontend_port_range_end=4100,
            network_security_group_rules=[
                batchmodels.NetworkSecurityGroupRule(
                priority=170,
                access=batchmodels.NetworkSecurityGroupRuleAccess.deny,
                source_address_prefix='Internet'
                )
            ]
        )
        ]
    ) 
)
```

## <a name="example-allow-rdp-traffic-from-a-specific-ip-address"></a>Exemplo: Permitir o tráfego RDP de um endereço IP específico

O seguinte snippet código de C# mostra como configurar o ponto de extremidade RDP em nós de computação em um pool do Windows para permitir acesso via RDP apenas do endereço IP *198.51.100.7*. A segunda regra de NSG nega o tráfego que não corresponde ao endereço IP.

```csharp
pool.NetworkConfiguration = new NetworkConfiguration
{
    EndpointConfiguration = new PoolEndpointConfiguration(new InboundNatPool[]
    {
        new InboundNatPool("RDP", InboundEndpointProtocol.Tcp, 3389, 7500, 8000, new NetworkSecurityGroupRule[]
        {   
            new NetworkSecurityGroupRule(179,NetworkSecurityGroupRuleAccess.Allow, "198.51.100.7"),
            new NetworkSecurityGroupRule(180,NetworkSecurityGroupRuleAccess.Deny, "*")
        })
    })    
};
```

## <a name="example-allow-ssh-traffic-from-a-specific-subnet"></a>Exemplo: Permitir o tráfego SSH de uma sub-rede específica

O seguinte snippet de código do Python mostra como configurar o ponto de extremidade SSH em nós de computação em um pool do Linux para permitir acesso somente da sub-rede *192.168.1.0/24*. A segunda regra de NSG nega o tráfego que não corresponde à sub-rede.

```python
pool.network_configuration=batchmodels.NetworkConfiguration(
    endpoint_configuration=batchmodels.PoolEndpointConfiguration(
        inbound_nat_pools=[batchmodels.InboundNATPool(
            name='SSH',
            protocol='tcp',
            backend_port=22,
            frontend_port_range_start=4000,
            frontend_port_range_end=4100,
            network_security_group_rules=[
                batchmodels.NetworkSecurityGroupRule(
                priority=170,
                access='allow',
                source_address_prefix='192.168.1.0/24'
                ),
                batchmodels.NetworkSecurityGroupRule(
                priority=175,
                access='deny',
                source_address_prefix='*'
                )
            ]
        )
        ]
    )
)
```

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre regras de NSG no Azure, veja [Filtrar o tráfego de rede com grupos de segurança de rede](../virtual-network/security-overview.md).

- Para uma visão geral detalhada do Lote, confira [Desenvolver soluções de computação paralela em grande escala com o Lote](batch-api-basics.md).

