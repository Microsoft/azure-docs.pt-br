---
title: Como atribuir um acesso MSI a um recurso do Azure usando a CLI do Azure
description: "Instruções passo a passo sobre como atribuir um MSI em um recurso, acesso a outro recurso, usando a CLI do Azure."
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
ms.date: 09/25/2017
ms.author: bryanla
ms.openlocfilehash: e77915c1d982ccf6262ffcbc09dc91dfd986dac5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-azure-cli"></a>Atribuir um acesso de MSI (Identidade de Serviço Gerenciado) a um recurso usando a CLI do Azure

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Depois de configurar um recurso do Azure com um MSI, você pode conceder o acesso de MSI a outro recurso, assim como qualquer entidade de segurança. Este exemplo mostra como conceder o acesso de MSI da máquina de virtual do Azure a uma conta de Armazenamento do Azure usando a CLI do Azure.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

Para executar os exemplos de script da CLI, você tem três opções:

- Usar o [Azure Cloud Shell](../cloud-shell/overview.md) no Portal do Azure (confira a próxima seção).
- Usar o Azure Cloud Shell inserido por meio do botão "Experimentar", localizado no canto superior direito de cada bloco de código.
- [Instale a versão mais recente da CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 ou posterior) se você preferir usar um console local da CLI. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Usar o RBAC para atribuir o acesso de MSI a outro recurso

Depois de habilitar o MSI em um recurso do Azure, [como uma VM do Azure](msi-qs-configure-cli-windows-vm.md): 

1. Se você estiver usando a CLI do Azure em um console local, primeiro entre no Azure usando o [logon az](/cli/azure/#login). Use uma conta que esteja associada à assinatura do Azure sob a qual você deseja implantar a VM:

   ```azurecli-interactive
   az login
   ```

2. Neste exemplo, fornecemos um acesso da VM do Azure para uma conta de armazenamento. Primeiro, usamos [az resource list](/cli/azure/resource/#list) para obter a entidade de serviço da VM chamada "myVM", que foi criada ao habilitarmos o MSI na VM:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```

3. Assim que tivermos a ID da entidade de serviço, usamos [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) para dar à VM acesso de "Leitor" para uma conta de armazenamento chamada "myStorageAcct":

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="troubleshooting"></a>Solucionar problemas

Se o MSI para o recurso não aparecer na lista de identidades disponíveis, verifique se ele foi habilitado corretamente. Em nosso caso, podemos voltar à VM do Azure no [Portal do Azure](https://portal.azure.com) e:

- Examine a página "Configuração" e certifique-se de que o MSI esteja habilitado = "Sim".
- Examine a página "Extensões" e verifique se a extensão do MSI foi implantada com êxito.

Se um deles estiver incorreto, reimplante o MSI em seu recurso ou solucione a falha de implantação.

## <a name="related-content"></a>Conteúdo relacionado

- Para obter uma visão geral do MSI, confira [Visão geral da Identidade de Serviço Gerenciado](msi-overview.md).
- Para habilitar o MSI em uma VM do Azure, confira [Configurar um MSI (identidade do serviço gerenciada) usando a CLI do Azure](msi-qs-configure-cli-windows-vm.md).

Use a seção de comentários a seguir para fornecer seus comentários e nos ajudar a aprimorar e adaptar nosso conteúdo.

