---
title: Integrar o Firewall do Azure com o Azure Standard Load Balancer
description: Você pode integrar um Firewall do Azure em uma rede virtual com um Azure Standard Load Balancer (público ou interno).
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 09/25/2020
ms.author: victorh
ms.openlocfilehash: 3b8fbc47b46f8be6e4ad7636a1d7552445501f0f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94653157"
---
# <a name="integrate-azure-firewall-with-azure-standard-load-balancer"></a>Integrar o Firewall do Azure com o Azure Standard Load Balancer

Você pode integrar um Firewall do Azure em uma rede virtual com um Azure Standard Load Balancer (público ou interno). 

O design preferencial é integrar um balanceador de carga interno com o Firewall do Azure, pois é um design muito mais simples. Você pode usar um balanceador de carga público, se já tiver um implantado e desejar mantê-lo em vigor. No entanto, você precisa estar ciente de um problema de roteamento assimétrico que pode interromper a funcionalidade com o cenário de balanceador de carga público.

Para obter mais informações sobre o Azure Load Balancer, confira [O que é o Azure Load Balancer?](../load-balancer/load-balancer-overview.md)

## <a name="public-load-balancer"></a>Balanceador de carga público

Com um balanceador de carga público, o balanceador de carga é implantado com um endereço IP de front-end público.

### <a name="asymmetric-routing"></a>Roteamento assimétrico

O roteamento assimétrico ocorre quando um pacote usa um caminho para o destino e outro caminho para retornar para a origem. Isso ocorre quando uma sub-rede tem uma rota padrão que vai para o endereço IP privado do firewall e você está usando um balanceador de carga público. Nesse caso, o tráfego de entrada do balanceador de carga é recebido por meio de seu endereço IP público, mas o caminho de retorno passa pelo endereço IP privado do firewall. Como o firewall tem monitoração de estado, ele descarta o pacote de retorno porque o firewall não está ciente dessa sessão estabelecida.

### <a name="fix-the-routing-issue"></a>Corrigir o problema de roteamento

Quando você implanta um Firewall do Azure em uma sub-rede, uma etapa é criar uma rota padrão para a sub-rede, direcionando os pacotes por meio do endereço IP privado do firewall localizado no AzureFirewallSubnet. Para obter mais informações, consulte [tutorial: implantar e configurar o Firewall do Azure usando o portal do Azure](tutorial-firewall-deploy-portal.md#create-a-default-route).

Quando introduz o firewall em seu cenário de balanceador de carga, você deseja que o tráfego de Internet entre por meio do endereço IP público do firewall. Daí, o firewall aplica suas regras de firewall e NATs aos pacotes para o endereço IP público do balanceador de carga. E o problema ocorre aí. Os pacotes chegam no endereço IP público do firewall, mas retornam para o firewall por meio do endereço IP privado (usando a rota padrão).
Para evitar esse problema, crie uma rota de host adicional para o endereço IP público do firewall. Pacotes que vão para o endereço IP público do firewall são roteadas pela Internet. Isso evita o uso da rota padrão para o endereço IP privado do firewall.

![Roteamento assimétrico](media/integrate-lb/Firewall-LB-asymmetric.png)

### <a name="route-table-example"></a>Exemplo de tabela de rotas

Por exemplo, as rotas a seguir são para um firewall no endereço IP público 20.185.97.136 e o endereço IP privado 10.0.1.4.

> [!div class="mx-imgBorder"]
> ![Tabela de rotas](media/integrate-lb/route-table.png)

### <a name="nat-rule-example"></a>Exemplo de regra NAT

No exemplo a seguir, uma regra NAT traduz o tráfego RDP para o firewall em 20.185.97.136 para o balanceador de carga em 20.42.98.220:

> [!div class="mx-imgBorder"]
> ![Regra NAT](media/integrate-lb/nat-rule-02.png)

### <a name="health-probes"></a>Investigações de integridade

Lembre-se de que você precisa ter um serviço Web em execução nos hosts no pool do balanceador de carga se usar investigações de integridade TCP para porta 80 ou investigações HTTP/HTTPS.

## <a name="internal-load-balancer"></a>Balanceador de carga interno

Com um balanceador de carga interno, o balanceador de carga é implantado com um endereço IP de front-end privado.

Não há nenhum problema de roteamento assimétrico com esse cenário. Os pacotes de entrada chegam ao endereço IP público do firewall, são convertidos para o endereço IP privado do balanceador de carga e, em seguida, retornam para o endereço IP privado do firewall usando o mesmo caminho de retorno.

Portanto, você pode implantar este cenário de forma semelhante ao cenário do balanceador de carga público, mas sem a necessidade da rota de host com endereço IP público do firewall.

>[!NOTE]
>As máquinas virtuais do pool de back-end não terão conectividade de saída com a Internet com essa configuração. </br> Para obter mais informações sobre como fornecer conectividade de saída, confira: </br> **[Conexões de saída no Azure](../load-balancer/load-balancer-outbound-connections.md)**</br> Opções para fornecer conectividade: </br> **[Configuração do balanceador de carga somente de saída](../load-balancer/egress-only.md)** </br> [**O que é NAT de Rede Virtual?**](../virtual-network/nat-overview.md)


## <a name="additional-security"></a>Segurança adicional

Para melhorar ainda mais a segurança do seu cenário de balanceamento de carga, você pode usar NSGs (grupos de segurança de rede).

Por exemplo, você pode criar um NSG na sub-rede de back-end em que se encontram as máquinas virtuais de balanceamento de carga. Permita o tráfego de entrada provenientes do endereço IP/porta do firewall.

![Grupo de segurança de rede](media/integrate-lb/nsg-01.png)

Para obter mais informações sobre NSGs, consulte [grupos de segurança](../virtual-network/network-security-groups-overview.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba como [implantar e configurar um firewall do Azure](tutorial-firewall-deploy-portal.md).