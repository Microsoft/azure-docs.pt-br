---
title: Configurar identidades gerenciadas usando o portal do Azure – Azure AD
description: Instruções passo a passo para configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando o portal do Azure.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78c973537e958b7199c137c81e72091f70b84ddb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97590980"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-vm-using-the-azure-portal"></a>Configurar identidades gerenciadas para recursos do Azure em uma VM usando o portal do Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

As identidades gerenciadas dos recursos do Azure fornecem aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código. 

Neste artigo, você aprende como habilitar e desabilitar identidades gerenciadas atribuídas ao usuário e ao sistema para uma VM (Máquina Virtual) do Azure, usando o portal do Azure. 

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, confira a [seção de visão geral](overview.md).
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="system-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo sistema

Nesta seção, você aprende como habilitar e desabilitar a identidade gerenciada atribuída ao sistema para VM usando o portal do Azure.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-vm"></a>Habilitar identidade gerenciada atribuída ao sistema durante a criação de uma VM

Para habilitar a identidade gerenciada atribuída pelo sistema em uma VM durante sua criação, sua conta precisa da atribuição de função [Colaboração da Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).  Nenhuma atribuição adicional de função de diretório do Azure Active Directory é necessária.

- Na guia **Gerenciamento** da seção **Identidade**, mude **Identidade do serviço gerenciado** para **Em**.  

![Habilitar a identidade atribuída pelo sistema durante a criação de VM](./media/msi-qs-configure-portal-windows-vm/enable-system-assigned-identity-vm-creation.png)

Consulte os seguintes Inícios Rápidos para criar uma VM: 

- [Criar uma máquina virtual do Windows com o Portal do Azure](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine) 
- [Criar uma máquina virtual Linux com o Portal do Azure](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)


### <a name="enable-system-assigned-managed-identity-on-an-existing-vm"></a>Habilitar a identidade gerenciada atribuída ao sistema em uma VM existente

Para habilitar a identidade gerenciada atribuída ao sistema em uma VM que foi originalmente provisionada sem ela, a conta precisará da atribuição de função [Colaborador da Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).  Nenhuma atribuição adicional de função de diretório do Azure Active Directory é necessária.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure que contenha a VM.

2. Navegue até a Máquina Virtual desejada e selecione **Identidade**.

3. Em **Sistema atribuído**, **Status**, selecione **Ativado** e, em seguida, clique em **Salvar**:

   ![Captura de tela que mostra a página "Identidade (versão prévia)" com o status "Atribuído pelo sistema" definido como "Ativado".](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

### <a name="remove-system-assigned-managed-identity-from-a-vm"></a>Remover identidade gerenciada atribuída ao sistema de uma VM

Para remover a identidade gerenciada atribuída pelo sistema de uma VM, sua conta precisa da atribuição de função [Atribuída do Virtual Machine](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).  Nenhuma atribuição adicional de função de diretório do Azure Active Directory é necessária.

Se você tiver uma Máquina Virtual que não precisa mais de identidade gerenciada atribuída ao sistema:

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure que contenha a VM. 

2. Navegue até a Máquina Virtual desejada e selecione **Identidade**.

3. Em **Sistema atribuído**, **Status**, selecione **Desativado** e, em seguida, clique em **Salvar**:

   ![Captura de tela da página de configuração](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)

## <a name="user-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo usuário

 Nesta seção, você aprende como adicionar e remover uma identidade gerenciada atribuída ao usuário de uma VM usando o portal do Azure.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-vm"></a>Atribuir uma identidade atribuída ao usuário durante a criação de uma VM

Para atribuir uma identidade atribuída pelo usuário a uma VM, sua conta precisa das atribuições de função [Contribuidor de Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) e [Operador de Identidade Gerenciada](../../role-based-access-control/built-in-roles.md#managed-identity-operator). Nenhuma atribuição adicional de função de diretório do Azure Active Directory é necessária.

Atualmente, o portal do Azure não dá suporte à atribuição de uma identidade gerenciada atribuída ao usuário durante a criação de uma VM. Em vez disso, consulte um dos seguintes artigos de Início Rápido de criação de VM para primeiro criar uma VM e, em seguida, vá para a próxima seção para obter detalhes sobre como atribuir uma identidade gerenciada atribuída ao usuário à VM:

- [Criar uma máquina virtual do Windows com o Portal do Azure](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Criar uma máquina virtual Linux com o Portal do Azure](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-vm"></a>Atribuir uma identidade gerenciada atribuída ao usuário a uma VM existente

Para atribuir uma identidade atribuída pelo usuário a uma VM, sua conta precisa das atribuições de função [Contribuidor de Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) e [Operador de Identidade Gerenciada](../../role-based-access-control/built-in-roles.md#managed-identity-operator). Nenhuma atribuição adicional de função de diretório do Azure Active Directory é necessária.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure que contenha a VM.
2. Navegue até a VM desejada e clique em **Identidade**, **Usuário atribuído** e, em seguida, **\+Adicionar**.

   ![Captura de tela que mostra a página "Identidade" com "Atribuído pelo usuário" selecionado e o botão "Adicionar" realçado.](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot1.png)

3. Clique na identidade atribuída ao usuário que você quer adicionar à VM e, em seguida, clique em **Adicionar**.

    ![Adicionar identidade gerenciada atribuída ao usuário à VM](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-vm"></a>Remover uma identidade gerenciada atribuída ao usuário de uma VM

Para remover uma identidade atribuída pelo usuário de uma VM, sua conta precisa da atribuição de função [Atribuído do Virtual Machine](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor). Nenhuma atribuição adicional de função de diretório do Azure Active Directory é necessária.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure que contenha a VM.
2. Navegue até a VM desejada e clique em **Identidade**, **Usuário atribuído**, clique no nome da identidade gerenciada atribuída ao usuário que deseja excluir e, em seguida, clique em **Remover** (clique em **Sim** no painel de confirmação).

   ![Remover identidade gerenciada atribuída ao usuário de uma VM](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vm-screenshot.png)

## <a name="next-steps"></a>Próximas etapas

- Usando o portal do Azure, conceda [acesso de identidade gerenciada de uma VM do Azure a outro recurso do Azure](howto-assign-access-portal.md).