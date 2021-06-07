---
title: Impor segurança com políticas em VMs do Windows no Azure
description: Como aplicar uma política a uma Máquina Virtual Windows do Azure Resource Manager
author: mimckitt
manager: vashan
ms.service: virtual-machines
ms.subservice: security
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 08/02/2017
ms.author: mimckitt
ms.openlocfilehash: 4a4e54510c4683dc1be9da09b96d6289136a26f1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102550256"
---
# <a name="apply-policies-to-windows-vms-with-azure-resource-manager"></a>Aplicar políticas a VMs Windows com o Azure Resource Manager
Usando políticas, uma organização pode impor várias convenções e regras em toda a empresa. A imposição do comportamento desejado pode ajudar a reduzir o risco e contribui para o sucesso da organização. Neste artigo, descrevemos como você pode usar as políticas do Azure Resource Manager para definir o comportamento desejado das Máquinas Virtuais de sua organização.

Para obter uma introdução às políticas, consulte [O que é o Azure Policy?](../../governance/policy/overview.md).

## <a name="permitted-virtual-machines"></a>Máquinas virtuais permitidas
Para garantir que as máquinas virtuais de sua organização são compatíveis com um aplicativo, você pode restringir os sistemas operacionais permitidos. No seguinte exemplo de política, você permite que apenas Máquinas Virtuais Windows Server 2012 R2 Datacenter sejam criadas:

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "in": [
          "Microsoft.Compute/virtualMachines",
          "Microsoft.Compute/VirtualMachineScaleSets"
        ]
      },
      {
        "not": {
          "allOf": [
            {
              "field": "Microsoft.Compute/imagePublisher",
              "in": [
                "MicrosoftWindowsServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageOffer",
              "in": [
                "WindowsServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageSku",
              "in": [
                "2012-R2-Datacenter"
              ]
            },
            {
              "field": "Microsoft.Compute/imageVersion",
              "in": [
                "latest"
              ]
            }
          ]
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Use um curinga para modificar a política anterior a fim de permitir qualquer imagem do Windows Server Datacenter:

```json
{
  "field": "Microsoft.Compute/imageSku",
  "like": "*Datacenter"
}
```

Use anyOf para modificar a política anterior para permitir qualquer Windows Server 2012 R2 Datacenter ou uma imagem superior:

```json
{
  "anyOf": [
    {
      "field": "Microsoft.Compute/imageSku",
      "like": "2012-R2-Datacenter*"
    },
    {
      "field": "Microsoft.Compute/imageSku",
      "like": "2016-Datacenter*"
    }
  ]
}
```

Para obter informações sobre campos de política, consulte [Aliases de política](../../governance/policy/concepts/definition-structure.md#aliases).

## <a name="managed-disks"></a>Discos gerenciados

Para exigir o uso de discos gerenciados, use a seguinte política:

```json
{
  "if": {
    "anyOf": [
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/virtualMachines"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/osDisk.uri",
            "exists": true
          }
        ]
      },
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/VirtualMachineScaleSets"
          },
          {
            "anyOf": [
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osDisk.vhdContainers",
                "exists": true
              },
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl",
                "exists": true
              }
            ]
          }
        ]
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="images-for-virtual-machines"></a>Imagens de máquinas virtuais

Por motivos de segurança, você pode exigir que apenas imagens personalizadas aprovadas sejam implantadas em seu ambiente. Você pode especificar o grupo de recursos que contém as imagens aprovadas ou as imagens aprovadas específicas.

O exemplo a seguir exige imagens de um grupo de recursos aprovado:

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "in": [
                    "Microsoft.Compute/virtualMachines",
                    "Microsoft.Compute/VirtualMachineScaleSets"
                ]
            },
            {
                "not": {
                    "field": "Microsoft.Compute/imageId",
                    "contains": "resourceGroups/CustomImage"
                }
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
} 
```

O exemplo a seguir especifica as IDs de imagem aprovadas:

```json
{
    "field": "Microsoft.Compute/imageId",
    "in": ["{imageId1}","{imageId2}"]
}
```

## <a name="virtual-machine-extensions"></a>Extensões de Máquina Virtual

Talvez você queira proibir o uso de determinados tipos de extensões. Por exemplo, uma extensão pode não ser compatível com determinadas imagens de máquina virtual personalizadas. O exemplo a seguir mostra como bloquear uma extensão específica. Ele usa o publicador e o tipo para determinar qual extensão será bloqueada.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "equals": "{extension-type}"

      }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```


## <a name="azure-hybrid-use-benefit"></a>Benefício de Uso do Azure Híbrido

Quando você tiver uma licença local, poderá salvar a taxa de licença em suas máquinas virtuais. Quando você não tiver a licença, deverá proibir a opção. A seguinte política proíbe o uso do Benefício de Uso do Azure Híbrido (AHUB):

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "in":[ "Microsoft.Compute/virtualMachines","Microsoft.Compute/VirtualMachineScaleSets"]
            },
            {
                "field": "Microsoft.Compute/licenseType",
                "exists": true
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

## <a name="next-steps"></a>Próximas etapas
* Depois de definir uma regra de política (conforme mostrado nos exemplos anteriores), você precisará criar a definição de política e atribuí-la a um escopo. O escopo pode ser uma assinatura, grupo de recursos ou recurso. Para atribuir políticas, consulte [Usar o portal do Azure para atribuir e gerenciar políticas de recursos](../../governance/policy/assign-policy-portal.md), [Usar o PowerShell para atribuir políticas](../../governance/policy/assign-policy-powershell.md) ou [Usar a CLI do Azure para atribuir políticas](../../governance/policy/assign-policy-azurecli.md).
* Para obter uma introdução às políticas de recursos, consulte [O que é o Azure Policy?](../../governance/policy/overview.md).
* Para obter orientação sobre como as empresas podem usar o Resource Manager para gerenciar assinaturas de forma eficaz, consulte [Azure enterprise scaffold – controle de assinatura prescritivas](/azure/architecture/cloud-adoption-guide/subscription-governance).
