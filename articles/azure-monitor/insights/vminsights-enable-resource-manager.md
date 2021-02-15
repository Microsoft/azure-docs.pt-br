---
title: Habilitar o Azure Monitor para VMs usando modelos do Resource Manager
description: Este artigo descreve como habilitar Azure Monitor para VMs para uma ou mais máquinas virtuais do Azure ou conjuntos de dimensionamento de máquinas virtuais usando modelos de Azure PowerShell ou Azure Resource Manager.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: cee7072f9bc844fb1f89168de3547dc726472b67
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/19/2020
ms.locfileid: "97695911"
---
# <a name="enable-azure-monitor-for-vms-using-resource-manager-templates"></a>Habilitar o Azure Monitor para VMs usando modelos do Resource Manager
Este artigo descreve como habilitar Azure Monitor para VMs para uma máquina virtual ou um conjunto de dimensionamento de máquinas virtuais usando modelos do Resource Manager. Esse procedimento pode ser usado para o seguinte:

- Máquina virtual do Azure
- Conjunto de dimensionamento de máquinas virtuais do Azure
- Máquina virtual híbrida conectada ao Azure Arc

## <a name="prerequisites"></a>Pré-requisitos

- [Criar e configurar um espaço de trabalho log Analytics](vminsights-configure-workspace.md). 
- Consulte [sistemas operacionais com suporte](vminsights-enable-overview.md#supported-operating-systems) para garantir que o sistema operacional da máquina virtual ou do conjunto de dimensionamento de máquinas virtuais que você está habilitando tem suporte. 

## <a name="resource-manager-templates"></a>Modelos do Gerenciador de Recursos

Criamos exemplos de modelos de Azure Resource Manager para integração de suas máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais. Esses modelos incluem cenários que você pode usar para habilitar o monitoramento em um recurso existente e criar um novo recurso com monitoramento habilitado.

>[!NOTE]
>O modelo precisa ser implantado no mesmo grupo de recursos que a máquina virtual ou o conjunto de dimensionamento de máquinas virtuais está habilitado.


Os modelos de Azure Resource Manager são fornecidos em um arquivo morto (. zip) que você pode [baixar](https://aka.ms/VmInsightsARMTemplates) de nosso repositório github. O conteúdo do arquivo inclui pastas que representam cada cenário de implantação com um arquivo de modelo e parâmetro. Antes de executá-los, modifique o arquivo de parâmetros e especifique os valores necessários. 

O arquivo de download contém os seguintes modelos para cenários diferentes:

- O modelo **ExistingVmOnboarding** habilita Azure monitor para VMs se a máquina virtual já existe.
- O modelo **NewVmOnboarding** cria uma máquina virtual e permite Azure monitor para VMs monitorá-la.
- O modelo **ExistingVmssOnboarding** habilita Azure monitor para VMs se o conjunto de dimensionamento de máquinas virtuais já existir.
- O modelo **NewVmssOnboarding** cria conjuntos de dimensionamento de máquinas virtuais e permite que Azure monitor para VMs monitorá-los.
- O modelo **ConfigureWorkspace** configura seu espaço de trabalho log Analytics para dar suporte a Azure monitor para VMs habilitando as soluções e a coleta de contadores de desempenho do sistema operacional Linux e Windows.

>[!NOTE]
>Se os conjuntos de dimensionamento de máquinas virtuais já estiverem presentes e a política de atualização estiver definida como **manual**, Azure monitor para VMs não será habilitada para instâncias por padrão depois de executar o modelo de Azure Resource Manager **ExistingVmssOnboarding** . Você precisa atualizar manualmente as instâncias.

## <a name="deploy-templates"></a>Implantar modelos
Os modelos podem ser implantados usando [qualquer método de implantação para modelos do Resource Manager](../../azure-resource-manager/templates/deploy-powershell.md) , incluindo os exemplos a seguir usando o PowerShell e a CLI.

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```


```azurecli
az deployment group create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```



## <a name="next-steps"></a>Próximas etapas

Agora que o monitoramento está habilitado para suas máquinas virtuais, essas informações estão disponíveis para análise com Azure Monitor para VMs.

- Para exibir as dependências de aplicativos descobertas, confira [Exibir o Mapa do Azure Monitor para VMs](vminsights-maps.md).

- Para identificar afunilamentos e a utilização geral com o desempenho da VM, consulte [Exibir o desempenho da VM do Azure](vminsights-performance.md).
