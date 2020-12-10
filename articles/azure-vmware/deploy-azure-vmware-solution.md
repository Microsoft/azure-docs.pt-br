---
title: Implantar e configurar a Solução VMware no Azure
description: Saiba como usar as informações coletadas na fase de planejamento para implantar a nuvem privada da Solução VMware no Azure.
ms.topic: tutorial
ms.date: 11/09/2020
ms.openlocfilehash: 7e31b9236a3c75009d15bde35019036b6db55cab
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861504"
---
# <a name="deploy-and-configure-azure-vmware-solution"></a>Implantar e configurar a Solução VMware no Azure

Neste artigo, você usará as informações da [seção de planejamento](production-ready-deployment-steps.md) para implantar a Solução VMware no Azure. Se você não tiver definido as informações, volte para a [seção de planejamento](production-ready-deployment-steps.md) antes de continuar.

## <a name="register-the-resource-provider"></a>Registre o provedor de recursos

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]


## <a name="deploy-azure-vmware-solution"></a>Implantar a Solução VMware no Azure

Use as informações coletadas no artigo [Planejando a implantação da Solução VMware no Azure](production-ready-deployment-steps.md):

>[!NOTE]
>Para implantar a Solução VMware no Azure, você precisa ter, no mínimo, o nível de colaborador na assinatura.

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-private-cloud-azure-portal-steps.md)]

>[!NOTE]
>Para obter uma visão geral de ponta a ponta desta etapa, assista ao vídeo [Solução VMware no Azure: implantação](https://www.youtube.com/embed/gng7JjxgayI).

## <a name="create-the-jump-box"></a>Criar o jumpbox

>[!IMPORTANT]
>Se você tiver deixado a opção **Rede Virtual** em branco durante a etapa de provisionamento inicial na tela **Criar uma Nuvem Privada**, conclua o tutorial [Configurar a rede da nuvem privada do VMware](tutorial-configure-networking.md) **antes** de continuar com esta seção.  

Depois de implantar a Solução VMware no Azure, você criará o jumpbox da rede virtual que se conecta com o vCenter e com o NSX. Após a configuração dos circuitos do ExpressRoute e do Alcance Global do ExpressRoute, o jumpbox não será necessário.  Mas é útil acessar o vCenter e o NSX na Solução VMware no Azure.  

:::image type="content" source="media/pre-deployment/jump-box-diagram.png" alt-text="Criar o jumpbox da Solução VMware no Azure" border="false" lightbox="media/pre-deployment/jump-box-diagram.png":::

Para criar uma VM (máquina virtual) na rede virtual que você [identificou ou criou como parte do processo de implantação](production-ready-deployment-steps.md#azure-virtual-network-to-attach-azure-vmware-solution), siga estas instruções: 

[!INCLUDE [create-avs-jump-box-steps](includes/create-jump-box-steps.md)]

## <a name="connect-to-a-virtual-network-with-expressroute"></a>Conectar-se a uma rede virtual com o ExpressRoute

Se você não tiver definido uma rede virtual na etapa de implantação e sua intenção for conectar o ExpressRoute da Solução VMware no Azure com um Gateway do ExpressRoute existente, siga as etapas abaixo.

Se você já tiver definido uma rede virtual na tela de implantação no Azure, pule para a próxima seção.

[!INCLUDE [connect-expressroute-to-vnet](includes/connect-expressroute-vnet.md)]

## <a name="verify-network-routes-advertised"></a>Verificar as rotas de rede anunciadas

O jumpbox está na rede virtual na qual a Solução VMware no Azure se conecta por meio do circuito do ExpressRoute dela.  No Azure, acesse o adaptador de rede do jumpbox e [veja as rotas em vigor](../virtual-network/manage-route-table.md#view-effective-routes).

Na lista de rotas em vigor, serão exibidas as redes criadas como parte da implantação da Solução VMware no Azure. Você verá várias redes que foram derivadas da rede [`/22` que você definiu](production-ready-deployment-steps.md#ip-address-segment) durante a [etapa de implantação](#deploy-azure-vmware-solution) anteriormente neste artigo.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-effective-routes.png" alt-text="Verificar as rotas de rede anunciadas da Solução VMware no Azure para a Rede Virtual do Azure" lightbox="media/pre-deployment/azure-vmware-solution-effective-routes.png":::

Neste exemplo, a rede 10.74.72.0/22 foi inserida durante a implantação, derivando as redes /24.  Se aparecer algo semelhante, você poderá se conectar com o vCenter na Solução VMware no Azure.

## <a name="connect-and-sign-in-to-vcenter-and-nsx-t"></a>Conectar-se e entrar no vCenter e no NSX-T

Faça logon no jumpbox que você criou na etapa anterior. Depois de fazer logon, abra um navegador da Web, navegue para o console de administração do vCenter e do NSX-T e entre nele.  

Você pode identificar os endereços IP e as credenciais do console de administração do vCenter-T e do NSX-T no portal do Azure.  Selecione sua nuvem privada e, na exibição **Visão geral**, selecione **Identidade > Padrão**. 

## <a name="create-a-network-segment-on-azure-vmware-solution"></a>Criar um segmento de rede na Solução VMware no Azure

O NSX-T é usado para criar segmentos de rede no ambiente da Solução VMware no Azure.  Na [seção de planejamento](production-ready-deployment-steps.md), você definiu as redes que deseja criar.  Se você não as tiver definido, volte para a [seção de planejamento](production-ready-deployment-steps.md) antes de continuar.

>[!IMPORTANT]
>Verifique se o bloco de endereço de rede CIDR que você definiu não se sobrepõe a nada nos ambientes do Azure ou locais.  

Siga o tutorial [Criar um segmento de rede do NSX-T na Solução VMware no Azure](tutorial-nsx-t-network-segment.md) para criar um segmento de rede do NSX-T na Solução VMware no Azure.

## <a name="verify-advertised-nsx-t-segment"></a>Verificar o segmento do NSX-T anunciado

Volte para a etapa [Verificar as rotas de rede anunciadas](#verify-network-routes-advertised). Você verá rotas adicionais na lista, representando os segmentos de rede que você criou na etapa anterior.  

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
2. Acesse um site da Internet usando um navegador da Web.
3. Execute ping no jumpbox que fica na Rede Virtual do Azure.

>[!IMPORTANT]
>Neste ponto, a Solução VMware no Azure está em funcionamento e você estabeleceu com êxito a conectividade entre a rede virtual do Azure e a Internet.

## <a name="next-steps"></a>Próximas etapas

Na próxima seção, você conectará a Solução VMware no Azure com sua rede local por meio do ExpressRoute.
> [!div class="nextstepaction"]
> [Conectar a Solução VMware no Azure ao ambiente local](azure-vmware-solution-on-premises.md)
