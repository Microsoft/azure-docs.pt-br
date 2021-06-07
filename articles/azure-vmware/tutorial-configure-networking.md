---
title: Tutorial – Configurar a rede para sua nuvem privada do VMware no Azure
description: Saiba como a criar e configurar a rede necessária para implantar sua nuvem privada no Azure
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: c609d191191cd9de111e83fe62761b149405cccc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103494337"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>Tutorial: Configurar a rede para a sua nuvem privada da VMware no Azure

Uma nuvem privada da Solução VMware no Azure requer uma Rede Virtual do Azure. Como a Solução VMware no Azure não é compatível com o vCenter local, são necessárias etapas adicionais para a integração ao ambiente local. Também é necessário configurar um circuito do ExpressRoute e um gateway de rede virtual.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma rede virtual
> * Criar um gateway de rede virtual
> * Conectar o circuito do ExpressRoute ao gateway
> * Localizar as URLs do vCenter e do NSX Manager

## <a name="prerequisites"></a>Pré-requisitos 
Uma rede virtual em que você criou uma [nuvem privada da Solução VMware no Azure](tutorial-create-private-cloud.md). 

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. Entre no [portal do Azure](https://portal.azure.com).

1. Navegue até o grupo de recursos criado no [tutorial de criação de uma nuvem privada](tutorial-create-private-cloud.md) e selecione **+ Adicionar** para definir um novo recurso. 

1. Na caixa de texto **Pesquisar no Marketplace**, digite **Rede Virtual**. Localize o recurso Rede Virtual e selecione-o.

1. Na página **Rede Virtual**, selecione **Criar** para configurar sua rede virtual para sua nuvem privada.

1. Na página **Criar Rede Virtual**, insira os detalhes da sua rede virtual.

1. Na guia **Básico**, insira um nome para a rede virtual, escolha a região apropriada e selecione **Avançar: Endereços IP**.

1. Na guia **Endereços IP**, em **Espaço de endereço IPv4**, insira o espaço de endereço criado no tutorial anterior.

   > [!IMPORTANT]
   > Use um espaço de endereço que **não** se sobreponha ao espaço de endereço usado quando você criou a nuvem privada no tutorial anterior.

1. Selecione **+ Adicionar sub-rede** e, na página **Adicionar sub-rede**, dê à sub-rede um nome e um intervalo de endereços apropriado. Quando concluir, selecione **Adicionar**.

1. Selecione **Examinar + criar**.

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network.png" alt-text="Selecione Examinar + criar." border="true":::

1. Verifique as informações e selecione **Criar**. Quando a implantação for concluída, você verá a rede virtual no grupo de recursos.

## <a name="create-a-virtual-network-gateway"></a>Criar um gateway de rede virtual

Agora que você criou uma rede virtual, você criará um gateway de rede virtual.

1. Em seu grupo de recursos, selecione **+ Adicionar** para adicionar um novo recurso.

1. Na caixa de texto **Pesquisar no Marketplace**, digite **Gateway de rede virtual**. Localize o recurso Rede Virtual e selecione-o.

1. Na página **Gateway de rede virtual**, selecione **Criar**.

1. Na guia Básico da página **Criar gateway de rede virtual**, forneça valores para os campos e selecione **Examinar + criar**. 

   | Campo | Valor |
   | --- | --- |
   | **Assinatura** | Valor já preenchido com a Assinatura à qual o grupo de recursos pertence. |
   | **Grupo de recursos** | Valor já preenchido para o grupo de recursos atual. O valor deve ser o grupo de recursos criado em um teste anterior. |
   | **Nome** | Insira um nome exclusivo para o gateway de rede virtual. |
   | **Região** | Selecione a localização geográfica do gateway de rede virtual. |
   | **Tipo de gateway** | Selecione **ExpressRoute**. |
   | **SKU** | Deixe o valor padrão: **padrão**. |
   | **Rede virtual** | Selecione a rede virtual criada anteriormente. Se você não vir a rede virtual, verifique se a região do gateway corresponde à região da rede virtual. |
   | **Intervalo de endereços da sub-rede do gateway** | Esse valor é preenchido quando você seleciona a rede virtual. Não altere o valor padrão. |
   | **Endereço IP público** | Selecione **Criar novo**. |

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network-gateway.png" alt-text="Forneça valores para os campos e selecione Examinar + criar." border="true":::

1. Verifique se os detalhes estão corretos e selecione **Criar** para iniciar a implantação do gateway de rede virtual. 
1. Quando a implantação for concluída, passe para a próxima seção para conectar sua conexão do ExpressRoute ao gateway de rede virtual que contém a nuvem privada da Solução VMware no Azure.

## <a name="connect-expressroute-to-the-virtual-network-gateway"></a>Conectar o ExpressRoute ao gateway de rede virtual

Agora que você implantou um gateway de rede virtual, você adicionará uma conexão entre ele e sua nuvem privada da Solução VMware no Azure.

[!INCLUDE [connect-expressroute-to-vnet](includes/connect-expressroute-vnet.md)]


## <a name="locate-the-urls-for-vcenter-and-nsx-manager"></a>Localizar as URLs do vCenter e do NSX Manager

Para entrar no vCenter e no NSX Manager, você precisará das URLs do cliente Web do vCenter e do site do NSX-T Manager. 

Navegue até sua nuvem privada da Solução VMware no Azure, em **Gerenciar**, selecione **Identidade**, aqui você encontrará as informações necessárias.

:::image type="content" source="./media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="Captura de tela das credenciais do vCenter e do NSX-T e URLs de cliente Web." border="true" lightbox="media/tutorial-access-private-cloud/ss4-display-identity.png":::

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar uma rede virtual
> * Criar um gateway de rede virtual
> * Conectar o circuito do ExpressRoute ao gateway
> * Localizar as URLs do vCenter e do NSX Manager

Continue no próximo tutorial para saber como criar os segmentos de rede do NSX-T que são usados para VMs no vCenter.

> [!div class="nextstepaction"]
> [Criar um segmento de rede do NSX-T](tutorial-nsx-t-network-segment.md)