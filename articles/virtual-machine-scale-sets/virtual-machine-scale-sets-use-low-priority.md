---
title: Criar um conjunto de dimensionamento do Azure que use VMs de baixa prioridade (versão prévia) | Microsoft Docs
description: Saiba como criar conjuntos de dimensionamento de máquinas virtuais do Azure que usem VMs de baixa prioridade para economizar nos custos
services: virtual-machine-scale-sets
documentationcenter: ''
author: mmccrory
manager: rajraj
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: memccror
ms.openlocfilehash: 861c68ae8163e0ba8c2af2a3d96153ac3e84855f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60803201"
---
# <a name="low-priority-vms-on-scale-sets-preview"></a>VMs de baixa prioridade em conjuntos de dimensionamento (versão prévia)

O uso de VMs de baixa prioridade em conjuntos de dimensionamento permite tirar proveito da capacidade inutilizada com uma economia significativa. A qualquer momento que o Azure precisar da capacidade de volta, a infraestrutura do Azure removerá VMs de baixa prioridade. Portanto, as VMs de baixa prioridade são ótimas para cargas de trabalho que podem lidar com interrupções, como trabalhos de processamento de carga, ambientes de desenvolvimento/teste, grandes cargas de trabalho de computação e etc.

A quantidade de capacidade disponível pode variar com base no tamanho, região, hora do dia e etc. Ao implantar VMs de baixa prioridade em conjuntos de dimensionamento, o Azure alocará as VMs se houver capacidade disponível, mas não há SLA para essas VMs. Um conjunto de dimensionamento de baixa prioridade é implantado em um domínio de falha único e não oferece garantias de alta disponibilidade.

## <a name="eviction-policy"></a>Política de remoção

Durante a criação de conjuntos de escala de baixa prioridade, é possível definir a política de remoção para *Deslocar* (padrão) ou *Excluir*. 

A política *Deslocar* move suas VMs removidas para o estado de parado desalocada permitindo que você reimplante instâncias removidas. No entanto, não há nenhuma garantia de que a alocação terá êxito. As VMs deslocadas afetarão sua cota de instância do conjunto de dimensionamento e você será cobrado pelos discos subjacentes. 

Se você quiser que suas VMs em seu conjunto de dimensionamento de baixa prioridade sejam excluídas quando forem removidas, você pode definir a política de remoção para*excluir*. Com a política de remoção definida para excluir, você pode criar novas VMs, aumentando a propriedade de contagem de instância do conjunto de dimensionamento. As VMs removidas são excluídas junto com seus discos subjacentes e, portanto, você não será cobrado pelo armazenamento. Você também pode usar o recurso de dimensionamento automático dos conjuntos de dimensionamento para tentar e compensar automaticamente as VMs removidas, mas não há nenhuma garantia de que a alocação terá êxito. É recomendável que você só use o recurso de dimensionamento automático em conjuntos de dimensionamento de baixa prioridade quando você definir a política de remoção para excluir para evitar que o custo de seus discos atinjam os limites de cota. 

