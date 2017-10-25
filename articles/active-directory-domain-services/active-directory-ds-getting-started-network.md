---
title: "Azure Active Directory Domain Services: introdução | Microsoft Docs"
description: Habilite o Azure Active Directory Domain Services usando o portal do Azure
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: maheshu
ms.openlocfilehash: 26fa86d166c54f48621ccbab13d1d17f465b7568
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Habilite o Azure Active Directory Domain Services usando o portal do Azure


## <a name="before-you-begin"></a>Antes de começar
Consulte as [Considerações de rede para o Azure Active Directory Domain Services](active-directory-ds-networking.md).


## <a name="task-2-configure-network-settings"></a>Tarefa 2: definir as configurações de rede
A próxima tarefa de configuração é criar uma rede virtual do Azure e uma sub-rede dedicada dentro dela. Você pode habilitar o Azure Active Directory Domain Services nessa sub-rede dentro de sua rede virtual. Você também pode escolher uma rede virtual existente e criar a sub-rede dedicada dentro dela.

1. Clique em **Rede virtual** para selecionar uma rede virtual.
2. Na folha **Escolher rede virtual**, você vê todas as redes virtuais desejadas. Você vê somente as redes virtuais que pertencem ao grupo de recursos e ao local do Azure que selecionou na página do assistente **Básico**.
3. Selecione a rede virtual na qual o Azure AD Domain Services deve ser habilitado. Você pode escolher uma rede virtual existente ou criar uma nova.

  > [!TIP]
  > **Você não pode mover seu domínio gerenciado para uma rede virtual diferente depois que tiver habilitado o Azure AD Domain Services.** Selecione a rede virtual certa para habilitar seu domínio gerenciado. Depois de criar um domínio gerenciado, você não pode movê-lo para uma rede virtual diferente sem excluir o domínio gerenciado. Recomendamos examinar as [considerações de rede para o Azure Active Directory Domain Services](active-directory-ds-networking.md) antes de continuar.  
  >

4. **Criar rede virtual:** clique em **Criar nova** para criar uma nova rede virtual. É altamente recomendável usar uma sub-rede dedicada para o Azure AD Domain Services. Por exemplo, crie uma sub-rede com o nome "DomainServices", tornando mais fácil para outros administradores entender o que está implantado dentro da sub-rede. Quando terminar, clique em **OK**.

    ![Escolher rede virtual](./media/getting-started/domain-services-blade-network-pick-vnet.png)

5. **Rede virtual existente:** se você planeja escolher uma rede virtual existente, [crie uma sub-rede dedicada usando a extensão de redes virtuais](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) e, em seguida, selecione essa sub-rede. Clique em **Rede Virtual** para selecionar uma rede virtual existente. Clique em **Sub-rede** para escolher a sub-rede dedicada nessa rede virtual existente, na qual você deseja habilitar o novo domínio gerenciado. Quando terminar, clique em **OK**.

    ![Escolher sub-rede na rede virtual](./media/getting-started/domain-services-blade-network-pick-subnet.png)

  > [!NOTE]
  > **Diretrizes para selecionar uma sub-rede**
  > 1. Use uma sub-rede dedicada para o Azure AD Domain Services. Não implante nenhuma outra máquina virtual nessa sub-rede. Essa configuração permite que você configure NSGs (grupos de segurança de rede) para suas máquinas virtuais/cargas de trabalho sem interromper seu domínio gerenciado. Para obter detalhes, consulte as [Considerações de rede do Azure Active Directory Domain Services](active-directory-ds-networking.md).
  2. Não selecione a sub-rede do Gateway para implantar o Azure AD Domain Services, pois essa não é uma configuração com suporte.
  3. Verifique se a sub-rede que você selecionou tem espaço de endereço disponível suficiente – pelo menos 3 a 5 endereços IP.
  >

6. Quando terminar, clique em **OK** para ir para a página **Grupo de administradores** do assistente.


## <a name="next-step"></a>Próxima etapa
[Tarefa 3: configurar o grupo administrativo e habilitar o Azure AD Domain Services](active-directory-ds-getting-started-admingroup.md)
