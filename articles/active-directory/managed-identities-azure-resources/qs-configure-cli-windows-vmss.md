---
title: Configurar identidades gerenciadas no conjunto de dimensionamento de máquinas virtuais – CLI do Azure – Azure AD
description: Instruções passo a passo para configurar identidades gerenciadas atribuídas ao sistema e ao usuário em um conjunto de dimensionamento de máquinas virtuais do Azure usando a CLI do Azure.
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
ms.openlocfilehash: 0e6e7e1724247c0e6d2b9db2fdf6980e8ef553c7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788179"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-azure-cli"></a>Configurar identidades gerenciadas para recursos do Azure em um conjunto de dimensionamento de máquinas virtuais usando a CLI do Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

As identidades gerenciadas dos recursos do Azure fornecem aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código. 

Neste artigo, você aprenderá a executar as seguintes operações para identidades gerenciadas para recursos do Azure em um conjunto de dimensionamento de máquinas virtuais do Azure usando a CLI do Azure:
- Habilitar e desabilitar a identidade gerenciada atribuída ao sistema em um conjunto de dimensionamento de máquinas virtuais do Azure
- Adicionar e remover uma identidade gerenciada atribuída ao usuário em um conjunto de dimensionamento de máquinas virtuais do Azure

Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com as identidades gerenciadas dos recursos do Azure, confira [O que são as identidades gerenciadas dos recursos do Azure?](overview.md). Para saber mais sobre tipos de identidade gerenciada atribuída pelo sistema e pelo usuário, confira [Tipos de identidade gerenciada](overview.md#managed-identity-types).

- Para realizar as operações de gerenciamento descritas neste artigo, sua conta precisará das seguintes atribuições de controle de acesso baseado em função do Azure:

  - [Colaborador da Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) para criar um conjunto de dimensionamento de máquinas virtuais e habilitar e remover identidade gerenciada atribuída ao usuário e/ou ao sistema de um conjunto de dimensionamento de máquinas virtuais.

  - [Função de Contratada de Identidade Gerenciada](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) para criar uma identidade gerenciada atribuída pelo usuário.

  - [Papel de Operador de Identidade Gerenciado](../../role-based-access-control/built-in-roles.md#managed-identity-operator) para atribuir e remover uma identidade gerenciada atribuída pelo usuário de e para um conjunto de dimensionamento de máquina virtual.

  > [!NOTE]
  > Não são necessárias atribuições de função do diretório adicionais do Azure AD.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="system-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo sistema

Nesta seção, você aprenderá a habilitar e desabilitar a identidade gerenciada atribuída ao sistema em um conjunto de dimensionamento de máquinas virtuais do Azure usando a CLI do Azure.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Habilitar a identidade gerenciada atribuída ao sistema durante a criação de um conjunto de dimensionamento de máquinas virtuais do Azure

Para criar um conjunto de dimensionamento de máquinas virtuais com a identidade gerenciada atribuída ao sistema habilitada:

1. Crie um [grupo de recursos](../../azure-resource-manager/management/overview.md#terminology) para confinamento e implantação do conjunto de dimensionamento de máquinas virtuais e os recursos relacionados, usando [az group create](/cli/azure/group/#az_group_create). Se você já tiver um grupo de recursos e quiser usá-lo, ignore esta etapa:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

1. [Crie](/cli/azure/vmss/#az_vmss_create) um conjunto de dimensionamento de máquinas virtuais. O exemplo a seguir cria um conjunto de dimensionamento de máquinas virtuais nomeado *myVMSS* com uma identidade gerenciada atribuída ao sistema, conforme solicitado pelo parâmetro `--assign-identity`. Os parâmetros `--admin-username` e `--admin-password` especificam o nome de usuário e a senha do usuário administrativo para a entrada na máquina virtual. Atualize esses valores como adequado ao seu ambiente: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Ativar identidade gerenciada atribuída pelo sistema em um conjunto de dimensionamento de máquina virtual do Azure existente

Se você precisar [Habilitar](/cli/azure/vmss/identity/#az_vmss_identity_assign) a identidade gerenciada atribuída pelo sistema em um conjunto de dimensionamento de máquinas virtuais do Azure existente:

```azurecli-interactive
az vmss identity assign -g myResourceGroup -n myVMSS
```

### <a name="disable-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Desabilitar identidade gerenciada atribuída ao sistema de um conjunto de dimensionamento de máquinas virtuais do Azure

Se você tiver um conjunto de dimensionamento de máquinas virtuais que não precise mais da identidade gerenciada atribuída ao sistema, mas ainda precisar de identidades gerenciadas atribuídas ao usuário, use o comando a seguir:

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Se você tiver uma máquina virtual que não precise mais da identidade gerenciada atribuída ao sistema e não tiver identidades gerenciadas atribuídas ao usuário, use o comando a seguir:

> [!NOTE]
> O valor `none` diferencia maiúsculas de minúsculas. Deve estar em letras minúsculas. 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type="none"
```

## <a name="user-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo usuário

Nesta seção, você aprende como habilitar e remover uma identidade gerenciada atribuída ao usuário usando a CLI do Azure.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Atribuir uma identidade gerenciada atribuída ao usuário durante a criação de um conjunto de dimensionamento de máquinas virtuais

Esta seção descreve a criação de um conjunto de dimensionamento de máquinas virtuais e a atribuição de uma identidade gerenciada atribuída ao usuário ao conjunto de dimensionamento de máquinas virtuais. Se você já tiver um conjunto de dimensionamento de máquinas virtuais que deseje usar, ignore esta seção e prossiga para a próxima.

1. Se você já tiver um grupo de recursos e quiser usá-lo, ignore esta etapa. Crie um [grupo de recursos ](~/articles/azure-resource-manager/management/overview.md#terminology) para independência e implantação da identidade gerenciada atribuída ao usuário, usando [ az group create ](/cli/azure/group/#az_group_create). Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<LOCATION>` pelos seus próprios valores. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Crie uma identidade gerenciada atribuída ao usuário usando [az identity create](/cli/azure/identity#az_identity_create).  O parâmetro `-g` especifica o grupo de recursos em que a identidade gerenciada atribuída pelo usuário é criada e o parâmetro `-n` especifica o nome. Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores:

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
   ```
   A resposta contém detalhes para a identidade gerenciada atribuída ao usuário criada, semelhante à seguinte. O valor do recurso `id` atribuído à identidade gerenciada atribuída ao usuário é usado na etapa a seguir.

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

3. [Crie](/cli/azure/vmss/#az_vmss_create) um conjunto de dimensionamento de máquinas virtuais. O exemplo a seguir cria um conjunto de dimensionamento de máquinas virtuais associado à nova identidade gerenciada atribuída ao usuário, conforme especificado pelo parâmetro `--assign-identity`. Substitua os valores dos parâmetros `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>` e `<USER ASSIGNED IDENTITY>` pelos seus próprios valores. 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Atribuir uma identidade gerenciada atribuída ao usuário a um conjunto de dimensionamento de máquinas virtuais existente

1. Crie uma identidade gerenciada atribuída ao usuário usando [az identity create](/cli/azure/identity#az_identity_create).  O parâmetro `-g` especifica o grupo de recursos em que a identidade gerenciada atribuída pelo usuário é criada e o parâmetro `-n` especifica o nome. Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   A resposta contém detalhes para a identidade gerenciada atribuída ao usuário criada, semelhante à seguinte.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY >/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. [Atribua](/cli/azure/vmss/identity) uma identidade gerenciada atribuída ao usuário ao seu conjunto de dimensionamento de máquinas virtuais. Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<VIRTUAL MACHINE SCALE SET NAME>` pelos seus próprios valores. O `<USER ASSIGNED IDENTITY>` é um recurso da identidade de usuário atribuída `name` propriedade, conforme criado na etapa anterior:

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Remover uma identidade gerenciada atribuída pelo usuário de um conjunto de dimensionamento da máquina virtual do Azure

Para [remover](/cli/azure/vmss/identity#az_vmss_identity_remove) uma identidade gerenciada atribuída ao usuário de um conjunto de dimensionamento de máquinas virtuais, use `az vmss identity remove`. Se esta for a única identidade gerenciada atribuída ao usuário atribuída ao conjunto de dimensionamento de máquinas virtuais, `UserAssigned` será removido do valor do tipo de identidade.  Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<VIRTUAL MACHINE SCALE SET NAME>` pelos seus próprios valores. O `<USER ASSIGNED IDENTITY>` será a propriedade `name` da identidade gerenciada atribuída ao usuário, que pode ser localizada na seção de identidade do conjunto de dimensionamento de máquinas virtuais usando `az vmss identity show`:

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
```

Se o seu conjunto de dimensionamento de máquina virtual não tiver uma identidade gerenciada atribuída pelo sistema e você quiser remover todas as identidades gerenciadas atribuídas pelo usuário, use o seguinte comando:

> [!NOTE]
> O valor `none` diferencia maiúsculas de minúsculas. Deve estar em letras minúsculas.

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Se o conjunto de dimensionamento de máquinas virtuais tiver identidades gerenciadas atribuídas ao sistema e atribuídas ao usuário, você poderá remover todas as identidades atribuídas ao usuário, alternando para usar somente a identidade gerenciada atribuída ao sistema. Use o seguinte comando:

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Próximas etapas

- [Identidades gerenciadas para visão geral de recursos do Azure](overview.md)
- Para o Início Rápido da criação do conjunto de dimensionamento de máquinas virtuais do Azure completo, confira: [Criar um conjunto de dimensionamento de máquinas virtuais com a CLI](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)