> [!NOTE]
> Durante a versão prévia, você poderá definir sua política de remoção usando [portal do Azure](#use-the-azure-portal) e [os modelos do Azure Resource Manager](#use-azure-resource-manager-templates). 

## <a name="deploying-low-priority-vms-on-scale-sets"></a>Implantação de VMs de baixa prioridade em conjuntos de dimensionamento

Para implantar VMs de baixa prioridade em conjuntos de dimensionamento, você poderá definir o novo sinalizador *Prioridade* como *Baixa*. Todas as VMs no conjunto de dimensionamento serão definidas como baixa prioridade. Para criar um conjunto de dimensionamento com VMs de baixa prioridade, use um dos seguintes métodos:
- [Portal do Azure](#use-the-azure-portal)
- CLI do Azure
- [PowerShell do Azure](#use-azure-powershell)
- [Modelos do Gerenciador de Recursos do Azure](#use-azure-resource-manager-templates)

## <a name="use-the-azure-portal"></a>Use o Portal do Azure

O processo para criar um conjunto de dimensionamento que usa VMs de baixa prioridade é o mesmo detalhado no [artigo de introdução](quick-create-portal.md). Quando estiver implantando um conjunto de escala, você pode optar por definir o sinalizador de baixa prioridade e a política de remoção: ![criar uma escala com baixa prioridade de VMs](media/virtual-machine-scale-sets-use-low-priority/vmss-low-priority-portal.png)

## <a name="use-the-azure-cli"></a>Usar a CLI do Azure

O processo para criar um conjunto de dimensionamento com VMs de baixa prioridade é o mesmo detalhado no [artigo de introdução](quick-create-cli.md). Basta adicionar o parâmetro '--Priority' à chamada da CLI e defini-lo como *Baixa* conforme mostrado no exemplo a seguir:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Low
```

## <a name="use-azure-powershell"></a>Usar PowerShell do Azure

O processo para criar um conjunto de dimensionamento com VMs de baixa prioridade é o mesmo detalhado no [artigo de introdução](quick-create-powershell.md).
Basta adicionar o parâmetro '-Priority' a [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) e defini-lo como *Baixa* conforme mostrado no exemplo a seguir:

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Low"
```

## <a name="use-azure-resource-manager-templates"></a>Usar Modelos do Azure Resource Manager

O processo para criar um conjunto de dimensionamento que use VMs de baixa prioridade é o mesmo detalhado no artigo de introdução para [Linux](quick-create-template-linux.md) ou [Windows](quick-create-template-windows.md). Adicione a propriedade ‘priority’ ao tipo de recurso *Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile* em seu modelo e especifique *Baixa* como valor. Certifique-se de usar a versão *2018-03-01* da API ou superior. 

Para definir a política de remoção para excluir, adicione o parâmetro 'evictionPolicy' e defina-o como *excluir*.

O exemplo a seguir cria uma conjunto de dimensionamento de baixa prioridade do Linux chamado *myScaleSet* em *Centro-Oeste dos EUA*, que irá *excluir* as VMs no conjunto de dimensionamento durante a remoção:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2018-03-01",
  "sku": {
    "name": "Standard_DS2_v2",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
       "priority": "Low",
       "evictionPolicy": "delete",
       "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```
## <a name="faq"></a>Perguntas frequentes

### <a name="can-i-convert-existing-scale-sets-to-low-priority-scale-sets"></a>É possível converter os conjuntos existentes de escala para conjuntos de escala de baixa prioridade?
Não, a definição do sinalizador de baixa prioridade só tem suporte no momento da criação.

### <a name="can-i-create-a-scale-set-with-both-regular-vms-and-low-priority-vms"></a>É possível criar uma escala com VMs regulares e VMs de baixa prioridade?
Não, um conjunto de escala não oferece suporte a mais de um tipo de prioridade.

### <a name="how-is-quota-managed-for-low-priority-vms"></a>Como a cota é gerenciada para VMs de baixa prioridade?
VMs de baixa prioridade e VMs regulares compartilham o mesmo pool de cota. 

### <a name="can-i-use-autoscale-with-low-priority-scale-sets"></a>É possível usar o dimensionamento automático com conjuntos de escala de baixa prioridade?
Sim, é possível definir regras de dimensionamento automático em seu conjunto de dimensionamento de baixa prioridade. Se suas VMs forem  removidas, o dimensionamento automático pode tentar criar novas VMs de baixa prioridade. Lembre-se de que essa capacidade não é garantida. 

### <a name="does-autoscale-work-with-both-eviction-policies-deallocate-and-delete"></a>O dimensionamento automático funciona com as políticas de remoção (deslocar e excluir)?
Recomenda-se definir sua política de remoção para excluir ao usar o dimensionamento automático. Isso ocorre porque instâncias desalocadas são contadas em relação a sua capacidade de contagem no conjunto de dimensionamento. Ao usar o dimensionamento automático, você provavelmente atingirá sua contagem de instâncias de destino rapidamente devido a instâncias desalocadas, removidas. 

## <a name="next-steps"></a>Próximas etapas
Agora que você criou um conjunto de dimensionamento com VMs de baixa prioridade, tente implantar nosso [modelo de dimensionamento automático com baixa prioridade](https://github.com/Azure/vm-scale-sets/tree/master/preview/lowpri).

Confira a [página de preços de conjunto de dimensionamento de máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) para obter detalhes sobre preços.
