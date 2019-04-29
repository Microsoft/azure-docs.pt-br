---
title: Recursos atualizados nos aplicativos gerenciados do Azure | Microsoft Docs
description: Descreve como trabalhar em recursos no grupo de recursos gerenciado de um aplicativo gerenciado do Azure.
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: 21f4e0aa339eb0c746f9b9b06f8aaada6c4d4b71
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61043368"
---
# <a name="work-with-resources-in-the-managed-resource-group-for-azure-managed-application"></a>Trabalhar com recursos no grupo de recursos gerenciado do aplicativo gerenciado do Azure

Este artigo descreve como atualizar os recursos implantados como parte de um aplicativo gerenciado. Como fornecedor de um aplicativo gerenciado, você tem acesso aos recursos no grupo de recursos gerenciado. Para atualizar esses recursos, você precisa localizar o grupo de recurso gerenciado associado a um aplicativo gerenciado, e acessar o recurso nesse grupo de recursos.

Este artigo pressupõe que você implantou o aplicativo gerenciado no projeto de exemplo [IaaS (Aplicativo Web gerenciado) com os serviços de gerenciamento do Azure](https://github.com/Azure/azure-managedapp-samples/tree/master/samples/201-managed-web-app). Se o aplicativo gerenciado incluir uma máquina virtual **Standard_D1_v2**. Se você não tiver implantado o aplicativo gerenciado, ainda poderá usar este artigo para se familiarizar com as etapas de atualização de um grupo de recursos gerenciado.

A imagem a seguir mostra o aplicativo gerenciado implantado.

![Aplicativo gerenciado implantado](./media/update-managed-resources/deployed.png)

Neste artigo, use a CLI do Azure para:

* Identificar o aplicativo gerenciado
* Identificar o grupo de recursos gerenciado
* Identificar os recursos de máquina virtual no grupo de recursos gerenciado
* Alterar o tamanho da VM (para um tamanho menor, se não for utilizada, ou maior para oferecer suporte a cargas adicionais)
* Atribuir uma política ao grupo de recursos gerenciado que especifica os locais permitidos

## <a name="get-managed-application-and-managed-resource-group"></a>Obter o aplicativo gerenciado e o grupo de recursos gerenciado

Para obter os aplicativos gerenciados em um grupo de recursos, use:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')]"
```

Para obter a ID do grupo de recursos gerenciados, use:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')].{ managedResourceGroup:managedResourceGroupId }"
```

## <a name="resize-vms-in-managed-resource-group"></a>Redimensionar VMs no grupo de recursos gerenciado

Para ver as máquinas virtuais no grupo de recursos gerenciado, forneça o nome do grupo de recursos gerenciado.

```azurecli-interactive
az vm list -g DemoApp6zkevchqk7sfq --query "[].{VMName:name,OSType:storageProfile.osDisk.osType,VMSize:hardwareProfile.vmSize}"
```

Para atualizar o tamanho das VMs, use:

```azurecli-interactive
az vm resize --size Standard_D2_v2 --ids $(az vm list -g DemoApp6zkevchqk7sfq --query "[].id" -o tsv)
```

Após a conclusão da operação, verifique se o aplicativo está em execução no Standard D2 v2.

![Aplicativo gerenciado usando o Standard D2 v2](./media/update-managed-resources/upgraded.png)

## <a name="apply-policy-to-managed-resource-group"></a>Aplicar a política ao grupo de recursos gerenciado

Obtenha o grupo de recursos gerenciado e a atribuição de uma política nesse escopo. A política **e56962a6-4747-49cd-b67b-bf8b01975c4c** é uma política interna para especificar locais permitidos.

```azurecli-interactive
managedGroup=$(az managedapp show --name <app-name> --resource-group DemoApp --query managedResourceGroupId --output tsv)

az policy assignment create --name locationAssignment --policy e56962a6-4747-49cd-b67b-bf8b01975c4c --scope $managedGroup --params '{
                            "listofallowedLocations": {
                                "value": [
                                    "northeurope",
                                    "westeurope"
                                ]
                            }
                        }'
```

Para ver os locais permitidos, use:

```azurecli-interactive
az policy assignment show --name locationAssignment --scope $managedGroup --query parameters.listofallowedLocations.value
```

A atribuição de política é exibida no portal.

![Exibir atribuição de política](./media/update-managed-resources/assignment.png)

## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução aos aplicativos gerenciados, consulte [Visão geral de aplicativos gerenciados](overview.md).
* Para obter os projetos de exemplo, consulte [Projetos de exemplo para aplicativos gerenciados do Azure](sample-projects.md).
