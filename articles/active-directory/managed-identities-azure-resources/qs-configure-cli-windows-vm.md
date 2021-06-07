---
title: Configurar identidades gerenciadas na VM do Azure usando a CLI do Azure – Azure AD
description: Instruções passo a passo para configurar identidades gerenciadas atribuídas ao usuário e ao sistema em uma VM do Azure usando a CLI do Azure.
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
ms.openlocfilehash: a8dbe9665612e888b7e7afe95a472ba6b0de8d48
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762507"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-azure-cli"></a>Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando a CLI do Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

As identidades gerenciadas dos recursos do Azure fornecem aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código. 

Neste artigo, usando a CLI do Azure, você aprenderá como executar as seguintes identidades gerenciadas para operações de recursos do Azure em uma VM do Azure:

- Habilitar e desabilitar a identidade gerenciada atribuída pelo sistema em uma VM do Azure
- Adicionar e remover uma identidade gerenciada atribuída pelo usuário em uma VM do Azure

Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com as identidades gerenciadas dos recursos do Azure, confira [O que são as identidades gerenciadas dos recursos do Azure?](overview.md). Para saber mais sobre tipos de identidade gerenciada atribuída pelo sistema e pelo usuário, confira [Tipos de identidade gerenciada](overview.md#managed-identity-types).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="system-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo sistema

Nesta seção, você aprenderá como habilitar e desabilitar a identidade gerenciada atribuída ao sistema em uma VM do Azure usando a CLI do Azure.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Ativar identidade gerenciada atribuída pelo sistema durante a criação de uma VM do Azure

Para criar uma VM do Azure com a identidade gerenciada atribuída ao sistema habilitada, a conta precisará da atribuição de função [Colaborador da Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).  Nenhuma atribuição adicional de função de diretório do Azure Active Directory é necessária.

1. Criar um [grupo de recursos](../../azure-resource-manager/management/overview.md#terminology) para contenção e implantação de VM e seus recursos relacionados usando [az group create](/cli/azure/group/#az_group_create). Ignore esta etapa, se você já tiver o grupo de recursos que deseja usar:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

1. Crie uma VM usando [az vm create](/cli/azure/vm/#az_vm_create). O exemplo a seguir cria uma VM nomeada *myVM* com uma identidade gerenciada atribuída ao sistema, conforme solicitado pelo parâmetro `--assign-identity`. Os parâmetros `--admin-username` e `--admin-password` especificam o nome de usuário e a senha do usuário administrativo para a entrada na máquina virtual. Atualize esses valores como adequado ao seu ambiente: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Habilitar identidade gerenciada atribuída ao sistema em uma VM do Azure existente

Para habilitar a identidade gerenciada atribuída pelo sistema em uma VM, sua conta precisa da atribuição de função [Colaboração da Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).  Nenhuma atribuição adicional de função de diretório do Azure Active Directory é necessária.

1. Se você estiver usando a CLI do Azure em um console local, primeiro entre no Azure usando o [logon az](/cli/azure/reference-index#az_login). Use uma conta que esteja associada com uma assinatura do Azure que contenha uma VM.

   ```azurecli-interactive
   az login
   ```

2. Use [az vm identity assign](/cli/azure/vm/identity/) com `identity assign` para habilitar a identidade atribuída ao sistema para uma VM existente:

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-system-assigned-identity-from-an-azure-vm"></a>Desabilitar uma identidade atribuída ao sistema de uma VM do Azure

Para desabilitar a identidade gerenciada atribuída ao sistema em uma VM, a conta precisará da atribuição de função [Colaborador da Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).  Nenhuma atribuição adicional de função de diretório do Azure Active Directory é necessária.

Se você tiver uma Máquina Virtual que não precisa mais da identidade atribuída ao sistema, mas ainda precisar de identidades atribuídas ao usuário, use o comando a seguir:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Se você tiver uma máquina virtual que não precisa mais da identidade atribuída ao sistema e não tiver identidades atribuídas ao usuário, use o comando a seguir:

> [!NOTE]
> O valor `none` diferencia maiúsculas de minúsculas. Deve estar em letras minúsculas. 

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none"
```


## <a name="user-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo usuário

Nesta seção, você aprenderá como adicionar e remover uma identidade gerenciada atribuída ao usuário de uma VM do Azure usando a CLI do Azure. Se você criar sua identidade gerenciada atribuída ao usuário em um RG diferente da VM. Você precisará usar a URL da sua identidade gerenciada para atribuí-la à VM.
ou seja, --identities "/subscriptions/<SUBID>/resourcegroups/<RESROURCEGROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NOME_DA_ID_ATRIBUÍDA_AO_USUÁRIO>"

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Atribuir uma identidade gerenciada atribuída pelo usuário durante a criação de uma VM do Azure

Para atribuir uma identidade atribuída pelo usuário a uma VM durante sua criação, sua conta precisa das atribuições de função [Contribuinte virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) e [Operador de identidade gerenciada](../../role-based-access-control/built-in-roles.md#managed-identity-operator). Nenhuma atribuição adicional de função de diretório do Azure Active Directory é necessária.

1. Se você já tiver um grupo de recursos e quiser usá-lo, ignore esta etapa. Crie um [grupo de recursos ](~/articles/azure-resource-manager/management/overview.md#terminology) para independência e implantação da identidade gerenciada atribuída ao usuário, usando [ az group create ](/cli/azure/group/#az_group_create). Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<LOCATION>` pelos seus próprios valores. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Crie uma identidade gerenciada atribuída ao usuário usando [az identity create](/cli/azure/identity#az_identity_create).  O parâmetro `-g` especifica o grupo de recursos em que a identidade gerenciada atribuída pelo usuário é criada e o parâmetro `-n` especifica o nome.    
    
   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g myResourceGroup -n myUserAssignedIdentity
   ```
   A resposta contém detalhes para a identidade gerenciada atribuída ao usuário criada, semelhante à seguinte. O valor da ID do recurso atribuído à identidade gerenciada atribuída ao usuário será usado na etapa a seguir.

   ```json
   {
       "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
       "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<myUserAssignedIdentity>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
       "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>",
       "location": "westcentralus",
       "name": "<USER ASSIGNED IDENTITY NAME>",
       "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
       "resourceGroup": "<RESOURCE GROUP>",
       "tags": {},
       "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
       "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. Crie uma VM usando [az vm create](/cli/azure/vm/#az_vm_create). O exemplo a seguir cria uma VM associada à nova identidade atribuída ao usuário, conforme especificado pelo parâmetro `--assign-identity`. Substitua os valores dos parâmetros `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` e `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores. 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY NAME>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Atribuir uma identidade gerenciada usuário atribuído a uma VM existente do Azure

Para atribuir uma identidade atribuída pelo usuário a uma VM, sua conta precisa das atribuições de função [Contribuidor de Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) e [Operador de Identidade Gerenciada](../../role-based-access-control/built-in-roles.md#managed-identity-operator). Nenhuma atribuição adicional de função de diretório do Azure Active Directory é necessária.

1. Crie uma identidade atribuída pelo usuário usando [az identity create](/cli/azure/identity#az_identity_create).  O parâmetro `-g` especifica o grupo de recursos em que a identidade atribuída ao usuário é criada e o parâmetro `-n` especifica o nome. Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores:

    > [!IMPORTANT]
    > Atualmente, não há suporte para criação de identidades gerenciadas atribuídas ao usuário com caracteres especiais (ou seja, sublinhado) no nome. Use caracteres alfanuméricos. Procure novamente por atualizações.  Para obter mais informações, consulte [Perguntas frequentes e problemas conhecidos](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   A resposta contém detalhes para a identidade gerenciada atribuída ao usuário criada, semelhante à seguinte. 

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. Atribua a identidade atribuída ao usuário à VM usando [az vm identity assign](/cli/azure/vm). Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<VM NAME>` pelos seus próprios valores. O `<USER ASSIGNED IDENTITY NAME>` é a propriedade `name` do recurso de identidade gerenciada atribuída ao usuário, conforme criado na etapa anterior. Se você criar sua identidade gerenciada atribuída ao usuário em um RG diferente da VM. Você precisará usar a URL da sua identidade gerenciada.

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Remover uma identidade gerenciada atribuída pelo usuário de uma VM do Azure

Para remover uma identidade atribuída ao usuário a uma VM, a conta precisará da atribuição de função [Colaborador da Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor). 

Se essa for a única identidade gerenciada atribuída ao usuário atribuída à máquina virtual, `UserAssigned` será removido do valor do tipo de identidade.  Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<VM NAME>` pelos seus próprios valores. O `<USER ASSIGNED IDENTITY>` será a propriedade `name` da identidade atribuída ao usuário, que pode ser localizada na seção de identidade da máquina virtual usando `az vm identity show`:

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
```

Se a VM não tiver uma identidade gerenciada atribuída ao sistema e você quiser remover todas as identidades atribuídas ao usuário, use o comando a seguir:

> [!NOTE]
> O valor `none` diferencia maiúsculas de minúsculas. Deve estar em letras minúsculas.

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Se a VM tiver identidades atribuídas ao sistema e atribuídas ao usuário, você poderá remover todas as identidades atribuídas ao usuário, alternando para usar somente atribuídas ao sistema. Use o seguinte comando:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Próximas etapas
- [Identidades gerenciadas para visão geral de recursos do Azure](overview.md)
- Para os guias de início rápido completos sobre VM do Azure, consulte: 
  - [Crie máquinas virtuais Windows com o CLI](../../virtual-machines/windows/quick-create-cli.md)  
  - [Crie máquinas virtuais Linux com o CLI](../../virtual-machines/linux/quick-create-cli.md)
