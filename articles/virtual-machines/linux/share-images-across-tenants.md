---
title: Compartilhar imagens da Galeria entre locatários no Azure | Microsoft Docs
description: Aprenda a compartilhar imagens de VM entre locatários do Azure usando Shared galerias de imagens.
services: virtual-machines-linux
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: cbf42de406ecb0caed67b77743f376284ab64608
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706547"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Compartilhar imagens da Galeria de VM entre locatários do Azure

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> Você não pode usar o portal para implantar uma VM de uma imagem em outro locatário do azure. Para criar uma VM de uma imagem compartilhada entre locatários, você deve usar a CLI do Azure ou [Powershell](../windows/share-images-across-tenants.md).

## <a name="create-a-vm-using-azure-cli"></a>Criar uma VM usando a CLI do Azure

Entrar a entidade de serviço para o locatário 1 usando a appID, a chave do aplicativo e a ID do locatário 1. Você pode usar `az account show --query "tenantId"` para obter IDs de locatário se necessário.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Conecte-se a entidade de serviço para o locatário 2 usando o appID, a chave do aplicativo e a ID do locatário 2:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Crie a VM. Substitua as informações no exemplo pelos seus próprios.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Próximas etapas

Se você tiver algum problema, você poderá [solucionar problemas de galerias de imagens compartilhadas](troubleshooting-shared-images.md).