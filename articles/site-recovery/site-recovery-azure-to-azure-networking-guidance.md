---
title: "Diretrizes de rede Azure Site Recovery para replicar máquinas virtuais de Azure para Azure | Microsoft Docs"
description: "Diretrizes de rede para replicar máquinas virtuais do Azure"
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/31/2017
ms.author: sujayt
ms.openlocfilehash: 99c08a1efbc610959fb4ba824dcb0601efac5877
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="networking-guidance-for-replicating-azure-virtual-machines"></a>Diretrizes de rede para replicar máquinas virtuais do Azure

>[!NOTE]
> Replicação de recuperação de site para máquinas virtuais do Azure está atualmente em visualização.

Este artigo fornece detalhes sobre as diretrizes de rede para o Azure Site Recovery quando você estiver replicando e recuperando máquinas virtuais do Azure de uma região para outra região. Para saber mais sobre os requisitos do Azure Site Recovery, confira o artigo [pré-requisitos](site-recovery-prereq.md).

## <a name="site-recovery-architecture"></a>Arquitetura do Site Recovery

Recuperação de site fornece uma maneira simple e fácil para replicar os aplicativos executados em máquinas virtuais do Azure para outra região do Azure para que possam ser recuperados se houver uma interrupção na região primária. Saiba mais sobre [este cenário e arquitetura de recuperação de Site](site-recovery-azure-to-azure-architecture.md).

## <a name="your-network-infrastructure"></a>Sua infraestrutura de rede

O diagrama a seguir ilustra o ambiente típico do Azure para um aplicativo em execução em máquinas virtuais do Azure:

