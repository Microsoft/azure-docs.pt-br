---
title: Habilitar o Revisions de VM usando modelos do Resource Manager
description: Este artigo descreve como você habilita as informações de VM para uma ou mais máquinas virtuais do Azure ou conjuntos de dimensionamento de máquinas virtuais usando modelos Azure PowerShell ou Azure Resource Manager.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: fc0c304a3fea81f44e01d3e815f34e44728ea42e
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031848"
---
# <a name="enable-vm-insights-using-resource-manager-templates"></a>Habilitar o Revisions de VM usando modelos do Resource Manager
Este artigo descreve como habilitar o Revisions da VM para uma máquina virtual ou um conjunto de dimensionamento de máquinas virtuais usando modelos do Resource Manager. Esse procedimento pode ser usado para o seguinte:

- Máquina virtual do Azure
- Conjunto de dimensionamento de máquinas virtuais do Azure
- Máquina virtual híbrida conectada ao Azure Arc

## <a name="prerequisites"></a>Pré-requisitos

- [Criar e configurar um espaço de trabalho log Analytics](./vminsights-configure-workspace.md). 
- Consulte [sistemas operacionais com suporte](./vminsights-enable-overview.md#supported-operating-systems) para garantir que o sistema operacional da máquina virtual ou do conjunto de dimensionamento de máquinas virtuais que você está habilitando tem suporte. 

## <a name="resource-manager-templates"></a>Modelos do Gerenciador de Recursos

Criamos exemplos de modelos de Azure Resource Manager para integração de suas máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais. Esses modelos incluem cenários que você pode usar para habilitar o monitoramento em um recurso existente e criar um novo recurso com monitoramento habilitado.

>[!NOTE]
>O modelo precisa ser implantado no mesmo grupo de recursos que a máquina virtual ou o conjunto de dimensionamento de máquinas virtuais está habilitado.


Os modelos de Azure Resource Manager são fornecidos em um arquivo morto (. zip) que você pode [baixar](https://aka.ms/VmInsightsARMTemplates) de nosso repositório github. O conteúdo do arquivo inclui pastas que representam cada cenário de implantação com um arquivo de modelo e parâmetro. Antes de executá-los, modifique o arquivo de parâmetros e especifique os valores necessários. 

O arquivo de download contém os seguintes modelos para cenários diferentes:

- O modelo **ExistingVmOnboarding** habilitará o Revisions da VM se a máquina virtual já existir.
- O modelo **NewVmOnboarding** cria uma máquina virtual e permite que o virtual insights o monitore.
- O modelo **ExistingVmssOnboarding** habilita o insights da VM se o conjunto de dimensionamento de máquinas virtuais já existir.
- O modelo **NewVmssOnboarding** cria conjuntos de dimensionamento de máquinas virtuais e permite que o virtual insights os monitore.
- O modelo **ConfigureWorkspace** configura seu espaço de trabalho log Analytics para dar suporte a informações de VM, habilitando as soluções e a coleta de contadores de desempenho do sistema operacional Linux e do Windows.

>[!NOTE]
>Se os conjuntos de dimensionamento de máquinas virtuais já estiverem presentes e a política de atualização estiver definida como **manual**, as informações de VM não serão habilitadas para instâncias por padrão após a execução do modelo de Azure Resource Manager **ExistingVmssOnboarding** . Você precisa atualizar manualmente as instâncias.

## <a name="deploy-templates"></a>Implantar modelos
Os modelos podem ser implantados usando [qualquer método de implantação para modelos do Resource Manager](../../azure-resource-manager/templates/deploy-powershell.md) , incluindo os exemplos a seguir usando o PowerShell e a CLI.

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```


```azurecli
az deployment group create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```



## <a name="next-steps"></a>Próximas etapas

Agora que o monitoramento está habilitado para suas máquinas virtuais, essas informações estão disponíveis para análise com o VM insights.

- Para exibir dependências de aplicativo descobertas, consulte [Exibir o mapa de informações de VM](vminsights-maps.md).

- Para identificar afunilamentos e a utilização geral com o desempenho da VM, consulte [Exibir o desempenho da VM do Azure](vminsights-performance.md).