---
title: Tutorial – acessar a nuvem privada
description: Saiba como acessar uma nuvem privada da Solução VMware no Azure
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: b7f21dbba5256712607241bb1258962fc22418fb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91578404"
---
# <a name="tutorial-access-an-azure-vmware-solution-private-cloud"></a>Tutorial: Acessar uma nuvem privada da Solução VMware no Azure

A Solução VMware no Azure não permite que você gerencie a nuvem privada com o vCenter local. Você precisará fazer uma configuração e uma conexão adicional com uma instância do vCenter local por meio de uma caixa de salto. 

Neste tutorial, você criará uma caixa de salto no grupo de recursos criado no [tutorial anterior](tutorial-configure-networking.md) e entrará no vCenter. A caixa de salto é uma VM (máquina virtual) do Windows na mesma rede virtual que você criou.  Ela fornece acesso ao vCenter e ao NSX Manager. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma máquina virtual do Windows para usar para se conectar ao vCenter
> * Fazer logon no vCenter por meio da máquina virtual

## <a name="create-a-new-windows-virtual-machine"></a>Criar uma máquina virtual do Windows

[!INCLUDE [create-avs-jump-box-steps](includes/create-jump-box-steps.md)]

## <a name="connect-to-the-local-vcenter-of-your-private-cloud"></a>Conectar-se ao vCenter local da nuvem privada

1. Na caixa de salto, entre no cliente do vSphere com o SSO do vCenter da VMware usando um nome de usuário administrador de nuvem e confirme se a interface do usuário é exibida com êxito.

1. No portal do Azure, selecione a nuvem privada e, na exibição **Visão Geral**, selecione **Identidade > Padrão**. 

   As URLs e as credenciais do usuário da nuvem privada do vCenter e do NSX-T Manager são exibidas.

   :::image type="content" source="media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="Exibir URLs e credenciais da nuvem privada do vCenter e do NSX Manager." border="true":::

1. Navegue até a VM criada na etapa anterior e conecte-se a ela. 

   Se precisar de ajuda para se conectar à VM, confira [conectar-se a uma máquina virtual](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine) para obter detalhes.

1. Na VM do Windows, abra um navegador e navegue até as URLs do vCenter e do NSX-T Manager em duas guias. 

1. Na guia do vCenter, insira as credenciais de usuário `cloudadmin@vmcp.local` da etapa anterior.

   :::image type="content" source="media/tutorial-access-private-cloud/ss5-vcenter-login.png" alt-text="Exibir URLs e credenciais da nuvem privada do vCenter e do NSX Manager." border="true":::

   :::image type="content" source="media/tutorial-access-private-cloud/ss6-vsphere-client-home.png" alt-text="Exibir URLs e credenciais da nuvem privada do vCenter e do NSX Manager." border="true":::

1. Na segunda guia do navegador, entre no NSX-T Manager.

   :::image type="content" source="media/tutorial-access-private-cloud/ss10-nsx-manager-home.png" alt-text="Exibir URLs e credenciais da nuvem privada do vCenter e do NSX Manager." border="true":::



## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar uma máquina virtual do Windows para usar para se conectar ao vCenter
> * Fazer logon no vCenter por meio da máquina virtual

Continue no próximo tutorial para saber como criar uma rede virtual para configurar o gerenciamento local para seus clusters de nuvem privada.

> [!div class="nextstepaction"]
> [Criar uma rede virtual](tutorial-configure-networking.md)