![ambiente do cliente](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Se você estiver usando uma conexão VPN ou o Azure ExpressRoute de uma rede local do Azure, o ambiente tem esta aparência:

![ambiente do cliente](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

Normalmente, os clientes protegem suas redes usando firewalls e/ou grupos de segurança de rede (NSGs). Os firewalls podem usar a lista branca baseada em URL ou IP para controlar a conectividade de rede. Os NSGs permitem regras usando intervalos de IP para controlar a conectividade de rede.

>[!IMPORTANT]
> Se você estiver usando um proxy autenticado para controlar a conectividade de rede, não há suporte e não é possível habilitar a replicação de recuperação de Site.

As seções a seguir discutem as alterações de conectividade de saída de rede que serão necessárias para máquinas virtuais do Azure para replicação de recuperação de Site para o trabalho.

## <a name="outbound-connectivity-for-azure-site-recovery-urls"></a>Conectividade de saída para URLs de recuperação de Site do Azure

Se você estiver usando qualquer proxy firewall baseado em URL para controlar a conectividade de saída, certifique-se de permitir estes URLs de serviço do Azure Site Recovery exigidos:


**URL** | **Finalidade**  
--- | ---
*.blob.core.windows.net | Necessário para que os dados possam ser gravados para a conta de armazenamento de cache da região de origem da VM.
login.microsoftonline.com | Necessário para autorização e autenticação para as URLs do serviço de recuperação de Site.
*.hypervrecoverymanager.windowsazure.com | Necessário para que a comunicação de serviço de recuperação de Site possa ocorrer da VM.
*.servicebus.windows.net | Necessário para que os dados de monitoramento e diagnóstico de recuperação de Site possam ser gravados da VM.

## <a name="outbound-connectivity-for-azure-site-recovery-ip-ranges"></a>Conectividade de saída para os intervalos de IP de recuperação de Site do Azure

>[!NOTE]
> Para criar automaticamente as regras NSG necessárias no grupo de segurança de rede, você pode [baixar e usar esse script](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702).

>[!IMPORTANT]
> * É recomendável que você crie as regras NSG necessárias em um grupo de segurança de rede de teste e verifique se não há nenhum problema antes de criar as regras em um grupo de segurança de rede de produção.
> * Para criar o número necessário de regras NSG, certifique-se de que sua assinatura está na lista de permissões. Contate o suporte para aumentar o limite de regra NSG em sua assinatura.

Se você estiver usando qualquer proxy firewall baseado em IP ou regras NSG para controlar a conectividade de saída, os seguintes intervalos IP devem estar na lista de permissões, dependendo dos locais de origem e destino das máquinas virtuais:

- Todos os intervalos IP que correspondem ao local de origem. (Você pode baixar os [intervalos IP](https://www.microsoft.com/download/confirmation.aspx?id=41653).) Permissão é necessário para que os dados possam ser gravados para a conta de armazenamento de cache da região de origem da VM.

- Todos os intervalos IP que correspondam ao Office 365 [identidade e autenticação de pontos de extremidade do IP V4](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity).

    >[!NOTE]
    > Se novos IPs forem adicionados aos intervalos de IP do Office 365 no futuro, você precisa criar novas regras NSG.

- Ponto de extremidade de serviço de recuperação de Site IPs ([disponíveis em um arquivo XML](https://aka.ms/site-recovery-public-ips)), que depende de seu local de destino:

   **Local de destino** | **Serviço de Recuperação do Site IPs** |  **Monitoramento de Recuperação de site IP**
   --- | --- | ---
   Ásia Oriental | 52.175.17.132 | 13.94.47.61
   Sudeste Asiático | 52.187.58.193 | 13.76.179.223
   Índia Central | 52.172.187.37 | 104.211.98.185
   Sul da Índia | 52.172.46.220 | 104.211.224.190
   Centro-Norte dos EUA | 23.96.195.247 | 168.62.249.226
   Norte da Europa | 40.69.212.238 | 52.169.18.8
   Europa Ocidental | 52.166.13.64 | 40.68.93.145
   Leste dos EUA | 13.82.88.226 | 104.45.147.24
   Oeste dos EUA | 40.83.179.48 | 104.40.26.199
   Centro-Sul dos Estados Unidos | 13.84.148.14 | 104.210.146.250
   Centro dos EUA | 40.69.144.231 | 52.165.34.144
   Leste dos EUA 2 | 52.184.158.163 | 40.79.44.59
   Leste do Japão | 52.185.150.140 | 138.91.1.105
   Oeste do Japão | 52.175.146.69 | 138.91.17.38
   Sul do Brasil | 191.234.185.172 | 23.97.97.36
   Leste da Austrália | 104.210.113.114 | 191.239.64.144
   Sudeste da Austrália | 13.70.159.158 | 191.239.160.45
   Canadá Central | 52.228.36.192 | 40.85.226.62
   Leste do Canadá | 52.229.125.98 | 40.86.225.142
   Centro-Oeste dos EUA | 52.161.20.168 | 13.78.149.209
   Oeste dos EUA 2 | 52.183.45.166 | 13.66.228.204
   Oeste do Reino Unido | 51.141.3.203 | 51.141.14.113
   Sul do Reino Unido | 51.140.43.158 | 51.140.189.52
   Sul do Reino Unido 2 | 13.87.37.4| 13.87.34.139
   Norte do Reino Unido | 51.142.209.167 | 13.87.102.68
   Coreia Central | 52.231.28.253 | 52.231.32.85
   Sul da Coreia | 52.231.298.185 | 52.231.200.144

## <a name="sample-nsg-configuration"></a>Exemplo de Configuração do NSG
Esta seção explica as etapas para configurar regras NSG para que a replicação de recuperação de Site possa trabalhar em uma máquina virtual. Se você estiver usando regras NSG para controlar a conectividade de saída, use regras de "Permitir HTTPS de saída" para todos os intervalos IP necessários.

>[!Note]
> Para criar automaticamente as regras NSG necessárias no grupo de segurança de rede, você pode [baixar e usar esse script](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702).

Por exemplo, se o local de origem da sua VM é "Leste dos EUA" e o local de destino de replicação é "Centro dos EUA", siga as etapas nas próximas duas seções.

>[!IMPORTANT]
> * É recomendável que você crie as regras NSG necessárias em um grupo de segurança de rede de teste e verifique se não há nenhum problema antes de criar as regras em um grupo de segurança de rede de produção.
> * Para criar o número necessário de regras NSG, certifique-se de que sua assinatura está na lista de permissões. Contate o suporte para aumentar o limite de regra NSG em sua assinatura.

### <a name="nsg-rules-on-the-east-us-network-security-group"></a>Regras NSG no grupo de segurança de rede Leste dos EUA

* Criar regras que correspondam aos [intervalos de IP do Leste dos EUA](https://www.microsoft.com/download/confirmation.aspx?id=41653). Isso é necessário para que os dados possam ser gravados para a conta de armazenamento de cache da VM.

* Criar regras para todos os intervalos IP que correspondam ao Office 365 [identidade e autenticação de pontos de extremidade do IP V4](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity).

* Criar regras que correspondam ao local de destino:

   **Localidade** | **Serviço de Recuperação do Site IPs** |  **Monitoramento de Recuperação de site IP**
    --- | --- | ---
   Centro dos EUA | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules-on-the-central-us-network-security-group"></a>Regras NSG no grupo de segurança de rede Centro dos EUA

Essas regras são necessárias para que a replicação possa ser ativada da região de destino para a região de origem após o failover:

* Regras que correspondam aos [intervalos de IP do Centro dos EUA](https://www.microsoft.com/download/confirmation.aspx?id=41653). Isso é necessário para que os dados possam ser gravados para a conta de armazenamento de cache da VM.

* Regras para todos os intervalos IP que correspondam ao Office 365 [identidade e autenticação de pontos de extremidade do IP V4](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity).

* Regras que correspondam ao local de origem:

   **Localidade** | **Serviço de Recuperação do Site IPs** |  **Monitoramento de Recuperação de site IP**
    --- | --- | ---
   Leste dos EUA | 13.82.88.226 | 104.45.147.24


## <a name="guidelines-for-existing-azure-to-on-premises-expressroutevpn-configuration"></a>Diretrizes para configuração da ExpressRoute/VPN do Azure-para-local existente

Se você tiver uma conexão de ExpressRoute ou VPN entre locais e o local de origem no Azure, siga as orientações nesta seção.

### <a name="forced-tunneling-configuration"></a>Configuração de túnel forçado

Uma configuração comum de cliente é definir sua própria rota padrão (0.0.0.0/0) que force o tráfego de saída da Internet a fluir através local. Não recomendamos isso. O tráfego de replicação e comunicação de serviço de recuperação de Site não devem deixar o limite do Azure. A solução é adicionar rotas definidas pelo usuário (UDRs) para [esses intervalos IP](#outbound-connectivity-for-azure-site-recovery-ip-ranges) para que o tráfego de replicação não seja local.

### <a name="connectivity-between-the-target-and-on-premises-location"></a>Conectividade entre o local de destino e no local

Siga estas diretrizes para conexões entre o local de destino e o local:
- Se seu aplicativo precisa se conectar a máquinas locais ou se houver clientes que se conectam ao aplicativo do local por VPN/ExpressRoute local, certifique-se de que você tenha pelo menos uma [conexão site a site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) entre o datacenter local e sua região do Azure de destino.

- Se você estiver esperando muito tráfego fluindo entre o datacenter local e sua região do Azure de destino, você deve criar outra [conexão ExpressRoute](../expressroute/expressroute-introduction.md) entre a região do Azure de destino e o datacenter local.

- Se você deseja manter IPs para as máquinas virtuais depois do failover, mantenha a conexão de site para site/ExpressRoute da região de destino em um estado desconectado. Isso é para certificar-se de que não há nenhum conflito de intervalo entre os intervalos de IP da região de origem e os intervalos de IP da região de destino.

### <a name="best-practices-for-expressroute-configuration"></a>Práticas recomendadas para configuração de ExpressRoute
Siga estas práticas recomendadas para configuração de ExpressRoute:

- Você precisa criar um circuito de ExpressRoute nas regiões de origem e de destino. Em seguida, você precisa criar uma conexão entre:
  - A Rede Virtual de origem e o circuito ExpressRoute.
  - A Rede Virtual de destino e o circuito ExpressRoute.

- Como parte do padrão de ExpressRoute, você pode criar circuitos na mesma região geopolítica. Para criar circuitos ExpressRoute em diferentes regiões geopolíticas, o Azure ExpressRoute Premium é necessário, o que envolve um custo incremental. (Se você já estiver usando o ExpressRoute Premium, não há nenhum custo extra.) Para obter mais detalhes, consulte o [documento de locais de ExpressRoute](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) e [preços do ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).

- É recomendável que você use intervalos IP diferentes em regiões de origem e destino. O circuito de ExpressRoute não será capaz de se conectar com duas redes virtuais do Azure de mesmo intervalo de IP ao mesmo tempo.

- Você pode criar redes virtuais com os mesmos intervalos IP em ambas as regiões e, em seguida, criar circuitos de ExpressRoute em ambas as regiões. No caso de um evento de failover, desconecte o circuito da rede virtual de origem e conecte o circuito na rede virtual de destino.

 >[!IMPORTANT]
 > Se a região principal estiver completamente inativa, a operação de desconectar pode falhar. Que impedirá a rede virtual de destino de conseguir conectividade de ExpressRoute.

## <a name="next-steps"></a>Próximas etapas
Inicie a proteção de suas cargas de trabalho ao [replicar máquinas virtuais do Azure](site-recovery-azure-to-azure.md).
