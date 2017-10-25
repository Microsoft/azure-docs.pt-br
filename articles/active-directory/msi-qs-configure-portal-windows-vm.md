---
title: Como configurar o MSI em uma VM do Azure usando o portal do Azure
description: "Instruções passo a passo para configurar uma Identidade de Serviço Gerenciado (MSI) em uma VM do Azure usando o portal do Azure."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: bryanla
ms.openlocfilehash: 169417530da21e0c8c58cbf770fd1d26660387f7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-vm-managed-service-identity-msi-using-the-azure-portal"></a>Configurar uma Identidade do Serviço Gerenciado (MSI) da VM usando a CLI do Azure

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

A Identidade de Serviço Gerenciado fornece aos serviços do Azure uma identidade gerenciada automaticamente no Active Directory do Azure. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código. 

Neste artigo, você aprenderá a habilitar e a remover o MSI de uma VM do Windows Azure usando o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Habilitar o MSI durante a criação de uma VM do Azure

Na data em que este artigo foi inscrito, habilitar MSI durante a criação de uma VM no portal do Azure não é suportado. Em vez disso, consulte um dos seguintes artigos de início rápido de criação de VM para primeiro criar uma VM:

- [Criar uma máquina virtual do Windows com o Portal do Azure](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Criar uma máquina virtual Linux com o Portal do Azure](../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

Vá para a próxima seção para obter detalhes sobre como habilitar o MSI numa VM.

## <a name="enable-msi-on-an-existing-azure-vm"></a>Habilitar MSI em uma VM do Azure existente

Se você tiver uma VM que foi originalmente provisionada sem um MSI:

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure que contenha a VM. Verifique também se sua conta pertence a uma função que fornece permissões de gravação na VM, como "Colaborador da Máquina Virtual".

2. Navegue até a Máquina Virtual desejada.

2. Clique na página de "Configuração", habilite o MSI na VM selecionando "Sim" em "Identidade de serviço gerenciado" e clique em **Salvar**. Esta operação pode demorar 60 segundos ou mais para ser concluída:

   ![Captura de tela da página de configuração](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

## <a name="remove-msi-from-an-azure-vm"></a>Remover o MSI de uma VM do Azure

Se você tiver uma Máquina Virtual que não precisa mais de um MSI:

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure que contenha a VM. Verifique também se sua conta pertence a uma função que fornece permissões de gravação na VM, como "Colaborador da Máquina Virtual".

2. Navegue até a Máquina Virtual desejada.

3. Clique na página de "Configuração", remova o MSI da VM selecionando "Não" em "Identidade de serviço gerenciado" e clique em **Salvar**. Esta operação pode demorar 60 segundos ou mais para ser concluída:

   ![Captura de tela da página de configuração](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)  

## <a name="related-content"></a>Conteúdo relacionado

- Para obter uma visão geral do MSI, confira [Visão geral da Identidade de Serviço Gerenciado](msi-overview.md).

## <a name="next-steps"></a>Próximas etapas

- Usando o portal do Azure, conceda [acesso a outro recurso do Azure](msi-howto-assign-access-portal.md) ao MSI da VM do Azure.

Use a seção de comentários a seguir para fornecer seus comentários e nos ajudar a aprimorar e adaptar nosso conteúdo.
