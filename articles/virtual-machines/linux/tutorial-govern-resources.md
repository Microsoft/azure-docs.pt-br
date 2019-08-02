---
title: Tutorial - Governar máquinas virtuais do Azure com a CLI do Azure | Microsoft Docs
description: Neste tutorial, você aprenderá como usar a CLI do Azure para gerenciar máquinas virtuais do Azure aplicando RBAC, políticas, bloqueios e tags
services: virtual-machines-linux
documentationcenter: virtual-machines
author: tfitzmac
manager: gwallace
editor: tysonn
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: tutorial
ms.date: 10/12/2018
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: 760055a831998aa026439302094e146fd4d39394
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708431"
---
# <a name="tutorial-learn-about-linux-virtual-machine-governance-with-azure-cli"></a>Tutorial: Aprender sobre a governança de máquina virtual do Linux com a CLI do Azure

[!INCLUDE [Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI do Azure localmente, este tutorial exigirá a execução da CLI do Azure versão 2.0.30 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="understand-scope"></a>Compreender o escopo

[!INCLUDE [Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

Neste tutorial, você aplica todas as configurações de gerenciamento a um grupo de recursos, de modo que seja possível remover facilmente essas configurações quando terminar.

Vamos criar esse grupo de recursos.

```azurecli-interactive
az group create --name myResourceGroup --location "East US"
```

Atualmente, o grupo de recursos está vazio.

## <a name="role-based-access-control"></a>Controle de acesso baseado em função

Você deseja certificar-se de que os usuários em sua organização têm o nível certo de acesso a esses recursos. Você não deseja conceder acesso ilimitado a usuários, mas também precisa certificar-se de que eles podem fazer o trabalho deles. O [controle de acesso baseado em função](../../role-based-access-control/overview.md) permite que você gerencie quais usuários têm permissão para executar ações específicas em um escopo.

Para criar e remover as atribuições de função, os usuários devem ter `Microsoft.Authorization/roleAssignments/*` acesso. Esse acesso deve ser concedido pelas funções Proprietário ou Administrador de Acesso do Usuário.

Para gerenciar soluções de máquinas virtuais, há três funções específicas do recurso que fornecem acesso comum:

* [Colaborador de Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Colaborador de rede](../../role-based-access-control/built-in-roles.md#network-contributor)
* [Colaborador da Conta de Armazenamento](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Em vez de atribuir funções a usuários individuais, muitas vezes é mais fácil usar um grupo do Azure Active Directory que tenha usuários que precisam realizar ações semelhantes. E, em seguida, atribuir esse grupo à função apropriada. Neste artigo, use um grupo existente para gerenciar a máquina virtual ou use o portal para [criar um grupo do Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Após criar um novo grupo ou encontrar um existente, use o comando [az role assignment create](/cli/azure/role/assignment) para atribuir o novo grupo do Azure Active Directory à função de Colaborador da Máquina Virtual para o grupo de recursos.

```azurecli-interactive
adgroupId=$(az ad group show --group <your-group-name> --query objectId --output tsv)

az role assignment create --assignee-object-id $adgroupId --role "Virtual Machine Contributor" --resource-group myResourceGroup
```

Se você receber um erro informando: **A Entidade de Segurança \<guid> não existe no diretório**, o novo grupo ainda não foi propagado por todo o Azure Active Directory. Tente executar o comando novamente.

Normalmente, você repete o processo para *Colaborador de Rede* e *Colaborador da Conta de Armazenamento*, visando certificar-se de que os usuários serão designados para gerenciar os recursos implantados. Neste artigo, você pode ignorar essas etapas.

## <a name="azure-policy"></a>Azure Policy

O [Azure Policy](../../governance/policy/overview.md) ajuda a garantir que todos os recursos da assinatura atendam aos padrões corporativos. Sua assinatura já possui várias definições de políticas. Para ver as definições de política disponíveis, use o comando [az policy definition list](/cli/azure/policy/definition):

```azurecli-interactive
az policy definition list --query "[].[displayName, policyType, name]" --output table
```

Você vê as definições de políticas existentes. O tipo de política é **BuiltIn** ou **Personalizada**. Procure as definições para aqueles que descrevem uma condição que você quer atribuir. Neste artigo, você atribui políticas que:

* Limitam os locais para todos os recursos.
* Limitam as SKUs para máquinas virtuais.
* Auditam máquinas virtuais que não utilizam discos gerenciados.

No exemplo a seguir, você pode recuperar três definições de política com base no nome de exibição. Você usa o comando [az policy assignment create](/cli/azure/policy/assignment) para atribuir essas definições ao grupo de recursos. Para algumas políticas, você deve fornecer valores de parâmetro para especificar os valores permitidos.

```azurecli-interactive
# Get policy definitions for allowed locations, allowed SKUs, and auditing VMs that don't use managed disks
locationDefinition=$(az policy definition list --query "[?displayName=='Allowed locations'].name | [0]" --output tsv)
skuDefinition=$(az policy definition list --query "[?displayName=='Allowed virtual machine SKUs'].name | [0]" --output tsv)
auditDefinition=$(az policy definition list --query "[?displayName=='Audit VMs that do not use managed disks'].name | [0]" --output tsv)

# Assign policy for allowed locations
az policy assignment create --name "Set permitted locations" \
  --resource-group myResourceGroup \
  --policy $locationDefinition \
  --params '{ 
      "listOfAllowedLocations": {
        "value": [
          "eastus", 
          "eastus2"
        ]
      }
    }'

# Assign policy for allowed SKUs
az policy assignment create --name "Set permitted VM SKUs" \
  --resource-group myResourceGroup \
  --policy $skuDefinition \
  --params '{ 
      "listOfAllowedSKUs": {
        "value": [
          "Standard_DS1_v2", 
          "Standard_E2s_v2"
        ]
      }
    }'

# Assign policy for auditing unmanaged disks
az policy assignment create --name "Audit unmanaged disks" \
  --resource-group myResourceGroup \
  --policy $auditDefinition
```

O exemplo anterior pressupõe que você já conhece os parâmetros para uma política. Se precisar exibir os parâmetros, use:

```azurecli-interactive
az policy definition show --name $locationDefinition --query parameters
```

## <a name="deploy-the-virtual-machine"></a>Implantar a máquina virtual

Você atribuiu funções e políticas, entãoestá pronto para implantar a solução. O tamanho padrão é Standard_DS1_v2, que é uma das suas SKUs permitidas. O comando cria chaves SSH, se elas ainda não existirem em um local padrão.

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --generate-ssh-keys
```

Após a conclusão da implantação, será necessário aplicar mais configurações de gerenciamento à solução.

## <a name="lock-resources"></a>Bloquear recursos

[Bloqueios de recursos](../../azure-resource-manager/resource-group-lock-resources.md) impedem que os usuários em sua organização acidentalmente excluam ou modifiquem recursos críticos. Ao contrário do controle de acesso baseado em função, bloqueios de recurso aplicam uma restrição a todos os usuários e funções. É possível definir o nível de bloqueio como *CanNotDelete* ou *ReadOnly*.

Para criar ou excluir bloqueios de gerenciamento, você deve ter acesso às ações `Microsoft.Authorization/locks/*`. Das funções internas, somente **Proprietário** e **Administrador do Acesso de Usuário** recebem essas ações.

Para bloquear a máquina virtual e o grupo de segurança de rede, use o comando [az lock create](/cli/azure/lock):

```azurecli-interactive
# Add CanNotDelete lock to the VM
az lock create --name LockVM \
  --lock-type CanNotDelete \
  --resource-group myResourceGroup \
  --resource-name myVM \
  --resource-type Microsoft.Compute/virtualMachines

# Add CanNotDelete lock to the network security group
az lock create --name LockNSG \
  --lock-type CanNotDelete \
  --resource-group myResourceGroup \
  --resource-name myVMNSG \
  --resource-type Microsoft.Network/networkSecurityGroups
```

Para testar os bloqueios, tente executar o comando a seguir:

```azurecli-interactive 
az group delete --name myResourceGroup
```

Você vê um erro informando que a operação de exclusão não pode ser concluída devido a um bloqueio. O grupo de recursos poderá ser excluído apenas se você remover especificamente os bloqueios. Essa etapa é apresentada em [Limpar os recursos](#clean-up-resources).

## <a name="tag-resources"></a>Recursos de marca

Você pode aplicar [marcas](../../azure-resource-manager/resource-group-using-tags.md) para os recursos do Azure para organizá-los logicamente por categorias. Cada marca consiste em um nome e em um valor. Por exemplo, você pode aplicar o nome "Ambiente" e o valor "Produção" a todos os recursos na produção.

[!INCLUDE [Resource Manager governance tags CLI](../../../includes/resource-manager-governance-tags-cli.md)]

Para aplicar marcas a uma máquina virtual, use o comando [az resource tag](/cli/azure/resource). Nenhuma marca existente no recurso é mantida.

```azurecli-interactive
az resource tag -n myVM \
  -g myResourceGroup \
  --tags Dept=IT Environment=Test Project=Documentation \
  --resource-type "Microsoft.Compute/virtualMachines"
```

### <a name="find-resources-by-tag"></a>Localizar recursos por marca

Para localizar recursos com um nome e valor da marca, use o comando [az resource list](/cli/azure/resource):

```azurecli-interactive
az resource list --tag Environment=Test --query [].name
```

É possível utilizar os valores retornados para tarefas de gerenciamento, como parar todas as máquinas virtuais com um valor de marca.

```azurecli-interactive
az vm stop --ids $(az resource list --tag Environment=Test --query "[?type=='Microsoft.Compute/virtualMachines'].id" --output tsv)
```

### <a name="view-costs-by-tag-values"></a>Exibir custos por valores de marca

[!INCLUDE [Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>Limpar recursos

O grupo de segurança de rede bloqueado não poderá ser excluído até que o bloqueio seja removido. Para remover o bloqueio, recuperar as IDs dos bloqueios e fornecê-los para o comando [az lock delete](/cli/azure/lock):

```azurecli-interactive
vmlock=$(az lock show --name LockVM \
  --resource-group myResourceGroup \
  --resource-type Microsoft.Compute/virtualMachines \
  --resource-name myVM --output tsv --query id)
nsglock=$(az lock show --name LockNSG \
  --resource-group myResourceGroup \
  --resource-type Microsoft.Network/networkSecurityGroups \
  --resource-name myVMNSG --output tsv --query id)
az lock delete --ids $vmlock $nsglock
```

Quando não for mais necessário, você pode usar o comando [az group delete](/cli/azure/group) para remover o grupo de recursos, a VM e todos os recursos relacionados. Saia da sessão SSH para sua VM e então exclua os recursos da seguinte maneira:

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou uma imagem de VM personalizada. Você aprendeu como:

> [!div class="checklist"]
> * Atribuir usuários a uma função
> * Aplicar políticas que impõem padrões
> * Proteger recursos críticos com bloqueios
> * Recursos de marca de cobrança e gerenciamento

Avance para o próximo tutorial para saber mais sobre a alta disponibilidade das máquinas virtuais.

> [!div class="nextstepaction"]
> [Monitorar máquinas virtuais](tutorial-monitoring.md)

