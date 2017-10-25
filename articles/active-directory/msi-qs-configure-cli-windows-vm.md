---
title: Como configurar o MSI em uma VM do Azure usando a CLI do Azure
description: "Instruções passo a passo para configurar um MSI (Identidade de Serviço Gerenciado) em uma VM do Azure usando a CLI do Azure."
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
ms.date: 09/14/2017
ms.author: bryanla
ms.openlocfilehash: fe276fe802eceb1f062ed8bda685dd44a1e3d175
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-vm-managed-service-identity-msi-using-azure-cli"></a>Configurar um MSI (Identidade do Serviço Gerenciado) da VM usando a CLI do Azure

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

A Identidade de Serviço Gerenciado fornece aos serviços do Azure uma identidade gerenciada automaticamente no Active Directory do Azure. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código. 

Neste artigo, você aprenderá a habilitar e a remover o MSI de uma VM do Windows Azure usando a CLI do Azure.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

Para executar os exemplos de script da CLI, você tem três opções:

- Usar o [Azure Cloud Shell](../cloud-shell/overview.md) no Portal do Azure (confira a próxima seção).
- Usar o Azure Cloud Shell inserido por meio do botão "Experimentar", localizado no canto superior direito de cada bloco de código.
- [Instale a versão mais recente da CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 ou posterior) se você preferir usar um console local da CLI. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Habilitar o MSI durante a criação de uma VM do Azure

Para criar uma VM habilitado MSI:

1. Se você estiver usando a CLI do Azure em um console local, primeiro entre no Azure usando o [logon az](/cli/azure/#login). Use uma conta que esteja associada à assinatura do Azure sob a qual você deseja implantar a VM:

   ```azurecli-interactive
   az login
   ```

2. Criar um [grupo de recursos](../azure-resource-manager/resource-group-overview.md#terminology) para contenção e implantação de VM e seus recursos relacionados usando [az group create](/cli/azure/group/#create). Ignore esta etapa, se você já tiver o grupo de recursos que deseja usar:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Crie uma VM usando [az vm create](/cli/azure/vm/#create). O exemplo a seguir cria uma VM denominada *myVM* com um MSI, como solicitado pelo parâmetro `--assign-identity`. Os parâmetros `--admin-username` e `--admin-password` especificam o nome de usuário e a senha do usuário administrativo para a entrada na máquina virtual. Atualize esses valores como adequado ao seu ambiente: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>Habilitar MSI em uma VM do Azure existente

Se você precisar habilitar o MSI em uma máquina virtual existente:

1. Se você estiver usando a CLI do Azure em um console local, primeiro entre no Azure usando o [logon az](/cli/azure/#login). Use uma conta que esteja associada com uma assinatura do Azure que contenha uma VM. Verifique também se sua conta pertence a uma função que fornece permissões de gravação na VM, como "Colaborador da Máquina Virtual":

   ```azurecli-interactive
   az login
   ```

2. Use [az vm assign-identity](/cli/azure/vm/#az_vm_assign_identity) com o parâmetro `--assign-identity` para adicionar uma MSI para uma VM existente:

   ```azurecli-interactive
   az vm assign-identity -g myResourceGroup -n myVm
   ```

## <a name="remove-msi-from-an-azure-vm"></a>Remover o MSI de uma VM do Azure

Se você tiver uma Máquina Virtual que não precisa mais de um MSI:

1. Se você estiver usando a CLI do Azure em um console local, primeiro entre no Azure usando o [logon az](/cli/azure/#login). Use uma conta que esteja associada com uma assinatura do Azure que contenha uma VM. Verifique também se sua conta pertence a uma função que fornece permissões de gravação na VM, como "Colaborador da Máquina Virtual":

   ```azurecli-interactive
   az login
   ```

2. Use o `-n ManagedIdentityExtensionForWindows` ou `-n ManagedIdentityExtensionForLinux` alterne (dependendo do tipo de VM) com [exclusão de extensão de vm az](https://docs.microsoft.com/cli/azure/vm/#assign-identity) para remover o MSI:

   ```azurecli-interactive
   az vm extension delete --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
   ```

## <a name="related-content"></a>Conteúdo relacionado

- [Visão geral da Identidade do Serviço Gerenciado](msi-overview.md)
- Para os guias de início rápido completos sobre VM do Azure, consulte: 

  - [Crie máquinas virtuais Windows com o CLI](../virtual-machines/windows/quick-create-cli.md)  
  - [Crie máquinas virtuais Linux com o CLI](../virtual-machines/linux/quick-create-cli.md) 

Use a seção de comentários a seguir para fornecer seus comentários e nos ajudar a aprimorar e adaptar nosso conteúdo.
















