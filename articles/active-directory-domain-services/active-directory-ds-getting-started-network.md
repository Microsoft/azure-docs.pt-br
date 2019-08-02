---
title: 'Azure Active Directory Domain Services: Introdução | Microsoft Docs'
description: Habilite o Azure Active Directory Domain Services usando o portal do Azure
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: dcad3a0c5642f195dcda67c7a08dfba66abddb3f
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67474214"
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Habilite o Azure Active Directory Domain Services usando o portal do Azure


## <a name="before-you-begin"></a>Antes de começar
Consulte as [Considerações de rede para o Azure Active Directory Domain Services](network-considerations.md).


## <a name="task-2-configure-network-settings"></a>Tarefa 2: definir as configurações de rede
A próxima tarefa de configuração é criar uma rede virtual do Azure e uma sub-rede dedicada dentro dela. Você pode habilitar o Azure Active Directory Domain Services nessa sub-rede dentro de sua rede virtual. Você também pode escolher uma rede virtual existente e criar a sub-rede dedicada dentro dela.

1. Clique em **Rede virtual** para selecionar uma rede virtual.
    > [!NOTE]
    > **Redes virtuais clássicas não têm mais suporte para novas implantações.** Redes virtuais clássicas não têm mais suporte para novas implantações. Domínios gerenciados existentes implantados em redes virtuais clássicas continuam a ter suporte. Em breve, a Microsoft habilitará você a migrar um domínio gerenciado existente de uma rede virtual clássica para uma rede virtual do Resource Manager.
    >

2. Na página **Escolher rede virtual**, você vê todas as redes virtuais desejadas. Você vê somente as redes virtuais que pertencem ao grupo de recursos e ao local do Azure que selecionou na página do assistente **Básico**.
3. Selecione a rede virtual na qual o Azure AD Domain Services deve ser habilitado. Você pode escolher uma rede virtual existente ou criar uma nova.

   > [!TIP]
   > **Você não pode mover seu domínio gerenciado para uma rede virtual diferente depois que tiver habilitado o Azure AD Domain Services.** Selecione a rede virtual certa para habilitar seu domínio gerenciado. Depois de criar um domínio gerenciado, você não pode movê-lo para uma rede virtual diferente sem excluir o domínio gerenciado. Recomendamos examinar as [considerações de rede para o Azure Active Directory Domain Services](network-considerations.md) antes de continuar.  
   >

4. **Criar rede virtual:** Clique em **Criar novo** para criar uma nova rede virtual. Use uma sub-rede dedicada para o Azure AD Domain Services. Por exemplo, crie uma sub-rede com o nome "DomainServices", tornando mais fácil para outros administradores entender o que está implantado dentro da sub-rede. Quando terminar, clique em **OK**.

    ![Escolher rede virtual](./media/getting-started/domain-services-blade-network-pick-vnet.png)

   > [!WARNING]
   > Certifique-se de escolher um espaço de endereço que esteja no espaço de endereços IP privado. Os endereços IP que você não possui que estejam no espaço de endereços públicos causam erros nos Azure Active Directory Domain Services.

5. **Rede virtual existente:** Se você planeja escolher uma rede virtual existente, [crie uma sub-rede dedicada usando a extensão de redes virtuais](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) e, em seguida, selecione essa sub-rede. Clique em **Rede Virtual** para selecionar uma rede virtual existente. Clique em **Sub-rede** para escolher a sub-rede dedicada nessa rede virtual existente, na qual você deseja habilitar o novo domínio gerenciado. Quando terminar, clique em **OK**.

    ![Escolher sub-rede na rede virtual](./media/getting-started/domain-services-blade-network-pick-subnet.png)

   > [!NOTE]
   > **Diretrizes para selecionar uma sub-rede**
   > 1. Use uma sub-rede dedicada para o Azure AD Domain Services. Não implante nenhuma outra máquina virtual nessa sub-rede. Essa configuração permite que você configure NSGs (grupos de segurança de rede) para suas máquinas virtuais/cargas de trabalho sem interromper seu domínio gerenciado. Para obter detalhes, consulte as [Considerações de rede do Azure Active Directory Domain Services](network-considerations.md).
   > 2. Não selecione a sub-rede do Gateway para implantar o Azure AD Domain Services, pois essa não é uma configuração com suporte.
   > 3. A sub-rede selecionada precisa ter pelo menos de 3 a 5 endereços IP disponíveis no espaço de endereço.

6. Quando terminar, clique em **OK** para ir para a página **Grupo de administradores** do assistente.


## <a name="next-step"></a>Próxima etapa
[Tarefa 3: configurar o grupo administrativo e habilitar o Azure AD Domain Services](active-directory-ds-getting-started-admingroup.md)
