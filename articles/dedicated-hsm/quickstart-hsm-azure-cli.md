---
title: 'Início rápido: Criar um HSM Dedicado do Azure com a CLI do Azure'
description: Crie, mostre, liste, atualize e exclua HSMs Dedicados do Azure usando a CLI do Azure.
services: dedicated-hsm
author: msmbaldwin
ms.author: mbaldwin
ms.topic: quickstart
ms.service: key-vault
ms.devlang: azurecli
ms.date: 01/06/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: e07bc758b1ef86b3d8c605cbce72f6db564a355f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020847"
---
# <a name="quickstart-create-an-azure-dedicated-hsm-by-using-the-azure-cli"></a>Início rápido: Criar um HSM Dedicado do Azure usando a CLI do Azure

Este artigo descreve como criar e gerenciar um HSM Dedicado do Azure usando a extensão [az dedicated-hsm](/cli/azure/ext/hardware-security-modules/dedicated-hsm) da CLI do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Você pode [criar uma conta gratuita](https://azure.microsoft.com/free/) se não tiver uma.
  
  Se você tiver mais de uma assinatura do Azure, defina a assinatura a ser usada para cobrança com o comando [az account set](/cli/azure/account#az_account_set) da CLI do Azure.
  
  ```azurecli-interactive
  az account set --subscription 00000000-0000-0000-0000-000000000000
  ```
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]  
  
- Todos os requisitos atendidos para um HSM dedicado, incluindo registro, aprovação e uma rede virtual e máquina virtual a serem usados para provisionamento. Para obter mais informações sobre requisitos e pré-requisitos de HSM dedicado, confira o [Tutorial: Implantar HSMs em uma rede virtual existente usando a CLI do Azure](tutorial-deploy-hsm-cli.md).
  

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um [grupo de recursos do Azure](../azure-resource-manager/management/overview.md) é um contêiner lógico para implantar e gerenciar recursos do Azure como um grupo. Se você ainda não tiver um grupo de recursos para o HSM dedicado, crie um usando o comando [az group create](/cli/azure/group#az_group_create). O seguinte exemplo cria um grupo de recursos chamado `myRG` na região do Azure `westus`:

```azurecli-interactive
az group create --name myRG --location westus
```

## <a name="create-a-dedicated-hsm"></a>Criar um HSM dedicado

Para criar um HSM dedicado, use o comando [az dedicated-hsm create](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_create). O exemplo a seguir provisiona um HSM dedicado chamado `hsm1` na região `westus`, grupo de recursos `myRG`, e assinatura, rede virtual e sub-rede especificadas. Os parâmetros necessários são `name`, `location` e `resource group`.

```azurecli-interactive
az dedicated-hsm create \
   --resource-group myRG \
   --name "hsm1" \
   --location "westus" \
   --network-profile-network-interfaces private-ip-address="1.0.0.1" \
   --subnet id="/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/hsm-group/providers/Microsoft.Network/virtualNetworks/MyHSM-vnet/subnets/MyHSM-vnet" \
   --stamp-id "stamp1" \
   --sku name="SafeNet Luna Network HSM A790" \
   --tags resourceType="hsm" Environment="test" \
   --zones "AZ1"
```

A implantação leva cerca de 25 a 30 minutos para ser concluída.

## <a name="get-a-dedicated-hsm"></a>Obter um HSM dedicado

Para obter um HSM dedicado atual, execute o comando [az dedicated-hsm show](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_show). O exemplo a seguir obtém o HSM dedicado `hsm1` no grupo de recursos `myRG`.

```azurecli-interactive
az dedicated-hsm show --resource-group myRG --name hsm1
```

## <a name="update-a-dedicated-hsm"></a>Atualizar um HSM dedicado

Use o comando [az dedicated-hsm update](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_update) para atualizar um HSM dedicado. O seguinte exemplo atualiza o HSM dedicado `hsm1` no grupo de recursos `myRG` e suas marcas:

```azurecli-interactive
az dedicated-hsm update --resource-group myRG –-name hsm1 --tags resourceType="hsm" Environment="prod" Slice="A"
```

## <a name="list-dedicated-hsms"></a>Lista de HSMs dedicados

Execute o comando [az dedicated-hsm list](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_list) para obter informações sobre os HSMs dedicados atuais. O seguinte exemplo lista os HSMs dedicados no grupo de recursos `myRG`:

```azurecli-interactive
az dedicated-hsm list --resource-group myRG
```

## <a name="remove-a-dedicated-hsm"></a>Remover um HSM dedicado

Para remover um HSM dedicado, use o comando [az dedicated-hsm delete](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_delete). O seguinte exemplo exclui o HSM dedicado `hsm1` do grupo de recursos `myRG`:

```azurecli-interactive
az dedicated-hsm delete --resource-group myRG –-name hsm1
```

## <a name="delete-the-resource-group"></a>Exclua o grupo de recursos

Se você não precisar mais do grupo de recursos criado para o HSM dedicado, poderá excluí-lo executando o comando [az group delete](/cli/azure/group#az_group_delete). Esse comando exclui o grupo e todos os recursos nele, incluindo os que não estão relacionados ao HSM dedicado. O seguinte exemplo exclui o grupo de recursos `myRG` e tudo nele:

```azurecli-interactive
az group delete --name myRG
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o HSM Dedicado do Azure, confira [HSM Dedicado do Azure](overview.md).
