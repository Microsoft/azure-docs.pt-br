---
title: Especifique o grupo de recursos para VMs no Azure DevTest Labs | Microsoft Docs
description: Saiba como especificar um grupo de recursos para VMs em um laboratório no Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 7b72048405d3025ca21b324b6ad3168dd0c9ac95
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "85483356"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Especifique um grupo de recursos para máquinas virtuais do laboratório no Azure DevTest Labs

Como um proprietário de laboratório, você pode configurar suas máquinas virtuais do laboratório a serem criadas em um grupo de recursos específico. Esse recurso o ajuda nos seguintes cenários:

- Tem menos grupos de recursos criados por laboratórios na sua assinatura.
- Faça com que seus laboratórios operem dentro de um conjunto fixo de grupos de recursos que você configurar.
- Contornar as restrições e aprovações necessárias para a criação de grupos de recursos na sua assinatura do Azure.
- Consolide todos os seus recursos de laboratório em um único grupo de recursos para simplificar o acompanhamento desses recursos e aplicar [políticas](../governance/policy/overview.md) para gerenciar recursos no nível do grupo de recursos.

Com esse recurso, você pode usar um script para especificar um grupo de recursos novo ou existente dentro de sua assinatura do Azure para todas as suas VMs de laboratório. Atualmente, Azure DevTest Labs dá suporte a esse recurso por meio de uma API.

> [!NOTE]
> Todos os limites de assinatura se aplicam ao criar laboratórios no DevTest Labs. Imagine um laboratório como qualquer outro recurso em sua assinatura. No caso de grupos de recursos, o limite é de [980 grupos de recursos por assinatura](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits). 

## <a name="use-azure-portal"></a>Usar o portal do Azure
Siga estas etapas para especificar um grupo de recursos para todas as VMs criadas no laboratório. 

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione **todos os serviços** no menu de navegação à esquerda. 
3. Selecione **DevTest Labs** na lista.
4. Na lista de laboratórios, selecione seu **laboratório**.  
5. Selecione **configuração e políticas** na seção **configurações** no menu à esquerda. 
6. Selecione **configurações de laboratório** no menu à esquerda. 
7. Selecione **todas as máquinas virtuais em um grupo de recursos**. 
8. Selecione um grupo de recursos existente na lista suspensa (ou) selecione **criar novo**, insira um **nome** para o grupo de recursos e selecione **OK**. 

    ![Selecione o grupo de recursos para todas as VMs de laboratório](./media/resource-group-control/select-resource-group.png)

## <a name="use-powershell"></a>Usar o PowerShell 
O exemplo a seguir mostra como usar um script do PowerShell para criar todas as máquinas virtuais do laboratório em um novo grupo de recursos.

```powershell
[CmdletBinding()]
Param(
    $subId,
    $labRg,
    $labName,
    $vmRg
)

az login | out-null

az account set --subscription $subId | out-null

$rgId = "/subscriptions/"+$subId+"/resourceGroups/"+$vmRg

"Updating lab '$labName' with vm rg '$rgId'..."

az resource update -g $labRg -n $labName --resource-type "Microsoft.DevTestLab/labs" --api-version 2018-10-15-preview --set properties.vmCreationResourceGroupId=$rgId

"Done. New virtual machines will now be created in the resource group '$vmRg'."
```

Invoque o script usando o comando a seguir. ResourceGroup.ps1 é o arquivo que contém o script anterior:

```powershell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-an-azure-resource-manager-template"></a>Usar um modelo de Azure Resource Manager
Se você estiver usando um modelo de Azure Resource Manager para criar um laboratório, use a propriedade **vmCreationResourceGroupId** na seção Propriedades do laboratório do modelo, conforme mostrado no exemplo a seguir:

```json
        {
            "type": "microsoft.devtestlab/labs",
            "name": "[parameters('lab_name')]",
            "apiVersion": "2018_10_15_preview",
            "location": "eastus",
            "tags": {},
            "scale": null,
            "properties": {
                "vmCreationResourceGroupId": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>",
                "labStorageType": "Premium",
                "premiumDataDisks": "Disabled",
                "provisioningState": "Succeeded",
                "uniqueIdentifier": "000000000f-0000-0000-0000-00000000000000"
            },
            "dependsOn": []
        },
```


## <a name="api-to-configure-a-resource-group-for-lab-vms"></a>API para configurar um grupo de recursos para VMs de laboratório
Você tem as seguintes opções como um proprietário de laboratório ao usar esta API:

- Escolha o **grupo de recursos do laboratório** para todas as máquinas virtuais.
- Escolha um **grupo de recursos existente** diferente do grupo de recursos do laboratório para todas as máquinas virtuais.
- Insira um **novo** nome de grupo de recursos para todas as máquinas virtuais.
- Continue usando o comportamento existente, no qual um grupo de recursos é criado para cada VM no laboratório.
 
Essa configuração se aplica a novas máquinas virtuais criadas no laboratório. As VMs mais antigas em seu laboratório que foram criadas em seus próprios grupos de recursos permanecem inalteradas. Os ambientes criados em seu laboratório continuam a permanecer em seus próprios grupos de recursos.

Como usar essa API:
- Use a versão de API **2018_10_15_preview**.
- Se você especificar um novo grupo de recursos, verifique se tem **permissões de gravação em grupos de recursos** em sua assinatura. Se você não tem permissões de gravação, a criação de novas máquinas virtuais no grupo de recursos especificado falhará.
- Ao usar a API, passe a **ID completa do grupo de recursos**. Por exemplo: `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`. Verifique se o grupo de recursos está na mesma assinatura que o laboratório. 


## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir: 

- [Definir políticas para um laboratório](devtest-lab-set-lab-policy.md)
- [Perguntas frequentes](devtest-lab-faq.md)
