---
title: Solucionar problemas de emparelhamento de rede virtual
description: Etapas para resolver a maioria dos problemas de emparelhamento de rede virtual.
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-network
ms.assetid: 1a3d1e84-f793-41b4-aa04-774a7e8f7719
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: f3e1ea8861646490875667f6238b3f8813bd6c4b
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518157"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>Solucionar problemas de emparelhamento de rede virtual

Este guia de solução de problemas fornece etapas para ajudá-lo a resolver a maior parte dos problemas com [emparelhamento de rede virtual](virtual-network-peering-overview.md).

![Diagrama de emparelhamento de rede virtual](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="configure-virtual-network-peering-between-two-virtual-networks"></a>Configurar o emparelhamento de rede virtual entre duas redes virtuais

As redes virtuais estão na mesma assinatura ou em assinaturas diferentes?

### <a name="the-virtual-networks-are-in-the-same-subscription"></a>As redes virtuais estão na mesma assinatura

Para configurar o emparelhamento de rede virtual das redes virtuais que estão na mesma assinatura, use os métodos nos seguintes artigos:

* Se as redes virtuais estiverem na *mesma região*, confira [Criando um emparelhamento](./virtual-network-manage-peering.md#create-a-peering).
* Se as redes virtuais estiverem em *diferentes regiões*, confira [Emparelhamento de rede virtual](./virtual-network-peering-overview.md). 

> [!Note]
> A conectividade não funciona no emparelhamento de rede virtual global para os seguintes recursos: 
>
> * VMs (máquinas virtuais) por trás do SKU ILB (balanceador de carga interno) básico
> * Cache Redis (usa o SKU ILB básico)
> * Gateway de Aplicativo (usa o SKU ILB básico)
> * Conjuntos de dimensionamento de máquinas virtuais (usa o SKU ILB básico)
> * Clusters do Azure Service Fabric (usa o SKU ILB básico)
> * SQL Server Always On (usa o SKU ILB básico)
> * Ambiente do Serviço de Aplicativo do Azure para PowerApps (usa o SKU ILB básico)
> * Gerenciamento de API do Azure (usa o SKU ILB básico)
> * Azure AD DS (Azure Active Directory Domain Services) (usa o SKU ILB básico)

Para obter mais informações, confira [os requisitos e as restrições](./virtual-network-peering-overview.md#requirements-and-constraints) de emparelhamento global.

### <a name="the-virtual-networks-are-in-different-subscriptions-or-active-directory-tenants"></a>As redes virtuais estão em assinaturas ou locatários do Active Directory diferentes

Para configurar o emparelhamento de rede virtual de redes virtuais em assinaturas ou locatários do Active Directory diferentes, confira [Criar emparelhamento em assinaturas diferentes para a CLI do Azure](./create-peering-different-subscriptions.md#cli).

> [!Note]
> Para configurar o emparelhamento de rede, você precisa ter permissões de **Colaborador de rede** em ambas as assinaturas. Para obter mais informações, confira [Permissões de emparelhamento](virtual-network-manage-peering.md#permissions).

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>Configurar o emparelhamento de rede virtual com topologia hub-spoke que usa recursos locais

![Diagrama de emparelhamento de rede virtual com spoke local](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="for-a-site-to-site-connection-or-an-expressroute-connection"></a>Para uma conexão site a site ou do ExpressRoute

Siga as etapas em: [Configurar o trânsito de gateway de VPN para o emparelhamento de rede virtual](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="for-point-to-site-connections"></a>Para conexões ponto a site

1. Siga as etapas em: [Configurar o trânsito de gateway de VPN para o emparelhamento de rede virtual](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Após o emparelhamento de rede virtual ser estabelecido ou alterado, baixe e reinstale o pacote ponto a site para permitir que os clientes ponto a site obtenham as rotas atualizadas para a rede virtual spoke.

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-virtual-network"></a>Configurar o emparelhamento de rede virtual com rede virtual de topologia hub-spoke

![Diagrama de emparelhamento de rede virtual com um spoke de rede virtual](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="the-virtual-networks-are-in-the-same-region"></a>As redes virtuais estão na mesma região


1. Na rede virtual do hub, configure uma NVA (solução de virtualização de rede).
1. Nas redes virtuais do spoke, aplique o tipo de próximo salto “solução de virtualização de rede” às rotas definidas pelo usuário.

Para obter mais informações, confira [Encadeamento de serviço](./virtual-network-peering-overview.md#service-chaining).

> [!Note]
> Se você precisar de ajuda para configurar uma NVA, [entre em contato com o fornecedor da NVA](https://mskb.pkisolutions.com/kb/2984655).

Para obter ajuda na solução de problemas de configuração e roteamento do dispositivo NVA, confira [Problemas com solução de virtualização de rede no Azure](./virtual-network-troubleshoot-nva.md).

### <a name="the-virtual-networks-are-in-different-regions"></a>As redes virtuais estão em regiões diferentes

Agora há suporte ao tráfego em emparelhamento de rede virtual global. A conectividade não funciona no emparelhamento de rede virtual global para os seguintes recursos:

* VMs por trás do SKU ILB básico
* Cache Redis (usa o SKU ILB básico)
* Gateway de Aplicativo (usa o SKU ILB básico)
* Conjuntos de dimensionamento (usa o SKU ILB básico)
* Clusters de Service Fabric (usa o SKU ILB básico)
* SQL Server Always On (usa o SKU ILB básico)
* Ambiente do Serviço de Aplicativo (usa o SKU ILB básico)
* Gerenciamento de API (usa o SKU ILB básico)
* Azure AD DS (usa o SKU ILB básico)

Para saber mais sobre requisitos de emparelhamento global e restrições, confira [Emparelhamento de rede virtual](./virtual-network-peering-overview.md#requirements-and-constraints).

## <a name="troubleshoot-a-connectivity-issue-between-two-peered-virtual-networks"></a>Solucionar um problema de conectividade entre duas redes virtuais emparelhadas

Entre no [portal do Azure](https://portal.azure.com/) com uma conta que tenha as [funções e permissões](virtual-network-manage-peering.md#permissions) necessárias. Selecione a rede virtual, selecione **Emparelhamento** e verifique o campo de **Status**. Qual é o status?

### <a name="the-peering-status-is-connected"></a>O status de emparelhamento é “Conectado”

Para solucionar o problema:

1. Verifique os fluxos de tráfego de rede:

   Use [Solução de problemas de conexão](../network-watcher/network-watcher-connectivity-overview.md) e [Verificação de fluxo de IP](../network-watcher/network-watcher-ip-flow-verify-overview.md) da VM de origem para a VM de destino para determinar se há um NSG ou UDR que esteja causando interferência nos fluxos de tráfego.

   Se você estiver usando um firewall ou uma NVA: 
   1. Documente os parâmetros UDR para poder restaurá-los após a conclusão desta etapa.
   2. Remova o UDR da sub-rede da VM ou NIC de origem que aponta para a NVA como o próximo salto. Verifique a conectividade da VM de origem diretamente com o destino que está ignorando a NVA. Se a etapa não funcionar, confira o [Solucionador de problemas de NVA](./virtual-network-troubleshoot-nva.md).

2. Faça um rastreamento de rede: 
   1. Inicie um rastreamento de rede na VM de destino. No caso do Windows, você pode usar **Netsh**. No caso do Linux, use **TCPDump**.
   2. Execute **TcpPing** ou **o PsPing** do IP de origem para o IP de destino.

      Este é um exemplo de um comando **TcpPing**: `tcping64.exe -t <destination VM address> 3389`

   3. Após concluir o **TcpPing**, interrompa o rastreamento de rede no destino.
   4. Se os pacotes chegam da origem, não há nenhum problema com a rede. Examine o firewall da VM e o aplicativo que está escutando nessa porta para localizar o problema de configuração.

   > [!Note]
   > Não é possível se conectar aos seguintes tipos de recursos por meio do emparelhamento de rede virtual global (redes virtuais em regiões diferentes):
   >
   > * VMs por trás do SKU ILB básico
   > * Cache Redis (usa o SKU ILB básico)
   > * Gateway de Aplicativo (usa o SKU ILB básico)
   > * Conjuntos de dimensionamento (usa o SKU ILB básico)
   > * Clusters de Service Fabric (usa o SKU ILB básico)
   > * SQL Server Always On (usa o SKU ILB básico)
   > * Ambiente do Serviço de Aplicativo (usa o SKU ILB básico)
   > * Gerenciamento de API (usa o SKU ILB básico)
   > * Azure AD DS (usa o SKU ILB básico)

Para obter mais informações, confira [os requisitos e as restrições](./virtual-network-peering-overview.md#requirements-and-constraints) de emparelhamento global.

### <a name="the-peering-status-is-disconnected"></a>O status de emparelhamento é “Desconectado”

Para resolver esse problema, exclua o emparelhamento das duas redes virtuais e crie-as novamente.

## <a name="troubleshoot-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-an-on-premises-resource"></a>Solucionar um problema de conectividade entre uma rede virtual hub-spoke e um recurso local

Sua rede usa um gateway de VPN ou uma NVA de terceiros?

### <a name="my-network-uses-a-third-party-nva-or-vpn-gateway"></a>Minha rede usa um gateway de VPN ou uma NVA de terceiros

Para solucionar problemas de conectividade que afetam um gateway de VPN ou uma NVA de terceiros, confira os seguintes artigos:

* [Solucionador de problemas de NVA](./virtual-network-troubleshoot-nva.md)
* [Encadeamento de serviços](./virtual-network-peering-overview.md#service-chaining)

### <a name="my-network-does-not-use-a-third-party-nva-or-vpn-gateway"></a>Minha rede não usa um gateway de VPN ou uma NVA de terceiros

A rede virtual do hub e a rede virtual spoke têm um gateway de VPN?

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-have-a-vpn-gateway"></a>Tanto a rede virtual do Hub quanto a rede virtual spoke têm um gateway de VPN

Não há suporte ao uso de um gateway remoto.

Se a rede virtual spoke já tiver um gateway de VPN, a opção **Usar gateway remoto** não terá suporte na rede virtual spoke. Isso ocorre devido a uma limitação no emparelhamento de rede virtual.

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-do-not-have-a-vpn-gateway"></a>Nenhuma das duas redes virtuais, a do hub e a spoke, tem um gateway de VPN

Para conexões site a site ou do Azure ExpressRoute, verifique as seguintes causas primárias de problemas de conectividade entre o local e a rede virtual remota:

* Na rede virtual que tem um gateway, verifique se a caixa de seleção **Permitir o tráfego encaminhado** está marcada.
* Na rede virtual que não tem um gateway, verifique se a caixa de seleção **Usar gateway remoto** está marcada.
* Peça ao administrador de rede que verifique se todos os seus dispositivos locais têm o espaço de endereço de rede virtual remota adicionado.

Para conexões ponto a site:

* Na rede virtual que tem um gateway, verifique se a caixa de seleção **Permitir o tráfego encaminhado** está marcada.
* Na rede virtual que não tem um gateway, verifique se a caixa de seleção **Usar gateway remoto** está marcada.
* Baixe e reinstale o pacote do cliente ponto a site. As rotas de rede virtual emparelhadas recentemente não adicionam rotas a clientes ponto a site automaticamente.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>Solucionar um problema de conectividade de rede hub-spoke entre redes virtuais spoke na mesma região

Uma rede de Hub deve incluir uma NVA. Configure UDRs em spokes que tenham uma NVA definida como o próximo salto e habilite **Permitir tráfego encaminhado** na rede virtual do hub.

Para obter mais informações, confira [Encadeamento de serviço](./virtual-network-peering-overview.md#service-chaining) e converse sobre esses requisitos com o [fornecedor de NVA](https://mskb.pkisolutions.com/kb/2984655) escolhido.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>Solucionar um problema de conectividade de rede hub-spoke entre redes virtuais spoke em regiões diferentes

Agora há suporte ao tráfego em emparelhamento de rede virtual global. A conectividade não funciona no emparelhamento de rede virtual global para os seguintes recursos:

* VMs por trás do SKU ILB básico
* Cache Redis (usa o SKU ILB básico)
* Gateway de Aplicativo (usa o SKU ILB básico)
* Conjuntos de dimensionamento (usa o SKU ILB básico)
* Clusters de Service Fabric (usa o SKU ILB básico)
* SQL Server Always On (usa o SKU ILB básico)
* Ambiente do Serviço de Aplicativo (usa o SKU ILB básico)
* Gerenciamento de API (usa o SKU ILB básico)
* Azure AD DS (usa o SKU ILB básico)

Para obter mais informações, confira [os requisitos e as restrições](./virtual-network-peering-overview.md#requirements-and-constraints) de emparelhamento global e as [Topologias de VPN diferentes](/archive/blogs/igorpag/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2).

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>Solucionar um problema de conectividade de rede hub-spoke entre um aplicativo Web e a rede virtual spoke

Para solucionar o problema:

1. Entre no portal do Azure. 
1. No aplicativo Web, selecione **rede** e, em seguida, **Integração VNET**.
1. Verifique se consegue ver a rede virtual remota. Insira manualmente o espaço de endereço de rede virtual remota (**Rede de Sincronização** e **Adicionar Rotas**).

Para obter mais informações, consulte os seguintes artigos:

* [Integrar seu aplicativo a uma rede virtual do Azure](../app-service/web-sites-integrate-with-vnet.md)
* [Sobre o roteamento VPN Ponto a Site](../vpn-gateway/vpn-gateway-about-point-to-site-routing.md)

## <a name="troubleshoot-a-virtual-network-peering-configuration-error-message"></a>Solucionar problemas de uma mensagem de erro de configuração de emparelhamento de rede virtual 

### <a name="current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>O `<TENANT ID>` do locatário atual não está autorizado a acessar a assinatura vinculada

Para resolver o problema, confira [Criar emparelhamento – CLI do Azure](./create-peering-different-subscriptions.md#cli).

### <a name="not-connected"></a>Não conectado

Para resolver esse problema, exclua o emparelhamento das duas redes virtuais e crie-as novamente.

### <a name="failed-to-peer-a-databricks-virtual-network"></a>Falha ao emparelhar uma rede virtual do Databricks

Para resolver o problema, configure o emparelhamento de rede virtual em **Azure Databricks** e especifique a rede virtual de destino usando **ID de recurso**. Para obter mais informações, confira [Emparelhar uma rede virtual do Databricks com uma rede virtual remota](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-peering#id2).

### <a name="the-remote-virtual-network-lacks-a-gateway"></a>A rede virtual remota não tem um gateway

Esse problema ocorre quando você emparelha redes virtuais de locatários diferentes e, posteriormente, deseja configurar `Use Remote Gateways`. Uma limitação do portal do Azure é que ele não pode validar a presença de um gateway de rede virtual na rede virtual de outro locatário.

Há três maneiras de resolver o erro:

 * Exclua os emparelhamentos e ative a opção `Use Remote Gateways` ao criar um novo emparelhamento.
 * Em vez do portal do Azure, use o PowerShell ou a CLI para habilitar `Use Remote Gateways`.

## <a name="next-steps"></a>Próximas etapas

* [Solucionar problemas de conectividade entre VMs do Azure](./virtual-network-troubleshoot-connectivity-problem-between-vms.md)