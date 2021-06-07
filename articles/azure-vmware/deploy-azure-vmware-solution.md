---
title: Implantar e configurar a Solução VMware no Azure
description: Saiba como usar as informações coletadas na fase de planejamento para implantar e configurar a nuvem privada da Solução VMware no Azure.
ms.topic: tutorial
ms.custom: contperf-fy21q3
ms.date: 02/17/2021
ms.openlocfilehash: 48b6927407a95d41603c3032f298ffc28def9693
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103462449"
---
# <a name="deploy-and-configure-azure-vmware-solution"></a>Implantar e configurar a Solução VMware no Azure

Neste artigo, você usará as informações da [seção de planejamento](production-ready-deployment-steps.md) para implantar e configurar a Solução VMware no Azure. 

>[!IMPORTANT]
>Se você ainda não tiver definido as informações, volte para a [seção de planejamento](production-ready-deployment-steps.md) antes de continuar.


## <a name="create-an-azure-vmware-solution-private-cloud"></a>Criar uma nuvem privada da Solução VMware no Azure

Siga os pré-requisitos e as etapas do tutorial [Criar uma nuvem privada da Solução VMware no Azure](tutorial-create-private-cloud.md). Você pode criar uma nuvem privada da Solução VMware no Azure usando o [portal do Azure](tutorial-create-private-cloud.md#azure-portal) ou a [CLI do Azure](tutorial-create-private-cloud.md#azure-cli).  

>[!NOTE]
>Para obter uma visão geral de ponta a ponta desta etapa, assista ao vídeo [Solução VMware no Azure: implantação](https://www.youtube.com/embed/gng7JjxgayI).

## <a name="create-the-jump-box"></a>Criar o jumpbox

>[!IMPORTANT]
>Se você tiver deixado a opção **Rede Virtual** em branco durante a etapa de provisionamento inicial na tela **Criar uma Nuvem Privada**, conclua o tutorial [Configurar a rede da nuvem privada do VMware](tutorial-configure-networking.md) **antes** de continuar com esta seção.  

Depois de implantar a Solução VMware no Azure, você criará o jumpbox da rede virtual que se conecta com o vCenter e com o NSX. Após a configuração dos circuitos do ExpressRoute e do Alcance Global do ExpressRoute, o jumpbox não será necessário.  Mas é útil acessar o vCenter e o NSX na Solução VMware no Azure.  

:::image type="content" source="media/pre-deployment/jump-box-diagram.png" alt-text="Criar o jumpbox da Solução VMware no Azure" border="false" lightbox="media/pre-deployment/jump-box-diagram.png":::

Para criar uma VM (máquina virtual) na rede virtual que você [identificou ou criou como parte do processo de implantação](production-ready-deployment-steps.md#attach-azure-virtual-network-to-azure-vmware-solution), siga estas instruções: 

[!INCLUDE [create-avs-jump-box-steps](includes/create-jump-box-steps.md)]

## <a name="connect-to-a-virtual-network-with-expressroute"></a>Conectar-se a uma rede virtual com o ExpressRoute

>[!IMPORTANT]
>Se você já tiver definido uma rede virtual na tela de implantação no Azure, pule para a próxima seção.

Se você não tiver definido uma rede virtual na etapa de implantação e sua intenção for conectar o ExpressRoute da Solução VMware no Azure com um Gateway do ExpressRoute existente, siga estas etapas.

[!INCLUDE [connect-expressroute-to-vnet](includes/connect-expressroute-vnet.md)]

## <a name="verify-network-routes-advertised"></a>Verificar as rotas de rede anunciadas

O jumpbox está na rede virtual na qual a Solução VMware no Azure se conecta por meio do circuito ExpressRoute dela.  No Azure, acesse o adaptador de rede do jumpbox e [veja as rotas em vigor](../virtual-network/manage-route-table.md#view-effective-routes).

Na lista de rotas em vigor, serão exibidas as redes criadas como parte da implantação da Solução VMware no Azure. Você verá várias redes que foram derivadas da [`/22` rede que você definiu](production-ready-deployment-steps.md#ip-address-segment-for-private-cloud-management) ao [criar uma nuvem privada](#create-an-azure-vmware-solution-private-cloud).  

:::image type="content" source="media/pre-deployment/azure-vmware-solution-effective-routes.png" alt-text="Verificar as rotas de rede anunciadas da Solução VMware no Azure para a Rede Virtual do Azure" lightbox="media/pre-deployment/azure-vmware-solution-effective-routes.png":::

Neste exemplo, a rede 10.74.72.0/22 foi inserida durante a implantação, derivando as redes /24.  Se aparecer algo semelhante, você poderá se conectar com o vCenter na Solução VMware no Azure.

## <a name="connect-and-sign-in-to-vcenter-and-nsx-t"></a>Conectar-se e entrar no vCenter e no NSX-T

Faça logon no jumpbox que você criou na etapa anterior. Depois de fazer logon, abra um navegador da Web, navegue para o vCenter e o NSX-T Manager e entre neles.  

Você pode identificar os endereços IP e as credenciais do console do vCenter-T e do NSX-T Manager no portal do Azure.  Selecione sua nuvem privada e selecione **Gerenciar** > **Identidade**.

>[!TIP]
>Selecione **Gerar uma nova senha** para gerar novas senhas do vCenter e do NSX-T.

:::image type="content" source="media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="Exibir URLs e credenciais da nuvem privada do vCenter e do NSX Manager." border="true":::



## <a name="create-a-network-segment-on-azure-vmware-solution"></a>Criar um segmento de rede na Solução VMware no Azure

Use o NSX-T Manager para criar segmentos de rede no ambiente da Solução VMware no Azure.  Você definiu as redes que deseja criar na [seção de planejamento](production-ready-deployment-steps.md).  Se você não as tiver definido, volte para a [seção de planejamento](production-ready-deployment-steps.md) antes de continuar.

>[!IMPORTANT]
>Verifique se o bloco de endereço de rede CIDR que você definiu não se sobrepõe a nada nos ambientes do Azure ou locais.  

Siga o tutorial [Criar um segmento de rede do NSX-T na Solução VMware no Azure](tutorial-nsx-t-network-segment.md) para criar um segmento de rede do NSX-T na Solução VMware no Azure.

## <a name="verify-advertised-nsx-t-segment"></a>Verificar o segmento do NSX-T anunciado

Volte para a etapa [Verificar as rotas de rede anunciadas](#verify-network-routes-advertised). Você verá outras rotas na lista que representa os segmentos de rede criados na etapa anterior.  

Para máquinas virtuais, você atribuirá os segmentos criados na etapa [Criar um segmento de rede na Solução VMware no Azure](#create-a-network-segment-on-azure-vmware-solution).  

Como o DNS é necessário, identifique qual servidor DNS você deseja usar.  

- Se você tiver o Alcance Global do ExpressRoute configurado, use qualquer servidor DNS que você usaria localmente.  
- Se você tiver um servidor DNS no Azure, use-o.  
- Se você não tiver nenhum dos dois, use o servidor DNS que o jumpbox estiver usando.

>[!NOTE]
>Esta etapa é para identificar o servidor DNS e não é feita nenhuma configuração nela.

## <a name="optional-provide-dhcp-services-to-nsx-t-network-segment"></a>(Opcional) Fornecer serviços DHCP para o segmento de rede do NSX-T

Se planejar usar o DHCP nos segmentos do NSX-T, continue nesta seção. Caso contrário, pule para a seção [Adicionar uma VM no segmento de rede do NSX-T](#add-a-vm-on-the-nsx-t-network-segment).  

Como você criou um segmento de rede do NSX-T, será possível criar e gerenciar o DHCP na Solução VMware no Azure de dois modos:

* Caso esteja usando o NSX-T para hospedar o servidor DHCP, será necessário [criar um servidor DHCP](manage-dhcp.md#create-a-dhcp-server) e executar a [retransmissão para esse servidor](manage-dhcp.md#create-dhcp-relay-service). 
* Caso esteja usando um servidor DHCP externo de terceiros em sua rede, será necessário [criar um serviço de retransmissão DHCP](manage-dhcp.md#create-dhcp-relay-service).  Para essa opção, [você faz apenas a configuração da retransmissão](manage-dhcp.md#create-dhcp-relay-service).


## <a name="add-a-vm-on-the-nsx-t-network-segment"></a>Adicionar uma VM ao segmento de rede do NSX-T

No vCenter da Solução VMware no Azure, implante uma VM e use-a para verificar a conectividade das redes da Solução VMware no Azure com:

- A Internet
- Redes Virtuais do Azure
- No local.  

Implante a VM como faria em qualquer ambiente vSphere.  Anexe a VM a um dos segmentos de rede já criados no NSX-T.  

>[!NOTE]
>Se você configurar um servidor DHCP, obterá a configuração de rede da VM por meio dele (não se esqueça de configurar o escopo).  Se pretender fazer a configuração de modo estático, faça isso como faria normalmente.

## <a name="test-the-nsx-t-segment-connectivity"></a>Testar a conectividade do segmento do NSX-T

Faça logon na VM criada na etapa anterior e verifique a conectividade;

1. Execute ping em um IP na Internet.
2. Em um navegador da Web, acesse um site da Internet.
3. Execute ping no jumpbox que fica na Rede Virtual do Azure.

A Solução VMware no Azure agora está em funcionamento e você estabeleceu com êxito a conectividade entre a rede virtual do Azure e a Internet.

## <a name="next-steps"></a>Próximas etapas

Na próxima seção, você conectará a Solução VMware no Azure com sua rede local por meio do ExpressRoute.
> [!div class="nextstepaction"]
> [Conectar a Solução VMware no Azure ao ambiente local](azure-vmware-solution-on-premises.md)
