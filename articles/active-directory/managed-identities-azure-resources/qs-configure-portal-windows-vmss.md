---
title: Configurar identidades gerenciadas no conjunto de dimensionamento de máquinas virtuais – Azure AD
description: Instruções passo a passo para configurar identidades gerenciadas para recursos do Azure em um conjunto de dimensionamento de máquinas virtuais usando o portal do Azure.
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
ms.openlocfilehash: 73c2f4167d6ce5465a879f6b2ef7e96d91065c4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97590912"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-vmss-using-the-azure-portal"></a>Configurar identidades gerenciadas para recursos do Azure em um VMSS (conjunto de dimensionamento de máquinas virtuais) usando o portal do Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

As identidades gerenciadas dos recursos do Azure fornecem aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código. 

Neste artigo, usando o PowerShell, você aprende como executar as seguintes identidades gerenciadas para operações de recursos do Azure em um conjunto de dimensionamento de máquinas virtuais:

- Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, confira a [seção de visão geral](overview.md).
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para realizar as operações de gerenciamento deste artigo, a conta precisará das seguintes atribuições de função do Azure:

    > [!NOTE]
    > Não são necessárias atribuições de função do diretório adicionais do Azure AD.

    - [Colaborador de Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) para habilitar e remover identidade gerenciada atribuída ao sistema de um conjunto de dimensionamento de máquinas virtuais.

## <a name="system-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo sistema

Nesta seção, você aprenderá como habilitar e desabilitar a identidade gerenciada atribuída ao sistema usando o portal do Azure.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Habilitar identidade gerenciada atribuída ao sistema durante a criação de um conjunto de dimensionamento de máquinas virtuais

Atualmente, o portal do Azure não dá suporte à habilitação da identidade gerenciada atribuída ao sistema durante a criação de um conjunto de dimensionamento de máquinas virtuais. Em vez disso, consulte o seguinte artigo de Início Rápido de criação do conjunto de dimensionamento de máquinas virtuais para criar primeiro um conjunto de dimensionamento de máquinas virtuais e, em seguida, vá para a próxima seção para obter detalhes sobre como habilitar a identidade gerenciada atribuída ao sistema em um conjunto de dimensionamento de máquinas virtuais:

- [Criar um conjunto de dimensionamento de máquinas virtuais no Portal do Azure](../../virtual-machine-scale-sets/quick-create-portal.md)  

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>Habilitar identidade gerenciada atribuída ao sistema em um conjunto de dimensionamento de máquinas virtuais existente

Para habilitar a identidade gerenciada atribuída ao sistema em um conjunto de dimensionamento de máquinas virtuais que foi originalmente provisionado sem ela:

1. Entre no [Portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure que contém o conjunto de dimensionamento de máquinas virtuais.

2. Navegue até o conjunto de dimensionamento de máquinas virtuais desejado.

3. Em **Sistema atribuído**, **Status**, selecione **Ativado** e, em seguida, clique em **Salvar**:

   ![A captura de tela mostra a página "Identidade (versão prévia)" com "Atribuído pelo sistema" selecionado, o status "Ativado" e o botão "Salvar" realçado.](./media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png) 

### <a name="remove-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Remover identidade gerenciada atribuída ao sistema de um conjunto de dimensionamento de máquinas virtuais

Se você tiver um conjunto de dimensionamento de máquinas virtuais que não precise mais de uma identidade gerenciada atribuída ao sistema:

1. Entre no [Portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure que contém o conjunto de dimensionamento de máquinas virtuais. Certifique-se também de que sua conta pertence a uma função que concede permissões de gravação no conjunto de dimensionamento de máquinas virtuais.

2. Navegue até o conjunto de dimensionamento de máquinas virtuais desejado.

3. Em **Sistema atribuído**, **Status**, selecione **Desativado** e, em seguida, clique em **Salvar**:

   ![Captura de tela da página de configuração](./media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)

## <a name="user-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo usuário

Nesta seção, você aprenderá como adicionar e remover uma identidade gerenciada atribuída ao usuário de um conjunto de dimensionamento de máquinas virtuais usando o portal do Azure.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Atribuir uma identidade gerenciada atribuída ao usuário durante a criação de um conjunto de dimensionamento de máquinas virtuais

Atualmente, o portal do Azure não dá suporte à atribuição de uma identidade gerenciada atribuída ao usuário durante a criação de um conjunto de dimensionamento de máquinas virtuais. Em vez disso, consulte o seguinte artigo de Início Rápido de criação do conjunto de dimensionamento de máquinas virtuais para criar primeiro um conjunto de dimensionamento de máquinas virtuais e, em seguida, vá para a próxima seção para obter detalhes sobre como atribuir uma identidade gerenciada atribuída ao usuário a ele:

- [Criar um conjunto de dimensionamento de máquinas virtuais no Portal do Azure](../../virtual-machine-scale-sets/quick-create-portal.md)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Atribuir uma identidade gerenciada atribuída ao usuário a um conjunto de dimensionamento de máquinas virtuais existente

1. Entre no [Portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure que contém o conjunto de dimensionamento de máquinas virtuais.
2. Navegue até o conjunto de dimensionamento de máquinas virtuais desejado e clique em **Identidade**, **Usuário atribuído** e, em seguida, **\+Adicionar**.

   ![Captura de tela que mostra a página "Identidade" com "Atribuído pelo usuário" selecionado e o botão "Adicionar" realçado.](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vmss-screenshot1.png)

3. Clique na identidade atribuída ao usuário que você quer adicionar ao conjunto de dimensionamento de máquinas virtuais e, em seguida, clique em **Adicionar**.
   
   ![Adicionar identidade atribuída ao usuário ao VMSS](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Remover uma identidade gerenciada atribuída ao usuário de um conjunto de dimensionamento de máquinas virtuais

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure que contenha a VM.
2. Navegue até o conjunto de dimensionamento de máquinas virtuais desejado e clique em **Identidade**, **Usuário atribuído**, no nome da identidade gerenciada atribuída ao usuário que você quer excluir e clique em **Remover** (clique em **Sim** no painel de confirmação).

   ![Remover identidade atribuída ao usuário de um VMSS](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vmss-screenshot.png)


## <a name="next-steps"></a>Próximas etapas

- Usando o portal do Azure, conceda um acesso de identidade gerenciada do conjunto de dimensionamento de máquinas virtuais do Azure [a outro recurso do Azure](howto-assign-access-portal.md).
