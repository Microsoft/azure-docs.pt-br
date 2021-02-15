---
title: Atribuir um acesso de identidade gerenciada a um recurso usando o CLI do Azure-Azure AD
description: Instruções passo a passo para atribuir uma identidade gerenciada em um recurso, acesso a outro recurso, usando CLI do Azure.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/29/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: e3b06ce76ae77aa62b20b707a736e8e20e5f6c45
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2021
ms.locfileid: "99090037"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-azure-cli"></a>Atribuir um acesso de identidade gerenciada a um recurso usando a CLI do Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Após configurar um recurso do Azure com uma identidade gerenciada, será possível conceder o acesso de identidade gerenciada a outro recurso, assim como qualquer entidade de segurança. Este exemplo mostra como conceder acesso de identidade gerenciada de uma máquina virtual do Azure ou Conjunto de dimensionamento de máquinas virtuais a uma conta de armazenamento do Azure usando a CLI do Azure.

Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com as identidades gerenciadas dos recursos do Azure, confira [O que são as identidades gerenciadas dos recursos do Azure?](overview.md). Para saber mais sobre tipos de identidade gerenciada atribuída pelo sistema e pelo usuário, confira [Tipos de identidade gerenciada](overview.md#managed-identity-types).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Usar o RBAC do Azure para atribuir um acesso de identidade gerenciada a outro recurso

Após habilitar a identidade gerenciada em um recurso do Azure, como uma [máquina virtual do Azure](qs-configure-cli-windows-vm.md) ou [Conjunto de dimensionamento de máquinas virtuais](qs-configure-cli-windows-vmss.md): 

1. Neste exemplo, fornecemos acesso de uma máquina virtual do Azure para uma conta de armazenamento. Primeiro, usamos [az resource list](/cli/azure/resource/#az-resource-list) para obter a entidade de serviço da máquina virtual nomeada myVM:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   Para um conjunto de dimensionamento de máquinas virtuais do Azure, o comando é o mesmo, exceto aqui, você obtém a entidade de serviço para o conjunto de dimensionamento de máquinas virtuais nomeado "DevTestVMSS":
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

1. Após obter a ID da entidade e serviço, use [criar atribuição de função az](/cli/azure/role/assignment#az-role-assignment-create) para que a máquina virtual ou o conjunto de dimensionamento de máquinas virtuais configurem o acesso do "Leitor" a uma conta de armazenamento nomeada "myStorageAcct":

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="next-steps"></a>Próximas etapas

- [Identidades gerenciadas para visão geral de recursos do Azure](overview.md)
- Para habilitar a identidade gerenciada em uma máquina virtual do Azure, consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando a CLI do Azure](qs-configure-cli-windows-vm.md).
- Para habilitar a identidade gerenciada em um conjunto de dimensionamento de máquinas virtuais do Azure, consulte [Configurar identidades gerenciadas para recursos do Azure em um conjunto de dimensionamento de máquinas virtuais usando a CLI do Azure](qs-configure-cli-windows-vmss.md).
