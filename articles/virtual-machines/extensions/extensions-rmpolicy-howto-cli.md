---
title: Usar a política do Azure para restringir a instalação da extensão VM | Microsoft Docs
description: Use a política do Azure para restringir as implantações de extensão VM.
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: roiyz
ms.reviewer: cynthn
ms.openlocfilehash: 79a65dc0e7aa85b2f8cef5f884fab7bc6cd076fe
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876717"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-linux-vms"></a>Usar a política do Azure para restringir a instalação de extensões nas VMs do Linux

Se você quiser impedir o uso ou a instalação de certas extensões de suas VMs do Linux, você pode criar uma política do Azure usando o CLI para restringir as extensões de máquinas virtuais dentro de um grupo de recursos. 

Este tutorial usa o CLI dentro da Cloud Shell do Azure, que é constantemente atualizada para a versão mais recente. Se você deseja executar a CLI do Azure localmente, você precisa instalar a versão 2.0.26 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

## <a name="create-a-rules-file"></a>Criar um arquivo de regras

Para restringir quais extensões podem ser instaladas, você precisa ter uma [regra](../../governance/policy/concepts/definition-structure.md#policy-rule) para fornecer a lógica para identificar a extensão.

Este exemplo mostra como negar as extensões de instalação publicadas por 'Microsoft.OSTCExtensions' criando um arquivo de regras na Cloud Shell do Azure, mas se você estiver trabalhando localmente na CLI, você também pode criar um arquivo local e substituir o caminho (~/clouddrive) pelo caminho para o arquivo local no seu computador.

Em uma [Cloud Shel de bash](https://shell.azure.com/bash), digite:

```azurecli-interactive 
vim ~/clouddrive/azurepolicy.rules.json
```

Copie e cole o seguinte .json no arquivo.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.OSTCExtensions/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.OSTCExtensions/virtualMachines/extensions/publisher",
                "equals": "Microsoft.OSTCExtensions"
            },
            {
                "field": "Microsoft.OSTCExtensions/virtualMachines/extensions/type",
                "in": "[parameters('notAllowedExtensions')]"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

Quando terminar, aperte **Esc** e, em seguida, digite **: wq** para salvar e fechar o arquivo.


## <a name="create-a-parameters-file"></a>Criar um arquivo de parâmetros

Você também precisa de um arquivo de [parâmetros](../../governance/policy/concepts/definition-structure.md#parameters) que cria uma estrutura para usar para passar uma lista de extensões para bloquear. 

Este exemplo mostra como criar um arquivo de parâmetros para VMs do Linux na Cloud Shell, mas se você estiver trabalhando localmente na CLI, você também pode criar um arquivo local e substituir o caminho (~/clouddrive) pelo caminho para o arquivo local no seu computador.

Na [Cloud Shel de bash](https://shell.azure.com/bash), digite:

```azurecli-interactive
vim ~/clouddrive/azurepolicy.parameters.json
```

Copie e cole o seguinte .json no arquivo.

```json
{
    "notAllowedExtensions": {
        "type": "Array",
        "metadata": {
            "description": "The list of extensions that will be denied. Example: CustomScriptForLinux, VMAccessForLinux etc.",
            "strongType": "type",
            "displayName": "Denied extension"
        }
    }
}
```

Quando terminar, aperte **Esc** e, em seguida, digite **: wq** para salvar e fechar o arquivo.

## <a name="create-the-policy"></a>Criar a política

Uma definição de política é um objeto usado para armazenar a configuração que você deseja usar. A definição de política usa os arquivos de regras e parâmetros para definir a política. Criar a definição de política usando [criar definição de política az](/cli/azure/role/assignment?view=azure-cli-latest).

Neste exemplo, os parâmetros e as regras de política são os arquivos criados e armazenados como arquivos. .json na sua cloud shell.

```azurecli-interactive
az policy definition create \
   --name 'not-allowed-vmextension-linux' \
   --display-name 'Block VM Extensions' \
   --description 'This policy governs which VM extensions that are blocked.' \
   --rules '~/clouddrive/azurepolicy.rules.json' \
   --params '~/clouddrive/azurepolicy.parameters.json' \
   --mode All
```


## <a name="assign-the-policy"></a>Atribuir a política

Este exemplo atribui a política a um grupo de recursos usando[criar atribuição da política az](/cli/azure/policy/assignment). Qualquer VM criada no grupo de recursos **myResourceGroup** não será capaz de instalar o Acesso VM do LInux ou as extensões de Scrip personalizadas para o Linux. O grupo de recursos deve existir antes que você possa atribuir a política.

Use [lista de contas az](/cli/azure/account?view=azure-cli-latest) para obter sua ID de assinatura para usar daquele no exemplo.


```azurecli-interactive
az policy assignment create \
   --name 'not-allowed-vmextension-linux' \
   --scope /subscriptions/<subscription Id>/resourceGroups/myResourceGroup \
   --policy "not-allowed-vmextension-linux" \
   --params '{
        "notAllowedExtensions": {
            "value": [
                "VMAccessForLinux",
                "CustomScriptForLinux"
            ]
        }
    }'
```

## <a name="test-the-policy"></a>Testar a política

Teste a política criando uma nova VM e tente adicionar um novo usuário.


```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --generate-ssh-keys
```

Tente criar um novo usuário chamado **myNewUser** usando a extensão de acesso da VM.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --password 'mynewuserpwd123!'
```



## <a name="remove-the-assignment"></a>Remover a atribuição

```azurecli-interactive
az policy assignment delete --name 'not-allowed-vmextension-linux' --resource-group myResourceGroup
```
## <a name="remove-the-policy"></a>Remover a política

```azurecli-interactive
az policy definition delete --name 'not-allowed-vmextension-linux'
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais, veja [Azure Policy](../../governance/policy/overview.md).